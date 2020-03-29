---
title: 添加實體 - LUIS
titleSuffix: Azure Cognitive Services
description: 創建實體以從語言理解 （LUIS） 應用中的使用者話語中提取關鍵資料。 提取的實體資料由用戶端應用程式用於完全刪除客戶請求。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1f2b293acdc77e25e6b932c47d466cc28a04a2b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221495"
---
# <a name="add-entities-to-extract-data"></a>添加實體以提取資料 

創建實體以從語言理解 （LUIS） 應用中的使用者話語中提取關鍵資料。 提取的實體資料由用戶端應用程式用於完全刪除客戶請求。

實體代表語句中您想要擷取的單字或片語。 實體描述與意圖有關的資訊，有時是您應用程式執行其工作的必備要素。 當您向意圖添加示例陳述時，可以在將示例陳述添加到意圖之外，或者（在之前或之後）將示例陳述添加到意圖之外，從而創建實體。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>規劃實體，然後創建和標記

機器學習的實體可以從示例陳述創建，也可以從 **"實體"** 頁創建。 

通常，最佳做法是在門戶中創建機器學習實體之前花時間規劃實體。 然後，從示例陳述中創建機器學習的實體，在子元件、描述項和約束中具有與當時所知的一樣多的詳細資訊。 [可分解實體教程](tutorial-machine-learned-entity.md)演示如何使用此方法。 

作為規劃實體的一部分，您可能知道需要文本匹配實體（如預構建實體、正則運算式實體或清單實體）。 您可以在 **"實體"** 頁創建這些，然後再在示例陳述中標記它們。 

標記時，可以標記單個實體，然後構建到父電腦學習實體。 或者，可以從父電腦學習實體開始，然後分解為子實體。 

> [!TIP] 
>標記可能指示實體的所有單詞，即使這些單詞在用戶端應用程式中提取時未使用也是如此。 

## <a name="creating-an-entity-before-or-with-labeling"></a>在標記之前創建實體

使用下表瞭解要創建每個實體或將每個實體添加到應用的實體。 

|實體類型|在 LUIS 門戶中創建實體的位置|
|--|--|
|已採用機器學習的實體|實體或意向詳細資訊|
|清單實體|實體或意向詳細資訊|
|規則運算式實體|實體|
|Pattern.any 實體|實體|
|預建實體|實體|
|預構建的域實體|實體|

可以從 **"實體"** 頁創建所有實體，也可以創建幾個實體，作為**在"意向詳細資訊**"頁上的示例陳述中標記實體的一部分。 _您只能在_**"意向"詳細資訊**頁中標記示例陳述中的實體。 

## <a name="create-a-machine-learned-entity"></a>創建機器學習實體

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>創建文本匹配實體

使用文本匹配實體提供了幾種提取資料的方法：

|文本匹配實體|目的|
|--|--|
|[清單實體](#add-list-entities-for-exact-matches)|正式名稱清單以及同義字作為替代形式|
|規則運算式實體|使用正則運算式實體匹配文本|
|[預建實體](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|匹配常見資料類型，如數位、電子郵件、日期|
|預構建的域實體|使用所選主題域匹配|
|[Pattern.any](#add-a-patternany-entity)| 匹配可能很容易與周圍文本混淆的實體|  

預構建的實體無需提供任何自訂訓練資料即可工作。 其他實體需要您提供客戶培訓資料（如 List 實體的專案）或運算式（如正則運算式或模式。

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>如何創建新的自訂實體

1. 在 LUIS 門戶中，轉到 **"管理**"部分，然後轉到 **"實體"** 頁。 
1. 選擇 **= 創建**，然後選擇實體類型。 
1. 繼續配置實體，然後選擇完成後**選擇"創建**"。 

### <a name="add-list-entities-for-exact-matches"></a>添加確切匹配的清單實體

清單實體代表一組固定且封閉的相關字組。 雖然您作為作者可以更改清單，但 LUIS 不會增長或縮小清單。 您還可以使用 _list 實體 .json 格式（引用實體清單.md_示例-json-導入到清單實體）導入到現有清單實體。 

下面的清單演示了正式名稱和同義字。 

|顏色 - 清單項名稱|顏色 - 同義字|
|--|--|
|紅色|深紅， 血液， 蘋果， 消防車|
|藍色|天空， 蔚藍， 鈷|
|綠色|凱莉， 石灰|

使用該過程創建清單實體。 創建清單實體後，無需在意圖中標記示例陳述。 清單項和同義字使用精確文本進行匹配。 

1. 從 **"生成"** 部分中，選擇左側面板中的**實體**，然後選擇 **" 創建**"。

1. 在"**創建實體類型"** 對話方塊中，輸入實體的名稱，如`Colors`和 選擇 **"清單**"。
1. 在"**創建清單實體**"對話方塊中，在 **"添加新子清單..."** 中，輸入清單項名稱，如`Green`，然後添加同義字。

    > [!div class="mx-imgBorder"]
    > ![在"實體詳細資訊"頁中創建顏色清單作為清單實體。](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. 完成添加清單項和同義字後，選擇 **"創建**"。

    完成對應用的一組更改後，請記住**訓練**應用。 在一次更改後，不要訓練應用。 

    > [!NOTE]
    > 此過程演示了從 **"意向詳細資訊**"頁中的示例陳述創建和標記清單實體。 您還可以從 **"實體"** 頁創建相同的實體。

## <a name="add-a-role-for-an-entity"></a>添加實體的角色

角色是基於上下文的實體的命名子類型。 

### <a name="add-a-role-to-distinguish-different-contexts"></a>添加角色以區分不同的上下文

在以下陳述中，有兩個位置，每個位置都由其周圍的單詞（如`to`和`from`） 在語義上指定： 

`Pick up the package from Seattle and deliver to New York City.`

在此過程中，向預構建`origin`的`destination`地理V2實體添加和角色。

1. 從 [建置]**** 區段，選取左面板中的 [實體]****。

1. 選擇 **= 添加預構建的實體**。 選擇**地理V2，** 然後選擇 **"完成**"。 這會向應用添加預構建的實體。
    
    如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](reference-pattern-syntax.md#explicit-lists)來更正此問題。 

1. 從實體的 **"實體"** 頁面清單中選擇新添加的預構建地理 V2 實體。 
1. 要添加新角色，請選擇**+****"未添加"角色**旁邊。
1. 在 **"類型角色..."** 文字方塊中，輸入角色`Origin`的名稱，然後輸入。 添加`Destination`輸入的第二個角色名稱。 

    > [!div class="mx-imgBorder"]
    > ![將 Origin 角色新增到 Location 實體的螢幕擷取畫面](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    角色將添加到預構建的實體中，但不會添加到使用該實體的任何陳述中。 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>在示例陳述中具有角色的標籤文本

1. 轉到"意向詳細資訊"頁，該頁面包含使用該角色的示例陳述。 
1. 要使用角色進行標注，請在示例陳述中選擇實體標籤（文本下的實線），然後從下拉清單中選擇 **"在實體調色板中查看**"。 

    > [!div class="mx-imgBorder"]
    > ![選擇實體調色板中的視圖的螢幕截圖](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    實體調色板向右打開。 

1. 選擇實體，然後轉到調色板的底部並選擇角色。 

    > [!div class="mx-imgBorder"]
    > ![選擇實體調色板中的視圖的螢幕截圖](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>添加模式. 任何實體

[模式.任何](luis-concept-entity-types.md)實體僅在[模式](luis-how-to-model-intent-pattern.md)中有效，而不是意圖的示例陳述。 這類型的實體可協助 LUIS 尋找變動長度與文字選項的實體結尾。 由於此實體會在模式中使用，因此，LUIS 就能得知實體結尾是在語句範本中的何處。

### <a name="steps-to-create-a-patternany-entity"></a>建立模式的步驟。

1. 從 **"生成"** 部分中，選擇左側面板中的**實體**，然後選擇 **" 創建**"。

1. 在"**選擇實體類型"** 對話方塊中，在 **"名稱"** 框中輸入機構名稱，然後選擇 **"模式"。** **Type** **Create**

    使用此實體[建立模式陳述](luis-how-to-model-intent-pattern.md)後，將使用電腦學習與文本匹配演算法的組合提取實體。 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>建立模式範本陳述以使用模式。

若要使用 pattern.any 實體，在 [模式]**** 頁面 (在 [改善應用程式效能]**** 區段中) 搭配正確的大括號語法來新增模式，例如 `Where is **{HumanResourcesFormTitle}** on the server?`。

如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](reference-pattern-syntax.md#explicit-lists)來更正此問題。 

## <a name="do-not-change-entity-type"></a>不更改實體類型

LUIS 不允許您變更實體的類型，因為它不知道要新增或移除哪些項目來建構該實體。 若要變更類型，更好的方式是使用稍微不同的名稱來建立正確類型的新實體。 一旦建立實體之後，在每個語句中，移除已加上標籤的舊實體名稱，並新增新的實體名稱。 為所有語句加上標籤之後，刪除舊實體。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用預先建置的模型](howto-add-prebuilt-models.md) 

深入了解：
* 如何[訓練](luis-how-to-train.md)
* 如何[測試](luis-interactive-test.md)
* 如何[發佈](luis-how-to-publish-app.md)
* 模式：
    * [概念](luis-concept-patterns.md)
    * [語法](reference-pattern-syntax.md)
* [預構建實體 GitHub 存儲庫](https://github.com/Microsoft/Recognizers-Text)
* [資料提取概念](luis-concept-data-extraction.md)


 
