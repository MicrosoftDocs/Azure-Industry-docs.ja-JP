---
title: IoT データからのアクションにつながる分析情報抽出の概要
description: Azure サービスを使用して IoT データから分析情報を抽出します。 ソリューション ガイドの概要。
author: ercenk
ms.author: ercenk
manager: gmarchet
ms.service: industry
ms.topic: article
ms.date: 11/28/2019
ms.openlocfilehash: 8ca31501d976e0b162735b7f55e2db7fae850776
ms.sourcegitcommit: a6eefa2a605c9e1c5015ae107c9847b18d415746
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540418"
---
# <a name="extracting-actionable-insights-from-iot-data"></a><span data-ttu-id="c2103-104">IoT データからアクションにつながる分析情報を抽出する</span><span class="sxs-lookup"><span data-stu-id="c2103-104">Extracting actionable insights from IoT data</span></span>

<span data-ttu-id="c2103-105">製造業者が注目しているのは最新の工場の現場です。デジタル ツールを使って製造と工程を改善しようとしているのです。</span><span class="sxs-lookup"><span data-stu-id="c2103-105">Manufacturers are looking at a modern factory floor with an eye to improving production and processes using digital tools.</span></span> <span data-ttu-id="c2103-106">そして、最大の共通点がモノのインターネット (IoT) です。</span><span class="sxs-lookup"><span data-stu-id="c2103-106">And the greatest common denominator is the Internet of Things (IoT).</span></span> <span data-ttu-id="c2103-107">機械がデータを出力し始めてから、もうかなり経っています。</span><span class="sxs-lookup"><span data-stu-id="c2103-107">Machines have been emitting data for some time now.</span></span> <span data-ttu-id="c2103-108">新しい機械が、今後さらに多くのデータを提供していくのは間違いありません。</span><span class="sxs-lookup"><span data-stu-id="c2103-108">New machines will doubtlessly be providing even more data.</span></span>
<span data-ttu-id="c2103-109">しかし、データがあるということは最初の一歩に過ぎません。</span><span class="sxs-lookup"><span data-stu-id="c2103-109">But having the data is just the first step.</span></span> <span data-ttu-id="c2103-110">このソリューション ガイドでは、予測メンテナンスなどのアクションに向けてデータを使えるようなるまでの道筋を紹介します。</span><span class="sxs-lookup"><span data-stu-id="c2103-110">Our solution guide provides a path to using the data for actions such as predictive maintenance.</span></span>

<span data-ttu-id="c2103-111">簡単に言うと、このソリューションでは、モノ (データ)、分析情報 (高度な分析を利用)、アクションの 3 つの主要ステップに重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="c2103-111">In brief, the solution focuses on three major steps: things (data), insights (via advanced analytics) and actions.</span></span>

![モノから分析情報、そしてアクションへ。](assets/extracting-insights-from-iot/things-insights-actions.png)

<span data-ttu-id="c2103-113">データがあるというのはすばらしいことですが、データは原材料に過ぎません。</span><span class="sxs-lookup"><span data-stu-id="c2103-113">Having data is great, but data is just raw materials.</span></span> <span data-ttu-id="c2103-114">アクションにつながるのは、分析によって得られた情報です。</span><span class="sxs-lookup"><span data-stu-id="c2103-114">Insights through analysis guides your actions.</span></span>

![ラムダ アーキテクチャ](assets/extracting-insights-from-iot/lambda-architecture.png)

<span data-ttu-id="c2103-116">これは柔軟性に優れたアーキテクチャで、高スループットでデータを取り込んで分析できます。</span><span class="sxs-lookup"><span data-stu-id="c2103-116">The architecture is flexible and lets you ingest and analyze data at high throughput.</span></span> <span data-ttu-id="c2103-117">ニーズの拡大に伴い、コンポーネントをスケールアップ/スケールダウンして、需要に対応したりコストを削減したりすることもできます。</span><span class="sxs-lookup"><span data-stu-id="c2103-117">As your needs grow, the components can also scale up and down to meet demand or save cost.</span></span> <span data-ttu-id="c2103-118">このガイドではアーキテクチャ (Azure Event Hubs を使用した取り込み、分析クライアント用の Power BI など) の実装に関する推奨事項についても紹介しています。</span><span class="sxs-lookup"><span data-stu-id="c2103-118">The guide also shows you recommendations for implementing the architecture—for example, using Azure Event Hubs to ingest, and Power BI for an analytics client.</span></span>

[<span data-ttu-id="c2103-119">IoT データからアクションにつながる分析情報を抽出する</span><span class="sxs-lookup"><span data-stu-id="c2103-119">Extracting actionable insights from IoT data</span></span>](./extracting-insights-from-iot-data.md)
