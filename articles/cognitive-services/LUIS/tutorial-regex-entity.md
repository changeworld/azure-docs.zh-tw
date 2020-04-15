---
title: 教學課程：規則運算式實體 - LUIS
description: 使用規則運算式實體從語句擷取格式一致的資料。
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 5b585ee52880c474d3f2736b34a267012b390aad
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545826"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>教學課程：從語句取得格式正確的資料
在本教學課程中，您將建立規則運算式實體從語句中擷取格式一致的資料。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 匯入應用程式
> * 新增意圖
> * 新增規則運算式實體
> * 定型、發佈及查詢應用程式以取得已擷取的資料

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>規則運算式實體

使用規則運算式實體從語句中提取格式正確的文字。 雖然語句的意圖一定會由機器學習決定，但此特定實體類型不會進行機器學習。 規則運算式實體適用於一貫由[規則運算式](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)表示的任何文字。

`Send pizza delivery time to x123432`

此範例會使用「簡短代碼」  來傳送文字簡訊。 此簡短代碼是 5 或 6 位數的數字代碼，前面會加上 x，並且可以使用規則運算式 `x\d{5,6}` 來加以描述。

當您將規則運算式實體新增至 LUIS 應用程式時，您不需要以規則運算式實體來[標記](label-entity-example-utterance.md)文字。 其會套用至所有意圖中的所有語句。

## <a name="import-example-json-to-begin-app"></a>匯入範例. json 以開始執行應用程式

1.  下載並儲存[應用程式的 JSON 檔案](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-intent-for-sending-confirmation-text-messages"></a>建立用來傳送確認文字簡訊的意圖

1. 選取 [+ 建立]  ，建立新意圖來將語句意圖分類，以便傳送確認文字。

1. 在快顯對話方塊方塊中輸入 `ConfirmationText`，然後選取 [完成]  。

1. 將語句範例新增至意圖。

    |範例語句|
    |--|
    |Send pizza delivery time to x123432|
    |Txt x234567 for time|
    |x23987 for the notice|

    若要擷取採用機器學習的實體，您應提供包含該實體的各種語句範例，但使用此非機器學習實體時，變異性並不重要。 只要文字符合規則運算式，就會將其擷取。

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>將規則運算式用於格式正確的資料
建立要比對文字數字的規則運算式實體。 此規則運算式會比對文字，但會忽略大小寫和文化特性造成的差異。

1. 在左側面板中選取 [實體]  。

1. 在 [實體] 清單頁面上選取 [+ 建立]  。

1. 在快顯對話方塊中，輸入新的實體名稱 `ConfirmationTextRegEx`，選取 **RegEx** 作為實體類型，然後選取 [下一步]  。

    > [!div class="mx-imgBorder"]
    > ![開始規則運算式實體的實體建立步驟](./media/luis-quickstart-intents-regex-entity/pizza-create-new-entity.png)

1. 在 [建立 RegEx 實體]  上，輸入 `x\d{5,6}` 作為 **Regex** 值，然後選取 [建立]  。

    > [!div class="mx-imgBorder"]
    > ![輸入規則運算式以從範例語句中擷取資料](./media/luis-quickstart-intents-regex-entity/pizza-set-regular-expression-for-new-entity.png)

1. 從左側功能表選取 [意圖]  ，然後選取 [ConfirmationText]  意圖，以查看語句中標示的規則運算式。

    > [!div class="mx-imgBorder"]
    > ![檢視範例語句中標記的規則運算式](./media/luis-quickstart-intents-regex-entity/pizza-reg-ex-entity-shown-example-utterances-intent.png)

    因為實體不是機器學習的實體，實體在建立時即會套用到語句，並顯示在 LUIS 入口網站中。

## <a name="train-the-app-before-testing-or-publishing"></a>在測試或發佈之前訓練應用程式

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>發佈應用程式以從端點進行查詢

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 移至網址列中的 URL 尾端，並以下列項目取代 _YOUR_QUERY_HERE_：

    `Text my pizza delivery to x23456 x234567 x12345`

    ```json
    {
        "query": "Text my pizza delivery to x23456 x234567 x12345",
        "prediction": {
            "topIntent": "ConfirmationText",
            "intents": {
                "ConfirmationText": {
                    "score": 0.7061845
                },
                "ModifyOrder": {
                    "score": 0.196021989
                },
                "None": {
                    "score": 0.02342912
                },
                "Test-Pizza": {
                    "score": 0.01213586
                },
                "CancelOrder": {
                    "score": 0.0063042324
                },
                "Confirmation": {
                    "score": 0.0058615827
                },
                "Greetings": {
                    "score": 0.00398947531
                }
            },
            "entities": {
                "ConfirmationTextRegEx": [
                    "x23456",
                    "x234567",
                    "x12345"
                ],
                "$instance": {
                    "ConfirmationTextRegEx": [
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x23456",
                            "startIndex": 26,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x234567",
                            "startIndex": 33,
                            "length": 7,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "ConfirmationTextRegEx",
                            "text": "x12345",
                            "startIndex": 41,
                            "length": 6,
                            "modelTypeId": 8,
                            "modelType": "Regex Entity Extractor",
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

    藉由使用規則運算式實體，LUIS 會擷取具名的資料，這樣更能以程式設計方式協助用戶端應用程式接收 JSON 回應。


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相關資訊

* [概念 - 實體](luis-concept-entity-types.md)
* [規則運算式實體的 JSON 參考](reference-entity-regular-expression.md?tabs=V3)
* [如何新增實體來擷取資料](luis-how-to-add-entities.md)

## <a name="next-steps"></a>後續步驟
本教學課程已建立新的意圖、新增語句範例，接著建立了規則運算式實體以從語句中擷取格式正確的資料。 定型和發佈應用程式之後，端點的查詢識別了意圖並傳回擷取的資料。

> [!div class="nextstepaction"]
> [了解清單實體](tutorial-list-entity.md)

