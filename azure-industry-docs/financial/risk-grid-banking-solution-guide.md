---
title: 概要 - Azure Batch、Azure Data Lake でのグリッド コンピューティングによるリスク分析
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 銀行取引におけるリスク グリッド コンピューティング向け Azure Batch 実装の技術的な側面を紹介します。
ms.openlocfilehash: 542fb820870048ac2ec2cb67c2bbf13988588ea1
ms.sourcegitcommit: f030566b177715794d2ad857b150317e72d04d64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74234663"
---
# <a name="risk-grid-computing-in-banking-solution-guide"></a>銀行取引におけるリスク グリッド コンピューティングのソリューション ガイド

この記事では、銀行取引における Microsoft Azure を使用したリスク グリッド コンピューティングのサポートと拡張の技術的な概要について、推奨システムとおおまかなアーキテクチャを含めて説明します。

このドキュメントは、リスク コンピューティング用に提案されたソリューションについて深く理解する必要があるソリューション アーキテクト (場合によっては技術的意思決定者) を対象としています。

## <a name="introduction"></a>はじめに

金融リスク分析モデルは一般にバッチ ジョブとして処理され、処理能力、データ アクセス、分析の要件を押し上げる大きな計算負荷を伴います。 多くの場合、リスク グリッド コンピューティング計算の要件は時間の経過と共に増加し、それに従ってコンピューティング リソースの必要量も増えます。

Azure で利用可能な幅広い製品とサービスは、ほとんどの問題に対して複数のソリューションが存在する可能性があることを意味します。 この記事では、銀行取引における、[Microsoft Azure Batch](/azure/batch/batch-dotnet-get-started?WT.mc_id=gridbanksg-docs-dastarr) を使用したリスク グリッド コンピューティング ソリューションにとって最も効果的と考えられる技術、パターン、実践の概要を説明します。

Azure Batch は、リスク グリッド コンピューティング モデルで一般的に使用されるインフラストラクチャと、バッチ処理のさまざまなステージの両方に対して、コスト効果の高い安全なソリューションを提供する無料のサービスです。 Azure Batch では、ハイブリッド ネットワークを使用して、またはバッチ プロセス全体を Azure に移すことによって、現在のオンプレミス コンピューティング リソースの強化や拡張を行うことが (または置き換えも) できます。  データはクラウドとの間で送受信されることもあれば、クラウドへのバースト モデルでオンプレミス リソースが不足しているときに、他のデータが計算ノードによって処理される間はオンプレミス上に留まることもあります。

## <a name="anatomy-of-a-batch-run"></a>バッチ実行の分析

通常、バッチ実行には少なくとも 2 つのアプリケーションが使用されます。 1 つのアプリケーション (通常は "ヘッド ノード" 上で実行される) からジョブをプールに送信し、場合によっては計算ノードの調整を行います。 オーケストレーションは、Azure portal を使用して構成することもできます。 もう 1 つのアプリケーションは計算ノードによってタスクとして実行されます (図 1 を参照)。

計算ノード アプリケーションは、並列処理リスク モデリング ファイルのタスクを実行します。 計算ノード上に複数のアプリケーションをインストールして実行できます。

これらのアプリケーションは、[Batch API](/azure/batch/batch-apis-tools?WT.mc_id=gridbanksg-docs-dastarr) を使用して、Azure Portal から直接アップロードするか、[Batch 用 Azure CLI コマンド](/azure/batch/cli-samples?WT.mc_id=gridbanksg-docs-dastarr)によってアップロードできます。

![Azure Batch グリッド コンピューティング](./assets/risk-grid-compute-assets/05-batch-grid-computing.png)

**図 1:** Azure Batch グリッド コンピューティング

バッチ実行は複数の論理要素で構成されます。 図 2 は、バッチ ジョブの論理モデルを示しています。 プールは、バッチ実行に使用される VM のコンテナーであり、計算ノード VM をプロビジョニングします。 プールは、計算ノード上にインストールされたアプリケーションのコンテナーでもあります。 ジョブはプール内で作成され、実行されます。 タスクは、ジョブによって実行されます。 タスクは worker アプリケーションの実行であり、コマンド ライン命令によって呼び出されます。

worker アプリケーションは、その作成時に計算ノードにインストールされます。

![プール、ジョブ、タスク](./assets/risk-grid-compute-assets/06-pool-job-logical-model.png)

**図 2:** Batch の概念の論理モデル

ジョブ実行時、プールによって、必要な worker VM がプロビジョニングされ、worker アプリケーションがインストールされます。 ジョブによってそれらの計算ノードにタスクが割り当てられると、コマンド ライン命令が実行され、通常はそれによって、インストールされているアプリケーションまたはスクリプトが呼び出されます。
Batch の使用は通常、次に示す典型的なパターンに従います。

1. Batch 資産を含むリソース グループを作成します。
2. リソース グループ内に Batch アカウントを作成します。
3. リンクされたストレージ アカウントを作成します。
4. worker VM をプロビジョニングするためのプールを作成します。
5. 計算ノードのアプリケーションまたはスクリプトをプールにアップロードします。
6. プール内の VM にタスクを割り当てるためのジョブを作成します。
7. ジョブをプールに追加します。
8. バッチの実行を開始します。
9. タスクはジョブによってキューに入れられ、計算ノード上で実行されます。
10. 計算ノードでは、VM が使用可能になったときにタスクが実行されます。

このプロセスを図 3 に示します。

![バッチ実行プロセス](./assets/risk-grid-compute-assets/07-batch-run-process.png)

**図 3:** Batch の概念の論理モデル

タスクが完了したら、計算ノードを削除することで、使用していないときに料金が発生するのを避けることができます。 それらを削除するには、コードまたはポータルを使用して、それらを含むプールを削除すると、worker VM が削除されます。

Batch の使用を開始するための詳細なチュートリアルについては、「[5 分間のクイック スタート](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)」にある、複数の言語または Azure portal を使用したプロセスについての説明を参照してください。

## <a name="batch-process-scheduling"></a>Batch でのプロセスのスケジューリング

Azure Batch にはスケジューラが組み込まれているため、ポータルまたは API を使用して各実行のスケジュールを定義できます。 Batch ジョブ スケジューラでは、複数のジョブを起動するための複数のスケジュールを定義できます。 各ジョブには、ジョブ開始時および終了時に何を行うかといった固有のプロパティがあります。 ジョブのスケジュールは、定期的な間隔で設定することも、1 回の実行用に設定することもできます。

多くの銀行グリッド コンピューティング システムでは、既に独自のスケジューリング サービスを使用しています。 自分のスケジューラを Azure に移す差し迫った必要性はないかもしれません。 これはシームレスに機能することが可能です。Azure Batch は手動で呼び出すことも、SDK を使用して呼び出すこともできるため、スケジューリングを引き続きオンプレミスで実行しながら、ワークロードを Azure 上で処理することができるからです。

バッチ処理は、あらかじめ定義されたスケジュールに従って実行することも、オンデマンドで実行することもできますが、どちらの場合も、計算ノード VM を使っていないときは、それらを稼働状態に維持する必要はありません。  数千ではないとしても数百の計算ノードを使用している場合は、キューに入っていたタスクの実行が完了した時点でサーバーのプロビジョニングを解除することによって、大幅なコスト削減を実現できます。

## <a name="compute-node-applications"></a>計算ノード アプリケーション

計算ノードには、タスクが呼び出されたときに実行されるアプリケーションが必要です。 それらのアプリケーションは、worker 上にインストールされたときに処理を行うジョブを実行するために、企業が作成します。 銀行取引のシナリオ向けのリスク グリッド コンピューティングにおいて、このアプリケーションでは多くの場合、ダウンストリーム分析などの処理に適した特別な形式にデータを変換するジョブが発生します。

アプリケーションを計算ノードに配布するためにプールに追加すると、アプリケーションはアプリケーション パッケージとしてアップロードされます。 アプリケーション パッケージは、以前にアップロードされたアプリケーション パッケージの別のバージョンとすることができます。 1 つの計算ノードに複数のアプリケーション パッケージをインストールできます。 ジョブには、worker マシンに読み込むアプリケーション パッケージが含まれています。

アプリケーション パッケージのデプロイも、バージョンによって管理できます。 1 つのアプリケーション パッケージの複数のバージョンがプールに読み込まれている場合は、図 4 に示すように、特定の 1 つのバージョンをバッチ実行で使用するように指定できます。 これは、監査の環境において、または企業が以前の実行の再現を希望する場合に、必要になることがあります。 また、worker アプリケーションにバグが混入した場合に、ロールバックを目的として使用されることもあります。

![バッチ実行プロセス](./assets/risk-grid-compute-assets/08-versioning-worker-applications.png)

**図 4:** 計算ノードのタスク アプリケーションのバージョン指定

アプリケーション パッケージは、タスクでアプリケーションを実行するために必要なアプリケーション バイナリとサポート ファイルを含む .zip ファイルとしてプールにアップロードされます。 アプリケーション パッケージには 2 つのスコープがあります。 アプリケーション パッケージは、プールのスコープ内に指定するか、タスクのスコープ内に指定することができます。

## <a name="pool-application-packages"></a>プールのアプリケーション パッケージ

これらのパッケージは、プール内のすべての計算ノードにデプロイされます。 計算ノード VM がプロビジョニング、再起動または再イメージ化されたとき、更新されたアプリケーションが存在する場合は、プールのアプリケーション パッケージの新しいコピーがインストールされます。 1 つのプールに 1 つ以上のアプリケーション パッケージが割り当てられる場合があります。これは、計算ノードでは指定されたすべてのパッケージが取得されることを意味します。

## <a name="task-application-packages"></a>タスクのアプリケーション パッケージ

タスク レベルをターゲットとするアプリケーション パッケージは、タスクを実行するようにスケジュールされた計算ノードだけにインストールされます。 タスクのアプリケーション パッケージは、1 つのプール内で複数のジョブが実行される用途に適しています。

タスク アプリケーションは、プールレベルのジョブによって生成されたデータを集約する場合に便利です。これはリスク グリッド コンピューティングのシナリオに適しています。 たとえば、タスク アプリケーションでは、リスク計算ワークフローで後ほど使用されるデータを生成する一連のリスク計算を実行できます。

## <a name="scaling-batch-jobs"></a>バッチ ジョブのスケール

銀行は多くの場合、コンピューティング リソースの使用率が低い週末や夜間に、リスク分析のバッチ実行を実施します。 このモデルは有効な場合がある一方、すぐに規模が拡大し、より多くの worker マシンをグリッドに追加するためにより多くの資金が必要になります。

バッチ ジョブの実行に時間がかかりすぎる場合や、バッチ実行により多くのコンピューティング能力が必要な場合、Azure には複数のオプションが用意されています。

1. より多くの計算ノード マシンを割り当ててスケール アウトします。
2. より能力の高い計算ノード マシンを割り当ててスケール アップします。 コア数やメモリについて、また GPU 処理能力についても、より高いパフォーマンスのニーズを満たす Azure マシンをプロビジョニングします。

> 注:Microsoft HPC Pack の Batch との併用は、より複雑なモデルであり、この記事では説明しません。

Batch 処理クラスター内にある処理 VM は、2 つだけと少ない場合もあれば、数千の同期タスクが、数万のコアを持つ数千の VM 計算ノード上で実行される場合もあります。 各 VM は、一度に 1 つのタスクを実行します。 プール内の VM の数は、負荷が増加または減少したときに、構成に従って手動または自動でスケールできます。

### <a name="burst-to-cloud"></a>クラウドへのバースト

大規模な分析ジョブの実行によってオンプレミス グリッド内のコンピューティング リソースが不足したとき、"クラウドへのバースト" は、Azure 内のより多くの計算ノードを追加することによって、それらのリソースを増強する方法を提供します。 クラウドへのバーストは、オンプレミス リソースに対する需要が高くなったときに、プライベート クラウドまたはインフラストラクチャのワークロードをクラウド サーバーに分散するモデルです。

これらの計算ノードは、Azure の IaaS プラットフォーム上にプロビジョニングされる Linux または Windows 仮想マシンとして、あらかじめ構成することができます。 さらに、サーバーをプロビジョニングし、Tibco Gridserver や IBM Symphony などの既存の投資と連携するように自動的に構成することができます。

### <a name="automatic-scaling-formulas"></a>自動スケールの数式

このエラスティックな特徴を Azure portal 上で、または[自動スケールの数式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)を使用して構成できます。 自動スケールの数式は、Batch 処理スケジューラにアップロードされた、Batch の動作をきめ細かく制御するためのスクリプトです。 計算ノードのプールに対する自動スケールは、ノードを自動スケールの数式に関連付けることによって行われます。

次に、1 台の VM を使用して自動スケールを開始し、必要に応じて 50 VM までスケールアップすることを指示する、自動スケールの数式の例を示します。 タスクが完了すると、VM が 1 台ずつ解放され、自動スケールの数式によってプールが縮小します。

```Formula
startingNumberOfVMs = 1;
maxNumberofVMs = 50;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);
```

### <a name="other-scaling-techniques"></a>その他のスケール手法

自動スケールは、PowerShell コマンドレット Enable-AzureBatchAutoScale によって有効にすることもできます。 Enable-AzureBatchAutoScale コマンドレットを使用すると、指定したプールの自動スケールが可能になります。 例を次に示します。

1. 最初のコマンドでは、式を定義し、`$Formula` 変数に格納します。
2. 2 番目のコマンドでは、`RiskGridPool` という名前のプールに対するスケールを、`$Formula` 内の式を使用して有効にします。

```console
C:\> $Formula = ‘startingNumberOfVMs = 1;
maxNumberofVMs = 50;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second?WT.mc_id=gridbanksg-docs-dastarr);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicatedNodes=min(maxNumberofVMs, pendingTaskSamples);’;

C:\> Enable-AzureBatchAutoScale -Id "RiskGridPool" -AutoScaleFormula $Formula -BatchContext $Context
```

スケールは、Azure CLI を使用して、`az batch pool resize` コマンドと Azure Portal によって実行することもできます。

## <a name="data-storage-and-retention"></a>データの保存と保持

データが計算ノードに取り込まれて処理された後、その結果の出力データをデータベース内に格納し、さらに処理や分析を行うことができます。または、格納する前の取り込みの時点で確実に、ダウンストリーム処理用の適切な形式に変換できます。 Microsoft Azure にはストレージのオプションが複数用意されています。 [使用するデータ ストア テクノロジ](/azure/architecture/data-guide/?WT.mc_id=gridbanksg-docs-dastarr)の選択は、分析および/または、ダウンストリーム プロセスでのレポートのニーズに大きく依存します。

ハイブリッド ネットワークの使用時、データ ストレージのターゲットがオンプレミスである可能性があります。 ハイブリッド ネットワーク経由で Batch を使用する場合、Azure ベースのストレージの場所を使用して、計算ノードからオンプレミスのデータ ストアにデータを書き戻すことができます。 worker から Azure ファイル ストレージに書き込むこともできます。このストレージはディスクとしてマウントできるため、オンプレミスのファイルを使用するプロセスから簡単にアクセスできます。

## <a name="monitoring-and-logging"></a>監視およびログ記録

バッチ ジョブの今後の実行を最適化するには、最適化した領域を識別しやすいようにデータを記録しておく必要があります。 たとえば、worker が CPU のキャパシティに近い状態で実行されている場合は、計算ノードにコアを追加することが CPU による制約の回避に役立ち、ジョブをより高速に完了することができます。 バッチ ジョブ内で実行されるアプリケーションにはそれぞれ固有の特性があり、バッチ実行において VM に対して行う最適化は同一とは限りません。 メモリ集中型タスクの場合、次回の実行時にマシンを異なる方法で構成すると、より多くのメモリを割り当てることができます。

ログ記録は、計算ノード アプリケーションとグリッド ヘッド アプリケーションによって、または [Batch 診断ログ](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)を使用するジョブによって行うことができます。 バッチ実行のパフォーマンスに関する情報のログ記録を構成すると、パフォーマンスの向上と、より高速なタスク完了のために改善すべき領域の特定に役立ちます。

### <a name="custom-batch-monitoring-and-logging"></a>カスタムの Batch 監視とログ記録

制御アプリケーションと計算ノード アプリケーションでは、このデータを生成して保存し、さらに詳しく分析できます。 バッチ ジョブを最適化するうえで役に立つデータとして、次のようなデータがあります。

- 各タスクの開始時刻と終了時刻
- 各計算ノードが動作しており、タスクを実行していた時間
- 各計算ノードが動作しており、タスクを実行していなかった時間
- 全体的なバッチ ジョブの実行時間

### <a name="batch-diagnostic-logging"></a>Batch 診断のログ記録

インストルメンテーション データの生成には、コントローラーと計算ノード アプリケーションを使用する以外の別の方法があります。 [Batch 診断ログ](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)では、さまざまな実行データをキャプチャできます。 Batch 診断ログは既定では有効になっておらず、Batch アカウントに対して有効にする必要があります。

Batch 診断ログによって、バッチ実行のトラブルシューティングと最適化に役立つ大量のデータが提供されます。 ジョブとタスクの開始時刻と終了時刻、コア数、合計ノード数、その他多くのメトリック。

Batch ログ記録には、プールの作成、ジョブの実行、タスクの実行など、バッチ実行によって生成されたイベントを格納するために、生成されたログの送信先となるストレージが必要です。診断ログ イベントを Azure ストレージ アカウントに格納することに加えて、Batch サービス ログ イベントを [Azure イベント ハブ](/azure/event-hubs/event-hubs-what-is-event-hubs?WT.mc_id=gridbanksg-docs-dastarr)にストリーミングし、[Azure Log Analytics](/azure/log-analytics/log-analytics-overview?WT.mc_id=gridbanksg-docs-dastarr) に送信することができます。

このようなデータを使用して、中核となる計算ノード アプリケーションとヘッド ノード アプリケーションを最適化できます。 そうすることで、必要なくなった worker VM のプロビジョニング解除を迅速化すること (バッチ実行が最後まで完了するのを待つのではなく) などによって、コストを削減できます。

### <a name="batch-management-tools"></a>Batch の管理ツール

Azure portal には、Batch 監視ダッシュボードが用意されていて、ジョブ実行中の Batch に関する情報が表示されるほか、アカウントのクォータ使用状況に関する情報も表示されます。 多くのバッチ ジョブ アプリケーションでは、それで十分です。

Azure portal 上で使用できる Batch の管理ツールと視覚化ツールに加えて、Batch を管理するための、[BatchLabs](https://github.com/Azure/BatchLabs) という無料オープン ソース ツールがあります。 これは、Azure Batch アプリケーションの作成、デバッグ、監視に役立つスタンドアロンのクライアント ツールです。 Mac、Linux、または Windows 用のインストール パッケージをダウンロードしてください。

## <a name="network-models"></a>ネットワーク モデル

リスク分析では多くの場合、数千とはいかないまでも数百単位のドキュメントをリスク グリッド コンピューティング プロセスに取り込む必要があります。 多くの場合、これらのファイルはオンプレミスのファイル ストア、ネットワーク共有、またはその他のリポジトリに配置されます。 Azure ベースの VM を使用してこれらのファイルに対するアクセスと処理を行うときは、多くの場合、オンプレミス ネットワークを Azure ネットワークにシームレスに接続するとファイル アクセスがシンプルで高速になり、便利です。 このアプローチはさらに、計算ノード上で処理を行うためコードの変更が不要であるということも意味します。

Azure には、セキュリティで保護された信頼性の高い方法で現在のオンプレミス システムを Azure に接続するために、[Microsoft Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbanksg-docs-dastarr) と [VPN Gateway](/azure/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr) という 2 つのモデルが用意されています。 実装、パフォーマンス、[その他の属性](/azure/networking/networking-overview?WT.mc_id=gridbanksg-docs-dastarr)には違いがあるものの、両方とも、セキュリティで保護された信頼性の高い接続を実現します。

または、リスク グリッド コンピューティングのヘッド ノードをオンプレミスで動作させ、REST API または .NET やその他の言語の SDK を使用して、バッチ ジョブを実行することができます。

その他にも、ハイブリッド ネットワーク ソリューションを使わずに Azure とオンプレミス リソースとの間の違いを埋める方法があります。 これについて以下に詳しく説明します。

### <a name="expressroute"></a>ExpressRoute

ExpressRoute では、現在のインターネット サービス プロバイダーなどの接続パートナー (ISP?WT.mc_id=gridbanksg-docs-dastarr) によって提供されるプライベート接続を経由して、オンプレミスまたはデータセンターのネットワークを Azure に接続します。 これにより、両方のネットワークが相互に同じネットワーク インスタンスとして表示できるようになり、ネットワーク間でのシームレスなアクセスが可能になります。 既存のオンプレミス システムを Azure ネットワークに統合する場合、ネットワークの統合は不可欠であり、ExpressRoute を使用すると可能な最速の接続速度が得られます。

Azure ExpressRoute の料金の詳細については、[こちらを参照してください](https://azure.microsoft.com/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr)。

### <a name="vpn-gateway"></a>VPN Gateway

VPN Gateway は、ネットワークを Azure に接続するもう 1 つの方法です。 このモデルの欠点は、トラフィックがインターネットを経由して転送されることです。 結果として接続の回復性が下がり、ネットワーク速度が ExpressRoute の速度に到達できない可能性がありますが、これはリスク グリッド コンピューティングのシナリオにとって障害にはならないと考えられます (通常、データ ファイルの読み込みは高速な処理であるため)。

VPN Gateway の料金の詳細については、[こちらを参照してください](https://azure.microsoft.com/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr)。

### <a name="choices-for-connectivity-details"></a>接続の選択に関する詳細

図 5 に示すように、ネットワークを Azure に拡張するには基本的に 2 つのモデルがあります。

1. 仮想ゲートウェイ – サイト対サイト
2. ExpressRoute – Exchange または ISP プロバイダー

![サイト対サイトと ExpressRoute](./assets/risk-grid-compute-assets/10-s2s-expressroute.png)

**図 5:** サイト対サイトと ExpressRoute

#### <a name="virtual-gateway-site-to-site-integration"></a>仮想ゲートウェイによるサイト対サイトの統合

[サイト対サイトの VPN Gateway](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal?WT.mc_id=gridbanksg-docs-dastarr) によってオンプレミス ネットワークを Azure VNet に接続します。 実質的に (リソース、サーバー、成果物への双方向アクセスが可能な) 同じネットワークの一部とすることで、ネットワーク間の差を埋めます。 これにより、リスク グリッド コンピューティングのバッチ ジョブを実行している Azure worker VM からデータ ファイルへの直接アクセスが可能になります。

#### <a name="expressroute-integration"></a>ExpressRoute による統合

Azure パートナー ネットワーク プロバイダーによって提供される ExpressRoute 接続を使用して、サイト対サイト接続と同じ利点を実現でき、しかも速度と信頼性が向上します。

ExpressRoute 接続モデルについては、[こちらを参照してください] (/azure/expressroute/expressroute-connectivity-models?WT.mc_id=gridbanksg-docs-dastarr)。

### <a name="batch-processing-without-an-azure-hybrid-network"></a>Azure ハイブリッド ネットワークを使用しない Batch 処理

もう 1 つの Batch シナリオでは、Azure ベースのコンピューティング マシンによって後から処理できるように、すべてのデータ ファイルを Azure ストレージにアップロードします。 ファイル ストレージと Blob ストレージが、リスク グリッド コンピューティングのデータ格納場所の候補になると考えられます。

図 6 に示すように、このシナリオでは、ジョブ コントローラーとすべての計算ノードが Azure 上で動作します。 Azure Machine Learning ソリューションやその他のシステムによるさらなる処理に備えて処理されたデータの送信先としては、Azure データ ストアが考えられます。 この追加処理は、この記事の取り扱い範囲外です。

![サイト対サイトと ExpressRoute](./assets/risk-grid-compute-assets/09-batch-upload-process.png)

**図 6:** Batch のアップロードから実行までのライフサイクル

### <a name="hybrid-network-connectivity-resources"></a>ハイブリッド ネットワーク接続のリソース

ご自分の状況に複数の構成が適用できる場合があります。 ネットワーク接続と Azure への統合に関する決定とアーキテクチャに関するガイダンスとして、パターンと実践グループによる「 _[オンプレミス ネットワークの Azure への接続](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbanksg-docs-dastarr)_ 」の記事を参照してください。

- 代替の VPN Gateway 構成方法については、[こちらの記事を参照してください](/azure/vpn-gateway/vpn-gateway-about-vpngateways?WT.mc_id=gridbanksg-docs-dastarr)。
- [ExpressRoute 接続モデル](/azure/expressroute/expressroute-connectivity-models?WT.mc_id=gridbanksg-docs-dastarr)について説明します。
- [ExpressRoute の料金](https://azure.microsoft.com/pricing/details/expressroute/?WT.mc_id=gridbanksg-docs-dastarr)を計算します。
- [VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr)

## <a name="security-considerations"></a>セキュリティに関する考慮事項

Azure [仮想ネットワーク (VNet)](/azure/virtual-network/virtual-networks-overview?WT.mc_id=gridbanksg-docs-dastarr) を作成し、その中にプールの計算ノードを作成できます。 これにより、バッチ実行に対して追加レベルの分離性が提供され、[Azure Active Directory (AAD)](/azure/active-directory/active-directory-whatis?WT.mc_id=gridbanksg-docs-dastarr) を使用した認証が可能になります。 詳細については、[プール ネットワーク構成](/azure/batch/batch-api-basics#pool-network-configuration?WT.mc_id=gridbanksg-docs-dastarr)に関する記事を参照してください。

AAD を使用してバッチ アプリケーションを認証するには、2 つの方法があります。

1. **統合認証**。 AAD アカウントを使用しているバッチ アプリケーションでは、そのアカウントを使用して、データ ストアやその他のリソースへのリソースを取得できます。

2. **サービス プリンシパル**。 AAD サービス プリンシパルは、ユーザーとアプリケーション用のアクセス ポリシーとアクセス許可を定義したものです。 サービス プリンシパルによって、そのアプリケーションに結び付けられている秘密鍵を使用した認証がユーザーに提供されます。 これにより、秘密鍵を使用して無人アプリケーションを認証できるようになります。 サービス プリンシパルには、実行時のリソースへのアクセスの際にアプリケーションを表すための、アプリケーション用のポリシーとアクセス許可が定義されます。 [こちら](/azure/active-directory/develop/active-directory-application-objects?WT.mc_id=gridbanksg-docs-dastarr)を参照してください。

AAD を使用したバッチ処理におけるセキュリティの詳細については、[こちらの記事を参照してください](/azure/batch/batch-aad-auth?WT.mc_id=gridbanksg-docs-dastarr)。

Batch サービスは、共有キーによって認証することもできます。 認証サービスでは、データと認証という 2 つのヘッダーの値を HTTP 要求に追加する必要があります。 共有キーによる認証の詳細については、[こちらを参照してください](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service?WT.mc_id=gridbanksg-docs-dastarr)。

## <a name="cost-considerations"></a>コストに関する考慮事項

Azure Batch は無料でご利用いただけます。 仮想マシン稼働時間、ストレージ、ネットワークなど、基になるリソースの使用した分だけをお支払いいただきます。 ただし、計算ノードの VM は稼働していない間も料金が発生するため、必要なくなったマシンはプロビジョニング解除することをお勧めします。 これを行うには、多くの場合、それらを含むプールを削除します。
>
プールを作成するときに、必要な計算ノードの種類と種類ごとの数を指定できます。 計算ノードには、次の 2 種類があります。
>
>**専用の計算ノード**は、ワークロード用に予約されます。 これは優先順位の低いノードより高価ですが、決して割り込まれないことが保証されています。
>
>**優先順位の低い計算ノード**は、Azure の余剰キャパシティを利用してバッチ ワークロードを実行します。 優先順位の低いノードは専用のノードより 1 時間あたりのコストが低く、多くのコンピューティング能力が必要なワークロードを可能にします。 詳細については、「[優先順位の低い VM で Batch を使用する](/azure/batch/batch-low-pri-vms?WT.mc_id=gridbanksg-docs-dastarr)」を参照してください。
>
>専用ノードと優先順位の低いノードが、同じプール内に存在できます。
>
>優先順位の低いコンピューティング ノードと専用のコンピューティング ノードの両方の価格情報については、「[Batch Pricing (Batch の価格)](https://azure.microsoft.com/pricing/details/batch/?WT.mc_id=gridbanksg-docs-dastarr)」を参照してください。

Batch 診断ログ サービスの使用時、Azure ストレージに出力されるデータには料金が発生します。 これは他のデータと同様のストレージ データであり、保持される診断データの量が料金に影響します。

## <a name="getting-started"></a>使用の開始

リスク グリッド コンピューティング用の Batch コンピューティングのように複雑な領域では、使用を開始するための場所が多数ありますが、ここでは、Batch テクノロジをよりよく理解するための論理的な出発点をいくつか示します。

コードと Azure portal の例を使用して Batch での作業を開始する場合は、[こちらを参照することをお勧めします](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)。 Azure Batch のサンプル アプリケーションは、[GitHub](https://github.com/Azure/azure-batch-samples) でも無料で利用できます。

バッチ計算ジョブを作成して実行するための簡単なアプリケーションを構築するのに役立つクイック チュートリアルを次に示します。 アプリケーションを構築するためのオプションは次のとおりです。

- [Batch .NET API](/azure/batch/batch-dotnet-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [Batch SDK for Python](/azure/batch/batch-python-tutorial?WT.mc_id=gridbanksg-docs-dastarr)
- [Batch SDK for Node.js](/azure/batch/batch-nodejs-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [PowerShell を使用した Batch 管理](/azure/batch/batch-powershell-cmdlets-get-started?WT.mc_id=gridbanksg-docs-dastarr)
- [Azure CLI を使用した Batch 管理](/azure/batch/batch-cli-get-started?WT.mc_id=gridbanksg-docs-dastarr)

概念実証の取り組みを開始することを検討してください。 Azure へのデータ インジェストのために、どのようなアプローチを使用しますか。 ハイブリッド ネットワークを使用しますか。または、SDK や REST インターフェイスを使用してデータをアップロードしますか。 ハイブリッド ネットワークを考えている場合は、これを実現するためのパイロットの開始を検討してください。

Batch コンピューティング ジョブのサイズを見積もり、適切なスケーリング ソリューションを選択します。 [自動スケールの数式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)を使用すると、複雑なスケジューリング シナリオを実現できます。一方、それよりもシンプルなシナリオは Azure portal を使用して実現できます。

## <a name="technologies-presented"></a>使用するテクノロジ

[Azure Batch](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr) は、クラウド上で大規模な並列処理ジョブを実行する機能を提供します。

[Azure Active Directory](/azure/active-directory/active-directory-whatis?WT.mc_id=gridbanksg-docs-dastarr) は、マルチテナントに対応したクラウドベースのディレクトリおよび ID 管理サービスで、中核となるディレクトリ サービス、アプリケーションのアクセス管理、ID の保護が 1 つのソリューションに統合されています。

[自動スケールの数式](/azure/batch/batch-automatic-scaling?WT.mc_id=gridbanksg-docs-dastarr)は、Batch のスケール動作をきめ細かく制御するために、バッチ処理スケジューラにアップロードされたスクリプトです。

[Batch 診断ログ](/azure/batch/batch-diagnostics?WT.mc_id=gridbanksg-docs-dastarr)は、ご自分のバッチ実行と生成されたイベントから詳細なログを作成できる、Azure Batch の機能です。 ログは Azure Storage に格納されます。

[BatchLabs](https://github.com/Azure/BatchLabs) は、Batch の監視と管理のためのスタンドアロン アプリケーションであり、Windows、MacOS、Linux 上で使用できます。

[ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbanksg-docs-dastarr) は、オンプレミス ネットワークと Azure ネットワークを結合するための、高速で高い信頼性を備えたハイブリッド ネットワーク ソリューションです。

[VPN Gateway](/azure/vpn-gateway/?WT.mc_id=gridbanksg-docs-dastarr) は、オンプレミス ネットワークと Azure ネットワークを結合するためにインターネットを使用する、ハイブリッド ネットワーク ソリューションです。

## <a name="conclusion"></a>まとめ

このドキュメントでは、銀行取引向けリスク グリッド コンピューティング用に Azure Batch を使用する場合の、技術的なソリューションと考慮事項の概要を示しました。 この記事では、Azure Batch の定義から[ネットワーク接続のオプション](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbanksg-docs-dastarr)、さらに料金に関する考慮事項まで、さまざまなことを説明しました。

リスク グリッド コンピューティング用に Azure Batch の評価に進むことを検討する場合は、作業を開始するための適切なリソースとして、[このページ](/azure/batch/?WT.mc_id=gridbanksg-docs-dastarr)を参照することをお勧めします。 リスク グリッド コンピューティングの本質である並列ファイル処理に関する、サンプルのガイド付きチュートリアルが用意されています。 Azure Portal、Azure CLI、.NET、Python を使用したチュートリアルがあります。
