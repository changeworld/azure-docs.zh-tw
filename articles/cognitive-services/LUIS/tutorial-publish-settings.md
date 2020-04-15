---
title: 教學課程：發佈設定 - LUIS
description: 在本教學課程中，您將變更發佈設定以取得改善的預測。
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545782"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>教學課程：新增情感分析作為發佈設定

在本教學課程中，您將修改發佈設定以擷取情感分析，然後查詢 LUIS 端點以查看傳回的使用者語句情感。

**在本教學課程中，您將了解如何：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 新增情感分析作為發佈設定
> * 從已發佈的端點取得語句的情感

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>情感分析是一項發佈設定

下列語句會顯示情感範例：

|情感|Score|語句|
|:--|:--|:--|
|負面|0.01 |比薩很難吃。|
|正面|0.97 |乳酪比薩很棒。|

情感分析是適用於每個語句的應用程式設定。 您的應用程式一經設定後，即可傳回語句的情感，無須標記資料。

這是發佈設定，因此不會標記在意圖或實體頁面上。 您可以在[互動式測試](luis-interactive-test.md#view-sentiment-results)窗格中或在端點 URL 進行測試時看到它。

## <a name="import-example-json-to-begin-app"></a>匯入範例. json 以開始執行應用程式

1.  下載並儲存[應用程式的 JSON 檔案](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json)。

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>進行應用程式定型

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>設定應用程式以納入情感分析

1. 從上方功能表中選取 [發佈]  。 情感分析是一項發佈設定。

1. 選取 [生產位置]  ，然後選取 [變更設定]  。
1. 將情感分析設定設為 [開啟]  。

    ![開啟情感分析作為發佈設定](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>從端點取得語句的情感

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 移至網址列中的 URL 尾端，並以下列項目取代 _YOUR_QUERY_HERE_：

    `Deliver 2 of the best cheese pizzas ever!!!`

    此語句與任何標示的語句都不同，因此這是很好的測試，且應該傳回具有所擷取情感分析的 `OrderPizza` 意圖。

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
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
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    情感分析為正面，分數為 86%。

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相關資訊

* 情感分析是由認知服務[文字分析](../Text-Analytics/index.yml)所提供。 這項功能受限於文字分析[支援的語言](luis-language-support.md#languages-supported)。
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)


## <a name="next-steps"></a>後續步驟
本教學課程會將情感分析新增為發佈設定，以整個語句擷取情感值。

> [!div class="nextstepaction"]
> [檢閱 HR 應用程式中的端點語句](luis-tutorial-review-endpoint-utterances.md)

