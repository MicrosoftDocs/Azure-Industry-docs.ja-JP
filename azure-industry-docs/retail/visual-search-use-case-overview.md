---
title: CosmosDB を使用した小売業における画像検索の入門
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: この記事では、オンプレミスから Azure に e コマース インフラストラクチャを移行する各フェーズについて説明します。
ms.openlocfilehash: b43ea305e11ac32da58e4d0521d79f90d5c23d85
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77053149"
---
# <a name="visual-search-overview"></a>画像検索の概要

## <a name="executive-summary"></a>概要

人工知能により、今日の小売業が変革される可能性があります。 小売業者は AI に支えられたカスタマー エクスペリエンス アーキテクチャを開発するようになると考えてよいでしょう。 AI で強化されたプラットフォームでは、ハイパー パーソナル化による収益の増加が期待されます。 デジタル コマースは、顧客の期待、嗜好、行動を高め続けています。 リアルタイム エンゲージメント、関連性のあるレコメンデーション、ハイパー パーソナル化などのニーズは、ボタンのクリックによるスピードと利便性の向上を促しています。 自然音声や視覚などを通じてアプリケーションのインテリジェンスを実現することで、小売業において、顧客の従来の買物の方法を覆し、価値を増大させる改善が可能になります。

このドキュメントでは、**画像検索**の AI の概念について説明し、その実装に関する重要な考慮事項を示します。 ワークフローの例を示し、各段階を関連する Azure テクノロジにマップします。 概念は、顧客がエクスペリエンスの意図に応じて、モバイル デバイスで撮影した画像やインターネット上にある画像を利用して、関連する項目や類似する項目の検索を実行できることに基づいています。 そのため、画像検索では、テキスト入力ではなく、複数のメタデータ ポイントを含む画像を使用することで速度が向上し、適切な項目をすばやく見つけ出すことができます。

## <a name="visual-search-engines"></a>画像検索エンジン

画像検索エンジンは、画像を入力として使用して情報を取得しますが、それだけではなく、多くの場合、出力としても使用します。

画像検索エンジンは小売業界で普及が進んでいますが、これには次のような十分な理由があります。

- 2017 年に発行された [Emarketer](https://www.emarketer.com/Report/Visual-Commerce-2017-How-Image-Recognition-Augmentation-Changing-Retail/2002059) のレポートによると、インターネット ユーザーの約 75% が、購入前に製品の写真やビデオを検索しています。
- [Slyce](https://slyce.it/wp-content/uploads/2015/11/Visual_Search_Technology_and_Market.pdf) (画像検索会社) の 2015 年のレポートによると、コンシューマーの 74% が、テキスト検索は非効率的であると思っています。

そのため、[Markets &amp; Markets](https://www.marketsandmarkets.com/PressReleases/image-recognition.asp) の調査によると、画像認識市場は 2019 年までに 250 億ドル以上の規模になると予想されます。

このテクノロジは、主要 e コマース ブランドで既に採用されています。これらのブランドは、このテクノロジの開発にも大きく貢献しています。 最も有名と思われる早期導入者は次のとおりです。

- アプリで Image Search および "Find It on eBay" ツールを提供する eBay (現時点ではモバイル エクスペリエンスのみ)。
- 画像検索ツールのレンズ (Lens) を提供する Pinterest。
- Bing Visual Search を提供する Microsoft。

## <a name="adopt-and-adapt"></a>採用と適応

画像検索から利益を得るために膨大な処理能力は不要です。 画像カタログを所有する企業は、Azure サービスに組み込まれた Microsoft の AI の専門知識を活用できます。

[Bing Visual Search](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/?WT.mc_id=vsearchgio-article-gmarchet) API を使用すると、画像からコンテキスト情報を抽出し、たとえば、家財道具、ファッション、数種類の製品などを識別できます。

この API では、独自のカタログ、関連するショッピング ソースの製品、関連検索から、見た目が似ている画像を返すこともできます。 興味深いものの、自社がこれらのソースの 1 つでない場合、これはあまり役に立ちません。

Bing には次の機能も用意されています。

- 画像に含まれるオブジェクトや概念を探索できるタグ。
- 画像内の関心領域 (衣類、家具など) を示す境界ボックス。

その情報を取得して、検索空間を会社の製品カタログに限定し、関心領域の対象となるカテゴリのオブジェクトなどに限定することで、時間を大幅に短縮できます。

## <a name="implement-your-own"></a>独自の画像検索の実装

画像検索を実装するときに考慮すべき重要な要素がいくつかあります。

- 画像の取り込みとフィルター処理
- 保存と取得の手法
- 特徴付け、エンコード、または "ハッシュ"
- 類似性測度または距離とランク付け

 ![](./assets/visual-search-use-case-overview/visual-search-pipeline.png)

*図 1: 画像検索パイプラインの例*

### <a name="sourcing-the-pictures"></a>画像の調達

画像カタログを所有していない場合、Fashion [MNIST](https://www.kaggle.com/zalando-research/fashionmnist) や Deep [Fashion](http://mmlab.ie.cuhk.edu.hk/projects/DeepFashion.html) などの公開されているデータセットでアルゴリズムをトレーニングすることが必要な場合があります。 これらのデータセットには複数の製品カテゴリが含まれており、画像分類および検索アルゴリズムのベンチマークに一般に使用されます。

 ![](./assets/visual-search-use-case-overview/deep-fashion-dataset.png)

*図 2: Deep Fashion データセットの例*

### <a name="filtering-the-images"></a>画像のフィルター処理

前述のようなベンチマーク データセットのほとんどは、既に前処理されています。

独自のデータセットを構築する場合、少なくとも、画像をすべて同じサイズにすることが望まれます。ほとんどの場合、サイズはモデルのトレーニングの対象となった入力によって決まります。

多くの場合、画像の明度を正規化することも推奨されます。 検索の詳細レベルによっては、色も冗長情報になる可能性があるため、黒と白に減らすと処理時間が短縮されます。

最後に、画像データセットは、それが表すさまざまなクラス間でバランスを取る必要があります。

### <a name="image-database"></a>画像データベース

データ レイヤーは、アーキテクチャの特に繊細なコンポーネントです。 データ レイヤーには以下が含まれます。

- イメージ
- 画像に関するメタデータ (サイズ、タグ、製品 SKU、説明)
- 機械学習モデルによって生成されたデータ (画像あたり 4096 要素の数値ベクトルなど)

さまざまなソースから画像を取得したり、最適なパフォーマンスを確保するために複数の機械学習モデルを使用したりすると、データの構造が変わります。 そのため、半構造化データを処理することができ、固定スキーマのないテクノロジまたは組み合わせを選択することが重要です。

また、最小数の有用なデータ ポイント (画像識別子またはキー、製品 SKU、説明、タグ フィールドなど) が必要になる場合もあります。

[Azure CosmosDB](https://azure.microsoft.com/services/cosmos-db/?WT.mc_id=vsearchgio-article-gmarchet) は、その上に構築されたアプリケーションに必要な柔軟性とさまざまなアクセス メカニズムを提供します (これは、カタログ検索に役立ちます)。 ただし、最適な価格/パフォーマンス比を実現するには注意が必要です。 CosmosDB ではドキュメントの添付ファイルを保存できますが、アカウントごとに上限があり、コストのかかる問題になる可能性があります。 実際の画像ファイルは BLOB に保存し、それらへのリンクをデータベースに挿入するのが一般的です。 CosmosDB の場合、これは、その画像に関連付けられたカタログ プロパティ (SKU、タグなど) を含むドキュメントと、(Azure Blob Storage や OneDrive などにある) 画像ファイルの URL を含む添付ファイルを作成することを意味します。

 ![](./assets/visual-search-use-case-overview/cosmosdb-data-model.png)

*図 3: CosmosDB 階層型リソース モデル*

Cosmos DB のグローバル配布を利用する場合、ドキュメントと添付ファイルはレプリケートされますが、リンクされたファイルはレプリケートされません。 これらのファイルには、コンテンツ配信ネットワークを使用することを検討してください。

適用可能な他のテクノロジとして、Azure SQL Database (固定スキーマを許容できる場合) と BLOB の組み合わせ、または Azure Table と BLOB の組み合わせ (安価で高速の保存と取得を実現する場合) があります。

### <a name="feature-extraction-amp-encoding"></a>特徴抽出とエンコード

エンコード プロセスでは、データベース内の画像から顕著な特徴を抽出し、数千の成分を持つことができる疎な "特徴" ベクトル (多数のゼロを持つベクトル) に各特徴をマップします。 このベクトルは、画像を特徴付けるエッジや形状などの特徴の数値表現であり、コードに似ています。

通常、特徴抽出手法では "_転移学習メカニズム_" を使用します。 これは、事前トレーニングされたニューラル ネットワークを選択し、それを使って各画像を実行して、生成された特徴ベクトルを画像データベースに保存するときに発生します。 このようにして、誰かがトレーニングしたネットワークから学習を "転移" します。 Microsoft は、事前トレーニングされたネットワークをいくつか開発し、公開しています。これらのネットワークは、画像認識タスク ([ResNet50](https://www.kaggle.com/keras/resnet50) など) に広く使用されています。

ニューラル ネットワークによって特徴ベクトルの長さと疎の度合いが異なるため、メモリとストレージの要件が異なります。

また、ネットワークによって適用できるカテゴリがそれぞれ異なる場合があるため、画像検索の実装では、実際にはさまざまなサイズの特徴ベクトルが生成されます。

事前トレーニングされたニューラル ネットワークは比較的使いやすいですが、独自の画像カタログでトレーニングされたカスタム モデルほど効率的ではない可能性があります。 これらの事前トレーニングされたネットワークは、通常、特定の画像コレクションの検索ではなく、ベンチマーク データセットの分類用に設計されています。

カテゴリの予測と密 (より小さい、疎ではない) ベクトルの両方を生成するようにそれらを修正し、再トレーニングすることができます。これは、検索空間を制限し、メモリとストレージの要件を削減するのに非常に役立ちます。 バイナリ ベクトルを使用できます。多くの場合、これは "[セマンティック ハッシュ](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf)" (ドキュメントのエンコードと取得の手法から派生した用語) と呼ばれます。 バイナリ表現により、後続の計算が簡素化されます。

 ![](./assets/visual-search-use-case-overview/resnet-modifications.png)

*図 4: 画像検索のための ResNet の変更 - F. Yang 他 (2017 年)*

事前トレーニングされたモデルを選択するか、独自のモデルを開発するかに関係なく、モデル自体の特徴付けやトレーニングを実行する場所を決定する必要があります。

Azure には、複数のオプション (VM、Azure Batch、[Batch AI](https://azure.microsoft.com/services/batch-ai/?WT.mc_id=vsearchgio-article-gmarchet)、Databricks クラスター) が用意されています。 ただし、どの場合も、GPU を使用することによって、最適な価格/パフォーマンス比が得られます。

最近、Microsoft はわずかな GPU コストで高速計算を実現する FPGA の提供も発表しました ([Project Brainwave](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/?WT.mc_id=vsearchgio-article-gmarchet))。 ただし、このドキュメントの執筆時点では、このオファリングは特定のネットワーク アーキテクチャに限定されているので、パフォーマンスを綿密に評価する必要があります。

### <a name="similarity-measure-or-distance"></a>類似性測度または距離

画像を特徴ベクトル空間内で表現する場合、類似性の検出は、そのような空間内にあるポイント間の距離測度の定義の問題になります。 距離が定義されたら、類似画像のクラスターを計算したり、類似性マトリックスを定義したりできます。 選択した距離メトリックによって、結果が異なる場合があります。 理解しやすいものとして、実数ベクトルに対する最も一般的なユークリッド距離測度があります。これは距離の大きさを取得します。 ただし、計算の観点から言うと、やや非効率的です。

[コサイン](https://en.wikipedia.org/wiki/Cosine_similarity)距離は、その大きさではなく、ベクトルの向きを取得するためによく使用されます。

バイナリ表現に対する[ハミング](https://en.wikipedia.org/wiki/Hamming_distance)距離などの代替手段では、ある程度の正確さが損なわれる代わりに、効率と速度が得られます。

ベクトルのサイズと距離測度の組み合わせによって、検索の計算負荷とメモリ負荷がどのくらいになるかが決まります。

### <a name="search-amp-ranking"></a>検索とランク付け

類似性が定義されたら、入力として渡されたものに最も近い N 個の項目を取得し、識別子のリストを返す効率的な方法を考案する必要があります。 これは "画像ランク付け" とも呼ばれます。 大規模なデータセットでは、すべての距離の計算に膨大な時間がかかるため、近似最近傍アルゴリズムを使用します。 これらのアルゴリズムのオープン ソース ライブラリがいくつか存在するので、ゼロからコーディングする必要はありません。

最後に、メモリと計算の要件によって、トレーニングされたモデルの展開テクノロジと高可用性が決まります。 通常、検索空間はパーティション分割され、ランク付けアルゴリズムの複数のインスタンスが並列実行されます。 スケーラビリティと可用性を実現する 1 つのオプションとして、[Azure Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/?WT.mc_id=vsearchgio-article-gmarchet) クラスターがあります。 その場合、ランク付けモデルを複数のコンテナー (各検索空間のパーティションを処理) および複数のノード (高可用性の確保) に展開することをお勧めします。

## <a name="next-steps"></a>次のステップ

画像検索の実装は複雑である必要はありません。 Bing を使用することも、Azure サービスを使用して独自に構築することもでき、Microsoft の AI 研究と AI ツールからメリットが得られます。

### <a name="trial"></a>試用版

- [Visual Search API テスト コンソール](https://dev.cognitive.microsoft.com/docs/services/878c38e705b84442845e22c7bff8c9ac)をお試しください。

### <a name="develop"></a>開発

- カスタマイズされたサービスの作成を開始するには、[Bing Visual Search API の概要](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview/?WT.mc_id=vsearchgio-article-gmarchet)をご覧ください。
- 最初の要求を作成するには、クイック スタート ([C#](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/csharp) | [Java](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/java) | [node.js](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/nodejs) | [Python](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/quickstarts/python)) をご覧ください。
- [Visual Search API リファレンス](https://aka.ms/bingvisualsearchreferencedoc)を理解します。

### <a name="background"></a>バックグラウンド

- [ディープ ラーニングによる画像のセグメント化](https://www.microsoft.com/developerblog/2018/04/18/deep-learning-image-segmentation-for-ecommerce-catalogue-visual-search/?WT.mc_id=vsearchgio-article-gmarchet): 画像をバックグラウンドから分離するプロセスについて説明する Microsoft の論文
- [Visual Search at eBay (eBay での画像検索)](https://arxiv.org/abs/1706.03154): コーネル大学による研究
- [Visual Discovery at Pinterest (Pinterest での画像検索)](https://arxiv.org/abs/1702.04680): コーネル大学による研究
- [Semantic Hashing (セマンティック ハッシュ)](https://www.cs.utoronto.ca/~rsalakhu/papers/semantic_final.pdf): トロント大学による研究

_この記事は、Giovanni Marchetti と Mariya Zorotovich によって作成されました。_