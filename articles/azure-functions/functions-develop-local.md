---
title: 在本地開發和運行 Azure 函數
description: 在於 Azure Functions 上執行 Azure 函式之前，先了解如何撰寫 Azure 函式並在本機電腦上進行測試。
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74230639"
---
# <a name="code-and-test-azure-functions-locally"></a>撰寫 Azure Functions 並在本機進行測試

雖然您能夠在 [Azure 入口網站]中開發及測試 Azure Functions，但許多開發人員仍偏好本機開發體驗。 Functions 可讓您輕鬆使用最喜愛的程式碼編輯器及開發工具，在本機電腦上建立及測試函式。 您的本機函式可以連線到即時 Azure 服務，而且您可以在本機電腦上使用完整的 Functions 執行階段進行偵錯。

## <a name="local-development-environments"></a>本機開發環境

您在本機電腦上開發函式的方式取決於您的[語言](supported-languages.md)和工具喜好設定。 下表中的環境支援本機開發：

|環境                              |Languages         |描述|
|-----------------------------------------|------------|---|
|[視覺工作室代碼](functions-develop-vs-code.md)| [C# （類庫）](functions-dotnet-class-library.md)， [C# 腳本 （.csx）](functions-reference-csharp.md)， [JavaScript，](functions-reference-node.md) [PowerShell，](functions-create-first-function-powershell.md) [Python](functions-reference-python.md) | [適用於 VS Code 的 Azure Functions 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)將 Functions 支援新增至 VS Code。 需要 Core Tools。 使用 2.x 版 Core Tools 時，支援在 Linux、MacOS 和 Windows 上進行開發。 若要深入了解，請參閱[使用 Visual Studio Code 建立第一個函式](functions-create-first-function-vs-code.md)。 |
| [命令提示字元或終端機](functions-run-local.md) | [C# （類庫）](functions-dotnet-class-library.md)， [C# 腳本 （.csx）](functions-reference-csharp.md)， [JavaScript，](functions-reference-node.md) [PowerShell，](functions-reference-powershell.md) [Python](functions-reference-python.md) | [Azure 函數核心工具]提供用於創建啟用本地開發的功能的核心運行時和範本。 2.x 版支援在 Linux、MacOS 和 Windows 上進行開發。 所有環境都依賴 Core Tools 執行本機 Functions 執行階段。 |
| [視覺工作室 2019](functions-develop-vs.md) | [C# (類別庫)](functions-dotnet-class-library.md) | Azure 函數工具組含在[Visual Studio 2019](https://www.visualstudio.com/vs/)和更高版本的**Azure 開發**工作負荷中。 讓您編譯類別庫中的函式，並將 .dll 發佈至 Azure。 包括用於本機測試的 Core Tools。 若要深入了解，請參閱[使用 Visual Studio 開發 Azure Functions](functions-develop-vs.md)。 |
| [Maven](functions-create-first-java-maven.md) (各種) | [JAVA](functions-reference-java.md) | 與 Core Tools 整合以便進行 Java 函式開發。 2.x 版支援在 Linux、MacOS 和 Windows 上進行開發。 若要深入了解，請參閱[使用 Java 和 Maven 建立您的第一個函式](functions-create-first-java-maven.md)。 也支援使用 [Eclipse](functions-create-maven-eclipse.md) 和 [IntelliJ IDEA](functions-create-maven-intellij.md) 進行開發 |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

每個本機開發環境都可讓您建立函式應用程式專案，並使用預先定義的 Functions 範本來建立新的函式。 每個環境都會使用 Core Tool，以便您在自己的電腦上針對實際的 Functions 執行階段進行您的函式測試和偵錯，就如同處理任何其他應用程式一樣。 還可以將函數應用專案從其中任何一個環境發佈到 Azure。  

## <a name="next-steps"></a>後續步驟

+ 要瞭解有關使用 Visual Studio 2019 開發已編譯 C# 函數的本地開發，請參閱[使用 Visual Studio 開發 Azure 函數](functions-develop-vs.md)。
+ 要瞭解有關在 Mac、Linux 或 Windows 電腦上使用 VS 代碼進行功能本地開發的更多資訊，請參閱[從 VS 代碼部署 Azure 函數](/azure/javascript/tutorial-vscode-serverless-node-01)。
+ 若要深入了解如何從命令提示字元或終端機開發函式，請參閱[使用 Azure Functions Core Tools](functions-run-local.md)。

<!-- LINKS -->

[Azure 函數核心工具]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure 門戶]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
