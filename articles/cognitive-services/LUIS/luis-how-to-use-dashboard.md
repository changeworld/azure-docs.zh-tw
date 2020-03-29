---
title: 儀表板 - 語言理解 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用已訓練的應用的儀表板修復意圖和實體。 儀表板顯示整體應用資訊，並突出顯示應修復的意圖。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73888205"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>如何使用儀表板改進應用

使用示例陳述時，查找並修復已訓練應用的意圖問題。 儀表板顯示整體應用資訊，並突出顯示應修復的意圖。 

查看儀表板分析是一個反覆運算過程，在更改和改進模型時重複。

對於在意圖中沒有任何示例陳述的應用（稱為_僅模式_應用），此頁面將沒有相關的分析。 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>可以從儀表板修復哪些問題？

儀表板中解決的三個問題包括：

|問題|圖表顏色|說明|
|--|--|--|
|資料不平衡|-|當示例陳述的數量差異很大時，就會發生這種情況。 所有意圖都需要具有_大致相同_的例陳述數 - "無"意圖除外。 它應該只有 10%-15% 的說出在應用程式中。<br><br> 如果資料不平衡，但意圖準確性高於特定閾值，則此不平衡不報告為問題。<br><br>**從此問題開始 - 它可能是其他問題的根本原因。**|
|預測不明確|橙色|當最高意圖和下一個意圖的分數足夠接近，以至於由於[負採樣](luis-how-to-train.md#train-with-all-data)或添加到意圖中的例子陳述，他們可能會在下一次訓練中翻轉時，就會發生這種情況。 |
|不正確的預測|紅色|當未預測標記的意圖（其意圖中的意圖）時，將發生這種情況。|

正確的預測用藍色表示。

儀表板顯示這些問題，並告訴您哪些意圖受到影響，並建議您應該做些什麼來改進應用。 

## <a name="before-app-is-trained"></a>在訓練應用之前 

在訓練應用之前，儀表板不包含任何修復建議。 訓練你的應用以查看這些建議。  

## <a name="check-your-publishing-status"></a>檢查發佈狀態

**發佈狀態**卡包含有關活動版本上次發佈的資訊。 

檢查活動版本是否為要修復的版本。 

![儀表板顯示應用的外部服務、已發佈的區域和聚合終結點命中。](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

這還顯示任何外部服務、已發佈區域和聚合終結點命中。 

## <a name="review-training-evaluation"></a>審核培訓評估

**培訓評估**卡包含應用整體準確性的匯總（按區域）。 分數表示意圖品質。 

![培訓評估卡包含有關應用總體準確性的第一個資訊區域。](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

圖表指示正確預測的意圖和具有不同顏色的問題區域。 當您使用建議改進應用時，此分數會增加。 

建議的修補程式按問題類型分隔開來，對應用來說是最重要的。 如果您希望查看和修復每個意圖的問題，請使用頁面底部**[帶有錯誤卡的"意圖](#intents-with-errors)**"。 

每個問題區域都有需要修復的意圖。 選擇意向名稱時，將打開 **"意向"** 頁，並應用一個篩選器到陳述。 此篩選器允許您關注導致問題的話語。

### <a name="compare-changes-across-versions"></a>比較不同版本的更改

在更改應用之前，請創建新版本。 在新版本中，對意圖的示例陳述進行建議的更改，然後再次訓練。 在儀表板頁的 **"培訓評估**卡"上，使用 **"顯示已訓練版本的更改**"來比較更改。 

![比較不同版本的更改](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>通過添加或編輯示例陳述和重新訓練來修復版本

修復應用的主要方法是添加或編輯示例陳述並重新訓練。 新的或改變的話語需要遵循[各種話語](luis-concept-utterance.md)的指導方針。

添加示例陳述應由：

* 高度理解不同意圖中的話語。
* 知道一個意圖中的話語如何與另一個意圖混淆。
* 能夠決定是否應將兩個意圖折疊成一個意圖（通常相互混淆）。 如果是這種情況，則必須使用實體拉出不同的資料。

### <a name="patterns-and-phrase-lists"></a>模式和片語清單

分析頁不指示何時使用[模式](luis-concept-patterns.md)或[片語清單](luis-concept-feature.md)。 如果確實添加它們，它可以説明進行不正確或不明確的預測，但無助于資料不平衡。 

### <a name="review-data-imbalance"></a>查看資料不平衡

從此問題開始 - 它可能是其他問題的根本原因。

**資料不平衡**意圖清單顯示需要更多陳述才能糾正資料不平衡的意圖。 

**要解決此問題**：

* 向意圖添加更多話語，然後再次訓練。 

除非儀表板上建議，否則不要向"無"意圖添加陳述。

> [!Tip]
> 使用頁面上的第三部分"**每個意圖**與 **"陳述（數位）"** 設置，作為一個快速的視覺化指南，其中意圖需要更多的陳述。  
    ![使用"資料（數位）"查找資料不平衡的意圖。](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>查看不正確的預測

**不正確的預測**意圖清單顯示具有陳述的意圖，這些陳述用作特定意圖的示例，但針對不同意圖進行預測。 

**要解決此問題**：

* 編輯陳述以更具體地針對意圖，然後再次訓練。
* 如果話語過於緊密對齊，則合併意圖，然後重新訓練。

### <a name="review-unclear-predictions"></a>查看不明確的預測

**不明確的預測**意圖清單顯示具有預測分數的意向，這些陳述距離最接近的對手不夠遠，由於[負採樣](luis-how-to-train.md#train-with-all-data)，該陳述的最高意圖可能會在下一次訓練中發生變化。

**解決此問題**;

* 編輯陳述以更具體地針對意圖，然後再次訓練。
* 如果話語過於緊密對齊，則合併意圖，然後重新訓練。

## <a name="utterances-per-intent"></a>每個意圖的意向

此卡片顯示整個意圖中的整體應用運行狀況。 當您修復意圖並重新訓練時，請繼續查看此卡片中的問題。

下圖顯示了一個平衡良好的應用程式，幾乎沒有要修復的問題。

![下圖顯示了一個平衡良好的應用程式，幾乎沒有要修復的問題。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

下圖顯示了一個平衡不佳的應用程式，有許多問題需要解決。

![下圖顯示了一個平衡良好的應用程式，幾乎沒有要修復的問題。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

將滑鼠懸停在每個意圖的欄上，以獲取有關意圖的資訊。 

![下圖顯示了一個平衡良好的應用程式，幾乎沒有要修復的問題。](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

使用**按"按排序"** 功能按問題類型排列意圖，以便您可以專注于問題中最有問題的意圖。 

## <a name="intents-with-errors"></a>具有錯誤的意圖

此卡允許您查看特定意圖的問題。 此卡的預設視圖是問題最大的意圖，因此您知道將精力集中在何處。

![帶有錯誤卡片的意向允許您查看特定意圖的問題。 預設情況下，該卡被過濾到最有問題的意圖中，因此您知道將精力集中在何處。](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

頂部環圈圖顯示了三種問題類型的意圖問題。 如果三種問題類型存在問題，則每種類型都有其自己的圖表，以及任何競爭對手的意圖。 

### <a name="filter-intents-by-issue-and-percentage"></a>按問題和百分比篩選意圖

卡片的這一部分允許您查找超出錯誤閾值的示例陳述。 理想情況下，您希望正確的預測很重要。 該百分比由業務和客戶驅動。 

確定您對業務感到滿意的閾值百分比。 

篩選器允許您查找具有特定問題的意圖：

|Filter|建議百分比|目的|
|--|--|--|
|大多數問題意圖|-|**從這裡開始**- 修復此意圖中的話語將比其他修復程式更改進應用程式。|
|更正以下預測|60%|這是所選意圖中正確但置信度低於閾值的表述的百分比。 |
|上述預測不明確|15%|這是所選意圖中與最接近的競爭對手意圖混淆的話語的百分比。|
|上面的預測不正確|15%|這是所選意圖中錯誤預測的陳述的百分比。 |

### <a name="correct-prediction-threshold"></a>正確的預測閾值

什麼是自信的預測信心評分？ 在應用開發之初，60% 可能是您的目標。 使用**下面的正確預測**，百分比為 60%， 以查找所選意圖中需要修復的任何陳述。

### <a name="unclear-or-incorrect-prediction-threshold"></a>預測閾值不明確或不正確

這兩個篩選器允許您在所選意圖中查找超出閾值的表述。 您可以將這兩個百分比視為錯誤百分比。 如果您對預測的 10-15% 錯誤率感到滿意，則將篩選器閾值設置為 15%，以查找高於此值的所有陳述。 

## <a name="next-steps"></a>後續步驟

* [管理您的 Azure 資源](luis-how-to-azure-subscription.md)
