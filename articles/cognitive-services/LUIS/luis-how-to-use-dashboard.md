---
title: 儀表板-Language Understanding-LUIS
titleSuffix: Azure Cognitive Services
description: 使用您定型的應用程式儀表板來修正意圖和實體。 儀表板會顯示整體的應用程式資訊，並強調應修正的意圖。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/08/2019
ms.openlocfilehash: 4867a065a85fab1e4abc7f19401239e5b76e1da4
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541402"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>如何使用儀表板來改善您的應用程式

當您使用範例語句時，尋找並修正您定型的應用程式意圖問題。 儀表板會顯示整體的應用程式資訊，並強調應修正的意圖。

審核儀表板分析是一種反復的程式，會在您變更和改善模型時重複執行。

針對在意圖中沒有任何範例語句的應用程式，此頁面不會有相關的分析，稱為 _僅限模式的_ 應用程式。

## <a name="what-issues-can-be-fixed-from-dashboard"></a>儀表板可以修正哪些問題？

儀表板中所解決的三個問題如下：

|問題|圖表色彩|說明|
|--|--|--|
|資料不平衡|-|當範例語句數量的差異很大時，就會發生這種情況。 所有意圖都必須有 _大約_ 相同的範例語句數目，但「無」意圖除外。 應用程式中的語句總數量應只有 10%-15%。<br><br> 如果資料已不平衡，但意圖精確度高於特定閾值，則不會將此不平衡回報為問題。<br><br>**從這個問題開始，這可能是其他問題的根本原因。**|
|不清楚的預測|橙色|這會發生在最上層意圖和下一個意圖的分數已接近下一次定型時，因為有 [負面取樣](luis-how-to-train.md#train-with-all-data) 或更多範例語句新增至意圖。 |
|不正確的預測|紅色|當未針對標示意圖 (範例語句時，就會發生這種情況，) 的意圖。|

正確的預測會以藍色表示。

儀表板會顯示這些問題，並告訴您哪些意圖受到影響，並建議您應如何做以改善應用程式。

## <a name="before-app-is-trained"></a>在訓練應用程式之前

在您將應用程式定型之前，儀表板不會包含任何修正的建議。 訓練您的應用程式以查看這些建議。

## <a name="check-your-publishing-status"></a>檢查您的發佈狀態

**發行狀態**卡包含使用中版本上次發佈的相關資訊。

檢查作用中的版本是否為您想要修正的版本。

![儀表板會顯示應用程式的外部服務、已發佈的區域，以及匯總的端點叫用。](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

這也會顯示任何外部服務、已發佈的區域，以及匯總的端點叫用。

## <a name="review-training-evaluation"></a>審查訓練評估

**訓練評估**卡片包含應用程式整體精確度的匯總摘要（依區域）。 分數表示意圖品質。

![訓練評估卡片包含有關應用程式整體精確度的第一個領域。](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

此圖表會指出正確預測的意圖以及具有不同色彩的問題區域。 當您利用建議來改善應用程式時，此分數會增加。

建議的修正會依問題類型分隔，而且對您的應用程式而言最重要。 如果您想要檢查並修正每個意圖的問題，請使用頁面底部的 [ **[具有錯誤的意圖](#intents-with-errors)** ] 卡片。

每個問題區域都有需要修正的意圖。 當您選取意圖名稱時，會開啟 [ **意圖** ] 頁面，並將篩選套用至語句。 此篩選器可讓您專注于造成問題的語句。

### <a name="compare-changes-across-versions"></a>比較版本之間的變更

在對應用程式進行變更之前，請先建立新的版本。 在新版本中，對意圖的範例語句進行建議的變更，然後再次定型。 在儀表板頁面的 **訓練評估** 卡片上，使用 [ **顯示來自定型版本的變更** ] 來比較變更。

![比較版本之間的變更](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>藉由新增或編輯範例語句和重新定型來修正版本

修正應用程式的主要方法是新增或編輯範例語句和重新定型。 新的或已變更的語句需要遵循 [各種語句](luis-concept-utterance.md)的指導方針。

新增範例語句應該由下列人員完成：

* 對不同意圖中的語句有很高的瞭解程度。
* 瞭解一個意圖中的語句可能與另一個意圖混淆。
* 可以決定是否要將兩個意圖（經常混淆）視為單一意圖。 如果是這種情況，則必須使用實體來提取不同的資料。

### <a name="patterns-and-phrase-lists"></a>模式和片語清單

[分析] 頁面不會指出何時要使用 [模式](luis-concept-patterns.md) 或 [片語清單](luis-concept-feature.md)。 如果您加入它們，它有助於進行不正確或不明確的預測，但無法協助資料不平衡。

### <a name="review-data-imbalance"></a>檢查資料不平衡

從這個問題開始，這可能是其他問題的根本原因。

[ **資料不平衡** 意圖] 清單會顯示需要更多語句的意圖，以便更正資料不平衡。

**修正此問題**：

* 將更多語句新增至意圖，然後再次定型。

除非在儀表板上建議，否則請勿將語句新增至 [無] 意圖。

> [!Tip]
> 使用頁面上的第三個區段，以**語句 (數位) **設定來**語句每個意圖**，作為需要更多語句之意圖的快速視覺化指南。
    ![使用 [語句 (number) ] 來尋找資料不平衡的意圖。](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>檢查不正確的預測

**不正確的預測**意圖清單會顯示具有語句的意圖，這些意圖可作為特定意圖的範例，但會針對不同意圖進行預測。

**修正此問題**：

* 編輯語句以更明確地瞭解意圖，並再次定型。
* 如果語句太接近，請合併意圖，並再次定型。

### <a name="review-unclear-predictions"></a>複習不清楚的預測

不 **清楚的預測** 意圖清單會顯示具有語句的意圖，而這些意圖的預測分數並非離最接近的競爭對手，而語句的最高意圖可能會在下一次定型時變更，因為有 [負面的取樣](luis-how-to-train.md#train-with-all-data)。

**若要修正此問題**，

* 編輯語句以更明確地瞭解意圖，並再次定型。
* 如果語句太接近，請合併意圖，並再次定型。

## <a name="utterances-per-intent"></a>每個意圖的語句

這張卡片顯示跨意圖的整體應用程式健康情況。 當您修正意圖和重新定型時，請繼續閱讀這張卡片以瞭解問題。

下圖顯示妥善平衡的應用程式，幾乎沒有任何問題需要修正。

![下圖顯示妥善平衡的應用程式，幾乎沒有任何問題需要修正。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

下圖顯示有許多問題可修正的效能不佳的應用程式。

![螢幕擷取畫面顯示每個意圖的預測，以及數個不清楚或不正確預測的結果。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

將滑鼠停留在每個意圖的列上，以取得意圖的相關資訊。

![螢幕擷取畫面顯示每個意圖的預測，以及不清楚或不正確預測結果的詳細資料。](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

使用 [ **排序依據** ] 功能，依問題類型排列意圖，讓您可以將焦點放在該問題最有問題的意圖。

## <a name="intents-with-errors"></a>有錯誤的意圖

這張卡片可讓您查看特定意圖的問題。 這張卡片的預設視圖是最有問題的意圖，因此您知道要將焦點放在哪裡。

![具有錯誤卡片的意圖可讓您查看特定意圖的問題。 根據預設，系統會將卡片篩選為最有問題的意圖，讓您知道要將工作放在哪裡。](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

頂端環圈圖會顯示在三個問題類型上的意圖問題。 如果三個問題類型中有問題，則每個類型都有自己的圖表，以及任何競爭對手意圖。

### <a name="filter-intents-by-issue-and-percentage"></a>依問題和百分比篩選意圖

卡片的這個區段可讓您尋找在錯誤臨界值之外的範例語句。 在理想情況下，您希望正確的預測很重要。 這是商務和客戶導向的百分比。

判斷您對企業感到滿意的閾值百分比。

篩選器可讓您尋找具有特定問題的意圖：

|篩選|建議的百分比|目的|
|--|--|--|
|最有問題的意圖|-|**從這裡開始** -修正此意圖中的語句，可改善應用程式，而不是其他修正程式。|
|更正下列預測|60%|這是所選意圖中正確的語句百分比，但低於閾值的信賴分數。 |
|上述不清楚的預測|15%|這是所選意圖中與最接近的競爭對手意圖混淆的語句百分比。|
|上述的預測不正確|15%|這是所選意圖中不正確預測的語句百分比。 |

### <a name="correct-prediction-threshold"></a>正確的預測臨界值

值得信賴的預測分數有哪些？ 在應用程式開發開始時，60% 可能是您的目標。 使用 **下方的正確預測** ，並以60% 的百分比尋找所選意圖中需要修正的任何語句。

### <a name="unclear-or-incorrect-prediction-threshold"></a>預測臨界值不清楚或不正確

這兩個篩選器可讓您在所選意圖中尋找超過閾值的語句。 您可以將這兩個百分比視為錯誤百分比。 如果您很熟悉預測的10-15% 錯誤率，請將篩選臨界值設為15%，以找出此值以上的所有語句。

## <a name="next-steps"></a>後續步驟

* [管理您的 Azure 資源](luis-how-to-azure-subscription.md)
