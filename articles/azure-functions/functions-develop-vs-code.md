---
title: 使用 Visual Studio Code 來開發 Azure Functions
description: 瞭解如何使用 Visual Studio Code 的 Azure Functions 擴充功能來開發和測試 Azure Functions。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c851f5284b87f224932b027fd10ce720327639c2
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167894"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>使用 Visual Studio Code 來開發 Azure Functions

[Visual Studio Code 的 Azure Functions 擴充]功能可讓您在本機開發函式，並將其部署到 Azure。 如果這是您第一次體驗 Azure Functions，可至 [Azure Functions 簡介](functions-overview.md)深入了解。

Azure Functions 擴充功能提供下列優點：

* 在本機開發電腦上編輯、建置及執行函數。
* 直接將 Azure Functions 專案發佈至 Azure。
* 以各種不同的語言撰寫您的函式，同時利用 Visual Studio Code 的優點。

擴充功能可以與下列語言搭配使用，這些語言是從2.x 版開始的 Azure Functions 執行時間所支援：

* [C # 編譯](functions-dotnet-class-library.md)
* [C # 腳本](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>需要您 [將 c # 腳本設定為預設專案語言](#c-script-projects)。

在本文中，範例目前僅適用于 JavaScript ( # A0) 和 c # 類別庫函式。  

本文提供有關如何使用 Azure Functions 擴充功能來開發函式並將其發佈至 Azure 的詳細資料。 閱讀本文之前，您應該先 [使用 Visual Studio Code 建立您的第一個函數](functions-create-first-function-vs-code.md)。

> [!IMPORTANT]
> 請勿混合單一函式應用程式的本機開發和入口網站開發。 當您從本機專案發佈至函式應用程式時，部署程序將會覆寫您在入口網站開發的任何函式。

## <a name="prerequisites"></a>必要條件

在安裝並執行 Visual Studio Code 的[Azure Functions 擴充]功能[Azure Functions 擴充]功能之前，您必須符合下列需求：

* [Visual Studio Code](https://code.visualstudio.com/) 安裝在其中一個 [支援的平臺](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上。

* 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

當您 [使用 Visual Studio Code 發佈](#publish-to-azure)時，會在您的訂用帳戶中建立您所需的其他資源（例如 Azure 儲存體帳戶）。

> [!IMPORTANT]
> 您可以在本機開發函式並將其發佈至 Azure，而不需要在本機啟動及執行函式。 若要在本機執行您的函式，您必須符合一些額外的需求，包括 Azure Functions Core Tools 的自動下載功能。 若要深入瞭解，請參閱 [在本機執行專案的其他需求](#additional-requirements-for-running-a-project-locally)。

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>建立 Azure Functions 專案

函式擴充功能可讓您建立函數應用程式專案，以及您的第一個函式。 下列步驟說明如何在新的函式專案中建立 HTTP 觸發的函數。 [HTTP 觸發](functions-bindings-http-webhook.md) 程式是最簡單的函式觸發程式範本，可供示範。

1. 從 **Azure：** 函式中，選取 [ **建立函數** ] 圖示：

    ![建立函式](./media/functions-develop-vs-code/create-function.png)

1. 選取函式應用程式專案的資料夾，然後選取函式 **專案的語言**。

1. 如果您尚未安裝 Core Tools，系統會要求您選取要安裝的 Core Tools **版本** 。 選擇2.x 版或更新版本。 

1. 選取 [ **HTTP 觸發** 程式函式] 範本，或者您可以選取 [ **立即略過** ] 來建立沒有函數的專案。 您稍後可以隨時將函式 [新增至您的專案](#add-a-function-to-your-project) 。

    ![選擇 HTTP 觸發程序範本](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 輸入 **HttpExample** 作為函式名稱，並選取 Enter，然後選取 [ **函數** 授權]。 此授權層級會要求您在呼叫函式端點時提供函式 [金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys) 。

    ![選取函數授權](./media/functions-develop-vs-code/create-function-auth.png)

    系統會在您所選擇的語言中建立函式，並在 HTTP 觸發的函式範本中建立函式。

    ![Visual Studio Code 中的 HTTP 觸發函式範本](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>產生的專案檔

專案範本會以您選擇的語言建立專案，並安裝所需的相依性。 在任何語言中，新專案都有下列檔案：

* **host.json**：讓您設定 Functions 主機。 當您在本機執行函式，以及當您在 Azure 中執行函式時，就會套用這些設定。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json.md)。

* **local.settings.js**：會維護您在本機執行函式時所使用的設定。 只有當您在本機執行函式時，才會使用這些設定。 如需詳細資訊，請參閱 [本機設定檔](#local-settings-file)。

    >[!IMPORTANT]
    >因為檔案上的 local.settings.js可以包含秘密，所以您必須將它從專案原始檔控制中排除。

根據您的語言，會建立這些其他檔案：

# <a name="c"></a>[C\#](#tab/csharp)

* [HttpExample.cs 類別庫](functions-dotnet-class-library.md#functions-class-library-project) 檔案，此檔案會執行函數。

此時，您可以藉由將 [參數加入至 c # 類別庫](#add-input-and-output-bindings)函式，將輸入和輸出系結新增至您的函式。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* 根資料夾中的檔案 package.js。

* HttpExample 資料夾，其中包含定義檔和[index.js](functions-reference-node.md#exporting-a-function)檔案的[function.js](functions-reference-node.md#folder-structure) ，這是包含函式程式碼的 Node.js 檔。

此時，您可以藉由 [修改檔案的 function.js](#add-input-and-output-bindings)，將輸入和輸出系結新增至您的函式。

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

您也可以 [將新的](#add-a-function-to-your-project)函式新增至您的專案。

## <a name="install-binding-extensions"></a>安裝繫結延伸模組

除了 HTTP 和計時器觸發程式之外，系結會在擴充功能套件中實作為系結。 您必須為需要的觸發程式和系結安裝擴充套件。 安裝系結延伸模組的程式取決於您專案的語言。

# <a name="c"></a>[C\#](#tab/csharp)

在終端機視窗中執行 [dotnet add package](/dotnet/core/tools/dotnet-add-package) 命令，以在您的專案中安裝所需的擴充套件。 下列命令會安裝 Azure 儲存體擴充功能，其會實作為 Blob、佇列和資料表儲存體的系結。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>將函式新增至您的專案

您可以使用其中一個預先定義的函數觸發程式範本，將新的函式新增至現有的專案。 若要加入新的函式觸發程式，請選取 F1 以開啟命令選擇區，然後搜尋並執行命令 **Azure Functions： Create function**。 遵循提示來選擇您的觸發程式類型，並定義觸發程式的必要屬性。 如果您的觸發程式需要存取金鑰或連接字串來連接到服務，請在建立函式觸發程式之前將其備妥。

此動作的結果取決於您專案的語言：

# <a name="c"></a>[C\#](#tab/csharp)

新的 c # 類別庫 ( .cs) 檔會加入至您的專案。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

專案中會建立新的資料夾。 此資料夾包含檔案的新 function.js以及新的 JavaScript 程式碼檔案。

---

## <a name="add-input-and-output-bindings"></a>新增輸入和輸出系結

您可以藉由加入輸入和輸出系結來展開您的函式。 新增系結的程式取決於您專案的語言。 若要深入了解繫結，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

下列範例會連接到名為的儲存體佇列 `outqueue` ，其中儲存體帳戶的連接字串是在 `MyStorageConnection` local.settings.js的應用程式設定中設定。

# <a name="c"></a>[C\#](#tab/csharp)

更新函式方法，以將下列參數加入至 `Run` 方法定義：

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

此程式碼會要求您加入下列 `using` 語句：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` 參數是 `ICollector<T>` 類型，其代表會在函式完成時寫入輸出繫結的訊息集合。 您可以將一或多個訊息新增至集合。 當函數完成時，這些訊息會傳送至佇列。

若要深入瞭解，請參閱 [佇列儲存體輸出](functions-bindings-storage-queue-output.md) 系結檔。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Visual Studio Code 可讓您遵循一組便利的提示，將系結新增至檔案中的 function.js。 若要建立系結，請在 macOS 上按一下滑鼠 (右鍵，然後按一下 []) 您函式資料夾中的檔案 **function.js** ，然後選取 [ **新增**系結]：

![將系結新增至現有的 JavaScript 函式 ](media/functions-develop-vs-code/function-add-binding.png)

以下是定義新儲存體輸出系結的範例提示：

| Prompt | 值 | 描述 |
| -------- | ----- | ----------- |
| **選取繫結方向** | `out` | 此繫結為輸出繫結。 |
| **選取具有方向的系結** | `Azure Queue Storage` | 此繫結是 Azure 儲存體佇列繫結。 |
| **用來在程式碼中識別此繫結的名稱** | `msg` | 識別您的程式碼中參考之繫結參數的名稱。 |
| **要接收訊息的佇列** | `outqueue` | 作為繫結寫入目標的佇列名稱。 當 *queueName* 不存在，繫結會在第一次使用時加以建立。 |
| **選取 [local.settings.js開啟] 的設定** | `MyStorageConnection` | 包含儲存體帳戶連接字串之應用程式設定的名稱。 此 `AzureWebJobsStorage` 設定包含您使用函式應用程式所建立之儲存體帳戶的連接字串。 |

在此範例中，下列系結會新增至檔案 `bindings` 中 function.js的陣列：

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

您也可以將相同的系結定義直接加入 function.js的。

在您的函式程式碼中， `msg` 會從存取系結 `context` ，如下列範例所示：

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

若要深入瞭解，請參閱 [佇列儲存體輸出](functions-bindings-storage-queue-output.md) 系結參考。

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>發佈至 Azure

Visual Studio Code 可讓您將函式專案直接發行至 Azure。 在這過程中，您會在 Azure 訂用帳戶中建立函式應用程式和相關的資源。 函式應用程式會為函式提供執行內容。 專案會封裝並部署到您 Azure 訂用帳戶中的新函式應用程式。

當您從 Visual Studio Code 發佈至 Azure 中的新函式應用程式時，系統會提供快速的函式應用程式建立路徑和先進的路徑。 

當您從 Visual Studio Code 發佈時，會利用 [Zip 部署](functions-deployment-technologies.md#zip-deploy) 技術。 

### <a name="quick-function-app-create"></a>快速函數應用程式建立

當您 **在 Azure 中選擇 [+ 建立新的函式應用程式**] 時，擴充功能會自動為您的函式應用程式所需的 Azure 資源產生值。 這些值是以您選擇的函式應用程式名稱為基礎。 如需使用預設值將專案發佈至 Azure 中新函數應用程式的範例，請參閱 [Visual Studio Code 快速入門文章](functions-create-first-function-vs-code.md#publish-the-project-to-azure)。

如果您想要為所建立的資源提供明確的名稱，您必須選擇 [advanced create path]。

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>使用 advanced options 將專案發佈至 Azure 中的新函數應用程式

下列步驟會將您的專案發行至以 advanced create options 建立的新函數應用程式：

1. 在 [ **Azure：** 函式] 區域中，選取 [ **部署至函數應用程式** ] 圖示。

    ![函數應用程式設定](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 如果您尚未登入，系統會提示您**登入 Azure**。 您也可以 **建立免費的 Azure 帳戶**。 從瀏覽器登入之後，請回到 Visual Studio Code。

1. 如果您有多個訂用帳戶，請選取函數應用程式的 **訂用** 帳戶，然後選取 [ **+ 在 Azure 中建立新的函數應用程式 ...] _Advanced_**。 這個 _Advanced_ 選項可讓您更充分掌控您在 Azure 中建立的資源。 

1. 遵循提示，提供此資訊：

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----------- |
    | 在 Azure 中選取函數應用程式 | 在 Azure 中建立新的函數應用程式 | 在下一個提示中，輸入識別新函數應用程式的全域唯一名稱，然後選取 Enter。 函式應用程式名稱的有效字元為 `a-z`、`0-9` 和 `-`。 |
    | 選取作業系統 | Windows | 函數應用程式會在 Windows 上執行。 |
    | 選取主控方案 | 使用情況方案 | 使用裝載的無伺服器 [使用量方案](functions-scale.md#consumption-plan) 。 |
    | 選取新應用程式的執行時間 | 您的專案語言 | 執行時間必須符合您要發行的專案。 |
    | 選取新資源的資源群組 | 建立新的資源群組 | 在下一個提示中輸入資源組名（例如 `myResourceGroup` ），然後選取 enter。 您也可以選取現有的資源群組。 |
    | 選取儲存體帳戶 | 建立新的儲存體帳戶 | 在下一個提示中，為您的函數應用程式所使用的新儲存體帳戶輸入全域唯一的名稱，然後選取 [輸入]。 儲存體帳戶名稱的長度必須介於3到24個字元之間，而且只能包含數位和小寫字母。 您也可以選取現有的帳戶。 |
    | 選取新資源的位置 | region | 選取 [區域](https://azure.microsoft.com/regions/) 中接近您或接近您函式存取之其他服務的位置。 |

    建立函數應用程式並套用部署套件之後，會出現通知。 在通知中選取 [檢視輸出]  ，即可檢視建立和部署結果，包括您所建立的 Azure 資源。

## <a name="republish-project-files"></a>重新發佈專案檔案

當您設定 [持續部署](functions-continuous-deployment.md)時，您在 Azure 中的函式應用程式會在已連線來源位置中的來源檔案更新時更新。 建議您持續部署，但您也可以從 Visual Studio Code 重新發佈專案檔更新。

> [!IMPORTANT]
> 發佈至現有的函式應用程式會覆寫該應用程式在 Azure 中的內容。

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="get-the-url-of-the-deployed-function"></a>取得已部署函式的 URL

若要呼叫 HTTP 觸發的函式，您需要函式在部署到函數應用程式時的 URL。 此 URL 包含任何必要的函式 [金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)。 您可以使用擴充功能來取得已部署函式的這些 Url。

1. 選取 F1 以開啟命令選擇區，然後搜尋並執行命令 **Azure Functions：複製**函式 URL。

1. 遵循提示，在 Azure 中選取您的函數應用程式，然後選取您想要叫用的特定 HTTP 觸發程式。

函數 URL 會複製到剪貼簿，以及查詢參數所傳遞的任何必要索引鍵 `code` 。 使用 HTTP 工具來提交 POST 要求，或針對遠端函式取得 GET 要求的瀏覽器。  

## <a name="run-functions-locally"></a>在本機執行函式

Azure Functions 擴充功能可讓您在本機開發電腦上執行函式專案。 本機執行時間是相同的執行時間，可在 Azure 中裝載您的函數應用程式。 本機設定是從檔案 [local.settings.js](#local-settings-file)讀取。

### <a name="additional-requirements-for-running-a-project-locally"></a>在本機執行專案的其他需求

若要在本機執行函數專案，您必須符合下列其他需求：

* 安裝 [Azure Functions Core Tools](functions-run-local.md#v2)的2.x 版或更新版本。 當您在本機啟動專案時，會自動下載並安裝 Core Tools 套件。 Core Tools 包含整個 Azure Functions 執行時間，因此下載和安裝可能需要一些時間。

* 安裝所選語言的特定需求：

    | 語言 | 需求 |
    | -------- | --------- |
    | **C#** | [C# 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI 工具](/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [JAVA 延伸模組的偵錯工具](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)<br/>[Maven 3 或更新版本](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>建議使用[Python 3.6.8](https://www.python.org/downloads/)|

    <sup>*</sup>主動式 LTS 和維護 LTS 版本 (建議使用8.11.1 和10.14.1 建議的) 。

### <a name="configure-the-project-to-run-locally"></a>將專案設定為在本機執行

函數執行時間會在內部針對 HTTP 和 webhook 以外的所有觸發程式類型使用 Azure 儲存體帳戶。 因此，您需要將 **AzureWebJobsStorage** 索引鍵設定為有效的 Azure 儲存體帳戶連接字串。

本節使用[Azure 儲存體總管](https://storageexplorer.com/) [Visual Studio Code 的 Azure 儲存體延伸](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)模組，以連接並取出儲存體連接字串。

設定儲存體帳戶連接字串：

1. 在 Visual Studio 中，開啟**Cloud Explorer**，展開 [**儲存體**帳戶] 儲存體  >  **帳戶**，然後選取 [**屬性**]，並複製**主要連接字串**值。

2. 在您的專案中，開啟 local.settings.json 檔案並將 **AzureWebJobsStorage** 機碼的值設定為您所複製的連接字串。

3. 重複上一步，針對函數所需的其他任何連接，將唯一機碼新增至 [值]**** 陣列。

如需詳細資訊，請參閱 [本機設定檔](#local-settings-file)。

### <a name="debugging-functions-locally"></a>在本機調試函式  

若要偵測您的函式，請選取 F5。 如果您尚未下載 [Core Tools][Azure Functions Core Tools]，系統會提示您這樣做。 當 Core Tools 已安裝且正在執行時，輸出會顯示在終端機中。 這與 `func host start` 從終端機執行 Core Tools 命令相同，但有其他的組建工作和附加的偵錯工具。  

當專案正在執行時，您可以觸發您的函式，就像將專案部署至 Azure 一樣。 當專案以「偵測」模式執行時，會如預期般在 Visual Studio Code 中叫用中斷點。

HTTP 觸發程式的要求 URL 會顯示在終端機的輸出中。 當專案在本機執行時，不會使用 HTTP 觸發程式的函式金鑰。 如需詳細資訊，請參閱[在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)。  

若要深入瞭解，請參閱使用 [Azure Functions Core Tools][Azure Functions Core Tools]。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

依預設，當專案發行至 Azure 時，這些設定不會自動遷移。 發佈完成後，您可以選擇將設定從 local.settings.js發佈至 Azure 中的函數應用程式。 若要深入瞭解，請參閱  [發行應用程式設定](#publish-application-settings)。

**ConnectionStrings** 中的值永遠不會發佈。

函數應用程式設定值也可以在您的程式碼中讀取為環境變數。 如需詳細資訊，請參閱這些語言特定參考文章的環境變數區段：

* [先行編譯 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 指令碼 (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure 中的應用程式設定

您專案中 local.settings.json 檔案的設定，應該與 Azure 中函數應用程式的應用程式設定相同。 您加入至 local.settings.js的任何設定也必須新增至 Azure 中的函數應用程式。 當您發行專案時，不會自動上傳這些設定。 同樣地，您在 [入口網站中](functions-how-to-use-azure-function-app-settings.md#settings) 的函式應用程式中建立的任何設定都必須下載到本機專案。

### <a name="publish-application-settings"></a>發佈應用程式設定

將必要設定發佈至 Azure 中的函式應用程式最簡單的方式，就是使用發佈專案之後出現的 [ **上傳設定** ] 連結：

![上傳應用程式設定](./media/functions-develop-vs-code/upload-app-settings.png)

您也可以使用 [命令選擇區] 中的 [ **Azure Functions：上傳本機設定** ] 命令來發佈設定。 您可以使用 [ **Azure Functions：新增設定** ] 命令，將個別設定新增至 Azure 中的應用程式設定。

> [!TIP]
> 在發佈之前，請務必先儲存您的 local.settings.js。

如果本機檔案已加密，則會進行解密、發佈及加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。

展開您的訂用帳戶、函式應用程式和**應用程式設定**，以在 [ **Azure：** 函式] 區域中查看現有的應用程式設定。

![Visual Studio Code 中的 View 函數應用程式設定](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>從 Azure 下載設定

如果您已在 Azure 中建立應用程式設定，您可以使用 [ **Azure Functions：下載遠端設定** ] 命令，將應用程式設定下載到您的 local.settings.js檔案。

在上傳時，如果本機檔案已加密，則會再次進行解密、更新及加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。

## <a name="monitoring-functions"></a>監視函式

當您在 [本機執行](#run-functions-locally)函式時，會將記錄資料串流處理到終端主控台。 您也可以在函式專案在 Azure 的函式應用程式中執行時，取得記錄檔資料。 您可以連線到 Azure 中的串流記錄以查看近乎即時的記錄資料，或者您可以啟用 Application Insights，以更完整地瞭解函式應用程式的運作方式。

### <a name="streaming-logs"></a>串流記錄

當您正在開發應用程式時，即時查看記錄資訊通常會很有用。 您可以查看函式所產生之記錄檔的資料流程。 此輸出是要求 HTTP 觸發函式的串流記錄範例：

![HTTP 觸發程式的串流記錄輸出](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

若要深入瞭解，請參閱 [串流記錄](functions-monitoring.md#streaming-logs)。

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 串流記錄只支援函數主機的單一實例。 當您的函式調整為多個實例時，其他實例的資料不會顯示在記錄資料流程中。 Application Insights 中的[即時計量資料流](../azure-monitor/app/live-stream.md)支援多個實例。 串流分析在近乎即時的情況下也會以取樣的 [資料](configure-monitoring.md#configure-sampling)為基礎。

### <a name="application-insights"></a>Application Insights

建議您將函數應用程式與 Application Insights 整合，來監視函式的執行。 當您在 Azure 入口網站中建立函數應用程式時，預設會進行這項整合。 當您在 Visual Studio 發佈期間建立函數應用程式時，您必須自行整合 Application Insights。 若要瞭解作法，請參閱 [啟用 Application Insights 整合](configure-monitoring.md#enable-application-insights-integration)。

若要深入瞭解如何使用 Application Insights 進行監視，請參閱 [監視 Azure Functions](functions-monitoring.md)。

## <a name="c-script-projects"></a>C \# 腳本專案

根據預設，所有 c # 專案都會建立為 [c # 編譯的類別庫專案](functions-dotnet-class-library.md)。 如果您想要改為使用 c # 腳本專案，您必須選取 c # 腳本做為 Azure Functions 延伸模組設定中的預設語言：

1. 選取 **[** 檔案  >  **喜好**  >  **設定**]。

1. 移至 [**使用者設定**  >  **延伸**模組]  >  **Azure Functions**。

1. 從**Azure Function： Project Language**選取**C # 腳本**。

完成這些步驟之後，對基礎核心工具的呼叫會包含 `--csx` 選項，此選項會產生 c # 腳本 ( .csx) 專案檔。 當您指定此預設語言時，您建立的所有專案都會預設為 c # 腳本專案。 設定預設值時，系統不會提示您選擇專案語言。 若要建立其他語言的專案，您必須變更這項設定，或將它從使用者 settings.js檔案中移除。 移除此設定之後，當您建立專案時，系統會再次提示您選擇語言。

## <a name="command-palette-reference"></a>命令選擇區參考

Azure Functions 擴充功能可在區域中提供實用的圖形介面，以便與 Azure 中的函式應用程式互動。 命令選擇區中的命令也可使用相同的功能 (F1) 。 這些 Azure Functions 命令可供使用：

|Azure Functions 命令  | 描述  |
|---------|---------|
|**新增設定**  |  在 Azure 中建立新的應用程式設定。 若要深入瞭解，請參閱 [發行應用程式設定](#publish-application-settings)。 您可能也需要將 [此設定下載至您的本機設定](#download-settings-from-azure)。 |
| **設定部署來源** | 將 Azure 中的函數應用程式連接至本機 Git 存放庫。 若要深入瞭解，請參閱 [Azure Functions 的持續部署](functions-continuous-deployment.md)。 |
| **連接到 GitHub 存放庫** | 將您的函數應用程式連線到 GitHub 存放庫。 |
| **複製函式 URL** | 取得在 Azure 中執行之 HTTP 觸發函式的遠端 URL。 若要深入瞭解，請參閱 [取得已部署函數的 URL](#get-the-url-of-the-deployed-function)。 |
| **在 Azure 中建立函數應用程式** | 在您的 Azure 訂用帳戶中建立新的函數應用程式。 若要深入瞭解，請參閱如何 [在 Azure 中發佈至新的函式應用程式](#publish-to-azure)一節。        |
| **解密設定** | 解密由 Azure Functions 加密的 [本機設定](#local-settings-file) **：加密設定**。  |
| **刪除函數應用程式** | 從 Azure 中的訂用帳戶移除函式應用程式。 當 App Service 方案中沒有其他應用程式時，您也可以選擇刪除該應用程式。 不會刪除其他資源，例如儲存體帳戶和資源群組。 若要移除所有資源，您應該改為 [刪除資源群組](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)。 您的本機專案不會受到影響。 |
|**Delete 函式**  | 從 Azure 中的函數應用程式移除現有的函式。 因為此刪除並不會影響您的本機專案，所以請考慮在本機移除函式，然後重新發行 [您的專案](#republish-project-files)。 |
| **刪除 Proxy** | 從 Azure 中的函數應用程式移除 Azure Functions proxy。 若要深入瞭解 proxy，請參閱使用 [Azure Functions Proxy](functions-proxies.md)。 |
| **刪除設定** | 刪除 Azure 中的函數應用程式設定。 這項刪除作業並不會影響檔案 local.settings.js的設定。 |
| **中斷與存放庫的連線**  | 移除 Azure 中的函數應用程式與原始檔控制存放庫之間的 [持續部署](functions-continuous-deployment.md) 連線。 |
| **下載遠端設定** | 從 Azure 中選擇的函式應用程式，將設定下載到您的 local.settings.js檔案。 如果本機檔案已加密，則會再次進行解密、更新及加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。 執行此命令之前，請務必先儲存 local.settings.js檔案的變更。 |
| **編輯設定** | 變更 Azure 中現有函數應用程式設定的值。 此命令不會影響檔案 local.settings.js的設定。  |
| **加密設定** | 在 `Values` [本機設定](#local-settings-file)中加密陣列中的個別專案。 在這個檔案中， `IsEncrypted` 也會設定為 `true` ，指定本機執行時間在使用之前將會解密設定。 加密本機設定，以降低洩漏重要資訊的風險。 在 Azure 中，應用程式設定一律會以加密方式儲存。 |
| **立即執行函數** | 在 Azure 中手動啟動 [計時器觸發](functions-bindings-timer.md) 的函式。 此命令用於測試。 若要深入瞭解如何在 Azure 中觸發非 HTTP 函式，請參閱 [手動執行非 HTTP 觸發](functions-manually-run-non-http.md)的函式。 |
| **初始化專案以搭配 VS Code 使用** | 將必要的 Visual Studio Code 專案檔加入至現有的函式專案。 您可以使用此命令來處理您使用 Core Tools 建立的專案。 |
| **安裝或更新 Azure Functions Core Tools** | 安裝或更新 [Azure Functions Core Tools]，用來在本機執行函式。 |
| **重新部署**  | 可讓您將專案檔從已連線的 Git 存放庫重新部署至 Azure 中的特定部署。 若要重新發佈 Visual Studio Code 的本機更新，請重新 [發佈您的專案](#republish-project-files)。 |
| **重新命名設定** | 變更 Azure 中現有函數應用程式設定的索引鍵名稱。 此命令不會影響檔案 local.settings.js的設定。 在 Azure 中重新命名設定之後，您應該將 [這些變更下載至本機專案](#download-settings-from-azure)。 |
| **重新啟動** | 在 Azure 中重新開機函數應用程式。 部署更新也會重新開機函數應用程式。 |
| **設定 AzureWebJobsStorage**| 設定 `AzureWebJobsStorage` 應用程式設定的值。 這是 Azure Functions 所需的設定。 它是在 Azure 中建立函數應用程式時設定。 |
| **開始** | 在 Azure 中啟動已停止的函式應用程式。 |
| **啟動串流記錄** | 針對 Azure 中的函數應用程式啟動串流記錄。 如果您需要近乎即時地查看記錄資訊，請在 Azure 的遠端疑難排解期間使用串流記錄。 若要深入瞭解，請參閱 [串流記錄](#streaming-logs)。 |
| **停止** | 停止在 Azure 中執行的函數應用程式。 |
| **停止串流記錄** | 停止 Azure 中函數應用程式的串流記錄。 |
| **切換為插槽設定** | 啟用時，可確保針對指定的部署位置保存應用程式設定。 |
| **卸載 Azure Functions Core Tools** | 移除擴充功能所需的 Azure Functions Core Tools。 |
| **上傳本機設定** | 將檔案 local.settings.js的設定上傳至 Azure 中所選擇的函式應用程式。 如果本機檔案已加密，則會將它解密、上傳並重新加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。 執行此命令之前，請務必先儲存 local.settings.js檔案的變更。 |
| **在 GitHub 中查看認可** | 當您的函式應用程式連線到存放庫時，顯示特定部署中的最新認可。 |
| **查看部署記錄** | 顯示特定部署至 Azure 中函數應用程式的記錄。 |

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure Functions Core Tools，請參閱使用 [Azure Functions Core Tools](functions-run-local.md)。

若要深入了解如何將函式開發為 .NET 類別庫，請參閱 [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)。 本文也提供範例的連結，說明如何使用屬性來宣告 Azure Functions 所支援的各種系結類型。

[適用於 Visual Studio Code 的 Azure Functions 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions \(英文\)
[Azure Functions Core Tools]: functions-run-local.md