---
title: 在本機開發和執行 Azure Functions
description: 瞭解如何在本機電腦上進行 Azure Functions 程式碼和測試，再于 Azure Functions 上執行。
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c37d51abcc8d612b777b845515cf07666369d4f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96168129"
---
# <a name="code-and-test-azure-functions-locally"></a>撰寫 Azure Functions 並在本機進行測試

雖然您能夠在 [Azure 入口網站]中開發及測試 Azure Functions，但許多開發人員仍偏好本機開發體驗。 Functions 可讓您輕鬆使用最喜愛的程式碼編輯器及開發工具，在本機電腦上建立及測試函式。 您的本機函式可以連線到即時 Azure 服務，而且您可以在本機電腦上使用完整的 Functions 執行階段進行偵錯。

## <a name="local-development-environments"></a>本機開發環境

您在本機電腦上開發函式的方式取決於您的[語言](supported-languages.md)和工具喜好設定。 下表中的環境支援本機開發：

|環境                              |語言         |描述|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C # (類別庫) ](functions-dotnet-class-library.md)、 [c # 腳本 (. .Csx) ](functions-reference-csharp.md)、 [JavaScript](functions-reference-node.md)、 [PowerShell](./create-first-function-vs-code-powershell.md)、 [Python](functions-reference-python.md) | [適用於 VS Code 的 Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)將 Functions 支援新增至 VS Code。 需要 Core Tools。 使用 2.x 版 Core Tools 時，支援在 Linux、MacOS 和 Windows 上進行開發。 若要深入了解，請參閱[使用 Visual Studio Code 建立第一個函式](./create-first-function-vs-code-csharp.md)。 |
| [命令提示字元或終端機](functions-run-local.md) | [C # (類別庫) ](functions-dotnet-class-library.md)、 [c # 腳本 (. .Csx) ](functions-reference-csharp.md)、 [JavaScript](functions-reference-node.md)、 [PowerShell](functions-reference-powershell.md)、 [Python](functions-reference-python.md) | [Azure Functions Core Tools] 提供核心執行時間和範本，可用來建立可啟用本機開發的函式。 2.x 版支援在 Linux、MacOS 和 Windows 上進行開發。 所有環境都依賴 Core Tools 執行本機 Functions 執行階段。 |
| [Visual Studio 2019](functions-develop-vs.md) | [C# (類別庫)](functions-dotnet-class-library.md) | Azure Functions 工具組含在 [Visual Studio 2019](https://www.visualstudio.com/vs/)和更新版本的 **Azure 開發** 工作負載中。 讓您編譯類別庫中的函式，並將 .dll 發佈至 Azure。 包括用於本機測試的 Core Tools。 若要深入了解，請參閱[使用 Visual Studio 開發 Azure Functions](functions-develop-vs.md)。 |
| [Maven](./create-first-function-cli-java.md) (各種) | [Java](functions-reference-java.md) | 與 Core Tools 整合以便進行 Java 函式開發。 2.x 版支援在 Linux、MacOS 和 Windows 上進行開發。 若要深入了解，請參閱[使用 Java 和 Maven 建立您的第一個函式](./create-first-function-cli-java.md)。 也支援使用 [Eclipse](functions-create-maven-eclipse.md) 和 [IntelliJ IDEA](functions-create-maven-intellij.md) 進行開發 |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

每個本機開發環境都可讓您建立函式應用程式專案，並使用預先定義的 Functions 範本來建立新的函式。 每個環境都會使用 Core Tool，以便您在自己的電腦上針對實際的 Functions 執行階段進行您的函式測試和偵錯，就如同處理任何其他應用程式一樣。 您也可以從上述任何環境將函式應用程式專案發佈到 Azure。

## <a name="next-steps"></a>後續步驟

+ 若要深入瞭解使用 Visual Studio 2019 進行編譯 c # 函式的本機開發，請參閱 [使用 Visual Studio 開發 Azure Functions](functions-develop-vs.md)。
+ 若要深入瞭解如何使用 Mac、Linux 或 Windows 電腦上的 VS Code 在本機開發函式，請參閱 [從 VS Code 部署 Azure Functions](/azure/developer/javascript/tutorial-vscode-serverless-node-01)。
+ 若要深入了解如何從命令提示字元或終端機開發函式，請參閱[使用 Azure Functions Core Tools](functions-run-local.md)。

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 入口網站]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows