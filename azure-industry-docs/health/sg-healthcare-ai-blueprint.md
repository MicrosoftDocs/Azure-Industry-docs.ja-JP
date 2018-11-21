---
title: AI 用の Azure ブループリントを実装する
author: dastarr
ms.author: dastarr
ms.date: 08/24/2018
ms.topic: article
ms.service: industry
description: この記事では、AI 用の Microsoft Azure ブループリントに関するガイダンスを提供します。
ms.openlocfilehash: f7c9290e6bbc0d500a9f7774c2020f78b5e94aca
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654349"
---
# <a name="implementing-the-azure-blueprint-for-ai"></a>AI 用の Azure ブループリントを実装する

## <a name="introduction"></a>はじめに

医療機関では、患者の治療効果の改善から日常業務の合理化に至るまでの業務の多くの部分に、AI (人工知能) と ML (機械学習) が有益なツールとなり得ることを実感しています。 多くの場合、医療機関には、AI/ML システムを実装する技術スタッフはいません。 このような状況を改善し、Azure で迅速に AI/ML ソリューションを稼働させるために、Microsoft では [Azure 医療 AI ブループリント](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)を作成しました。 このブループリントを使用して、安全で、コンプライアンスとセキュリティを確保し、信頼性の高い方法で AI/ML を導入する方法を示します。

AI 用の医療ブループリントにより、Azure を使用している組織は、AI/ML を自力で導入できます。 この記事では、ブループリントのインストール、そのコンポーネント、および患者の入院期間を予測するために AI/ML 実験を実行する方法について説明します。

### <a name="benefits"></a>メリット

このブループリントは、医療機関が規制の厳しい医療環境 (システムに HIPAA および HITRUST コンプライアンス要件を確実に守らせることなど) で AI/ML をサポートするために、適切な PaaS (サービスとしてのプラットフォーム) アーキテクチャに関するガイダンスとクイック スタートを提供するために作成されました。

多くの場合、医療機関の技術スタッフには新しいプロジェクトに取れる時間はほとんどなく、新しい複雑なテクノロジを学習する必要があるプロジェクトでは特にそうです。 このブループリントは、技術スタッフが Azure とそのサービスのいくつかをすばやく把握するのに役立ち、習得のためのコストを節約できます。 技術スタッフは、インストールした後にリファレンス実装としてこのブループリントから学習し、その知識を使用してその機能を拡張したり、ブループリントにならって作られた新しい AI/ML ソリューションを作成したりできます。

このブループリントによって組織では、AI/ML の新しい機能をすばやく稼働できます。 AI と ML の準備が整ったら、技術スタッフは、さまざまなソースから収集されたデータを使用して AI/ML の実験を実行する準備が整います。 たとえば、これまでの敗血症の事例やその状態の個々の患者に関して追跡された多くの付随する変数には、データが既に存在している可能性があります。 このデータを匿名化された形式で使用すれば、技術スタッフは、患者の敗血症の可能性の指標を探して、その状態を適切に回避するための手術方法の変更に役立たせることができます。

このブループリントは、患者の入院期間を予測する方法を学習するためのデータとサンプル コードを提供します。 これは、AI/ML ソリューションのコンポーネントについて学習するために使用できるサンプルのユース ケースです。

### <a name="platform-or-infrastructure-as-a-service"></a>サービスとしてのプラットフォームまたはインフラストラクチャ

Microsoft Azure は PaaS と SaaS の両方のサービスを提供していて、お客様のニーズに合うものを選択することは、ユース ケースごとに異なります。 このブループリントは、PaaS サービスを使用して、患者の入院期間の予測について解決するよう設計されています。 Azure の医療 AI ブループリントは、医療機関向けに事前構成された、セキュリティで保護されコンプライアンスに準拠した AI/ML ソリューションのインスタンスを作成するために必要なすべてのものを提供します。 このブループリントで使用される PaaS モデルは、ブループリントを完全なソリューションとしてインストールし、構成します。

### <a name="paas-option"></a>PaaS オプション

PaaS サービスのモデルを使用すると、管理するハードウェアがないため、結果として総保有コストが削減されます。 組織がハードウェアや仮想マシンを購入および維持する必要はありません。 このブループリントは PaaS サービスを排他的に使用します。

これにより、オンプレミスのソリューションを維持するためのコストが削減され、技術スタッフはインフラストラクチャではなく戦略的イニシアティブに自由に集中できます。 また、コンピューティングとストレージに対する支払いを、資本支出予算から営業費の予算に移すこともできます。 このブループリントのシナリオ実行のコストは、サービスの使用量とデータ ストレージのコストによって決まります。

### <a name="iaas-option"></a>IaaS オプション

ブループリントとこの記事は PaaS 実装に焦点を当てていますが、ブループリントをサービスとしてのインフラストラクチャ (IaaS) 環境で使用できるようにする[オープン ソース拡張機能](https://github.com/Azure/Azure-Health-Extension)があります。

IaaS ホスティング モデルでは、お客様には、Azure でホストされる VM の稼働時間とその処理能力に対してお支払いいただきます。 IaaS は、お客様が自分の VM を管理するため、より高いレベルの制御を提供します。しかし、VM は稼働時間と使用量に対して課金されるため、通常コストは増加します。 さらに、お客様には、修正プログラムの適用やマルウェアからの保護などを行うことで VM を保守する責任があります。

IaaS モデルはこの記事の範囲外です。この記事では、このブループリントの PaaS のデプロイに焦点を当てています。

## <a name="the-healthcare-aiml-blueprint"></a>医療 AI/ML ブループリント

このブループリントは、医療のコンテキストでこのテクノロジを使用する出発点となります。 ブループリントを Azure にインストールすると、適切なアクター、権限、およびサービスを含む AI/ML シナリオをサポートするすべてのリソース、サービス、およびいくつかのユーザー アカウントが作成されます。

このブループリントには、患者の入院期間を予測する AI/ML 実験が含まれています。これは、スタッフの配置、ベッド数、その他の器材を前もって計画するのに役立ちます。 パッケージには、インストール スクリプト、サンプル コード、テスト データ、セキュリティとプライバシーのサポートなどが含まれています。

## <a name="blueprint-technical-resources"></a>ブループリントの技術リソース

以下のリソースはすべて、この GitHub リポジトリにあります。

主要なリソースは次のとおりです。

1. [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/powershell-scripting?WT.mc_id=ms-docs-dastarr) スクリプト (デプロイ、構成、およびその他のタスク用)。
2. [インストールについての詳細な手順](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md) (インストール スクリプトの使用方法を含む)。
3. [包括的なよくある質問](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/faq.md)。

このモデルの横断的関心事には ID とセキュリティが含まれていて、どちらも患者データを扱うときは特に重要です。 ML パイプラインのコンポーネントを、次の図に示します。

![ML パイプライン](assets/sg-healthcare-ai-blueprint-assets/ml-pipeline.png)

次の図には、インストールされている Azure の製品が示されています。 各リソースまたはサービスは、ID とセキュリティの横断的関心事を含む、AI/ML 処理ソリューションのコンポーネントを提供します。

![コンポーネントのゾーン](assets/sg-healthcare-ai-blueprint-assets/component-zones.png)

規制対象の医療環境に新しいシステムを実装するのは、手間のかかることです。 たとえば、システムのすべての側面を確実に HIPAA に準拠させ、かつ HITRUST 認定可能にするのは、ライトウェイト ソリューションを開発するだけではすみません。 このブループリントは、これらの複雑さに役立つ識別とリソースのアクセス許可をインストールします。

また、このブループリントは、患者の入院または退院をシミュレートおよび調査するために使用される、追加のスクリプトとデータも提供します。 これらのスクリプトを使用すると、スタッフは、安全で隔離されたシナリオでソリューションを使用して AI と ML を実装する方法についての学習を、すぐに始めることができます。

### <a name="additional-blueprint-resources"></a>ブループリントのその他のリソース

このブループリントは、技術スタッフのための優れたガイダンスと手順について説明し、完全に機能するインストールの作成に役立つ成果物も含まれています。 これらの他の成果物は、次のとおりです。

1. [脅威モデル](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=01828de2-9555-4bac-a2a0-44e9ed2eeeaf&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)。[Microsoft Threat Modeling Tool](https://www.microsoft.com/en-us/download/details.aspx?id=49168&WT.mc_id=ms-docs-dastarr) で使用できます。 この脅威モデルは、ソリューションのコンポーネント、その間のデータ フロー、および信頼境界を示しています。 このツールは、基本のブループリントを拡張しようとしているものによって脅威モデリングのために使用したり、セキュリティの観点からシステムアーキテクチャについて学習するために使用したりできます。

2. [HITRUST お客様の責任マトリックス](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=eab85244-b9ab-490a-9e2a-611153f7d3af&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)。Excel ブック内にあります。  これは、マトリックス内の要件ごとに、お客様が提供する必要があるものと Microsoft が提供する必要があるものを示しています。 この責任マトリックスについての詳細は、この記事では、このドキュメントの「セキュリティとコンプライアンス」の「ブループリントの責任マトリックス」のセクションに記載されています。

3. [HITRUST ヘルス データと AI のレビュー](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=ffc32e44-665e-46c5-b753-163d55a17d27&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)のホワイトペーパーは、HITRUST 認定資格を満たすための要件の観点から、このブループリントを検討しています。

4. [HIPAA ヘルス データと AI のレビュー](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=d5ce675c-3e83-45db-98a6-ae77fc439436&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)のホワイトペーパーは、HIPAA の規制を念頭にアーキテクチャをレビューしています。

これらのリソースは [GitHub のこちら](https://github.com/Azure/Health-Data-and-AI-Blueprint)にあります。

## <a name="installing-the-blueprint"></a>ブループリントのインストール

このブループリント ソリューションを使用した準備と実行のための時間的投資はわずかです。 PowerShell スクリプトの知識が少しあると望ましいのですが、スクリプトのスキルに関係なく技術者がこのブループリントのデプロイが成功するために、インストールのガイドとして役立つステップ バイ ステップの手順を使用できます。

技術スタッフは、Azure の使用経験が浅くても、30 分～ 1 時間でブループリントをインストールすることを期待できます。

### <a name="the-installation-script"></a>インストール スクリプト

このブループリントは、インストールについての非常に優れたガイダンスと手順を備えています。 また、ブループリントのサービスとリソースのインストールとアンインストールのスクリプトも備えています。 PowerShell デプロイ スクリプトの呼び出しは簡単です。 ブループリントをインストールする前に、特定のデータを収集して、次に示すように deploy.ps1 スクリプトへの引数として使用する必要があります。

```powershell
.\deploy.ps1 -deploymentPrefix <prefix> `
            -tenantId <tenant id> ` # also known as the AAD directory
            -tenantDomain <tenant domain> `
            -subscriptionId <subscription id> `
            -globalAdminUsername <user id> ` # ID from your AAD account
            -deploymentPassword <universal password> ` # applied to all new users and service accounts
            -appInsightsPlan 1 # we want app insights set up
```

### <a name="the-installation-environment"></a>インストール環境

**重要!** ブループリントは、Azure の外部のマシンからはインストールしないでください。 インストールは、Azure 内のクリーンな Windows 10 (またはその他の Windows VM) を作成し、そこからインストール スクリプトを実行すると、成功する可能性が高くなります。 この手法では、クラウドベースの VM を使用して、待機時間を軽減し、スムーズなインストールの作成を支援します。

このスクリプトは、インストール中に、読み込んで使用するその他のパッケージを呼び出します。 Azure 内の VM からインストールすると、インストール先のマシンとターゲットのリソース間のラグが大幅に低下します。 ただし、ダウンロードしたスクリプト パッケージの一部は、スクリプト パッケージが Azure 環境の外部に存在するために待機時間に対して脆弱なままになります。そのため、タイムアウト エラーが発生する可能性があります。

### <a name="install-failure-dont-panic"></a>インストールに失敗! (焦らず対処してください)

インストーラーは、インストール中にいくつかの外部パッケージをダウンロードします。 場合によっては、インストール先のマシンとパッケージの間のラグによってスクリプト リソースの要求がタイムアウトになることがあります。 これが発生した場合には、2 つの選択肢があります。

1. 変更なしでインストール スクリプトをもう一度実行します。 インストーラーは、既に割り当てられているリソースをチェックし、必要なもののみをインストールします。 この手法は適切に機能しますが、インストール スクリプトが既存のリソースを割り当てようとするというリスクがあります。 これによりエラーが発生する可能性があり、インストールは失敗します。

2. deploy.ps1 スクリプトは引き続き実行できますが、ブループリント サービスをアンインストールするために別の引数を渡します。 

```powershell
.\deploy.ps1 -clearDeploymentPrefix <prefix> `
             -tenantId <value> `
             -subscriptionId <value> `
             -tenantDomain <value> `
             -globalAdminUsername <value> `
             -clearDeployment
```

アンインストールが完了した後、インストール スクリプト内のプレフィックスを変更し、もう一度インストールしてみてください。 待機時間の問題が再度発生しなくなる可能性があります。 スクリプト パッケージのダウンロード中にインストールに失敗した場合は、アンインストーラー スクリプトを実行してから、インストーラーをもう一度実行してください。

アンインストール スクリプトを実行すると、次のものが失われます。

- インストーラー スクリプトによってインストールされたユーザー
- リソース グループとそのそれぞれのサービス (データ ストレージを含む) は失われます
- AAD (Azure Active Directory) に登録されたアプリケーション

Key Vault は「論理的な削除」として保持され、ポータルには表示されませんが、30 日間は割り当てを解除されないことに注意してください。 これにより、必要に応じて Key Vault を再構成できます。 これによる影響とその処理方法の詳細については、この記事の「技術的な問題」の「Key Vault」のセクションを参照してください。

### <a name="reinstall-after-an-uninstall"></a>アンインストール後の再インストール

アンインストール後にブループリントを再インストールする必要がある場合は、次回のデプロイでプレフィックスを変更する必要があります。プレフィックスを変更しないと、アンインストールされた Key Vault によりエラーが発生します。 これについての詳細は、この記事の _「技術的な問題」の「Key Vault」_ のセクションを参照してください。

### <a name="required-administrator-roles"></a>必要な管理者ロール

ブループリントをインストールするユーザーには、AAD でのグローバル管理者ロールがなければなりません。 インストールを実行するアカウントも、使用するサブスクリプションの Azure サブスクリプション管理者である必要があります。 インストールを実行するユーザーにこのどちらのロールもない場合、インストールは失敗します。

![ブループリントのインストーラー](assets/sg-healthcare-ai-blueprint-assets/blueprint-installer.png)

さらに、AAD との緊密な統合のため、このインストールは MSDN サブスクリプションを使用するようには設計されていません。 標準の Azure アカウントを使用する必要があります。 必要に応じて、ブループリント ソリューションのインストールとそのデモの実行に使用できるクレジットで[無料試用版を入手](https://azure.microsoft.com/en-us/free/?WT.mc_id=ms-docs-dastarr)してください。

## <a name="adding-other-resources"></a>その他のリソースの追加

Azure ブループリントのインストールには、AI/ML ユース ケースを実装するために必要なもの以外のサービスは含まれていません。 ただし、その他のリソースやサービスを Azure 環境に追加して、追加のイニシアチブに適したテスト ベッドや運用システムの出発点にすることができます。 たとえば、同じサブスクリプションおよび AAD の他の PaaS サービスまたは IaaS リソースを追加することもできます。

その他の Azure 機能が必要な場合は、[Cosmos DB](/azure/cosmos-db/introduction?WT.mc_id=ms-docs-dastarr) や新しい [Azure Functions](/azure/azure-functions/functions-overview?WT.mc_id=ms-docs-dastarr) などの新しいリソースをソリューションに追加することもできます。 新しいリソースやサービスを追加する場合は、規制やポリシーの準拠を維持するため、確実にセキュリティとプライバシー ポリシーを満たすように構成してください。

新しいリソースやサービスは、[Azure REST API](https://docs.microsoft.com/en-us/rest/api/?view=Azure&WT.mc_id=ms-docs-dastarr)、[Azure PowerShell スクリプト](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps?view=azurermps-6.6.0&WT.mc_id=ms-docs-dastarr)、または [Azure portal](http://portal.azure.com/?WT.mc_id=ms-docs-dastarr) を使用して作成することもできます。

## <a name="using-machine-learning-with-the-blueprint"></a>ブループリントでの機械学習の使用

このブループリントは、[患者の入院期間](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr#example-use-case)を予測するモデルで使用される回帰アルゴリズムを含む ML シナリオを示すために作成されました。 これは、スタッフ配置のスケジューリングとその他の運用上の決定に役立つように医療機関が実行する、一般的な予測です。 さらに、特定の状態の平均入院期間が増加または減少すると、時間の経過とともに異常を検出することができます。

### <a name="ingesting-training-data"></a>トレーニング データの取り込み

ブループリントがインストールされていて、すべてのサービスが適切に動作していれば、分析するデータを取り込むことができます。 100,000 件の患者[レコードが取り込み可能](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr#ingest)で、モデルで使用できます。 患者レコードの取り込みは、次に示すように、[Azure Machine Learning Studio](/azure/machine-learning/studio/what-is-ml-studio?WT.mc_id=ms-docs-dastarr) を使用して患者の入院期間の実験を実行する最初の手順です。

![取り込み](assets/sg-healthcare-ai-blueprint-assets/ingest.png)

ブループリントには、実験と、Machine Learning Studio (MLS) で ML ジョブを実行するために必要なデータが含まれています。 この例では、実験でトレーニング済みモデルを使用して、多数の変数に基づいて患者の入院期間を予測しています。

このデモ環境では、Azure SQL データベースに取り込まれたデータに欠陥やデータ要素の欠落はありません。 このデータはクリーンです。 たいていの場合はクリーンでないデータが取り込まれるため、機械学習トレーニング アルゴリズムの提供に使用したり ML ジョブでデータを使用したりする前に、「クリーンアップ」する必要があります。 データの欠落やデータ内の正しくない値は、ML 分析結果に悪影響を与えます。

## <a name="azure-machine-learning-studio"></a>Azure Machine Learning Studio

多くの医療機関には、ML プロジェクトに集中する技術スタッフはいません。 つまり、多くの場合は、貴重なデータが未使用のままであったり、ML ソリューションを作成するために費用のかかるコンサルタントを呼び寄せたりします。

AI/ML エキスパートと AI/ML について学習している人は、Azure Machine Learning Studio を使用して実験を設計することができます。 MLS は、ML 実験の作成に使用される Web ベースの設計環境です。 MLS を使えば、モデルを作成、トレーニング、評価、およびスコア付けして、さまざまなツールを使用してモデルを開発する際に貴重な時間を節約することができます。

MLS は ML ワークロード向けの完全なツールセットを提供します。 つまり、ML を初めて使用するユーザーは、このツールをすぐに使い始めることができ、他の ML ツールを使う場合よりも速く成果を挙げることができます。 そのため、IT スタッフは、他の部分でも、ML スペシャリストを呼び寄せることなく、価値を提供することに集中できます。 自分の医療機関でこれができることは、さまざまな仮説をテストできることを意味します。患者への干渉主義などの実施可能な洞察について分析された結果データは、エンドツーエンドのデータサイエンス ワークフローを実験として視覚的に構成するドラッグ アンド ドロップ キャンバスで使用される、[あらかじめ作成されたモジュール](/azure/machine-learning/studio-module-reference/index?WT.mc_id=ms-docs-dastarr#help-for-machine-learning-modules)を提供します。

デシジョン ツリー、デシジョン フォレスト、クラスタリング、タイム シリーズ、異常検出などの特定のアルゴリズムをカプセル化する、あらかじめ作成されたモジュールがあります。

どの実験にもカスタム モジュールを追加できます。 これらは [R 言語](/azure/machine-learning/studio/extend-your-experiment-with-r?WT.mc_id=ms-docs-dastarr)または [Python](/azure/machine-learning/studio/execute-python-scripts?WT.mc_id=ms-docs-dastarr) で記述されています。 これにより、構築済みのモジュールとカスタム ロジックを使用して、より高度な実験を作成することができます。

MLS により、学習モデルの[作成と使用](/azure/machine-learning/studio-module-reference/machine-learning-initialize-model?WT.mc_id=ms-docs-dastarr)だけでなく、一般的なアプリケーションで使用できる設計済みの実験のセットを提供することができます。 さらに、ブループリントのリソースを変更することなく、新しい実験を MLS に追加することができます。

時間を節約するには、[Azure AI ギャラリー](https://gallery.azure.ai/industries/healthcare?WT.mc_id=ms-docs-dastarr)にアクセスして、医療などの特定の業種向けのすぐに使用できる ML ソリューションを見つけてください。 たとえば、ギャラリーには、乳がん検出と心臓疾患の予測のためのソリューションと実験が含まれています。

## <a name="security-and-compliance"></a>セキュリティとコンプライアンス

セキュリティとコンプライアンスは、医療環境でソフトウェア システムを作成、インストール、または管理する際に考慮すべき、2 つの最も重要な点です。 ソフトウェア システムの導入に対して行った投資は、必要なセキュリティ ポリシーと認定資格を満たさないことで効果をなくす可能性があります。

この記事とブループリントでは技術的なセキュリティに焦点を当てていますが、物理的なセキュリティや管理上のセキュリティなどのその他のタイプのセキュリティも重要です。 これらのセキュリティについてはこの記事の対象範囲外です。この記事では、ブループリントの技術的なセキュリティに焦点を当てています。

### <a name="principle-of-least-privilege"></a>最小限の特権の原則

このブループリントは、ロールを持つ名前付きユーザーをインストールして、ソリューション内のリソースへのそのユーザーのアクセスのニーズをサポートしたり制限したりします。 このモデルは「最小限の特権の原則」と呼ばれ、システム設計におけるリソース アクセスに対するアプローチです。 この原則では、サービスおよびユーザー アカウントは、正当な目的のために必要なそのシステムとサービスのみにアクセスできることが必要であるとしています。

このセキュリティ モデルにより、HIPAA と HITRUST の要件に対するシステムのコンプライアンスが確保され、組織に対するリスクが取り除かれます。

### <a name="defense-in-depth"></a>多層防御

複数の抽象化レイヤーのセキュリティ制御を使用しているシステム設計は、多層防御を使用しています。 多層防御は、複数のレベルのセキュリティの冗長性を提供します。 これは、1 つのレイヤーの防御には依存しないことを意味します。 これにより、ユーザーおよびサービス アカウントがリソース、サービス、およびデータに適切にアクセスできることが保証されます。 Azure は、システム アーキテクチャのすべてのレベルのセキュリティおよび監視リソースを提供して、テクノロジのランドスケープ全体に多層防御を提供します。

ブループリントによってインストールされたソフトウェア システムなどでは、ユーザーは、ログインしても特定のリソースへのアクセス許可がない場合があります。 この多層防御の例は、最小特権の原則をサポートする RBAC (ロールベースのアクセス制御) と AAD によって提供されます。

2 要素認証も技術的な多層防御の形式であり、ブループリントのインストール時に必要に応じて含めることができます。

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault サービスはコンテナーであり、シークレット、証明書、およびアプリケーションで使用されるその他のデータを格納するために使用されます。 これには、データベース文字列、REST エンドポイント URL、API キー、および開発者がアプリケーション内へのハードコーディングや .config ファイルでの配布を希望しないその他のものが含まれています。

さらに、コンテナーには、アプリケーション サービス ID や AAD アクセス許可を持つその他のアカウントからアクセスできます。 これにより、コンテナーの内容を必要とするアプリケーションが、実行時にシークレットにアクセスできます。

コンテナーに格納されたキーは暗号化または署名されている可能性があり、キーの使用状況はセキュリティ上の問題のために監視できます。

Key Vault を削除しても、Azure からすぐに消去されるわけではありません。 これによる影響については、この記事の _「技術的な問題」の「Key Vault」_ のセクションを参照してください。

### <a name="application-insights"></a>Application Insights

多くの場合、医療機関には、高い信頼性と回復力が必要なミッション クリティカルでライフクリティカルなシステムがあります。 サービスの異常や中断は、可能な限り早く検出して修正する必要があります。 [Application Insights](/azure/application-insights/app-insights-proactive-application-security-detection-pack?WT.mc_id=ms-docs-dastarr) は、アプリケーションを監視し、問題が発生したときにアラートを送信する、アプリケーション パフォーマンス管理 (APM) テクノロジです。 エラーまたはアプリケーションの異常について、実行時にアプリケーションを監視します。 複数のプログラミング言語を使用するように設計されており、アプリケーションが正常でありスムーズに実行されていることを確認するための、豊富な機能セットを備えています。

たとえば、アプリケーションにメモリ リークがあるとします。 Application Insights は、豊富なレポートと監視対象の KPI によって、このような問題の検出と診断を支援します。 Application Insights は、アプリケーション開発者のための堅牢な APM サービスです。

この[対話型デモ](https://analytics.applicationinsights.io/demo#/discover/home?WT.mc_id=ms-docs-dastarr)は、Application Insights の主な機能を示しています。これには、医療機関の技術者がアプリケーションの状態と正常性を監視するために使用できる、包括的な監視ダッシュボードが含まれています。

#### <a name="azure-security-center"></a>Azure Security Center

ミッション クリティカルなアプリケーションでは、リアルタイムのセキュリティと KPI の監視が必要です。 Azure Security Center (ASC) によって、Azure リソースを確実に安全に保護することができます。 ASC は、セキュリティ管理および高度な脅威の防止サービスです。 これを使用して、ワークロード全体へのセキュリティ ポリシーの適用、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。

Security Center Standard は、以下のようなサービスを備えています。

- **ハイブリッド セキュリティ** - オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 これは、Azure を使用する医療機関で使用される、ハイブリッド クラウド ネットワークで特に役立ちます。
- **高度な脅威検出** - ASC は、高度な分析と [Microsoft インテリジェント セキュリティ グラフ](http://cloud-platform-assets.azurewebsites.net/intelligent-security-graph/?WT.mc_id=ms-docs-dastarr)を使用して、巧妙化していくサイバー攻撃に対応して直ちに軽減します。
- **アクセスとアプリケーションの制御** - 特定のワークロード向けの、機械学習を活用したホワイトリスト登録の推奨事項を適用することで、マルウェアや他の望ましくないアプリケーションをブロックします。

医療 AI ブループリントのコンテキストでは、ASC は、システム コンポーネントを分析し、サブスクリプション内のサービスとリソースの脆弱性を示すダッシュボードを備えています。 ダッシュボードの個々の要素には、次のようなソリューションの懸念事項が表示されます。

- ポリシーとコンプライアンス
- リソース セキュリティの検疫
- 脅威の防止

システムの脅威の脆弱性を改善するための 13 の提案を特定するダッシュボードの例を、次に示します。 また、HIPAA とポリシーへの準拠がわずか 46% であることも示されています。

![脅威の防止](assets/sg-healthcare-ai-blueprint-assets/threat_protection.png)

重大度が高いセキュリティの問題を掘り下げると、次に示すように、影響を受けるリソースと各リソースに必要な解決策がわかります。

IT スタッフがすべてのリソースとネットワークを手動でセキュリティ保護しようとすると、多くの時間を費やす可能性があります。 特定のシステムの脆弱性を識別するために ASC を使用すれば、他の戦略的取り組みに時間を費やすことができます。 多くの脆弱性が識別された場合、ASC は、修復アクションを自動的に適用し、管理者が問題を深く詳細に調査しなくてもリソースを保護できます。

![高いリスク](assets/sg-healthcare-ai-blueprint-assets/high_risks.png)

ACS は、その脅威の検出とアラート機能によってさらに多くのことを行います。 ACS を使用して、環境を保護するために、ネットワーク、マシン、クラウド サービスに対する攻撃や侵害後のアクティビティを監視します。
ASC は、さまざまな Azure リソースからセキュリティ情報とログを自動的に収集、分析、および統合します。 

ASC の ML 機能により、手動による方法では明らかにされない脅威を検出できます。 ASC には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。

### <a name="rbac-security"></a>RBAC セキュリティ

[ロールベースのアクセス制御](/azure/role-based-access-control/role-assignments-portal?WT.mc_id=ms-docs-dastarr) (RBAC)は、リソースごとに特定の権限を持つこともある、保護されたリソースへのアクセスを許可または拒否します。 これにより、適切なユーザーのみが、指定されたシステム コンポーネントにアクセスできます。 たとえば、データベース管理者は暗号化された患者データが含まれているデータベースにアクセスできる場合がありますが、医療機関は、適切な患者のレコードに、それらを表示するアプリケーションを使用しないとアクセスできない場合があります。 これは、通常は電子医療記録または電子カルテのシステムです。 看護師はデータベースにアクセスする必要はなく、データベース管理者は患者のカルテのデータを確認する必要はありません。

RBAC はこれを実現するために Azure セキュリティに含まれていて、Azure リソースの厳密に対象を絞ったアクセス管理を実現します。 ユーザーごとの詳細な設定により、セキュリティおよびシステム管理者は、各ユーザーへの権限の付与を非常に正確に行うことができます。

### <a name="blueprint-responsibility-matrix"></a>ブループリントの責任マトリックス

HITRUST のお客様の責任マトリックスは、お客様の実装と Azure 上に構築されたシステムのセキュリティ制御の文書化をサポートする Excel 文書です。 このブックに、関連する HITRUST の要件が示され、各要件を満たす際に Microsoft とお客様が負う責任が記載されます。

クラウド環境へのセキュリティ制御の実装の共同責任を理解することは、Azure 上にシステムを構築するお客様には不可欠です。 特定のセキュリティ制御の実装は、Microsoft の責任であることも、お客様の責任であることも、または Microsoft とお客様の共同責任であることもあります。 さまざまなクラウド実装が、Microsoft とお客様の責任を共有する方法に影響します。

例については、下記の責任の表を参照してください。

|Azure の責任  |お客様の責任  |
|---------|---------|
|Azure には、そのサービスのプロビジョニング環境に関連する情報保護プログラムの手法とメカニズムの、実装、管理、および監視の責任があります。 | お客様には、Azure サービスへのアクセスと使用に使用される、お客様が管理する資産に対して、情報保護プログラムの手法とメカニズムの実装、構成、管理、および監視の責任があります。 |
|Azure には、そのサービスのプロビジョニング環境に関連するアカウント管理の手法とメカニズムの、実装、構成、管理、および監視の責任があります。 |また、お客様には、デプロイされた Azure 仮想マシンのインスタンスと常駐アプリケーション コンポーネントのアカウント管理の責任もあります。|

これらは、クラウド システムをデプロイする際に考慮すべきさまざまな責任のうちの 2 つの例にすぎません。 HITRUST のお客様の責任マトリックスは、Azure システムの実装で組織の HITRUST のコンプライアンスをサポートするために設計されています。

## <a name="customization"></a>カスタマイズ

インストール後はブループリントをカスタマイズするのが一般的です。 環境をカスタマイズする理由や技法はさまざまです。

ブループリントは、インストール スクリプトを変更することで、インストール前にカスタマイズすることもできます。 これは可能ではありますが、独立した PowerShell スクリプトを作成して、初期インストールの完了後に実行することをお勧めします。 初期インストールが行われた後で、ポータルを使用して新しいサービスをシステムに追加することもできます。

カスタマイズには、次のいずれかまたは複数を含めることができます。

- Machine Learning Studio に新しい実験を追加する
- 関連付けられていないその他のサービスを環境に追加する
- データの取り込みと ML 実験出力を変更して、Azure SQL patientdb データベースとは別のデータ ソースを使用する
- ML 実験に運用データを提供する
- 実験で必要なものに一致するように、取り込まれる独自のデータをクリーンする

インストールのカスタマイズは、Azure ソリューションの操作と同じです。 サービスまたはリソースを追加または削除して、新しい機能を提供することもできます。 ブループリントをカスタマイズする際は、実装が確実に機能し続けるよう、ML パイプライン全体を変更しないよう注意してください。

## <a name="technical-issues"></a>技術的な問題

次の問題により、ブループリントのインストールが失敗したり、望ましくない構成でインストールされたりすることがあります。

### <a name="key-vault"></a>Key Vault

Azure のリソースを削除するとき、キー コンテナーは一意です。 コンテナーは、Azure によって復旧の目的で保持されます。 そのため、インストール スクリプトを実行するたびにインストール スクリプトに別のプレフィックスを渡す必要があります。そうしないと、古いコンテナー名との競合が原因でインストールが失敗します。 キー コンテナー、およびその他のすべてのリソースには、インストール スクリプトに指定するプレフィックスを使用して名前が付けられます。

インストール スクリプトによって作成される Key Vault は、30 日間「論理的な削除」として保持されます。 現在のところポータルからはアクセスできませんが、論理的に削除された [Key Vault は PowerShell から管理可能](/azure/key-vault/key-vault-soft-delete-powershell?WT.mc_id=ms-docs-dastarr)であり、手動で削除することもできます。

### <a name="azure-active-directory"></a>Azure Active Directory

ブループリントは、運用システムではなく空の AAD にインストールすることを強くお勧めします。 ライブの AAD インスタンスにブループリントのアカウントが追加されないよう、新しい AAD インスタンスを作成し、インストール中はそのテナント ID を使用してください。

## <a name="technologies-presented"></a>使用するテクノロジ

- 詳細については、[Azure のヘルス データと AI のブループリント](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)に関するページを参照してください。
- [こちらの GitHub リポジトリ](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)をダウンロード、複製、またはフォークを実行してください。
- [Machine Learning Studio](/azure/machine-learning/studio/?WT.mc_id=ms-docs-dastarr) は、データ サイエンティストが Machine Learning の実験の作成に使用するワークスペースおよびツールです。 これにより、組み込みのアルゴリズム、専用のウィジェット、および Python スクリプトと R スクリプトを使用できます。
- シークレット、証明書、およびその他のプライベート データは、[Azure Key Vault](/azure/key-vault/key-vault-whatis?WT.mc_id=ms-docs-dastarr) に保持されます。
- スクリプト言語 PowerShell は、ブループリントの設定に役立ちます。ただし、必要なコマンドはインストール手順に示されています。
- [Azure AI ギャラリー](https://docs.microsoft.com/en-us/powershell/scripting/powershell-scripting?WT.mc_id=ms-docs-dastarr)には、お客様に役立つ業種別の AI/ML ソリューションのレシピ ボックスがあります。 データ サイエンティストと医療業界のその他のエキスパートによって公開されたいくつかのソリューションがあります。
- [Azure Security Center](/azure/security-center/?WT.mc_id=ms-docs-dastarr) は、アプリケーションの動作、脆弱性、および対応策の手法に関する洞察を提供します。
- [Microsoft Threat Modeling Tool](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr) は、システム環境に対する脅威の計画を立てて予測するために使用します。 ブループリントに含まれている脅威モデルを確認する必要があります。

## <a name="wrapping-up"></a>まとめ

[Azure 医療データ AI のブループリント](/azure/security/blueprints/azure-health?WT.mc_id=ms-docs-dastarr)は完全な ML ソリューションであり、技術者が、Azure とシステムが医療業界の規制要件に準拠していることを確認する方法を理解するための、学習ツールとして使用できます。 これは、運用システムを Azure Machine Learning Studio を中心として使用する出発点として使用することもできます。

[ブループリントをダウンロード](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/deployment.md)して、数日や数週間ではなく、数時間での実装を開始してください。 インストールに関する問題やブループリントに関する質問がある場合は、[よくあるご質問のページ](https://github.com/Azure/Health-Data-and-AI-Blueprint/blob/master/faq.md)にアクセスしてください。

サポート資料をダウンロードして、インストールと ML 実験以外のブループリント実装についての理解を深めてください。 この資料には次のものが含まれます。

1. [HITRUST のお客様の責任マトリックス](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=eab85244-b9ab-490a-9e2a-611153f7d3af&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
2. [包括的な脅威モデル](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=01828de2-9555-4bac-a2a0-44e9ed2eeeaf&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
3. [HITRUST ヘルス データおよび AI のレビューのホワイトペーパー](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=ffc32e44-665e-46c5-b753-163d55a17d27&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics&WT.mc_id=ms-docs-dastarr)
4. [HIPAA ヘルス データデータおよび AI のレビュー](https://servicetrust.microsoft.com/ViewPage/HIPAABlueprint?command=Download&downloadType=Document&downloadId=d5ce675c-3e83-45db-98a6-ae77fc439436&docTab=d7c399a0-2b92-11e8-9910-13dc07d708f7_Data_Analytics?WT.mc_id=ms-docs-dastarr)

ブループリントを使用するのが学習目的であれ、組織の AI/ML ソリューションのためであれ、医療に焦点を当てた Azure での AI/ML の使用の出発点となります。