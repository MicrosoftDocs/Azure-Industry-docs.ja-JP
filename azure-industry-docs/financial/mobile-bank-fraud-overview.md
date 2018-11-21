---
title: モバイル バンクでの不正行為
author: dstarr
ms.author: dastarr
ms.date: 09/26/2018
ms.topic: article
ms.service: industry
description: モバイル バンキングで発生する不正行為の概要
ms.openlocfilehash: f1ddd07428d2abf63337d63e64b7d703a4cc345d
ms.sourcegitcommit: 76f2862adbec59311b5888e043a120f89dc862af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2018
ms.locfileid: "51654149"
---
# <a name="mobile-bank-fraud"></a><span data-ttu-id="e16e2-103">モバイル バンクでの不正行為</span><span class="sxs-lookup"><span data-stu-id="e16e2-103">Mobile Bank Fraud</span></span>

<span data-ttu-id="e16e2-104">これまで銀行詐欺行為はルール エンジンを使って検出していました。つまり、一連の二者択一の質問を行うことで、"これは不正なトランザクションですか" という質問に対する、単純な "はい" または "いいえ" という回答を導き出していました。</span><span class="sxs-lookup"><span data-stu-id="e16e2-104">In the past, bank fraud detection was accomplished using a rules engine, which went down a set of binary questions to arrive at a simple “yes or no” to the question: is this a fraudulent transaction?</span></span> <span data-ttu-id="e16e2-105">今の銀行取引はほとんどがオンラインで行われ、サイバー犯罪者が盗みに使う方法も高度です。</span><span class="sxs-lookup"><span data-stu-id="e16e2-105">Today most banking is done online, and cybercriminals are using sophisticated ways to steal.</span></span> <span data-ttu-id="e16e2-106">一度アカウントが侵害されると、わずか数分で大量の情報が盗まれます。</span><span class="sxs-lookup"><span data-stu-id="e16e2-106">And once an account is compromised, thieves need only a few minutes to steal thousands.</span></span> <span data-ttu-id="e16e2-107">不正行為の検出で効果を上げるには、その検出をほぼリアルタイムで行う必要があり、ルール エンジンでは不十分です。</span><span class="sxs-lookup"><span data-stu-id="e16e2-107">Fraud detection must happen in near-real-time to be effective, and a rules engine will not suffice.</span></span> <span data-ttu-id="e16e2-108">新しい脅威に対応するには、代わりに、高度な Data Analytics などの新しいツールを機械学習と組み合わせます。</span><span class="sxs-lookup"><span data-stu-id="e16e2-108">Instead, new tools such as advanced data analytics are combined with machine learning to adapt to new threats.</span></span> <span data-ttu-id="e16e2-109">不快感を与えないと思われるデータを使って、ユーザーが本物かどうかを判断できます。たとえば、"このデバイスまたはコンシューマーはどのくらい頻度でログインしていますか" といった質問</span><span class="sxs-lookup"><span data-stu-id="e16e2-109">Data that seems innocuous can be used to determine if a user is real—questions such as "how frequently does this device or consumer log in?"</span></span> <span data-ttu-id="e16e2-110">を使用することによって不正行為を特定できます。</span><span class="sxs-lookup"><span data-stu-id="e16e2-110">can be used to spot fraud.</span></span>

<span data-ttu-id="e16e2-111">モバイル バンクの不正行為に関するソリューション ガイドでは、その課題と、不正行為検出ソリューション、つまり "はい" または "いいえ" の答えを 2 秒で出すソリューションの構築に使用できる戦略について説明します。</span><span class="sxs-lookup"><span data-stu-id="e16e2-111">Our solution guide on mobile bank fraud walks through the challenge and the strategies that can be used to build a fraud-detection solution—one that answers yes or no in two seconds.</span></span>