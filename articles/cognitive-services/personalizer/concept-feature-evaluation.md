---
title: 功能評估 - 個人化
titleSuffix: Azure Cognitive Services
description: 當您從 Azure 門戶在個人化程式資源中運行評估時，個人化程式會提供有關上下文和操作的功能影響模型的資訊。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242420"
---
# <a name="feature-evaluation"></a>功能評估

當您從[Azure 門戶](https://portal.azure.com)在個人化程式資源中運行評估時，個人化程式會提供有關上下文和操作的功能影響模型的資訊。 

這對於以下用途：

* 想像一下您可以使用的其他功能，從模型中更重要的功能中獲得靈感。
* 查看哪些功能並不重要，並可能刪除它們或進一步分析可能影響使用的內容。
* 就新內容或產品值得帶入目錄向編輯或整理團隊提供指導。
* 解決向個人化程式發送功能時發生的常見問題和錯誤。

更重要的要素在模型中具有更強的權重。 由於這些功能具有更強的重量，因此當個人儀獲得更高的獎勵時，它們往往會存在。

## <a name="getting-feature-importance-evaluation"></a>獲取功能重要性評估

要查看要素重要性結果，必須運行評估。 評估基於評估期間觀察到的特徵名稱創建人可讀特徵標籤。

有關功能重要性的結果資訊表示當前個人化線上模型。 評價分析了在評估期結束時保存的模型在評估期間完成的所有培訓後，採用當前線上學習政策所保存的模型的特徵重要性。 

功能重要性結果不代表在評估期間測試或創建的其他策略和模型。  評估不包括在評估期結束後發送給個人伺服器的功能。

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>如何解釋特徵重要性評價

個人化程式通過創建具有類似重要性的功能的"組"來評估功能。 可以說，一個組比其他人具有整體上更強的重要性，但在組中，要素的順序按字母順序排列。

有關每個功能的資訊包括：

* 功能是來自上下文還是操作。
* 功能鍵和值。

例如，霜淇淋店訂購應用程式可能會看到"CoNtext.Weather：Hot"作為一個非常重要的功能。

個人化顯示功能的相關性，如果同時考慮，這些功能會產生更高的回報。

例如，您可能會看到"上下文.天氣：熱*與*行動.功能表項目目：冰霜"以及"上下文.天氣：冷*與*行動.功能表項目目：暖茶：

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>您可以根據功能評估執行的操作

### <a name="imagine-additional-features-you-could-use"></a>想像一下您可以使用的其他功能

從模型中更重要的功能獲得靈感。 例如，如果您在視頻移動應用中看到"CoNtext.MobileBattery：低"，您可能會認為該連線類型也可能使客戶選擇在另一個視訊短片上看到一個視訊短片，然後將有關連線類型和頻寬的功能添加到你的應用中。

### <a name="see-what-features-are-not-important"></a>查看哪些功能並不重要

可能會刪除不重要的功能或進一步分析可能影響使用的內容。 由於多種原因，功能可能排名較低。 一種可能是，真正的功能不會影響使用者的行為。 但它也可能意味著該功能對使用者並不明顯。 

例如，視頻網站可以看到"Action.Video解析度=4k"是一個低重要性功能，與使用者研究相矛盾。 原因可能是應用程式甚至沒有提及或顯示視頻解析度，因此使用者不會基於它更改其行為。

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>為編輯或整理團隊提供指導

提供有關新內容或產品的指導，值得帶入目錄中。 個人化工具被設計為增強人類洞察力和團隊的工具。 這樣做的一種方法是向編輯組提供有關推動行為的產品、文章或內容的資訊。 例如，視頻應用程式場景可能顯示有一個重要功能稱為"Action.VideoEntities.Cat：true"，這促使編輯團隊引入更多貓視頻。

### <a name="troubleshoot-common-problems-and-mistakes"></a>排除常見問題和錯誤

可以通過更改應用程式代碼來修復常見問題和錯誤，這樣它就不會向個人化程式發送不當或格式不正確的功能。 

發送功能時的常見錯誤包括：

* 發送個人識別資訊 （PII）。 特定于一個人的 PII（如姓名、電話號碼、信用卡號、IP 位址）不應與個人代卡器一起使用。 如果應用程式需要跟蹤使用者，請使用非標識 UUID 或其他一些 UserID 號碼。 在大多數情況下，這也是有問題的。
* 對於大量使用者，每個使用者的交互不太可能比所有使用者的交互量更重要，因此發送使用者 IT（即使非 PII）可能會為模型增加比值更多的雜訊。
* 將日期和時間欄位作為精確時間戳記而不是壯舉時間值發送。 具有上下文.時間戳記.Day_星期一或"上下文.時間戳記.小時"="13"等功能更有用。 每個要素最多有 7 或 24 個要素值。 但"CoNtext.timeStamp"："1985-04-12T23：20：50.52Z"是如此精確，以至於無法從中吸取教訓，因為它永遠不會再次發生。

## <a name="next-steps"></a>後續步驟

通過個人化程式瞭解[可擴充性和性能](concepts-scalability-performance.md)。

