---
title: 教學課程：預測用意 - LUIS
description: 在本教學課程中，建立自訂應用程式來預測使用者的用意。 此應用程式不會擷取語句文字中的各種資料元素 (例如電子郵件地址或日期)，因此是最簡單的 LUIS 應用程式類型。
ms.topic: tutorial
ms.date: 03/24/2020
ms.openlocfilehash: c58c96f717de77c065d7f844928714eb4fb3e4db
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80286739"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>教學課程：建置 LUIS 應用程式來判斷使用者意圖

在本教學課程中，您會建立自訂應用程式，以根據語句 (文字) 預測使用者的用意。

**在本教學課程中，您將了解如何：**

> [!div class="checklist"]
> * 建立新的應用程式
> * 建議意圖
> * 新增範例語句
> * 訓練應用程式
> * 發佈應用程式
> * 從端點取得意圖預測

## <a name="user-intentions-as-intents"></a>使用者用意成為意圖

應用程式的目的是要判斷交談式自然語言文字的用意：

`I'd like to order a veggie pizza with a salad on the side.`

這些用意可歸類為各種**意圖**。

|Intent|目的|
|--|--|
|`ModifyOrder`|判斷使用者的披薩訂單。|
|`Greeting`|開始進行 Bot 對話。|
|`ConfirmOrder`|確認披薩訂單。|
|`None`|判斷使用者是否詢問應用程式不該回答的事項。 此意圖是在應用程式建立過程中提供，且無法刪除。 |

## <a name="create-a-new-app"></a>建立新的應用程式

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>建立新意圖

意圖可用來根據使用者的意圖 (從自然語言文字判斷) 對使用者語句進行分類。

若要將語句分類，意圖需要應歸類為此意圖的使用者語句範例。

1. 在 [意圖]  頁面上的 [建置]  區段中，選取 [+ 建立]  以建立新意圖。 輸入新意圖名稱 `OrderPizza`，然後選取 [完成]  。

    當使用者想要訂購披薩時，系統就會預測 `OrderPizza` 意圖。

1. 將您預期使用者會要求的數個範例語句新增至此意圖：

    |`OrderPizza` 範例語句|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![新增範例語句](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    藉由提供_範例語句_，您可以教導 LUIS 應針對此意圖預測哪些種類的語句。

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>建立其餘的意圖

1. 建立 `Greeting` 意圖，並新增下列範例語句。 此意圖用來判斷使用者是否開始進行新的披薩訂單對話。

    |`Greeting` 範例語句|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. 建立 `Confirm` 意圖，並新增下列範例語句。 此意圖用來判斷使用者是否已完成訂購並接受訂單詳細資料。

    |`Confirm` 範例語句|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>None 意圖範例語句

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>進行應用程式定型

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>發佈應用程式

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>取得意圖預測

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. 移至位址中的 URL 尾端並輸入：

    `get a medium vegetarian pizza for delivery`

    這與範例語句並不完全相同，因此若要查看 LUIS 是否能了解以此意圖應預測的內容，這是很好的測試。

    最後一個查詢字串參數是 `query`，也就是語句**查詢**。 此語句與所有範例語句不同。 這是很好的測試，而且應傳回 `OrderPizza` 意圖作為評分最高的意圖。

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    實體陣列是空的，因為此應用程式目前沒有任何實體 (語句中要擷取的資料單位)。

    JSON 結果會將評分最高的意圖識別為 **`prediction.topIntent`** 屬性。 所有分數都介於 1 到 0，分數越好者越接近 1。

1. 將 URL **查詢** 參數變更為以 **問候** 意圖為目標：

    `Howdy`

    這與範例語句並不完全相同，因此若要查看 LUIS 是否能了解以此意圖應預測的內容，這是很好的測試。

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    此預測的信賴分數佔 44%。 若要增加信賴分數，請新增 15 到 30 個範例語句。

## <a name="client-application-next-steps"></a>用戶端應用程式的後續步驟

在 LUIS 傳回 JSON 回應之後，隨著此要求完成 LUIS。 LUIS 不會提供使用者語句的回答，只會在自然語言中識別所要求的是哪一類資訊。 Azure Bot 等用戶端應用程式會提供交談式後續追蹤。


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>相關資訊

* [實體類型](luis-concept-entity-types.md)
* [如何訓練](luis-how-to-train.md)
* [發佈方法](luis-how-to-publish-app.md)
* [如何在 LUIS 入口網站中測試](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>後續步驟

本教學課程已在端點上建立 LUIS 應用程式、建立意圖、新增每個意圖的範例語句、將範例語句新增至 None 意圖、進行訓練、發佈及測試。 這些是建置 LUIS 模型的基本步驟。

> [!div class="nextstepaction"]
> [將可分解實體新增至此應用程式](tutorial-machine-learned-entity.md)
