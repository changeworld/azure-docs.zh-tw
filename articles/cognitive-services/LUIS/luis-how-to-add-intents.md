---
title: 新增意圖-LUIS
titleSuffix: Azure Cognitive Services
description: 將意圖新增至 LUIS 應用程式，以識別具有相同用意的問題或命令群組。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73904333"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>新增意圖以判斷語句的使用者用意

將[意圖](luis-concept-intent.md)新增至 LUIS 應用程式，以識別具有相同用意的問題或命令群組。 

從上方導覽列的 [建置]**** 區段，然後從左側面板的 [意圖]**** 管理意圖。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>新增意圖

1. 在[LUIS preview 入口網站](https://preview.luis.ai)中，選取 [**組建**] 以查看意圖。 
1. 在 [**意圖**] 頁面上，選取 [ **+ 建立**]。
1. 在 [**建立新的意圖**] 對話方塊中，輸入意圖名稱，例如`ModifyOrder`，然後選取 [**完成**]。

    > [!div class="mx-imgBorder"]
    > ![新增意圖](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    意圖需要範例語句。

## <a name="add-an-example-utterance"></a>新增範例語句

範例語句是使用者問題或命令的文字範例。 若要教 Language Understanding （LUIS）何時預測此意圖，您需要將範例語句新增至意圖。 LUIS 需要介於15到30個範例語句的範圍內，才能開始瞭解意圖。 請勿在 bulk 中新增範例語句。 應謹慎選擇每個語句，以瞭解其與意圖中的範例有何不同。 

1. 在 [意圖詳細資料] 頁面上，輸入您預期來自使用者的相關語句， `Deliver a large cheese pizza`例如，在意圖名稱底下的文字方塊中，然後按 enter。
 
    > [!div class="mx-imgBorder"]
    > ![意圖詳細資料頁面的螢幕擷取畫面 (已醒目提示語句)](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS 會將所有語句轉換成小寫，並在[權杖](luis-language-support.md#tokenization)前後加上空格，例如連字號。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意圖預測錯誤 

意圖中的範例語句可能會在範例語句目前所在的意圖和定型期間決定的意圖之間有意圖預測錯誤。 

若要尋找語句預測錯誤並加以修正，請使用 [不正確且不清楚] 的**篩選**選項與 [**詳細視圖**] 的 [ **view** ] 選項結合。 

![若要尋找語句預測錯誤並加以修正，請使用 [篩選] 選項。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

套用篩選和視圖，而且有範例語句出現錯誤時，[範例語句] 清單會顯示語句和問題。

> [!div class="mx-imgBorder"]
> ![![套用篩選準則和視圖時，如果有範例語句出現錯誤，則範例語句清單會顯示語句和問題。](./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

每個資料列都會顯示語句範例的目前定型分數，這是最接近的競爭對手分數，這就是這兩個分數的差異。 

### <a name="fixing-intents"></a>修正意圖

若要瞭解如何修正意圖預測錯誤，請使用 [[摘要] 儀表板](luis-how-to-use-dashboard.md)。 [摘要] 儀表板會提供作用中版本最後一次訓練的分析，並提供修正模型的最佳建議。  

## <a name="using-the-contextual-toolbar"></a>使用內容相關的工具列

內容工具列提供其他動作：

* 編輯或刪除範例語句
* 將範例語句重新指派至不同的意圖
* 篩選和查看：只顯示包含篩選實體的語句或 view 選擇性的詳細資料
* 搜尋範例語句

## <a name="train-your-app-after-changing-model-with-intents"></a>在利用意圖變更模型後訓練您的應用程式

在您新增、編輯或移除意圖之後，[訓練](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)您的應用程式，變更才會套用到端點查詢。 請不要在每次單一變更後訓練。 在一組變更之後進行定型。 

## <a name="next-steps"></a>後續步驟

深入了解如何新增具有實體的[範例語句](luis-how-to-add-example-utterances.md)。 
