---
title: 銀行業におけるデータ管理の概要
author: dstarr
ms.author: dastarr
ms.date: 10/30/2019
ms.topic: article
ms.service: industry
description: Microsoft Azure を使用して、規制対象となる銀行環境でのデータを管理する手法について説明します。
ms.openlocfilehash: 1314054018c04e45b6450604febbf0142ead380d
ms.sourcegitcommit: f42a60539bec2a7769b42b6574f09eed4d1b6c79
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750532"
---
# <a name="data-management-in-banking-overview"></a>銀行業におけるデータ管理の概要

## <a name="introduction"></a>はじめに

現在の銀行は、顧客や変化する財務情勢の両方についての膨大な量の貴重な情報をファイアウォール内で保護して格納する責任を負っています。 多くの場合、そのような情報が使われることはありません。データを使えば、さまざまな銀行活動の意思決定を改善できる可能性があるにもかかわらず、簡単にアクセスしたり検索したりすることができないためです。

銀行は、このようなデータを使うことで、ローンに返済できなくなるリスクがある人の情報をすばやく見つけたり、どの市場ポートフォリオの評価調整が必要かを決定したりすることができるかもしれません。 さらに、規制の要件を満たすためにどのようにデータが格納および管理されているかについて明快な視野を持ち、規制に準拠する形でデータを利用、保持、アーカイブ、削除できるようにもなるでしょう。

大小を問わず、日々銀行機能の要件を満たすために必要なたくさんの決定事項があるので、データはますます重要になります。 それだけでなく、厳格な規制要件および財務犯罪防止義務を考えれば、銀行にはデータ リポジトリに到着する初期情報までさかのぼってデータ分析プロセスの結果を監査する機能が必要です。 追跡可能性には、取り込みからアクションにつながるデータの生成までの透明性が必要です。

銀行が提供している多くの口座または業務を管理するには、こういったすべてのデータを迅速かつコスト効率よく理解する必要があります。 銀行がデジタル的に成熟するにともない、データの量と新しい方法でそのデータを活用する機会が指数関数的に増加し、新しいビジネス モデルや顧客を中心とした業務領域を追求できるようになります。

効率的な業務、優れたアプリケーションのパフォーマンス、規制遵守を実現するには、適切なデータ ストレージ戦略が重要になります。 データ ストレージ戦略は、ビジネス インテリジェンスやアクションにつながる洞察を得るために使用できる形式でデータを取得する際の最初の要にもなります。

データ管理の一般的なパターンを次に示します。

![データ管理フロー](./assets/data-mgmt-in-banking-overview/data-mgmt-00.png)

このモデルで、「Data Services」は、あらゆるデータ変換やデータ結合など、アーカイブ以外のデータ操作を指しています。 データを活用して情報に基づく意思決定を増やす際に鍵となるのは、この活動です。

あらゆる銀行や金融機関は、データの取り込み、移動、格納を行います。 この記事では、Azure にデータを移動し、従来型のオンプレミスによるデータの格納、処理、アーカイブ、および削除から移行する方法について説明します。 Azure にデータを移動すると、銀行や金融機関は以下のような基本的なメリットを得ることができます。

- 必要な時間と場所でのみコンピューティング リソースやデータ容量を使用し、グローバル スケールで実質的に制限のないコスト管理を実現。

- オンプレミスの物理サーバーを廃止し、設備投資と管理コストを削減。

- バックアップとディザスター リカバリーの統合により、コストおよびデータ保護の複雑さを軽減。

- コンプライアンスのニーズを満たしつつ、低コスト ストレージにコールド データを自動アーカイブ。

- 学習、予測、変換などのニーズに応じてデータを処理する高度な統合データ サービスへのアクセス。

この記事では、効率的に Azure にデータを取り込むための推奨テクニックや、クラウドに移行してからの基本的なデータ管理テクニックについて説明します。

## <a name="data-ingest"></a>データの取り込み

金融機関には、既に収集されており、現在のアプリケーションで使用されているデータが必要になります。 このデータを Azure に移動するには、いくつかのオプションがあります。 多くの場合、既存のアプリケーションは、最小限の変更でオンプレミスと同様に Azure 内のデータに接続できます。 Microsoft [Azure SQL Database](/azure/sql-database/?WT.mc_id=bankdm-docs-dastarr) を使用する場合は特にこれが当てはまりますが、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/databases?WT.mc_id=bankdm-docs-dastarr) でも Oracle や TeraData MongoDB など向けのソリューションを見つけることができます。

オンプレミスから Azure にデータを移動するためのデータ移行戦略にはさまざまなものがあり、かかる時間も大きく異なります。 以下に示すすべての手法には、データの透明性と信頼性の高いセキュリティが備わっています。

### <a name="virtual-network-vnet-service-endpoints"></a>Virtual Network (VNet) サービス エンドポイント

顧客の金融情報を扱う場合、最大の懸念事項となるのがセキュリティです。
多くの場合、Azure のリソース (データベースなど) のセキュリティ保護は、Azure 自身のネットワーク インフラストラクチャの設定と、特定のエンドポイントを経由するネットワーク アクセスによります。

Azure にデータを転送する前に、Azure リソースとオンプレミスからの接続の両方を保護するネットワーク トポロジを検討するとよいでしょう。
[Virtual Network (VNet?WT.mc_id=bankdm-docs-dastarr) サービス エンドポイント](/azure/virtual-network/virtual-network-service-endpoints-overview?WT.mc_id=bankdm-docs-dastarr)は、Azure で定義した VNet に対するセキュリティで保護された直接接続を提供します。

VNet は、制限された VNet 内の Azure リソースを格納するために、Azure で定義されます。 VNet に対するエンドポイントは、重要な Azure サービス リソースと定義されている VNet 上のリソースに対してのみ、セキュリティで保護されたアクセスを有効化します。

## <a name="database-lift-and-shift"></a>データベースのリフト アンド シフト

データベース移行の「リフト アンド シフト」モデルは、Azure SQL Database を使用する最も一般的なシナリオの 1 つです。 リフト アンド シフトとは、単に既存のオンプレミス データベースを取得してクラウドに直接移動することを意味します。 これを行う理由には、次のようなものがあります。

- 価格が高いなどの運用上の理由で、現在のデータセンターから移行する
- 現在、オンプレミス SQL Server データベースのハードウェアが期限切れになっている、または耐用年数が近づいている
- 会社の全般的な「クラウド移行」戦略に対応する
- SQL Azure の可用性とディザスター リカバリー機能を活用する

小規模のデータベースの場合、一般的なデータ インジェストの最初のステップとなるのは、Azure Portal、Azure CLI、または Azure SDK で必要になるデータストアと構造 (テーブルなど) の作成です。 こういった小さなデータ ストアの場合、次のステップは、適切なデータを適切な Azure データ ストレージにコピーするカスタム アプリケーションを記述することかもしれません。 大規模なデータ移行の場合、Azure でバックアップを復元するのが通常、最も高速なルートです。

Azure にデータを安全かつ迅速に転送する方法は、たくさんあります。 いくつかの標準的な手法と、それぞれの利点と欠点については、[この記事を参照してください](/azure/architecture/data-guide/scenarios/data-transfer?WT.mc_id=bankdm-docs-dastarr)。

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

SQL Server データベースのリフト アンド シフトを行う場合、[Microsoft Azure Database Migration Service](/azure/dms/dms-overview?WT.mc_id=bankdm-docs-dastarr) を使用してデータベースを Azure に移動することができます。 このサービスは、[Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?WT.mc_id=bankdm-docs-dastarr) を使用してオンプレミス データベースと Azure SQL で提供される機能の互換性を保証しています。 データベースを移行する前に必要な変更を行うかどうかは自由です。 なお、このサービスの使用には、オンプレミス ネットワークと Azure 間のサイト対サイトのインターネット接続が必要です。

### <a name="bulk-copy-program-for-sql-server"></a>SQL Server 用の一括コピー プログラム

現在オンプレミスにある SQL Server を SQL Azure に移動する場合、もう 1 つの優れた手法となるのが、SQL Server Management Studio と [BCP ユーティリティ](https://azure.microsoft.com/blog/bcp-and-sql-azure/?WT.mc_id=bankdm-docs-dastarr)を使用してデータを SQL Azure 移動する方法です。 スクリプトを書いて元のオンプレミス サーバーから Azure SQL データベースを作成した後、BCP を使用して迅速にデータを SQL Azure に転送します。

### <a name="blob-and-file-storage"></a>Blob およびファイル ストレージ

多くの場合、銀行の各支店には、ローカル オンプレミス サーバーに独自のファイル ストアがあります。 これが原因で、支店間でファイル共有の問題が発生し、特定のファイルで信頼できる唯一の情報源が存在しないという結果になる場合があります。 さらに悪いことに、その金融機関には支店がアクセスする「正式な」ファイル ストアがあり、接続が途切れるなど、ファイル共有へのアクセスに問題が発生している可能性もあります。

Azure には、こういった問題の軽減に役立つサービスがあります。 このデータを Azure に移動すれば、すべてのデータに対して信頼できる唯一の情報源を提供できます。さらに、アクセス許可やアクセス制御を一元管理でき、どこからでもアクセスできるストレージも提供できます。

特定のデータ形式に適した別のデータ ストレージ ソリューションも存在します。
たとえば、オンプレミスの SQL Server の格納データには、Azure SQL が最適でしょう。 .csv ファイルまたは Excel ファイルの格納データは、Blob service の上に実装されている [Azure Blob](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=bankdm-docs-dastarr) ストレージまたは [Azure Files](/azure/storage/files/storage-files-introduction?WT.mc_id=bankdm-docs-dastarr) ストレージが最適でしょう。

Azure に出入りするほぼすべてのデータは、データ移動の一環として Blob ストレージを通過します。 Blob ストレージでは、次の重要な要素があります。

- 耐久性と可用性
- セキュリティ保護とコンプライアンス
- 管理性とコスト効率
- 拡張性と高パフォーマンス
- オープン性と相互運用性

すべての支店を同じ Azure ファイル共有に接続する場合、多くは図 1 のように既存の銀行のデータセンター経由で行います。 銀行のデータ センターは、SMB (サーバー メッセージ ブロック) 接続経由で Files ストレージに接続します。
論理的に、およびサイトのネットワークの観点から見れば、ファイル共有は銀行のデータセンターに配置することも、その他のネットワーク ファイル共有としてマウントすることもできます。
この手法を使用すると、保存中のデータも、データ センターと Azure 間の転送中のデータも暗号化されます。

![論理ファイル共有](./assets/data-mgmt-in-banking-overview/data-mgmt-01.png)

図 1

多くの場合、企業は Files ストレージを使用して大量のファイルを統合およびセキュリティ保護します。 これにより、古いファイル サーバーを廃止したり、ハードウェアの使用目的を変更したりできます。
Files ストレージに移行するもう 1 つの利点は、データ管理と復旧サービスの一元化です。

### <a name="azure-data-box"></a>Azure Data Box

多くの場合、銀行が Azure に取り込む情報は、ペタバイト単位でなかったとしてもテラバイト単位です。 幸い、Azure のデータ ストアは非常に柔軟で、高い拡張性を備えています。

莫大なデータを Azure に移行にすることに重点を置いているサービスが、[Azure Data Box](https://azure.microsoft.com/services/storage/databox/?WT.mc_id=bankdm-docs-dastarr) です。 このサービスは、Azure の接続経由でデータやバックアップを転送せずにデータを移行できるように設計されています。 Azure Data Box はテラバイト単位のデータに適したアプライアンスで、Azure Portal から注文できます。 これは、皆さんの所在地に向けて出荷されます。そこでネットワークに接続して標準の NAS プロトコル経由でデータを読み込みます。読み込んだデータは、standard256 AES 暗号化によって保護されます。 データをアプライアンスに格納してから Azure データ センターに返送すると、そこでデータが Azure に取り込まれます。
その後、デバイスは安全に消去されます。

## <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection (AIP) は、組織がドキュメントと電子メールを分類、ラベル付け、保護するのに役立つクラウドベースのソリューションです。 ルールおよび条件を定義する管理者は自動で、ユーザーまたはユーザーが推奨設定を指定されている組み合わせの下では手動で、これを実行できます。

## <a name="data-services"></a>データ サービス

銀行は、さまざまなコア銀行システムと、元のシステム、オンボード システム、オファー管理システム、CRM システムなどから届くデータのため、マスター データ管理やメタ データの不整合に悩まされています。 Azure には、このような問題やその他一般的に生じるデータの問題の軽減に役立つツールがあります。

金融サービス組織は、データに対して多くの操作を行う必要があります。 Azure データ ストアにデータを書き込む場合、データを変換したり、他のデータと結合して取り込むデータを拡張したりする必要があるかもしれません。

### <a name="azure-data-factory"></a>Azure Data Factory

[Microsoft Azure Data Factory](/azure/data-factory/introduction?WT.mc_id=bankdm-docs-dastarr) は、Data Factory パイプラインの受信、処理、およびデータ移動の監視をサポートするフル マネージド サービスです。 Data Factory のアクティビティは、データ管理パイプラインの構造を形成します。

Data Factory を使用すると、Azure が受け取るデータや他の Azure サービス間を流れるデータの変換や拡張を行うことができます。 Data Factory は、ETL (抽出 - 変換 - 読み込み)、ELT (抽出 - 読み込み - 変換)、データ統合という複雑なハイブリッド プロジェクト用に構築された、管理クラウド サービスです。

たとえば、データはアクションにつながる分析情報を得るために、分析パイプラインやツールに提供されるかもしれません。 データは、機械学習ソリューションに送られたり、のちほどダウンストリームで処理を行うために別の形式に変換されたりするかもしれません。 一例として、.csv ファイルを機械学習システムに適した parquet ファイルに変換し、その parquet ファイルを Blob ストレージに格納することがあげられます。

データは、[Azure HDInsight](/azure/hdinsight/hadoop/apache-hadoop-introduction?WT.mc_id=bankdm-docs-dastarr)、[Spark](/azure/hdinsight/spark/apache-spark-overview?WT.mc_id=bankdm-docs-dastarr)、[Azure Data Lake Analytics](/azure/data-lake-analytics/data-lake-analytics-overview?WT.mc_id=bankdm-docs-dastarr)、[Azure Machine Learning](/azure/machine-learning/?WT.mc_id=bankdm-docs-dastarr) などのダウンストリーム コンピューティング サービスに送ることもできます。 これにより、システムに直接データを提供して、分析を行ったりインテリジェント レポートを生成したりすることができます。 次の図 2 に、データのイングレスに使われる一般的なモデルの 1 つを示します。 データは、共通の [Data Lake](/azure/data-lake-store/data-lake-store-overview?WT.mc_id=bankdm-docs-dastarr) に保持され、ダウンストリーム分析サービスに使用されます。

![Data Lake 取り込みモデル](./assets/data-mgmt-in-banking-overview/data-mgmt-02.png)

図 2

Data Factory パイプラインはアクティビティで構成されます。アクティビティは、データセットを取り込んで出力します。 アクティビティはパイプラインにまとめることができます。パイプラインでは、データを取得する場所、処理方法、結果の格納場所を定義することができます。 アクティビティでパイプラインを構築することは、Data Factory の中核になります。Azure Portal で視覚的なワークフローを適切に構成すると、パイプラインの作成が簡単になります。 [完全なアクティビティの一覧は、こちらを参照してください](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=bankdm-docs-dastarr)。

### <a name="azure-databricks"></a>Azure Databricks

[Azure Databricks](/azure/azure-databricks/?WT.mc_id=bankdm-docs-dastarr) は、Azure のマネージド Apache Spark ベースの分析プラットフォームです。 非常に拡張性が高く、Spark ジョブは必要な大きさのマシン クラスターで実行できます。 Notebook から使用できる Databricks は、データ サイエンティスト、データ エンジニア、およびビジネス アナリストが共同作業を行う場所を提供します。

データ変換や分析が必要な場合、Databricks は論理処理パイプラインになります。 直接 Data Factory で取り込み、洞察までにかかる時間が重要になる機械学習シナリオや、単純なファイル変換に使用することができます。

![Databricks](./assets/data-mgmt-in-banking-overview/data-mgmt-03.png)

## <a name="archiving-data"></a>データのアーカイブ

アクティブなデータ ストアでデータが不要になった場合、国や地方の銀行規制に従い、コンプライアンスまたは監査証跡用にアーカイブすることができます。 Azure には、アクセス頻度の低いデータの格納に使用できるオプションがあります。 多くの場合、何年も保管しなければならないデータには、プライバシーの問題があります。

データ格納コストは高くなる場合があります。特に、オンプレミス データベースに格納する場合にはそれが当てはまります。 これらのデータベースには、頻繁にアクセスされず、新しくアーカイブされるデータの書き込みや、アーカイブの必要がなくなったデータの削除のみ行われるものもあります。
オンプレミス マシンへアクセス頻度が少ないということは、ハードウェアの総保有コストが高いということを意味します。

### <a name="azure-archive-storage"></a>Azure Archive Storage

ファイルやイメージなどの非構造化データの Blob ストレージとして、Azure はホット、クール、およびアーカイブという[複数層の記憶域](/azure/storage/blobs/storage-blob-storage-tiers?WT.mc_id=bankdm-docs-dastarr)を提供しています。 ホット アクセス層は、アクティブかつ最も高性能で、アプリケーションで使用されるデータ向けです。 クール アクセス層は、データの短期的なバックアップとディザスター リカバリーに使用するデータセットで、アプリケーションでアクセス頻度の低いデータに使用することもできます。 アーカイブ層はオフラインを想定するデータ用で、コストが最も低くなります。

アーカイブ層のデータは、クールまたはホット層に復元できますが、この操作は完了するまで数時間かかる場合があります。 少なくとも 180 日間アクセスしないデータがある場合、アーカイブ ストレージが適切かもしれません。 アーカイブ ストレージ内に BLOB がある場合、それを読み取ることはできませんが、一覧表示、削除、およびメタデータの取得など、他の既存の操作は実行できます。 アーカイブ データ層は、最もコストのかからない BLOB ストレージのデータ層です。

### <a name="azure-sql-database-long-term-retention"></a>Azure SQL Database の長期保有

Azure SQL を使用する場合、バックアップを 10 年間保存する[バックアップ長期保有サービス](/azure/sql-database/sql-database-long-term-retention?WT.mc_id=bankdm-docs-dastarr)を使用できます。 ユーザーは、週、月、または年単位で長期ストレージに保持するようにバックアップをスケジュールできます。

長期ストレージからデータベースを復元するには、特定のバックアップを、そのタイムスタンプに基づいて選択します。 データベースは、元のデータベースと同じサブスクリプションの既存のサーバーに復元できます。

## <a name="deleting-unwanted-data"></a>不要なデータの削除

多くの場合、銀行規制やデータ保持に関するポリシーに準拠するために、不要になったデータを削除する必要があります。 この不要なデータの技術的なソリューションを実装する前に、合意されたポリシーに違反しないような消去プランを制定することが重要です。 データは、アーカイブまたは Azure の他のデータ ストアからいつでも削除できます。

不要なデータを削除するための効果的な戦略は、一定の間隔で削除することです。最も一般的なのは、夜間または毎週です。 [時間によってトリガーされる Azure 関数](/azure/azure-functions/functions-bindings-timer?WT.mc_id=bankdm-docs-dastarr)を書くと、このジョブを効率よく実行することができます。 お客様がデータを削除すると、Microsoft Azure によって、キャッシュされたデータやバックアップ コピーも含めて、データが削除されます。

## <a name="getting-started"></a>Getting Started (概要)

現在の使用量や現在使われているデータ モデルの成熟度に基づいて使用を開始する方法はたくさんあります。 すべての場合で、これがデータ ストレージ、処理、データ ストアごとに必要なリテンション期間モデルの確認を行う最適なタイミングです。 これは、規制に対するコンプライアンス シナリオでデータ管理システムを構築する際に重要です。
クラウドは、現在のオンプレミスにはない新しいチャンスを提供します。 既存のデータ モデルがある場合、その更新が必要かもしれません。

新しいデータ モデルに納得できたら、データ インジェスト戦略を決定します。 どのようなデータ ソースがあるでしょうか。 Azure にはどんなデータを格納する予定でしょうか。 いつどうやって Azure に移行する予定でしょうか。 コンテンツの種類、サイズなどにより、移行に役立つ多くのリソースがあります。 Azure Data Migration Service は、そのような例の 1 つです。

データを Azure にホストしたら、役立つ期間や有効期間を過ぎたデータの消去プランを作成します。 長期 (コールド) 記憶域はアーカイブの優れたオプションですが、期限切れデータをクリーンアップすると、占有領域やストレージ全体のコストを削減できます。 バックアップおよびアーカイブ [Azure ソリューション アーキテクチャ](https://azure.microsoft.com/solutions/architecture/?solution=backup-archive?WT.mc_id=bankdm-docs-dastarr)は、全体的な戦略の計画に役立つ優れたリソースです。

## <a name="relevant-technologies"></a>関連テクノロジ

- [Azure Functions](/azure/azure-functions/functions-bindings-timer?WT.mc_id=bankdm-docs-dastarr) は、サーバーレスのスクリプトおよび小さなプログラムで、システム イベントまたはタイマーに応答して実行できます。

- [Azure Storage クライアント ツール](/azure/storage/common/storage-explorers?WT.mc_id=bankdm-docs-dastarr)は、データ ストアにアクセスでき、Azure portal よりもはるかに多くのものを含むツールです。

- [Blob ストレージ](/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=bankdm-docs-dastarr)は、テキストやイメージ、その他の非構造化データのファイルを格納するのに適しています。

- [Databricks](/azure/azure-databricks/?WT.mc_id=bankdm-docs-dastarr) は、Spark クラスターの簡単な実装を提供するフル マネージド サービスです。

- [Data Factory](/azure/data-factory/concepts-pipelines-activities?WT.mc_id=bankdm-docs-dastarr) は、データの保管、移行、および処理のサービスを自動化データ パイプラインにまとめるために使用できるクラウド データ統合サービスです。

## <a name="conclusion"></a>まとめ

銀行および金融業界のデジタル ランドスケープは急速に変化しています。それにともない、顧客は即座に利用でき、ランプ アップ タイムが遅くないソリューションやパートナーを求めるようになっています。 データ インジェストが指数関数的に増加するにつれて、銀行には重要なデータを格納、分析、使用する高速かつ革新的で安全な方法が必要となります。

いくつかのテクノロジと戦略を使用している Azure は、データ インジェスト、処理、アーカイブ、および削除の要件に役立ちます。 データを Azure に取り込むことは簡単です。種類や構造などに応じて、データを格納するさまざまなデータ ストアを利用できます。サード パーティのデータベースも含めれば、SQL Server や SQL Azure 以外のデータ ソリューションも利用できます。

Databricks や Data Factory などの Azure サービスを使用すると、運用やデータ操作を簡単にすることができます。 アーカイブ ストレージはアクセス頻度の低いデータの長期的なストレージに使用でき、必要に応じてローリング サイクルで削除できます。

データ管理計画の立案を開始するには、[バックアップおよびアーカイブ ストレージ](https://azure.microsoft.com/solutions/architecture/?solution=backup-archive?WT.mc_id=bankdm-docs-dastarr)用の Azure ソリューションのライブラリを参照してください。

**作成者**:Howard Bush および David Starr
