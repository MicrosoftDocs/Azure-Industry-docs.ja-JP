---
title: レコメンダー システムと R のアルゴリズムの Azure での再利用
author: kbaroni
ms.author: kbaroni
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: R 言語で記述されたレコメンデーション アプリを再利用および最適化する方法。 Azure VM 上の Machine Learning Server に依存します。
ms.openlocfilehash: c5c35de681abc52641952f8bc9e95095b9d99d97
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054220"
---
# <a name="optimize-and-reuse-an-existing-recommendation-system"></a>既存のレコメンデーション システムの最適化と再利用  

このドキュメントでは、R で記述された既存のレコメンデーション システムを正しく再利用および改善するプロセスについて説明します。このための鍵は、Microsoft Machine Learning Server に組み込まれた MicrosoftML ライブラリと RevoScaleR ライブラリの並列性でした。

## <a name="recommendation-systems-and-r"></a>レコメンデーション システムと R

小売業者にとって、消費者の好みと購買履歴を理解することは競争上の強みです。 小売業者は長年にわたり、そのようなデータを機械学習と組み合わせて使用することで、消費者に関連する製品を識別したり、パーソナライズされたショッピング体験を提供したりしてきました。 このアプローチは**製品のレコメンデーション**と呼ばれ、小売業者にとって大きな収入の流れを生み出します。 推奨事項システムは、次のような質問への回答に役立ちます。*このユーザーが次にどのような映画を見るか。この顧客が関心を持つ可能性のあるその他のサービスは何ですか。この顧客が休暇を希望する場所*
ある最近の顧客は次のことを知りたがっていました: *消費者 (サブスクライバー) は契約を更新するだろうか?* その顧客は、サブスクライバーが契約を更新する確率を予測する既存のレコメンデーション モデルを持っていました。 予測が生成されたら、追加の処理を適用して応答をはい、いいえ、可能性ありのいずれかに分類していました。 その後、モデルの応答はコール センターのビジネス プロセスに統合されました。 そのプロセスによって、サービス エージェントはパーソナライズされたレコメンデーションを消費者に提供することができました。  
この顧客の初期の分析製品の多くは、レコメンデーション システムの中核の機械学習モデルを含め、[プログラミング言語 R](https://docs.microsoft.com/machine-learning-server/rebranding-microsoft-r-server) で構築されていました。 サブスクライバーの増加に伴い、データおよび計算の要件も高度化しました。 そのため今となっては、レコメンデーションのワークロードは非常に低速であり、処理が面倒です。 最近は、分析製品戦略に Python が組み込まれるようになってきています。 しかし短期的には、R への投資を維持し、より効率的な開発とデプロイのプロセスを見つける必要があります。 課題は、Azure の機能を使用して既存のアプローチを最適化することでした。 私たちは、レコメンデーションのワークロードのための概念実証テクノロジ スタックを提供し、検証するタスクに着手しました。 ここでは、類似のプロジェクトで利用できる一般的なアプローチをまとめています。  

## <a name="design-goals"></a>設計目標

重要な優先事項は、次のような強みを提供するために、モデルのワークフローを再設計および自動化することでした:

- モデル開発サイクルの迅速化とイノベーションまでの時間の短縮。 データ準備とモデル開発のサイクルが効率的であれば、実験のボリュームを増加させることができます。  
- 最新のデータに基づいて再トレーニングのサイクルをより頻繁に回すことで、より正確なモデル結果とより的確なレコメンデーションを得る。
- よりスケーラブルで、完全な運用環境で稼働する概念実証 (POC) の実装。
- 開発、テスト、デプロイ間のプロセスを自動化することで、運用開始までの期間を短縮。 自動化によって運用上のエラーや遅滞も削減します。  

## <a name="requirements"></a>必要条件

ワークフローの再設計には次の要件がありました:

- チームの既存の R スキルと専門知識を活用する。
- 意味のある場面ではコードを再利用する。
- サブスクライバー データをデータベースに格納し、モデルのトレーニングと再トレーニングのプロセスに容易かつ迅速に統合する。
- モデルの再トレーニングとスコアリングを Web アプリのインターフェイスから呼び出す。
- Web フロントエンドでの認証に Azure Active Directory を使用する。

## <a name="technology-stack"></a>テクノロジ スタック

このプロジェクトのパイプラインには複数のテクノロジとツールが必要であり、次の 4 つの領域が軸でした:

1. サブスクライバー データの持続性とアクセシビリティ。
2. モデルの開発、トレーニング、選択。  
3. モデル デプロイと運用化。
4. Web アプリケーションを使用してスコアリングとモデルの再トレーニングを呼び出すこと。

パイプライン図のテクノロジ構成要素については、以下で詳しく説明します。

![最適化のアーキテクチャ](assets/recommendation-engine-optimization/recommendation-architecture.png)

### <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R ワークロードを選択する主な理由は、 **[RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler)** と **Microsoft ML** です。 データをインポートしたり、分類モデルを作成したり、それらを運用環境にデプロイしたりするために、これらのパッケージに含まれる関数をコード全体で多用していました。
それぞれ "開発" と "運用" のために構成された、Azure の 2 つの Linux 仮想マシンに MLS がデプロイされました。 開発 VM には、数百のモデルのトレーニングとテストを促進するために、より多くのメモリと処理能力がプロビジョニングされました。 また、リモート ユーザーが RStudio IDE に容易にアクセスできるよう、RStudio Server をホストしていました。 運用サーバーはより小規模な VM 上に構成され、Web アプリケーションから REST API 経由で呼び出せる R モデルをホストするために必要な拡張機能を追加していました。

### <a name="rstudio-server"></a>RStudio Server

**[RStudio Server](https://www.rstudio.com/products/rstudio/#Server)** は、リモートまたはラップトップ クライアント用のブラウザー ベースのインターフェイスを提供する Linux アプリケーションです。 ポート 8787 で実行され、Azure VM でネットワーク セキュリティ規則が作成されるとリモート接続が可能になります。 RStudio IDE を好むアナリストやデータ サイエンティストに、計算とメモリの容量が大きい仮想マシンへのアクセスを提供するための効率的なオプションです。 ソース エディションと商用エディションの両方がダウンロード可能です。

### <a name="azure-sql-database"></a>Azure SQL データベース

サブスクライバー データは元々、500 人の一意なサブスクライバーの購買およびプリファレンス情報を、600 万行もの 1 つの巨大な .csv ファイルに格納するものでした。 データをデータベースに格納することで、R 内からのデータ アクセスが高速化され、フィルターを適用した読み取りが可能になりました。 トレーニングまたは再トレーニングのためにデータ セット全体をインポートする必要がなくなります: データはデータベース ソースでサブスクライバーによってフィルタリングされ、データのインポートと処理に必要なリソースが大幅に削減されます。  
Azure には、マネージド クラウド データベースのオプションが複数あります。 [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/) が選ばれた理由は、顧客が SQL Server に慣れ親しんでいたこと、そしてより重要な点として、将来的に SQL Server Machine Learning Services を Azure SQL Database にいっそう大規模に導入する計画があったことです。 [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning?view=sql-server-2017) は、ストアド プロシージャを使用して R および Python ワークロードを実行するためのデータベース内機能です。

### <a name="nodejs-and-reactjs"></a>Node.js と React.js

R スクリプトを呼び出したり、Web サイトをセキュリティで保護したりするための Web インターフェイスが作成されました。 Node.js が Web サーバー フレームワークとして選ばれた理由は、分散環境内の Web アプリケーションのための軽量で効率的なランタイム環境を実現することです。 React はユーザー インターフェイスの構築に使用され、フロントエンドに位置し、Web サーバーでホストされている Web サービスを呼び出します。 Node + React は、Web サービスのプロトタイプを作成するための最速のパスをモデル パイプラインに提供すると判断されました。  

## <a name="infrastructure-implementation"></a>インフラストラクチャの実装

以下のセクションでは、このプロジェクトのサーバー インフラストラクチャがどのようにデプロイされたかについて説明します。 開発とデプロイのインフラストラクチャを正しく整備することは、モデリングに応用するアプローチや技法の判断と並んで、最重要の事項です。

### <a name="initial-database-load"></a>初期データベース負荷

最初のステップは、サブスクライバー データを巨大な .csv ファイルから Azure SQL Database にインポートすることでした。 Azure SQL Database へのデータのインポートには複数のオプションがあり、この[リファレンス](https://blogs.msdn.microsoft.com/sqlcat/2010/07/30/loading-data-to-sql-azure-the-fast-way/)で説明しています。 私たちが行った方法は次のとおりです:

1. [こちら](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)の手順に従って、Azure portal からデータベースを作成しました。
2. VM からデータベースに接続するために、[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) をダウンロードして使用しました。
3. [SQL インポート/エクスポート ウィザード](https://docs.microsoft.com/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard?view=sql-server-2017)を選択しました (時間が限られている場合、より効率的なデータ インポート オプションがあります)。 インポート/エクスポート ウィザードはデータ型をデータ ソースからターゲットにマップすることに注意してください。このシナリオでは、すべてのデータ要素は許容される varchar(max) データ型にマップされました。 異なるマッピングが必要なシナリオの場合、ウィザードでデータ型を変更できます ([参照](https://docs.microsoft.com/sql/integration-services/import-export-data/data-type-mapping-in-the-sql-server-import-and-export-wizard?view=sql-server-2017))。  
4. データベースに送信されるほとんどのクエリはフィールド *subscriber_id* でフィルタリングされるため、そのフィールドにインデックスを作成しました。

### <a name="web-application"></a>Web アプリケーション

Web アプリケーションは 3 つの機能を担います:

- 認証: Web ユーザーは *React* フロントエンドを通じて *Azure Active Directory* の認証を受けます。
- モデルのスコアリング: 特定のサブスクライバーについてユーザーから入力データを受け取り、予測応答を返す REST API を使用してサブスクライバーのデータを Web サービスに送信します。  
- モデルの再トレーニング: サブスクライバー ID を入力として受け取り、開発サーバー上の R スクリプトを呼び出してそのサブスクライバーのモデルを再トレーニングします。

*Azure Active Directory* によるシングル サインオン (SSO) の実装は予想以上に困難であると判明しました。 これは、シングル ページ アプリケーション (SPA) のフレームワークによるものでした。 ある特定の Azure Active Directory ライブラリが成功の鍵でした: [react-adal](https://github.com/salvoravida/react-adal)。 以下のリファレンスで、認証の実装に役立つガイダンスが提供されていました:

- [Azure AD の認証シナリオ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios)
- [シングル ページ アプリケーション](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios#single-page-application-spa)

### <a name="development-vm-mls-930"></a>開発 VM (MLS 9.3.0)

開発 VM はモデルの開発、トレーニングと再トレーニング、および分類モデルのデプロイをホストしました。 Azure VM (DS 13 V2) に Linux/Ubuntu 16.10 がプロビジョニングされ、z次のものがベース VM にインストールされました:

- Machine Learning Server 9.3.0 の使用方法については、[こちら](https://docs.microsoft.com/machine-learning-server/install/machine-learning-server-install)を参照してください。 必ず、セットアップ検証の手順を実行してインストールを確認してください。 これは開発 VM であったため、*Web サービスのデプロイとリモート接続の有効化*に関するセクションは無視しました。
- [RStudio Server](https://www.rstudio.com/products/rstudio/download-server/) (オープン ソースのバージョン)。 R/r ベースを再インストールしないよう注意してください (以前に MLS 9.3.0 でインストール済み)。  
- VM に[ネットワーク セキュリティ グループを追加](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal)して、RStudio Server 用のポート 8787 でインバウンド接続を許可します。  
- 開発 VM と Azure SQL Database 間の通信を処理するための ODBC ドライバー。 次の ODBC ドライバーが VM にインストールされました:  
- Linux/ubuntu 16.10 と互換性のある [SQL Server 17 用 ODBC ドライバー](https://www.microsoft.com/download/details.aspx?id=56567)  
- [ODBC を使用したリレーショナル データのインポート](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-data-odbc)に関する記事でインストールが指示されているオープン ソースの ODBC ドライバー unixodbc。 注: この記事の Ubuntu 関連の手順には 2 か所の誤りがあります。  
- unixodbc がインストールされているかどうかを確認するには:       ````Apt list –installed | grep unixODBC (should be unixodbc)````
      - ドライバーをインストールするには: ````sudo apt-get install unixodbc-devel unixodbc-bin unixodbc (should be unixodbc-dev)````

### <a name="operations-vm-mls-930"></a>運用 VM (MLS 9.3.0)

運用 VM はモデルの Web サービスとエンドポイントをホストし、Swagger ファイルを格納し、分類モデルのシリアライズされたバージョンを格納しました。 構成は MLS 開発サーバーによく似ています。 ただし、運用化のために構成されています。つまり、REST エンドポイントのサービスを提供するために必要な Web サービスがインストールされています。 運用 VM をデプロイするために、デプロイを迅速化する ARM テンプレートがあります。 参照: [Configuring Microsoft Machine Learning Server 9.3 to Operationalize Analytics using ARM Templates](https://blogs.msdn.microsoft.com/mlserver/2018/02/27/configuring-microsoft-machine-learning-server-9-3-to-operationalize-analytics-using-arm-templates/)\(ARM テンプレートを使用した分析を運用化するための Microsoft Machine Learning Server 9.3 の構成\)。 このプロジェクトでは、この *ARM テンプレート*を使用して [One-Box](https://github.com/Microsoft/microsoft-r/tree/master/mlserver-arm-templates/one-box-configuration/linux) 構成をデプロイしました。  
これにより、モデル パイプラインをサポートするためのサーバー コンポーネントを準備して稼働させました。

## <a name="model-implementation"></a>モデルの実装

1 つの重要な決定がこのプロジェクトの最終的なモデル設計に影響を及ぼし、単一のモノリシック モデルではなく "多モデル" 設計に移行することになりました。 違い: 個々のサブスクライバーは、すべてのサブスクライバーにサービスを提供する 1 つの大きな分類モデルではなく、独自の分類モデルを持ちます。 モデルが小規模なほどメモリ占有領域が小さくなり、運用環境で水平方向のスケーリングが容易であったため、この顧客にとっては好ましいアプローチでした。

### <a name="data-import"></a>データのインポート

モデル開発に必要なすべてのデータは *Azure SQL Database* にあります。 モデルのトレーニングと再トレーニングのために、2 段階でデータのインポートが行われました:

1. 特定の *subscriber_id* と返される結果セットのデータを取得するために、クエリがデータベースに送信されました。 データベースへのクエリ アクセスのために、2 つのオプションが検討されました:

- [RxSQLServerData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxsqlserverdata) という RevoScaleR 関数
- R odbc パッケージ

データベース レベルでのデータ フィルタリングを可能にする R "odbc" ライブラリを使用することに決定しました。 特定のサブスクライバー モデルに必要な行のみにデータベース テーブルをフィルタリングすることで、R に読み込んで処理する行数を最小化しました。 それにより、各モデルをトレーニングまたは再トレーニングするために必要なメモリ、計算量、および全体的な時間が短縮されました。  

1. 結果セットは R データ フレームに変換され、分類アルゴリズムの要件に従って一部のデータ型は varchar から整数または数値に明示的に変換されました。 この機能のために、RevoScaleR 関数 [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) が使用されました。 *rxImport* 関数は RevoScaleR と MicrosoftML にバンドルされており、マルチスレッドの設計になっています。 ここでは、どのようにそれを使用したかの例を示します:

````r

# Populate the data frame and modify column types as needed
input_data <- rxImport(sqlServerDataDS, colClasses = c(  
Approved="integer",
      OnTimeArrivalRate="numeric",
      Amount="numeric",
      IsInformed="numeric",
      <continue with list of columns> )
# View the characteristics of the variables in the data source
rxGetInfo (input_data, getVarInfo = TRUE)
````

## <a name="unbalanced-data"></a>不均衡なデータ

レコメンデーション モデルの目標は、顧客が契約を更新する確率を予測し、その確率を "はい"、"いいえ"、または "可能性あり" に分類することであったため、分類アルゴリズムが使用されました。 分類アルゴリズムの精度とパフォーマンスに大きく影響する可能性がある 1 つの問題は、不均衡なデータ セットです。  
ある "クラス" に対して別の "クラス" よりも多くのサンプルが存在する場合、データ セットは不均衡です。 この場合、各サブスクライバーの利用可能な行数のバランスが不均衡でした。1 人のサブスクライバーにつき 100 万行を超える場合もあれば、330 人の顧客のデータが 100 行未満の場合もありました。 次のグラフは、サブスクライバーあたりの行数 (サンプル) の不均衡を示しています: ![imbalance-chart](assets/recommendation-engine-optimization/recommendaton_engine_chart.png)

不均衡なデータ セットを取り扱うための 1 つの技法は、データ セットを変更し、情報不足のクラスをオーバーサンプリングするか、情報過多のクラスをアンダーサンプリングするというものです。 もう 1 つの技法は、データに関するデータ所有者の正確な知識とその属性を使用して、追加のデータを合成的に生成するというものです。 顧客はサブスクライバーの最小サンプル サイズのしきい値を確立しました。 そのしきい値を下回るサブスクライバーについては、データを扱う必要があります。 このプロジェクトでは、両方のアプローチが検討されました。

## <a name="algorithm-selection"></a>アルゴリズムの選択

*rxDForest*、*rxFastTrees*、および *rxFastForest* という 3 種類の分類アルゴリズムの実装が評価されました。 3 つのアルゴリズムはすべてマルチスレッドと並列処理を利用します。 また Microsoft ML は、利用可能であれば複数の CPU または GPU を使用します。 モデルを評価するための基準には、次のものがあります:

- 新しいモデルは元のモデルよりも正確だったか?
- 運用環境で稼働しているモデルのメモリ占有領域はどれくらいだったか? 運用環境は、精度に妥協することなく、ほぼリアルタイムの予測応答を伴って多数のモデルの同時実行をサポートできたか?
- アルゴリズムは不均衡なデータ セットをどれくらいうまく扱えたか? 合成データを生成することによって不均衡を事前処理する必要があったか?

次の表は、結果をまとめたものです:

| アルゴリズム | [説明] | 結果 | メモ |
| :--------- | :------------ | :--------- | :--------------- |
| [rxFastTrees](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfasttrees) | FastRank のマルチスレッド バージョンを実装するブースト デシジョン ツリーの並列実装。 | 正確、最速のパフォーマンス。 | 不均衡なデータのための特別な機能はなし。 事前処理されたデータを入力として提供する必要があった |
| [rxFastForest](https://docs.microsoft.com/machine-learning-server/r-reference/microsoftml/rxfastforest) | ランダム フォレストの並列実装。rxFastTrees を使用してデシジョン ツリーのアンサンブル学習器を構築する。 | 事前処理されたデータによってオリジナルより精度が向上。 rxDForest よりメモリ使用量が少なく、高速 |不均衡なデータのための特別な機能はなし。 事前処理されたデータを入力として提供。 |
| [rxDForest](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdforest) | ランダム フォレストの並列実装。 RevoScaleR に含まれている。 不均衡なデータの処理 (欠落しているデータの除去、データの条件付け、サンプルの階層化の処理) をすべて関数呼び出し内で実行できる。 | 元のモデルよりも高速。 元のモデルと同等以上の精度。 再サンプリングと合成のためのさまざまな技法を使用して不均衡なデータ セットを処理する。 最大のメモリ占有領域。  | 条件付けされたデータが関数内部に含まれるため、メモリ占有領域が最大。   データの処理は良好でしたが、データ所有者が提供するカスタマイズされた変換ほど良好ではありませんでした。 |

最終的に、顧客は *rxFastForest* アルゴリズムを選択しました。また、不均衡なデータの取り扱いについては、[vtreat](https://cran.r-project.org/web/packages/vtreat/index.html) ライブラリを使用し、カスタマイズされたデータの事前処理ステップを追加することによって、情報不足のサブスクライバーのデータを合成的に生成することに決定しました。

## <a name="model-deployment--web-services"></a>モデル デプロイと Web サービス

デプロイ用のモデルを運用 VM に公開することは簡単であり、[mrsdeploy を使用して R モデルを Web サービスとしてデプロイする方法](https://docs.microsoft.com/machine-learning-server/operationalize/quickstart-publish-r-web-service)に関するこちらのクイック スタート ドキュメントで説明されています。
このシナリオでは、開発 VM でモデルを作成した後、次の手順を使用して運用 VM に公開しました:

1. mrsdeploy パッケージで認証に使用できる 2 つの関数のいずれかを使用して、開発者 VM から運用 VM へのリモート ログインを確立します。remoteLogin() ではローカル管理者名とパスワードを使用し、remoteLoginAAD() では Azure Active Directory を使用します。 どちらのオプションについても、mrsdeploy を使用して Machine Learning Server または R Server にログインしリモート セッションを開く方法に関するこちらのリファレンスで説明されています。  
2. モデルがトレーニングされたら、mrsdeploy パッケージの publishService または updateService 関数を使用して、運用 VM に公開します。 このプロジェクトでは複数のデプロイ方法を使用し、方法に応じて新しいモデルを公開または既存のモデルを更新しました。 両方のケースを扱うために、次のコードが実装されました:

````r
# If the service does not exist, publish it and if it does exist, update it.  
# No service by this name so publish one
 api <- publishService(serviceName, code =sc_predict, model = model,  
      inputs = list(prop_data="data.frame"),  
      outputs = list(answer = "numeric"), v = "v1.0.0" )
 print("=========== Model Created =============")
} else {
# A service by this name already exists, update it  
 api <- updateService(serviceName, model = model,
     inputs = list(prop_data="data.frame"), v = "v1.0.0" )
 print("=========== Model Updated =============")
}
 ````

デプロイされると、モデルはシリアライズされて運用サーバーに格納され、*標準*または*リアルタイム*のどちらかのモードで Web サービス経由で使用できます。 Web サービスが標準モードで呼び出されるたびに、R と必要なライブラリが呼び出しごとに読み込まれ、アンロードされます。 対照的に、*リアルタイム* モードでは、R とライブラリは一度しか読み込まれず、後続の Web サービス呼び出しで再利用されます。 Web サービス呼び出しのオーバーヘッドの大半は R とライブラリの読み込みであるため、リアルタイム モードではモデル スコアリングの待ち時間が大幅に短縮され、応答時間は 10 ミリ秒を切る可能性があります。 標準とリアルタイムの各オプションのドキュメントおよび参考例については、[こちら](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services)を参照してください。 リアルタイムは単一の予測に適していますが、スコアリングのために入力データ フレームを渡すこともできます。 詳しくは、[mrsdeploy を使用したバッチ処理による非同期 Web サービスの使用](https://docs.microsoft.com/machine-learning-server/operationalize/how-to-consume-web-service-asynchronously-batch)に関するこちらのリファレンスで説明しています。

## <a name="conclusion"></a>まとめ

Microsoft Machine Learning Server に組み込まれている MicrosoftML および RevoScaleR ライブラリの並列性を利用することで、数百人規模のサブスクライバー向けの個別分類モデルの開発、デプロイ、スコアリングが高速化されました。 モデルの精度が向上し、トレーニングと再トレーニングの時間が短縮されましたが、既存の R コード ベースへの変更は最小限で済みます。
モデル パイプラインをサポートするためのインフラストラクチャの実装と、テクノロジ コンポーネントを正しく隅々まで構成することは複雑な作業かもしれません。 独自のアプローチで作業を始めるためのリファレンスをいくつか紹介します:

- [Machine Learning Server のドキュメント](https://docs.microsoft.com/machine-learning-server/)
- [Machine Learning Server の R チュートリアル](https://docs.microsoft.com/advanced-analytics/tutorials/sql-server-r-tutorials?view=sql-server-2017)
- [Machine Learning Server の R サンプル](https://docs.microsoft.com/machine-learning-server/r/r-samples)
- [R 関数ライブラリ リファレンス](https://docs.microsoft.com/machine-learning-server/r-reference/introducing-r-server-r-package-reference)

## <a name="references"></a>References

小売業向けのその他の予測ソリューションの構築に興味がある場合は、Azure [AI ギャラリー](https://gallery.azure.ai/industries/retail)の[小売業セクション](https://gallery.azure.ai/)を参照してください。  