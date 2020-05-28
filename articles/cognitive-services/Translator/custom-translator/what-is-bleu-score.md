---
title: 什麼是 BLEU 分數？ - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: BLEU 是針對相同來源句子的機器翻譯和人類建立的參考翻譯之間的差異測量。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: b35dce5a2b572e5a3f11601d5e67efb2da02cdec
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997055"
---
# <a name="what-is-a-bleu-score"></a>什麼是 BLEU 分數？

[BLEU (雙語評估互補)](https://en.wikipedia.org/wiki/BLEU) 是指對於相同來源句子的自動翻譯與一或多個人工參考翻譯之間的差異所做的量測。

## <a name="scoring-process"></a>評分程序

BLEU 演算法會比較自動翻譯的連續片語與它在參考翻譯中找到的連續片語，並以加權方式計算相符項數目。 這些相符項與位置無關。 相符程度越高，與參考翻譯的相似度就越高，分數也就越高。 可理解度和文法的正確性不會列入考量。

## <a name="how-bleu-works"></a>BLEU 的運作方式為何？

BLEU 的優點是，它會將測試主體的個別句子判斷錯誤平均地相互關聯，而不是嘗試為每個句子設計精確的人為判斷。

如需 BLEU 分數的詳細討論，請參閱[這裡](https://youtu.be/-UqDljMymMg)。

BLEU 結果有很大一部分取決於您領域的廣度、測試資料與定型和調整資料的一致性，以及可用於定型的資料量。 如果您的模型已在較窄的領域中定型，且您的定型資料與測試資料相一致，則可望會有較高的 BLEU 分數。

>[!NOTE]
>以相同的測試集、相同的語言組和相同的 MT 引擎比較 BLEU 結果時，BLEU 分數的比較才有正當性。 來自不同測試集的 BLEU 分數必定是不同的。
