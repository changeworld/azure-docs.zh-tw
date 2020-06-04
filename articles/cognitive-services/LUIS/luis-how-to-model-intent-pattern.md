---
title: 模式新增精確度-LUIS
titleSuffix: Azure Cognitive Services
description: 新增模式範本，以改善 Language Understanding （LUIS）應用程式中的預測精確度。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 4af8f90abf2a5581ec18ea550cbc5f1a5df50a4e
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344895"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何新增模式以改善預測精確度
LUIS 應用程式收到端點語句之後，請使用[模式](luis-concept-patterns.md)來改善以單字順序和字組選擇顯示模式之語句的預測精確度。 模式會使用特定的[語法](luis-concept-patterns.md#pattern-syntax)來指出的位置：[實體](luis-concept-entity-types.md)、實體[角色](luis-concept-roles.md)和選擇性文字。

> [!CAUTION]
> 模式只包含機器學習實體父系，而不是子實體。

## <a name="add-template-utterance-using-correct-syntax"></a>使用正確的語法新增範本語句

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。
1. 在左面板中，選取 [**改善應用程式效能**] 底下的 [**模式**]。

1. 選取模式的正確意圖。

1. 在範本文字方塊中，輸入範本語句並選取 Enter。 當您想要輸入實體名稱時，請使用正確的模式實體語法。 實體語法的開頭為 `{`。 實體清單隨即顯示。 選取正確的實體。

    > [!div class="mx-imgBorder"]
    > ![模式的實體螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果您的實體包含[角色](luis-concept-roles.md)，請在機構名稱後面指定具有單一冒號的角色， `:` 例如 `{Location:Origin}` 。 實體的角色清單會顯示在清單中。 選取角色，然後選取 Enter。

    > [!div class="mx-imgBorder"]
    > ![實體與角色的螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    在您選取正確的實體之後，完成模式輸入，然後選取 Enter。 當您輸入好模式時，請[訓練](luis-how-to-train.md)您的應用程式。

    > [!div class="mx-imgBorder"]
    > ![包含兩種實體類型的已輸入模式螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>建立模式。任何實體

[模式。任何](luis-concept-entity-types.md)實體只有在[模式](luis-how-to-model-intent-pattern.md)中有效，而非意圖的範例語句。 這類型的實體可協助 LUIS 尋找變動長度與文字選項的實體結尾。 由於此實體會在模式中使用，因此，LUIS 就能得知實體結尾是在語句範本中的何處。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。
1. 在 [**組建**] 區段中，選取左面板中的 [**實體**]，然後選取 [ **+ 建立**]。

1. 在 [**選擇實體類型**] 對話方塊中，于 [**名稱**] 方塊中輸入機構名稱，然後選取 [**模式]。任何****類型**為 []，然後選取 [**建立**]。

    一旦您[建立了](luis-how-to-model-intent-pattern.md)使用此實體語句的模式，就會使用結合的機器學習和文字比對演算法來解壓縮該實體。

## <a name="adding-example-utterances-as-pattern"></a>將範例語句新增為模式

如果您想要加入實體的模式，_最簡單_的方式是從 [意圖詳細資料] 頁面建立模式。 這可確保您的語法符合範例語句。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。
1. 在 [**意圖**清單] 頁面上，選取您想要建立語句的範本範例語句的意圖名稱。
1. 在 [意圖詳細資料] 頁面上，選取您想要作為範本語句之範例語句的資料列，然後從內容工具列選取 [ **+ 新增為模式**]。

    > [!div class="mx-imgBorder"]
    > ![在 [意圖詳細資料] 頁面上選取 [範例語句] 做為範本模式的螢幕擷取畫面。](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    語句必須包含實體，才能從語句建立模式。

1. 在快顯方塊中，選取 [**確認模式**] 頁面上的 [**完成**]。 您不需要定義實體的子實體或功能。 您只需要列出機器學習實體。

    > [!div class="mx-imgBorder"]
    > ![在 [意圖詳細資料] 頁面上確認範例語句為範本模式的螢幕擷取畫面。](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. 如果您需要編輯範本，例如選取 [文字] 做為選擇性，並使用 `[]` （方形）括弧，您必須從 [**模式**] 頁面進行這種編輯。

1. 在導覽列中，選取 [**定型**]，以新的模式訓練應用程式。

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

## <a name="use-contextual-toolbar"></a>使用內容相關的工具列

模式清單上方的內容相關工具列可讓您：

* 搜尋模式
* 編輯模式
* 將個別模式重新指派至不同的意圖
* 將數個模式重新指派至不同的意圖
* Delete-a-單一模式
* 刪除數個模式
* 依實體篩選模式清單
* 篩選模式-依意圖列出
* 移除實體或意圖篩選條件
* 在意圖或實體頁面上從現有的語句新增模式

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用模式[建立模式](luis-tutorial-pattern.md)。任何和角色都有教學課程。
* 了解如何[訓練](luis-how-to-train.md)您的應用程式。
