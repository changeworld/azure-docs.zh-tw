---
title: 使用 Azure Cosmos DB 和 Functions 儲存非結構化資料
description: 使用 Azure Functions 和 Cosmos DB 儲存非結構化資料
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 2828bf14b6965e87ef9547020e870333c4e839af
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987984"
---
# <a name="store-unstructured-data-using-azure-functions-and-azure-cosmos-db"></a>使用 Azure Functions 和 Azure Cosmos DB 儲存非結構化資料

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是儲存非結構化和 JSON 資料的好方法。 Cosmos DB 與 Azure Functions 結合，能夠讓儲存資料輕鬆快速，所使用的程式碼比起在關聯式資料庫中儲存資料所需的程式碼更少。

> [!NOTE]
> 此時，Azure Cosmos DB 觸發程序、輸入繫結，以及輸出繫結只會使用 SQL API 和圖形 API 帳戶。

在 Azure Functions 中，輸入和輸出繫結會提供宣告式方法，以便從函式連線到外部服務資料。 在本文中，了解如何更新現有的函式，以新增可在 Azure Cosmos DB 文件中儲存非結構化資料的輸出繫結。

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

## <a name="create-an-azure-cosmos-db-account"></a>建立 Azure Cosmos DB 帳戶

在建立輸出繫結之前，您必須先擁有使用 SQL API 的 Azure Cosmos DB 帳戶。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-an-output-binding"></a>新增輸出繫結

1. 在 Azure 入口網站中，瀏覽至您先前建立的函式應用程式，並加以選取。

1. 選取 [函式]，然後選取 HttpTrigger 函式。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-select-http-function.png" alt-text="在 Azure 入口網站中選取您的 HTTP 函式。" border="true":::

1. 選取 [整合] 和 [+ 新增輸出]。

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-add-output-binding.png" alt-text="在 Azure 入口網站中選取您的 HTTP 函式。" border="true":::

1. 使用表格中指定的 [建立輸出] 設定：

     :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-configure-cosmosdb-binding.png" alt-text="在 Azure 入口網站中選取您的 HTTP 函式。" border="true":::

    | 設定      | 建議的值  | 描述                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **繫結類型** | Azure Cosmos DB | 要選取用來建立 Azure Cosmos DB 之輸出繫結的繫結類型名稱。 |
    | **文件參數名稱** | taskDocument | 該名稱參考程式碼中的 Cosmos DB 物件。 |
    | **資料庫名稱** | taskDatabase | 用於儲存文件的資料庫名稱。 |
    | **集合名稱** | taskCollection | 資料庫集合的名稱。 |
    | **如果為 true，就會建立 Cosmos DB 資料庫和集合** | 是 | 集合尚未存在，因此加以建立。 |
    | **Cosmos DB 帳戶連線** | 新增設定 | 選取 [新增]，並選擇 [Azure Cosmos DB 帳戶] 和您先前建立的 [資料庫帳戶]，然後選取 [確定]。 建立適用於帳戶連線的應用程式設定。 繫結會使用此設定來連線至資料庫。 |

1. 選取 [確定] 以建立繫結。

## <a name="update-the-function-code"></a>更新函式程式碼

將現有函式程式碼取代為下列採用您所選語言的程式碼：

# <a name="c"></a>[C#](#tab/csharp)

使用下列程式碼來取代現有的 C# 函式：

```csharp
#r "Newtonsoft.Json"

using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, out object taskDocument, ILogger log)
{
    string name = req.Query["name"];
    string task = req.Query["task"];
    string duedate = req.Query["duedate"];

    // We need both name and task parameters.
    if (!string.IsNullOrEmpty(name) && !string.IsNullOrEmpty(task))
    {
        taskDocument = new
        {
            name,
            duedate,
            task
        };

        return (ActionResult)new OkResult();
    }
    else
    {
        taskDocument = null;
        return (ActionResult)new BadRequestResult();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用下列程式碼來取代現有的 JavaScript 函式：

```js
module.exports = async function (context, req) {

    // We need both name and task parameters.
    if (req.query.name && req.query.task) {

        // Set the output binding data from the query object.
        context.bindings.taskDocument = req.query;

        // Success.
        context.res = {
            status: 200
        };
    }
    else {
        context.res = {
            status: 400,
            body: "The query options 'name' and 'task' are required."
        };
    }
};
```
---

此程式碼範例會讀取 HTTP 要求查詢字串，並將它們指派給 `taskDocument` 物件中的欄位。 `taskDocument` 繫結會從此繫結參數傳送物件資料，以便儲存在繫結的文件資料庫中。 第一次執行函式時會建立資料庫。

## <a name="test-the-function-and-database"></a>測試函式和資料庫

1. 選取 [測試]。 在 [查詢] 底下選取 [+ 新增參數]，並將下列參數新增至查詢字串：

    + `name`
    + `task`
    + `duedate`

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function.png" alt-text="在 Azure 入口網站中選取您的 HTTP 函式。" border="true":::


1. 選取 [執行] 並確認會傳回 200 狀態。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-test-function-output.png" alt-text="在 Azure 入口網站中選取您的 HTTP 函式。" border="true":::


1. 在 Azure 入口網站中，搜尋並選取 **Azure Cosmos DB**。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-search-cosmos-db.png" alt-text="在 Azure 入口網站中選取您的 HTTP 函式。" border="true":::

1. 選擇您的 Azure Cosmos DB 帳戶，然後選取 [資料總管]。

1. 展開 **TaskCollection** 節點，選取新文件，並確認文件包含您的查詢字串值，以及一些額外的中繼資料。

    :::image type="content" source="./media/functions-integrate-store-unstructured-data-cosmosdb/functions-data-explorer-check-document.png" alt-text="在 Azure 入口網站中選取您的 HTTP 函式。" border="true":::

您已將繫結成功新增至可在 Azure Cosmos DB 中儲存非結構化資料的 HTTP 觸發程序。

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>後續步驟

如需繫結至 Cosmos DB 資料庫的詳細資訊，請參閱 [Azure Functions Cosmos DB 繫結](functions-bindings-cosmosdb.md)。

[!INCLUDE [functions-quickstart-next-steps](../../includes/functions-quickstart-next-steps-2.md)]
