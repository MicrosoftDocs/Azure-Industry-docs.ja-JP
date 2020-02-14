---
title: 製造業向けの IoT から分析情報を抽出するためのアーキテクチャ
description: Azure サービスを使用して IoT データから分析情報を抽出します。
author: ercenk
ms.author: ercenk
manager: gmarchet
ms.service: industry
ms.topic: article
ms.date: 11/28/2019
ms.openlocfilehash: c08e6bbb1da47084122dae1ed6a9e1cea0b59473
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77053404"
---
# <a name="extracting-actionable-insights-from-iot-data"></a>IoT データからアクションにつながる分析情報を抽出する

工場の機械の担当者は、モノのインターネット (IoT) がプロセスと結果の向上における次のステップであることを既に認識しています。 機械または工場のフロアにセンサーを設置することが最初のステップです。 次のステップはそのデータを使用することであり、それがこのドキュメントの目的です。 このガイドでは、IoT データの分析からアクションにつながる分析情報を抽出するために必要なコンポーネントの技術的な概要を説明します。

IoT 分析ソリューションでは、一連のデバイスから送られてきた生の IoT データを、より分析に適した形式に変換します。 データが分析可能な形式になったら、実際に分析を実行できます。 分析の例を次に示します。

- 時間経過による温度を示す横棒グラフなど、テレメトリ データのシンプルな視覚エフェクト
- 総合設備効率 (OEE) など、主要業績評価指標 (KPI) の計算
- 機械学習モデルを利用した予測分析

これらの分析は、さらに、アクションを示す分析情報を提供します。 アクションの範囲は、機械への単純なコマンドの送信から、操作パラメーターの調整、別のソフトウェア システムでのアクションの実行、会社全体への運用改善プログラムの実装といったことにまで及びます。

次の図は、工場の機械と最終的な結果の間で発生するアクションのチェーンを示したものです。最終的な結果は "使用率" のダッシュボード表現であり、グラフと "87.5%" というラベルが表示されています。

![工場からダッシュボードまで。](assets/extracting-insights-from-iot/factory-to-dashboard.png)

わかりやすくするため、機械の使用率というシンプルな KPI を使用しています。 "*機械の使用率*" は、機械が実際に部品を "*製造している*" 時間の割合です。 たとえば、シフトが 8 時間であり、そのうちの 7 時間だけ機械が部品を製造している場合、そのシフトにおけるその機械の使用率は 87.5% (7/8 x 100) になります。

## <a name="approach"></a>アプローチ

IoT アプリケーションを構成するコンポーネントは、データまたはイベントを送信する**モノ** (つまりデバイス)、データを使用して生成される**分析情報**、さらに分析情報を使用して生成される、ビジネスやプロセスの改善に役立つ**アクション**の 3 つです。

製造工場の機器 (モノ) は、動作しながらさまざまな種類のデータを送信します。 たとえば、供給量と温度のデータを送信するフライス盤などです。このデータを使用して、機械が稼働しているかどうかが評価され (分析情報)、それを使用して工場が最適化されます (アクション)。 ここでは、データを抽出し、ダッシュボードで視覚化し、新しい分析情報を抽出して、さらにアクションを実行する手順について説明します。
![モノから分析情報に、そしてアクションへ。](assets/extracting-insights-from-iot/things-insights-actions.png)

Microsoft では、IoT アプリケーション向けの参照アーキテクチャの概要を公開しており、さまざまなサブシステムと、それらのサブシステムに対して推奨されるアプローチが示されています。
IoT アプリケーションは、以下のサブシステムで構成されます。

1. デバイス、またはオンプレミスのエッジ ゲートウェイ: クラウドにメッセージ ソース (デバイス) を安全に登録できる特定の種類のデバイスです。 エッジ ゲートウェイでは、ネイティブ プロトコルから別の形式 (JSON など) へのメッセージ変換が行われる場合もあります。
2. クラウド ゲートウェイ サービスまたはハブ ([Azure IoT Hub](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk) や [Azure Event Hubs](/azure/event-hubs/event-hubs-about?WT.mc_id=iotinsightssoln-docs-ercenk) など): データを安全に取り込んで、デバイス管理機能を提供します。 
3. ストリーム プロセッサ: ストリーミング データを使用します。 プロセッサは、ビジネス プロセスと統合して、ストレージにデータを配置することもありますも。
4. ダッシュボード形式のユーザー インターフェイス: IoT データを視覚化し、デバイスの管理を容易にします。

![ソリューションのアーキテクチャ。](assets/extracting-insights-from-iot/architecture.png)

この記事では、分析情報を抽出するプロセスに注目します。 主要な手順を次に示します。

1. データにアクセスし、処理を行ってデータ ストリームにします。
2. データを処理して格納します。
3. データを視覚化または提供します。 

次の図は、データ ソースから、変換、取り込み、処理と格納、提示、最終的なアクションという、データの流れを示したものです。

![データ フロー: ソース、取り込み、提示、アクション。](assets/extracting-insights-from-iot/data-flow.png)

## <a name="converting-the-data-to-a-stream"></a>データからストリームへの変換

IoT のデータは時系列データ、つまり一定の期間について見た方が意味のある、"モノ" からの値です。 工場の機器はある時間だけ動作し、その間にイベントが発生します。 工場のデータが [Azure IoT Hub](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk) などのデータ インジェスト サービスに送信されない場合は、そのストア (生産実行システム (MES) や HTTP エンドポイントなど) から定期的にデータをポーリングして、インジェスト サービスにデータを送信する必要があります。  
データからストリームへの変換は、通常、次のように行われます。

1. データ ソースにアクセスします。
2. データを変換して補強します。
3. ストリーミング データを取り込むことができるエンドポイントにデータを送信します。

ここでは、データ ソースへのアクセスについては詳しく説明しません。アクセスはデータが存在する場所に依存し、バリエーションが多すぎるからです。

## <a name="transforming-and-enriching-the-data"></a>データの変換と補強

通常は、生データに対して変換操作を行うことで、データを標準化し、取り込むことができる状態にする必要があります。  具体的な変換は、使用される分析の種類によって異なります。  データ変換の一般的な例としては、時系列データに欠測があって入力する必要がある場合や、異なる機械の間で時間尺度を合理化する必要がある場合などです。  望ましいデータ レコードは、タイムスタンプを含み (ソースに含まれる場合)、名前と値のペアになっていることです。 一般に、データは階層形式で送られてきて、それをフラットな構造に変換する必要があります。

次の図では、サイズが一定ではない階層データ構造を、標準化された列と行の形式 (ブロック) に変換しています。

![階層からフラットへのデータの整形。](assets/extracting-insights-from-iot/hierarchy-to-flat.png)

通常、データにはインターネットからはアクセスできません。 一般的なパターンは、エッジ ゲートウェイを使用して、工場からインジェスト ポイントにデータをプッシュすることです。 [Azure IoT Edge](/azure/iot-edge?WT.mc_id=iotinsightssoln-docs-ercenk) は、IoT Hub を基にして構築されたサービスです。 IoT Edge デバイスは、ゲートウェイとして機能することができ、透過ゲートウェイ、プロトコル変換、ID 変換の 3 つの[パターン](/azure/iot-edge/iot-edge-as-gateway?WT.mc_id=iotinsightssoln-docs-ercenk)に対応します。

外部からデータを使用でき、インターネットからデータにアクセスできる場合は、複数の Azure サービスを使用して、データにアクセスし、変換して、補強することができます。 次のようなオプションがあります。

- [App Service](/azure/app-service/?WT.mc_id=iotinsightssoln-docs-ercenk)、[Azure Kubernetes Service](/azure/aks/?WT.mc_id=iotinsightssoln-docs-ercenk) (AKS)、[Container Instances](/azure/container-instances/?WT.mc_id=iotinsightssoln-docs-ercenk)、[Service Fabric](/azure/service-fabric/service-fabric-overview?WT.mc_id=iotinsightssoln-docs-ercenk) など、さまざまな Azure コンピューティング サービスにデプロイされたカスタム コード。
- [Azure Logic Apps](/azure/logic-apps/?WT.mc_id=iotinsightssoln-docs-ercenk)
- [Azure Data Factory のパイプラインとアクティビティ](/azure/data-factory/copy-activity-overview/?WT.mc_id=iotinsightssoln-docs-ercenk)
- [Azure Functions](/azure/azure-functions/functions-overview)
- [BizTalk Services](https://azure.microsoft.com/services/biztalk-services/)

上記の各サービスには、シナリオに応じて、それぞれに固有のベネフィットとコストがあります。 たとえば、Logic Apps では、[XML ドキュメントを変換する](/azure/logic-apps/logic-apps-enterprise-integration-transform?WT.mc_id=iotinsightssoln-docs-ercenk)ための手段が提供されます。 ただし、データは非常に複雑な XML ドキュメントになる可能性があるため、データを変換する大規模な XSLT スクリプトを開発するのは実際的でない場合があります。 そのような場合は、さまざまな Azure サービスから複数のマイクロサービスを使用してハイブリッド ソリューションを開発することがあります。 たとえば、Azure Logic Apps に実装されたマイクロサービスでは、HTTP エンドポイントをポーリングし、生の結果を一時的に格納して、別のマイクロサービスに通知することができます。 メッセージを変換する他のマイクロサービスのカスタム コードは、[Azure Functions ホスト](https://github.com/Azure/azure-functions-host)でホストできます。  

![Functions によってデータをポーリングされて処理される Https。](assets/extracting-insights-from-iot/poll-logic-process.png)

または、Azure Data Factory によって調整されるワークフローを使用して、一連のアクティビティで変換を実行することもできます。 使用できるアクティビティの種類について詳しくは、「[Azure Data Factory のパイプラインとアクティビティ](/azure/data-factory/concepts-pipelines-activities)」をご覧ください。
受信時にメッセージにタイムスタンプを設定できます。または、メッセージに含まれるタイムスタンプを使用して、測定された複数の値の時系列を再構築することもできます。 そのため、情報の整合性と最終的な応答の適時性を保証するには、極めて小さいインジェスト待機時間と高いスループットが不可欠です。 待機時間を最小限にするため、工場のできる限り近くでタイムスタンプを正規化します。

## <a name="ingesting-the-data-stream"></a>データ ストリームの取り込み

データをストリームとして分析するには、時間枠に基づいてデータに対してクエリを実行し、パターンとリレーションシップを識別できます。 Azure プラットフォームには、高スループットでデータを取り込むことができるさまざまなサービスがあります。
以下のサービスの選択は、プロジェクトのニーズによって異なります。たとえば、デバイス管理、プロトコルサポート、スケーラビリティ、チームによるプログラミングモデルの設定などです。たとえば、チームは、エクスペリエンスのために Kafka を使用するか、ソリューションに Kafka ブローカーを用意する必要があるかを設定できます。 または、別のケースでは、取り込みポイントへのデバイスのアクセスをセキュリティで保護するために、[IoT Hub Device Provisioning Service の TPM キーの構成証明](/azure/iot-dps/?WT.mc_id=iotinsightssoln-docs-ercenk)をデータ インジェスト システムで利用することがプロジェクトで要求される場合があります。

- [Azure IoT Hub](/azure/iot-hub/?WT.mc_id=iotinsightssoln-docs-ercenk) は、IoT アプリケーションとデバイスの間に存在する双方向通信ハブです。 セキュリティで保護された通信、メッセージのルーティング、他の Azure サービスとの統合、デバイスを制御および構成するための管理機能を提供することで、完全な機能を備えた IoT ソリューションを可能にするスケーラブルなサービスです。

- [Azure Event Hubs](/azure/event-hubs/event-hubs-about?WT.mc_id=iotinsightssoln-docs-ercenk) は、非常に高いスループット レートで同時実行ソースからテレメトリ データを収集するための高スケールのインジェスト専用サービスです。
- [HDInsight での Apache Kafka](/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=iotinsightssoln-docs-ercenk) は、[Apache Kafka](https://kafka.apache.org/) をホストするマネージド サービスです。 Apache Kafka は、オープンソースの分散ストリーミング プラットフォームであり、メッセージ ブローカーの機能も提供します。 ホステッド サービスでは、Kafka のアップタイムに関して 99.9% のサービス レベル アグリーメント (SLA) があります。

## <a name="processing-and-storing-the-data"></a>データの処理と格納

IoT アプリケーションは、イベント ドリブン システムなので、履歴データを保持して操作する必要もあるという課題をもたらします。 受信データは追加型のデータであり、大規模になることがあります。 主としてアーカイブ、一括分析、機械学習 (ML) モデルの構築という理由から、長期的なデータ保持が必要になります。 その一方で、異常の検出、ローリング時間枠でのパターンの認識、値がしきい値を上回ったり下回ったりした場合のアラートのトリガーのために、ほぼリアルタイムで分析を行うには、イベント ストリームが不可欠です。
Microsoft の Azure IoT 参照アーキテクチャでは、ラムダ アーキテクチャを使用する IoT ソリューションでのデバイスからクラウドへのメッセージとイベントに対する推奨されるデータ フローが提供されます。 ラムダ アーキテクチャではほぼリアルタイムのストリーミング データとアーカイブ データ両方の分析が可能で、受信データの処理に最適なオプションです。

## <a name="lambda-architecture"></a>ラムダ アーキテクチャ

ラムダ アーキテクチャは、データ フロー用に 2 つのパスを作成することでこの問題に対処しています。 システムに送信されるすべてのデータは、次の 2 つのパスを経由します。

- バッチ レイヤー (コールド パス) は、すべての受信データを未加工の形式で保存し、データに対してバッチ処理を実行します。 この処理の結果は、バッチ ビューとして保存されます。 それは複雑な分析を実行する遅い処理のパイプラインです。たとえば、複数のソースからの長期 (時間、日、それ以上) のデータを結合し、レポートや機械学習モデルとなどの新しい情報を生成するような場合です。
- スピード レイヤー (ホット パス) では、リアルタイムでデータを分析します。 このレイヤーは、精度と引き換えに待機時間が短くなるように設計されています。 受信メッセージをアーカイブして表示し、これらのレコードを分析して短期の重要な情報とアラームなどのアクションを生成する、高速処理パイプラインです。
- バッチ レイヤーは "サービス レイヤー" にフィードし、サービス レイヤーはクエリに応答します。 バッチ レイヤーは、効率的なクエリのためにバッチ ビューにインデックスを付けます。 速度レイヤーは、最新のデータに基づく増分更新でサービス レイヤーを更新します。

次の図では、変換のステージを表す 5 つのブロックを示します。 最初のブロックはデータ ストリームであり、スピード レイヤーとバッチ レイヤーの両方に並列でフィードします。 どちらのレイヤーもサービス レイヤーにフィードします。スピード レイヤーとサービス レイヤーはどちらも、分析クライアントにフィードします。
![ラムダ アーキテクチャ。](assets/extracting-insights-from-iot/lambda-schematic.png)

 Azure プラットフォームでは、アーキテクチャの実装に使用できるさまざまなサービスが提供されています。 次の図では、それらのサービスを実装のためにマップする方法を示します。 図では、変換の 5 つのステージが示されており、各ステージに関連する Azure テクノロジが含まれます。 暗色のボックスは、これらのタスクを実行するのに複数のオプションを使用できることを表します。

![ラムダ アーキテクチャ。](assets/extracting-insights-from-iot/lambda-architecture-all.png)

スピード レイヤーのデータ インジェスト サービスのオプションについては、前のセクション「データ ストリームの取り込み」で説明されています。

[HDInsight での Apache Kafka](/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=iotinsightssoln-docs-ercenk) は、データ インジェスト サービスとストリーム処理の両方に対するデータ ストリームを実装するサービス オプションにできます。

データ インジェスト サービスに対して Event Hubs を使用する場合は、[Azure Stream Analytics](/azure/stream-analytics?WT.mc_id=iotinsightssoln-docs-ercenk) (ASA) を使用します。 Azure Stream Analytics は、デバイスからの大量のデータ ストリームを分析することができるイベント処理エンジンです。 受信データは、デバイス、センサー、Web サイト、ソーシャル メディア フィード、アプリケーションなどから得ることができます。 また、データ ストリームから情報を抽出し、パターンや関係を把握することもできます。

Stream Analytics のクエリの出発点となるのは、ストリーミング データのソースです。ソースは、Azure Event Hubs や Azure IoT Hub に取り込まれるほか、Azure Blob Storage などのデータ ストアから取り込まれます。 ストリームを分析するには、データのストリーム配信元の入力ソースを指定する Stream Analytics ジョブを作成します。 データやパターン、関係をどのように探し出すかを定義する変換クエリもこのジョブで指定します。 変換クエリには、SQL に似たクエリ言語が活用されます。特定の期間にわたるストリーミング データに対し、この言語を使ってフィルター処理、並べ替え、集計、結合が行われます。

## <a name="warm-path"></a>ウォーム パス

このドキュメントのシナリオ例は、機械使用率 KPI (ガイドの先頭で説明済み) です。 データを単純に解釈し、機械がデータを送信している場合は利用されていると想定します。 ただし、機械は実際には何も製造していないときでもデータを送信することがあります (たとえば、アイドル時やメンテナンス時)。 これは、IoT データから分析情報を抽出しようとするときの非常に一般的な課題を示します。つまり、探しているデータは、取得しているデータでは手に入りません。 そのため、この例では、機械が製造しているかどうかを明確に示すデータを取得していません。  したがって、他のデータ ソースで取得しているデータを結合し、機械が製造しているかどうかを判断するためのルールを適用することで、使用状況を推測する必要があります。 さらに、会社によって "製造" の解釈が異なる場合があるため、ルールも変わる可能性があります。 ウォーム パスは、システムを通じたデータ フローの分析のすべてです。 このストリームをほぼリアルタイムで処理し、ウォーム ストレージに保存して、分析クライアントにプッシュします。

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs は、データ フローのウォーム パスの最初のステップに最適です。

次の図では、スピード レイヤーのステージを示します。 イベント ハブ、Stream Analytics インスタンス、ウォーム ストレージ用のデータ ストアで構成されます。

![ラムダ アーキテクチャ: スピード レイヤーが強調表示されている。](assets/extracting-insights-from-iot/lambda-2.png)
  
Azure プラットフォームではイベント ハブでのイベント処理用に多くのオプションが提供されていますが、Stream Analytics をお勧めします。 Stream Analytics では、ストリーミングされたデータを視覚化するために Power BI サービスにデータをプッシュすることもできます。

Stream Analytics では、たとえばタンブリング/スライディング/ホッピング ウィンドウ、ストリーム集計、外部データ ソース結合など、大規模で複雑な分析を実行できます。 さらに複雑な処理の場合は、次の図に示すように、複数のイベント ハブ インスタンス、Stream Analytics ジョブ、Azure 関数をカスケードしてパフォーマンスを拡張できます。

![イベント ハブ、分析、Power BI。](assets/extracting-insights-from-iot/event-hubs-to-power-bi.png)
  
ウォーム ストレージは、Azure SQL Database など、Azure プラットフォーム上の各種サービスで実装できます。 お勧めは [Azure Cosmos DB](/azure/cosmos-db/introduction?WT.mc_id=iotinsightssoln-docs-ercenk) です。 それは、Microsoft のグローバル分散型マルチモデル データベースです。 柔軟でスキーマに依存しない自動インデックス作成および高度なクエリ インターフェイスからメリットが得られるデータセットに最適です。 Cosmos DB は、複数リージョンと読み取り/書き込みに対応し、自動フェールオーバーに加えて手動フェールオーバーをサポートします。 さらに、Cosmos DB では、ユーザーはデータに Time to Live (TTL) を設定でき、古いデータは自動的に期限切れになります。 レコードがデータベース内に留まる時間を制御し、したがってデータベースのサイズを制御する機能を使用することをお勧めします。

Cosmos DB の価格は、使用されたストレージとプロビジョニングされた[要求ユニット数](/azure/cosmos-db/request-units)に基づきます。 Cosmos DB は、大規模なデータ セットの集計が関係するクエリを必要としないシナリオに最適です。このようなクエリでは、デバイスの最後のイベントなどの基本的なクエリより多くの要求ユニットが必要になります。  

[Microsoft Power BI](/power-bi/power-bi-overview?WT.mc_id=iotinsightssoln-docs-ercenk) はソフトウェア サービス、アプリ、コネクタのコレクションであり、これらが連携して、関連のないデータ ソースを、一貫性があり視覚的に没入型で対話形式の分析情報に変換します。 Power BI は、重要な情報を最新の状態に維持するのに役立ちます。 [Power BI でのリアルタイム ストリーミング](/power-bi/service-real-time-streaming?WT.mc_id=iotinsightssoln-docs-ercenk)を利用して、データをサービスにプッシュできます。 このリアルタイム ストリームは、Power BI のダッシュボード上のさまざまなビジュアルに対するリアルタイム ストリーミング データ ソースとして機能できます。

## <a name="cold-path"></a>コールド パス

ウォーム パスは、時間経過に伴うパターンを検出するためのストリーム処理が行われる場所です。 ただし、機械、ライン、プラント、製造されるパーツなど、異なるピボットと集計で、過去の期間についての使用率も計算する必要があります。 これらの結果をウォーム パスの結果とマージして、ユーザーに統合されたビューを提供します。 コールド パスには、バッチ レイヤーとサービス レイヤーが含まれています。 その組み合わせにより、システムの長期的なビューが提供されます。

コールド パスには、ソリューションの長期データ ストアが含まれます。 また、長い期間に対する高速のクエリ応答を提供する事前に計算された集計ビューを作成するバッチ レイヤーも含まれます。 Azure プラットフォーム上のこのレイヤーで使用可能なテクノロジの選択肢は非常に多様です。

![ラムダ アーキテクチャ: バッチ レイヤーが強調表示されている。](assets/extracting-insights-from-iot/lambda-3.png)
  
[Azure Time Series Insights](/azure/time-series-insights/?WT.mc_id=iotinsightssoln-docs-ercenk) (TSI) は、時系列データ用の分析、ストレージ、視覚化サービスです。 SQL に似たフィルターと集計を提供し、ユーザー定義関数の必要性を減らします。 TSI は、Event Hubs、IoT Hub、Azure Blob Storage からデータを受信できます。 TSI のすべてのデータはメモリと SSD に格納され、対話型分析でデータを常に使用できることが保証されます。 たとえば、数千万件のイベントに対する一般的な集計が、ミリ秒単位で返ります。 また、異なる時系列のオーバーレイ、ダッシュボードの比較、アクセシビリティの高い表形式ビュー、ヒート マップなどの視覚化も提供されます。 TSI の主な機能は次のとおりです。

- データについてのレポートをすぐに必要としないソリューション用の組み込み視覚化サービス。 TSI では、データ レコードのクエリに対して約 30 から 60 秒の待機時間があります。 
- 大規模なデータ セットのクエリを実行する機能。
- 任意の数のユーザーが、追加コストなしで、無制限の数のクエリを実行できます。

TSI の最大リテンション期間は 400 日、最大ストレージ制限は 3 TB です。 さらに長いリテンション期間、またはより多くの容量が必要な場合は、コールド ストレージ データベースを使用します (必要に応じて、クエリ用にデータを TSI にスワップします)。

IoT アプリケーション用のコールド ストレージは、必ず時間と共に増大します。 ここでは、データは、長期間保存され、分析のためにバッチ ビューで集計されます。 ML モデル用のデータもここに格納されます。 コールド ストレージには [Azure Storage](/azure/storage/?WT.mc_id=iotinsightssoln-docs-ercenk) をお勧めします。 高い可用性とセキュリティ、耐久性、スケーラビリティ、冗長性を備えたクラウド ストレージを提供する、Microsoft が管理するクラウド サービスです。 Azure Storage には、Azure BLOB (オブジェクト)、Azure Data Lake Storage Gen2、Azure Files、Azure キュー、Azure テーブルが含まれます。 コールド ストレージには、BLOB、Data Lake Storage Gen2、Azure テーブル、またはそれらの組み合わせを使用できます。

[Azure Table Storage](/azure/cosmos-db/table-storage-overview?WT.mc_id=iotinsightssoln-docs-ercenk) は、NoSQL の構造化データをクラウド内に格納するサービスです。スキーマレスのデザインでキー/属性ストアを実現します。 Table Storage はスキーマがないため、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 Table Storage のデータ アクセスは、多くの種類のアプリケーションにとって高速でコスト効率に優れ、また一般に、従来の SQL と比較して、同様の容量のデータを低コストで保存することができます。 ここでは、サンプル用に 1 つのテーブルを使用し、データ ストリームから受信するイベント用に 1 つのテーブルを使用します。 パーティション キーの設計は特に重要な概念です。どちらのテーブルも、イベントまたはサンプルのタイムスタンプに時間を使用します。 詳細については、「 [Table サービス データ モデルについて](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?WT.mc_id=iotinsightssoln-docs-ercenk)」を参照してください。

JSON や、IoT アプリケーションによって受信される未処理データを含む XML ドキュメントなど、膨大な量の非構造化データを格納するには、[Blob Storage](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=iotinsightssoln-docs-ercenk)、[Azure Files](/azure/storage/files/storage-files-introduction?WT.mc_id=iotinsightssoln-docs-ercenk)、または [Azure Data Lake Storage Gen2](/azure/storage/data-lake-storage/introduction?WT.mc_id=iotinsightssoln-docs-ercenk) が最適なオプションです。

Azure Blob Storage には、世界中のどこからでも HTTP または HTTPS 経由で安全にアクセスできます。 Blob Storage へのアクセスは、サービスで使用されているいずれかの[承認メカニズム](/azure/storage/common/storage-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json?WT.mc_id=iotinsightssoln-docs-ercenk)を使用して、承認される必要があります。 サービスでは、複数のレプリケーション [オプション](/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json?WT.mc_id=iotinsightssoln-docs-ercenk)が提供されています (ローカル冗長、ゾーン冗長、geo 冗長、読み取りアクセス geo 冗長)。 また、最もコスト効率のよいソリューションを可能にする 3 つの[アクセス層](/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=iotinsightssoln-docs-ercenk)もあります。

データがコールド ストレージに格納されたら、ラムダ アーキテクチャのサービス レイヤー上にバッチ ビューを作成する必要があります。 [Azure Data Factory](/azure/data-factory/introduction?WT.mc_id=iotinsightssoln-docs-ercenk) は、サービス レイヤー上にバッチ ビューを作成するための優れたソリューションです。 クラウドベースのマネージド データ統合サービスを通じて、データの移動と変換を制御して自動化するデータ主導型のワークフローをクラウドに作成することができます。 Azure Data Factory を使えば、各種のデータ ストアからデータを取り込むことができる[データ主導型のワークフロー](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=iotinsightssoln-docs-ercenk) (パイプライン) を作成し、スケジューリングできます。 [Azure HDInsight Hadoop](/azure/hdinsight/?WT.mc_id=iotinsightssoln-docs-ercenk)、[Spark](/azure/hdinsight/?WT.mc_id=iotinsightssoln-docs-ercenk)、[Azure Databricks](/azure/azure-databricks/?WT.mc_id=iotinsightssoln-docs-ercenk) などのサービスを使用して、データを処理および変換できます。 これにより、機械学習モデルを構築し、分析クライアントでそれを使用できます。

たとえば、次の図に示すように、Data Factory のパイプラインは、マスター データ ストアからデータを読み取ります。 1 つのパイプラインで、データを要約して集計し、Azure Data Warehouse のインスタンスを設定します。 また、Data Factory のパイプラインには、ML モデルの構築に使用される [Azure Databricks ノートブック アクティビティ](/azure/data-factory/transform-data-using-databricks-notebook?WT.mc_id=iotinsightssoln-docs-ercenk)も含まれます。

![ラムダ アーキテクチャ: バッチ レイヤーが強調表示されている。](assets/extracting-insights-from-iot/master-data-to-ml-analytics.png)
  
[Azure SQL Database](/azure/sql-database/?WT.mc_id=iotinsightssoln-docs-ercenk) または [Azure SQL Data Warehouse](/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is?WT.mc_id=iotinsightssoln-docs-ercenk) は、バッチ ビューをホストするのに最適なオプションです。 これらのサービスでは、マスター データについて事前計算され、集計されたビューが提供できます。 

Azure SQL Database (SQL DB) は、Microsoft SQL Server データベース エンジンの最新バージョンに基づく、サービスとしてのリレーショナル データベースです。 SQL DB は、データドリブンのアプリケーションや Web サイトの構築に使用できる、高パフォーマンスで信頼性が高くセキュリティで保護されたデータベースです。 Azure サービスなので、インフラストラクチャを管理する必要はありません。 データ量が増加すると、ソリューションはクエリの高速化のためにデータを集計して格納する手法の使用を開始できます。 事前計算集計は、よく知られている手法です (特に追加専用データについて)。 コスト管理にも役に立ちます。

Azure SQL Data Warehouse では、一部のシナリオで役に立つ多くの追加機能が提供されています。 超並列処理を利用して、ペタバイト単位のデータに対して複雑なクエリを短時間で実行するクラウドベースのエンタープライズ データ ウェアハウスです。 ペタバイト規模のデータを保持し、クエリを高速で実行したい場合は、SQL Data Warehouse をお勧めします。

## <a name="visualizing-the-data"></a>データの視覚化

このレイヤーでは、2 つのデータ パイプライン (ウォーム パスとコールド パス) をマージして、データの集約されたビューを提供します。 この例では、複数のメトリックを使用して、ウォームとコールド両方のパスで機械の使用率を推定しました。 分析ステージでは、これらのパスからのデータを結合する視覚エフェクトを提供します。

![ラムダ アーキテクチャ: 分析クライアント レイヤーが強調表示されている。](assets/extracting-insights-from-iot/lambda-4.png)

[Microsoft Power BI](/power-bi/?WT.mc_id=iotinsightssoln-docs-ercenk) と [Azure Time Series Insights](/azure/time-series-insights/?WT.mc_id=iotinsightssoln-docs-ercenk) では、すぐに使用できるデータ視覚化が提供されています。 Power BI はビジネス分析ソリューションであり、データを視覚化して、組織全体で分析情報を共有したり、アプリや Web サイトに埋め込んだりすることができます。 [Power BI Desktop](https://powerbi.microsoft.com/desktop/?WT.mc_id=iotinsightssoln-docs-ercenk) は、レポートとその基になるデータ ソースをモデル化するための、強力な無料ツールです。  Power BI の視覚エフェクトを埋め込むアプリケーションでは、デスクトップ ツールによって作成され、Power BI サービス上でホストされているレポートを使用します。

Time Series Insights は、データの視覚化とクエリを行うためのデータ エクスプローラーと、REST Query API を備えています。 さらに、TSI を利用したグラフをカスタム アプリケーションに埋め込むことができる、JavaScript コントロール ライブラリが公開されています。 次に示すのは、観察されたサンプルの数を単に調べることで、作業現場での機械の使用率を近似する着信データに対する、TSI での基本的なヒートマップ ビューです。

![ラムダ アーキテクチャ: バッチ レイヤーが強調表示されている。](assets/extracting-insights-from-iot/client-screen.png)

複数のソースからデータを集約するブラウザー ベースのユーザー インターフェイスが必要な場合は、TSI と Power BI サービスのどちらでも視覚化コントロールを埋め込むことができます。 また、どちらでも、広範なカスタマイズが可能な REST API ([Power BI Rest API](/rest/api/power-bi/?WT.mc_id=iotinsightssoln-docs-ercenk)、[TSI REST API](/rest/api/time-series-insights/time-series-insights-reference-queryapi?WT.mc_id=iotinsightssoln-docs-ercenk)) と JavaScript SDK ([Power BI JavaScript SDK](https://github.com/Microsoft/PowerBI-JavaScript?WT.mc_id=iotinsightssoln-docs-ercenk)、[TSI JavaScript SDK](/azure/time-series-insights/tutorial-explore-js-client-lib?WT.mc_id=iotinsightssoln-docs-ercenk)) が提供されています。

## <a name="next-steps"></a>次の手順

多くの概念について説明し、詳細を学習して独自の要件に手法を適用するための一連の出発点を読者に提供しました。 この目的に役立つと思われるいくつかのチュートリアルを次に示します。

- データからストリームへの変換
  - [スケジュールに従って実行されるロジック アプリの作成](/azure/logic-apps/tutorial-build-schedule-recurring-logic-app-workflow?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [Logic Apps でのデータ操作のためのコード例](/azure/logic-apps/logic-apps-data-operations-code-samples?WT.mc_id=iotinsightssoln-docs-ercenk)
  - Azure 関数をホストするための[コンテナーでの Azure Functions の実行](/azure/azure-functions/functions-create-function-linux-custom-image?WT.mc_id=iotinsightssoln-docs-ercenk)については、複数の場所で説明されています。 カスタム イメージを使用した Linux での関数の作成、任意のプラットフォームでの関数の実行、Azure Functions Runtime 用の Docker イメージ
  - [Azure Functions でさまざまなバインドを使用する](/azure/azure-functions/functions-triggers-bindings?WT.mc_id=iotinsightssoln-docs-ercenk)

- ホット パス
  - Event Hubs、Azure Stream Analytics、Power BI の使用方法を示すエンド ツー エンド チュートリアル。 「[チュートリアル: Azure Event Hubs に送信されたリアルタイム イベントのデータの異常を視覚化する](/azure/event-hubs/event-hubs-tutorial-visualize-anomalies?WT.mc_id=iotinsightssoln-docs-ercenk)」および「[Stream Analytics ジョブを作成し、通話データを分析して結果を Power BI ダッシュボードで視覚化する](/azure/stream-analytics/stream-analytics-manage-job?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。
  -[.NET での Azure Cosmos DB の使用](/azure/cosmos-db/sql-api-get-started?WT.mc_id=iotinsightssoln-docs-ercenk)
- コールド パス
  - Azure Data Factory で [Spark アクティビティを使用してクラウドのデータを変換する](/azure/data-factory/tutorial-transform-data-spark-portal?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [チュートリアル: Azure Time Series Insights 環境を作成する](/azure/time-series-insights/tutorial-create-populate-tsi-environment?WT.mc_id=iotinsightssoln-docs-ercenk)
- 分析クライアント
  - [Power BI について](/power-bi/guided-learning/?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [Time Series Insights SPA を作成する](/azure/time-series-insights/tutorial-create-tsi-sample-spa?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [Time Series Insights Java Script クライアント ライブラリを調べる](/azure/time-series-insights/tutorial-explore-js-client-lib?WT.mc_id=iotinsightssoln-docs-ercenk)
  - [TSI のデモ](https://insights.timeseries.azure.com/demo)および [Power BI のデモ](https://microsoft.github.io/PowerBI-JavaScript/demo/v2-demo/index.html)をご覧ください。

## <a name="appendix-pillars-of-software-quality-posq"></a>付録: ソフトウェア品質の重要な要素 (PoSQ)

成功するクラウド アプリケーションは、[ソフトウェア品質の 5 つの重要な要素](/azure/architecture/guide/pillars?WT.mc_id=iotinsightssoln-docs-ercenk)、すなわち拡張性、可用性、回復性、管理性、セキュリティに基づいて構築されています。 ここでは、必要に応じて、コンポーネントごとにこれらの要素について簡単に説明します。 可用性、回復性、管理性、および DevOps については説明しません。これらは主として実装レベルで対応されており、ここでは Azure プラットフォームが API、ツール、診断、ログによってこれらを実現するための広範な手段を提供していることを説明したいためです。 上記の要素に加えて、コスト効率についても説明します。

以下の要素について簡単に確認しておきます。

- **拡張性**は、増加した負荷を処理する、システムの能力です。 アプリケーションをスケーリングする方法は、主に 2 つあります。 垂直スケーリング (スケールアップ) は、VM のサイズを大きくするなど、リソースの容量を増やすことです。 水平スケーリング (スケールアウト) とは、VM やデータベース レプリカなど、リソースの新しいインスタンスを追加することです。 拡張性要素には、パフォーマンスと負荷処理機能も含まれています。
- **可用性**とは、システムが動作し実際に使用可能である時間の割合です。 通常は、アップタイムの割合として測定されます。 アプリケーション エラー、インフラストラクチャの問題、システムの負荷はすべて、可用性を低下させる可能性があります。 Microsoft Azure サービスのサービス レベル アグリーメントについては、「[サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。 可用性は、システム レベルでのみ意味のあるメトリックです。 個別のコンポーネントが、システムの全体的な可用性に寄与します。
- **回復性**とは、障害から回復して動作を続行する、システムの能力です。 回復性の目的は、障害の発生後にアプリケーションを十分に機能する状態に戻すことです。 回復性は、可用性に密接に関連しています。
- **管理性と DevOps**。 この要素は、運用環境においてアプリケーションの稼働を維持する運用プロセスを対象とするものです。 デプロイは、信頼性が高く、予測可能である必要があります。 人的ミスの可能性を下げるために、自動化されている必要があります。 迅速かつ定期的なプロセスであるべきで、新機能やバグ修正のリリースを遅らせることはありません。 同じくらい重要なのは、　更新プログラムに問題がある場合に、ご自分で迅速にロールバックまたはロールフォワードできる必要があるということです。
- 設計と実装から、デプロイと運用まで、ソリューションのライフ サイクル全体を通して、**セキュリティ**を主要な対象にする必要があります。 ID 管理、インフラストラクチャの保護、アプリケーションのセキュリティ、承認、データの主権と暗号化、監査はすべて、対処する必要がある広範な領域です。

## <a name="posq-converting-the-data-to-a-stream"></a>PoSQ: データからストリームへの変換

**拡張性**: 拡張性には、2 つの観点からアプローチできます。 1 つ目はコンポーネントの観点で、2 つ目はソース データを提供するシステムの観点です。

各 Azure サービスでは、垂直スケーリングと水平スケーリングのオプションが提供されています。 ソリューションの設計時に、拡張性の要件を検討することを強くお勧めします。

ソース データを提供するシステムの場合、あまり頻繁にクエリを実行することにより、システムが過負荷になって、基本的にシステムに対するサービス拒否 (DoS) 攻撃にならないように、十分注意する必要があります。 システムをポーリングする場合は、ポーリング頻度の調整に 2 つの影響があることに留意する必要があります。つまり、データの粒度 (クエリの頻度を高くするほど、リアルタイムに近くなります) と、リモート システムにかかる負荷です。

**セキュリティ**: 対称キーまたは非対称キーを使用してリモート システムにアクセスする場合、シークレットを [Azure Key Vault](/azure/key-vault/?WT.mc_id=iotinsightssoln-docs-ercenk) で保持することをお勧めします。

## <a name="posq-warm-path"></a>PoSQ: ウォーム パス

**拡張性**: インジェスト サブシステムで Azure Event Hubs を使用する場合、メインの拡張性メカニズムは[スループット ユニット](/azure/event-hubs/event-hubs-features#throughput-units?WT.mc_id=iotinsightssoln-docs-ercenk)です。 Event Hubs では、スループット ユニットを静的に設定する機能、または[自動インフレ機能](/azure/event-hubs/event-hubs-auto-inflate?WT.mc_id=iotinsightssoln-docs-ercenk)によって設定する機能が提供されます。

Stream Analytics の[ストリーミング ユニット](/azure/stream-analytics/stream-analytics-streaming-unit-consumption?WT.mc_id=iotinsightssoln-docs-ercenk) (SU) とは、ジョブを実行するために割り当てられる計算リソースのことです。 SU 数が大きいほど、多くの CPU とメモリ リソースがジョブ用に割り当てられます。 この能力により、クエリ ロジックに集中することができ、Stream Analytics ジョブがタイミングよく実行されるようハードウェアを管理する必要がなくなります。 SU だけでなく、[クエリを適切に並列化する](/azure/stream-analytics/stream-analytics-scale-jobs?WT.mc_id=iotinsightssoln-docs-ercenk)ことで効率的に使用することが非常に重要です。

Azure Cosmos DB の実装は、適切なスループット パラメーターと適切なパーティション分割設計でプロビジョニングする必要があります。 スループットのプロビジョニングは、コンテナー レベルまたはデータベース レベルで利用できます。詳しくは、[要求ユニット](/azure/cosmos-db/request-units?WT.mc_id=iotinsightssoln-docs-ercenk) (RU) に関するページをご覧ください。 Cosmos DB では、RU を見積もるためのツールが提供されています。 スループットをプロビジョニングするだけでなく、[データベースを効率的にパーティション分割する](/azure/cosmos-db/partition-data?WT.mc_id=iotinsightssoln-docs-ercenk)ことが重要です。

**セキュリティ**: クライアントによる Azure Event Hubs へのアクセスでは、Shared Access Signature (SAS) トークンとイベント パブリッシャーの組み合わせがクライアント認証に使用されます。 バックエンド アプリケーションのセキュリティは、Service Bus のトピックと同じ概念に従います。 Event Hubs のセキュリティ モデルについて詳しくは、「[Event Hubs の認証とセキュリティ モデルの概要](/azure/event-hubs/event-hubs-authentication-and-security-model-overview?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。

Cosmos DB データベースのセキュリティ保護では、データに対する制御されたアクセスと、保存時の暗号化が提供されます。 詳しくは、「[Azure Cosmos DB データベースのセキュリティ](/azure/cosmos-db/database-security?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。

**コスト効率**: Event Hubs の価格は、SKU (Standard または Premium)、受信したイベントの数 (百万単位)、およびスループット ユニットの関数です。 最適な組み合わせは、受信メッセージによって決定されるデータ取り込み率を調べることによって実現できます。

Cosmos DB を使用するときは、RU の使用率によってストアの最適な使用を観察することをお勧めします。 Cosmos DB には、データ保持を制御するための機能もあります。前に説明したように、この機能を使用してレコードがデータベースに留まっている時間を制御し、それによってデータベースのサイズを制御することをお勧めします。

## <a name="posq-cold-path"></a>PoSQ: コールド パス

**拡張性**: Azure Time Series Insights (TSI) は、"容量" という名前のメトリックによってスケーリングされます。これは、SKU に関連する受信レート、ストレージ容量、コストに適用される乗数です。 

Azure Time Series Insights には複数の SKU があり、やはり垂直スケーリングに直接影響します。 スケーリングについて詳しくは、「[Azure Time Series Insights 環境の計画](/azure/time-series-insights/time-series-insights-environment-planning?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。 他の多くの Azure サービスと同じように、TSI も "迷惑な隣人" 問題を回避するための調整の対象になります。 迷惑な隣人とは、共有環境 (/azure/sql-database/sql-database-service-tiers-vcore?WT.mc_id=iotinsightssoln-docs-ercenk) でリソースを独占し、他のユーザーを枯渇させるアプリケーションのことです。 調整の管理については、[TSI のドキュメント](/azure/time-series-insights/time-series-insights-environment-mitigate-latency?WT.mc_id=iotinsightssoln-docs-ercenk)をご覧ください。 

ストレージ アカウントの拡張性ターゲットについては、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](/azure/storage/common/storage-scalability-targets?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。 1 つのストレージ アカウントの容量を超えてデータを格納するための一般的な手法は、複数のストレージ アカウント間でのパーティション分割です。

Azure SQL Database には、購入モデル ([DTU ベース](/azure/sql-database/sql-database-service-tiers-dtu?WT.mc_id=iotinsightssoln-docs-ercenk)と仮想コア ベース) に応じて垂直と水平両方の拡張性を管理するための多くのオプションがあります。 [SQL Database のドキュメント](/azure/sql-database/sql-database-scale-resources?WT.mc_id=iotinsightssoln-docs-ercenk)を使用して将来のソリューションに対する最適なオプションをさらに調査することをお勧めします。

**セキュリティ**: TSI 環境では、管理アクセスとデータ アクセスに対して個別に[アクセス ポリシー](/azure/time-series-insights/time-series-insights-data-access?WT.mc_id=iotinsightssoln-docs-ercenk)が提供されます。 定義されているデータ ソース以外に、TSI 環境にデータを直接追加する方法はありません。 管理アクセス ポリシーでは、環境の構成に関連したアクセス許可が付与されます。 データ アクセス ポリシーは、データ クエリの発行、環境内での参照データの操作、環境に関連付けられた保存クエリとパースペクティブの共有を実行するためのアクセス許可を付与します。

Azure Data Factory サービスでは、マネージド ストアまたは Azure Key Vault で、データ ストアの資格情報を保護する複数の方法が提供されています。 転送中のデータの暗号化は、データ ストアのトランスポート (HTTPS、TLS など) に依存します。 保存時のデータの暗号化は、データ ストアにも依存します。 詳しくは、「[Azure Data Factory におけるデータ移動のセキュリティに関する考慮事項](/azure/data-factory/data-movement-security-considerations?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。

SQL Database では、データ アクセス、監視と監査、および保存時のデータの暗号化に関する広範なセキュリティ機能のセットが提供されています。 詳しくは、「[SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database?WT.mc_id=iotinsightssoln-docs-ercenk)」をご覧ください。

**コスト効率**: すべての分析ソリューションの中心にあるのはストレージです。 分析エンジンでは、大量のデータを妥当な時間で処理するための速度、効率、セキュリティ、およびスループットが必要です。 データの集計と要約により基盤プラットフォームを最適に使用するメカニズムの考案と、ポリグロットなストアの効率的な使用が、コストを効率的に管理するための手段です。 Azure はクラウド プラットフォームなので、プログラムで使用停止、使用再開、リソース サイズ変更を行うメソッドがあります。 たとえば、[作成または更新操作](/rest/api/sql/databases/createorupdate?WT.mc_id=iotinsightssoln-docs-ercenk)では、Azure SQL Database のデータベースのサイズを変更する方法が提供されます。