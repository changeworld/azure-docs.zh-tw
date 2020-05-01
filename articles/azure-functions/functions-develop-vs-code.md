---
title: 使用 Visual Studio Code 開發 Azure Functions
description: 瞭解如何使用適用于 Visual Studio Code 的 Azure Functions 延伸模組來開發和測試 Azure Functions。
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277163"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>使用 Visual Studio Code 開發 Azure Functions

[Visual Studio Code 的 Azure Functions 擴充]功能可讓您在本機開發函式，並將其部署至 Azure。 如果這是您第一次體驗 Azure Functions，可至 [Azure Functions 簡介](functions-overview.md)深入了解。

Azure Functions 延伸模組提供下列優點：

* 在本機開發電腦上編輯、建置及執行函數。
* 直接將 Azure Functions 專案發佈至 Azure。
* 撰寫各種語言的函數，同時利用 Visual Studio Code 的優點。

延伸模組可以與下列語言搭配使用，這些語言是從2.x 版開始的 Azure Functions 執行時間所支援：

* [C # 編譯](functions-dotnet-class-library.md)
* [C # 腳本](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>需要您[將 c # 腳本設定為預設的專案語言](#c-script-projects)。

在本文中，範例目前僅適用于 JavaScript （node.js）和 c # 類別庫函式。  

本文提供如何使用 Azure Functions 擴充功能來開發函式並將其發佈至 Azure 的詳細資料。 閱讀本文之前，您應該先[使用 Visual Studio Code 建立您的第一個](functions-create-first-function-vs-code.md)函式。

> [!IMPORTANT]
> 請勿混用單一函式應用程式的本機開發與入口網站開發。 當您從本機專案發佈至函式應用程式時，部署程序將會覆寫您在入口網站開發的任何函式。

## <a name="prerequisites"></a>先決條件

在您安裝並執行 Visual Studio Code 的[Azure Functions 延伸]模組[Azure Functions 延伸]模組之前，您必須符合下列需求：

* [Visual Studio Code](https://code.visualstudio.com/)安裝在其中一個[支援的平臺](https://code.visualstudio.com/docs/supporting/requirements#_platforms)上。

* 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

當您[使用 Visual Studio Code 發行](#publish-to-azure)時，您的訂用帳戶中會建立您所需的其他資源（例如 Azure 儲存體帳戶）。

> [!IMPORTANT]
> 您可以在本機開發函式，並將其發佈至 Azure，而不需要在本機啟動和執行它們。 若要在本機執行您的函式，您必須符合一些額外需求，包括自動下載 Azure Functions Core Tools。 若要深入瞭解，請參閱[在本機執行專案的其他需求](#additional-requirements-for-running-a-project-locally)。

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>建立 Azure Functions 專案

函式擴充功能可讓您建立函數應用程式專案，以及您的第一個函式。 下列步驟示範如何在新的函式專案中建立 HTTP 觸發的函數。 [HTTP 觸發](functions-bindings-http-webhook.md)程式是要示範的最簡單函式觸發程式範本。

1. 在 [ **Azure：函數**] 中，選取 [**建立**函式] 圖示：

    ![建立函式](./media/functions-develop-vs-code/create-function.png)

1. 選取函數應用程式專案的資料夾，然後選取函式**專案的語言**。

1. 如果您尚未安裝核心工具，系統會要求您選取要安裝的核心工具**版本**。 選擇2.x 版或更新版本。 

1. 選取 [ **HTTP 觸發**程式] 函式範本，或者您可以選取 [**立即略過**] 來建立不含函式的專案。 您稍後可以隨時將函式[新增至您的專案](#add-a-function-to-your-project)。

    ![選擇 HTTP 觸發程序範本](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 在 [函式名稱] 中輸入**HttpExample** ，然後選取 Enter，再選取 [**函數**授權]。 當您呼叫函式端點時，此授權層級會要求您提供[函數金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    ![選取函數授權](./media/functions-develop-vs-code/create-function-auth.png)

    函式會以您所選擇的語言建立，並在 HTTP 觸發的函數範本中建立。

    ![Visual Studio Code 中的 HTTP 觸發函式範本](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>產生的專案檔

專案範本會以您選擇的語言建立專案，並安裝所需的相依性。 針對任何語言，新的專案會有下列檔案：

* **host.json**：讓您設定 Functions 主機。 當您在本機執行函式時，以及在 Azure 中執行函式時，會套用這些設定。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json.md)。

* [**本機**]：維護在本機執行函式時所使用的設定。 只有當您在本機執行函數時，才會使用這些設定。 如需詳細資訊，請參閱[本機設定檔](#local-settings-file)。

    >[!IMPORTANT]
    >因為本機. 設定的 json 檔案可以包含秘密，所以您必須將它從您的專案原始檔控制中排除。

根據您的語言而定，會建立這些其他檔案：

# <a name="c"></a>[C\#](#tab/csharp)

* 實作用函式的[HttpExample.cs 類別庫](functions-dotnet-class-library.md#functions-class-library-project)檔案。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* 根資料夾中的 package. json 檔案。

* HttpExample 資料夾，其中包含函式[. json 定義](functions-reference-node.md#folder-structure)檔和[.js](functions-reference-node.md#exporting-a-function)檔案，也就是包含函式程式碼的 node.js 檔案。

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

此時，您可以藉由修改函式[json](#add-a-function-to-your-project)檔案或[將參數加入至 c # 類別庫](#add-a-function-to-your-project)函式，將輸入和輸出系結新增至函式。

您也可以[將新的函式加入至專案](#add-a-function-to-your-project)。

## <a name="install-binding-extensions"></a>安裝繫結延伸模組

除了 HTTP 和計時器觸發程式以外，系結會在延伸模組套件中執行。 您必須為需要的觸發程式和系結安裝延伸模組套件。 安裝系結延伸模組的程式取決於您專案的語言。

# <a name="c"></a>[C\#](#tab/csharp)

在終端機視窗中執行[dotnet add package](/dotnet/core/tools/dotnet-add-package)命令，以在您的專案中安裝所需的延伸模組套件。 下列命令會安裝 Azure 儲存體延伸模組，其會執行 Blob、佇列和資料表儲存體的系結。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>將函式新增至您的專案

您可以使用其中一個預先定義的函數觸發程式範本，將新的函式加入至現有的專案。 若要新增函式觸發程式，請選取 F1 以開啟命令選擇區，然後搜尋並執行命令**Azure Functions： Create function**。 依照提示選擇觸發程式類型，並定義觸發程式的必要屬性。 如果您的觸發程式需要存取金鑰或連接字串，才能連接到服務，請在建立函式觸發程式之前，先備妥它。

此動作的結果取決於您專案的語言：

# <a name="c"></a>[C\#](#tab/csharp)

新的 c # 類別庫（.cs）檔案會加入至您的專案。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

專案中會建立新的資料夾。 此資料夾包含新的函式 json 檔案和新的 JavaScript 程式碼檔案。

---

## <a name="add-input-and-output-bindings"></a>新增輸入和輸出系結

您可以藉由新增輸入和輸出系結來擴充您的函式。 新增系結的程式取決於您專案的語言。 若要深入了解繫結，請參閱 [Azure Functions 觸發程序和繫結概念](functions-triggers-bindings.md)。

下列範例會連線到名為`outqueue`的儲存體佇列，其中儲存體帳戶的連接字串是在 application 設定的`MyStorageConnection` [web.config] 中設定。

# <a name="c"></a>[C\#](#tab/csharp)

更新函數方法，將下列參數新增至`Run`方法定義：

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

此程式碼會要求您新增下列`using`語句：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` 參數是 `ICollector<T>` 類型，其代表會在函式完成時寫入輸出繫結的訊息集合。 您可以將一或多個訊息新增至集合。 當函式完成時，會將這些訊息傳送至佇列。

若要深入瞭解，請參閱[佇列儲存體輸出](functions-bindings-storage-queue-output.md)系結檔。

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Visual Studio Code 可讓您遵循一組方便的提示，將系結新增至您的函式. json 檔案。 若要建立系結，請以滑鼠右鍵按一下（Ctrl + 按一下 [macOS]）函式資料夾中的函式**json**檔案，然後選取 [**新增**系結]：

![將系結新增至現有的 JavaScript 函式 ](media/functions-develop-vs-code/function-add-binding.png)

以下是定義新儲存體輸出系結的範例提示：

| Prompt | 值 | 描述 |
| -------- | ----- | ----------- |
| **選取繫結方向** | `out` | 此繫結為輸出繫結。 |
| **選取具有方向的系結** | `Azure Queue Storage` | 此繫結是 Azure 儲存體佇列繫結。 |
| **用來在程式碼中識別此繫結的名稱** | `msg` | 識別您的程式碼中參考之繫結參數的名稱。 |
| **要接收訊息的佇列** | `outqueue` | 作為繫結寫入目標的佇列名稱。 當 *queueName* 不存在，繫結會在第一次使用時加以建立。 |
| **從 [local. settings. json] 選取設定** | `MyStorageConnection` | 應用程式設定的名稱，其中包含儲存體帳戶的連接字串。 此`AzureWebJobsStorage`設定包含您使用函數應用程式所建立之儲存體帳戶的連接字串。 |

在此範例中，會將下列系結新增`bindings`至函式 json 檔案中的陣列：

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

您也可以將相同的系結定義直接加入至您的函數. json。

在您的函式程式`msg`代碼中，會從`context`存取系結，如下列範例所示：

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

若要深入瞭解，請參閱[佇列儲存體輸出](functions-bindings-storage-queue-output.md)系結參考。

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>發佈至 Azure

Visual Studio Code 可讓您將函式專案直接發行至 Azure。 在這過程中，您會在 Azure 訂用帳戶中建立函式應用程式和相關的資源。 函式應用程式會為函式提供執行內容。 專案會封裝並部署到您 Azure 訂用帳戶中的新函式應用程式。

當您從 Visual Studio Code 發行至 Azure 中的新函式應用程式時，您會同時提供快速函式應用程式建立路徑和先進路徑。 

當您從 Visual Studio Code 發佈時，會利用[Zip 部署](functions-deployment-technologies.md#zip-deploy)技術。 

### <a name="quick-function-app-create"></a>快速函數應用程式建立

當您選擇 [ **+ 在 Azure 中建立新的函式應用程式**...] 時，延伸模組會自動為您的函數應用程式所需的 Azure 資源產生值。 這些值是以您選擇的函數應用程式名稱為基礎。 如需使用預設值將您的專案發佈至 Azure 中新函式應用程式的範例，請參閱[Visual Studio Code 快速入門文章](functions-create-first-function-vs-code.md#publish-the-project-to-azure)。

如果您想要為所建立的資源提供明確的名稱，您必須選擇 [advanced create path]。

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>使用 advanced 選項將專案發佈至 Azure 中的新函式應用程式

下列步驟會將您的專案發佈至使用 advanced create 選項建立的新函數應用程式：

1. 在 [ **Azure：** 函式] 區域中，選取 [**部署至函數應用程式**] 圖示。

    ![函數應用程式設定](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 如果您尚未登入，系統會提示您登**入 Azure**。 您也可以**建立免費的 Azure 帳戶**。 從瀏覽器登入之後，請返回 Visual Studio Code。

1. 如果您有多個訂用帳戶，請選取函數應用程式的**訂用**帳戶，然後選取 [ **+ 在 Azure 中建立新的函數應用程式 ...]_Advanced_**。 這個_Advanced_選項可讓您更充分掌控您在 Azure 中建立的資源。 

1. 依照提示進行，提供下列資訊：

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----------- |
    | 選取 Azure 中的函數應用程式 | 在 Azure 中建立新的函數應用程式 | 在下一個提示中，輸入可識別新函數應用程式的全域唯一名稱，然後選取 Enter。 函式應用程式名稱的有效字元為 `a-z`、`0-9` 和 `-`。 |
    | 選取作業系統 | Windows | 函數應用程式會在 Windows 上執行。 |
    | 選取主控方案 | 取用方案 | 使用「無伺服器取用[方案」裝載](functions-scale.md#consumption-plan)。 |
    | 為新的應用程式選取執行時間 | 您的專案語言 | 執行時間必須符合您要發行的專案。 |
    | 為新資源選取資源群組 | 建立新的資源群組 | 在下一個提示中，輸入資源組名（例如`myResourceGroup`），然後選取 enter。 您也可以選取現有的資源群組。 |
    | 選取儲存體帳戶 | 建立新的儲存體帳戶 | 在下一個提示中，輸入函數應用程式所使用之新儲存體帳戶的全域唯一名稱，然後選取 Enter 鍵。 儲存體帳戶名稱的長度必須介於3到24個字元之間，而且只能包含數位和小寫字母。 您也可以選取現有的帳戶。 |
    | 選取新資源的位置 | region | 選取[區域](https://azure.microsoft.com/regions/)中您或接近您函數所存取之其他服務的位置。 |

    建立函數應用程式並套用部署套件之後，就會出現通知。 在通知中選取 [檢視輸出]  ，即可檢視建立和部署結果，包括您所建立的 Azure 資源。

## <a name="republish-project-files"></a>重新發佈專案檔案

當您設定[持續部署](functions-continuous-deployment.md)時，每當連線來源位置中的來源檔案更新時，就會更新 Azure 中的函數應用程式。 我們建議您持續部署，但您也可以從 Visual Studio Code 重新發佈專案檔更新。

> [!IMPORTANT]
> 發佈至現有的函式應用程式會覆寫該應用程式在 Azure 中的內容。

1. 在 Visual Studio Code 中，選取 F1 以開啟命令選擇區。 在命令選擇區中，搜尋並選取 [ **Azure Functions：部署至函式應用程式**]。

1. 如果您尚未登入，系統會提示您登**入 Azure**。 當您從瀏覽器登入之後，請返回 Visual Studio Code。 如果您有多個訂用帳戶，請選取包含您函數應用程式的**訂用**帳戶。

1. 在 Azure 中選取現有的函數應用程式。 當您收到有關覆寫函式應用程式中所有檔案的警告時，請選取 [**部署**] 以確認警告並繼續。

專案已重建、重新封裝並上傳至 Azure。 現有的專案會由新的封裝取代，而函式應用程式會重新開機。

## <a name="get-the-url-of-the-deployed-function"></a>取得已部署函式的 URL

若要呼叫 HTTP 觸發的函式，您需要函式部署至函數應用程式的 URL。 此 URL 包含任何必要的[功能鍵](functions-bindings-http-webhook-trigger.md#authorization-keys)。 您可以使用擴充功能來取得已部署函式的這些 Url。

1. 選取 F1 以開啟命令選擇區，然後搜尋並執行命令**Azure Functions： Copy FUNCTION URL**。

1. 遵循提示，在 Azure 中選取您的函式應用程式，然後按您想要叫用的特定 HTTP 觸發程式。

函數 URL 會連同`code`查詢參數所傳遞的任何必要索引鍵一起複製到剪貼簿。 使用 HTTP 工具來提交 POST 要求，或對遠端函式取得 GET 要求的瀏覽器。  

## <a name="run-functions-locally"></a>在本機執行函式

Azure Functions 擴充功能可讓您在本機開發電腦上執行函式專案。 本機執行時間是在 Azure 中裝載函數應用程式的相同執行時間。 本機設定會從[本機. 設定的 json](#local-settings-file)檔案中讀取。

### <a name="additional-requirements-for-running-a-project-locally"></a>在本機執行專案的其他需求

若要在本機執行函式專案，您必須符合下列其他需求：

* 安裝2.x 版或更新版本的[Azure Functions Core Tools](functions-run-local.md#v2)。 當您在本機啟動專案時，會自動下載並安裝 Core Tools 套件。 核心工具組含整個 Azure Functions 執行時間，因此下載和安裝可能需要一些時間。

* 安裝所選語言的特定需求：

    | Language | 需求 |
    | -------- | --------- |
    | **C#** | [C# 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [JAVA 擴充功能的偵錯工具](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[Maven 3 或更新版本](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>建議使用[Python 3.6.8](https://www.python.org/downloads/)|

    <sup>*</sup>Active LTS and 維護 LTS 版本（建議使用建議使用8.11.1 和10.14.1）。

### <a name="configure-the-project-to-run-locally"></a>將專案設定為在本機執行

函數執行時間會針對 HTTP 和 webhook 以外的所有觸發程式類型，在內部使用 Azure 儲存體帳戶。 因此，您必須將**AzureWebJobsStorage**索引鍵設定為有效的 Azure 儲存體帳戶連接字串。

本節使用適用于[Visual Studio Code 的 Azure 儲存體擴充](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)功能搭配[Azure 儲存體總管](https://storageexplorer.com/)來連接和抓取儲存體連接字串。

設定儲存體帳戶連接字串：

1. 在 Visual Studio 中，開啟**Cloud Explorer**，展開 [**儲存體帳戶** > ]**儲存體帳戶**，然後選取 [**屬性**]，並複製 [**主要連接字串**] 值。

2. 在您的專案中，開啟 local.settings.json 檔案並將 **AzureWebJobsStorage** 機碼的值設定為您所複製的連接字串。

3. 重複上一步，針對函數所需的其他任何連接，將唯一機碼新增至 [值]**** 陣列。

如需詳細資訊，請參閱[本機設定檔](#local-settings-file)。

### <a name="debugging-functions-locally"></a>本機的調試函式  

若要對函式進行偵錯工具，請選取 F5。 如果您尚未下載[核心工具][Azure Functions Core Tools]，系統會提示您這麼做。 當核心工具已安裝且正在執行時，輸出會顯示在終端機中。 這與從終端機執行`func host start` Core Tools 命令相同，但有額外的組建工作和附加的偵錯工具。  

當專案正在執行時，您可以觸發您的函式，就像將專案部署至 Azure 一樣。 當專案在「調試」模式下執行時，會如預期般在 Visual Studio Code 中叫用中斷點。

HTTP 觸發程式的要求 URL 會顯示在終端機的輸出中。 當專案在本機執行時，不會使用 HTTP 觸發程式的函式金鑰。 如需詳細資訊，請參閱[在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)。  

若要深入瞭解，請參閱使用[Azure Functions Core Tools][Azure Functions Core Tools]。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

根據預設，當專案發佈至 Azure 時，不會自動遷移這些設定。 發佈完成後，您可以選擇將設定從本機. json 發行至 Azure 中的函式應用程式。 若要深入瞭解，請參閱[發行應用程式設定](#publish-application-settings)。

**ConnectionStrings** 中的值永遠不會發佈。

函數應用程式設定值也可以在您的程式碼中讀取為環境變數。 如需詳細資訊，請參閱這些特定語言參考文章的環境變數章節：

* [先行編譯 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 指令碼 (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure 中的應用程式設定

您專案中的本機. settings. json 檔案中的設定應該與 Azure 中的函數應用程式中的應用程式設定相同。 您新增至本機的任何設定也必須新增至 Azure 中的函數應用程式。 當您發行專案時，不會自動上傳這些設定。 同樣地，您在[入口網站](functions-how-to-use-azure-function-app-settings.md#settings)中的函式應用程式中建立的任何設定，都必須下載至您的本機專案。

### <a name="publish-application-settings"></a>發行應用程式設定

將必要設定發佈至 Azure 中的函式應用程式最簡單的方式，就是使用發佈專案之後出現的 [**上傳設定**] 連結：

![上傳應用程式設定](./media/functions-develop-vs-code/upload-app-settings.png)

您也可以使用 [命令選擇區] 中的 [ **Azure Functions：上傳本機設定**] 命令來發行設定。 您可以使用 [ **Azure Functions：新增設定**] 命令，將個別設定新增至 Azure 中的應用程式設定。

> [!TIP]
> 發行之前，請務必先儲存您的本機. 設定 json 檔案。

如果本機檔案已加密，則會進行解密、發行和加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。

藉由展開您的訂用帳戶、函數應用程式和**應用程式設定**，在 [ **Azure：** 函式] 區域中查看現有的應用程式設定。

![Visual Studio Code 中的 View function 應用程式設定](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>從 Azure 下載設定

如果您已在 Azure 中建立應用程式設定，您可以使用 [ **Azure Functions：下載遠端設定**] 命令，將其下載到您的本機. 設定 json 檔案中。

如同上傳，如果本機檔案已加密，則會重新解密、更新及加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。

## <a name="monitoring-functions"></a>監視函式

當您在[本機執行](#run-functions-locally)函式時，會將記錄資料串流處理至終端機主控台。 當您的函式專案在 Azure 中的函式應用程式中執行時，您也可以取得記錄資料。 您可以連線到 Azure 中的串流記錄以查看近乎即時的記錄資料，或者您可以啟用 Application Insights，以更全面瞭解函數應用程式的行為。

### <a name="streaming-logs"></a>串流記錄

當您開發應用程式時，通常會以近乎即時的方式查看記錄資訊。 您可以查看您的函式所產生之記錄檔的資料流程。 此輸出是 HTTP 觸發函式之要求的串流記錄範例：

![HTTP 觸發程式的串流記錄輸出](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

若要深入瞭解，請參閱[串流記錄](functions-monitoring.md#streaming-logs)。

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 串流記錄僅支援函式主控制項的單一實例。 當您的函數調整為多個實例時，來自其他實例的資料不會顯示在記錄資料流程中。 Application Insights 中的[即時計量資料流](../azure-monitor/app/live-stream.md)支援多個實例。 串流分析也會以近乎即時的方式，以取樣的[資料](functions-monitoring.md#configure-sampling)為基礎。

### <a name="application-insights"></a>Application Insights

我們建議您將函數應用程式與 Application Insights 整合，以監視函式的執行。 當您在 Azure 入口網站中建立函數應用程式時，預設會進行這項整合。 當您在 Visual Studio 發佈期間建立函數應用程式時，您需要自行整合 Application Insights。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要深入了解，請參閱[監視 Azure Functions](functions-monitoring.md)。

## <a name="c-script-projects"></a>C\#腳本專案

根據預設，所有 c # 專案都會建立為[c # 編譯的類別庫專案](functions-dotnet-class-library.md)。 如果您想要改為使用 c # 腳本專案，您必須選取 [c # 腳本] 做為 Azure Functions 擴充功能設定中的預設語言：

1.  > 選取 **[** 檔案**喜好** > **設定**]。

1. 移至 [**使用者設定** > ] [**延伸** > 模組]**Azure Functions**。

1. 從**Azure Function： Project Language**選取**C # 腳本**。

完成這些步驟之後，對基礎核心工具所做的`--csx`呼叫會包含選項，其會產生併發行 c # 腳本（. .csx）專案檔案。 當您指定這個預設語言時，您建立的所有專案都會預設為 c # 腳本專案。 當設定預設值時，系統不會提示您選擇專案語言。 若要以其他語言建立專案，您必須變更此設定，或從使用者的設定 json 檔案中將其移除。 當您移除此設定之後，系統會在您建立專案時，再次提示您選擇您的語言。

## <a name="command-palette-reference"></a>命令選擇區參考

Azure Functions 延伸模組會在區域中提供有用的圖形化介面，以便與 Azure 中的函數應用程式互動。 相同的功能也可以在命令選擇區（F1）中做為命令。 這些 Azure Functions 命令可供使用：

|Azure Functions 命令  | 描述  |
|---------|---------|
|**加入新的設定**  |  在 Azure 中建立新的應用程式設定。 若要深入瞭解，請參閱[發行應用程式設定](#publish-application-settings)。 您可能也需要將[此設定下載到本機設定](#download-settings-from-azure)。 |
| **設定部署來源** | 將 Azure 中的函數應用程式連接至本機 Git 存放庫。 若要深入瞭解，請參閱[Azure Functions 的持續部署](functions-continuous-deployment.md)。 |
| **連接到 GitHub 存放庫** | 將您的函式應用程式連接至 GitHub 存放庫。 |
| **複製函式 URL** | 取得在 Azure 中執行之 HTTP 觸發函式的遠端 URL。 若要深入瞭解，請參閱[取得已部署函數的 URL](#get-the-url-of-the-deployed-function)。 |
| **在 Azure 中建立函數應用程式** | 在您的 Azure 訂用帳戶中建立新的函數應用程式。 若要深入瞭解，請參閱如何[在 Azure 中發佈至新的函式應用程式](#publish-to-azure)一節。        |
| **解密設定** | 解密已由**Azure Functions： Encrypt 設定**加密的[本機設定](#local-settings-file)。  |
| **刪除函數應用程式** | 從您在 Azure 中的訂用帳戶移除函式應用程式。 當 App Service 方案中沒有其他應用程式時，您也可以選擇刪除這項功能。 其他資源（例如儲存體帳戶和資源群組）則不會被刪除。 若要移除所有資源，您應該改為[刪除資源群組](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)。 您的本機專案不會受到影響。 |
|**Delete 函式**  | 從 Azure 中的函數應用程式移除現有的函式。 因為此刪除作業不會影響您的本機專案，所以請考慮在本機移除該函式，然後重新[發佈專案](#republish-project-files)。 |
| **刪除 Proxy** | 從 Azure 中的函數應用程式移除 Azure Functions proxy。 若要深入瞭解 proxy，請參閱使用[Azure Functions Proxy](functions-proxies.md)。 |
| **刪除設定** | 刪除 Azure 中的函數應用程式設定。 這項刪除作業不會影響您的本機. 設定 json 檔案中的設定。 |
| **中斷存放庫的連線**  | 移除 Azure 中的函數應用程式與原始檔控制存放庫之間的[持續部署](functions-continuous-deployment.md)連線。 |
| **下載遠端設定** | 從 Azure 中選擇的函式應用程式，將設定下載至您的本機. 設定 json 檔案。 如果本機檔案已加密，則會進行解密、更新及加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。 執行此命令之前，請務必先將變更儲存至您的本機設定 json 檔案。 |
| **編輯設定** | 變更 Azure 中現有函數應用程式設定的值。 此命令不會影響您的本機. 設定 json 檔案中的設定。  |
| **加密設定** | 在[本機設定](#local-settings-file)中加密`Values`陣列中的個別專案。 在這個檔案中`IsEncrypted` ，也會設定`true`為，這會指定本機執行時間將會在使用之前先解密設定。 將本機設定加密，以降低洩漏重要資訊的風險。 在 Azure 中，應用程式設定一律會以加密方式儲存。 |
| **立即執行函式** | 在 Azure 中手動啟動[計時器觸發](functions-bindings-timer.md)的函式。 此命令可用於測試。 若要深入瞭解如何在 Azure 中觸發非 HTTP 函式，請參閱[手動執行非 HTTP 觸發](functions-manually-run-non-http.md)的函式。 |
| **初始化要與 VS Code 搭配使用的專案** | 將必要的 Visual Studio Code 專案檔案加入至現有的函式專案。 使用此命令來處理您使用 Core Tools 建立的專案。 |
| **安裝或更新 Azure Functions Core Tools** | 安裝或更新[Azure Functions Core Tools]，用來在本機執行函式。 |
| **重新部署**  | 可讓您將已連線的 Git 存放庫中的專案檔重新部署到 Azure 中的特定部署。 若要從 Visual Studio Code 重新發佈本機更新，請重新[發佈您的專案](#republish-project-files)。 |
| **重新命名設定** | 變更 Azure 中現有函數應用程式設定的索引鍵名稱。 此命令不會影響您的本機. 設定 json 檔案中的設定。 在 Azure 中重新命名設定之後，您應該將[這些變更下載到本機專案](#download-settings-from-azure)。 |
| **重新啟動** | 在 Azure 中重新開機函數應用程式。 部署更新也會重新開機函數應用程式。 |
| **設定 AzureWebJobsStorage**| 設定`AzureWebJobsStorage`應用程式設定的值。 這是 Azure Functions 所需的設定。 它是在 Azure 中建立函式應用程式時所設定。 |
| **啟動** | 在 Azure 中啟動已停止的函式應用程式。 |
| **啟動串流記錄** | 在 Azure 中啟動函數應用程式的串流記錄。 如果您需要近乎即時地查看記錄資訊，請在 Azure 中的遠端疑難排解期間使用串流記錄。 若要深入瞭解，請參閱[串流記錄](#streaming-logs)。 |
| **停止** | 停止在 Azure 中執行的函式應用程式。 |
| **停止串流記錄** | 停止 Azure 中函數應用程式的串流記錄。 |
| **切換為位置設定** | 啟用時，可確保指定部署位置的應用程式設定會持續存在。 |
| **卸載 Azure Functions Core Tools** | 移除延伸模組所需的 Azure Functions Core Tools。 |
| **上傳本機設定** | 將設定從您的本機. 設定 json 檔案上傳至 Azure 中所選的函式應用程式。 如果本機檔案已加密，則會進行解密、上傳和加密。 如果兩個位置中的設定有衝突的值，系統會提示您選擇如何繼續。 執行此命令之前，請務必先將變更儲存至您的本機設定 json 檔案。 |
| **GitHub 中的 View Commit** | 當您的函式應用程式連線至存放庫時，會顯示特定部署中的最新認可。 |
| **查看部署記錄** | 針對 Azure 中的函數應用程式，顯示特定部署的記錄。 |

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Azure Functions Core Tools，請參閱使用[Azure Functions Core Tools](functions-run-local.md)。

若要深入了解如何將函式開發為 .NET 類別庫，請參閱 [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)。 本文也提供範例的連結，說明如何使用屬性來宣告 Azure Functions 所支援的各種系結類型。

[適用於 Visual Studio Code 的 Azure Functions 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md
