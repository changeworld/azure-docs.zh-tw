---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/16/2020
ms.author: glenga
ms.openlocfilehash: 916aa2552e5dd004ec767df98ce7c78f7320efd0
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964067"
---
## <a name="run-the-function-locally"></a>在本機執行函式

Azure Functions Core Tools 與 Visual Studio Code 整合，可讓您在本機執行和偵錯 Azure Functions 專案。  

1. 若要偵錯您的函式，請在您連結偵錯工具之前，先將呼叫插入函式程式碼中的 [`Wait-Debugger`](/powershell/module/microsoft.powershell.utility/wait-debugger?view=powershell-6) Cmdlet，然後按 F5 來啟動函式應用程式專案並連結偵錯工具。 Core Tools 的輸出會顯示在**終端機**面板中。

1. 在**終端機**面板中，複製 HTTP 觸發函式的 URL 端點。

    ![Azure 本機輸出](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. 將查詢字串 `?name=<yourname>` 附加至此 URL，然後在次要 PowerShell 命令提示字元中使用 `Invoke-RestMethod` 執行要求，如下所示：

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    您也可以在瀏覽器中透過下列 URL 執行 GET 要求：

    <http://localhost:7071/api/HttpExample?name=PowerShell>

    如果您呼叫 HttpTrigger 端點，但未在本文中傳遞 `name` 參數作為查詢參數，則函式會傳回 `BadRequest` 錯誤。 當您檢閱 run.ps1 中的程式碼時，您會看到這個錯誤按設計發生。

1. 要求的相關資訊會顯示在 [終端機]  面板中。

    ![終端機面板中的函式執行](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. 若要停止偵錯，請按 Ctrl + C 來停止 Core Tools。

確認函式在本機電腦上正確執行之後，就可以將專案發佈到 Azure。

> [!NOTE]
> 請記得先移除對 `Wait-Debugger` 的任何呼叫，再將函式發佈至 Azure。 