---
title: Azure ML と分析を使用したコンシューマー ブランドの SKU の最適化
author: scseely
ms.author: scseely
ms.date: 11/20/2019
ms.topic: article
ms.service: industry
description: 小売業界の品揃えの最適化。 AI および ML からの分析情報を使用した SKU の最適化。
ms.openlocfilehash: 22411776e830bb3c71f8c1277b30ec4331a3ef17
ms.sourcegitcommit: 3b175d73a82160c4cacec1ce00c6d804a93c765d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054373"
---
# <a name="sku-optimization-for-consumer-brands-solution-guide"></a>コンシューマー ブランドの SKU の最適化のソリューション ガイド

## <a name="introduction"></a>はじめに

小売業者とコンシューマー ブランドは、コンシューマーがマーケットプレース内で購入しようとしている適切な製品とサービスを確保することに重点を置いています。 また、売上を最大限に伸ばすことを検討するときに、ショッピング体験の主要な部分は製品 (または製品の組み合わせ) であるといえるでしょう。 提供の可能性 (在庫) は、常にコンシューマー ブランドにとっての関心事です。

製品の在庫 (SKU の品揃えとも呼ばれます) は、サプライと物流のバリュー チェーンにまたがる複雑なトピックです。 このドキュメントでは、消費財の観点から、SKU の品揃えを最適化して収益を最大化することの問題について具体的に説明します。 SKU の品揃えの最適化のパズルは、次の質問に回答するアルゴリズムを開発することで解決できます。

- 特定の市場またはストアで最適に実行されているのはどの SKU か。 
- そのパフォーマンスに基づいて特定の市場またはストアに割り当てるべき SKU はどれか。
- より高いパフォーマンスの SKU に置き換える必要があるパフォーマンスが低い SKU はどれか。
- コンシューマーおよび市場区分について引き出せるその他の分析情報は何か。

## <a name="automate-decision-making"></a>意思決定の自動化

これまで、コンシューマー ブランドでは、SKU ポートフォリオ内の SKU の数を増やすことでコンシューマーの需要に対処していました。 SKU の数が増え、競合が増加すると、収益の 90% が、ポートフォリオ内の製品 SKU の 10% のみで構成されていると推定されます。 通常は、収益の 80% が SKU の 20% からもたらされます。 この比率は、収益性を向上させるための候補です。 

従来の静的なレポート作成方法では、履歴データを活用していますが、これでは分析情報が制限されます。 よくても、意思決定と実装は手動で行われています。 これは人が介入し、処理時間がかかることを意味します。 人工知能 (AI) とクラウド コンピューティングの進化により、さまざまな選択肢と予測を提供する高度な分析を使用することが可能になりました。 この種の自動化により、結果と顧客への対応速度が改善されます。

## <a name="sku-assortment-optimization"></a>SKU の品揃えの最適化

SKU の品揃えソリューションでは、売上データを意味のある詳細な比較に分割することで、何百万もの SKU を処理する必要があります。 ソリューションの目標は、高度な分析を使用して製品の品揃えを調整することで、すべての直販店やストアで売上を最大化することです。 2 つ目の目標は、在庫切れをなくし、品揃えを改善することです。 財政上の目標は、売上を 5 から 10% 増やすことです。 その目標を達成するため、分析情報は次のことを可能にします。

- SKU ポートフォリオのパフォーマンスを理解して、パフォーマンスの低い SKU を管理する。
- 在庫切れを減らすために SKU の配布を最適化する。
- 新しい SKU が短期戦略および長期戦略をどのようにサポートしているかを理解する。
- 既存のデータから反復可能でスケーラブル、かつ実用的な分析情報を作成する。

## <a name="descriptive-analytics"></a>記述的分析

記述的モデルでは、データ ポイントを集計し、製品の売上に影響を及ぼす可能性がある要因間のリレーションシップを調べます。 この情報は、場所、天気、国勢調査データなど、一部の外部データポイントで補強される場合があります。視覚エフェクトを使用すると、データを解釈して洞察を得ることができます。 ただし、データの更新頻度にもよりますが、この方法では、前の販売サイクルで何が起こったか、または現期間で何が起こっているかを理解することしかできません。

たとえば、ある期間でパフォーマンスが最も高かった SKU と最も低かった SKU を把握するこのケースでは、従来のデータ ウェアハウスとレポートのアプローチで十分です。

次の図は、売上の履歴データの一般的なレポートを示しています。 これには、条件を選択して結果をフィルターするためのチェック ボックスを備えた複数のブロックが使用されています。 中央には、時間の経過と共に売上を表示する 2 つの棒グラフが示されています。 最初のグラフでは、週単位の平均売上が示されており、2 つ目のグラフには、週単位の数量が示されています。

 ![売上の履歴データを示すダッシュボードの例。](assets/sku-optimization-solution-guide/sku-max-model.png)
  
## <a name="predictive-analytics"></a>予測分析

履歴レポートは、何が起こっているかを把握するのに役立ちます。 最終的には、発生する可能性があることを予測します。 過去の情報は、そうした目的に役立つ場合があります。 たとえば、季節的傾向を特定できます。 しかし、新製品の導入をモデル化するための "What-if" シナリオなどをカバーすることはできません。 そのためには、顧客の行動のモデル化に焦点を移す必要があります。これが売上を左右する最終的な要因だからです。

### <a name="an-in-depth-look-at-the-problem-choice-models"></a>問題の詳細: モデルの選択

まず、探しているものと持っているデータを定義することから始めます。

品揃えの最適化は、予想収益を最大化する、販売する製品のサブセットを見つけることを意味します。 これが探しているものです。

**トランザクション データ**は、財務目的で定期的に収集されます。 

**品揃えデータ**には、潜在的に SKU に関連するすべてのものが含まれます。必要なものの例を次に示します。 

- SKU の数
- SKU の説明
- 割り当てられた数量
- SKU と購入した数量
- イベント (購入など) のタイムスタンプ
- SKU 価格
- POS の SKU 価格
- 任意の時点でのすべての SKU の在庫レベル

残念ながら、こうしたデータはトランザクション データほど確実に収集されません。 

このドキュメントでは、わかりやすくするため、外部要因は考慮せず、トランザクション データと SKU データのみを考慮します。

その場合でも、n 個の製品がある場合、2n の考えられる品揃えがあることに注意してください。 これにより、最適化の問題がコンピューターに高い負荷のかかるプロセスになります。 製品数が多い場合には、考えられるすべての組み合わせを評価することは実用的ではありません。 そのため、通常は、品揃えをカテゴリ (穀物など)、場所、およびその他の条件で分割して、変数の数を減らします。 最適化モデルは、実行可能なサブセットにするために順列の数を "削る" ことを試みます。

問題の最も重要な点は、効果的に**コンシューマーの行動をモデリング**できるかどうかです。 コンシューマーに提示される製品が、コンシューマーが購入したいものと一致するのが理想です。

コンシューマーの選択を予測する数学的モデルは、数十年にわたって開発されています。 最適な実装テクノロジを最終的に決定するのは、モデルの選択です。そのため、ここではその概要を説明し、いくつかの考慮事項を提供します。

## <a name="parametric-models"></a>パラメトリック モデル

パラメトリック モデルは、関数と有限のパラメーターを使用して顧客の行動を見積もります。 データに最適なパラメーターのセットを自由に見積もります。 最も古く最もよく知られているものの 1 つが、[多項ロジスティック回帰](https://en.wikipedia.org/wiki/Multinomial_logistic_regression) (MNL、多クラス ロジット、ソフトマックス回帰とも呼ばれます) です。 これは分類の問題で考えられる複数の結果の確率の計算に使用されます。 このケースでは、MNL を使用して計算できます。

- 品揃え (a) 内のそのカテゴリの品目のセットが顧客 (v) にとって既知の有用性を持つ場合、コンシューマー (c) が特定の時点 (t) で品目 (i) を選ぶ確率。 

品目の有用性がその機能の関数であることをも想定しています。 有用性の測定には外部情報も含めることができます (雨が降ると傘の有用性が高まるなど)。

パラメーターの見積もりと結果の評価における MNL の取り扱いやすさにより、Microsoft ではその他のモデルのベンチマークとして MNL をしばしば使用しています。 つまり、MNL よりも悪い場合は、そのアルゴリズムは役に立たないということです。
いくつかのモデルは MNL から派生されていますが、それらについてはこのドキュメントでは取り扱いません。 

R および Python プログラミング言語用のライブラリがあります。 R には、glm (および派生物) を使用できます。  Python には、[scikit-learn](http://scikit-learn.org/stable/)、[biogeme](http://biogeme.epfl.ch/)、[larch](https://pypi.org/project/larch/) があります。 これらのライブラリでは、MNL の問題を指定するツールと、さまざまなプラットフォームでソリューションを検索する並列ソルバーが提供されています。

最近では、別の方法では解決困難になる可能性がある多くのパラメーターを持つ複雑なモデルを計算するため、GPU に MNL モデルを実装することが提案されています。

大きな多クラスの問題では、ソフトマックス出力層を持つニューラル ネットワークが効果的に使用されています。 これらのネットワークでは、多くの異なる結果の確率分布を表す出力のベクターが生成されます。 これらは他の実装と比べるとトレーニングに時間がかかりますが、多くのクラスとパラメーターを処理することができます。 

## <a name="non-parametric-models"></a>ノンパラメトリック モデル

MNL はその人気に関わらず、いくつかの重要な想定をその有用性を制限する可能性のある人間の行動に置いています。 具体的には、ある人が 2 つの選択肢から選択する相対的な確率が、その後のセットで導入された追加の代替策とは独立していることを想定しています。 多くの場合、これは実用的ではありません。

たとえば、製品 "A" と "B" を同じくらい気に入っている場合、一方を選択する確率は 50% です。 製品 "C" を混ぜてみましょう。 "A" を選択する確率はまだ 50% かもしれませんが、優先順位が "B" と "C" で分割され、確率が 25% ずつになります。 相対的確率が変更されました。

また、MNL やそこから派生したものに、在庫切れによる代替品や品揃えの多様性を考慮する簡単な方法はありません (つまり、明確なアイデアがない場合は、在庫の中からランダムに品目を選択します)。
ノンパラメトリック モデルは、代替品を考慮し、顧客の行動に課される制約を減らすために設計されています。 

モデルには、コンシューマーが品揃え内の製品に対して厳密な優先順位を表す "順位付け" の概念が導入されています。 したがって、コンシューマーの購買行動は、製品を優先順位の降順で並べ替えることでモデル化できます。 

品揃えの最適化の問題は、収益の最大化として表現できます。

<center><img style="float: center;" src="assets/sku-optimization-solution-guide/assortment-optimization-problem.png" width="150"/></center>
 
- $r_i$ は製品 i の収益を表します。 
- 製品 i が順位付け k で選択される場合、$y_i^k$ は 1 で、それ以外の場合は 0 です。  
- $\lambda_k$ は、顧客が順位付け k に従って選択する確率です。
- 製品が品揃えに含まれる場合、$x_i$ は 1 で、それ以外の場合は 0 です。
- K は順位付けの数です。 
- n は製品の数です。

制約の対象:

- 各順位付けに対して 1 つだけ選択できる
- 順位付け k の下では、製品 i を選択できるのは、それが品揃えに含まれている場合のみ
- 製品 i が品揃えに含まれている場合、順位付け k でそれより優先順位が低い選択肢を選択することはできない 
- 購入なしは選択肢の 1 つであるため、順位付けで優先順位が低い選択肢を選択することはできない

このような公式化では、問題は[混合整数最適化](https://en.wikipedia.org/wiki/Integer_programming)と見なすことができます。

n 個の製品がある場合、選択しないという選択肢を含めた考えられる順位付けの最大数は、階乗 (n+1)! であることについて考えてみましょう。

公式化における制約により考えられる選択肢の比較的効率的な排除 (たとえば、最も優先順位の高い選択肢が選択されて、1 に設定され、残りが 0 に設定される) が可能になりますが、選択可能な代替品の数を考えると、実装のスケーラビリティが重要になることが想像できます。

### <a name="the-importance-of-data"></a>データの重要度

売上データがすぐに使用できることは既に説明しました。 それらを使用して、品揃えの最適化モデルに情報を提供します。 具体的には、確率分布 λ を見つけます。

POS システムからの売上データは、タイムスタンプ付きのトランザクションと、その時と場所で顧客に表示される製品のセットで構成されます。 これらから、実際の売上のベクターを構築できます。この要素は vi で、m は既定の品揃え $S_m$ で品目 i の顧客への販売確率を表します。

マトリックスを構築することもできます。

<center><img style="float: center;" src="assets/sku-optimization-solution-guide/matrix-construction.png" width="300"/></center>

売上データを仮定して確率分布 λ を見つけることは、別の最適化の問題になります。 売上の予測ミスを最小限に抑えるため、ベクター λ を見つけます。

$$min_\lambda|\Lambda\lambda - v|$$

計算は回帰として表現することもできるため、多変量のデシジョン ツリーのようなモデルを使用することができます。 

## <a name="implementation-details"></a>実装の詳細

上記の公式化から差し引くことができるため、最適化モデルはデータドリブンと数値計算の両方になります。

Neal Analytics などの Microsoft パートナーは、これらの条件を満たす堅牢なアーキテクチャを開発しています。 [最大 SKU](https://appsource.microsoft.com/product/web-apps/neal_analytics.8066ad01-1e61-40cd-bd33-9b86c65fa73a?tab=Overview?WT.mc_id=invopt-article-gmarchet) に関するページを参照してください。 例としてこれらのアーキテクチャを使用し、いくつかの考慮事項を提供します。

- まず、これらのアーキテクチャは、(1) モデルをフィードするために堅牢でスケーラブルなデータ パイプラインと、(2) それらを実行するために堅牢でスケーラブルな実行インフラストラクチャに依存しています。
- 次に、ダッシュボードを使用してプランナーが簡単に結果を利用できます。

図 2 は、サンプル アーキテクチャを示しています。 これには、4 つの主要なブロック (キャプチャ、プロセス、モデル、運用化) が含まれています。 各ブロックには、主要なプロセスが含まれています。 キャプチャには "データの前処理" が含まれ、プロセスには "データの格納" 機能が含まれ、モデルには "機械学習モデルのトレーニング" 機能が含まれ、運用化には "データの格納" とレポート オプション (ダッシュボードなど) が含まれています。

![キャプチャ、プロセス、モデル、運用化の 4 つの部分で構成されているアーキテクチャ。](assets/sku-optimization-solution-guide/architecture-sku-optimization.png)<center><font size="1">_図 2: SKU 最適化のためのアーキテクチャ: Neal Analytics 提供_</font></center>

## <a name="the-data-pipeline"></a>データ パイプライン

このアーキテクチャでは、モデルのトレーニングと操作のためにデータ パイプラインを確立することの重要性が強調されています。 統合ワークフローを設計して実行できるマネージド ETL (抽出、変換、読み込み) サービスの [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction?WT.mc_id=invopt-article-gmarchet) を使用して、パイプライン内のアクティビティを調整します。

Azure Data Factory は、データセットを使用または生成する (またはその両方) "アクティビティ" と呼ばれるコンポーネントを持つマネージド サービスです。

アクティビティは以下に分割できます。

- データ移動 (たとえば、移動元から移動先にコピーする)
- データ変換 (たとえば、SQL クエリを使用した集計や、ストアド プロシージャの実行など)

アクティビティのセットをリンクするワークフローは、データ ファクトリ サービスによってスケジュール、監視、管理することができます。 完全なワークフローは、"パイプライン" と呼ばれています。

キャプチャ フェーズでは、(Data Factory に組み込まれている) コピー アクティビティを利用して、さまざまなソース (オンプレミスとクラウドの両方) からデータを Azure SQL Data Warehouse に転送できます。 そのやり方の例については、次のドキュメントに記載されています。

- [Azure SQL DW をコピー先またはコピー元としてデータをコピーする](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse?WT.mc_id=invopt-article-gmarchet)
- [Azure SQL DW へのデータの読み込み](https://docs.microsoft.com/azure/data-factory/load-azure-sql-data-warehouse?WT.mc_id=invopt-article-gmarchet)

次の図は、パイプラインの定義を示しています。 これは同じサイズの連続する 3 つのブロックで構成されています。 最初の 2 つはデータセットとアクティビティで、データ フローを示す矢印で接続されています。 3 番目のブロックは、"パイプライン" というラベルが付いており、カプセル化を示すため、最初の 2 つを単にポイントしています。 

 ![Azure Data Factory の概念: アクティビティのパイプラインによって消費されるデータセット。](assets/sku-optimization-solution-guide/azure-data-factory.png)<center><font size="1">_図 3: Azure Data Factory の基本的な概念_</font></center>

Neal Analytics のソリューションで使用されているデータ形式の例は、Microsoft の Appsource ページで確認できます。 このソリューションには、次のデータセットが含まれています。

- ストアと SKU の組み合わせごとの売上履歴データ
- ストアとコンシューマーのレコード
- SKU コードと説明
- 製品の機能をキャプチャする SKU の属性 (サイズ、素材など)。 これらは通常、製品のバリアントを区別するためにパラメトリック モデルで使用されます。

データ ソースが特定の形式で表されない場合、Data Factory によって一連の変換アクティビティが提供されます。 

プロセスのフェーズでは、SQL Data Warehouse がメイン ストレージ エンジンです。 そのため、このような変換アクティビティを、パイプラインの一部として自動的に呼び出すことができる SQL ストアド プロシージャとして表すこともできます。 詳細な手順は、次のドキュメントに記載されています。

- [SQL ストアド プロシージャを使用したデータの変換](https://docs.microsoft.com/azure/data-factory/transform-data-using-stored-procedure?WT.mc_id=invopt-article-gmarchet)

Data Factory により、SQL Data Warehouse および SQL ストアド プロシージャに制限されることはありません。 実際には、さまざまなプラットフォームと統合します。 たとえば、変換の代わりに、Databricks の使用を選択して、Python スクリプトを実行することもできます。 1 つのプラットフォームをストレージと変換、および次の "モデル" フェーズで機械学習アルゴリズムのトレーニングに使用できることは利点です。

## <a name="training-the-ml-algorithm"></a>ML アルゴリズムのトレーニング

パラメトリック モデルおよびノンパラメトリック モデルを実装するのに役立ついくつかのツールがあります。 スケーラビリティとパフォーマンスの要件に応じて選択します。

[Azure ML Studio](https://studio.azureml.net/?WT.mc_id=invopt-article-gmarchet) は、プロトタイプ作成に最適なツールです。 コード モジュール (R または Python) またはグラフィック環境で定義済みの ML コンポーネント (多クラス分類子、ブースト デシジョン ツリー回帰など) を使用して、トレーニング ワークフローを構築して実行するための簡単な方法を提供します。 また、REST インターフェイスを生成して、数回のクリックでさらに使用するための Web サービスとしてトレーニング済みモデルを公開することもできます。 

ただし、これが処理できるデータ サイズは (現時点では) 10 GB に制限されており、各コンポーネントで使用できるコアの数も (現時点では) 2 個に制限されています。

次の図は、使用中の ML Studio の例を示しています。 これは機械学習の実験をグラフィカルに表示したものです。 図には、ブロックのいくつかのグループが示されています。 ブロックの各セットは、実験の何らかの段階を表しており、データの入力と出力を示すため、各ブロックが 1 つまたは複数のブロックに接続されています。

![使用中の Machine Learning Studio の例。](assets/sku-optimization-solution-guide/ml-training-pipeline-example.png)<center><font size="1">_図 4: R と構築済みのコンポーネントを使用した ML トレーニング パイプラインの例_</font></center>

さらにスケールする必要があるが、Microsoft の一般的な機械学習アルゴリズム (多項ロジスティック回帰など) の高速な並列実装の一部を引き続き使用したい場合は、Azure Data Science Virtual Machine で実行されている Microsoft ML Server を検討することもできます。

非常に大きなデータ サイズ (TB) の場合、ストレージと計算要素が次のことができるプラットフォームを選択することをお勧めします。

- モデルをトレーニングしていないときにコストを制限するために個別にスケールできる。
- 複数のコアに計算を分散できる。
- データ移動を制限するため、ストレージの "近く" で計算を実行できる。

Azure [HDInsight](https://azure.microsoft.com/services/hdinsight/?WT.mc_id=invopt-article-gmarchet) と [Databricks](https://azure.microsoft.com/services/databricks/?WT.mc_id=invopt-article-gmarchet) はどちらもこれらの要件を満たしています。 また、どちらも Azure Data Factory エディター内でサポートされている実行プラットフォームです。 ワークフローでいずれかを統合するのは比較的簡単です。

ML Server とそのライブラリは、HDInsight の一番上に配置できますが、プラットフォーム機能を最大限に活用するため、SparkML、Python で Microsoft ML Spark ライブラリ、または TFoCS、Spark-LP、SolveDF などのその他のリニア プログラミング ソルバーを使用して、最適な ML アルゴリズムを実装することもできます。 

トレーニング プロセスの開始すると、Data Factory ワークフローから適切な pySpark スクリプトやノートブックを呼び出せるかが問題になります。 これは、グラフィカル エディターで完全にサポートされます。 詳細については、「[Azure Data Factory で Databricks Notebook アクティビティを使用して Databricks ノートブックを実行する](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook?WT.mc_id=invopt-article-gmarchet)」を参照してください。

次の図は、Azure portal からアクセスしたときの Data Factory ユーザー インターフェイスを示しています。 これには、ワークフロー内のさまざまなプロセスのブロックが含まれています。 

![Databricks Notebook アクティビティを示す Data Factory インターフェイス。](assets/sku-optimization-solution-guide/data-factory-pipeline-databricks.png)<center><font size="1">_図 5: Databricks Notebook アクティビティを使用した Data Factory パイプラインの例_</font></center>

また、Microsoft の[インベントリの最適化ソリューション](https://gallery.azure.ai/Solution/Inventory-Optimization-3?WT.mc_id=invopt-article-gmarchet)では、[Azure Batch](https://azure.microsoft.com/services/batch/?WT.mc_id=invopt-article-gmarchet) を通じてスケールされるソルバーのコンテナーベースの実装を提案しています。 [pyomo](http://www.pyomo.org/about/) などのスペシャリスト最適化ライブラリでは、最適化の問題を Python プログラミング言語で表現し、[bonmin](https://projects.coin-or.org/Bonmin) (オープン ソース) や [gurobi](http://www.gurobi.com/) (商用) などの独立したソルバーを呼び出して、ソリューションを見つけることができます。

在庫最適化のドキュメントでは、品揃えの最適化とは異なる問題 (注文数量) を取り上げていますが、Azure でのソルバーの実装は同じように適用することができます。

この手法はこれまで提案されていたものよりも複雑になりますが、最大限のスケーラビリティが可能になります (主に使用できるコアの量で制限されます)。

## <a name="running-the-model-operationalize"></a>モデルの実行 (運用化)

モデルをトレーニングすると、そのモデルを実行するには通常、デプロイに使用したのとは異なるインフラストラクチャが必要になります。 簡単に使用できるようにするため、REST インターフェイスを持つ Web サービスとしてモデルをデプロイすることもできます。 Azure ML Studio と ML Server はどちらも、このようなサービスの作成プロセスを自動化します。 ML Server の場合、サポート インフラストラクチャをデプロイするためのテンプレートも提供されています。 関連する[ドキュメント](https://docs.microsoft.com/machine-learning-server/what-is-operationalization?WT.mc_id=invopt-article-gmarchet)を参照してください。

次の図は、このデプロイのアーキテクチャを示しています。 R 言語と Python を実行しているサーバーの表現が含まれています。 どちらのサーバーも、計算を実行する Web ノードのサブ セクションと通信します。 大規模なデータ ストアが計算ブロックに接続されています。

![ML Server のデプロイ図。 複数のノードを実行する前のロード バランサー。](assets/sku-optimization-solution-guide/ml-server-deployment-example.png)<center><font size="1">_図 6: ML Server のデプロイの例_</font></center>


HDInsight または Databricks 上で作成され、そのために Spark 環境 (ライブラリ、並列機能など) に依存するモデルの場合、クラスターでの実行を検討することもできます。 ガイダンスについては、[こちら](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/spark-model-consumption?WT.mc_id=invopt-article-gmarchet)を参照してください。

これには、スコアリングのために Data Factory パイプラインのアクティビティを通じて、運用モデルそのものを呼び出せるという利点があります。

コンテナーを使用するには、モデルをパッケージ化し、それを Azure Kubernetes Service 上にデプロイします。 プロトタイプでは、[Azure Data Science VM](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/?WT.mc_id=invopt-article-gmarchet) を使用する必要があります。また、VM に Azure ML [コマンドライン](https://docs.microsoft.com/azure/machine-learning/desktop-workbench/model-management-service-deploy?WT.mc_id=invopt-article-gmarchet) ツールをインストールする必要もあります。

## <a name="data-output-and-reporting"></a>データの出力とレポート

デプロイすると、モデルで金融取引のワークフローや在庫の読み取りを処理して、最適な品揃えの予測を生成できるようになります。 そのため、生成されたデータをさらに詳しく分析するために、Azure SQL Data Warehouse に戻して格納することができます。 具体的には、最大の収入源と損失源を識別することで、さまざまな SKU のパフォーマンスの履歴を調査できるようになります。 その後、これらをモデルから提案された品揃えと比較して、パフォーマンスと再トレーニングの必要性を評価することができます。

[PowerBI](https://powerbi.microsoft.com/get-started/?&OCID=AID719832_SEM_uhlWLg3x&lnkd=Google_PowerBI_Brand&gclid=CjwKCAjw5ZPcBRBkEiwA-avvkyOLMJCrhqH8iac84aLX7EcUQIirSSqUCostzGi8y_XntJTCD73ZixoCQ4sQAvD_BwE?WT.mc_id=invopt-article-gmarchet) では、プロセスで生成されたデータを分析して表示する方法が提供されます。 

次の図は、一般的な Power BI ダッシュボードを示しています。 SKU の在庫情報を表示する 2 つのグラフが含まれています。 

![12 か月間の結果を示すダッシュボードの例。](assets/sku-optimization-solution-guide/sku-max-model.png)<center><font size="1">_図 7: モデル結果のレポートの例: Neal Analytics 提供_</font></center>

## <a name="security-considerations"></a>セキュリティに関する考慮事項

機密データを処理するソリューションには、財務記録、在庫レベル、価格情報が含まれています。 このような機密データは保護する必要があります。 データのセキュリティとプライバシーに関する懸念を緩和するには、次のことに注意してください。

- Azure Integration Runtime を使用して、Azure Data Factory パイプラインの一部をオンプレミスで実行できます。 ランタイムは、オンプレミス ソースとの間でデータ移動アクティビティを実行します。 また、オンプレミスで実行するためのアクティビティをディスパッチします。
  - 具体的には、カスタム アクティビティを開発して、Azure に転送するデータを匿名化 (およびオンプレミスで実行) することができます。
- ここで言及したすべてのサービスが、送信および保存時の暗号化をサポートしています。 Azure Data Lake にデータを格納する場合、暗号化は既定で有効になります。 Azure SQL Data Warehouse を使用する場合は、Transparent Data Encryption (TDE) を有効にできます。
- 言及されているすべてのサービス (ML Studio を除く) が、認証と承認のために Azure Active Directory との統合をサポートしています。 独自のコードを記述する場合、その統合をアプリケーションに組み込む必要があります。

GDPR の詳細については、Microsoft の[コンプライアンス](https://www.microsoft.com/trustcenter?WT.mc_id=invopt-article-gmarchet) ページを参照してください。

## <a name="technologies-mentioned"></a>本ドキュメントで言及した技術

- [Azure Batch](https://azure.microsoft.com/services/batch/?WT.mc_id=invopt-article-gmarchet)
- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/?&OCID=AID719825_SEM_w1MNAVjn&lnkd=Google_Azure_Brand&gclid=CjwKCAjw5ZPcBRBkEiwA-avvk4bGtyQo11KBY-u2skor1SydsSl1vrYUmhyGhhwyJhDlAYpnMmIcRRoCTfsQAvD_BwE&dclid=CMn6lvfRkd0CFRwBrQYdtIoJOA?WT.mc_id=invopt-article-gmarchet)
- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction?WT.mc_id=invopt-article-gmarchet)
- [Azure Integration Runtime](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime?WT.mc_id=invopt-article-gmarchet)
- [HDInsight](https://azure.microsoft.com/services/hdinsight/?WT.mc_id=invopt-article-gmarchet)
- [Databricks](https://azure.microsoft.com/services/databricks/?WT.mc_id=invopt-article-gmarchet)
- [Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is?WT.mc_id=invopt-article-gmarchet)
- [Azure ML Studio](https://studio.azureml.net/?WT.mc_id=invopt-article-gmarchet)
- [Microsoft ML Server](https://docs.microsoft.com/machine-learning-server/what-is-machine-learning-server?WT.mc_id=invopt-article-gmarchet)
- [Azure Data Science VM](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/?WT.mc_id=invopt-article-gmarchet)
- [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/?WT.mc_id=invopt-article-gmarchet)
- [Microsoft PowerBI](https://powerbi.microsoft.com/?WT.mc_id=invopt-article-gmarchet)
- [Pyomo 最適化モデリング言語](http://www.pyomo.org/)
- [Bonmin ソルバー](https://projects.coin-or.org/Bonmin)
- [Spark 用 TFoCS ソルバー](https://github.com/databricks/spark-tfocs)
