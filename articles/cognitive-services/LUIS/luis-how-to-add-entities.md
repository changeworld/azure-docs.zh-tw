---
title: 新增實體-LUIS
titleSuffix: Azure Cognitive Services
description: 建立實體以從 Language Understanding （LUIS）應用程式中的使用者語句，將重要資料解壓縮。 用戶端應用程式會使用已解壓縮的實體資料來 fullfil 客戶要求。
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221495"
---
# <a name="add-entities-to-extract-data"></a>新增實體以將資料解壓縮 

建立實體以從 Language Understanding （LUIS）應用程式中的使用者語句，將重要資料解壓縮。 您的用戶端應用程式會使用已解壓縮的實體資料來 fullfil 客戶要求。

實體代表語句中您想要擷取的單字或片語。 實體描述與意圖有關的資訊，有時是您應用程式執行其工作的必備要素。 您可以在將範例語句新增至意圖時，或在將範例語句新增至意圖時，建立實體。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="plan-entities-then-create-and-label"></a>規劃實體，然後建立並加上標籤

機器學習的實體可以從範例語句建立，或從 [**實體**] 頁面建立。 

一般來說，最佳做法是在入口網站中建立機器學習的實體之前，花時間規劃實體。 然後，從範例語句建立機器學習的實體，並在您所知道的時候，在子元件和描述項和條件約束中提供更多詳細資料。 [分解 entity 教學](tutorial-machine-learned-entity.md)課程會示範如何使用這個方法。 

在規劃實體時，您可能會知道您需要文字相符的實體（例如預先建立的實體、正則運算式實體或清單實體）。 您可以從 [**實體**] 頁面建立這些專案，然後在範例語句中標示它們。 

標記時，您可以標記個別實體，然後建立至父系機器學習的實體。 或者，您可以從父系機器學習的實體開始，然後分解成子實體。 

> [!TIP] 
>標籤所有可能表示實體的單字，即使在用戶端應用程式中解壓縮時未使用這些字。 

## <a name="creating-an-entity-before-or-with-labeling"></a>在標記之前或之後建立實體

使用下表來瞭解哪些實體要建立或新增每個實體至應用程式。 

|實體類型|在 LUIS 入口網站中建立實體的位置|
|--|--|
|已採用機器學習的實體|實體或意圖詳細資料|
|清單實體|實體或意圖詳細資料|
|規則運算式實體|實體|
|Pattern.any 實體|實體|
|預建實體|實體|
|預先建立的網域實體|實體|

您可以從 [**實體**] 頁面建立所有實體，也可以在 [**意圖詳細資料**] 頁面上的範例語句中，建立幾個實體做為標記實體的一部分。 您只能在 [**意圖詳細資料**] 頁面的範例語句中_標示_實體。 

## <a name="create-a-machine-learned-entity"></a>建立機器學習的實體

[!INCLUDE [Create and label entities in machine-learned tutorial](includes/decomposable-tutorial-links.md)]

## <a name="create-a-text-matching-entity"></a>建立文字對應實體

使用文字對應實體提供數種方式來解壓縮資料：

|文字對應的實體|目的|
|--|--|
|[清單實體](#add-list-entities-for-exact-matches)|正式名稱清單和同義字做為替代形式|
|規則運算式實體|使用正則運算式實體來比對文字|
|[預建實體](tutorial-machine-learned-entity.md#add-prebuilt-number-to-help-extract-data)|符合一般資料類型，例如數位、電子郵件、日期|
|預先建立的網域實體|使用選取的主體網域進行比對|
|[Pattern.any](#add-a-patternany-entity)| 若要比對可能會輕易與周圍文字混淆的實體|  

預先建立的實體在不需提供任何自訂定型資料的情況下， 其他實體則需要您提供客戶訓練資料（例如清單實體的專案）或運算式（例如正則運算式或模式）。

<a name="add-list-entities"></a>

### <a name="how-to-create-a-new-custom-entity"></a>如何建立新的自訂實體

1. 在 LUIS 入口網站中，移至 [**管理**] 區段，然後移至 [**實體**] 頁面。 
1. 選取 [ **+ 建立**]，然後選取實體類型。 
1. 繼續設定實體，然後在完成時選取 [**建立**]。 

### <a name="add-list-entities-for-exact-matches"></a>新增完全相符專案的清單實體

清單實體代表一組固定且封閉的相關字組。 當您身為作者的人可以變更清單時，LUIS 不會增加或縮小清單。 您也可以使用 [清單] [entity. json 格式] （參考---------匯入清單實體）匯入到現有的清單實體。 

下列清單示範標準名稱和同義字。 

|色彩清單專案名稱|色彩-同義字|
|--|--|
|紅色|crimson，血壓，apple，引發引擎|
|藍色|天空、azure、鈷|
|綠色|王，酸|

使用程式來建立清單實體。 建立清單實體之後，您就不需要在意圖中標示範例語句。 清單專案和同義字會使用完全相符的文字進行比對。 

1. 在 [**組建**] 區段中，選取左面板中的 [**實體**]，然後選取 [ **+ 建立**]。

1. 在 [**建立實體類型**] 對話方塊中，輸入實體的名稱，例如， `Colors`然後選取 [**清單**]。
1. 在 [**建立清單實體**] 對話方塊的 [**加入新**的子清單 ...] 中，輸入清單專案名稱，例如`Green`，然後新增同義字。

    > [!div class="mx-imgBorder"]
    > ![在實體詳細資料頁面中，以清單實體的形式建立色彩清單。](media/how-to-add-entities/create-list-entity-of-colors.png) 

1. 當您完成新增清單專案和同義字時，請選取 [**建立**]。

    當您完成對應用程式的一組變更時，請記得**訓練**應用程式。 請不要在單一變更後訓練應用程式。 

    > [!NOTE]
    > 此程式示範如何從**意圖詳細資料**頁面中的範例語句建立及標記清單實體。 您也可以從 [**實體**] 頁面建立相同的實體。

## <a name="add-a-role-for-an-entity"></a>新增實體的角色

「角色」（role）是以內容為基礎之實體的命名子型別。 

### <a name="add-a-role-to-distinguish-different-contexts"></a>新增角色來區別不同的內容

在下列語句中，有兩個位置，每個都是以語義方式在其周圍的單字`to` （ `from`例如和）上指定： 

`Pick up the package from Seattle and deliver to New York City.`

在此程式中， `origin`將`destination`和角色新增至預先建立的 geographyV2 實體。

1. 從 [建置]**** 區段，選取左面板中的 [實體]****。

1. 選取 [ **+ 新增預先**建立的實體]。 選取 [ **geographyV2** ]，然後選取 [**完成**]。 這會將預先建立的實體新增至應用程式。
    
    如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](reference-pattern-syntax.md#explicit-lists)來更正此問題。 

1. 從實體的 [**實體**] 頁面清單中，選取新加入的預先建立的 geographyV2 實體。 
1. 若要加入新的角色， **+** 請選取 [下一步]，**不新增任何角色**。
1. 在 [**類型角色 ...** ] 文字方塊中，輸入角色`Origin`的名稱，然後輸入。 新增第二個角色名稱`Destination` ，然後輸入。 

    > [!div class="mx-imgBorder"]
    > ![將 Origin 角色新增到 Location 實體的螢幕擷取畫面](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    角色會新增至預建實體，但不會新增至任何使用該實體的語句。 

### <a name="label-text-with-a-role-in-an-example-utterance"></a>在範例語句中使用角色標籤文字

1. 移至意圖詳細資料頁面，其中包含使用角色的範例語句。 
1. 若要以角色加上標籤，請選取範例語句中的實體標籤（[文字] 底下的實線），然後從下拉式清單中選取 [**在實體調色板中查看**]。 

    > [!div class="mx-imgBorder"]
    > ![選取 entity 調色板中 View 的螢幕擷取畫面](media/how-to-add-entities/select-text-label-with-entity-palette-for-role.png)   

    實體調色板會在右側開啟。 

1. 選取實體，然後移至色板底部並選取角色。 

    > [!div class="mx-imgBorder"]
    > ![選取 entity 調色板中 View 的螢幕擷取畫面](media/how-to-add-entities/select-role-from-entity-palette-entity-inspector.png)

<a name="add-pattern-any-entities"></a>

## <a name="add-a-patternany-entity"></a>新增模式。任何實體

[模式。任何](luis-concept-entity-types.md)實體只有在[模式](luis-how-to-model-intent-pattern.md)中有效，而非意圖的範例語句。 這類型的實體可協助 LUIS 尋找變動長度與文字選項的實體結尾。 由於此實體會在模式中使用，因此，LUIS 就能得知實體結尾是在語句範本中的何處。

### <a name="steps-to-create-a-patternany-entity"></a>建立模式的步驟。任何實體

1. 在 [**組建**] 區段中，選取左面板中的 [**實體**]，然後選取 [ **+ 建立**]。

1. 在 [**選擇實體類型**] 對話方塊中，于 [**名稱**] 方塊中輸入機構名稱，然後選取 [**模式]。任何****類型**為 []，然後選取 [**建立**]。

    一旦您[建立了](luis-how-to-model-intent-pattern.md)使用此實體語句的模式，就會使用結合機器學習和文字比對演算法來解壓縮該實體。 

### <a name="create-a-pattern-template-utterance-to-use-patternany-entity"></a>建立要使用 pattern 的模式範本語句。任何實體

若要使用 pattern.any 實體，在 [模式]**** 頁面 (在 [改善應用程式效能]**** 區段中) 搭配正確的大括號語法來新增模式，例如 `Where is **{HumanResourcesFormTitle}** on the server?`。

如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](reference-pattern-syntax.md#explicit-lists)來更正此問題。 

## <a name="do-not-change-entity-type"></a>不要變更實體類型

LUIS 不允許您變更實體的類型，因為它不知道要新增或移除哪些項目來建構該實體。 若要變更類型，更好的方式是使用稍微不同的名稱來建立正確類型的新實體。 一旦建立實體之後，在每個語句中，移除已加上標籤的舊實體名稱，並新增新的實體名稱。 為所有語句加上標籤之後，刪除舊實體。 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"] 
> [使用預先建置的模型](howto-add-prebuilt-models.md) 

深入了解：
* 如何[定型](luis-how-to-train.md)
* [測試](luis-interactive-test.md)方式
* 如何[發佈](luis-how-to-publish-app.md)
* 模式
    * [概念](luis-concept-patterns.md)
    * [語法](reference-pattern-syntax.md)
* [預先建立的實體 GitHub 存放庫](https://github.com/Microsoft/Recognizers-Text)
* [資料提取概念](luis-concept-data-extraction.md)


 
