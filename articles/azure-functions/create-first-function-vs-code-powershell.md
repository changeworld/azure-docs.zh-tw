---
title: 使用 Visual Studio Code 建立 PowerShell 函式 - Azure Functions
description: 了解如何建立 PowerShell 函式，然後使用 Visual Studio Code 中的 Azure Functions 擴充功能，將本機專案發佈至 Azure Functions 中的無伺服器裝載。
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: ad129d8faec5725a6e76f14577e6a0e9deae8d86
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968002"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>快速入門：使用 Visual Studio Code 在 Azure 中建立 PowerShell 函式

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

在本文中，您會使用 Visual Studio Code 建立可回應 HTTP 要求的 PowerShell 函式。 在本機測試程式碼之後，您可以將其部署到 Azure Functions 的無伺服器環境。

完成本快速入門後，您的 Azure 帳戶中會產生幾美分或更少的少許費用。

本文還有 [CLI 型版本](create-first-function-cli-powershell.md)。

## <a name="configure-your-environment"></a>設定環境

開始使用之前，請先確定您具備下列必要項目︰

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 3.x 版。

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core 3.1 執行階段](https://www.microsoft.com/net/download)和 [.NET Core 2.1 執行階段](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ 其中一個[支援的平台](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上有 [Visual Studio Code](https://code.visualstudio.com/)。    

+ [適用於 Visual Studio Code 的 PowerShell 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)。  

+ 適用於 Visual Studio Code 的 [Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)。 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>建立本機專案

在本節中，您會使用 Visual Studio Code 在 PowerShell 中建立本機 Azure Functions 專案。 稍後在本文中，您會將函式程式碼發佈至 Azure。

1. 選擇活動列中的 Azure 圖示，然後在 [Azure：  函式] 區域，選取 [建立新專案...]  圖示。

    ![選擇建立新專案](./media/functions-create-first-function-vs-code/create-new-project.png)

1. 選擇您專案工作區的目錄位置，然後選擇 [選取]  。

    > [!NOTE]
    > 這些步驟主要設計為在工作區以外的地方完成。 在此案例中，請勿選取屬於工作區的專案資料夾。

1. 依照提示提供下列資訊：

    + **為您的函式專案選取語言**：選擇 `PowerShell`。

    + **選取您的專案第一個函式的範本**：選擇 `HTTP trigger`。

    + **提供函式名稱**：輸入 `HttpExample`。

    + **授權層級** 選擇 `Anonymous`，讓任何人都能呼叫您的函式端點。 若要了解授權層級，請參閱[授權金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    + **選取您開啟專案的方式**：選擇 `Add to workspace`。

1. Visual Studio Code 會使用這項資訊產生具有 HTTP 觸發程序的 Azure Functions 專案。 您可以在 Explorer 中檢視本機專案檔。 若要深入了解所建立的檔案，請參閱[產生的專案檔](functions-develop-vs-code.md#generated-project-files)。 

[!INCLUDE [functions-run-function-test-local-vs-code-ps](../../includes/functions-run-function-test-local-vs-code-ps.md)]

確認函式在本機電腦上正確執行之後，就可以使用 Visual Studio Code 將專案直接發佈到 Azure。 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>後續步驟

您已透過 Visual Studio Code，使用簡單的 HTTP 觸發函式建立函式應用程式。 在下一篇文章中，您可以藉由新增輸出繫結來展開該函式。 這個繫結會從 HTTP 要求將字串寫入 Azure 佇列儲存體佇列中的訊息。 

> [!div class="nextstepaction"]
> [連線至 Azure 儲存體佇列](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
