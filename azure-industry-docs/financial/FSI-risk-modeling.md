# <a name="enabling-the-financial-services-risk-lifecycle-with-azure-and-r"></a><span data-ttu-id="4828c-101">Azure と R を使用して金融サービスのリスク ライフサイクルを有効にする</span><span class="sxs-lookup"><span data-stu-id="4828c-101">Enabling the financial services risk lifecycle with Azure and R</span></span>


<span data-ttu-id="4828c-102">リスク計算は、主要な金融サービス業務のライフサイクルのいくつかの段階できわめて重要です。</span><span class="sxs-lookup"><span data-stu-id="4828c-102">Risk calculations are pivotal at several stages in the lifecycle of key financial services operations.</span></span> <span data-ttu-id="4828c-103">たとえば、保険商品の管理ライフサイクルを簡略化すると、次の図のようになります。</span><span class="sxs-lookup"><span data-stu-id="4828c-103">For example, a simplified form of the insurance product management lifecycle might look something like the diagram below.</span></span> <span data-ttu-id="4828c-104">リスク計算の側面は青色のテキストで示されています。</span><span class="sxs-lookup"><span data-stu-id="4828c-104">The risk calculation aspects are shown in blue text.</span></span>

![](./assets/fsi-risk-modeling/image1.png)

<span data-ttu-id="4828c-105">投資会社でのシナリオは次のようになります:</span><span class="sxs-lookup"><span data-stu-id="4828c-105">A scenario in a capital markets firm might look like this:</span></span>

![](./assets/fsi-risk-modeling/image2.png)

<span data-ttu-id="4828c-106">これらのプロセスを通じて、リスクのモデリングに関連した次のような共通のニーズがあります:</span><span class="sxs-lookup"><span data-stu-id="4828c-106">Through these processes there are common needs around risk modelling including:</span></span>

1.  <span data-ttu-id="4828c-107">保険会社のアクチュアリー、投資会社のクォンツといったリスク アナリストによるアドホックのリスク関連実験の必要性。</span><span class="sxs-lookup"><span data-stu-id="4828c-107">The need for ad-hoc risk-related experimentation by risk analysts; actuaries in an insurance firm or quants in a capital markets firm.</span></span>
    <span data-ttu-id="4828c-108">これらのアナリストは通常、それぞれの分野で人気のあるコードおよびモデリング ツールである R や Python を業務に使用します。</span><span class="sxs-lookup"><span data-stu-id="4828c-108">These analysts typically work with code and modelling tools popular in their domain: R and Python.</span></span> <span data-ttu-id="4828c-109">多くの大学の金融工学および MBA コースで、R または Python のトレーニングがカリキュラムに含まれています。</span><span class="sxs-lookup"><span data-stu-id="4828c-109">Many university curriculums include training in R or Python in mathematical finance and in MBA courses.</span></span>
    <span data-ttu-id="4828c-110">どちらの言語も、一般的なリスク計算をサポートする幅広いオープン ソース ライブラリを提供しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-110">Both languages offer a wide range of open source libraries which support popular risk calculations.</span></span> <span data-ttu-id="4828c-111">多くの場合、アナリストは適切なツールに加えて、次のものにアクセスする必要があります:</span><span class="sxs-lookup"><span data-stu-id="4828c-111">Along with appropriate tooling, analysts often require access to:</span></span>

    <span data-ttu-id="4828c-112">a.</span><span class="sxs-lookup"><span data-stu-id="4828c-112">a.</span></span>  <span data-ttu-id="4828c-113">正確な市場価格データ。</span><span class="sxs-lookup"><span data-stu-id="4828c-113">Accurate market pricing data.</span></span>

    <span data-ttu-id="4828c-114">b.</span><span class="sxs-lookup"><span data-stu-id="4828c-114">b.</span></span>  <span data-ttu-id="4828c-115">既存のポリシーおよびクレーム データ。</span><span class="sxs-lookup"><span data-stu-id="4828c-115">Existing policy and claims data.</span></span>

    <span data-ttu-id="4828c-116">c.</span><span class="sxs-lookup"><span data-stu-id="4828c-116">c.</span></span>  <span data-ttu-id="4828c-117">既存の市場ポジション データ。</span><span class="sxs-lookup"><span data-stu-id="4828c-117">Existing market position data.</span></span>

    <span data-ttu-id="4828c-118">d.</span><span class="sxs-lookup"><span data-stu-id="4828c-118">d.</span></span>  <span data-ttu-id="4828c-119">その他の外部データ。</span><span class="sxs-lookup"><span data-stu-id="4828c-119">Other external data.</span></span> <span data-ttu-id="4828c-120">ソースには、死亡率表や競争的価格データといった構造化データが含まれます。</span><span class="sxs-lookup"><span data-stu-id="4828c-120">Sources include structured data such as mortality tables and competitive pricing data.</span></span> <span data-ttu-id="4828c-121">天気やニュースなどのあまり伝統的でないソースも使用できます。</span><span class="sxs-lookup"><span data-stu-id="4828c-121">Less traditional sources such as weather, news and others may also be used.</span></span>

    <span data-ttu-id="4828c-122">e.</span><span class="sxs-lookup"><span data-stu-id="4828c-122">e.</span></span>  <span data-ttu-id="4828c-123">インタラクティブなデータの迅速な調査を可能にする計算容量。</span><span class="sxs-lookup"><span data-stu-id="4828c-123">Computational capacity to enable quick interactive data investigations.</span></span>

2.  <span data-ttu-id="4828c-124">アドホックな機械学習アルゴリズムを値付けや市場戦略の決定に利用することもできます。</span><span class="sxs-lookup"><span data-stu-id="4828c-124">They may also make use of ad-hoc machine learning algorithms for pricing or determining market strategy.</span></span>

3.  <span data-ttu-id="4828c-125">商品計画、取引戦略、類似の議論に使用するためにデータを視覚化して提示する必要性。</span><span class="sxs-lookup"><span data-stu-id="4828c-125">The need to visualize and present data for use in product planning, trading strategy, and similar discussions.</span></span>

4.  <span data-ttu-id="4828c-126">値付け、評価、市場リスクのための、アナリストによって構成された定義済みモデルの迅速な実行。</span><span class="sxs-lookup"><span data-stu-id="4828c-126">The rapid execution of defined models, configured by the analysts, for pricing, valuations, and market risk.</span></span> <span data-ttu-id="4828c-127">評価では、専用のリスク モデリング、市場リスク ツール、およびカスタム コードの組み合わせを使用します。</span><span class="sxs-lookup"><span data-stu-id="4828c-127">The valuations use a combination of dedicated risk modelling, market risk tools, and custom code.</span></span> <span data-ttu-id="4828c-128">分析はバッチで実行され、夜間、毎週、毎月、四半期に 1 回、および年次の変動する計算によりワークロードの急増が発生します。</span><span class="sxs-lookup"><span data-stu-id="4828c-128">The analysis is executed in a batch with varying nightly, weekly, monthly, quarterly, and annual calculations generating spikes in workloads.</span></span>

5.  <span data-ttu-id="4828c-129">総合的なリスク報告のために、その他の企業レベルのリスク基準とデータを統合。</span><span class="sxs-lookup"><span data-stu-id="4828c-129">The integration of data with other enterprise wide risk measures for consolidated risk reporting.</span></span> <span data-ttu-id="4828c-130">より大規模な組織では、より低レベルのリスク予測を企業のリスク モデリングおよびレポート ツールに移転できます。</span><span class="sxs-lookup"><span data-stu-id="4828c-130">In larger organizations lower level risk estimates may be transferred to an enterprise risk modelling and reporting tool.</span></span>

6.  <span data-ttu-id="4828c-131">投資家や規制上の要件を満たすために、定義された形式かつ義務付けられた間隔で結果を報告する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4828c-131">Results must be reported in a defined format at the required interval to meet investor and regulatory requirements.</span></span>

<span data-ttu-id="4828c-132">マイクロソフトでは、Azure のサービスと、パートナーが [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) で提供するサービスを組み合わせることによって、上記の懸念事項をサポートします。</span><span class="sxs-lookup"><span data-stu-id="4828c-132">Microsoft supports the above concerns through a combination of Azure services and partner offerings in the [Azure Marketplace](https://azuremarketplace.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span> <span data-ttu-id="4828c-133">この記事では、R を使用してアドホックの実験を実行する方法の実用的な例を示します。1 台のマシンで実験を実行する方法、[Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely) で同じ実験を実行する方法、モデリングで外部サービスを利用する方法の順に説明します。</span><span class="sxs-lookup"><span data-stu-id="4828c-133">In this article, we show practical examples of how to perform ad-hoc experimentation using R. We begin by explaining how to run the experiment on a single machine, then show how to run the same experiment on [Azure Batch](https://docs.microsoft.com/azure/batch/?WT.mc_id=fsiriskmodelr-docs-scseely), and close by showing how to take advantage of external services in our modelling.</span></span> <span data-ttu-id="4828c-134">[銀行業](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)と[保険業](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely)に焦点を当てたこれらの記事では、定義されたモデルを Azure で実行するためのオプションおよび考慮事項について説明しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-134">Options and considerations for the execution of defined models on Azure are described in these articles focused on [banking](https://docs.microsoft.com/azure/industry/financial/risk-grid-banking-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely) and [insurance](https://docs.microsoft.com/azure/industry/financial/actuarial-risk-analysis-and-financial-modeling-solution-guide?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span>

## <a name="analyst-modelling-in-r"></a><span data-ttu-id="4828c-135">R でのアナリスト モデリング</span><span class="sxs-lookup"><span data-stu-id="4828c-135">Analyst Modelling in R</span></span> 

<span data-ttu-id="4828c-136">まず、簡略化された代表的な資本市場のシナリオでアナリストが R をどのように使用できるかを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="4828c-136">Let's start by looking at how R may be used by an analyst in a simplified, representative capital markets scenario.</span></span> <span data-ttu-id="4828c-137">これは、既存の計算用 R ライブラリを参照するか、またはゼロからコードを記述することによって構築できます。</span><span class="sxs-lookup"><span data-stu-id="4828c-137">You can build this either by referencing an existing R library for the calculation or by writing code from scratch.</span></span> <span data-ttu-id="4828c-138">この例では、外部の値付けデータも取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4828c-138">In our example, we also must fetch external pricing data.</span></span> <span data-ttu-id="4828c-139">シンプルながらも分かりやすい例にするために、普通株先渡契約の潜在的将来エクスポージャー (PFE) を計算します。</span><span class="sxs-lookup"><span data-stu-id="4828c-139">To keep the example simple but illustrative, we calculate the potential future exposure (PFE) of an equity stock forward contract.</span></span>
<span data-ttu-id="4828c-140">この例では、リスクのライフサイクルに集中するために、複合デリバティブのような証券向けの複雑な定量的モデリング技法を避けて単一のリスク要因に焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="4828c-140">This example avoids complex quantitative modelling techniques for instruments like complex derivatives and focuses on a single risk factor to concentrate on the risk life cycle.</span></span> <span data-ttu-id="4828c-141">この例では次のことを行います:</span><span class="sxs-lookup"><span data-stu-id="4828c-141">Our example does the following:</span></span>

1.  <span data-ttu-id="4828c-142">関心のある証券を選択します。</span><span class="sxs-lookup"><span data-stu-id="4828c-142">Select an instrument of interest.</span></span>

2.  <span data-ttu-id="4828c-143">証券の過去価格を入手します。</span><span class="sxs-lookup"><span data-stu-id="4828c-143">Source historic prices for the instrument.</span></span>

3.  <span data-ttu-id="4828c-144">幾何ブラウン運動 (GBM) を使用した単純なモンテカルロ (MC) 計算によるモデル株価:</span><span class="sxs-lookup"><span data-stu-id="4828c-144">Model equity price by simple Monte Carlo (MC) calculation, which uses Geometric Brownian Motion (GBM):</span></span>

    <span data-ttu-id="4828c-145">a.</span><span class="sxs-lookup"><span data-stu-id="4828c-145">a.</span></span>  <span data-ttu-id="4828c-146">期待リターン μ (ミュー) とボラティリティ σ (シータ) を予測します。</span><span class="sxs-lookup"><span data-stu-id="4828c-146">Estimate expected return μ (mu) and volatility σ (theta).</span></span>

    <span data-ttu-id="4828c-147">b.</span><span class="sxs-lookup"><span data-stu-id="4828c-147">b.</span></span>  <span data-ttu-id="4828c-148">モデルを過去データに較正します。</span><span class="sxs-lookup"><span data-stu-id="4828c-148">Calibrate the model to historic data.</span></span>

4.  <span data-ttu-id="4828c-149">結果を伝達するためにさまざまなパスを視覚化します。</span><span class="sxs-lookup"><span data-stu-id="4828c-149">Visualize the various paths to communicate the results.</span></span>

5.  <span data-ttu-id="4828c-150">max(0,株式価値) をプロットして、PFE の意味、Value at Risk (VaR: 想定最大損失額) との差を示します</span><span class="sxs-lookup"><span data-stu-id="4828c-150">Plot max(0,Stock Value) to demonstrate the meaning of PFE, the difference to Value at Risk (VaR)</span></span>

    <span data-ttu-id="4828c-151">a.</span><span class="sxs-lookup"><span data-stu-id="4828c-151">a.</span></span>  <span data-ttu-id="4828c-152">解説: PFE = 株価 (T) -- 先渡契約価格 K</span><span class="sxs-lookup"><span data-stu-id="4828c-152">To clarify: PFE = Share Price (T) -- Forward Contract Price K</span></span>

6.  <span data-ttu-id="4828c-153">タイム ステップごとに/シミュレーション期間の終了時点で 0.95 分位点を取って PFE 価値を得ます</span><span class="sxs-lookup"><span data-stu-id="4828c-153">Take the 0.95 Quantile to get the PFE value at each time step / end of simulation period</span></span>

<span data-ttu-id="4828c-154">MSFT 株式をベースに、株式先渡の潜在的将来エクスポージャーを計算することにします。</span><span class="sxs-lookup"><span data-stu-id="4828c-154">We will calculate the potential future exposure for an equity forward based on MSFT stock.</span></span> <span data-ttu-id="4828c-155">前述のように、株価をモデル化するためには、モデルを過去データに較正できるように MSFT 株式の過去価格が必要です。</span><span class="sxs-lookup"><span data-stu-id="4828c-155">As mentioned above, to model the stock prices, historic prices for the MSFT stock are required so we can calibrate the model to historical data.</span></span> <span data-ttu-id="4828c-156">過去の株価を取得するには多くの方法があります。</span><span class="sxs-lookup"><span data-stu-id="4828c-156">There are many ways to acquire historical stock prices.</span></span> <span data-ttu-id="4828c-157">この例では、外部のサービス事業者 [Quandl](https://www.quandl.com/) の株価サービスの無料版を使用します。</span><span class="sxs-lookup"><span data-stu-id="4828c-157">In our example, we use a free version of a stock price service from an external service provider, [Quandl](https://www.quandl.com/).</span></span>


> <span data-ttu-id="4828c-158">注: この例で使用しているのは、概念の学習用途に使用できる [WIKI Prices データセット](https://www.quandl.com/databases/WIKIP)です。</span><span class="sxs-lookup"><span data-stu-id="4828c-158">Note: The example uses the [WIKI Prices dataset](https://www.quandl.com/databases/WIKIP) which can be used for learning concepts.</span></span> <span data-ttu-id="4828c-159">実際の米国株式データを使用する場合、Quandl では [End of Day US Stock Prices (米国株価終値) データセット](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices)の使用を推奨しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-159">For production usage of US based equities, Quandl recommends using the [End of Day US Stock Prices dataset](https://www.quandl.com/data/EOD-End-of-Day-US-Stock-Prices).</span></span>

<span data-ttu-id="4828c-160">データを処理して株式に関連するリスクを定義するには、次のことを行う必要があります:</span><span class="sxs-lookup"><span data-stu-id="4828c-160">To process the data and define the risk associated with the equity, we need to do the following things:</span></span>

1.  <span data-ttu-id="4828c-161">株式の過去データを取得します。</span><span class="sxs-lookup"><span data-stu-id="4828c-161">Retrieve history data from the equity.</span></span>

2.  <span data-ttu-id="4828c-162">過去データから予想されるリターン μ とボラティリティ σ を決定します。</span><span class="sxs-lookup"><span data-stu-id="4828c-162">Determine the expected return μ and volatility σ from the historic data.</span></span>

3.  <span data-ttu-id="4828c-163">いくつかのシミュレーションを使用して基礎株価をモデル化します。</span><span class="sxs-lookup"><span data-stu-id="4828c-163">Model the underlying stock prices using some simulation.</span></span>

4.  <span data-ttu-id="4828c-164">モデルを実行する</span><span class="sxs-lookup"><span data-stu-id="4828c-164">Run the model</span></span>

5.  <span data-ttu-id="4828c-165">将来における株式のエクスポージャーを決定します。</span><span class="sxs-lookup"><span data-stu-id="4828c-165">Determine the exposure of the equity in the future.</span></span>

<span data-ttu-id="4828c-166">はじめに、Quandl サービスから株式を取得し、過去 180 日間の終値履歴をプロットします。</span><span class="sxs-lookup"><span data-stu-id="4828c-166">We start by retrieving the stock from the Quandl service and plotting the closing price history over the last 180 days.</span></span>

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

<span data-ttu-id="4828c-167">データを入手したら、GBM モデルを較正します。</span><span class="sxs-lookup"><span data-stu-id="4828c-167">With the data in hand, we calibrate the GBM model.</span></span>

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

<span data-ttu-id="4828c-168">次に、基礎株価をモデル化します。</span><span class="sxs-lookup"><span data-stu-id="4828c-168">Next, we model the underlying stock prices.</span></span> <span data-ttu-id="4828c-169">個別の GBM プロセスをゼロから実装するか、またはこの機能を提供する多くの R パッケージのいずれかを利用することができます。</span><span class="sxs-lookup"><span data-stu-id="4828c-169">We can either implement the discrete GBM process from scratch or utilize one of many R packages which provide this functionality.</span></span> <span data-ttu-id="4828c-170">この問題の解決方法を提供する R パッケージ [*sde* (確率的微分方程式のシミュレーションと推論)](https://cran.r-project.org/web/packages/sde/index.html) を使用します。</span><span class="sxs-lookup"><span data-stu-id="4828c-170">We use the R package [*sde* (Simulation and Inference for Stochastic Differential Equations)](https://cran.r-project.org/web/packages/sde/index.html) which provides a method of solving this problem.</span></span> <span data-ttu-id="4828c-171">GBM 法には、履歴データに較正されるか、またはシミュレーション パラメーターとして与えられるパラメーターの集合が必要です。</span><span class="sxs-lookup"><span data-stu-id="4828c-171">The GBM method requires a set of parameters which are either calibrated to historic data or given as simulation parameters.</span></span> <span data-ttu-id="4828c-172">履歴データを使用し、シミュレーション (P0) の開始時に μ、σ、および株価を提供します。</span><span class="sxs-lookup"><span data-stu-id="4828c-172">We use the historic data, providing μ, σ and the stock prices at the beginning of the simulation (P0).</span></span>

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

<span data-ttu-id="4828c-173">いくつかのシミュレーション パスに対して、モンテカルロ シミュレーションを開始して潜在的エクスポージャーをモデル化する準備ができました。</span><span class="sxs-lookup"><span data-stu-id="4828c-173">We are now ready to start a Monte Carlo simulation to model the potential exposure for some number of simulation paths.</span></span> <span data-ttu-id="4828c-174">シミュレーションを 50 モンテカルロ パスと 256 タイム ステップに制限します。</span><span class="sxs-lookup"><span data-stu-id="4828c-174">We will limit the simulation to 50 Monte Carlo paths and 256 time steps.</span></span> <span data-ttu-id="4828c-175">シミュレーションをスケール アウトして R の並列化を利用するための準備として、モンテカルロ シミュレーション ループでは foreach 文を使用します。</span><span class="sxs-lookup"><span data-stu-id="4828c-175">In preparation for scaling out the simulation and taking advantage of parallelization in R, the Monte Carlo simulation loop uses a foreach statement.</span></span>

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

<span data-ttu-id="4828c-176">基礎となる MSFT 株式の価格をシミュレートしたところです。</span><span class="sxs-lookup"><span data-stu-id="4828c-176">We have now simulated the price of the underlying MSFT stock.</span></span> <span data-ttu-id="4828c-177">株式先渡のエクスポージャーを計算するには、プレミアムを減算し、エクスポージャーを正の値のみに制限します。</span><span class="sxs-lookup"><span data-stu-id="4828c-177">To calculate the exposure of the equity forward, we subtract the premium and limit the exposure to only positive values.</span></span>

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

<span data-ttu-id="4828c-178">次の 2 つの画像はシミュレーションの結果を示しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-178">The next two pictures show the result of the simulation.</span></span> <span data-ttu-id="4828c-179">最初の画像は、50 パスにわたる基礎株価のモンテカルロ シミュレーションを示しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-179">The first picture shows the Monte Carlo simulation of the underlying stock price for 50 paths.</span></span> <span data-ttu-id="4828c-180">2 番目の画像は、株式先渡のプレミアムを差し引いてエクスポージャーを正の値に制限した後の、株式先渡の基礎信用エクスポージャーを示しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-180">The second picture illustrates the underlying Credit Exposure for the equity forward after subtracting the premium of the equity forward and limiting the exposure to positive values.</span></span>


|       |    |
|---    |--- |
| <img src="./assets/fsi-risk-modeling/image3.png" width="400px" alt="Figure 1 - 50 Monte Carlo Paths"/> | <img src="./assets/fsi-risk-modeling/image4.png" width="400px" alt="Figure 2 - Credit Exposure for Equity Forward"/> |
| <span data-ttu-id="4828c-181">図 1 - 50 モンテカルロ パス</span><span class="sxs-lookup"><span data-stu-id="4828c-181">Figure 1 - 50 Monte Carlo Paths</span></span> | <span data-ttu-id="4828c-182">図 2 - 株式先渡の信用エクスポージャー</span><span class="sxs-lookup"><span data-stu-id="4828c-182">Figure 2 - Credit Exposure for Equity Forward</span></span> |


<span data-ttu-id="4828c-183">最後のステップでは、1 か月 0.95 分位点 PFE を次のコードによって計算します。</span><span class="sxs-lookup"><span data-stu-id="4828c-183">In the last step, the 1-Month 0.95 Quantile PFE is calculated by the following code.</span></span>

````R
# Calculate the PFE at each time step
df_pfe <- cbind(t,apply(pfe_mc,2,quantile,probs = instrument.pfeQuantile ))

resulting in the final PFE plot
plot(df_pfe, t = 'l', ylab = "Potential Future Exposure in USD", xlab = "time t in Years")
````

<img src="./assets/fsi-risk-modeling/image5.png" width="500px" alt="Potential Future Exposure for MSFT Equity Forward" /> 

<span data-ttu-id="4828c-184">図 3 MSFT 株式先渡の潜在的将来エクスポージャー</span><span class="sxs-lookup"><span data-stu-id="4828c-184">Figure 3 Potential Future Exposure for MSFT Equity Forward</span></span>

## <a name="using-azure-batch-with-r"></a><span data-ttu-id="4828c-185">R での Azure Batch の使用</span><span class="sxs-lookup"><span data-stu-id="4828c-185">Using Azure Batch with R</span></span> 

<span data-ttu-id="4828c-186">前述の R ソリューションは、Azure Batch に接続してリスク計算にクラウドを利用することができます。</span><span class="sxs-lookup"><span data-stu-id="4828c-186">The R solution described above can be connected to Azure Batch and leverage the cloud for risk calculations.</span></span> <span data-ttu-id="4828c-187">これは私たちのものと違い、並列計算に追加の労力をほとんど要しません。</span><span class="sxs-lookup"><span data-stu-id="4828c-187">This takes little extra effort for a parallel calculation such as ours.</span></span> <span data-ttu-id="4828c-188">Azure Batch への R の接続については、[Azure Batch を使用して並列 R シミュレーションを実行する](https://docs.microsoft.com/en-us/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)チュートリアルで詳しい情報を提供しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-188">The tutorial, [Run a parallel R simulation with Azure Batch](https://docs.microsoft.com/en-us/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely), provides detailed information on connecting R to Azure Batch.</span></span> <span data-ttu-id="4828c-189">以下では、Azure Batch に接続するプロセスのコードと概要を示し、簡略化した PFE 計算でクラウドの拡張機能を利用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="4828c-189">Below we show the code and summary of the process to connect to Azure Batch and how to take advantage of the extension to the cloud in a simplified PFE calculation.</span></span>

<span data-ttu-id="4828c-190">この例では、前述のものと同じモデルを扱います。</span><span class="sxs-lookup"><span data-stu-id="4828c-190">This example tackles the same model described earlier.</span></span> <span data-ttu-id="4828c-191">既に説明したように、この計算はパーソナル コンピューター上で実行できます。</span><span class="sxs-lookup"><span data-stu-id="4828c-191">As we have seen before, this calculation can run on our personal computer.</span></span> <span data-ttu-id="4828c-192">モンテカルロ パスの数を増やすか、より短いタイム ステップを使用すると、実行時間が大幅に長くなります。</span><span class="sxs-lookup"><span data-stu-id="4828c-192">Increases to the number of Monte Carlo paths or use of smaller time steps will result in much longer execution times.</span></span> <span data-ttu-id="4828c-193">ほとんどすべての R コードは変更されないままです。</span><span class="sxs-lookup"><span data-stu-id="4828c-193">Almost all of the R code will remain unchanged.</span></span> <span data-ttu-id="4828c-194">このセクションでは相違点に注目します。</span><span class="sxs-lookup"><span data-stu-id="4828c-194">We will highlight the differences in this section.</span></span>

<span data-ttu-id="4828c-195">モンテカルロ シミュレーションの各パスは Azure で実行されます。</span><span class="sxs-lookup"><span data-stu-id="4828c-195">Each path of the Monte Carlo simulation runs in Azure.</span></span> <span data-ttu-id="4828c-196">これが可能なのは、それぞれのパスが他のパスから独立しており、"きまりが悪いほど並列的な" 計算ができるからです。</span><span class="sxs-lookup"><span data-stu-id="4828c-196">We can do this because each path is independent of the others, giving us an "embarrassingly parallel" calculation.</span></span>

<span data-ttu-id="4828c-197">Azure Batch を使用するためには、計算でクラスターが使用できるようになる前に、基礎となるクラスターを定義してコード内でそれを参照します。</span><span class="sxs-lookup"><span data-stu-id="4828c-197">To use Azure Batch, we define the underlying cluster and reference it in the code before the cluster can be used in the calculations.</span></span> <span data-ttu-id="4828c-198">計算を実行するために、次の cluster.json 定義を使用します:</span><span class="sxs-lookup"><span data-stu-id="4828c-198">To run the calculations, we use the following cluster.json definition:</span></span>

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

<span data-ttu-id="4828c-199">このクラスター定義では、次の R コードがクラスターを利用します:</span><span class="sxs-lookup"><span data-stu-id="4828c-199">With this cluster definition, the following R code makes use of the cluster:</span></span>
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

<span data-ttu-id="4828c-200">最後に、foreach ステートメントを以前のものから更新し、doAzureParallel パッケージを使用するようにします。</span><span class="sxs-lookup"><span data-stu-id="4828c-200">Finally, we update the foreach statement from earlier to use the doAzureParallel package.</span></span> <span data-ttu-id="4828c-201">これは小さな変更であり、sde パッケージへの参照を追加し、%do% を %dopar% に変更します:</span><span class="sxs-lookup"><span data-stu-id="4828c-201">It's a minor change, adding a reference to the sde package and changing the %do% to %dopar%:</span></span>

````R
# Execute the MC simulation for the wiener process utilizing the GBM method from the sde package and extend the computation to the cloud
exposure_mc <- foreach(i = 1:nt, .combine = rbind, .packages = 'sde') %dopar% GBM(x = P0, r = mu, sigma = sigma, T = T, N = n)
rownames(exposure_mc) <- c()
````

<span data-ttu-id="4828c-202">各モンテカルロ シミュレーションはタスクとして Azure Batch に送信されます。</span><span class="sxs-lookup"><span data-stu-id="4828c-202">Each Monte Carlo simulation is submitted as a task to Azure Batch.</span></span> <span data-ttu-id="4828c-203">タスクはクラウドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="4828c-203">The task executes in the cloud.</span></span> <span data-ttu-id="4828c-204">アナリスト ワークベンチに返送される前に結果がマージされます。</span><span class="sxs-lookup"><span data-stu-id="4828c-204">Results are merged before being sent back to the analyst workbench.</span></span> <span data-ttu-id="4828c-205">要求された計算に必要なスケーリングと基礎インフラストラクチャをフル活用するために、複雑な処理と計算がクラウドで実行されます。</span><span class="sxs-lookup"><span data-stu-id="4828c-205">The heavy lifting and computations execute in the cloud to take full advantage of scaling and the underlying infrastructure required by the requested calculations.</span></span>

<span data-ttu-id="4828c-206">計算が完了したら、次の 1 つの命令を呼び出すことによって、追加のリソースを簡単にシャットダウンできます:</span><span class="sxs-lookup"><span data-stu-id="4828c-206">After the calculations have finished, the additional resources can easily be shut-down by invoking the following a single instruction:</span></span>

````R
# Stop the Cloud cluster
stopCluster(cluster)
````


## <a name="use-a-saas-offering"></a><span data-ttu-id="4828c-207">SaaS サービスを使用する</span><span class="sxs-lookup"><span data-stu-id="4828c-207">Use a SaaS offering</span></span>

<span data-ttu-id="4828c-208">最初の 2 つの例は、適切な評価モデルを開発するためにローカルとクラウドのインフラストラクチャを利用する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-208">The first two examples show how to utilize local and cloud infrastructure for developing an adequate valuation model.</span></span> <span data-ttu-id="4828c-209">このパラダイムは変わり始めました。</span><span class="sxs-lookup"><span data-stu-id="4828c-209">This paradigm has begun to shift.</span></span> <span data-ttu-id="4828c-210">オンプレミスのインフラストラクチャがクラウドベースの IaaS および PaaS サービスに形を変えたのと同じように、関連するリスク数値のモデリングはサービス指向のプロセスに形を変えつつあります。</span><span class="sxs-lookup"><span data-stu-id="4828c-210">In the same way that on-premises infrastructure has transformed into cloud-based IaaS and PaaS services, the modelling of relevant risk figures is transforming into a service-oriented process.</span></span>
<span data-ttu-id="4828c-211">今日のアナリストは 2 つの大きな課題に直面しています:</span><span class="sxs-lookup"><span data-stu-id="4828c-211">Today's analysts face two major challenges:</span></span>

1.  <span data-ttu-id="4828c-212">規制要件によってモデリングの要件が上がり、使用する計算容量が増加しています。</span><span class="sxs-lookup"><span data-stu-id="4828c-212">The regulatory requirements use increasing compute capacity to add to modeling requirements.</span></span> <span data-ttu-id="4828c-213">規制当局は、最新のリスク数値の報告をより頻繁に義務付けるようになってきています。</span><span class="sxs-lookup"><span data-stu-id="4828c-213">The regulators are asking for more frequent and up-to date risk figures.</span></span>

2.  <span data-ttu-id="4828c-214">これまで有機的に発展してきた既存のリスク インフラストラクチャは、新たな要件と高度化したリスク モデリングの迅速な実装にあたって課題を突き付けています。</span><span class="sxs-lookup"><span data-stu-id="4828c-214">The existing risk infrastructure has grown organically with time and creates challenges when implementing new requirements and more advanced risk modeling in an agile manner.</span></span>

<span data-ttu-id="4828c-215">クラウドベースのサービスは、必要な機能を提供し、リスク分析をサポートすることができます。</span><span class="sxs-lookup"><span data-stu-id="4828c-215">Cloud-based services can deliver the required functionality and support risk analysis.</span></span> <span data-ttu-id="4828c-216">このアプローチにはいくつかの利点があります:</span><span class="sxs-lookup"><span data-stu-id="4828c-216">This approach has some advantages:</span></span>

-   <span data-ttu-id="4828c-217">規制当局が要求する最も一般的なリスク計算は、規制下のすべての当事者が実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="4828c-217">The most common risk calculations required by the regulator must be implemented by everyone under the regulation.</span></span> <span data-ttu-id="4828c-218">専門のサービス事業者のサービスを利用することで、アナリストは、すぐに使えて規制要件に準拠したリスク計算の恩恵を受けます。</span><span class="sxs-lookup"><span data-stu-id="4828c-218">By utilizing services from a specialized service provider, the analyst benefits from ready to use, regulator-compliant risk calculations.</span></span> <span data-ttu-id="4828c-219">そのようなサービスには、市場リスク計算、取引先リスク計算、X-Value Adjustment (XVA: X 価値調整)、さらには Fundamental Review of Trading Book (FRTB: トレーディング勘定の抜本的な見直し) 計算が含まれます。</span><span class="sxs-lookup"><span data-stu-id="4828c-219">Such services may include market risk calculations, counterparty risk calculations, X-Value Adjustment (XVA), and even Fundamental Review of Trading Book (FRTB) calculations.</span></span>

-   <span data-ttu-id="4828c-220">これらのサービスは Web サービスを通じてインターフェイスを公開します。</span><span class="sxs-lookup"><span data-stu-id="4828c-220">These services expose their interfaces through web services.</span></span> <span data-ttu-id="4828c-221">こうしたその他のサービスによって既存のリスク インフラストラクチャを強化できます。</span><span class="sxs-lookup"><span data-stu-id="4828c-221">The existing risk infrastructure can be enhanced by these other services.</span></span>

<span data-ttu-id="4828c-222">私たちの例では、FRTB 計算のためにクラウドベースのサービスを呼び出したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="4828c-222">In our example, we want to invoke a cloud-based service for FRTB calculations.</span></span> <span data-ttu-id="4828c-223">これらのいくつかは [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely) に見つかります。</span><span class="sxs-lookup"><span data-stu-id="4828c-223">Several of these can be found on [AppSource](https://appsource.microsoft.com/?WT.mc_id=fsiriskmodelr-docs-scseely).</span></span> <span data-ttu-id="4828c-224">この記事では [Vector Risk](http://www.vectorrisk.com/) の試用版オプションを選択しました。</span><span class="sxs-lookup"><span data-stu-id="4828c-224">For this article we chose a trial option from [Vector Risk](http://www.vectorrisk.com/).</span></span> <span data-ttu-id="4828c-225">システムの改良を続けます。</span><span class="sxs-lookup"><span data-stu-id="4828c-225">We will continue to modify our system.</span></span> <span data-ttu-id="4828c-226">次に、サービスを使用して金利のリスク数値を計算します。</span><span class="sxs-lookup"><span data-stu-id="4828c-226">This time, we use a service to calculate the risk figure of interest.</span></span> <span data-ttu-id="4828c-227">このプロセスは次のステップで構成されます:</span><span class="sxs-lookup"><span data-stu-id="4828c-227">This process consists of the following steps:</span></span>

1.  <span data-ttu-id="4828c-228">適切なリスク サービスを正しいパラメーターを与えて呼び出します。</span><span class="sxs-lookup"><span data-stu-id="4828c-228">Call the relevant risk service and with the right parameters.</span></span>

2.  <span data-ttu-id="4828c-229">サービスが計算を終了するまで待ちます。</span><span class="sxs-lookup"><span data-stu-id="4828c-229">Wait until the service finishes the calculation.</span></span>

3.  <span data-ttu-id="4828c-230">結果を取得してリスク分析に組み込みます。</span><span class="sxs-lookup"><span data-stu-id="4828c-230">Retrieve and incorporate the results into the risk analysis.</span></span>

<span data-ttu-id="4828c-231">R コードに変換されます。この R コードは、準備された入力テンプレートにある、必要な入力値の定義によって拡張できます。</span><span class="sxs-lookup"><span data-stu-id="4828c-231">Translated into R code, our R code can be enhanced by the definition of the required input values from a prepared input template.</span></span>

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


<span data-ttu-id="4828c-232">次に、Web サービスを呼び出す必要があります。</span><span class="sxs-lookup"><span data-stu-id="4828c-232">Next, we need to call the web service.</span></span> <span data-ttu-id="4828c-233">このケースでは、StartCreditExposure メソッドを呼び出して計算を開始します。</span><span class="sxs-lookup"><span data-stu-id="4828c-233">In this case, we call the StartCreditExposure method to trigger the calculation.</span></span> <span data-ttu-id="4828c-234">API のエンドポイントを *endpoint* という名前の変数に格納します。</span><span class="sxs-lookup"><span data-stu-id="4828c-234">We store the endpoint for the API in a variable named *endpoint*.</span></span>

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

<span data-ttu-id="4828c-235">計算が終了したら、結果を取得します。</span><span class="sxs-lookup"><span data-stu-id="4828c-235">Once the calculations have finished, we retrieve the results.</span></span>

````R
# get back high level results
result <- POST( paste(endpoint, "GetCreditExposureResults", sep = ""), 
                authenticate(username, password, type = "basic"),
                content_type("application/json"),
                add_headers(`Ocp-Apim-Subscription-Key` = api_key),
               body = sprintf('{"getCreditExposureResults": {"token":"DataSource=Production;Organisation=Microsoft", "ticket": "%s"}}', ticket), encode = "raw")

result <- content(result)
````

<span data-ttu-id="4828c-236">アナリストは、受け取った結果を使用して作業の続きを行います。</span><span class="sxs-lookup"><span data-stu-id="4828c-236">This leaves the analyst to continue with the results received.</span></span> <span data-ttu-id="4828c-237">関心のある適切なリスク数値が結果から抽出され、プロットされます。</span><span class="sxs-lookup"><span data-stu-id="4828c-237">The relevant risk figures of interest are extracted from the results and plotted.</span></span>

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


<span data-ttu-id="4828c-238">結果のプロットは次のようになります:</span><span class="sxs-lookup"><span data-stu-id="4828c-238">The resulting plots look like this:</span></span>

|       |     |
|----    |---- |
| <img src="./assets/fsi-risk-modeling/image6.png" width="400px" alt="Figure 4 - Credit Exposure for MSFT Equity Forward - Calculated with a Cloud Based Risk Engine"/> | <img src="./assets/fsi-risk-modeling/image7.png" width="400px" alt="Figure 5 - Potential Future Exposure for MSFT Equity Forward - Calculated with a Cloud  Based Risk Engine" /> |
| <span data-ttu-id="4828c-239">図 4 - MSFT 株式先渡の信用エクスポージャー -</span><span class="sxs-lookup"><span data-stu-id="4828c-239">Figure 4 - Credit Exposure for MSFT Equity Forward -</span></span> <br/><span data-ttu-id="4828c-240">クラウドベースのリスク エンジンで計算</span><span class="sxs-lookup"><span data-stu-id="4828c-240">Calculated with a Cloud Based Risk Engine</span></span> | <span data-ttu-id="4828c-241">図 5 - MSFT 株式先渡の潜在的将来エクスポージャー -</span><span class="sxs-lookup"><span data-stu-id="4828c-241">Figure 5 - Potential Future Exposure for MSFT Equity Forward -</span></span> <br/> <span data-ttu-id="4828c-242">クラウドベースのリスク エンジンで計算</span><span class="sxs-lookup"><span data-stu-id="4828c-242">Calculated with a Cloud  Based Risk Engine</span></span> |



## <a name="next-steps"></a><span data-ttu-id="4828c-243">次の手順</span><span class="sxs-lookup"><span data-stu-id="4828c-243">Next Steps</span></span>

<span data-ttu-id="4828c-244">クラウドの計算インフラストラクチャや SaaS ベースのリスク分析サービスへの柔軟なアクセスによって、投資業や保険業のリスク アナリストはスピードと機敏性を向上させることができます。</span><span class="sxs-lookup"><span data-stu-id="4828c-244">Flexible access to the cloud through compute infrastructure and SaaS-based risk analysis services can deliver improvements in speed and agility for risk analysts working in capital markets and insurance.</span></span> <span data-ttu-id="4828c-245">この記事では、リスク アナリストが知っているツールを使用して Azure やその他のサービスを使用する方法の例を説明しました。</span><span class="sxs-lookup"><span data-stu-id="4828c-245">In this article we worked through an example which illustrates how to use Azure and other services using tools risk analysts know.</span></span> <span data-ttu-id="4828c-246">Azure の機能を活用して、自らの手でリスク モデルの作成と強化に取り組んでみてください。</span><span class="sxs-lookup"><span data-stu-id="4828c-246">Try taking advantage of Azure's capabilities as you create and enhance your risk models.</span></span>

### <a name="tutorials"></a><span data-ttu-id="4828c-247">チュートリアル</span><span class="sxs-lookup"><span data-stu-id="4828c-247">Tutorials</span></span>


-   <span data-ttu-id="4828c-248">R 開発者: [Azure Batch での並列 R シミュレーションの実行](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)</span><span class="sxs-lookup"><span data-stu-id="4828c-248">R Developers: [Run a parallel R simulation with Azure Batch](https://docs.microsoft.com/azure/batch/tutorial-r-doazureparallel?WT.mc_id=fsiriskmodelr-docs-scseely)</span></span>

-   [<span data-ttu-id="4828c-249">基本的な R コマンドと RevoScaleR 関数: 25 の一般的な例</span><span class="sxs-lookup"><span data-stu-id="4828c-249">Basic R commands and RevoScaleR functions: 25 common examples</span></span>](https://docs.microsoft.com/machine-learning-server/r/tutorial-r-to-revoscaler?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [<span data-ttu-id="4828c-250">RevoScaleR を使用したデータの視覚化と分析</span><span class="sxs-lookup"><span data-stu-id="4828c-250">Visualize and analyze data using RevoScaleR</span></span>](https://docs.microsoft.com/machine-learning-server/r/tutorial-revoscaler-data-model-analysis?WT.mc_id=fsiriskmodelr-docs-scseely)

-   [<span data-ttu-id="4828c-251">HDInsight での ML Services とオープン ソース R の機能の概要</span><span class="sxs-lookup"><span data-stu-id="4828c-251">Introduction to ML Services and open-source R capabilities on HDInsight</span></span>](https://docs.microsoft.com/azure/hdinsight/r-server/r-server-overview?WT.mc_id=fsiriskmodelr-docs-scseely)

<span data-ttu-id="4828c-252">_本記事の執筆者は Dr.Darko Mocelj および Rupert Nicolay です。_</span><span class="sxs-lookup"><span data-stu-id="4828c-252">_This article was authored by Dr. Darko Mocelj and Rupert Nicolay._</span></span>