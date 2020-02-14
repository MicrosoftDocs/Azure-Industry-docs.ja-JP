---
title: 製造業における Azure ML と IoT を使用した予測メンテナンス
author: ercenk
ms.author: ercenk
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 製造業のお客様向けに Azure で予測メンテナンスを開発する方法を説明したソリューション ガイド。
ms.openlocfilehash: c32893d534279cda35f7c6a142869d2983eaca67
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77053846"
---
# <a name="predictive-maintenance-in-manufacturing-solution-guide"></a>製造業における予測メンテナンスのためのソリューション ガイド


## <a name="introduction"></a>はじめに

予測メンテナンスは、センサー、人工知能、およびデータ サイエンスを組み合わせて利用することにより、機器の保守を最適化します。 機器の保守を予測することは、保守コストを最小限に抑え、稼働時間を最大化するために必要であり、これを実現することで、製造業者に大きな価値がもたらされます。

このソリューションの中心となるのは、データです。 データには、適切な故障インジケーターに加えて、状況を説明するその他の側面を含める必要があります。 このようなデータは、センサー、機械のログ、および製造アプリケーション ログから収集できます。

この記事では、予測メンテナンス ソリューションを構築するためのオプションについて説明します。 また、さまざまな視点と、始めるために役立つ既存の参考資料も紹介します。 予測メンテナンス ソリューションの要件は、機器、環境、プロセス、および組織によって異なります。そのため、ここでは、ニーズに適したソリューションの構築を進める上で役立つ代替アプローチやテクノロジについても説明します。

まず、予測メンテナンス ソリューションを構成するコンポーネントの概要から説明します。


![高度なソリューション](./assets/pdm-assets/highlevelsolution.png)

この作業内訳は、発生する作業を大まかにまとめたものです。

1. 故障データを含めて、トレーニング データを収集する

2. このトレーニング データを使用し、一連の条件を指定して、将来の資産の故障を予測できるように機械学習 (ML) モデルのトレーニングを行う

3. データの収集を継続的に行う

4. 収集したデータを ML モデルに入力し、ML モデルは、通常、ある程度の信頼度で故障を予測する (たとえば、“次の 24 時間以内に機械が故障する確率は 85%” など)

5. 予測される故障ケースを明らかにする

6. データから得られる洞察に基づいて計画を立て実践する

## <a name="training-the-ml-model"></a>ML モデルのトレーニング

ML モデルを構築するには、十分な量の正確かつ完全なデータが必要です。 さらに、予測メンテナンスは、特有の課題をもたらします。その中で最も大きな課題は、故障データの可用性です。 故障は、比較的まれな事故です。特に、CNC マシンや石油精製所のコンポーネントなど、高額の設備投資を必要とする機器では、めったに発生しません。このため、センサー データを長期間収集しても、十分な故障データが得られない可能性があります。 “故障” をどのように定義するか、つまり、正確には何を故障とするかを検討する必要があります。 デバイスが予想外に機能を停止したときですか?
デバイスが、もはや期待されるレベルのパフォーマンスを発揮できない程度まで性能が低下したときですか? 故障ケースは、金属疲労に起因する部品の故障のために壊れた切断機、または大惨事が発生する前に、故障を示すその他のインジケーターですか?

## <a name="considering-the-data-needed-for-ml"></a>ML に必要なデータについて検討する

これらの故障を正確に記録するのに十分なデータを取得しているかどうかについても検討する必要があります。 多くの場合、センサー データだけでは、故障を特定するには十分ではありません。 場合によっては、機械の状態を故障状態として “フラグ” を付けるための外部データや、二次的な情報源 (故障ケースを別のシステムから取得するオペレーターなど) が必要になることがあります。 このデータは、ERP、製造実行システム (MES)、履歴などに存在しているだけでなく、製造会社でよく見られる評判の良くない IT/OT の分離を超えて存在する可能性があるため、必要なデータのセキュリティ保護をさらに困難にします。

本来、予測メンテナンスは動的な問題であるため、関連する機械学習モデルについては、更新 (または再トレーニング) を継続的に行う必要があります。 うまくいけば、予測メンテナンスによって、故障の発生件数は削減されます。これは、良いことではありますが、その結果、故障データは減少します。 また、故障に影響を及ぼす機能が変更され、以前の機械学習モデルが無効になる場合もあります。 このため、故障条件に何らかの変更を加えてモデルのトレーニングを定期的に行うことをお勧めします。

“最新の” データは、以前にモデルのトレーニングに使用された条件以外の、モデルに新しく取り込まれた条件を意味します。 つまり、故障を変数 _x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>n</sub>, f(x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>n</sub>)_ の関数としてモデル化できますが、最終的には、変数 _x<sub>(n+1)</sub>,⋯,x<sub>(m+n)</sub>_ も故障に影響することを発見する可能性があり、_f(x<sub>1</sub>,x<sub>2</sub>,⋯,x<sub>(m+n)</sub>)_ に合わせてモデルのトレーニングを変更することが必要になります。 モデルは、故障の検出で十分なパフォーマンスを発揮していない可能性があれば、次の繰り返しのためにも、機械の MES ログから得られたデータ ポイントを含む新しいモデルを構築することになります。

データをクラウドにストリーミングする最新の IoT 環境がなくても、機械学習モデルのトレーニングに必要なデータは、MES、履歴、またはその他の実稼働システムに既に存在している可能性があります。 データが準備されているかどうかだけの問題であり、データがあれば、それを使用して機械学習モデルのトレーニングを行うことができます。

次の図は、ML モデルのトレーニングを行うための一般的なワークフローを示しています。 “繰り返し” というラベルの付いた矢印は、これが反復プロセスであることを示しています。つまり、最新のデータが記録されたり、条件が変更されたりした場合に、モデルの再トレーニングを継続的に行うプロセスです。 このループを繰り返す時期と頻度は、実装固有の条件によって異なります。このため、モデルの “老朽化” または “機能低下” を検出するには、以前構築したモデルが故障を予測する際のパフォーマンスを注意深く監視する必要があります。

継続的に新しいモデルのトレーニングを行い、それをデプロイしても、これには、モデルの管理という別の課題が伴います。 Microsoft は、モデルの CI/CD (継続的インテグレーションおよび継続的デリバリー) のための Azure Machine Learning モデル管理サービスを提供しています。

![ML モデル構築の段階](assets/pdm-assets/mlmodelbuildingstages.png)

Microsoft は、データを準備し、機械学習モデルをトレーニングする方法について[詳細なガイド](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/cortana-analytics-playbook-predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk)を発行しています。 メンテナンスのための 3 つの一般的な質問とそれに関連する機械学習アルゴリズムがあります。

- "_今から X 時間以内にその資産で障害が発生する確率はどのくらいですか。_ " 回答: 0 から 100%
  - **二項分類:** 二項分類は、データを使用して、2 つのクラスの一方のメンバーとして、データの項目または行のカテゴリ、タイプ、またはクラスを決定する機械学習メソッドです。 分類アルゴリズムには複数の種類があり、Microsoft は、[Machine Learning Studio モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model-classification?WT.mc_id=pdmsolution-docs-ercenk)として利用可能な一連のアルゴリズムを公開しています。
- "_その資産の残存耐用時間数はどのくらいですか。_ " 回答: X 時間
  - **回帰:** 回帰は、機械学習アルゴリズムの一種で、指定された一連の他の変数から 1 つの変数の値を予測します。 Machine Learning Studio には、一連の回帰アルゴリズムが[モジュール](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model-regression?WT.mc_id=pdmsolution-docs-ercenk)として含まれています。
    - **長期間の短期メモリ (lstm):** [lstm](https://colah.github.io/posts/2015-08-Understanding-LSTMs/?WT.mc_id=pdmsolution-docs-ercenk)ネットワークは、ディープニューラルネットワーク (dnn) の一種です。 DNN は、脳内の個々のニューロンの動作に着想を得て、モデル化されたものです。 Microsoft は、LSTM を予測メンテナンスに使用する方法を説明するために、[ステップ バイ ステップ ガイド](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/scenario-deep-learning-for-predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk)を発行しています。
- "_最も緊急に整備を必要としているのはどの資産ですか。_ " 回答: 資産 X
  - **多クラス分類:** 多クラス分類は、データを使用して、3 つ以上のクラスのメンバーとして、データの項目または行のカテゴリ、タイプ、またはクラスを決定する機械学習メソッドです。

データを取り込むということは、複数のチャネルを利用し、最初に一括して初期化した後、故障を予測するためにストリーミング データの受信を継続し、そのデータを以降のモデルの構築にも使用することを意味します。

## <a name="bringing-data-to-azure"></a>データを Azure に取り込む

Microsoft Azure には、データを取り込んで格納するための各種サービスが用意されています。 Azure に転送するデータがまだない場合、データを取得するにはバッチ メソッドをお勧めします。 データをファイルとして既知の形式 (csv、json、xml など) にエクスポートできる場合、バッチ メソッドは適切な選択肢です。 また、データを圧縮してからアップロードし、クラウド側で処理するという方法を選択することもできます。

- [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?WT.mc_id=pdmsolution-docs-ercenk) を使用して Blob ストレージにアップロードする (Windows と Linux の両方)

- Linux にファイル システムとして [Blob ストレージをマウントする](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux?WT.mc_id=pdmsolution-docs-ercenk)

- データ サイズが大きく、アップロードに時間がかかりすぎる場合は、[インポート/エクスポート サービス](https://docs.microsoft.com/azure/storage/common/storage-import-export-service?WT.mc_id=pdmsolution-docs-ercenk)を使用する

- Windows、Linux、MacOS に[Azure ファイル共有をマウントする](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows?WT.mc_id=pdmsolution-docs-ercenk)

データが SQL Server データベース内にある場合、[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?WT.mc_id=pdmsolution-docs-ercenk) を使用して、データを Azure SQL Database にアップロードすることもできます。

Azure プラットフォームには、抽出、変換、読み込み (ETL) 操作のための各種のツールおよびサービスがあります。 最もよく知られているサービスは、[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/?WT.mc_id=pdmsolution-docs-ercenk) です。これは、データを操作するための完全な機能セットを提供します。 データを操作するためのその他のオプションは、オープン ソース ライブラリを介して Azure で使用可能な数多くの ML サービスで提供されます。

ML モードのトレーニングについては、Microsoft Azure は数多くのオプションを提供しています。これらはすべて、さまざまに組み合わせて使用することができます。

- [Azure Machine Learning サービス](https://docs.microsoft.com/azure/machine-learning/preview/?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/?WT.mc_id=pdmsolution-docs-ercenk)

- [Data Science Virtual Machine](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/?WT.mc_id=pdmsolution-docs-ercenk)

- [HDInsight の Spark MLLib](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-machine-learning-mllib-ipython?WT.mc_id=pdmsolution-docs-ercenk)

- [Batch AI トレーニング サービス](https://docs.microsoft.com/azure/batch-ai/?WT.mc_id=pdmsolution-docs-ercenk)

どのツールを使用するかは、操作の複雑さ、チームの経験、およびデータのサイズによって決まります。

クラウドソリューションのコスト式には、追加のエンジニアリング、管理、データ転送などのクラウドサービスのコストに加えて、多くの変数が含まれています。コストを評価するときにこれらの変数を使用し、十分な情報に基づいた意思決定を行います。 合計コスト方程式を構成するのは、サービスだけではありません。

詳細な科目としては、データ分析プロセスおよびモデル公開プロセスの設計があり、これらは、使用されるテクノロジによって異なります。 これらのトピックは、この記事の範囲外ですが、 モデルの生成に使用できるプロセスと Azure サービスについて説明した一連の記事を参照することができます。 また、Microsoft は、データ サイエンティスト チームがデータのライフサイクル全体で効果的に共同作業できる、データ ソリューションを構築するための組織的なアプローチも提供しています。

Microsoft の [Azure Machine Learning のドキュメント](https://docs.microsoft.com/azure/machine-learning?WT.mc_id=pdmsolution-docs-ercenk)は、ML モデルおよび AI モデルを構築し、クラウドにデプロイして管理するためのオプションを調べる出発点として適しています。

Microsoft Azure プラットフォームは、データを一括処理し、ML モデルを構築するための豊富な選択肢を提供しています。 クラウド プラットフォームでは、ほとんど無限でスケーラブルなコンピューティング能力とストレージ容量を利用できるため、ML モデルおよび AI モデルを構築することができます。 このため、モデルの構築に Azure サービスを利用することは、このデータ フローを実装するための合理的なオプションです。

## <a name="using-the-model"></a>モデルの使用

ML モデルを構築したら、それを実行 (または “使用”) して機器の保守の必要性を予測するためのメカニズムが必要です。 機器から受信されたデータは、処理レイヤーを通過し、それに基づいて将来の故障ケースが予測され、メンテナンス チームが実行できるさまざまな対策が提示されます。

![モデルの使用](assets/pdm-assets/usingthemodel.png)

データを取り込むには、オンラインでライブ センサー データをソリューションにストリーミングするか、オフラインでセンサー データをソリューションに定期的にインポートします。

Microsoft Azure プラットフォームには、データの取り込み、処理、および保存のための次のような各種サービスが用意されています。

- [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview?WT.mc_id=pdmsolution-docs-ercenk)

- [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-what-is-iot-hub?WT.mc_id=pdmsolution-docs-ercenk)

- [Apache Kafka for HDInsight](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-introduction?WT.mc_id=pdmsolution-docs-ercenk)

ML モデルを構築するプロセスとは異なり、ML モデルを使用するために、大量の計算リソースは必要ありません。 ニーズに応じて、モデルをクラウド内のサービスにデプロイすることも、工場でローカルに展開することもできます。

ML モデルをローカルのみまたはクラウドのみで実行する場合、主に次の 2 つの代替方法があります。

## <a name="local-execution"></a>ローカル実行

ML モデルはローカルで実行されますが、データは、取り込み、保存、さらなる処理のためにクラウドに送信されます。 このオプションは、早期検出が重要となるシナリオに適しています。

![ローカル実行](assets/pdm-assets/localandcloud.png)

## <a name="cloud-execution"></a>クラウド実行

取り込み、処理、および保存と ML モデルの実行はすべて、Azure クラウドで発生します。 このオプションは、複数のテナント間または地域間で ML モデルの実行結果を共有する (さらに待ち時間が重要ではない) 場合の方が適しています。 “エッジ ゲートウェイ” と呼ばれることが多いオプションのコンポーネントは、データの集計とプロジェクト、ストリーム分析などの一部の作業を実行するために、[“アンバサダー” パターン](https://docs.microsoft.com/azure/architecture/patterns/ambassador?WT.mc_id=pdmsolution-docs-ercenk)と呼ばれるパターンに従って、ローカルで追加することができます。

Azure でモデルを使用するには、複数の方法があります。 [Azure Machine Learning Web サービス](https://docs.microsoft.com/azure/machine-learning/studio/consume-web-services?WT.mc_id=pdmsolution-docs-ercenk)は最も簡単な方法で、モデルの作成方法として [Azure Machine Learning Studio](https://docs.microsoft.com/azure/machine-learning/studio/what-is-ml-studio?WT.mc_id=pdmsolution-docs-ercenk) を使用します。 [Azure Machine Learning モデル管理](https://docs.microsoft.com/azure/machine-learning/preview/model-management-overview?WT.mc_id=pdmsolution-docs-ercenk)を選択することもできます。これは、モデルを管理するための包括的なサービス セットを提供し、認証、負荷分散、自動スケールアウト、および暗号化の各機能とともに、REST API エンドポイントも提供します。 モデルは、単一のマシン (たとえば、Data Science Virtual Machine、IoT デバイス、ローカルの PC など) または [Azure Container Service](https://docs.microsoft.com/azure/aks/intro-kubernetes?WT.mc_id=pdmsolution-docs-ercenk) にデプロイすることができます。 モデルを REST API を通じて公開すると、それが使用される可能性は、カスタム アプリケーションからエンタープライズ ソリューション統合まで無限です。

![クラウドのみ](assets/pdm-assets/cloudonly.png)

クラウドのみのデプロイは、必ずしも、データのライブ ストリーミングのみであることを意味するとは限りません。 考えられる戦略としては、ローカル システム (たとえば、履歴や MES など) からデータを定期的にエクスポートし、それをクラウド システムにインポートして、結果を提供することです。 このオプションは、デバイスがデータをシステムに直接プッシュできない場合、既存のシステムが既にデータを収集している場合、またはほぼリアルタイムのデータ処理が必要ない場合に実現可能な手法になる可能性があります。 このような場合、エッジ ゲートウェイを検討する必要はありません。

## <a name="predictive-maintenance-in-the-iot-context"></a>IoT コンテキストでの予測メンテナンス

多くの IoT ソリューションは、機能セットの一部としてデータを取り込んで保存します。 さらに、多くの場合、予測メンテナンス ソリューションは IoT データに依存するため、IoT ソリューションに自然に機能を追加できます。 このコンテキストで強調すべき重要な点は、故障について予測モデルをトレーニングするために、故障を既存のデータに記録させることの重要性です。

一部のユース ケースには、ほぼリアルタイムのデータ処理が必要です。 このような場合、データ インジェスト率の高い機能を備えたスケーラブルな IoT ソリューションが必要です。 Microsoft Azure プラットフォームには、スケーラビリティに優れた IoT のニーズを満たすためのソリューションを実現できる数多くのサービスが用意されています。 Azure プラットフォーム上の [Microsoft の IoT ソリューション アーキテクチャ](https://docs.microsoft.com/azure/iot-suite/iot-suite-what-is-azure-iot?WT.mc_id=pdmsolution-docs-ercenk)には、次の 3 段階で論理コンポーネントが含まれています。

- デバイスの接続性

- データ処理と分析

- プレゼンテーション

![IoT ソリューションのアーキテクチャ](assets/pdm-assets/iot.png)

Azure IoT ソリューション アーキテクチャの詳細は、[オンラインで入手できます](https://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf?WT.mc_id=pdmsolution-docs-ercenk)。
ただし、膨大な数のデバイスがバックエンド サービスに接続される可能性があるため、特有の課題が発生する可能性があります。

## <a name="data-ingestion-and-stream-processing"></a>データ インジェストとストリーム処理

デバイスからデータを取り込むのは、2 つの個別のサービス間で行われる通信の問題です。つまり、データを生成するシステム (デバイス) と、このデータを処理するシステム (ML モデルをトレーニングし、受信データ ポイントとトレーニングされたモデルを比較して、残存耐用年数を予測するシステム) との間の通信です。

定義上、分散システムは、本来相互に通信する必要がある個別のコンポーネントで構成されます。 通信を可能にするための 1 つのオプションとして、関連するコンポーネントに相互に直接通信させることが考えられます。 これにより、保守とスケーリングが困難なシステムが作成されます。 コンポーネントの数が増加すると、通信リンクが複雑になります (_O(n<sup>2</sup>)_ )。 これよりも適切なアプローチは、データを共通ハブに投稿し、共通ハブから読み取る方法です。

![サブコンポーネントの通信](./assets/pdm-assets/subcomponentcommunication.png)

データ インジェスト用の新しいコンポーネントを挿入すると、通信の拡張性は向上します。 このコンポーネントは、データ インジェスト プロセスを地理的に分割するオプションを備えた、スケーラブルかつセキュアで、ほぼ確実に世界中からアクセスできる必要があります。 

予測メンテナンスを検討することは、IoT ソリューションの機能です。 データはゲートウェイ経由でストリーミングされるため、予測メンテナンス機能と関連するサービスにルーティングする必要があります。
もう 1 つの検討すべきパターンは、[ゲートウェイ ルーティング](https://docs.microsoft.com/azure/architecture/patterns/gateway-routing?WT.mc_id=pdmsolution-docs-ercenk)です。

どちらのパターンも、Azure サービス、[IoT ハブ](https://azure.microsoft.com/services/iot-hub/?WT.mc_id=pdmsolution-docs-ercenk)、[Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/?WT.mc_id=pdmsolution-docs-ercenk) を使用して実現することができます。

## <a name="edge-and-cloud-processing-cooperation"></a>エッジとクラウドの処理の連携

必ずしもすべてのデバイスや機器が、一貫してインターネットに直接アクセスできるとは限りません。
場合によっては、それらのデータを共通ゲートウェイから抽出する必要があります。 たとえば、[MTConnect](https://www.mtconnect.org/) エージェントは、データを抽出するための REST インターフェイスを提供するだけです。

他にも、待ち時間、デバイス データをクラウドに送信する前にローカルでスクラブする必要性 (マルチテナントの場合)、デバイス データに対してプロジェクションまたは集計を実行する必要性などの考慮事項が考えられます。 [アンバサダー パターン](https://docs.microsoft.com/azure/architecture/patterns/ambassador?WT.mc_id=pdmsolution-docs-ercenk)は、これらのニーズに対応するための有効なアプローチです。 [Microsoft Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works?WT.mc_id=pdmsolution-docs-ercenk) は、[Microsoft Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/?WT.mc_id=pdmsolution-docs-ercenk) のためのプロキシとして動作できるだけでなく、ローカル処理機能とリモート管理を提供することもできる実装です。

一般的な展開には、店舗でのほぼリアルタイムのアラートが含まれますが、データは依然として、アーカイブ、モデルのトレーニング、およびタイム クリティカルではないレポート生成のためにスクラブされ、クラウド内のマルチテナント ソリューションに投稿されます。 お客様は、Azure IoT Edge と IoT Hub の機能を利用して、エッジ デバイスでのデータ フィルタリング オプションを制御できるだけでなく、他の店舗システムと対話してアラートを配信することができます。

![マルチテナント](assets/pdm-assets/multitenant.png)

## <a name="multitenant-perspective"></a>マルチテナントの視点

前述のとおり、一部の製造業者またはサード パーティは、予測メンテナンス サービスをお客様に提供することが必要になる場合があります。 これらのサービスは、マルチテナント クラウド デプロイで提供される可能性が高く、独自の一連の課題を提示します。

### <a name="data-security-and-isolation"></a>データのセキュリティと分離

サービスを提供する当事者は、顧客からの機密情報が識別され、適切に保護またはスキャンされされていることを確認する必要があります。Microsoft Azure は、使用するストレージサービスに応じてデータを暗号化するための機能を提供します。

デバイスがデータを生成して送信する方法も、デバイスごとの証明書、デバイスごとの有効化/無効化、TLS セキュリティ、X.509 サポート、IP のホワイトリスト登録/ブラックリスト登録、共有アクセス ポリシーなどの既知の手法を使用してセキュリティで保護する必要があります。 サービスを提供する当事者は、顧客からの機密情報が識別され、適切にセキュリティで保護されていることを確認する必要があります。[Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-encryption?WT.mc_id=pdmsolution-docs-ercenk)、 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?WT.mc_id=pdmsolution-docs-ercenk)、 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest?WT.mc_id=pdmsolution-docs-ercenk)、および[Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql?WT.mc_id=pdmsolution-docs-ercenk)は、保存データの暗号化に使用できるサービスの例です。 ソリューション プロバイダーは、同一のリソース (たとえば、データベース) または複数のリソース内でデータをパーティション分割する方法も検討する必要があります。 

### <a name="geographical-considerations"></a>地理的な考慮事項

ほとんどの場合、データを生成するデバイスは、地理的に分散されます。 ソリューションでは、データ ソースに最も近いインジェスト ポイントを提供する必要があります。 また、継続的な接続が問題となる場合、データを一括して取り込むことが必要な場合、またはローカルのストア アンド フォワード メカニズムを提供することが必要な場合もあります。

### <a name="scalability"></a>スケーラビリティ

ML モデルの構築には、柔軟にスケーリングできるコンピューティング リソースが必要です。
ソリューション プロバイダーは、コンピューティング リソースを効果的に使用するプロセスを策定し、そのプロセスを使用して、ソリューションをオンデマンドでスケーリングする必要があります。

### <a name="provisioning-tenants-and-secure-access"></a>テナントのプロビジョニングとセキュリティで保護されたアクセス

サービス プロバイダーは、効果的に新しいテナントをオンボーディングする方法と、テナント自身がそのアカウントを管理するための手段を提供する方法を考案する必要があります。 この場合、排他的リソースまたは共有リソースのどちらに展開するかも決定します。


## <a name="pillars-of-software-quality-review"></a>ソフトウェア品質レビューの重要な要素 

複雑なシステムには、機能要件を満たす以外にも、追加の精査が必要です。 成功するクラウド ソリューションでは、スケーラビリティ、可用性、回復性、管理、およびセキュリティの 5 つの重要な要素に重点が置かれています。 この 5 つの重要な要素に加えて、ソリューションの費用対効果を産出することも必要です。

詳細については、記事「[ソフトウェア品質の重要な要素](https://docs.microsoft.com/azure/architecture/guide/pillars?WT.mc_id=pdmsolution-docs-ercenk)」を参照してください。

| 重要な要素                      |                                                                                                                                                                                                                                                                                                                                                                                                 |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| スケーラビリティ                 | ほとんどの Azure サービスは、垂直スケーリングと水平スケーリングの両方のオプションをサポートします。 Azure プラットフォームへのリソースのオンデマンドのデプロイを利用し、自動化されたサービスを介してスケール (サイズとインスタンス数) を制御します。                                                                                                                                                                   |
| 可用性と回復性 | コンピューティング リソースとストレージ リソースは、数多くの Azure サービスを使用して、必要に応じて柔軟にプロビジョニングすることができます。 すべての Azure サービスには、さまざまなレベルの SLA が用意されていますが、ソリューションでは、適切な設計原則を使用して、SLA レベルを検討し、適宜利用する必要があります。                                                                                                                    |
| 管理                  | Azure リソースは、ARM テンプレート、コマンド ライン ツール、PowerShell コマンドレット、Azure Management API など、さまざまな方法で展開し、管理することができます。 ツールと UI を使用する代わりに、Azure リソースを管理する際の自動化されたソリューションを構築することを検討する必要があります。                                                                                                                                |
| Security                    | Azure IoT Hub は、TLS 上で対称キーと非対称キー (X509 証明書および TPM) をサポートします。 データ ストアは、Identity and Access Management (IAM) 設定を使用して保護され、データ ストアも保存中のデータの暗号化をサポートします。 一般的に、大まかなセキュリティ チェックリストとして、承認、認証、送信中および保存中の暗号化、監査メカニズムを検討する必要があります。 |
| コスト効率              | リソースを必要に応じてプロビジョニングすること、および使用しなくなったときにリソースを自動的に廃棄することを検討する必要があります。                                                                                                                                                                                                                                                                                                  |

## <a name="conclusion"></a>まとめ

予測メンテナンスは、長い間、議論の的になってきました。 Microsoft Azure などのクラウド プラットフォームにおける最近の開発により、予測メンテナンスの実装者は、これまでデータを取り扱う際に障害となっていた数多くの課題を克服できるようになりました。 コンピューティング容量と記憶域容量を柔軟にスケーリングできるため、クラウド プラットフォームは、新しい収益機会だけでなく、予測メンテナンスを新たに実装する機会も提供します。 Microsoft の Azure プラットフォームには、予測メンテナンス ソリューションのビジネス目標を達成するためのさまざまな機能を提供する数多くのサービスが用意されています。

この記事では、データの収集方法とデータ モデルのトレーニング方法に関するビジョンについて説明するとともに、前述のセクションで予測した結果に向けて行動を起こすためのトレーニング済みモデルの利用方法についても説明しました。

## <a name="further-reading"></a>参考資料

1. [Future-focused: Stop thinking in the past and get ahead of the unexpected  with  IoT](https://blogs.microsoft.com/iot/2017/02/28/future-focused-stop-thinking-in-the-past-and-get-ahead-of-the-unexpected-with-iot-2/?WT.mc_id=pdmsolution-docs-ercenk) (将来を見据えて: 古い考えを捨て、不測の事態を IoT で把握する)

2. [Boost equipment reliability with IoT-enabled predictive  maintenance](https://www.microsoft.com/internet-of-things/predictive-maintenance?WT.mc_id=pdmsolution-docs-ercenk) (IoT 対応の予測メンテナンスで機器の信頼性を向上)

3. [Capture value from the Internet of Things: How to approach a predictive  maintenance  project](https://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF?WT.mc_id=pdmsolution-docs-ercenk) (モノのインターネットから価値を得る: 予測メンテナンス プロジェクトへのアプローチ)

4. [Partner perspectives: Predictive maintenance on the  frontlines](https://blogs.microsoft.com/iot/2017/03/21/partner-perspectives-predictive-maintenance-on-the-frontlines/?WT.mc_id=pdmsolution-docs-ercenk) (パートナーの視点: フロントラインでの予測メンテナンス)

5. [From commoditization to servitization: Transforming your business to  compete in the new age of field service with  IoT](https://blogs.microsoft.com/iot/2016/11/07/from-commodization-to-servitization-transforming-your-business-to-compete-in-the-new-age-of-field-service-with-iot/?WT.mc_id=pdmsolution-docs-ercenk) (コモディティ化からサービス化へ: IoT によるビジネス変革でフィールド サービスの新時代を勝ち抜く)
