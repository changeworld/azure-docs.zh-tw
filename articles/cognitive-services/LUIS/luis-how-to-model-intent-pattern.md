---
title: 模式增加精度 - LUIS
titleSuffix: Azure Cognitive Services
description: 添加模式範本以提高語言理解 （LUIS） 應用程式中的預測準確性。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311711"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何添加模式以提高預測準確性
LUIS 應用接收端點陳述後，使用[模式](luis-concept-patterns.md)提高以字順序和單詞選擇顯示模式的話語的預測準確性。 模式使用特定的[語法](luis-concept-patterns.md#pattern-syntax)來指示：[實體](luis-concept-entity-types.md)、實體[角色](luis-concept-roles.md)和可選文本的位置。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> 模式僅包括機器學習的實體父級，不包括子元件。

## <a name="adding-example-utterances-as-pattern"></a>將示例陳述添加為模式

如果要為實體添加模式，_最簡單的_方法是從"意向詳細資訊"頁建立模式。 這可確保語法與示例陳述匹配。

1. 在[預覽 LUIS 門戶](https://preview.luis.ai)中，從"**我的應用"** 頁面選擇應用。
1. 在 **"意向"** 清單頁上，選擇要從中創建範本陳述的示例陳述的意圖名稱。
1. 在"意向詳細資訊"頁上，選擇要用作範本陳述的示例陳述的行，然後從上下文工具列中選擇 **" 添加為模式**"。

    > [!div class="mx-imgBorder"]
    > ![在"意向詳細資訊"頁上選擇示例陳述作為範本模式的螢幕截圖。](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. 在"快顯視窗"框中，在"**確認模式**"頁上選擇 **"完成**"。 不需要定義實體的子元件、約束或描述項。 您只需列出機器學習的實體。

    > [!div class="mx-imgBorder"]
    > !["意向詳細資訊"頁上確認示例陳述作為範本模式的螢幕截圖。](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. 如果需要使用`[]`（方形）括弧編輯範本（如選擇文本作為可選文本），則需要從 **"模式"** 頁進行此編輯。

1. 在巡覽列中，選擇 **"訓練"** 以使用新模式訓練應用。

## <a name="add-template-utterance-using-correct-syntax"></a>使用正確的語法添加範本陳述

1. 在 [我的應用程式]**** 頁面上選取您的應用程式名稱加以開啟，然後在 [改善應用程式效能]**** 之下，選取左面板中的 [模式]****。

    > [!div class="mx-imgBorder"]
    > ![模式清單的螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. 選取模式的正確意圖。

1. 在範本文字方塊中，輸入範本語句並選取 Enter。 當您想要輸入實體名稱時，請使用正確的模式實體語法。 實體語法的開頭為 `{`。 實體清單隨即顯示。 選擇正確的實體。

    > [!div class="mx-imgBorder"]
    > ![模式的實體螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果實體包含[一個角色](luis-concept-roles.md)，則指示具有單個冒號的角色`:`，在機構名稱（如`{Location:Origin}`） 之後， 實體的角色清單會顯示在清單中。 選取角色，然後選取 Enter。

    > [!div class="mx-imgBorder"]
    > ![實體與角色的螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    在您選取正確的實體之後，完成模式輸入，然後選取 Enter。 當您輸入好模式時，請[訓練](luis-how-to-train.md)您的應用程式。

    > [!div class="mx-imgBorder"]
    > ![包含兩種實體類型的已輸入模式螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>在利用模式變更模型後訓練您的應用程式
在您新增、編輯、移除或重新指派模式之後，[訓練](luis-how-to-train.md)並[發佈](luis-how-to-publish-app.md)您的應用程式，您的變更才會影響端點查詢。

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>使用上下文工具列

模式清單上方的上下文工具列允許您：

* 搜索模式
* 編輯模式
* 將個別模式重新指派至不同的意圖
* 將數個模式重新指派至不同的意圖
* 刪除單一模式
* 刪除數個模式
* 依實體篩選模式清單
* 篩選器模式-按意圖清單
* 移除實體或意圖篩選條件
* 在意圖或實體頁面上從現有的語句新增模式

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用模式[構建模式](luis-tutorial-pattern.md).
* 了解如何[訓練](luis-how-to-train.md)您的應用程式。
