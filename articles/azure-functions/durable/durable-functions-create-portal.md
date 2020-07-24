---
title: 使用 Azure 入口網站建立 Durable Functions
description: 了解如何安裝 Azure Functions 的 Durable Functions 擴充，供入口網站開發使用。
ms.topic: conceptual
ms.date: 04/10/2020
ms.reviewer: azfuncdf
ms.openlocfilehash: b029fa246977dfe4210f6e8df242415f7e4103f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081911"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>使用 Azure 入口網站建立 Durable Functions

NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 中提供 Azure Functions 的 [Durable Functions](durable-functions-overview.md) 擴充。 此延伸模組必須安裝在您的函式應用程式中。 本文說明如何安裝此套件，以便您可以在 Azure 入口網站中開發長期函式。

> [!NOTE]
> 
> * 如果您正在使用 C# 開發長期函式，則應考慮 [Visual Studio 2019 開發](durable-functions-create-first-csharp.md)。
> * 如果您正在使用 JavaScript 開發長期函式，則應考慮 [Visual Studio Code 開發](./quickstart-js-vscode.md)。

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控任何函式的執行。 函式應用程式可讓您將多個函式群組為邏輯單位，方便管理、部署、調整和共用資源。 您可以建立 .NET 或 JavaScript 應用程式。

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

根據預設，所建立的函式應用程式會使用 2.x 版的 Azure Functions 執行階段。 Durable Functions 擴充功能適用於以 C# 撰寫的 Azure Functions 執行階段的 1.x 和 2.x 版本，以及以 JavaScript 撰寫的 2.x 版本。 但是，無論選擇的語言為何，範本僅在以執行階段 2.x 版本為目標時才可以使用。

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>安裝 Durable Functions npm 套件 (僅限 JavaScript)

如果您要建立 JavaScript Durable Functions，您必須安裝 [`durable-functions`npm 套件](https://www.npmjs.com/package/durable-functions)：

1. 從函式應用程式的頁面，選取左窗格中 [開發工具] 之下的 [進階工具]。

   :::image type="content" source="./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png" alt-text="函式平台功能會選擇 Kudu":::

2. 在 [進階工具] 頁面中，選取 [執行]。

3. 在 Kudu 主控台中，選取 [偵錯主控台]，然後選取 [CMD]。

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu 偵錯主控台":::

3. 函式應用程式的檔案目錄結構應該會顯示。 瀏覽到 `site/wwwroot` 資料夾。 從這裡，您可以將 `package.json` 檔案拖放到檔案目錄視窗來上傳該檔案。 範例 `package.json` 如下：

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   :::image type="content" source="./media/durable-functions-create-portal/kudu-choose-debug-console.png" alt-text="Kudu upload package.json":::

4. `package.json` 上傳之後，請從 Kudu 遠端執行主控台執行 `npm install` 命令。

   ![Kudu run npm install](./media/durable-functions-create-portal/kudu-npm-install.png)
   
## <a name="create-an-orchestrator-function"></a>建立協調器函式

1. 在您的函式應用程式中，從左側窗格選取 [函式]，然後從頂端功能表選取 [新增]。 

1. 在 [新函式] 的搜尋欄位中，輸入 `durable`，然後選擇 [Durable Functions HTTP 入門] 範本。

   :::image type="content" source="./media/durable-functions-create-portal/durable-functions-http-starter-template.png" alt-text="選取 Durable Functions HTTP 入門":::

1. 針對 [新函式] 名稱，輸入 `HttpStart`，然後選取 [建立函式]。

   建立的函式用來啟動協調流程。

1. 在函式應用程式中建立另一個函式，這次是使用 **Durable Functions 協調器**範本。 將新的協調流程函式命名為 `HelloSequence`。

1. 使用 **Durable Functions 活動**範本建立名為 `Hello` 的第三個函式。

## <a name="test-the-durable-function-orchestration"></a>測試長期函式的協調流程

1. 回到 **HttpStart** 函式，選擇 [取得函式 URL]，然後選取 [複製到剪貼簿] 圖示以複製該 URL。 您可以使用此 URL 來啟動 **HelloSequence** 函式。

1. 使用 Postman 或 cURL 等 HTTP 工具，將 POST 要求傳送至您複製的 URL。 下列範例是 cURL 命令，它向長期函式傳送 POST 要求：

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence --header "Content-Length: 0"
    ```

    在此範例中，`{your-function-app-name}` 是網域 (您的函式應用程式名稱)。 回應訊息包含一組可用於監視和管理執行的 URI 端點，如下列範例所示︰

    ```json
    {  
       "id":"10585834a930427195479de25e0b952d",
       "statusQueryGetUri":"https://...",
       "sendEventPostUri":"https://...",
       "terminatePostUri":"https://...",
       "rewindPostUri":"https://..."
    }
    ```

1. 呼叫 `statusQueryGetUri` 端點 URI，您將看到長期函式目前的狀態，看起來可能如下範例：

    ```json
        {
            "runtimeStatus": "Running",
            "input": null,
            "output": null,
            "createdTime": "2017-12-01T05:37:33Z",
            "lastUpdatedTime": "2017-12-01T05:37:36Z"
        }
    ```

1. 繼續呼叫 `statusQueryGetUri` 端點，直到狀態變成 [已完成]，您會看到如下列範例所示的回應：

    ```json
    {
            "runtimeStatus": "Completed",
            "input": null,
            "output": [
                "Hello Tokyo!",
                "Hello Seattle!",
                "Hello London!"
            ],
            "createdTime": "2017-12-01T05:38:22Z",
            "lastUpdatedTime": "2017-12-01T05:38:28Z"
        }
    ```

您的第一個長期函式現在已在 Azure 中運作。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [了解常見的耐久函式模式](durable-functions-overview.md#application-patterns)
