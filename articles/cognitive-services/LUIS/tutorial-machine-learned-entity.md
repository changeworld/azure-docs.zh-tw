---
title: 教學課程：使用機器學習實體擷取結構化資料 - LUIS
description: 使用機器學習實體從語句中擷取結構化資料。 若要提高擷取的正確性，請新增具有特徵的子實體。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: 1f85ba0c64db23e156f384fadcc5ca7bf84a58d4
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130718"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>教學課程：在 Language Understanding (LUIS) 中使用機器學習實體從使用者語句中擷取結構化資料

在本教學課程中，您將使用機器學習實體從語句中擷取結構化資料。

機器學習實體會提供具有[特徵](luis-concept-feature.md)的子實體，以支援[模型分解概念](luis-concept-model.md#v3-authoring-model-decomposition)。

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 匯入範例應用程式
> * 新增機器學習實體
> * 新增子實體和特徵
> * 定型、測試、發佈應用程式
> * 從端點取得實體預測

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>為何要使用機器學習實體？

本教學課程會新增機器學習實體，以從使用者的語句中擷取資料。

實體會定義要從語句中擷取的資料。 這包括為資料提供名稱、類型 (如果可能的話)、任何資料解析 (如果有所混淆)，以及組成資料的確切文字。

若要定義資料，您必須：
* 建立實體
* 在範例語句中，為代表實體的文字加上標籤。 這些加上標籤的範例會向 LUIS 指出什麼是實體，以及可在語句中哪個位置找到實體。

## <a name="entity-decomposability-is-important"></a>實體的可分解性是很重要的

對於macOS意圖預測和資料擷取，實體的可分解性都是很重要的。

請從機器學習實體開始著手，這是資料擷取的開端和最上層實體。 然後，將實體分解成子實體。

雖然您在開始執行應用程式時可能還不確定實體所需的詳細程度，但最佳做法是從機器學習實體開始著手，然後在您的應用程式成熟時，使用子實體進行分解。

在本教學課程中，您會建立機器學習實體以代表披薩應用程式的訂單。 實體會擷取與訂單相關的文字，並取出大小和數量。

`Please deliver one large cheese pizza to me` 的語句應將 `one large cheese pizza` 擷取為訂單，然後也應擷取 `1` (數量) 和 `large` (大小)。

## <a name="download-json-file-for-app"></a>下載應用程式的 JSON 檔案

下載並儲存[應用程式的 JSON 檔案](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)。

## <a name="import-json-file-for-app"></a>匯入應用程式的 JSON 檔案

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>建立機器學習實體

若要擷取關於披薩訂單的詳細資料，請建立最上層的機器學習 `Order` 實體。

1. 在 [意圖] 頁面上，選取 [OrderPizza] 意圖。

1. 在範例語句清單中，選取下列語句。

    |訂單範例語句|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    按住游標並將其拖曳到第一個範例意圖的文字上方。 在顯示的功能表中，輸入 `Order` 作為實體的名稱。 然後，從清單中選取 `Order Create new entity`。

    ![完整訂單文字的標籤開頭和結尾](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > 實體不一定是整個語句。 在此特定案例中，`pickup` 表示接收訂單的方式。 從概念性觀點來看，`pickup` 應該屬於訂單的已標記實體。

1. 在 [選擇實體類型] 方塊中選取 [新增結構]，然後選取 [下一步]。 必須要有結構，才能新增子實體 (例如大小和數量)。

    ![此螢幕擷取畫面顯示 [選擇實體類型] 視窗，其中已核取 [新增結構] 選項。](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. 在 [新增子實體 (選用)] 方塊中，選取 `Order` 列上的 **+** ，然後將 `Size` 和 `Quantity` 新增為子實體，然後選取 [建立]。

    > [!div class="mx-imgBorder"]
    > ![此螢幕擷取畫面顯示 [新增子實體 (選用)] 視窗，其中已反白顯示子實體。](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>編輯子實體以改善擷取

先前的步驟會建立實體和子實體。 若要改善擷取，請將特徵新增至子實體。

### <a name="improve-size-extraction-with-phrase-list"></a>使用片語清單改善大小擷取

1. 從左側功能表中選取 [實體]，然後選取 [訂單] 實體。

1. 在 [結構描述和功能] 索引標籤上，選取 [大小] 子實體，然後選取 [+新增特徵]。

1. 在下拉式功能表中選取 [建立新片語清單]。

1. 在 [建立新的片語清單] 方塊中輸入名稱 `SizePhraselist`，然後依序輸入 `small`、`medium` 和 `large` 的值。 在填入 [建議] 方塊後，選取 `extra large` 和 `xl`。 選取 [建立] 以建立新的片語清單。

    此片語清單特徵可為 `Size` 子實體提供範例文字，以利該子實體尋找與大小相關的文字。 此片語清單不需要包含每個大小文字，但應包含預期會指出大小的文字。

### <a name="add-sizelist-entity"></a>新增 SizeList 實體

新增用戶端應用程式可辨識的已知大小清單也有助於擷取。

1. 從左側功能表中選取 [實體]，然後選取 [+建立]。

1. 將實體名稱設為 `SizeListentity`，並將類型設定為 [清單]，以便在與上一節中建立的 `SizePhraselist` 比較時輕鬆識別。

1. 新增用戶端應用程式預期的大小：`Small`、`Medium`、`Large` 和 `XLarge`，然後為每個項目新增同義字。 同義字應該是使用者在聊天機器人中輸入的詞彙。 當實體完全符合標準化的值或同義字時，會使用清單實體來將其擷取。

    |標準化值|同義字|
    |--|--|
    |小型|sm、sml、小型、最小|
    |中|md、mdm、一般、平均、中型|
    |大型|lg、lrg、大|
    |特大|xl、最大、特大|


    > [!div class="mx-imgBorder"]
    > ![此螢幕擷取畫面顯示 SizeList 視窗和清單項目，其中已選取 XLarge。](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>新增 SizeList 實體的特徵

1. 從左側功能表中選取 [實體]，以返回實體清單。

1. 從實體清單中選取 [訂單]。

1. 在 [結構描述和特徵] 索引標籤上，選取 [大小] 實體，然後選取 [+新增特徵]。

1. 從下拉式清單中選取 [@ SizeListentity]。

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>使用預先建置的數字實體

新增預先建立的數字實體也會協助擷取。

1. 從左側功能表中選取 [實體]，然後選取 [新增預先建立的實體]。

1. 從清單中選取 [數字]，然後選取 [完成]。

1. 從左側功能表中選取 [實體]，以返回實體清單。

### <a name="add-feature-of-prebuilt-number-entity"></a>新增預先建立的數字實體特徵

1. 從實體清單中選取 [訂單]。

1. 在 [結構描述和特徵] 索引標籤上，選取 [數量] 實體，然後選取 [+新增特徵]。

1. 從下拉式清單中選取 [@ number]。

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>設定必要特徵

在 [訂單] 的實體詳細資料頁面上，為 [@ SizeList] 特徵和 [@ number] 特徵選取星號 `*`。 星號會出現在與特徵名稱相同的標籤中。

> [!div class="mx-imgBorder"]
> ![此螢幕擷取畫面顯示具有星號的 @SizeList 功能，而且需要警告。](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>標籤範例語句

已建立機器學習的實體，而且子實體具有特徵。 若要改進擷取，必須以子實體標記範例語句。

1. 從左側導覽中選取 [意圖]，然後選取 [OrderPizza] 意圖。

1. 若要開啟 **實體選擇區** ，請在內容工具列中選取 @ 符號。

1. 選取選擇區中的每個實體資料列，然後使用選擇區游標來選取每個範例語句中的實體。 當您完成時，實體清單看起來應該如下圖所示。

    > [!div class="mx-imgBorder"]
    > ![設定必要特徵的部分螢幕擷取畫面](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>進行應用程式定型

若要將應用程式定型，請選取 [定型]。 在定型後，會將變更 (例如新的實體和加上標籤的語句) 套用至使用中模型。

## <a name="add-a-new-example-utterance"></a>新增新的範例語句

1. 訓練之後，請將新的範例語句新增至 `OrderPizza` 意圖，以了解 LUIS 對機器學習實體的理解程度。

    |訂單範例語句|
    |--|
    |`I need a large pepperoni pizza`|

    整體而言的最高實體 `Order` 會加上標籤，而 `Size` 子實體也會以虛線標示。

    > [!div class="mx-imgBorder"]
    > ![使用實體預測的新範例語句部分螢幕擷取畫面](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    虛線表示以目前定型的應用程式為基礎的預測。

1. 若要將預測變更為加上標籤的實體，請在相同的資料列上選取核取記號。

    > [!div class="mx-imgBorder"]
    > ![此螢幕擷取畫面顯示已反白顯示核取記號的範例語句。](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    此時機器學習實體就會開始運作，因為其可在新的範例語句內找到實體。 當您新增範例語句時，如果實體未正確預測，請為該實體和子實體加上標籤。 如果正確預測實體，請務必確認預測。


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>為應用程式定型以將實體變更套用至應用程式

選取 [定型]，以使用這些新的語句將應用程式定型。

此時，訂單有一些可供擷取的詳細資料 (大小、數量和訂單總計文字)。 `Order` 實體還會更加精細，例如比薩配料、餅皮類型和附餐。 這類項目應分別建立為 `Order` 實體的子實體。

## <a name="test-the-app-to-validate-the-changes"></a>測試應用程式以驗證變更

使用互動式 **測試** 面板來測試應用程式。 此程序可讓您輸入新的語句，然後檢視預測結果，以確認作用中且已定型的應用程式運作的情形。 意圖預測應有不錯的信賴度 (高於 60%)，且實體擷取應該至少會取用 `Order` 實體。 訂單實體可能會不夠詳細，因為幾個語句不足以處理每個案例。

1. 選取頂端導覽中的 [測試]。
1. 輸入語句 `2 small cheese pizzas for pickup`，然後選取 [輸入]。 使用中模型在超過 60% 的信賴度下預測了正確的意圖。


1. 選取 [檢查] 以查看實體預測。

    > [!div class="mx-imgBorder"]
    > ![在互動式測試面板中檢視實體預測的部分螢幕擷取畫面。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>發佈應用程式以便從 HTTP 端點加以存取

若要在聊天機器人或其他應用程式中收到 LUIS 預測，您必須將應用程式發佈到端點。

1. 選取右上方導覽列中的 [發佈]。

    ![右上方功能表中的 LUIS 發佈到端點按鈕的螢幕擷取畫面](./media/howto-publish/publish-button.png)

1. 選取 [生產] 位置，然後選取 [變更設定]、[情感分析]，然後選取 [完成]。

    > [!div class="mx-imgBorder"]
    > ![LUIS 發佈到端點的螢幕擷取畫面](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. 選取通知中的 [存取您的端點 URL] 連結，以移至 [Azure 資源] 頁面。 端點 URL 會列為 [範例查詢]。

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>從 HTTP 端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 移至網址列中的 URL 結尾處，然後以您在互動式測試面板中輸入的相同查詢取代 _YOUR_QUERY_HERE_ 。

    `2 small cheese pizzas for pickup`

    最後一個 querystring 參數是 `query`，也就是 **query** 語句。

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相關資訊

* [教學課程 - 意圖](luis-quickstart-intents-only.md)
* [概念 - 實體](luis-concept-entity-types.md)概念資訊
* [概念 - 特徵](luis-concept-feature.md)概念資訊
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)

## <a name="next-steps"></a>後續步驟

在本教學課程中，應用程式會使用機器學習實體來尋找使用者語句的意圖，並從該語句中擷取詳細資料。 使用機器學習實體可讓您分解實體的詳細資料。

> [!div class="nextstepaction"]
> [新增預先建置的 keyPhrase 實體](luis-quickstart-intent-and-key-phrase.md)
