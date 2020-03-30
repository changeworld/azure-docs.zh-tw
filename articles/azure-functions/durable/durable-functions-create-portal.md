---
title: 使用 Azure 入口網站建立 Durable Functions
description: 了解如何安裝 Azure Functions 的 Durable Functions 擴充，供入口網站開發使用。
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: azfuncdf
ms.openlocfilehash: 0060088acb100036c094406e01d0d736a4af88eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769637"
---
# <a name="create-durable-functions-using-the-azure-portal"></a>使用 Azure 入口網站建立 Durable Functions

NuGet 套件 [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) 中提供 Azure Functions 的 [Durable Functions](durable-functions-overview.md) 擴充。 此延伸模組必須安裝在您的函式應用程式中。 本文說明如何安裝此套件，以便您可以在 Azure 入口網站中開發長期函式。

> [!NOTE]
> 
> * 如果您正在開發 C# 中的持久函數，則應考慮[Visual Studio 2019 開發](durable-functions-create-first-csharp.md)。
> * 如果您正在使用 JavaScript 開發長期函式，則應考慮 [Visual Studio Code 開發](./quickstart-js-vscode.md)。

## <a name="create-a-function-app"></a>建立函數應用程式

您必須擁有函式應用程式以便主控任何函式的執行。 函數應用允許您將函數分組為邏輯單元，以便更輕鬆地管理、部署、擴展和共用資源。 您可以建立 .NET 或 JavaScript 應用程式。

[!INCLUDE [Create function app Azure portal](../../../includes/functions-create-function-app-portal.md)]

根據預設，所建立的函式應用程式會使用 2.x 版的 Azure Functions 執行階段。 Durable Functions 擴充功能適用於以 C# 撰寫的 Azure Functions 執行階段的 1.x 和 2.x 版本，以及以 JavaScript 撰寫的 2.x 版本。 但是，無論選擇的語言為何，範本僅在以執行階段 2.x 版本為目標時才可以使用。

## <a name="install-the-durable-functions-npm-package-javascript-only"></a>安裝 Durable Functions npm 套件 (僅限 JavaScript)

如果要創建 JavaScript 持久函數，則需要安裝[`durable-functions`npm 包](https://www.npmjs.com/package/durable-functions)。

1. 選取您的函式應用程式名稱，後面接著**平台功能**，然後**進階工具 (Kudu)**。

   ![函式平台功能會選擇 Kudu](./media/durable-functions-create-portal/function-app-platform-features-choose-kudu.png)

2. 在 Kudu 主控台中，選取 [偵錯主控台]****，然後選擇 [CMD]****。

   ![Kudu 偵錯主控台](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

3. 函式應用程式的檔案目錄結構應該會顯示。 瀏覽到 `site/wwwroot` 資料夾。 從這裡，您可以將 `package.json` 檔案拖放到檔案目錄視窗來上傳該檔案。 範例 `package.json` 如下：

    ```json
    {
      "dependencies": {
        "durable-functions": "^1.3.1"
      }
    }
    ```

   ![Kudu upload package.json](./media/durable-functions-create-portal/kudu-choose-debug-console.png)

4. `package.json` 上傳之後，請從 Kudu 遠端執行主控台執行 `npm install` 命令。

   ![Kudu run npm install](./media/durable-functions-create-portal/kudu-npm-install.png)

## <a name="create-an-orchestrator-function"></a>建立協調器函式

1. 展開函數應用，然後按一下**函數**旁邊的**+** 按鈕。 如果這是函式應用程式中的第一個函式，請依序選取 [入口網站內]**** 和 [繼續]****。 否則，請移至步驟三。

   ![Azure 入口網站中的 Functions 快速入門](./media/durable-functions-create-portal/function-app-quickstart-choose-portal.png)

1. 依序選擇 [更多範本]**** 和 [完成並檢視範本]****。

    ![Functions 快速入門選擇更多範本](./media/durable-functions-create-portal/add-first-function.png)

1. 在搜尋欄位中，輸入 `durable`，然後選擇 [Durable Functions HTTP 入門]**** 範本。

1. 提示後，選擇 **"安裝**"以安裝 Azure 持久任務擴展和函數應用中的任何依賴項。 您只需要為指定的函式應用程式安裝一次延伸模組。 安裝成功之後，請選取 [繼續]****。

    ![安裝繫結延伸模組](./media/durable-functions-create-portal/install-durabletask-extension.png)

1. 安裝完成後，將新的函式命名為 `HttpStart`，然後選擇 [建立]****。 建立的函式用來啟動協調流程。

1. 在函式應用程式中建立另一個函式，這次是使用 **Durable Functions 協調器**範本。 將新的協調流程函式命名為 `HelloSequence`。

1. 使用 **Durable Functions 活動**範本建立名為 `Hello` 的第三個函式。

## <a name="test-the-durable-function-orchestration"></a>測試長期函式的協調流程

1. 返回 **HttpStart** 函式中，選擇 **</> Get 函式 URL** 並**複製**該 URL。 您可以使用此 URL 來啟動 **HelloSequence** 函式。

1. 使用 Postman 或 cURL 等 HTTP 工具，將 POST 要求傳送至您複製的 URL。 下列範例是 cURL 命令，它向長期函式傳送 POST 要求：

    ```bash
    curl -X POST https://{your-function-app-name}.azurewebsites.net/api/orchestrators/HelloSequence
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

1. 繼續呼叫 `statusQueryGetUri` 端點，直到狀態變成 [已完成]****，您會看到如下列範例所示的回應：

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
