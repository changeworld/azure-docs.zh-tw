---
title: 使用 Functions 在 Azure 儲存體佇列中新增訊息
description: 使用 Azure Functions 來建立無伺服器函式，該函式是由 HTTP 要求叫用，並且在 Azure 儲存體佇列中建立訊息。
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: mvc
ms.openlocfilehash: 5ae282750580ed5b4e53e78c52ca285e40365fd3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121976"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>使用 Functions 在 Azure 儲存體佇列中新增訊息

在 Azure Functions 中，輸入和輸出繫結會提供宣告式方法，以便從您的程式碼可用之外部服務製作資料。 在本快速入門中，您會使用輸出繫結，在函式由 HTTP 要求觸發時於佇列中建立訊息。 您可使用 Azure 儲存體容器來檢視函式建立的佇列訊息。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門：

- Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 請遵循[從 Azure 入口網站建立您的第一個函式](functions-create-first-azure-function.md)中的指示，但是不要進行**清除資源**步驟。 該快速入門會建立您可以在這裡使用的函式應用程式和函式。

## <a name="add-an-output-binding"></a><a name="add-binding"></a>新增輸出繫結

在本節中，您會使用入口網站 UI 來將佇列儲存體輸出繫結新增至您稍早建立的函式。 這個繫結可供撰寫最少的程式碼，即可在佇列中建立訊息。 您不需要為以下工作撰寫程式碼，例如開啟儲存體連線、建立佇列，或取得佇列的參考。 Azure Functions 執行階段和佇列輸出繫結會為您進行這些工作。

1. 在 Azure 入口網站中，針對您在[從 Azure 入口網站建立您的第一個函式](functions-create-first-azure-function.md)中建立的函式應用程式，開啟函式應用程式分頁。 若要開啟此頁面，請搜尋並選取**函數應用程式**。 接著，選取函數應用程式。

1. 選取函數應用程式，然後選取在稍早的快速入門中所建立函式。

1. 選取 [整合]，然後選取 [+ 新增輸出]。

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="建立函式的輸出繫結。" border="true":::

1. 選取 [Azure 佇列儲存體輸出] 繫結類型，然後新增此螢幕擷取畫面後續表格中所指定的設定： 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="在 Azure 入口網站中對函式新增佇列儲存體輸出繫結。" border="true":::
    
    | 設定      |  建議的值   | 描述                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **訊息參數名稱** | outputQueueItem | 輸出繫結參數的名稱。 | 
    | **佇列名稱**   | outqueue  | 儲存體帳戶中的連線目標佇列名稱。 |
    | **儲存體帳戶連線** | AzureWebJobsStorage | 您可以使用應用程式函式已在使用的儲存體帳戶連線，或建立新的連線。  |

1. 選取 [確定] 來新增繫結。

您已定義了輸出繫結，接下來您需要將程式碼更新為使用繫結來對佇列新增訊息。  

## <a name="add-code-that-uses-the-output-binding"></a>新增會使用輸出繫結的程式碼

在本節中，您會將撰寫訊息的程式碼新增至輸出佇列。 此訊息包含值，該值會傳遞至查詢字串中的 HTTP 觸發程序。 例如，如果查詢字串包含 `name=Azure`，則佇列訊息將會是 *Name passed to the function:Azure*。

1. 在函式中，選取 [編碼 + 測試] 以在編輯器中顯示函式程式碼。

1. 根據您的函式語言更新函式程式碼：

    # <a name="c"></a>[C\#](#tab/csharp)

    將 **outputQueueItem** 參數新增至方法簽章，如下列範例所示。

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    在 `return` 陳述式前面的函式所包含的本文中，新增使用參數的程式碼以建立佇列訊息。

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    新增會使用 `context.bindings` 物件上之輸出繫結的程式碼以建立佇列訊息。 在 `context.done` 陳述式前面新增此程式碼。

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. 選取 [儲存] 來儲存變更。

## <a name="test-the-function"></a>測試函式

1. 儲存程式碼的變更後，選取 [測試]。
1. 確認測試符合下列影像，然後選取 [執行]。 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="在 Azure 入口網站中測試佇列儲存體繫結。" border="true":::

    請注意，**要求本文**包含 `name` 值：Azure。 這個值會出現在叫用函式時建立的佇列訊息中。
    
    除了在這裡選取 [執行] 之外，您可以呼叫函式，方法是在瀏覽器中輸入 URL，並且在查詢字串中指定 `name` 值。 瀏覽器方法會顯示在[先前的快速入門](functions-create-first-azure-function.md#test-the-function)中。

1. 檢查記錄以確定函式已成功。 

您第一次使用輸出繫結時，Functions 執行階段會在儲存體帳戶中建立名為 **outqueue** 的新佇列。 您將使用儲存體帳戶來確認已建立佇列以及其中的訊息。

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>尋找連線至 AzureWebJobsStorage 的儲存體帳戶


1. 移至函數應用程式，然後選取 [設定]。

1. 在 [應用程式設定] 下選取 [AzureWebJobsStorage]。

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="找出連線至 AzureWebJobsStorage 的儲存體帳戶。" border="true":::

1. 找出並記下帳戶名稱。

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="找出連線至 AzureWebJobsStorage 的儲存體帳戶。" border="true":::

### <a name="examine-the-output-queue"></a>檢查輸出佇列

1. 在函數應用程式的資源群組中，選取用於本快速入門的儲存體帳戶。

1. 在 [佇列服務] 下選取 [佇列]，然後選取名為 **outqueue** 的佇列。 

   佇列包含訊息，該訊息將您執行 HTTP 觸發程序函式時建立的輸出繫結排入佇列。 如果您已叫用預設 `name` 值為 *Azure* 的函式，則佇列訊息是 *Name passed to the function:Azure*。

1. 再次執行函式，您會看到新的訊息出現在佇列中。  

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您將輸出繫結新增至現有函式。 如需佇列儲存體繫結的詳細資訊，請參閱 [Azure Functions 儲存體佇列繫結](functions-bindings-storage-queue.md)。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
