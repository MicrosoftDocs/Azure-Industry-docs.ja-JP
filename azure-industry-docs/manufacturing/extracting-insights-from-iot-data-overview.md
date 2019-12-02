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
# <a name="extracting-actionable-insights-from-iot-data"></a>IoT データからアクションにつながる分析情報を抽出する

製造業者が注目しているのは最新の工場の現場です。デジタル ツールを使って製造と工程を改善しようとしているのです。 そして、最大の共通点がモノのインターネット (IoT) です。 機械がデータを出力し始めてから、もうかなり経っています。 新しい機械が、今後さらに多くのデータを提供していくのは間違いありません。
しかし、データがあるということは最初の一歩に過ぎません。 このソリューション ガイドでは、予測メンテナンスなどのアクションに向けてデータを使えるようなるまでの道筋を紹介します。

簡単に言うと、このソリューションでは、モノ (データ)、分析情報 (高度な分析を利用)、アクションの 3 つの主要ステップに重点を置いています。

![モノから分析情報、そしてアクションへ。](assets/extracting-insights-from-iot/things-insights-actions.png)

データがあるというのはすばらしいことですが、データは原材料に過ぎません。 アクションにつながるのは、分析によって得られた情報です。

![ラムダ アーキテクチャ](assets/extracting-insights-from-iot/lambda-architecture.png)

これは柔軟性に優れたアーキテクチャで、高スループットでデータを取り込んで分析できます。 ニーズの拡大に伴い、コンポーネントをスケールアップ/スケールダウンして、需要に対応したりコストを削減したりすることもできます。 このガイドではアーキテクチャ (Azure Event Hubs を使用した取り込み、分析クライアント用の Power BI など) の実装に関する推奨事項についても紹介しています。

[IoT データからアクションにつながる分析情報を抽出する](./extracting-insights-from-iot-data.md)
