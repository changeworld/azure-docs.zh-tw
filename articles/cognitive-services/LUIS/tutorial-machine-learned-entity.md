---
title: 教學課程：使用機器學習實體擷取結構化資料 - LUIS
description: 使用機器學習實體從語句中擷取結構化資料。 若要提高擷取的精確度，請新增具有描述項和限制式的子元件。
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 52bf2fb0b9f37e0c731a46c0aaf8b6c5e7f0e911
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545846"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learned-entities-in-language-understanding-luis"></a>教學課程：在 Language Understanding (LUIS) 中使用機器學習實體從使用者語句中擷取結構化資料

在本教學課程中，您將使用機器學習實體從語句中擷取結構化資料。

機器學習實體可提供子元件實體及其描述項和限制式，以支援[模型分解概念](luis-concept-model.md#v3-authoring-model-decomposition)。

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 匯入範例應用程式
> * 新增機器學習實體
> * 新增子元件
> * 新增子元件的描述項
> * 新增子元件的限制式
> * 訓練應用程式
> * 測試應用程式
> * 發佈應用程式
> * 從端點取得實體預測

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learned-entity"></a>為何要機器學習實體？

本教學課程會新增機器學習實體，以從語句中擷取資料。

實體會定義要從語句中擷取的資料。 這包括為資料提供名稱、類型 (如果可能的話)、任何資料解析 (如果有所混淆)，以及組成資料的確切文字。

若要定義實體，您必須建立實體，然後在所有意圖的範例語句中為代表實體的文字加上標籤。 這些加上標籤的範例會向 LUIS 指出什麼是實體，以及可在語句中哪個位置找到實體。

## <a name="entity-decomposability-is-important"></a>實體的可分解性是很重要的

對於macOS意圖預測和資料擷取，實體的可分解性都是很重要的。

請從機器學習實體開始著手，這是資料擷取的開端和最上層實體。 然後，將實體分解成用戶端應用程式所需的組件。

雖然您在開始執行應用程式時可能還不確定所需的詳細程度，但最佳做法是從機器學習實體開始著手，然後在您的應用程式成熟時，使用子元件進行分解。

您可在此建立機器學習實體來代表比薩應用程式的訂單。 此訂單應具備履行訂單所需的所有組件。 首先，實體會擷取與訂單相關的文字，並取出大小和數量。

`Please deliver one large cheese pizza to me` 的語句應將 `one large cheese pizza` 擷取為訂單，然後也應擷取 `1` 和 `large`。

您可以新增更多的分解項目，例如建立配料或餅皮等子元件。 在完成本教學課程之後，您應可自信地將這些子元件新增至現有的 `Order` 實體。

## <a name="import-example-json-to-begin-app"></a>匯入範例. json 以開始執行應用程式

1.  下載並儲存[應用程式的 JSON 檔案](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json)。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="label-text-as-entities-in-example-utterances"></a>在範例語句中將文字標記為實體

若要擷取關於比薩訂單的詳細資料，請建立最上層的機器學習 `Order` 實體。

1. 在 [意圖]  頁面上，選取 [OrderPizza]  意圖。

1. 在範例語句清單中，選取下列語句。

    |訂單範例語句|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    請從最左邊的文字 `pickup` 前面開始選取 (#1)，再選取到最右邊的文字 `anchovies` (#2 - 如此即結束標籤程序)。 快顯功能表隨即出現。 在快顯方塊中，輸入 `Order` 作為實體的名稱 (#3)。 然後，從清單中選取 `Order - Create new entity` (#4)。

    ![完整訂單文字的標籤開頭和結尾](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > 實體不一定是整個語句。 在此特定案例中，`pickup` 表示接收訂單的方式。 從概念性觀點來看，`pickup` 應該屬於訂單的已標記實體。

1. 在 [選擇實體類型]  方塊中選取 [新增結構]  ，然後選取 [下一步]  。 必須要有結構，才能新增子元件 (例如大小和數量)。

    ![將結構新增至實體](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. 在 [建立機器學習實體]  方塊中，在 [結構]  方塊內新增 `Size`，然後選取 [輸入]。
1. 若要新增**描述項**，請在 [描述項]  區域中選取 `+`，然後選取 [建立新的片語清單]  。

1. 在 [建立新的片語清單描述項]  方塊中輸入名稱 `SizeDescriptor`，然後依序輸入 `small`、`medium` 和 `large` 的值。 在填入 [建議]  方塊後，選取 `extra large` 和 `xl`。 選取 [完成]  以建立新的片語清單。

    此片語清單描述項可為 `Size` 子元件提供範例文字，以利尋找與大小相關的文字。 這份清單不需要包含每個大小文字，但應包含預期會指出大小的文字。

    ![建立大小子元件的描述項](media/tutorial-machine-learned-entity/size-entity-size-descriptor-phrase-list.png)

1. 在 [建立機器學習實體]  視窗上選取 [建立]  ，以完成建立 `Size` 子元件的作業。

    此時會建立具有 `Size` 元件的 `Order` 實體，但只有 `Order` 實體會套用至語句。 您必須在範例語句中為 `Size` 實體文字加上標籤。

1. 在相同的範例語句中，藉由選取 `large` 這個字，然後從下拉式清單中選取 [大小]  實體，為該字的 [大小]  子元件加上標籤。

    ![在語句中為文字的大小實體加上標籤。](media/tutorial-machine-learned-entity/mark-and-create-size-entity.png)

    由於您已_明確_為文字加上標籤，標籤與預測均相符，因此文字底下會顯示實線。

1. 為其餘語句中的 `Order` 實體和大小實體加上標籤。 文字中的方括弧分別表示加上標籤的 `Order` 實體及其中的 `Size` 實體。

    |訂單範例語句|
    |--|
    |`can i get [a pepperoni pizza and a can of coke] please`|
    |`can i get [a [small] pizza with onions peppers and olives]`|
    |`[delivery for a [small] pepperoni pizza]`|
    |`i need [2 [large] cheese pizzas 6 [large] pepperoni pizzas and 1 [large] supreme pizza]`|

    ![在其餘所有的範例語句中建立實體和子元件。](media/tutorial-machine-learned-entity/entity-subentity-labeled-not-trained.png)

    > [!CAUTION]
    > 如何處理隱含的資料 (例如，意指單一比薩的字母 `a`)？ 或者，若沒有 `pickup` 和 `delivery` 可指出比薩的預期位置，該如何處理？ 沒有大小可指出您的預設大小 (大或小) 時，又該如何處理？ 請考慮在用戶端應用程式中，將隱含的資料處理視為商務規則的一部分，而不是或不僅是 LUIS。

1. 若要將應用程式定型，請選取 [定型]  。 在定型後，會將變更 (例如新的實體和加上標籤的語句) 套用至使用中模型。

1. 定型之後，請將新的範例語句新增至意圖，以了解 LUIS 對機器學習實體的理解程度。

    |訂單範例語句|
    |--|
    |`pickup XL meat lovers pizza`|

    整體而言的最高實體 `Order` 會加上標籤，而 `Size` 子元件也會以虛線標示。

    ![使用實體預測的新範例語句](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    虛線連結表示預測。

1. 若要將預測變更為加上標籤的實體，請選取資料列，然後選取 [確認實體預測]  。

    ![選取 [確認實體預測] 以接受預測。](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance.png)

    此時機器學習實體就會開始運作，因為它可在新的範例語句內找到實體。 當您新增範例語句時，如果實體未正確預測，請為該實體和子元件加上標籤。 如果正確預測實體，請務必確認預測。

## <a name="add-prebuilt-number-to-help-extract-data"></a>新增預建的數值以利擷取資料

訂單資訊應該也會包含訂單中的項目數，例如比薩的數量。 若要擷取這項資料，必須將新的機器學習子元件新增至 `Order`，且該元件必須要有預建數值的限制式。 藉由將實體限定於預建的數值，無論文字是數字 `2` 還是文字 `two`，實體均可找出並擷取數值。

首先，將預建的數值實體新增至應用程式。

1. 從左側功能表中選取 [實體]  ，然後選取 [+ 新增預先建立的實體]  。

1. 在 [新增預先建置的實體]  方塊中搜尋並選取**數值**，然後選取 [完成]  。

    ![新增預先建置的實體](media/tutorial-machine-learned-entity/add-prebuilt-entity-as-constraint-to-quantity-subcomponent.png)

    預建的實體會新增至應用程式，但此時還不是限制式。

## <a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>建立具有限制式的子元件實體以利擷取資料

`Order` 實體應具有 `Quantity` 子元件，用以判斷訂單中某個項目的數量。 數量應限定為數值，以便用戶端應用程式可立即依名稱使用擷取的資料。

限制式會套用作為文字比對項目；包括經由完全相符比對 (例如清單實體)，或透過規則運算式 (例如規則運算式實體或預建的實體)。

使用限制式後，系統只會擷取符合限制式的文字。

1. 選取 [實體]  ，然後選取 `Order` 實體。
1. 選取 [+ 新增元件]  並輸入名稱 `Quantity`，然後選取 [輸入] 將新的子元件新增至 `Order` 實體。
1. 在出現成功通知後，在 [進階選項]  中選取 [限制式] 鉛筆圖示。
1. 在下拉式清單中，選取預建的數值。

    ![以預建的數值作為限制式，建立數量實體。](media/tutorial-machine-learned-entity/create-constraint-from-prebuilt-number.png)

    當文字符合預先建立的數子實體時，就會套用 `Quantity` 實體。

    具有限制式的實體已建立，但尚未套用至範例語句。

    > [!NOTE]
    > 子元件可內嵌在其他子元件中，最多 5 個層級。 本文並未加以說明，但您可從入口網站和 API 取得相關資訊。

## <a name="label-example-utterance-to-teach-luis-about-the-entity"></a>標記範例語句，以告知 LUIS 有關實體的資訊

1. 從左側導覽中選取 [意圖]  ，然後選取 [OrderPizza]  意圖。 下列語句中的三個數值會加上標籤，但會顯示在 `Order` 實體行下方。 這種較低的層級表示已找到實體，但未將其視為 `Order` 實體以外的實體。

    ![已找到預建的數值，但尚未將其視為訂單實體以外的實體。](media/tutorial-machine-learned-entity/prebuilt-number-not-part-of-order-entity.png)

1. 在範例語句中選取 `2`，然後從清單中選取 `Quantity`，為數值標示 `Quantity` 實體。 在相同的範例語句中，為 `6` 和 `1` 加上標籤。

    ![為文字標示數量實體。](media/tutorial-machine-learned-entity/mark-example-utterance-with-quantity-entity.png)

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>為應用程式定型以將實體變更套用至應用程式

選取 [定型]  ，以使用這些新的語句為應用程式定型。 訓練之後，`Order` 元件會正確預測出 `Quantity` 子元件。 這種正確的預測會以實線表示。

![為應用程式定型，然後檢閱範例語句。](media/tutorial-machine-learned-entity/trained-example-utterances.png)

此時，訂單有一些可供擷取的詳細資料 (大小、數量和訂單總計文字)。 `Order` 實體還會更加精細，例如比薩配料、餅皮類型和附餐。 這類項目應分別建立為 `Order` 實體的子元件。

## <a name="test-the-app-to-validate-the-changes"></a>測試應用程式以驗證變更

使用互動式**測試**面板來測試應用程式。 此程序可讓您輸入新的語句，然後檢視預測結果，以確認作用中和已定型的應用程式運作的情形。 意圖預測應有不錯的信賴度 (高於70%)，且實體擷取應該至少會取用 `Order` 實體。 訂單實體可能會不夠詳細，因為 5 個語句不足以處理每個案例。

1. 選取頂端導覽中的 [測試]  。
1. 輸入語句 `deliver a medium veggie pizza`，然後選取 [輸入]。 使用中模型在超過 70% 的信賴度下預測了正確的意圖。

    ![輸入新的語句以測試意圖。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance.png)

1. 選取 [檢查]  以查看實體預測。

    ![在互動式測試面板中檢視實體預測。](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

    大小已正確識別。 請記住，`OrderPizza` 意圖中的範例語句並不會以 `medium` 的範例作為大小，但會使用包含中尺寸的 `SizeDescriptor` 片語清單的描述項。

    數量未正確預測。 如果 LUIS 預測中沒有傳回大小，您可以在用戶端應用程式中修正此問題，方法是將大小預設為 1。

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>發佈應用程式以便從 HTTP 端點加以存取

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>從 HTTP 端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 移至網址列中的 URL 結尾處，然後以您在互動式測試面板中輸入的相同查詢取代 _YOUR_QUERY_HERE_。

    `deliver a medium veggie pizza`

    最後一個 querystring 參數是 `query`，也就是 **query** 語句。

    ```json
    {
        "query": "deliver a medium veggie pizza",
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
                            "medium"
                        ],
                        "$instance": {
                            "Size": [
                                {
                                    "type": "Size",
                                    "text": "medium",
                                    "startIndex": 10,
                                    "length": 6,
                                    "score": 0.9955588,
                                    "modelTypeId": 1,
                                    "modelType": "Entity Extractor",
                                    "recognitionSources": [
                                        "model"
                                    ]
                                }
                            ]
                        }
                    }
                ],
                "$instance": {
                    "Order": [
                        {
                            "type": "Order",
                            "text": "a medium veggie pizza",
                            "startIndex": 8,
                            "length": 21,
                            "score": 0.7983857,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
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
