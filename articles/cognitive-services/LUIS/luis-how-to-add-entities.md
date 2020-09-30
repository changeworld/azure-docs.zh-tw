---
title: 新增實體-LUIS
description: 在 Language Understanding (LUIS) 應用程式中建立實體，以將使用者語句中的重要資料解壓縮。 用戶端應用程式會使用已解壓縮的實體資料來履行客戶要求。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540960"
---
# <a name="add-entities-to-extract-data"></a>新增實體以將資料解壓縮

在 Language Understanding (LUIS) 應用程式中建立實體，以將使用者語句中的重要資料解壓縮。 您的用戶端應用程式會使用已解壓縮的實體資料來履行客戶要求。

實體代表語句中您想要擷取的單字或片語。 實體描述與意圖有關的資訊，有時是您應用程式執行其工作的必備要素。 當您將範例語句新增至意圖， (或在) 將範例語句加入意圖之前或之後，您可以建立實體。

## <a name="plan-entities-then-create-and-label"></a>規劃實體，然後建立並加上標籤

您可以從範例語句建立機器學習實體，或從 [ **實體** ] 頁面建立。

一般而言，最佳做法是在入口網站中，于建立機器學習服務實體之前，花時間規劃實體。 然後，從範例語句建立機器學習實體，並在您所知的子實體和功能中提供許多詳細資料。 [可分解 entity 教學](tutorial-machine-learned-entity.md)課程會示範如何使用這個方法。

在規劃實體時，您可能會知道需要符合文字的實體 (例如預建的實體、正則運算式實體，或) 的清單實體。 您可以從 [ **實體** ] 頁面建立這些專案，然後在範例語句中標示它們。

加上標籤時，您可以為個別實體加上標籤，然後建立至父機器學習實體。 或者，您可以從父機器學習實體開始，然後分解成子實體。

> [!TIP]
>標記可能指出實體的所有字組，即使在用戶端應用程式中解壓縮時並未使用這些字組。

## <a name="when-to-create-an-entity"></a>建立實體的時機

在規劃您的實體之後，您應該建立您的機器學習服務實體和子實體。 這可能需要新增預先建立的實體或文字相符的實體，以提供機器學習實體的功能。 這些都應該在標記之前完成。

開始標記範例語句之後，您可以建立機器學習實體或擴充清單實體。

使用下表來瞭解建立或新增每個實體類型到應用程式的位置。

|實體類型|在 LUIS 入口網站中建立實體的位置|
|--|--|
|機器學習實體|實體或意圖詳細資料|
|清單實體|實體或意圖詳細資料|
|規則運算式實體|實體|
|Pattern.any 實體|實體|
|預建實體|實體|
|預先建立的網域實體|實體|

您可以從 [ **實體** ] 頁面建立所有實體，也可以在 [ **意圖詳細資料** ] 頁面上的 [範例] 語句中，建立幾個實體作為標籤的一部分。 您只能從 [**意圖詳細資料**] 頁面的範例語句中_標示_實體。



## <a name="how-to-create-a-new-custom-entity"></a>如何建立新的自訂實體

此程式適用于機器學習實體、清單實體和正則運算式實體。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 選取 [ **實體** ] 頁面。
1. 選取 [ **+ 建立**]，然後選取實體類型。
1. 繼續設定實體，然後在完成時選取 [ **建立** ]。

## <a name="create-a-machine-learned-entity"></a>建立機器學習實體

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 從 [ **組建** ] 區段中，選取左面板中的 [ **實體** ]，然後選取 [ **+ 建立**]。
1. 在 [ **建立實體類型** ] 對話方塊中，輸入實體的名稱並選取 [已 **學習的機器**]，然後選取 []。 若要加入子實體，請選取 [ **加入結構**]。 選取 [建立]。

    > [!div class="mx-imgBorder"]
    > ![建立機器學習實體的螢幕擷取畫面。](media/add-entities/machine-learned-entity-with-structure.png)

1. 在 [ **新增子實體**] 中，藉由選取 **+** 父實體資料列上的來新增列。

    > [!div class="mx-imgBorder"]
    > ![新增子實體的螢幕擷取畫面。](media/add-entities/machine-learned-entity-with-subentities.png)

1. 選取 [ **建立** ] 以完成建立程式。

## <a name="add-a-feature-to-a-machine-learned-entity"></a>將功能新增至機器學習實體

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 從 [ **組建** ] 區段中，選取左面板中的 [ **實體** ]，然後選取 [機器學習] 實體。
1. 選取實體或列資料列上的 [ **+ 新增功能** ] 來加入功能。
1. 從現有的實體和片語清單中選取。
1. 如果找不到實體，請選取該功能的星號 `*` 。

    > [!div class="mx-imgBorder"]
    > ![將功能新增至實體的螢幕擷取畫面。](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>建立規則運算式實體

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 從 [ **組建** ] 區段中，選取左面板中的 [ **實體** ]，然後選取 [ **+ 建立**]。

1. 在 [ **建立實體類型** ] 對話方塊中，輸入實體的名稱並選取 [ **RegEx**]，在 [ **RegEx** ] 欄位中輸入正則運算式，然後選取 [ **建立**]。

    > [!div class="mx-imgBorder"]
    > ![建立正則運算式實體的螢幕擷取畫面。](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>建立清單實體

清單實體代表一組固定且封閉的相關字組。 當您在作者可以變更清單時，LUIS 不會擴大或縮小清單。 您也可以使用 [清單的 json 格式](reference-entity-list.md#example-json-to-import-into-list-entity)，匯入到現有的清單實體。

下列清單示範正式名稱和同義字。

|色彩清單專案名稱|色彩-同義字|
|--|--|
|紅色|crimson、血、apple、消防引擎|
|藍色|天空，鈷|
|綠色|王，橙色|

使用程式建立清單實體。 建立清單實體之後，您就不需要在意圖中為範例語句加上標籤。 清單專案和同義字會使用完全相符的文字進行比對。
1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 從 [ **組建** ] 區段中，選取左面板中的 [ **實體** ]，然後選取 [ **+ 建立**]。

1. 在 [ **建立實體類型** ] 對話方塊中，輸入實體的名稱，例如， `Colors` 然後選取 [ **清單**]。
1. 在 [ **建立清單實體** ] 對話方塊中，于 [ **加入新的子清單 ...**] 中輸入清單專案名稱，例如 `Green` ，然後加入同義字。

    > [!div class="mx-imgBorder"]
    > ![在 [實體詳細資料] 頁面中，將色彩清單建立為清單實體。](media/how-to-add-entities/create-list-entity-of-colors.png)

1. 當您完成加入清單專案和同義字時，請選取 [ **建立**]。

    當您對應用程式進行一組變更時，請記得將應用程式 **定型** 。 在單一變更之後，請勿將應用程式定型。

    > [!NOTE]
    > 此程式示範如何從 [ **意圖詳細資料** ] 頁面中的範例語句建立及標記清單實體。 您也可以從 [ **實體** ] 頁面建立相同的實體。

## <a name="add-a-role-for-an-entity"></a>新增實體的角色

角色是實體的命名子類型，以內容為基礎。

### <a name="add-a-role-to-distinguish-different-contexts"></a>新增角色來區別不同的內容

在下列語句中，有兩個位置，每個位置都是以語義方式指定，例如 `to` 和 `from` ：

`Pick up the package from Seattle and deliver to New York City.`

在此程式中， `origin` 將 `destination` 角色新增至預建的 geographyV2 實體。
1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 從 [建置]**** 區段，選取左面板中的 [實體]****。

1. 選取 [ **+ 新增預先**建立的實體]。 選取 [ **geographyV2** ]，然後選取 [ **完成**]。 這會將預先建立的實體新增至應用程式。

    如果您發現模式在包含 Pattern.any 時所擷取的實體不正確，請使用[明確清單](reference-pattern-syntax.md#explicit-lists)來更正此問題。

1. 從實體的 **實體** 頁面清單中，選取新加入的預建 geographyV2 實體。
1. 若要加入新的角色，請選取 **+** [ **未新增角色**] 旁的 [新增]。
1. 在 [ **類型角色 ...** ] 文字方塊中，輸入角色的名稱， `Origin` 然後輸入。 新增第二個角色名稱， `Destination` 然後輸入。

    > [!div class="mx-imgBorder"]
    > ![將 Origin 角色新增到 Location 實體的螢幕擷取畫面](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    角色會新增至預建實體，但不會使用該實體新增至任何語句。

### <a name="label-text-with-a-role-in-an-example-utterance"></a>在範例語句中具有角色的標籤文字

> [!TIP]
> 您可以使用機器學習實體的子實體加上標籤來取代角色。

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 移至 [意圖詳細資料] 頁面，其具有使用角色的範例語句。
1. 若要使用角色加上標籤，請在 [範例語句] 中選取 [文字])  (實線的實體標籤，然後從下拉式清單中選取 [ **在實體窗格中視圖]** 。

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面顯示已選取 [實體窗格] 功能表項目中的 [View]。](media/add-entities/view-in-entity-pane.png)

    實體選擇區會開啟到右側。

1. 選取實體，然後移至 [元件] 底部並選取角色。

    > [!div class="mx-imgBorder"]
    > ![螢幕擷取畫面：顯示要在哪裡選取角色。](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>建立模式。任何實體

**模式。任何**實體僅適用于[模式](luis-how-to-model-intent-pattern.md)。


## <a name="do-not-change-entity-type"></a>不要變更實體類型

LUIS 不允許您變更實體的類型，因為它不知道要新增或移除哪些項目來建構該實體。 若要變更類型，更好的方式是使用稍微不同的名稱來建立正確類型的新實體。 一旦建立實體之後，在每個語句中，移除已加上標籤的舊實體名稱，並新增新的實體名稱。 為所有語句加上標籤之後，刪除舊實體。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用預先建置的模型](howto-add-prebuilt-models.md)

深入了解：
* 如何 [定型](luis-how-to-train.md)
* 如何 [測試](luis-interactive-test.md)
* 如何 [發行](luis-how-to-publish-app.md)
* 模式：
    * [概念](luis-concept-patterns.md)
    * [語法](reference-pattern-syntax.md)
* [預先建立的實體 GitHub 存放庫](https://github.com/Microsoft/Recognizers-Text)
* [資料解壓縮概念](luis-concept-data-extraction.md)



