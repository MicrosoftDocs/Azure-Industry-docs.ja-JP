---
title: eコマース ソリューションの Azure への移行の概要
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: この記事では、オンプレミスから Azure に e コマース インフラストラクチャを移行する各フェーズについて説明します。
ms.openlocfilehash: e918f1157dc2bc42a6c4d0decfef95a8daa7ccf0
ms.sourcegitcommit: b8f9ccc4e4453d6912b05cdd6cf04276e13d7244
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74263356"
---
# <a name="migrating-your-e-commerce-solution-to-azure-overview"></a>eコマース ソリューションの Azure への移行の概要

## <a name="introduction"></a>はじめに

企業にとって、既存の eコマース ソリューションをクラウドに移行することには多くのメリットがあります。これにより、スケーラビリティが実現され、24 時間 365 日のアクセシビリティを顧客に提供することができ、クラウド サービスの統合が容易になります。 しかし、eコマース ソリューションをクラウドに移行する作業は大掛かりなものとなり、コストについても意志決定者の理解を得る必要があります。 このドキュメントでは、オプションを提示することを目的に Azure の移行の範囲について説明します。 最初のフェーズは、IT 部門によるコンポーネントのクラウドへの移行から始まります。 Azure に移行した後は、eコマース チームが ROI を向上させ、クラウドを活用するために実行できる手順について説明します。

**重大な決定を下すべきとき**

グローバルな eコマース取引が小売売上高の総額に占める割合はごくわずかに過ぎませんが、その販売経路は年々着実に成長を続けています。 2017 年には、小売売上高の総額に占める eコマースの売上は 10.2% で、2016 年の 8.6% から増加しました ([出典](https://www.emarketer.com/Report/Worldwide-Retail-Ecommerce-Sales-eMarketers-Updated-Forecast-New-Mcommerce-Estimates-20162021/2002182))。 クラウド コンピューティングの登場と共に、eコマースが成熟してくるにつれ、小売業者は岐路に立たされています。  選択しなければなりません。 進化し続けるテクノロジによってもたらされる新機能を用いたビジネス モデルを構想することも、既存システムのモダナイゼーションを計画することもできます。

**カスタマー ジャーニーを向上させる**

カスタマー ジャーニーに主に的を絞った eコマースには、さまざまな属性があります。 これらの属性は、発見、評価、購入、購入後という 4 つの主な領域に分類できます。

顧客の行動は、データとして取り込まれます。 ショッピング ファネルは、製品データ、トランザクション、インベントリ、配送、受注処理、顧客プロファイル、ショッピング カート、製品のおすすめなどを表示するために使用するアプリケーションへのコネクション ポイントをまとめたものです。

一般的なリテール ビジネスでは、顧客向けのアプリケーションから基盤となるアプリケーションまでソフトウェア ソリューションの大きなコレクションに依存します。  次の図は、一般的なリテール ビジネスに存在する機能を絵で表しています。

 ![](./assets/migrating-ecommerce-solution-to-azure/ecommerce-system-sketch.png)

クラウドは、組織がテクノロジを獲得し、使用し、管理する方法を一変させるチャンスを与えてくれます。  データ センターの維持にかかるコストの削減、信頼性やパフォーマンスの向上、サービスを後から追加できる柔軟性の実現など、その他にもメリットがあります。 このユース ケースでは、リテール ビジネスが既存のインフラストラクチャを Azure に移行するために選択できるフローに注目します。 また、リホスト、リファクター、リビルドといった段階的なアプローチを使用して、新しい環境を活用していきます。 多くの組織がこの一連のフローに従ってモダナイゼーションを行うかもしれませんが、ほとんどの場合、開始点としていずれかのフェーズに落ち着きます。  現在のアプリケーションを Azure にリホストすることを断念し、直接リファクターまたはリビルドから開始することを選択するかもしれません。  この決定は、組織のモダナイゼーションの必要を最適な形で満たすために、アプリケーション、そして組織によってそれぞれ異なったものとなります。

## <a name="rehost"></a>リホスト

&quot;リフト アンド シフト&quot; とも呼ばれますが、この段階では、物理サーバーと VM をクラウドにそのまま移行します。 現在のサーバー環境を IaaS に直接移行することによって、コスト削減、セキュリティや信頼性の向上といったメリットが得られます。 サイズを適切に設定した VM でワークロードを実行するといった手法によって、コストを削減できます。 今日、オンプレミスの VM や物理マシンの性能が、小売業者が通常の業務で必要とする性能を超えているということが多々見受けられます。 VM は、1 年に数回だけ発生する季節的なビジネス ピークに対応できるように備えておく必要があります。 その結果、オフピーク シーズン中には使用しない性能のためにも支払っていることになります。 Azure では、その時点のビジネス サイクルの需要に基づいて、適切なサイズの VM を選択します。

Azure にリホストするための 3 つのフェーズがあります。

- **分析** :アプリケーション、ワークロード、ネットワーク、セキュリティなどのオンプレミス リソースを識別し、インベントリ管理を行います。 このフェーズの終わりには、既存システムの完全なドキュメントが作成されています。
- **移行** :各サブシステムをオンプレミスから Azure に移行します。 この段階では、アプリケーションの通信を継続しながら、Azure をデータ センターの拡張機能として使用します。
- **最適化** :システムを Azure に移行する際、適切にサイズが設定されるようにします。 一部の VM に割り当てられているリソースが多過ぎることが判明した場合、VM のタイプを CPU、メモリ、ローカル ストレージが適切に組み合わされたものに変更します。

### <a name="analyze"></a>分析

以下の手順を実行します。

1. オンプレミスのサーバーとアプリケーションのリストを作成します。 その際、エージェントまたは管理ツールを使用して、サーバー、そのサーバーで実行されているアプリケーション、現在のサーバー使用率、サーバーとアプリケーションの構成方法に関するメタデータを収集します。 結果として、環境内のすべてのサーバーとアプリケーションのレポートが生成されます。
1. 依存関係を識別します。 ツールを使用して、相互に通信しているサーバーおよび相互に通信しているアプリケーションを識別します。 結果として、すべてのアプリケーションとワークロードのマップが生成されます。 これらのマップは、移行計画に送られ使用されます。
1. 構成を分析します。 目標は、Azure で実行するのに必要な VM のタイプを把握することです。 結果として、Azure に移行できるすべてのアプリケーションのレポートが生成されます。 これらは、さらに以下のように分類できます。
      1. 変更なし
      1. 名前の変更など基本的な変更
      1. わずかなコードの変更など小規模な変更
      1. 移行に余分な作業を必要とする互換性のないワークロード
1. 予算を作成します。 CPU やメモリなどをそれぞれ列挙したリストと各アプリケーションの要件は既に作成されています。 これらのワークロードを適切なサイズで設定された VM に配置します。 クラウド プラットフォームでは、使用量に基づいて請求されます。 お客様のニーズを適切なサイズに設定された Azure VM にマップするツールがあります。 Windows VM または SQL Server を移行している場合、Azure での費用を削減する [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/?WT.mc_id=retailecomm-docs-scseely)についても調べる必要があります。

Microsoft では、システムを分析し、カタログするための複数のツールを提供しています。 VMWare を実行している場合、検出と評価をサポートする [Azure Migrate](/azure/migrate/migrate-overview?WT.mc_id=retailecomm-docs-scseely) を使用できます。 このツールは、Azure に移動できるマシンを識別し、実行する VM のタイプを提案し、ワークロードのコストを見積もります。 Hyper-V 環境では、[Azure Site Recovery Deployment Planner](/azure/site-recovery/hyper-v-deployment-planner-overview?WT.mc_id=retailecomm-docs-scseely) を使用します。 数百もの VM を移動する必要がある大規模な移行では、[Azure 移行パートナー](https://azure.microsoft.com/migration/partners/?WT.mc_id=retailecomm-docs-scseely)を利用することも検討できます。 これらのパートナーは、ワークロードを移行するための専門知識と経験を有しています。

### <a name="migrate"></a>移行

どのサービスをクラウドに移行するか、どのような順序で移行するかについて計画を開始します。 この段階でワークロードの移行も行うため、次の順序で実行します。

1. ネットワークを構築する。
2. ID システム (Azure Active Directory) を組み込む。
3. Azure でのストレージ部分をプロビジョニングする。

移行中、Azure 環境はオンプレミス ネットワークの拡張機能となります。 論理ネットワークと [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview?WT.mc_id=retailecomm-docs-scseely) を接続できます。 [Azure ExpressRoute](/azure/expressroute/?WT.mc_id=retailecomm-docs-scseely) を使用することで、インターネットに接続したことのないプライベート接続で、ネットワークと Azure 間の通信を維持することもできます。 Azure VPN Gateway がオンプレミス VPN デバイスと通信するサイト間 VPN を使用することもできます。この方法では、すべてのトラフィックが Azure とネットワーク間の暗号化された通信を使用して安全に送信されます。 ハイブリッド ネットワークをセットアップする方法の詳細を示すリファレンス アーキテクチャを公開しました。[こちら](/azure/architecture/reference-architectures/hybrid-networking/vpn?WT.mc_id=retailecomm-docs-scseely)を参照してください。

ネットワークを構成したら、ビジネス継続性について計画します。 オンプレミス データをクラウドに移行して、クラウドと既存のデータが同じであることを確認する、リアルタイム レプリケーションを使用することをお勧めします。 eコマース ストアを閉じることはありません。重複では、顧客に与える影響を最小限に留めながら、オンプレミスから Azure に切り替える機能を提供します。

データ、アプリケーション、および関連するサーバーの Azure への移行を開始します。 多くの企業では、[Azure Site Recovery](/azure/site-recovery/site-recovery-overview?WT.mc_id=retailecomm-docs-scseely) サービスを使用して Azure に移行します。 このサービスは、事業継続とディザスター リカバリー (BCDR) を目的としています。 これは、オンプレミスから Azure への移行に最適です。 実装チームは、オンプレミスの VM と物理サーバーを Azure に移行する方法を詳しく説明した[こちら](/azure/site-recovery/migrate-tutorial-on-premises-azure?WT.mc_id=retailecomm-docs-scseely)をお読みください。

サブシステムを Azure に移行したら、テストを行い、すべて想定どおりに動作していることを確認します。 すべての問題が解決されたら、ワークロードを Azure に移動します。

### <a name="optimize"></a>最適化

この時点では、環境を監視し続け、環境の変化にワークロードが一致するように基盤となるコンピューティング オプションを変更します。 環境の正常性を監視する際は、必ず各リソースがどれほど使用されているかに注目する必要があります。 目標は、ほとんどの VM で使用率が 75 - 90% になるようにすることです。 極端に低い使用率で稼働する VM では、より多くのアプリケーションをその VM で実行するようにするか、適切なパフォーマンス レベルを保持する Azure 上の最もコストのかからない VM に移行することを検討します。

Azure には、環境を最適化するツールも用意されています。 [Azure Advisor](/azure/advisor/advisor-overview?WT.mc_id=retailecomm-docs-scseely) は、環境のコンポーネントを監視し、ベスト プラクティスに基づいてユーザーの環境に合わせた推奨事項を提示します。 この推奨事項は、アプリケーションを使用するリソースのパフォーマンス、セキュリティ、可用性の向上を図るのに役立ちます。 Azure portal では、アプリケーションの正常性に関する情報も把握できます。 ご使用の VM で、[Linux および Windows 向けの Azure 仮想マシン拡張機能](/azure/virtual-machines/extensions/overview?WT.mc_id=retailecomm-docs-scseely)を活用してください。 これらの拡張機能では、デプロイ後の構成、ウイルス対策、アプリの監視といった機能を利用できます。 また、[Network Watcher](/azure/network-watcher/network-watcher-monitoring-overview?WT.mc_id=retailecomm-docs-scseely)、[Service Map](/azure/monitoring/monitoring-walkthrough-servicemap?WT.mc_id=retailecomm-docs-scseely)、[Application Insights](/azure/application-insights/app-insights-overview?WT.mc_id=retailecomm-docs-scseely)、[Log Analytics](/azure/log-analytics/log-analytics-overview?WT.mc_id=retailecomm-docs-scseely) など、ネットワーク診断、サービスの使用状況、サービスを介したアラートに関するその他の Azure サービスも利用できます。

組織の一部のシステムが Azure で最適化されている中、開発チームは移行後のフェーズ、つまりリファクターを開始することができます。

## <a name="refactor"></a>リファクター

移行が完了したら、eコマース アプリケーションは、Azure で新しいホームのメリットを使い始めることができます。 リファクター フェーズは、環境全体が移行が完了するまで待つ必要はありません。 CMS チームの移行が完了したものの、ERP チームがまだ完了していないといった場合でも、問題ありません。 CMS チームは、リファクタリングの作業を開始できます。 この段階で追加の Azure サービスを使用して、アプリケーションをリファクタリングすることにより、コスト、信頼性、パフォーマンスを最適化します。 リフト アンド シフトでは、プロバイダーが管理するハードウェアと OS しか利用できませんでしたが、このモデルではクラウド サービスを利用してコストを削減することもできます。 アプリケーション コードや構成を少し変更するだけで、現在のアプリケーションのそのまま利用し、コンテナー、データベース、ID 管理システムなどの新しいインフラストラクチャ サービスにアプリケーションを接続します。

リファクタリング作業で変更するコードと構成はほんのわずかです。 ほとんどの場合、この段階で採用されるテクノロジはリソースを構築してデプロイするスクリプトに依存するため、つまりデプロイ手順はスクリプトで行うため、オートメーションにより専念することになります。

さまざまな Azure サービスを利用できますが、リファクター フェーズで使用する最も一般的なサービス、つまりコンテナー、アプリ サービス、データベース サービスに焦点を当てます。 なぜリファクタリングに注目するのでしょうか。 リファクタリングでは、無理のない範囲でコードの負債を保持することで、長期的なコストを削減する強力なコード基盤を提供します。

コンテナーは、アプリケーションをバンドルする方法を提供します。 コンテナーがオペレーティング システムを仮想化する手法のおかげで、1 つの VM に複数のコンテナーをまとめることができます。 まったくコードを変更せずに、またはいくつかコードを変更するだけで、アプリケーションをコンテナーに移動することができます。構成は変更しなければならない場合があります。 この作業は、アプリケーションをコンテナーにバンドルするスクリプトの記述につながります。 開発チームは、リファクタリング期間に、これらのスクリプトの記述とテストを行います。 Azure では、[Azure Kubernetes Service](/azure/aks/?WT.mc_id=retailecomm-docs-scseely) (AKS) と、コンテナー イメージを管理するために使用できる関連する [Azure Container Registry](https://azure.microsoft.com/services/container-registry/?WT.mc_id=retailecomm-docs-scseely) を介してコンテナー詰めをサポートします。

アプリ サービスでは、さまざまな Azure サービスを利用できます。 たとえば、[RabbitMQ](https://www.rabbitmq.com/) のようなキューにメッセージを配置することで、既存のインフラストラクチャで顧客の注文を処理できます。 (たとえば、1 つのメッセージで顧客に請求し、2 つ目のメッセージで注文の品を出荷します)。リホストするときは、別の VM に RabbitMQ を配置します。 リファクタリング中に、[Service Bus](/azure/service-bus-messaging/service-bus-queues-topics-subscriptions?WT.mc_id=retailecomm-docs-scseely) のキューまたはトピックをソリューションに追加し、RabbitMQ コードを書き直して、キュー機能を提供する VM の使用を停止します。 この変更により、一連の VM を低コストの常時オン メッセージ キュー サービスと置き換えられます。 Azure Portal には、その他のアプリ サービスもあります。

データベースについては、VM からサービスにデータベースを移行できます。 Azure では、[Azure SQL Database](/azure/sql-database/sql-database-cloud-migrate?WT.mc_id=retailecomm-docs-scseely) および [Azure SQL Database Managed Instance](/azure/sql-database/sql-database-managed-instance?WT.mc_id=retailecomm-docs-scseely) で SQL Server のワークロードをサポートします。 [データ移行サービス](https://azure.microsoft.com/services/database-migration/?WT.mc_id=retailecomm-docs-scseely)はご使用のデータベースを評価し、移行前に行う必要のある処理について知らせ、それからデータベースを VM からサービスに移行します。 Azure では、[MySQL](https://azure.microsoft.com/services/mysql/?WT.mc_id=retailecomm-docs-scseely)、[PostgreSQL](https://azure.microsoft.com/services/postgresql/?WT.mc_id=retailecomm-docs-scseely)、および[その他のデータベース](https://azure.microsoft.com/services/#databases?WT.mc_id=retailecomm-docs-scseely) エンジンのサービスをサポートします。

## <a name="rebuild"></a>再構築

ここまで、eコマース システムへの変更を最小限に留める努力をしてきました。稼働しているシステムはそのまま残してきました。 これから、クラウドのメリットを利用する方法について考慮します。 この段階では、PaaS または SaaS のサービスとアーキテクチャを積極的に採用することにより、既存のアプリケーションを変更します。 このプロセスでは、新機能を追加するか、クラウドのアプリケーションを再設計するため、大掛かりな変更が発生します。  _マネージド API_ は、クラウド システムを活用する新しい概念です。 サービス間の通信用の API を作成して、システムを簡単に更新することができます。  2 つ目のメリットは、所有しているデータから分析情報を得られることです。 _マイクロサービス プラス API_ アーキテクチャに移行し、機械学習とデータを分析するための他のツールを使用することによって、これを行います。

### <a name="microservices--apis"></a>マイクロサービス プラス API

マイクロサービスは、外部と接続する API を介して通信します。 各サービスは自己完結型であり、1 つのビジネス機能を実装させる必要があります (たとえば、顧客に商品を提案する、ショッピング カートを維持するなど)。 アプリケーションをマイクロサービスに分解するには、時間と計画が必要です。 マイクロサービスを定義する明確なルールはありませんが、大抵一緒に変更するコンポーネントをまとめて、デプロイ可能なユニットを減らすようにするのが一般的です。 マイクロサービスを使用すると、全体的なアプリケーションのテスト負荷を削減しながら、必要な頻度で変更をデプロイできます。 一部のサービスは、非常に小さくなる可能性があります。 そのため、[Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=retailecomm-docs-scseely) を使用してサーバーレスを進めると、使用していないときにリソースを消費しないようにしながら、呼び出し元の必要に応じてスケールアウトすることができるようになります。 その他のサービスは、製品の管理、顧客の注文の取り込みなど、ビジネス機能に基づいて分けられます。

サーバーレスのメカニズムには難点もあります。負荷が軽いときに、クラウド内のサーバーがコードを構成して実行するのに数秒かかるため、応答が遅くなる可能性があります。 顧客による商品の検索、注文、返品要求など、環境内の顧客によってよく使われる部分については、迅速かつ容易に対応できるようにしておきたいと思われるかもしれません。 パフォーマンスが低下してしまうと、ショッピング ファネルで顧客を失ってしまうリスクがあります。 迅速に対応する機能を既に備えていれば、[Azure Kubernetes Service](/azure/aks/?WT.mc_id=retailecomm-docs-scseely) で個別にデプロイ可能なユニットとしてその機能をリビルドします。  大量のメモリ、複数の CPU、たくさんのローカル ストレージを組み合わせる必要があるサービスなどのケースでは、自社の VM でマイクロサービスをホストする方が良い場合もあります。

各サービスは、相互作用に API を使用します。 API へのアクセスでは、マイクロサービスに直接接続できますが、その場合、サービスと通信する側がアプリケーション トポロジを把握している必要があります。 [API Management](/azure/api-management/?WT.mc_id=retailecomm-docs-scseely) のようなサービスでは、API を発行する一元的な方法が提供されます。 すべてのアプリケーションは、API Management サービスに接続するだけで済みます。 開発者は使用可能な API を検出できます。 API Management サービスは、リテール環境のパフォーマンスを向上させる機能も提供します。 このサービスは、アプリケーションのさまざまな部分に分けて API へのアクセスを制限したり (ボトルネックを避けるため)、値の変更に時間のかかる応答をキャッシュしたり、JSON から XML に変換したりできます。 ポリシーの完全な一覧については、[こちら](/azure/api-management/api-management-policies?WT.mc_id=retailecomm-docs-scseely)をご覧ください。

### <a name="make-use-of-your-data-and-the-azure-marketplace"></a>データと Azure Marketplace を使用する

すべてのデータとシステムを Azure に配置しているため、ビジネスに他の SaaS ソリューションを簡単に組み込むことができます。 すぐに行えることがあります。 たとえば、[Power BI](https://powerbi.microsoft.com/?WT.mc_id=retailecomm-docs-scseely) を使用してさまざまなデータ ソースを 1 つにまとめることができます。これにより、視覚化を実現し、レポートを生成して、分析情報を得ることができます。

次に、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?WT.mc_id=retailecomm-docs-scseely) で提供されるサービスに注目してください。これは、在庫の最適化、顧客の属性に基づいたキャンペーンの管理、好みや履歴に基づいた各顧客への適切なアイテムの表示などを実現するのに役立ちます。 Marketplace オファリングで機能するデータを構成するのに少し時間がかかります。

## <a name="next-steps"></a>次の手順

多くの開発チームは、技術的負債に対処し、キャパシティをより効率的に活用するために、リホストとリファクターを同時に行えないものかと模索します。 次の手順に進む前に、リホストを行うことにはメリットがいくつかあります。  新しい環境にデプロイする際に生じる問題を診断して修正するのが簡単になります。 これにより、開発チームとサポート チームに、Azure を用いた新しい環境に慣れる時間を与えることができます。 システムのリファクーとリビルドを開始するときには、安定して稼働しているアプリケーションの上に構築していくことができます。 そして、加える変更をより小さく、的を絞ったものにし、更新の頻度を増やすことができます。

クラウドへの移行に関する汎用的なホワイトペーパーを公開しました:「[クラウド移行の基本](https://azure.microsoft.com/resources/cloud-migration-essentials-e-book/?_lrsc=9618a836-9f81-4087-901f-51058783c3a8&WT.mc_id=retailecomm-docs-scseely)」。 移行を計画する際にこれをお読みいただければ、きっとお役に立つでしょう。

## <a name="technologies-presented"></a>使用するテクノロジ

リホスト中に以下が使用されます。

- [Azure Advisor](/azure/advisor/advisor-overview?WT.mc_id=retailecomm-docs-scseely) は、ベスト プラクティスに従って Azure デプロイメントを最適化できるようにする、個人用に設定されたクラウド コンサルタントです。
- [Azure Migrate](/azure/migrate/migrate-overview?WT.mc_id=retailecomm-docs-scseely) サービスは、Azure への移行についてオンプレミスのワークロードを評価します。
- [Azure Site Recovery](/azure/site-recovery/site-recovery-overview?WT.mc_id=retailecomm-docs-scseely) では、Azure VM、オンプレミス VM、物理サーバーのディザスター リカバリーを調整および管理します。
- [Azure Virtual Network](/azure/virtual-network/virtual-networks-overview?WT.mc_id=retailecomm-docs-scseely) では、Azure Virtual Machines (VM) などのさまざまな種類の Azure リソースが、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。
- [Azure ExpressRoute](/azure/expressroute/?WT.mc_id=retailecomm-docs-scseely) を利用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。

リファクター中に以下が使用されます。

- [Azure Kubernetes Service](/azure/aks/?WT.mc_id=retailecomm-docs-scseely) を使用すると、ホストされている Kubernetes 環境を管理できます。これによって、コンテナー オーケストレーションの知識がなくてもコンテナー化されたアプリケーションを迅速かつ簡単にデプロイおよび管理できるようになります。
- [Azure SQL Database](/azure/sql-database/sql-database-technical-overview?WT.mc_id=retailecomm-docs-scseely) は、Microsoft Azure における汎用リレーショナル データベース管理サービスです。 これは、リレーショナル データ、JSON、空間、XML などの構造をサポートします。 SQL Database には、マネージド シングル SQL データベース、エラスティック プール内のマネージド SQL データベース、および SQL マネージド インスタンスが用意されています。

リビルド中に以下が使用されます。

- Azure [API Management](/azure/api-management/?WT.mc_id=retailecomm-docs-scseely) が組織にもたらす利点は、外部のパートナーや社内の開発者に API を公開することによって、社内に眠っているデータやサービスの可能性を発掘できることです。
- [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=retailecomm-docs-scseely) は、小規模なコード (&quot;関数&quot;) をクラウドで手軽に実行できるソリューションです。
- [Power BI](https://powerbi.microsoft.com/?WT.mc_id=retailecomm-docs-scseely) は、組織全体に分析情報を提供できるビジネス分析ツール スイートです。

## <a name="conclusion"></a>まとめ

eコマース システムを Azure に移行するには、分析、計画、明確に定められたアプローチが必要になります。 リホスト、リファクター、リビルドの 3 段階のアプローチについて考慮しました。 これらのアプローチでは、各ステップで生じる変更を最小限に抑えながら、作業中のフェーズから次のフェーズに移ることが可能になります。 小売業者は、リホストをスキップして、コンポーネントのリファクターまたはリビルドを行うこともできます。 多くの場合、これでモダナイゼーションへの明確な道筋が見えてきます。後はできるときに行うだけです。 Azure での経験を重ねるにつれ、新しい機能の追加、コストの削減、システム全体の改善といった機会が見えてくるでしょう。


_この記事は、Scott Seely と Mariya Zorotovich によって作成されました。_