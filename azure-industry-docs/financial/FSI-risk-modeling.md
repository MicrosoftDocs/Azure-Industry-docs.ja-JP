---
ms.openlocfilehash: cff221055e76d7334793782d19eadd0960712a1f
ms.sourcegitcommit: 461c520509d53bae1021eebf9733a98edbf71e4d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2019
ms.locfileid: "66716856"
---
# <a name="enabling-the-financial-services-risk-lifecycle-with-azure-and-r"></a>Azure と R を使用して金融サービスのリスク ライフサイクルを有効にする


リスク計算は、主要な金融サービス業務のライフサイクルのいくつかの段階できわめて重要です。 たとえば、保険商品の管理ライフサイクルを簡略化すると、次の図のようになります。 リスク計算の側面は青色のテキストで示されています。

![](./assets/fsi-risk-modeling/image1.png)

投資会社でのシナリオは次のようになります:

![](./assets/fsi-risk-modeling/image2.png)

これらのプロセスを通じて、リスクのモデリングに関連した次のような共通のニーズがあります:

1.  保険会社のアクチュアリー、投資会社のクォンツといったリスク アナリストによるアドホックのリスク関連実験の必要性。
    これらのアナリストは通常、それぞれの分野で人気のあるコードおよびモデリング ツールである R や Python を業務に使用します。 多くの大学の金融工学および MBA コースで、R または Python のトレーニングがカリキュラムに含まれています。
    どちらの言語も、一般的なリスク計算をサポートする幅広いオープン ソース ライブラリを提供しています。 多くの場合、アナリストは適切なツールに加えて、次のものにアクセスする必要があります:

    a.  正確な市場価格データ。

    b.  既存のポリシーおよびクレーム データ。

    c.  既存の市場ポジション データ。

    d.  その他の外部データ。 ソースには、死亡率表や競争的価格データといった構造化データが含まれます。 天気やニュースなどのあまり伝統的でないソースも使用できます。

    e.  インタラクティブなデータの迅速な調査を可能にする計算容量。

2.  アドホックな機械学習アルゴリズムを値付けや市場戦略の決定に利用することもできます。

3.  商品計画、取引戦略、類似の議論に使用するためにデータを視覚化して提示する必要性。

4.  値付け、評価、市場リスクのための、アナリストによって構成された定義済みモデルの迅速な実行。 評価では、専用のリスク モデリング、市場リスク ツール、およびカスタム コードの組み合わせを使用します。 分析はバッチで実行され、夜間、毎週、毎月、四半期に 1 回、および年次の変動する計算によりワークロードの急増が発生します。

5.  総合的なリスク報告のために、その他の企業レベルのリスク基準とデータを統合。 より大規模な組織では、より低レベルのリスク予測を企業のリスク モデリングおよびレポート ツールに移転できます。

6.  投資家や規制上の要件を満たすために、定義された形式かつ義務付けられた間隔で結果を報告する必要があります。

マイクロソフトでは、Azure のサービスと、パートナーが [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) で提供するサービスを組み合わせることによって、上記の懸念事項をサポートします。 この記事では、R を使用してアドホックの実験を実行する方法の実用的な例を示します。1 台のマシンで実験を実行する方法、[Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely) で同じ実験を実行する方法、モデリングで外部サービスを利用する方法の順に説明します。 [銀行業](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)と[保険業](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)に焦点を当てたこれらの記事では、定義されたモデルを Azure で実行するためのオプションおよび考慮事項について説明しています。

## <a name="analyst-modelling-in-r"></a>R でのアナリスト モデリング 

まず、簡略化された代表的な資本市場のシナリオでアナリストが R をどのように使用できるかを見てみましょう。 これは、既存の計算用 R ライブラリを参照するか、またはゼロからコードを記述することによって構築できます。 この例では、外部の値付けデータも取得する必要があります。 シンプルながらも分かりやすい例にするために、普通株先渡契約の潜在的将来エクスポージャー (PFE) を計算します。
この例では、リスクのライフサイクルに集中するために、複合デリバティブのような証券向けの複雑な定量的モデリング技法を避けて単一のリスク要因に焦点を当てています。 この例では次のことを行います:

1.  関心のある証券を選択します。

2.  証券の過去価格を入手します。

3.  幾何ブラウン運動 (GBM) を使用した単純なモンテカルロ (MC) 計算によるモデル株価:

    a.  期待リターン μ (ミュー) とボラティリティ σ (シータ) を予測します。

    b.  モデルを過去データに較正します。

4.  結果を伝達するためにさまざまなパスを視覚化します。

5.  max(0,株式価値) をプロットして、PFE の意味、Value at Risk (VaR: 想定最大損失額) との差を示します

    a.  解説: PFE = 株価 (T) -- 先渡契約価格 K

6.  タイム ステップごとに/シミュレーション期間の終了時点で 0.95 分位点を取って PFE 価値を得ます

MSFT 株式をベースに、株式先渡の潜在的将来エクスポージャーを計算することにします。 前述のように、株価をモデル化するためには、モデルを過去データに較正できるように MSFT 株式の過去価格が必要です。 過去の株価を取得するには多くの方法があります。 この例では、外部のサービス事業者 [Quandl](https://www.quandl.com/) の株価サービスの無料版を使用します。


> 注:この例で使用しているのは、概念の学習用途に使用できる [WIKI Prices データセット](https://www.quandl.com/databases/WIKIP)です。 実際の米国株式データを使用する場合、Quandl では [End of Day US Stock Prices (米国株価終値) データセット](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices)の使用を推奨しています。

データを処理して株式に関連するリスクを定義するには、次のことを行う必要があります:

1.  株式の過去データを取得します。

2.  過去データから予想されるリターン μ とボラティリティ σ を決定します。

3.  いくつかのシミュレーションを使用して基礎株価をモデル化します。

4.  モデルを実行する

5.  将来における株式のエクスポージャーを決定します。

はじめに、Quandl サービスから株式を取得し、過去 180 日間の終値履歴をプロットします。

````R
# Lubridate package must be installed
if (!require(lubridate)) install.packages('lubridate')
library(lubridate)

# Quandl package must be installed
if (!require(Quandl)) install.packages('Quandl')
library(Quandl)

# Get your API key from quandl.com
quandl_api = "enter your key here"

# Add the key to the Quandl keychain
Quandl.api_key(quandl_api)

quandl_get <-
    function(sym, start_date = "2018-01-01") {
        require(devtools)
        require(Quandl)
        # Retrieve the Open, High, Low, Close and Volume Column for a given Symbol
        # Column Indices can be deduced from this sample call
        # data <- Quandl(c("WIKI/MSFT"), rows = 1)

        tryCatch(Quandl(c(
        paste0("WIKI/", sym, ".8"),    # Column 8 : Open
        paste0("WIKI/", sym, ".9"),    # Column 9 : High
        paste0("WIKI/", sym, ".10"),   # Column 10: Low
        paste0("WIKI/", sym, ".11"),   # Column 11: Close
        paste0("WIKI/", sym, ".12")),  # Column 12: Volume
        start_date = start_date,
        type = "raw"
        ))
    }

# Define the Equity Forward
instrument.name <- "MSFT"
instrument.premium <- 100.1
instrument.pfeQuantile <- 0.95

# Get the stock price for the last 180 days
instrument.startDate <- today() - days(180)

# Get the quotes for an equity and transform them to a data frame
df_instrument.timeSeries <- quandl_get(instrument.name,start_date = instrument.startDate)

#Rename the columns
colnames(df_instrument.timeSeries) <- c()
colnames(df_instrument.timeSeries) <- c("Date","Open","High","Low","Close","Volume")

# Plot the closing price history to get a better feeling for the data
plot(df_instrument.timeSeries$Date, df_instrument.timeSeries$Close)
````

データを入手したら、GBM モデルを較正します。

````R
# Code inspired by the book Computational Finance, An Introductory Course with R by 
#    A. Arratia.

# Calculate the daily return in order to estimate sigma and mu in the Wiener Process
df_instrument.dailyReturns <- c(diff(log(df_instrument.timeSeries$Close)), NA)

# Estimate the mean of std deviation of the log returns to estimate the parameters of the Wiener Process

estimateGBM_Parameters <- function(logReturns,dt = 1/252) {

    # Volatility
    sigma_hat = sqrt(var(logReturns)) / sqrt(dt)

    # Drift
    mu_hat = mean(logReturns) / dt + sigma_hat**2 / 2.0

    # Return the parameters
    parameter.list <- list("mu" = mu_hat,"sigma" = sigma_hat)

    return(parameter.list)
}

# Calibrate the model to historic data
GBM_Parameters <- estimateGBM_Parameters(df_instrument.dailyReturns[1:length(df_instrument.dailyReturns) - 1])
````

次に、基礎株価をモデル化します。 個別の GBM プロセスをゼロから実装するか、またはこの機能を提供する多くの R パッケージのいずれかを利用することができます。 この問題の解決方法を提供する R パッケージ [*sde* (確率的微分方程式のシミュレーションと推論)](https://cran.r-project.org/web/packages/sde/index.html) を使用します。 GBM 法には、履歴データに較正されるか、またはシミュレーション パラメーターとして与えられるパラメーターの集合が必要です。 履歴データを使用し、シミュレーション (P0) の開始時に μ、σ、および株価を提供します。

````R
if (!require(sde)) install.packages('sde')
library(sde)

sigma <-  GBM_Parameters$sigma
mu <- GBM_Parameters$mu
P0 <- tail(df_instrument.timeSeries$Close, 1)

# Calculate the PFE looking one month into the future
T <- 1 / 12

# Consider nt MC paths
nt=50

# Divide the time interval T into n discrete time steps
n = 2 ^ 8 

dt <- T / n
t <- seq(0,T,by=dt)
````

いくつかのシミュレーション パスに対して、モンテカルロ シミュレーションを開始して潜在的エクスポージャーをモデル化する準備ができました。 シミュレーションを 50 モンテカルロ パスと 256 タイム ステップに制限します。 シミュレーションをスケール アウトして R の並列化を利用するための準備として、モンテカルロ シミュレーション ループでは foreach 文を使用します。

````R
# Track the start time of the simulation
start_s <- Sys.time()

# Instead of a simple for loop to execute a simulation per MC path, call the
# simulation with the foreach package
# in order to demonstrate the similarity to the AzureBatch way to call the method.

library(foreach)
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package
exposure_mc <- foreach (i=1:nt, .combine = rbind ) %do%  GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()

# Track the end time of the simulation
end_s <- Sys.time()

# Duration of the simulation

difftime(end_s, start_s) 
````

基礎となる MSFT 株式の価格をシミュレートしたところです。 株式先渡のエクスポージャーを計算するには、プレミアムを減算し、エクスポージャーを正の値のみに制限します。

````R
# Calculate the total Exposure as V_i(t) - K, put it to zero for negative exposures
pfe_mc <- pmax(exposure_mc - instrument.premium ,0)

ymax <- max(pfe_mc)
ymin <- min(pfe_mc)
plot(t, pfe_mc[1,], t = 'l', ylim = c(ymin, ymax), col = 1, ylab="Credit Exposure in USD", xlab="time t in Years")
for (i in 2:nt) {
    lines(t, pfe_mc[i,], t = 'l', ylim = c(ymin, ymax), col = i)
}
````

次の 2 つの画像はシミュレーションの結果を示しています。 最初の画像は、50 パスにわたる基礎株価のモンテカルロ シミュレーションを示しています。 2 番目の画像は、株式先渡のプレミアムを差し引いてエクスポージャーを正の値に制限した後の、株式先渡の基礎信用エクスポージャーを示しています。


|       |    |
|---    |--- |
| <img src="./assets/fsi-risk-modeling/image3.png" width="400px" alt="Figure 1 - 50 Monte Carlo Paths"/> | <img src="./assets/fsi-risk-modeling/image4.png" width="400px" alt="Figure 2 - Credit Exposure for Equity Forward"/> |
| 図 1 - 50 モンテカルロ パス | 図 2 - 株式先渡の信用エクスポージャー |


最後のステップでは、1 か月 0.95 分位点 PFE を次のコードによって計算します。

````R
# Calculate the PFE at each time step
df_pfe <- cbind(t,apply(pfe_mc,2,quantile,probs = instrument.pfeQuantile ))

resulting in the final PFE plot
plot(df_pfe, t = 'l', ylab = "Potential Future Exposure in USD", xlab = "time t in Years")
````

<img src="./assets/fsi-risk-modeling/image5.png" width="500px" alt="Potential Future Exposure for MSFT Equity Forward" /> 

図 3 MSFT 株式先渡の潜在的将来エクスポージャー

## <a name="using-azure-batch-with-r"></a>R での Azure Batch の使用 

前述の R ソリューションは、Azure Batch に接続してリスク計算にクラウドを利用することができます。 これは私たちのものと違い、並列計算に追加の労力をほとんど要しません。 Azure Batch への R の接続については、[Azure Batch を使用して並列 R シミュレーションを実行する](https://docs.microsoft.com/en-us/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)チュートリアルで詳しい情報を提供しています。 以下では、Azure Batch に接続するプロセスのコードと概要を示し、簡略化した PFE 計算でクラウドの拡張機能を利用する方法を示します。

この例では、前述のものと同じモデルを扱います。 既に説明したように、この計算はパーソナル コンピューター上で実行できます。 モンテカルロ パスの数を増やすか、より短いタイム ステップを使用すると、実行時間が大幅に長くなります。 ほとんどすべての R コードは変更されないままです。 このセクションでは相違点に注目します。

モンテカルロ シミュレーションの各パスは Azure で実行されます。 これが可能なのは、それぞれのパスが他のパスから独立しており、"きまりが悪いほど並列的な" 計算ができるからです。

Azure Batch を使用するためには、計算でクラスターが使用できるようになる前に、基礎となるクラスターを定義してコード内でそれを参照します。 計算を実行するために、次の cluster.json 定義を使用します:

````JavaScript
{
  "name": "myMCPool",
  "vmSize": "Standard_D2_v2",
  "maxTasksPerNode": 4,
  "poolSize": {
    "dedicatedNodes": {
      "min": 1,
      "max": 1
    },
    "lowPriorityNodes": {
      "min": 3,
      "max": 3
    },
    "autoscaleFormula": "QUEUE"
  },
  "containerImage": "rocker/tidyverse:latest",
  "rPackages": {
    "cran": [],
    "github": [],
    "bioconductor": []
  },
  "commandLine": [],
  "subnetId": ""
}
````

このクラスター定義では、次の R コードがクラスターを利用します:
````R
# Define the cloud burst environment
library(doAzureParallel)

# set your credentials
setCredentials("credentials.json")

# Create your cluster if not exist
cluster <- makeCluster("cluster.json")

# register your parallel backend
registerDoAzureParallel(cluster)

# check that your workers are up
getDoParWorkers()
````

最後に、foreach ステートメントを以前のものから更新し、doAzureParallel パッケージを使用するようにします。 これは小さな変更であり、sde パッケージへの参照を追加し、%do% を %dopar% に変更します:

````R
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package and extend the computation to the cloud
exposure_mc <- foreach(i = 1:nt, .combine = rbind, .packages = 'sde') %dopar% GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()
````

各モンテカルロ シミュレーションはタスクとして Azure Batch に送信されます。 タスクはクラウドで実行されます。 アナリスト ワークベンチに返送される前に結果がマージされます。 要求された計算に必要なスケーリングと基礎インフラストラクチャをフル活用するために、複雑な処理と計算がクラウドで実行されます。

計算が完了したら、次の 1 つの命令を呼び出すことによって、追加のリソースを簡単にシャットダウンできます:

````R
# Stop the Cloud cluster
stopCluster(cluster)
````


## <a name="use-a-saas-offering"></a>SaaS サービスを使用する

最初の 2 つの例は、適切な評価モデルを開発するためにローカルとクラウドのインフラストラクチャを利用する方法を示しています。 このパラダイムは変わり始めました。 オンプレミスのインフラストラクチャがクラウドベースの IaaS および PaaS サービスに形を変えたのと同じように、関連するリスク数値のモデリングはサービス指向のプロセスに形を変えつつあります。
今日のアナリストは 2 つの大きな課題に直面しています:

1.  規制要件によってモデリングの要件が上がり、使用する計算容量が増加しています。 規制当局は、最新のリスク数値の報告をより頻繁に義務付けるようになってきています。

2.  これまで有機的に発展してきた既存のリスク インフラストラクチャは、新たな要件と高度化したリスク モデリングの迅速な実装にあたって課題を突き付けています。

クラウドベースのサービスは、必要な機能を提供し、リスク分析をサポートすることができます。 このアプローチにはいくつかの利点があります:

-   規制当局が要求する最も一般的なリスク計算は、規制下のすべての当事者が実装する必要があります。 専門のサービス事業者のサービスを利用することで、アナリストは、すぐに使えて規制要件に準拠したリスク計算の恩恵を受けます。 そのようなサービスには、市場リスク計算、取引先リスク計算、X-Value Adjustment (XVA: X 価値調整)、さらには Fundamental Review of Trading Book (FRTB: トレーディング勘定の抜本的な見直し) 計算が含まれます。

-   これらのサービスは Web サービスを通じてインターフェイスを公開します。 こうしたその他のサービスによって既存のリスク インフラストラクチャを強化できます。

私たちの例では、FRTB 計算のためにクラウドベースのサービスを呼び出したいと考えています。 これらのいくつかは [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) に見つかります。 この記事では [Vector Risk](http://www.vectorrisk.com/) の試用版オプションを選択しました。 システムの改良を続けます。 次に、サービスを使用して金利のリスク数値を計算します。 このプロセスは次のステップで構成されます:

1.  適切なリスク サービスを正しいパラメーターを与えて呼び出します。

2.  サービスが計算を終了するまで待ちます。

3.  結果を取得してリスク分析に組み込みます。

R コードに変換されます。この R コードは、準備された入力テンプレートにある、必要な入力値の定義によって拡張できます。

````R
Template <- readLines('RequiredInputData.json')
data <- list(
# drilldown setup
  timeSteps = seq(0, n, by = 1),
  paths = as.integer(seq(0, nt, length.out = min(nt, 100))),
# calc setup
  calcDate = instrument.startDate,
  npaths = nt,
  price = P0,
  vol = sigma,
  drift = mu,
  premium = instrument.premium,
  maturityDate = today()
  )
body <- whisker.render(template, data)
````


次に、Web サービスを呼び出す必要があります。 このケースでは、StartCreditExposure メソッドを呼び出して計算を開始します。 API のエンドポイントを *endpoint* という名前の変数に格納します。

````R
# make the call
result <- POST( paste(endpoint, "StartCreditExposure", sep = ""),  
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
                body = body, encode = "raw"
               )

result <- content(result)
````

計算が終了したら、結果を取得します。

````R
# get back high level results
result <- POST( paste(endpoint, "GetCreditExposureResults", sep = ""), 
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
               body = sprintf('{"getCreditExposureResults": {"token":"DataSource=Production;Organisation=Microsoft", "ticket": "%s"}}', ticket), encode = "raw")

result <- content(result)
````

アナリストは、受け取った結果を使用して作業の続きを行います。 関心のある適切なリスク数値が結果から抽出され、プロットされます。

````R
if (!is.null(result$error)) {
    cat(result$error$message)
} else {
    # plot PFE
    result <- result$getCreditExposureResultsResponse$getCreditExposureResultsResult
    df <- do.call(rbind, result$exposures)
    df <- as.data.frame(df)
    df <- subset(df, term <= n)   
}

plot(as.numeric(df$term[df$statistic == 'PFE']) / 365, df$result[df$statistic == 'PFE'], type = "p", xlab = ("time t in Years"), ylab = ("Potential Future Exposure in USD"), ylim = range(c(df$result[df$statistic == 'PFE'], df$result[df$statistic == 'PFE'])), col = "red")
````


結果のプロットは次のようになります:

|       |     |
|----    |---- |
| <img src="./assets/fsi-risk-modeling/image6.png" width="400px" alt="Figure 4 - Credit Exposure for MSFT Equity Forward - Calculated with a Cloud Based Risk Engine"/> | <img src="./assets/fsi-risk-modeling/image7.png" width="400px" alt="Figure 5 - Potential Future Exposure for MSFT Equity Forward - Calculated with a Cloud  Based Risk Engine" /> |
| 図 4 - MSFT 株式先渡の信用エクスポージャー - <br/>クラウドベースのリスク エンジンで計算 | 図 5 - MSFT 株式先渡の潜在的将来エクスポージャー - <br/> クラウドベースのリスク エンジンで計算 |



## <a name="next-steps"></a>次の手順

クラウドの計算インフラストラクチャや SaaS ベースのリスク分析サービスへの柔軟なアクセスによって、投資業や保険業のリスク アナリストはスピードと機敏性を向上させることができます。 この記事では、リスク アナリストが知っているツールを使用して Azure やその他のサービスを使用する方法の例を説明しました。 Azure の機能を活用して、自らの手でリスク モデルの作成と強化に取り組んでみてください。

### <a name="tutorials"></a>チュートリアル


-   R 開発者: [Azure Batch で並列 R シミュレーションを実行する](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [基本的な R コマンドと RevoScaleR 関数: 25 の一般的な例](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [RevoScaleR を使用したデータの視覚化と分析](https://docs.microsoft.com/machine-learning-server/r/tutorial-revoscaler-data-model-analysis?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [HDInsight での ML Services とオープン ソース R の機能の概要](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview?WT.mc_id=fsiriskmodelr-docs-scseely)

_本記事の執筆者は Dr.Darko Mocelj および Rupert Nicolay です。_