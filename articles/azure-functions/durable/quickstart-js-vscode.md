---
title: 使用 JavaScript 在 Azure 中建立第一個耐久函式
description: 使用 Visual Studio Code 建立及發佈 Azure Durable Function。
author: ColbyTresness
ms.topic: quickstart
ms.date: 11/07/2018
ms.reviewer: azfuncdf, cotresne
ms.openlocfilehash: b0a1d1a9305f6de2a072ee1ded310d8de174436b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845718"
---
# <a name="create-your-first-durable-function-in-javascript"></a>使用 JavaScript 建立第一個耐久函式

*Durable Functions* 是 [Azure Functions](../functions-overview.md) 的擴充功能，可讓您在無伺服器環境中撰寫具狀態函式。 此擴充功能會為您管理狀態、設定檢查點和重新啟動。

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

在本文中，您會了解如何使用 Visual Studio Code Azure Functions 擴充功能，在本機建立及測試 "hello world" 耐久函式。  此函式會協調對其他函式的呼叫並鏈結在一起。 接著會將函式程式碼發佈至 Azure。

![在 Azure 中執行耐久函式](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程：

* 安裝 [Visual Studio Code](https://code.visualstudio.com/download)。

* 確定您有最新版的 [Azure Functions Core Tools](../functions-run-local.md)。

* 在 Windows 電腦上，確認您已安裝且正在執行 [Azure 儲存體模擬器](../../storage/common/storage-use-emulator.md)。 在 Mac 或 Linux 電腦上，您必須使用實際的 Azure 儲存體帳戶。

* 確定您已安裝 [Node.js](https://nodejs.org/) 8.0 版或更新版本。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>建立本機專案 

在這一節中，您會使用 Visual Studio Code 來建立本機 Azure Functions 專案。 

1. 在 Visual Studio Code 中按 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 `Azure Functions: Create new project...`。

1. 選擇您專案工作區的目錄位置，然後選擇 [選取]  。

    > [!NOTE]
    > 這些步驟主要設計為在工作區以外的地方完成。 在此案例中，請勿選取屬於工作區的專案資料夾。

1. 按照提示，針對您所需的語言提供下列資訊：

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----------- |
    | 為您的函式應用程式專案選取語言 | JavaScript | 建立本機 Node.js Functions 專案。 |
    | 選取版本 | Azure Functions v2 | 您只會在尚未安裝 Core Tools 時看到此選項。 在此情況下，Core Tools 會在您第一次執行應用程式時安裝。 |
    | 選取您專案第一個函式的範本 | HTTP 觸發程序 | 在新的函式應用程式中建立由 HTTP 觸發的函式。 |
    | 提供函式名稱 | HttpTrigger | 按 Enter 鍵以使用預設名稱。 |
    | 授權層級 | 函式 | 當您呼叫函式的 HTTP 端點時，`function` 授權層級會要求您提供存取金鑰。 這會使存取不安全的端點變得更加困難。 若要深入了解，請參閱[授權金鑰](../functions-bindings-http-webhook.md#authorization-keys)。  |
    | 選取您要如何開啟專案 | 新增到工作區 | 在目前的工作區中建立函式應用程式。 |

如有需要，Visual Studio Code 安裝 Azure Functions Core Tools。 其也會在新的工作區中建立函式應用程式專案。 此專案包含 [host.json](../functions-host-json.md) 和 [local.settings.json](../functions-run-local.md#local-settings-file) 組態檔。 其也會建立 HttpExample 資料夾，其中包含 [function.json 定義檔](../functions-reference-node.md#folder-structure)和 [index.js 檔案](../functions-reference-node.md#exporting-a-function) (這是包含函式程式碼的 Node.js 檔案)。

此外，也會在根資料夾中建立 package.json 檔案。

## <a name="install-the-durable-functions-npm-package"></a>安裝 Durable Functions npm 套件

1. 在函式應用程式的根目錄中執行 `durable-functions`，以安裝 `npm install durable-functions` npm 套件。

## <a name="creating-your-functions"></a>建立您的函式

我們現在將建立開始使用 Durable Functions 所需的三個函式：HTTP 入門、協調器時和活動函式。 HTTP 入門將起始您的整個解決方案，且協調器會將工作分派給各種活動函式。

### <a name="http-starter"></a>HTTP 入門

首先，建立 HTTP 觸發的函式，該函式會啟動耐久函式協調流程。

1. 從 [Azure: 函式]  中，選擇 [建立函式]  圖示。

    ![建立函式](./media/quickstart-js-vscode/create-function.png)

2. 選取含有函式應用程式專案的資料夾，然後選取 [Durable Functions HTTP 入門]  函式範本。

    ![選擇 HTTP 入門範本](./media/quickstart-js-vscode/create-function-choose-template.png)

3. 將預設名稱保留為 `DurableFunctionsHttpStart` 並按下 ** **Enter**，然後選取 [匿名]  驗證。

    ![選擇匿名驗證](./media/quickstart-js-vscode/create-function-anonymous-auth.png)

我們現已在我們的耐久函式中建立進入點。 讓我們新增協調器。

### <a name="orchestrator"></a>協調器

現在，我們將建立協調器來協調活動函式。

1. 從 [Azure: 函式]  中，選擇 [建立函式]  圖示。

    ![建立函式](./media/quickstart-js-vscode/create-function.png)

2. 選取含有函式應用程式專案的資料夾，然後選取 [Durable Functions 協調器]  函式範本。 將名稱保留為預設值 "DurableFunctionsOrchestrator"

    ![選擇協調器範本](./media/quickstart-js-vscode/create-function-choose-template.png)

我們已新增協調器來協調活動函式。 讓我們現在新增參考的活動函式。

### <a name="activity"></a>活動

現在，我們將建立活動函式來實際執行解決方案的工作。

1. 從 [Azure: 函式]  中，選擇 [建立函式]  圖示。

    ![建立函式](./media/quickstart-js-vscode/create-function.png)

2. 選取含有函式應用程式專案的資料夾，然後選取 [Durable Functions 活動]  函式範本。 將名稱保留為預設值 "Hello"。

    ![選擇活動範本](./media/quickstart-js-vscode/create-function-choose-template.png)

我們現在已新增要開始協調流程以及將活動函式鏈結在一起所需的所有元件。

## <a name="test-the-function-locally"></a>在本機測試函式

Azure Functions Core Tools 可讓您在本機開發電腦上執行 Azure Functions 專案。 第一次從 Visual Studio Code 啟動函式時，系統會提示您安裝這些工具。

1. 在 Windows 電腦上，啟動 Azure 儲存體模擬器，並確定 local.settings.json  的 **AzureWebJobsStorage** 屬性設定為 `UseDevelopmentStorage=true`。

    針對 Storage Emulator 5.8，請確定 local.settings.json 的 **AzureWebJobsSecretStorageType** 屬性設定為 `files`。 在 Mac 或 Linux 電腦上，您必須將 **AzureWebJobsStorage** 屬性設定為現有 Azure 儲存體帳戶的連接字串。 您稍後會在本文中建立儲存體帳戶。

2. 若要測試您的函式，可在函式程式碼中設定中斷點，並按 F5 以啟動函式應用程式專案。 Core Tools 的輸出會顯示在**終端機**面板中。 如果這是您第一次使用 Durable Functions，則會安裝Durable Functions 擴充功能 且建置可能需要幾秒鐘的時間。

    > [!NOTE]
    > JavaScript Durable Functions 需要 **1.7.0** 版或更新版的 **Microsoft.Azure.WebJobs.Extensions.DurableTask** 擴充功能。 從 Azure Functions 應用程式的根資料夾執行下列命令，以安裝 Durable Functions 擴充功能 `func extensions install -p Microsoft.Azure.WebJobs.Extensions.DurableTask -v 1.7.0`

3. 在**終端機**面板中，複製 HTTP 觸發函式的 URL 端點。

    ![Azure 本機輸出](../media/functions-create-first-function-vs-code/functions-vscode-f5.png)

4. 將 `{functionName}` 取代為 `DurableFunctionsOrchestrator`。

5. 使用 [Postman](https://www.getpostman.com/) 或 [cURL](https://curl.haxx.se/) 之類的工具，將 HTTP POST 要求傳送至 URL 端點。

   回應是 HTTP 函式的初始結果，讓我們知道耐久協調流程已成功啟動。 這還不是協調流程的最終結果。 回應包含一些實用的 URL。 讓現在我們查詢協調流程的狀態。

6. 複製 `statusQueryGetUri` 的 URL 值並將它貼在瀏覽器的網址列中，然後執行要求。 或者，您也可以繼續使用 Postman 來發出 GET 要求。

   此要求會查詢協調流程執行個體的狀態。 您應該會取得最終回應，內容指出執行個體已完成，並包含耐久函式的輸出或結果。 如下所示： 

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

7. 若要停止偵錯，請在 VS Code 中按 **Shift + F5**。

確認函式在本機電腦上正確執行之後，就可以將專案發佈到 Azure。

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>在 Azure 中測試您的函式

1. 從 [輸出]  面板中複製 HTTP 觸發程序的 URL。 呼叫 HTTP URL 觸發函式的 URL 應採用下列格式：

        http://<functionappname>.azurewebsites.net/orchestrators/<functionname>

2. 將 HTTP 要求的新 URL 貼到瀏覽器的網址列。 在使用已發佈的應用程式之前，您應會取得如同以往的相同狀態回應。

## <a name="next-steps"></a>後續步驟

您已使用 Visual Studio Code 來建立及發佈 JavaScript 耐久函式應用程式。

> [!div class="nextstepaction"]
> [了解常見的耐久函式模式](durable-functions-overview.md#application-patterns)