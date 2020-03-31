---
title: 教學課程：清單實體 - LUIS
description: 取得符合預先定義項目清單的資料。 清單上的每個項目可以擁有也完全相符的同義字
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 1cfeccbd54e8ef8ec315d53fc7a766760c92a0d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "79297402"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>教學課程：使用清單實體從語句中取得文字完全相符的資料

在本教學課程中，您將了解如何取得與預先定義項目清單完全相符的資料。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 匯入應用程式並使用現有意圖
> * 新增清單實體
> * 定型、發佈及查詢應用程式以取得已擷取的資料

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>什麼是清單實體？

清單實體是和語句中的文字完全相符的文字項目。 清單上的每個項目可以包含同義字清單。 當您要取得完全相符的項目時，請使用清單實體。

針對此匯入的披薩應用程式，您將為不同類型的批薩餅皮建立清單實體。

在下列情況下，清單實體是這類資料的好選擇：

* 資料值是一組已知的值。
* 此組合不會超過此實體類型的最大 LUIS [界限](luis-boundaries.md)。
* 語句中的文字是與同義字或正式名稱相符的項目 (不區分大小寫)。 LUIS 不會將清單用於相符項目以外的範圍。 詞幹分析、複數及其他變化無法僅透過清單實體來解析。 若要管理變化，請考慮使用[模式](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance)並搭配選擇性的文字語法。

> [!CAUTION]
> 如果您不確定要使用清單實體或包含片語清單的機器學習實體作為描述項，最好且最彈性的做法是使用包含片語清單的機器學習實體作為描述項。 此方法可讓 LUIS 學習和擴充要擷取的資料值。

## <a name="import-example-json-and-add-utterances"></a>匯入範例. json 並新增語句

1.  下載並儲存[應用程式的 JSON 檔案](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)。

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. 匯入的應用程式具有 `OrderPizza` 意圖。 選取該意圖，然後新增具有新餅皮類型的幾個語句：

    |新語句|
    |--|--|
    |請訂購一個一般餅皮的辣肉腸小披薩|
    |3 個薄皮夏威夷披薩|
    |外送 2 個芝心披薩和麵包棒|
    |外帶一個厚片披薩|
    |一個辣肉腸深盤披薩|

## <a name="crust-list-entity"></a>餅皮清單實體

現在，**OrderPizza** 意圖具有餅皮類型的範例語句，LUIS 必須了解哪些字組代表餅皮類型。

主要名稱和同義字的範例如下：

|正式名稱|同義字|
|--|--|
|深盤|深<br>深盤餅皮<br>厚片<br>厚片餅皮|
|一般|標準<br>原始<br>正常<br>一般餅皮<br>原始餅皮<br>正常餅皮|
|芝心|芝心餅皮|
|薄皮|薄餅皮<br>極薄<br>極薄餅皮|

1. 在左側面板中選取 [實體]  。

1. 選取 [+ 建立]  。

1. 在實體快顯對話方塊中，輸入 `CrustList` 作為實體名稱，以及輸入 [清單]  作為實體類型。 選取 [下一步]  。

    > [!div class="mx-imgBorder"]
    > ![建立新實體快顯對話方塊的螢幕擷取畫面](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. 在 [建立清單實體]  頁面上，輸入標準名稱和每個標準名稱的同義字，然後選取 [建立]  。

    > [!div class="mx-imgBorder"]
    > ![將項目新增至清單實體的螢幕擷取畫面](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    當您將清單實體新增至 LUIS 應用程式時，您不需要以清單實體來[標記](label-entity-example-utterance.md)文字。 其會套用至所有意圖中的所有語句。

## <a name="train-the-app-before-testing-or-publishing"></a>在測試或發佈之前訓練應用程式

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>發佈應用程式以從端點進行查詢

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>從端點取得意圖和實體預測

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. 移至位址中的 URL 結尾並輸入以下語句：

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    最後一個 querystring 參數是 `query`，也就是 **query** 語句。


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

    餅皮類型會以完全相符的文字來進行尋找，並在 JSON 回應中傳回結果。 用戶端應用程式會使用這項資訊來處理訂單。

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相關資訊

* [清單實體](luis-concept-entity-types.md#list-entity)概念資訊
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)
* [概念 - 實體](luis-concept-entity-types.md)
* [規則運算式實體的 JSON 參考](reference-entity-regular-expression.md?tabs=V3)
* [如何新增實體來擷取資料](luis-how-to-add-entities.md)

## <a name="next-steps"></a>後續步驟
本教學課程已新增語句範例，接著建立了清單實體以從語句中擷取完全相符的文字項目。 定型和發佈應用程式之後，端點的查詢識別了意圖並傳回擷取的資料。

> [!div class="nextstepaction"]
> [新增具有角色的預先建置實體](tutorial-entity-roles.md)

