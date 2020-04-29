---
title: 儀表板-Language Understanding-LUIS
titleSuffix: Azure Cognitive Services
description: 使用您已定型的應用程式儀表板來修正意圖和實體。 儀表板會顯示整體的應用程式資訊，並醒目提示應修正的意圖。
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73888205"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>如何使用儀表板來改善您的應用程式

當您使用範例語句時，尋找並修正已定型應用程式意圖的問題。 儀表板會顯示整體的應用程式資訊，並醒目提示應修正的意圖。 

審查儀表板分析是一種反復的程式，當您變更並改善模型時，請重複此作業。

在意圖中沒有任何範例語句的應用程式，此頁面不會有相關的分析，稱為_僅限模式_的應用程式。 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>哪些問題可以從儀表板修正？

在儀表板中解決的三個問題如下：

|問題|圖表色彩|說明|
|--|--|--|
|資料不平衡|-|當範例語句數量大幅變化時，就會發生這種情況。 所有意圖都必須有_大致_相同的範例語句數，但 None 意圖除外。 應用程式中的總語句數量應該只有 10%-15%。<br><br> 如果資料已不平衡，但意圖精確度高於特定閾值，則不會將此不平衡回報為問題。<br><br>**從這個問題開始，這可能是其他問題的根本原因。**|
|不清楚的預測|橙色|當最上層意圖和下一個意圖的分數已接近下一次定型時，就會發生這種情況，因為已將[負面取樣](luis-how-to-train.md#train-with-all-data)或更多範例語句新增至意圖。 |
|不正確的預測|紅色|當範例語句未針對標示的意圖（其所在的意圖）進行預測時，就會發生這種情況。|

正確的預測以藍色表示。

儀表板會顯示這些問題，並告訴您哪些意圖會受到影響，並建議您應採取哪些動作來改善應用程式。 

## <a name="before-app-is-trained"></a>在應用程式定型之前 

在您訓練應用程式之前，儀表板不會包含任何修正的建議。 訓練您的應用程式以查看這些建議。  

## <a name="check-your-publishing-status"></a>檢查您的發行狀態

**發行狀態**卡包含作用中版本上次發佈的相關資訊。 

檢查作用中版本是否為您想要修正的版本。 

![儀表板會顯示應用程式的外部服務、已發行的區域，以及匯總的端點叫用。](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

這也會顯示任何外部服務、已發行的區域，以及匯總的端點叫用。 

## <a name="review-training-evaluation"></a>審查訓練評估

**定型評估**卡包含應用程式整體精確度的匯總摘要（依區域）。 分數表示意圖品質。 

![定型評估卡包含應用程式整體精確度的第一個資訊區域。](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

圖表會以不同的色彩指出正確預測的意圖和問題區域。 當您以建議改善應用程式時，此分數會增加。 

建議的修正是以問題類型分隔，而且對您的應用程式而言是最重要的。 如果您想要針對每個意圖來檢查並修正問題，請使用頁面底部的 [**[具有錯誤的意圖](#intents-with-errors)**] 卡片。 

每個問題區域都有需要修正的意圖。 當您選取意圖名稱時，會開啟 [**意圖**] 頁面，並將篩選套用至語句。 此篩選準則可讓您專注于造成問題的語句。

### <a name="compare-changes-across-versions"></a>比較不同版本之間的變更

在對應用程式進行變更之前，請先建立新的版本。 在新版本中，對意圖的範例語句進行建議的變更，然後再次訓練。 在儀表板頁面的**訓練評估**卡上，使用 [**顯示來自定型版本的變更**] 來比較變更。 

![比較不同版本之間的變更](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>藉由新增或編輯範例語句和重新定型來修正版本

修正應用程式的主要方法將是新增或編輯範例語句和重新定型。 新的或已變更的語句必須遵循[各種語句](luis-concept-utterance.md)的指導方針。

新增範例語句應由下列人員完成：

* 對不同意圖中的語句有非常程度的瞭解。
* 瞭解如何在一個意圖中語句，與另一個意圖混淆。
* 能夠決定是否要將兩個意圖（經常與彼此混淆），折迭成單一意圖。 如果是這種情況，則必須使用實體來提取不同的資料。

### <a name="patterns-and-phrase-lists"></a>模式與片語清單

[分析] 頁面不會指出何時使用[模式](luis-concept-patterns.md)或[片語清單](luis-concept-feature.md)。 如果您加入它們，它可以協助進行不正確或不清楚的預測，但不會有資料不平衡的協助。 

### <a name="review-data-imbalance"></a>審查資料不平衡

從這個問題開始，這可能是其他問題的根本原因。

[**資料不平衡**意圖] 清單會顯示需要更多語句的意圖，以便更正資料不平衡。 

**若要修正此問題**：

* 將更多語句新增至意圖，然後再次訓練。 

請勿將語句新增至 [無] 意圖，除非儀表板上有建議。

> [!Tip]
> 使用頁面上的第三個區段 [以**語句（數位）** 設定**語句每個意圖**]，做為哪些意圖需要更多語句的快速視覺參考。  
    ![使用 [語句（數位）] 來尋找資料不平衡的意圖。](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>審查不正確的預測

**不正確的預測**意圖清單會顯示具有語句的意圖，這會當做特定意圖的範例使用，但是會針對不同意圖進行預測。 

**若要修正此問題**：

* 編輯語句以更明確地進行意圖並再次定型。
* 結合意圖，如果語句太緊密對齊並再次定型。

### <a name="review-unclear-predictions"></a>審查不清楚的預測

[不**清楚的預測**意圖] 清單會顯示具有語句的意圖，其中的預測分數從最近的競爭對手到目前為止都不夠，因此語句的最高意圖可能會因為[負面取樣](luis-how-to-train.md#train-with-all-data)而在下一次定型時變更。

**若要修正此問題**，

* 編輯語句以更明確地進行意圖並再次定型。
* 結合意圖，如果語句太緊密對齊並再次定型。

## <a name="utterances-per-intent"></a>每個意圖的語句

這張卡片會顯示所有意圖的整體應用程式健全狀況。 當您修正意圖和重新定型時，請繼續查看這張卡片以瞭解問題。

下圖顯示良好平衡的應用程式，幾乎沒有問題可以修正。

![下圖顯示良好平衡的應用程式，幾乎沒有問題可以修正。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

下圖顯示效能不佳的應用程式，其中有許多要修正的問題。

![下圖顯示良好平衡的應用程式，幾乎沒有問題可以修正。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

將滑鼠停留在每個意圖的列上方，即可取得意圖的相關資訊。 

![下圖顯示良好平衡的應用程式，幾乎沒有問題可以修正。](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

使用 [**排序依據**] 功能可依問題類型排列意圖，讓您可以專注于與該問題最有問題的意圖。 

## <a name="intents-with-errors"></a>具有錯誤的意圖

這張卡片可讓您查看特定意圖的問題。 這張卡片的預設觀點是最有問題的意圖，讓您知道要將工作放在何處。

![[具有錯誤的意圖] 卡片可讓您查看特定意圖的問題。 根據預設，系統會將卡片篩選成最有問題的意圖，讓您知道要將工作放在何處。](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

頂端的環圈圖會顯示在三個問題類型方面的意圖問題。 如果這三個問題類型有問題，每個類型都有自己的圖表，以及任何競爭對手意圖。 

### <a name="filter-intents-by-issue-and-percentage"></a>依問題和百分比篩選意圖

這一節的卡片可讓您找出低於錯誤閾值的範例語句。 理想的情況是，您想要正確的預測才會很重要。 該百分比是商業和客戶驅動的。 

決定您對企業滿意的閾值百分比。 

此篩選準則可讓您尋找具有特定問題的意圖：

|Filter|建議的百分比|目的|
|--|--|--|
|最有問題的意圖|-|**從這裡開始**-修正此意圖中的語句將可改善應用程式，而不是其他修正。|
|更正以下的預測|60%|這是所選意圖中正確的語句百分比，但低於閾值的信賴分數。 |
|不清楚的預測|15%|這是所選意圖中與最近競爭對手意圖混淆的語句百分比。|
|上述預測不正確|15%|這是所選意圖中不正確預測的語句百分比。 |

### <a name="correct-prediction-threshold"></a>正確的預測閾值

您有什麼信心預測信賴分數？ 在應用程式開發的開端，60% 可能是您的目標。 請使用低於60% 百分比的**正確預測**，在所選意圖中尋找需要修正的任何語句。

### <a name="unclear-or-incorrect-prediction-threshold"></a>不清楚或不正確的預測閾值

這兩個篩選準則可讓您在選取的意圖中尋找超出閾值的語句。 您可以將這兩個百分比視為錯誤百分比。 如果您很熟悉預測的10-15% 錯誤率，請將篩選臨界值設定為15%，以尋找高於此值的所有語句。 

## <a name="next-steps"></a>後續步驟

* [管理您的 Azure 資源](luis-how-to-azure-subscription.md)
