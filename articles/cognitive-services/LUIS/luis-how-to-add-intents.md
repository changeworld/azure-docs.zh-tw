---
title: 新增意圖-LUIS
titleSuffix: Azure Cognitive Services
description: 將意圖新增至 LUIS 應用程式，以識別具有相同用意的問題或命令群組。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: c2802f1b41ed2842e12c808a8c380ebd646ffa03
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540926"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>新增意圖以判斷語句的使用者用意

將[意圖](luis-concept-intent.md)新增至 LUIS 應用程式，以識別具有相同用意的問題或命令群組。

在 LUIS 入口網站中，您可以從頂端導覽列的 [ **組建** ] 區段，然後從左面板的 **意圖**來管理意圖。

## <a name="add-an-intent-to-your-app"></a>在您的應用程式中新增意圖

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 在 [ **意圖** ] 頁面上，選取 [ **+ 建立**]。
1. 在 [ **建立新意圖** ] 對話方塊中，輸入意圖名稱，例如 `ModifyOrder` ，然後選取 [ **完成**]。

    > [!div class="mx-imgBorder"]
    > ![新增意圖](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    此意圖需要 [範例語句](luis-concept-utterance.md) ，才能在已發佈的預測端點上預測語句。

## <a name="add-an-example-utterance"></a>新增範例語句

範例語句是使用者問題或命令的文字範例。 若要教授 Language Understanding (LUIS) 預測此意圖的時機，您需要將範例語句新增至意圖。 LUIS 需要15到30個範例語句的範圍，才能開始瞭解意圖。 請勿大量新增範例語句。 每個語句都應該謹慎選擇，以瞭解其與意圖中已有的範例有何不同。

1. 在 [意圖詳細資料] 頁面上，輸入您預期使用者的相關語句，例如， `Deliver a large cheese pizza` 在意圖名稱下方的文字方塊中，然後按 enter。

    > [!div class="mx-imgBorder"]
    > ![意圖詳細資料頁面的螢幕擷取畫面 (已醒目提示語句)](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS 會將所有語句轉換成小寫，並且在 [權杖](luis-language-support.md#tokenization) 周圍加上空格，例如連字號。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意圖預測錯誤

當語句未針對意圖的定型應用程式進行預測時，就會判斷意圖預測錯誤。

1. 若要尋找語句的預測錯誤並加以修正，請使用不正確和不清楚的 **篩選** 選項。

    > [!div class="mx-imgBorder"]
    > ![若要尋找語句預測錯誤並加以修正，請使用篩選選項。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. 若要在 [意圖詳細資料] 頁面上顯示分數值，請從 [**視圖**選項] 功能表中選取 [**顯示詳細資料意圖分數**]。

    當套用篩選和視圖，而且有範例語句有錯誤時，範例語句清單會顯示語句和問題。

每個資料列都會顯示範例語句目前定型的預測分數，也就是最接近的競爭對手分數，也就是這兩個分數的差異。

### <a name="fixing-intents"></a>修正意圖

若要瞭解如何修正意圖預測錯誤，請使用 [摘要儀表板](luis-how-to-use-dashboard.md)。 摘要儀表板會針對使用中版本的最後一個定型提供分析，並提供最佳的建議來修正您的模型。

## <a name="using-the-contextual-toolbar"></a>使用內容相關的工具列

內容工具列會提供其他動作：

* 編輯或刪除範例語句
* 將範例語句重新指派給不同的意圖
* 篩選和查看：只顯示包含篩選實體的語句或查看選擇性的詳細資料
* 搜尋範例語句

## <a name="train-your-app-after-changing-model-with-intents"></a>在利用意圖變更模型後訓練您的應用程式

在您新增、編輯或移除意圖之後，[訓練](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)您的應用程式，變更才會套用到端點查詢。 不要在每次單一變更後定型。 在變更群組之後定型。

## <a name="next-steps"></a>後續步驟

深入了解如何新增具有實體的[範例語句](luis-how-to-add-example-utterances.md)。
