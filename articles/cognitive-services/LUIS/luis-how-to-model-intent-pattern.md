---
title: 模式新增精確度-LUIS
titleSuffix: Azure Cognitive Services
description: 新增模式範本，以改善 Language Understanding (LUIS) 應用程式的預測精確度。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 914ca77b18d0469c3ea926848be4c60aab04c9c6
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539099"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>如何新增模式以改善預測精確度
LUIS 應用程式接收到端點語句之後，請使用 [模式](luis-concept-patterns.md) 來改善以文字順序和單字選擇顯示模式之語句的預測精確度。 模式使用特定的 [語法](luis-concept-patterns.md#pattern-syntax) 來指出 [實體](luis-concept-entity-types.md)、實體 [角色](luis-concept-roles.md)和選擇性文字的位置。

> [!CAUTION]
> 模式只包含機器學習實體父系，而非子實體。

## <a name="add-template-utterance-using-correct-syntax"></a>使用正確的語法新增範本語句

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 在 [**改善應用程式效能**] 下，選取左面板中的**模式**。

1. 選取模式的正確意圖。

1. 在範本文字方塊中，輸入範本語句並選取 Enter。 當您想要輸入實體名稱時，請使用正確的模式實體語法。 實體語法的開頭為 `{`。 實體清單隨即顯示。 選取正確的實體。

    > [!div class="mx-imgBorder"]
    > ![模式的實體螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    如果您的實體包含 [角色](luis-concept-roles.md)，請在機構名稱之後以單一冒號表示角色， `:` 例如 `{Location:Origin}` 。 實體的角色清單會顯示在清單中。 選取角色，然後選取 Enter。

    > [!div class="mx-imgBorder"]
    > ![實體與角色的螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    在您選取正確的實體之後，完成模式輸入，然後選取 Enter。 當您輸入好模式時，請[訓練](luis-how-to-train.md)您的應用程式。

    > [!div class="mx-imgBorder"]
    > ![包含兩種實體類型的已輸入模式螢幕擷取畫面](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>建立模式。任何實體

[模式。任何](luis-concept-entity-types.md) 實體僅適用于 [模式](luis-how-to-model-intent-pattern.md)，而不是意圖的範例語句。 這類型的實體可協助 LUIS 尋找變動長度與文字選項的實體結尾。 由於此實體會在模式中使用，因此，LUIS 就能得知實體結尾是在語句範本中的何處。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 從 [ **組建** ] 區段中，選取左面板中的 [ **實體** ]，然後選取 [ **+ 建立**]。

1. 在 [**選擇實體類型**] 對話方塊中，于 [**名稱**] 方塊中輸入機構名稱，然後選取 [**模式]。任何****型**別，然後選取 [**建立**]。

    當您建立使用此實體 [的模式語句](luis-how-to-model-intent-pattern.md) 之後，就會使用結合的機器學習和文字比對演算法來解壓縮該實體。

## <a name="adding-example-utterances-as-pattern"></a>將範例語句新增為模式

如果您想要新增實體的模式， _最簡單_ 的方式是從 [意圖詳細資料] 頁面建立模式。 這可確保您的語法符合範例語句。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 在 [ **意圖** ] 清單頁面上，選取您要從中建立範本語句的範例語句意圖名稱。
1. 在 [意圖詳細資料] 頁面上，選取您要用來作為範本語句的範例語句資料列，然後從內容工具列中選取 [ **+ 新增為模式** ]。

    > [!div class="mx-imgBorder"]
    > ![在意圖詳細資料頁面上選取範例語句作為範本模式的螢幕擷取畫面。](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    語句必須包含實體，才能從語句建立模式。

1. 在快顯方塊中，選取 [**確認模式**] 頁面上的 [**完成**]。 您不需要定義實體的子實體或功能。 您只需要列出機器學習實體。

    > [!div class="mx-imgBorder"]
    > ![在意圖詳細資料頁面上，將範例語句確認為範本模式的螢幕擷取畫面。](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. 如果您需要編輯範本（例如，將文字選取為選用），並使用 `[]` (正方形) 括弧，則必須從 [ **模式** ] 頁面進行這種編輯。

1. 在巡覽列中，選取 [ **定型** ]，以新的模式將應用程式定型。

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

[模式] 清單上方的內容工具列可讓您：

* 搜尋模式
* 編輯模式
* 將個別模式重新指派至不同的意圖
* 將數個模式重新指派至不同的意圖
* Delete-單一模式
* 刪除數個模式
* 依實體篩選模式清單
* 篩選-模式-依意圖列出
* 移除實體或意圖篩選條件
* 在意圖或實體頁面上從現有的語句新增模式

## <a name="next-steps"></a>後續步驟

* 瞭解如何使用模式 [建立模式](luis-tutorial-pattern.md) 。任何和角色都有教學課程。
* 了解如何[訓練](luis-how-to-train.md)您的應用程式。
