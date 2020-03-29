---
title: 添加意圖 - LUIS
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904333"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>新增意圖以判斷語句的使用者用意

將[意圖](luis-concept-intent.md)新增至 LUIS 應用程式，以識別具有相同用意的問題或命令群組。 

從上方導覽列的 [建置]**** 區段，然後從左側面板的 [意圖]**** 管理意圖。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>新增意圖

1. 在[LUIS 預覽門戶](https://preview.luis.ai)中，選擇 **"生成"** 以查看意圖。 
1. 在 **"意向"** 頁上，選擇 **"創建**"。
1. 在 **"創建新意向"** 對話方塊中，輸入意向名稱，例如`ModifyOrder`，然後選擇 **"完成**"。

    > [!div class="mx-imgBorder"]
    > ![添加意圖](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    意圖需要示例陳述。

## <a name="add-an-example-utterance"></a>新增範例語句

範例語句是使用者問題或命令的文字範例。 要教導語言理解 （LUIS） 何時預測此意圖，您需要向意圖添加示例陳述。 LUIS 需要 15 到 30 個示例陳述來開始理解意圖。 不要大量新增示例陳述。 每個陳述都應仔細選擇，因為它與意圖中已有的示例如何不同。 

1. 在意向詳細資訊頁上，輸入您希望使用者提供的相關陳述，例如`Deliver a large cheese pizza`在意向名稱下方的文字方塊中，然後按 Enter。
 
    > [!div class="mx-imgBorder"]
    > ![意圖詳細資料頁面的螢幕擷取畫面 (已醒目提示語句)](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS 將所有陳述轉換為小寫，並在[代號](luis-language-support.md#tokenization)（如連字號）周圍添加空格。

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>意圖預測錯誤 

意圖中的示例陳述在示例陳述當前的意圖與訓練期間確定的意圖之間可能存在意圖預測錯誤。 

要查找陳述預測錯誤並修復它們，請使用"不正確和不明確"的 **"篩選器"** 選項與 **"詳細視圖**"的 **"視圖"** 選項結合使用。 

![要查找陳述預測錯誤並修復它們，請使用"篩選"選項。](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

應用篩選器和視圖，並且存在具有錯誤的示例陳述時，示例陳述清單將顯示陳述和問題。

> [!div class="mx-imgBorder"]
> ![!{當應用篩選器和視圖，並且存在具有錯誤的示例陳述時，示例陳述清單將顯示陳述和問題。（./媒體/luis-如何添加意圖/查找錯誤在話語中.png）](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

每行顯示示例陳述的當前訓練預測分數，即最接近的對手的分數，即這兩個分數的差異。 

### <a name="fixing-intents"></a>修復意圖

要瞭解如何修復意圖預測錯誤，請使用["摘要儀表板](luis-how-to-use-dashboard.md)"。 摘要儀表板提供活動版本上次培訓的分析，並提供修復模型的一些建議。  

## <a name="using-the-contextual-toolbar"></a>使用內容相關的工具列

上下文工具列提供其他操作：

* 編輯或刪除示例陳述
* 將示例陳述重新分配給其他意圖
* 篩選器和視圖：僅顯示包含篩選實體或查看可選詳細資訊的表述
* 搜索示例陳述

## <a name="train-your-app-after-changing-model-with-intents"></a>在利用意圖變更模型後訓練您的應用程式

在您新增、編輯或移除意圖之後，[訓練](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)您的應用程式，變更才會套用到端點查詢。 每一次更換後都不要訓練。 一組更改後進行培訓。 

## <a name="next-steps"></a>後續步驟

深入了解如何新增具有實體的[範例語句](luis-how-to-add-example-utterances.md)。 
