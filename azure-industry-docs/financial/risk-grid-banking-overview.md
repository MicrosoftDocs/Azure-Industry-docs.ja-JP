---
title: Azure Batch、Azure Data Lake でのグリッド コンピューティングによるリスク分析
author: dstarr
ms.author: dastarr
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: Azure で銀行取引にリスク グリッド コンピューティングを実装するときのビジネス上の考慮事項を示します。
ms.openlocfilehash: 746b93e545aa8ff61a8fab4a021b6c5caa1889bb
ms.sourcegitcommit: f030566b177715794d2ad857b150317e72d04d64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74234657"
---
# <a name="risk-grid-computing-in-banking-overview"></a>銀行取引のリスク グリッド コンピューティングの概要

## <a name="introduction"></a>はじめに

企業の財務部門や投資銀行では、最も重要な業務の 1 つがリスクの分析です。

投資ポートフォリオに関連するリスクの全体像を与えるために、金融リスク アナリストは調査内容を見直し、景気と社会状況を監視し、規制情報を常に確認し、投資環境のコンピューター モデルを作ります。

ポートフォリオに影響を与えるさまざまなベクトルを対象とするリスク分析には、コンピューターによるモデル化が必要になるだけの複雑性があります。 ほとんどのアナリストはコンピューター モデルでの作業にかなりの時間を費やし、金融状況の変化をシミュレーションして、予測します。 投資リスク (市場リスク、信用リスク、運用リスク) を評価するとき、予測モデルを処理するための計算負荷はデータの量と多様性のために非常に大きくなることがあります。

クラウド コンピューティングを利用すれば、資本コストとインフラ管理の負担なく、アナリストは必要なときに大量の計算リソースにアクセスできるので、クラウド コンピューティングはリスク グリッド コンピューティングやリスク モデリングに大きな利点となります。 この記事では、Microsoft Azure を活用して現在のリスク グリッド コンピューティング リソースを増やし、リスク グリッド コンピューティング ワークロードのコストとスピードを最適化する方法について検証します。 トピックとしては、安全で信頼できる接続、一括処理、オンプレミス サーバーのフル稼働時に必要に応じて計算リソースを増やす方法について説明します。

## <a name="grid-computing-services"></a>グリッド コンピューティング サービス

アナリストは自分のモデルをバッチ処理パイプラインに送るシンプルで信頼できる方法を必要とします。データの取り込みから、データ処理、分析に進み、結果データから洞察を引き出します。

リスク モデルの入力データにはいくつかの形式があります。最も一般的な形式は Excel ファイルまたは .csv ファイルです。 そのようなファイルは多くの場合、リスク コンピューティング パイプラインの後半で、リスク モデルの処理にもっとふさわしい形式に再構築されます。 そのようなファイルを解析し、処理する一般的な手法は、共通の目標に到達する目的で連動する仮想マシン (VMs?WT.mc_id=gridbank-docs-dastarr) のグリッドによる一括処理です。

[Azure Batch](/azure/batch/?WT.mc_id=gridbank-docs-dastarr) は、下の図のように、複数の worker VM を並行で動かすことを可能にする Azure サービスです。 データ ファイルを処理し、結果を機械学習システムまたはデータ ストアに送信することは、worker ノードの共通タスクです。 worker ノードによって実行されるアプリケーション コードは顧客によって作成されます。そのため、ほぼすべてのアクションがバッチ ジョブ内で行われることがあります。

![オンプレミス バッチ](./assets/risk-grid-compute-assets/01-on-prem.png)

Azure には、Azure Batch を使用するリスク グリッド コンピューティングのための高度なソリューションがあります。 顧客は Azure Batch を使用して既存のリスク コンピューティング グリッドを拡張したり、オンプレミス リソースを完全クラウド基盤のソリューションに置換したりできます。

Azure クラウドには安全な方法で直接接続できます。 ハイブリッド ネットワーク経由で Azure に接続時、Batch のリスク処理グリッド ワーカー ノードは、オンプレミスに保存されたデータに接続してモデリング データにアクセスすることができます。 顧客は Azure 内の適切なストレージにデータをアップロードすることもできます。Batch はデータに直接アクセスできます。

## <a name="secure-connectivity-to-azure"></a>Azure へのセキュリティで保護された接続

Azure でリスク グリッド コンピューティング ソリューションを構築するとき、企業は多くの場合、取引システム、ミドル オフィス リスク管理、リスク分析など、既存のオンプレミス アプリケーションを引き続き使用します。 Azure はそのような既存の投資を拡張するものです。

クラウドに接続するときは、最初に考慮するべき問題がセキュリティです。 Azure に直接接続するための最初の一歩は、現行のセキュリティ モデルを計算に入れることです。 オンプレミスで Active Directory (AD?WT.mc_id=gridbank-docs-dastarr) をご使用中のお客様は、Azure に接続する際、既存の ID リソースを使用することができます。 サービス アカウントはオンプレミス AD に存続できます。

### <a name="hybrid-network-solution"></a>ハイブリッド ネットワーク ソリューション

ハイブリッド ネットワークにより、顧客のオンプレミス ネットワークに Azure が直接接続されます。 Azure には、セキュリティで保護された信頼性の高い方法で現在のオンプレミス システムを Azure に接続するために、[Microsoft Azure ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbank-docs-dastarr) と [VPN Gateway](/azure/vpn-gateway/?WT.mc_id=gridbank-docs-dastarr) という 2 つのモデルが用意されています。 実装、パフォーマンス、その他の属性には違いがあるものの、両方とも信頼できる接続ソリューションです。

![Azure 接続](./assets/risk-grid-compute-assets/02-connectivity.png)

&quot;クラウドへのバースト&quot;を利用すると、既存リソースの負荷が急増したとき、クラウドベースのコンピューターに負荷を分散できます。お客様のデータ センターまたはプライベート クラウドのリソースを補強することができます。 ハイブリッド ネットワーク モデルを使用すると、クラウド基盤のリスク コンピューティング グリッドが既存ネットワークの単純な拡張となるため、"クラウドへのバースト" シナリオが簡単になります。

上記の論理アーキテクチャに提示されているシンプルなモデルのネットワーク接続構成以外にもネットワーク接続構成がいくつか存在します。 ネットワークの Azure への接続に関する決定とアーキテクチャのガイダンスとして、「[_オンプレミス ネットワークの Azure への接続_](/azure/architecture/reference-architectures/hybrid-networking/)」という記事が参考になります。

### <a name="rest-api-solution-over-internet"></a>インターネット経由の REST API ソリューション

ハイブリッド ネットワークを構築する代わりに、データを Azure Storage (ファイルまたは Blob ストレージがおそらく候補に?WT.mc_id=gridbank-docs-dastarr) にアップロードし、Batch にストレージからデータ ファイルを読み込ませることができます。 これを達成するには、安全な (SSL?WT.mc_id=gridbank-docs-dastarr) 接続を利用して Azure に接続し、Azure Storage にドキュメントを保管し、[Batch サービス REST API](/rest/api/batchservice/?WT.mc_id=gridbank-docs-dastarr) または SDK と目的にかなったアプリケーションでリスク グリッド コンピューティング ジョブを管理し、Batch 実行を調整します。

### <a name="azure-data-factory"></a>Azure Data Factory

シナリオによっては [Azure Data Factory](/azure/data-factory/?WT.mc_id=gridbank-docs-dastarr) を使用するというソリューションもあります。Azure Data Factory はクラウド基盤のデータ統合サービスであり、保管、移動、処理からなる大規模なパイプラインを構成します。 データは Data Factory パイプライン経由で必要なときにアップロードできます。 このサービスによって、Azure portal でビジュアル デザイナーが提供されます。Extract/Transform/Load (抽出/変換/ロード、ETL?WT.mc_id=gridbank-docs-dastarr) ソリューションを Azure で構築するためのデザイナーです。 Data Factory では、さらに処理する目的で Azure にデータを取り込むことができます。

## <a name="matching-processing-needs-with-demand"></a>処理ニーズを要求に合わせる

毎日のリスクであれ、大量のデータが付随する月末のリスクであれ、リスクを計算するとき、相当な計算リソースが使用されます。 このような計算が年中無休で実行されることはありません。 リスク計算がオンプレミス グリッドで実行されていないとき、組織が保有する高額なサーバーには何のワークロードも与えられませんが、他の固定費と共に、電力、冷却、データセンターの占有コストは常に発生します。

### <a name="augmenting-on-premises-grid-with-azure-batch"></a>Azure Batch でオンプレミス グリッドを補強する

コストを最小限に抑えるために、要求が低いとき、要件を満たすのに十分な worker ノードだけを所有し、管理することを企業は選択できます。 その場合、要求の大きいリスク グリッド コンピューティング ジョブは Azure の高性能サーバーに送ることができます。作業量要求に合わせて自由自在に拡大縮小できます。

Azure Batch 処理モデルには、リスク グリッド コンピューティングに関して利点がいくつかあります。

- さまざまなオンプレミス システムで既存の投資を増強します。
- 要求が低いときは、既存のインフラストラクチャでリスク分析ニーズにサービスを提供できます。その際、Azure ベースの worker ノードの割り当てを解除します。
- 要求が高いときは、リスク コンピューティング グリッドに追加の容量を提供します。
- Batch ワークロードで必要とされる処理能力とコンピューターのプロファイルを照合し、組み合わせることができます。負荷に対して[ハイ パフォーマンス コンピューティング (HPC)](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) 構成が求められる場合にも対応できます。

一般的なソリューションは、オンプレミス worker がすべて使用されているとき、Azure で自動的に worker ノードを追加することです。 リンク グリッド ヘッド ノードによって追加の worker が要求されます。 Azure でグリッド worker ノードの数が自動的にスケーリングされ、柔軟な要求ソリューションが可能になります。

![ハイブリッド クラウド](./assets/risk-grid-compute-assets/03-hybrid-cloud.png)

この配置にはリソースが効率的に使用されること以外にも長所があります。 独立したタスクの場合、ワーカーを追加することで負荷を直線的に拡大縮小することができます。 Azure にはまた、非常に大きな VM インスタンスや複数の GPU エードを持つコンピューターを試す柔軟性があります。 この柔軟性により実験やイノベーションが可能になります。

四半期に 1 回の評価など、計算能力を増やす必要があるとき、その追加の容量も Azure Batch 自動スケールから調達できます。 自動スケールによって Batch ソリューションに順応性が与えられます。 Azure を利用し、必要な負荷に合わせてリソースを拡大縮小することで、ハードウェアを所有することより低いコストで容量を大幅に増やすことができます。

ほとんどの商用グリッド製品で何らかの形式の "クラウドへのバースト" に対応しており、リスク分析負荷の概念実証を簡単にしています。 たとえば、[Microsoft HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) は Azure で実行することができるほか、TIBCO や Univa といった企業の製品も同様に実行できます。 このようなサード パーティ製のツールまたはシステムの多くは [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?WT.mc_id=gridbank-docs-dastarr) から入手できます。

### <a name="migrating-additional-resources-to-the-cloud"></a>追加リソースをクラウドに移行する

ワークロードが増えたり、オンプレミス データセンターのインフラストラクチャが古くなったりした場合、組織はリスク グリッド コンピューティングのためのバッチ処理全体を Azure に移動できます。

### <a name="growing-into-azure"></a>Azure に移行する

オンプレミス コンピューターの寿命が尽きたら、worker ノードをクラウドにさらに分散できます。 Batch ヘッド ノードにも同じことが当てはまります。 これにより、オンプレミス ネットワークと Azure の関係が逆になります。 これは、Azure ExpressRoute などのネットワーク間製品や残りのオンプレミス worker ノードの使用を止めることでコストを減らす機会になる可能性もあります。

この変更の一環として、さまざまなファイル取り込み手法を利用し、Azure でデータを利用できる場合があります。 Azure にはストレージの選択肢がたくさん用意されています。たとえば、REST エンドポイントの場合、計算ジョブにオンプレミス ネットワークから回収させるのではなく、データを直接アップロードできます。

 ![オンプレミス バッチ](./assets/risk-grid-compute-assets/04-batch-process.png)

このモデルの下では、すべてのリスク グリッド コンピューティング活動をクラウドで行うことができます。 worker によって処理されるデータ ファイルは Azure ストレージに格納できます。データは Azure Data Lake に直接送信できます。Azure HDInsight で機械学習ニーズに対処できます。 最後になりますが、Power BI と Azure Analytics は優れたデータ分析ツールであり、Azure に保管されているすべてのデータで機能します。

### <a name="data-security-considerations-for-risk-grid-computing"></a>リスク グリッド コンピューティングのデータ セキュリティ上の考慮事項

多くの場合、計算データに個人を特定できる情報 (PII?WT.mc_id=gridbank-docs-dastarr) は含まれていませんが、ほとんどの銀行は今でもクラウドにワークロードを置く前にセキュリティ リスク評価を実施しています。 この評価には Microsoft からのインプットが必要になる場合があり、結果としてセキュリティに関する推奨事項が与えられることがあります。

リスク グリッド コンピューティングで注目すべき考慮事項は、[Azure VNet 内でバッチ処理を実行する](/azure/batch/batch-virtual-network?WT.mc_id=gridbank-docs-dastarr)ということです。 これによってプール コンピューティング ノードが他のコンピューティング ノードやオンプレミス ネットワークと安全に通信できます。 適切なサービス アカウントと Network Service Groups (NSG) 作成し、バッチ コンピューティング ノードで使用する必要があります。 転送するデータと Azure Storage に保管しているデータの暗号化についても [Azure ではソリューションを用意しています](/azure/security/blueprints/financial-services-regulated-workloads?WT.mc_id=gridbank-docs-dastarr)。

考慮すべき領域は次のとおりです。Active Directory (AD) に参加している/参加していないコンピューティング ノード (Windows Server ノード?WT.mc_id=gridbank-docs-dastarr)、[VM ディスク暗号化](/azure/security/azure-security-disk-encryption?WT.mc_id=gridbank-docs-dastarr)、保存中と転送中の計算入出力データのセキュリティ、Azure ネットワーク構成、アクセス許可などがあります。 認証は秘密鍵を通して REST API レベルで処理されることもあります。

## <a name="getting-started"></a>Getting Started (概要)

お客様の多くは、社内リスク コンピューティング グリッドを既に使用しています。 会社がグリッドを内部で開発した場合、そのグリッドを延長するものとして Azure Batch を検討してください。 Azure Batch の出発点としてお勧めなのが現行のオンプレミス ソリューションの拡張です。具体的には、現行の処理アプリケーション ロジックを複製し、それを Azure でバッチ ジョブとして実行します。 これには、アプリケーションの機能にもよりますが、Azure Batch コンピューティング ノードをオンプレミス ネットワークに参加させるネットワーク ソリューションが必要な場合があります。

セキュリティ、スピード、接続の信頼性に関する問題を緩和するには、Azure ExpressRoute または VPN Gateway を使用し、オンプレミス ネットワークを Azure に接続することを検討してください。 そこから、オンプレミス ヘッド ノードにより Azure ベースの worker ノードのクラスターをプロビジョニングし、必要に応じて拡大縮小します。

これでようやく、リスク コンピューティング インフラストラクチャを Azure に完全移行する準備ができました。 準備ができた場合は、[こちらの記事](/azure/batch/?WT.mc_id=gridbank-docs-dastarr)をお読みになり、今すぐ移行を始めてください。

## <a name="technologies-presented"></a>提供されるテクノロジ

[Azure Batch](/azure/batch/?WT.mc_id=gridbank-docs-dastarr) では、オンプレミス リスク コンピューティング worker ノードを増強し、要求に合わせてコンピューティング リソースを動的に提供できます。

[Azure DataLake](/azure/data-lake-store?WT.mc_id=gridbank-docs-dastarr) では、リスク分析データのストレージ、処理、分析が提供されます。

[ExpressRoute](/azure/expressroute/expressroute-introduction?WT.mc_id=gridbank-docs-dastarr) は、接続プロバイダーが提供するプライベート接続を介してオンプレミス ネットワークを Azure に延長するものです。

[Azure HDInsight](/azure/hdinsight/?WT.mc_id=gridbank-docs-dastarr) は完全なマネージド型のオープンソース分析サービスであり、月末の一括実行で提供されるデータのような大量のデータを処理します。

[Microsoft HPC Pack](/azure/virtual-machines/windows/hpcpack-cluster-options?WT.mc_id=gridbank-docs-dastarr) では、一括処理のためにハイ パフォーマンス コンピューティング クラスターをプロビジョニングできます。

[Power BI](/power-bi/?WT.mc_id=gridbank-docs-dastarr) は、分析情報を得て共有する目的でリスク アナリストが使用する一連のビジネス分析ツールです。

[VPN Gateway](/azure/vpn-gateway/?WT.mc_id=gridbank-docs-dastarr) はインターネット経由でオンプレミス ネットワークを Azure クラウドに延長するものです。

## <a name="conclusion"></a>まとめ

この記事で取り上げたソリューションは、銀行業務におけるリスク グリッド コンピューティングの手法です。 Azure 製品/サービスとさまざまな既存のクライアント システム アーキテクチャを考慮すると、他のアーキテクチャが使用されることもあります。 その場合でも、Batch にはこの記事で取り上げた長所があり、リスク グリッド コンピューティングのモデルとして理にかなったモデルを提供します。

[オンプレミス ネットワークを Azure に延長する](/azure/architecture/reference-architectures/hybrid-networking/?WT.mc_id=gridbank-docs-dastarr)ことで、オンプレミス ネットワークに既に存在するネットワーク リソースやその他の処理システムに Azure は簡単にアクセスできます。 オンプレミスのコンピューターが寿命に近づいているなら、ハイブリッド モデルを支えるより、Azure で Batch コンピューティングを全面的に使用する方が理にかなっている場合もあります。

ハイブリッド ネットワークを必要とせずに Batch を活用するもう 1 つの方法は、バッチ ジョブを開始する前にファイルを Azure Storage にアップロードすることです。 これは段階的に行うことも、一括実行の開始プロセスとして行うこともできます。

接続方法を選択したら、リスク コンピューティングを開始する論理的な手順は、Azure コンピューティング worker ノードに既存のジョブを配置し、それをテスト環境で実行し、コードを変更する必要があるか確認することになります。 選択した言語またはツールで Azure Batch を開始するとき、[この記事の内容が出発点となります](/azure/batch/batch-virtual-network?WT.mc_id=gridbank-docs-dastarr)。

[銀行取引におけるリスク グリッド コンピューティングのソリューション ガイド](/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=banking-docs-dastarr)
