---
title: 使用視覺化工作室代碼開發 Azure 函數
description: 瞭解如何使用視覺化工作室代碼的 Azure 函數擴展來開發和測試 Azure 函數。
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 2d33b7dddf29d37d174bdb7734e9048bc1658840
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277163"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>使用視覺化工作室代碼開發 Azure 函數

[視覺化工作室代碼的 Azure 函數擴展]允許您在本地開發函數並將其部署到 Azure。 如果這是您第一次體驗 Azure Functions，可至 [Azure Functions 簡介](functions-overview.md)深入了解。

Azure 函數擴展提供以下好處：

* 在本機開發電腦上編輯、建置及執行函數。
* 直接將 Azure Functions 專案發佈至 Azure。
* 使用各種語言編寫函數，同時利用 Visual Studio Code 的優勢。

擴展可以與以下語言一起使用，Azure 函數運行時支援從版本 2.x 開始：

* [C# 編譯](functions-dotnet-class-library.md)
* [C# 腳本](functions-reference-csharp.md)<sup>*</sup>
* [JAVAscript](functions-reference-node.md)
* [JAVA](functions-reference-java.md)
* [電源外殼](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>要求您將[C# 腳本設置為預設專案語言](#c-script-projects)。

在本文中，示例當前僅適用于 JavaScript （Node.js） 和 C# 類庫函數。  

本文詳細介紹了如何使用 Azure 函數擴展來開發函數並將其發佈到 Azure。 在閱讀本文之前，應使用[Visual Studio 代碼創建第一個函數](functions-create-first-function-vs-code.md)。

> [!IMPORTANT]
> 不要將單個函數應用的本地開發和門戶開發混為一談。 當您從本機專案發佈至函式應用程式時，部署程序將會覆寫您在入口網站開發的任何函式。

## <a name="prerequisites"></a>Prerequisites

在安裝和運行 Visual Studio 代碼的[Azure 函數擴展][Azure 函數擴展]之前，必須滿足以下要求：

* 安裝在其中一個[受支援平臺上](https://code.visualstudio.com/docs/supporting/requirements#_platforms)的[視覺化工作室代碼](https://code.visualstudio.com/)。

* 有效的 Azure 訂用帳戶。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[使用 Visual Studio 代碼發佈](#publish-to-azure)時，將在訂閱中創建所需的其他資源（如 Azure 存儲帳戶）。

> [!IMPORTANT]
> 您可以在本地開發函數並將其發佈到 Azure，而無需在本地啟動和運行它們。 要在本地運行函數，您需要滿足一些其他要求，包括自動下載 Azure 函數核心工具。 要瞭解更多資訊，請參閱[在本地運行專案的其他要求](#additional-requirements-for-running-a-project-locally)。

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>建立 Azure Functions 專案

函數擴展允許您創建函數應用專案以及第一個函數。 以下步驟演示如何在新函數專案中創建 HTTP 觸發函數。 [HTTP 觸發器](functions-bindings-http-webhook.md)是最簡單的函數觸發器範本。

1. 從**Azure：函數**，選擇 **"創建函數**"圖示：

    ![建立函式](./media/functions-develop-vs-code/create-function.png)

1. 選擇函數應用專案的資料夾，然後**為函數專案選擇語言**。

1. 如果尚未安裝核心工具，系統將要求您選擇要安裝的核心工具**的版本**。 選擇版本 2.x 或更高版本。 

1. 選擇**HTTP 觸發器**函數範本，或者選擇 **"立即跳過"** 以創建沒有函數的專案。 您以後始終可以[向專案添加函數](#add-a-function-to-your-project)。

    ![選擇 HTTP 觸發程序範本](./media/functions-develop-vs-code/create-function-choose-template.png)

1. 鍵入函數名稱的**HttpExample，** 然後選擇"輸入"，然後選擇 **"函數**授權"。 此授權級別要求您在調用函數終結點時提供[函數金鑰](functions-bindings-http-webhook-trigger.md#authorization-keys)。

    ![選擇函數授權](./media/functions-develop-vs-code/create-function-auth.png)

    函數以您選擇的語言和 HTTP 觸發函數的範本創建。

    ![視覺化工作室代碼中的 HTTP 觸發功能範本](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>生成的專案檔案

專案範本使用您選擇的語言創建專案，並安裝所需的依賴項。 對於任何語言，新專案都有以下檔：

* **host.json**：讓您設定 Functions 主機。 當您在本地運行函數並在 Azure 中運行函數時，這些設置將應用。 如需詳細資訊，請參閱 [host.json 參考](functions-host-json.md)。

* **本地.settings.json**：維護在本地運行函數時使用的設置。 這些設置僅在您本地運行函數時使用。 有關詳細資訊，請參閱[本地設置檔](#local-settings-file)。

    >[!IMPORTANT]
    >由於 local.settings.json 檔可以包含機密，因此您需要將其從專案原始程式碼管理中排除。

根據您的語言，將創建以下其他檔：

# <a name="c"></a>[C\#](#tab/csharp)

* HttpExample.cs實現函數的[類庫檔](functions-dotnet-class-library.md#functions-class-library-project)。

# <a name="javascript"></a>[JAVAscript](#tab/nodejs)

* 根資料夾中的包.json 檔。

* 包含[函數.json 定義檔和](functions-reference-node.md#folder-structure)[索引.js 檔的](functions-reference-node.md#exporting-a-function)HttpExample 資料夾，包含函數代碼的 Node.js 檔。

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

此時，您可以通過[修改函數.json 檔](#add-a-function-to-your-project)或向[C# 類庫函數添加參數，](#add-a-function-to-your-project)向函數添加輸入和輸出綁定。

您還可以[向專案添加新函數](#add-a-function-to-your-project)。

## <a name="install-binding-extensions"></a>安裝繫結延伸模組

除 HTTP 和計時器觸發器外，綁定在擴展包中實現。 您必須為需要它們的觸發器和綁定安裝擴展包。 安裝綁定擴展的過程取決於專案的語言。

# <a name="c"></a>[C\#](#tab/csharp)

在終端視窗中運行[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)命令，以安裝專案中所需的擴展包。 以下命令安裝 Azure 存儲擴展，該擴展為 Blob、佇列和表存儲實現綁定。

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JAVAscript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>將函式新增至您的專案

可以使用預定義的函數觸發器範本之一向現有專案添加新函數。 要添加新函數觸發器，請選擇 F1 打開命令調色板，然後搜索並運行**命令 Azure 函數：創建函數**。 按照提示選擇觸發器類型並定義觸發器所需的屬性。 如果您的觸發器需要訪問金鑰或連接字串才能連接到服務，請先在創建函數觸發器之前做好準備。

此操作的結果取決於專案的語言：

# <a name="c"></a>[C\#](#tab/csharp)

新的 C# 類庫 （.cs） 檔將添加到專案中。

# <a name="javascript"></a>[JAVAscript](#tab/nodejs)

在專案中創建新資料夾。 該資料夾包含一個新的函數.json 檔和新的 JavaScript 代碼檔。

---

## <a name="add-input-and-output-bindings"></a>添加輸入和輸出綁定

您可以通過添加輸入和輸出綁定來擴展函數。 添加綁定的過程取決於專案的語言。 要瞭解有關綁定的更多內容，請參閱[Azure 函數觸發器和綁定概念](functions-triggers-bindings.md)。

以下示例連接到名為 的`outqueue`存儲佇列，其中存儲帳戶的連接字串在 local.set.json`MyStorageConnection`中的應用程式設定中設置。

# <a name="c"></a>[C\#](#tab/csharp)

更新函數方法以將以下參數添加到`Run`方法定義：

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

此代碼要求您添加以下`using`語句：

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

`msg` 參數是 `ICollector<T>` 類型，其代表會在函式完成時寫入輸出繫結的訊息集合。 向集合添加一個或多個消息。 當函數完成時，這些消息將發送到佇列。

要瞭解更多資訊，請參閱[佇列存儲輸出綁定](functions-bindings-storage-queue-output.md)文檔。

# <a name="javascript"></a>[JAVAscript](#tab/nodejs)

Visual Studio Code 允許您按照一組方便的提示向函數.json 檔添加綁定。 要創建綁定，請按右鍵 （Ctrl+按一下 macOS） 函數 **.json**檔，並在函數資料夾中選擇 **"添加綁定**" ：

![向現有 JavaScript 函數添加綁定 ](media/functions-develop-vs-code/function-add-binding.png)

以下是定義新存儲輸出綁定的示例提示：

| Prompt | 值 | 描述 |
| -------- | ----- | ----------- |
| **選取繫結方向** | `out` | 此繫結為輸出繫結。 |
| **選擇帶方向的綁定** | `Azure Queue Storage` | 此繫結是 Azure 儲存體佇列繫結。 |
| **用來在程式碼中識別此繫結的名稱** | `msg` | 識別您的程式碼中參考之繫結參數的名稱。 |
| **要接收訊息的佇列** | `outqueue` | 作為繫結寫入目標的佇列名稱。 當 *queueName* 不存在，繫結會在第一次使用時加以建立。 |
| **從"本地設置.json"中選擇設置** | `MyStorageConnection` | 包含存儲帳戶的連接字串的應用程式設定的名稱。 該`AzureWebJobsStorage`設置包含使用函數應用創建的存儲帳戶的連接字串。 |

在此示例中，以下綁定將添加到函數.json`bindings`檔中的陣列中：

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

您還可以將相同的綁定定義直接添加到函數.

在函數代碼中，`msg`從`context`訪問綁定，如以下示例所示：

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

要瞭解更多資訊，請參閱[佇列存儲輸出綁定](functions-bindings-storage-queue-output.md)引用。

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>發佈至 Azure

視覺化工作室代碼允許您將函數專案直接發佈到 Azure。 在這過程中，您會在 Azure 訂用帳戶中建立函式應用程式和相關的資源。 函式應用程式會為函式提供執行內容。 專案會封裝並部署到您 Azure 訂用帳戶中的新函式應用程式。

當您從 Visual Studio 代碼發佈到 Azure 中的新功能應用時，將為您提供快速函數應用創建路徑和高級路徑。 

當您從 Visual Studio 代碼發佈時，您利用了[Zip 部署](functions-deployment-technologies.md#zip-deploy)技術。 

### <a name="quick-function-app-create"></a>快速功能應用創建

選擇 **= 在 Azure 中創建新函數應用時，** 擴展將自動為函數應用所需的 Azure 資源生成值。 這些值基於您選擇的函數應用名稱。 有關使用預設值將專案發佈到 Azure 中的新函數應用的示例，請參閱 Visual Studio[代碼快速入門文章](functions-create-first-function-vs-code.md#publish-the-project-to-azure)。

如果要為創建的資源提供顯式名稱，則必須選擇高級創建路徑。

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>使用高級選項將專案發佈到 Azure 中的新功能應用

以下步驟將專案發佈到使用高級創建選項創建的新函數應用：

1. 在**Azure：功能**區域中，選擇 **"部署到功能應用"** 圖示。

    ![函數應用程式設定](./media/functions-develop-vs-code/function-app-publish-project.png)

1. 如果未登錄，系統將提示您**登錄到 Azure**。 您還可以**創建一個免費的 Azure 帳戶**。 從瀏覽器登錄後，返回 Visual Studio 代碼。

1. 如果有多個訂閱，請選擇函數應用的**訂閱**，然後在 Azure 中選擇 **" 創建新功能應用..."_高級_**。 此_高級_選項使您能夠對在 Azure 中創建的資源進行更多控制。 

1. 按照提示，提供以下資訊：

    | Prompt | 值 | 描述 |
    | ------ | ----- | ----------- |
    | 在 Azure 中選擇函數應用 | 在 Azure 中創建新功能應用 | 在下一個提示符中，鍵入標識新功能應用的全域唯一名稱，然後選擇 Enter。 函式應用程式名稱的有效字元為 `a-z`、`0-9` 和 `-`。 |
    | 選擇作業系統 | Windows | 函數應用在 Windows 上運行。 |
    | 選擇託管計畫 | 取用方案 | 使用無伺服器[消耗計畫託管](functions-scale.md#consumption-plan)。 |
    | 為新應用選擇運行時 | 您的專案語言 | 運行時必須與您要發佈的專案匹配。 |
    | 為新資源選擇資源組 | 創建新資源組 | 在下一個提示符中，鍵入資源組名稱，`myResourceGroup`如 ， 然後選擇 Enter。 您還可以選擇現有資源組。 |
    | 選擇存儲帳戶 | 建立新的儲存體帳戶 | 在下一個提示符中，為函數應用使用的新存儲帳戶鍵入全域唯一名稱，然後選擇 Enter。 存儲帳戶名稱必須長在 3 到 24 個字元之間，並且只能包含數位和小寫字母。 您還可以選擇現有帳戶。 |
    | 選取新資源的位置 | region | 選擇您附近[區域](https://azure.microsoft.com/regions/)或函數訪問的其他服務附近的位置。 |

    創建函數應用並應用部署包後，將出現通知。 在通知中選取 [檢視輸出]****，即可檢視建立和部署結果，包括您所建立的 Azure 資源。

## <a name="republish-project-files"></a>重新發佈專案檔案

設置[連續部署](functions-continuous-deployment.md)時，每當在連接的源位置更新原始檔案時，Azure 中的函數應用都會更新。 我們建議持續部署，但您也可以從 Visual Studio Code 重新發佈專案檔案更新。

> [!IMPORTANT]
> 發佈至現有的函式應用程式會覆寫該應用程式在 Azure 中的內容。

1. 在視覺化工作室代碼中，選擇 F1 以打開命令調色板。 在命令調色板中，搜索並選擇**Azure 函數：部署到函數應用**。

1. 如果未登錄，系統將提示您**登錄到 Azure**。 從瀏覽器登錄後，請返回視覺化工作室代碼。 如果您有多個訂閱，請選擇包含函數應用的**訂閱**。

1. 在 Azure 中選擇現有功能應用。 當您收到有關覆蓋函數應用中所有檔的警告時，選擇 **"部署"** 以確認警告並繼續。

專案將重新生成、重新封包並上載到 Azure。 現有專案由新包替換，功能應用重新開機。

## <a name="get-the-url-of-the-deployed-function"></a>獲取已部署函數的 URL

要調用 HTTP 觸發的函數，您需要在函數部署到函數應用時具有 URL。 此 URL 包括任何必需[的函數鍵](functions-bindings-http-webhook-trigger.md#authorization-keys)。 可以使用擴展獲取已部署函數的這些 URL。

1. 選擇 F1 打開命令調色板，然後搜索並運行命令 Azure**函數：複製函數 URL**。

1. 按照提示在 Azure 中選擇函數應用，然後選擇要調用的特定 HTTP 觸發器。

函數 URL 以及`code`查詢參數傳遞的任何必需鍵將複製到剪貼簿。 使用 HTTP 工具提交 POST 請求，或瀏覽器向遠端函數提交 GET 請求。  

## <a name="run-functions-locally"></a>在本機執行函式

Azure 函數擴展允許您在本地開發電腦上運行函數專案。 本地運行時與在 Azure 中承載函數應用的運行時相同。 本地設置從[本地.settings.json 檔](#local-settings-file)讀取。

### <a name="additional-requirements-for-running-a-project-locally"></a>在本地運行專案的其他要求

要在本地運行函數專案，必須滿足以下附加要求：

* 安裝[Azure 函數核心工具](functions-run-local.md#v2)的版本 2.x 或更高版本。 在本地啟動專案時，將自動下載並安裝核心工具組。 核心工具組括整個 Azure 函數運行時，因此下載和安裝可能需要一些時間。

* 安裝所選語言的特定需求：

    | 語言 | 需求 |
    | -------- | --------- |
    | **C#** | [C# 擴充功能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[.NET Core CLI 工具](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x)   |
    | **JAVA** | [JAVA 擴展的調試器](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](https://aka.ms/azure-jdks)<br/>[馬文 3 或更高版本](https://maven.apache.org/) |
    | **JAVAscript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Python 延伸模組](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[推薦 Python 3.6.8](https://www.python.org/downloads/)|

    <sup>*</sup>活動 LTS 和維護 LTS 版本（建議 8.11.1 和 10.14.1）。

### <a name="configure-the-project-to-run-locally"></a>將專案配置為在本地運行

函數運行時在內部使用 Azure 存儲帳戶，用於 HTTP 和 Webhook 以外的所有觸發器類型。 因此，您需要將**值.AzureWebJobs 存儲**金鑰設置為有效的 Azure 存儲帳戶連接字串。

本節使用具有[Azure 存儲資源管理器](https://storageexplorer.com/)[的視覺化工作室代碼的 Azure 存儲副檔名](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)連接到並檢索存儲連接字串。

設定儲存體帳戶連接字串：

1. 在視覺化工作室中，打開**雲資源管理器**，展開**存儲帳戶** > **，** 然後選擇 **"屬性"** 並複製**主連接字串**值。

2. 在您的專案中，開啟 local.settings.json 檔案並將 **AzureWebJobsStorage** 機碼的值設定為您所複製的連接字串。

3. 重複上一步，針對函數所需的其他任何連接，將唯一機碼新增至 [值]**** 陣列。

有關詳細資訊，請參閱[本地設置檔](#local-settings-file)。

### <a name="debugging-functions-locally"></a>本地調試功能  

要調試函數，請選擇 F5。 如果您尚未下載[核心工具][Azure 函數核心工具]，系統將提示您這樣做。 安裝並運行核心工具時，輸出將顯示在終端中。 這與從終端運行`func host start`核心工具命令相同，但具有其他生成任務和附加調試器。  

專案運行時，可以像將專案部署到 Azure 時一樣觸發函數。 當專案在偵錯模式下運行時，中中斷點將按預期在 Visual Studio 代碼中命中。

HTTP 觸發器的請求 URL 顯示在終端中的輸出中。 當專案在本地運行時，不使用 HTTP 觸發器的功能鍵。 如需詳細資訊，請參閱[在 Azure Functions 中測試程式碼的策略](functions-test-a-function.md)。  

要瞭解更多資訊，請參閱[使用 Azure 函數核心工具][Azure 函數核心工具]。

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

預設情況下，在將專案發佈到 Azure 時，不會自動遷移這些設置。 發佈完成後，您可以選擇將設置從本地.settings.json 發佈到 Azure 中的函數應用。 要瞭解更多資訊，請參閱[發佈應用程式設定](#publish-application-settings)。

**ConnectionStrings** 中的值永遠不會發佈。

函數應用程式設定值也可以在代碼中讀取為環境變數。 有關詳細資訊，請參閱這些特定于語言的參考文章的環境變數部分：

* [先行編譯 C#](functions-dotnet-class-library.md#environment-variables)
* [C# 指令碼 (.csx)](functions-reference-csharp.md#environment-variables)
* [JAVA](functions-reference-java.md#environment-variables)
* [JAVAscript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Azure 中的應用程式設定

專案中的 local.settings.json 檔中的設置應與 Azure 中的函數應用中的應用程式設定相同。 添加到本地.settings.json 的任何設置也必須添加到 Azure 中的函數應用。 發佈專案時，這些設置不會自動上載。 同樣，必須在[門戶中的](functions-how-to-use-azure-function-app-settings.md#settings)函數應用中創建的任何設置都必須下載到本地專案。

### <a name="publish-application-settings"></a>發佈應用程式設定

將所需設置發佈到 Azure 中的函數應用的最簡單方法是使用發佈專案後顯示的 **"上載設置"** 連結：

![上傳應用程式設定](./media/functions-develop-vs-code/upload-app-settings.png)

您還可以使用**Azure 函數：在命令調色板中上載本地設置**命令來發佈設置。 可以使用**Azure 函數：添加新設置**命令，將單個設置添加到 Azure 中的應用程式設定。

> [!TIP]
> 在發佈本地.settings.json 檔之前，請確保保存該檔。

如果本地檔已加密，則重新解密、發佈和加密。 如果兩個位置中存在具有衝突值的設置，系統將提示您選擇如何繼續。

通過擴展訂閱、函數應用和**應用程式設定**，查看**Azure：功能**區域中的現有應用設置。

![在視覺化工作室代碼中查看功能應用設置](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>從 Azure 下載設置

如果在 Azure 中創建了應用程式設定，則可以使用**Azure 函數：下載遠端設置**命令將它們下載到本地.settings.json 檔中。

與上傳一樣，如果本地檔已加密，則再次解密、更新和加密。 如果兩個位置中存在具有衝突值的設置，系統將提示您選擇如何繼續。

## <a name="monitoring-functions"></a>監視函式

在[本地運行函數](#run-functions-locally)時，日誌資料將資料流到終端主控台。 您還可以在 Azure 中的函數應用中運行函數專案時獲取日誌資料。 您可以連接到 Azure 中的流式處理日誌以查看近即時日誌資料，也可以啟用應用程式見解以更全面地瞭解函數應用的行為方式。

### <a name="streaming-logs"></a>串流記錄

開發應用程式時，在接近即時的情況下查看日誌記錄資訊通常很有用。 您可以查看函數生成的日誌檔流。 此輸出是向 HTTP 觸發函數的請求的流式處理日誌的示例：

![用於 HTTP 觸發器的流式處理日誌輸出](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

要瞭解更多資訊，請參閱[流式處理日誌](functions-monitoring.md#streaming-logs)。

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> 流式處理日誌僅支援函數主機的單個實例。 將函數縮放到多個實例時，日誌流中不會顯示來自其他實例的資料。 應用程式見解中的[即時指標流](../azure-monitor/app/live-stream.md)確實支援多個實例。 雖然也是在接近即時，流分析是基於[採樣資料](functions-monitoring.md#configure-sampling)。

### <a name="application-insights"></a>Application Insights

我們建議您通過將函數應用與應用程式見解集成來監視函數的執行。 在 Azure 門戶中創建函數應用時，預設情況下會發生此集成。 在視覺化工作室發佈期間創建功能應用時，需要自己集成應用程式見解。

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

若要深入了解，請參閱[監視 Azure Functions](functions-monitoring.md)。

## <a name="c-script-projects"></a>C\#腳本專案

預設情況下，所有 C# 專案都創建為[C# 編譯的類庫專案](functions-dotnet-class-library.md)。 如果希望改用 C# 腳本專案，則必須在 Azure 函數擴展設置中選擇 C# 腳本作為預設語言：

1. 選擇 **"檔** > **首選項** > **設置**"。

1. 轉到**使用者設置** > **擴展** > **Azure 函數**。

1. 從 Azure 函數中選擇**C#腳本****：專案語言**。

完成這些步驟後，對基礎核心工具進行的調用包括一`--csx`個選項，該選項生成併發布 C# 腳本 （.csx） 專案檔案。 指定此預設語言後，您創建的所有專案都預設為 C# 腳本專案。 設置預設值時，系統不會提示您選擇專案語言。 要以其他語言創建專案，必須更改此設置或將其從使用者設置.json 檔中刪除。 刪除此設置後，將再次提示您在創建專案時選擇語言。

## <a name="command-palette-reference"></a>命令調色板參考

Azure 函數擴展在區域中提供了一個有用的圖形介面，用於與 Azure 中的函數應用進行交互。 與命令調色板 （F1） 中的命令一樣，功能也可用。 這些 Azure 函數命令可用：

|Azure 函數命令  | 描述  |
|---------|---------|
|**添加新設置**  |  在 Azure 中創建新的應用程式設定。 要瞭解更多資訊，請參閱[發佈應用程式設定](#publish-application-settings)。 您可能還需要[將此設置下載到本地設置](#download-settings-from-azure)。 |
| **配置部署源** | 將 Azure 中的函數應用連接到本地 Git 存儲庫。 要瞭解詳細資訊，請參閱[Azure 函數的連續部署](functions-continuous-deployment.md)。 |
| **連接到 GitHub 存儲庫** | 將函數應用連接到 GitHub 存儲庫。 |
| **複製功能 URL** | 獲取在 Azure 中運行的 HTTP 觸發函數的遠端 URL。 要瞭解更多資訊，請參閱[獲取已部署函數的 URL。](#get-the-url-of-the-deployed-function) |
| **在 Azure 中創建函數應用** | 在 Azure 中的訂閱中創建新的函數應用。 要瞭解詳細資訊，請參閱有關如何發佈到 Azure[中的新功能應用](#publish-to-azure)的部分。        |
| **解密設置** | 解密已由 Azure 函數加密的[本地設置](#local-settings-file)**：加密設定**。  |
| **刪除函數應用** | 從 Azure 中的訂閱中刪除函數應用。 當應用服務方案中沒有其他應用時，您也可以選擇刪除它。 其他資源（如存儲帳戶和資源組）不會被刪除。 要刪除所有資源，應改為[刪除資源組](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources)。 您的本地專案不受影響。 |
|**刪除功能**  | 從 Azure 中的函數應用中刪除現有函數。 由於此刪除不會影響本地專案，因此請考慮在本地刪除該函數，然後[重新發佈專案](#republish-project-files)。 |
| **刪除代理** | 從 Azure 中的函數應用中刪除 Azure 函數代理。 要瞭解有關代理的更多詳細資訊，請參閱使用[Azure 函數代理](functions-proxies.md)。 |
| **刪除設置** | 刪除 Azure 中的函數應用設置。 此刪除不會影響本地.settings.json 檔中的設置。 |
| **從回購斷開連接**  | 刪除 Azure 中的函數應用和原始程式碼管理存儲庫之間的[連續部署](functions-continuous-deployment.md)連接。 |
| **下載遠端設置** | 將設置從 Azure 中選定的函數應用下載到本地.settings.json 檔中。 如果本地檔已加密，則將再次解密、更新和加密。 如果兩個位置中存在具有衝突值的設置，系統將提示您選擇如何繼續。 在運行此命令之前，請確保將更改保存到本地.settings.json 檔。 |
| **編輯設置** | 更改 Azure 中現有函數應用設置的值。 此命令不會影響本地.settings.json 檔中的設置。  |
| **加密設定** | 加密本地設置中陣列中的`Values`單個項。 [local settings](#local-settings-file) 在此檔中，`IsEncrypted`也設置為`true`，它指定本地運行時將在使用設置之前解密設置。 加密本地設置，以降低洩露重要資訊的風險。 在 Azure 中，應用程式設定始終以加密方式存儲。 |
| **立即執行功能** | 在 Azure 中手動啟動[計時器觸發的函數](functions-bindings-timer.md)。 此命令用於測試。 要瞭解有關在 Azure 中觸發非 HTTP 函數的更多資訊，請參閱[手動運行非 HTTP 觸發函數](functions-manually-run-non-http.md)。 |
| **初始化專案，以便與 VS 代碼一起使用** | 將所需的視覺化工作室代碼專案檔案添加到現有函數專案中。 使用此命令可以處理使用核心工具創建的專案。 |
| **安裝或更新 Azure 函數核心工具** | 安裝或更新[Azure 函數核心工具]，用於在本地運行函數。 |
| **重新部署**  | 允許您將專案檔案從連接的 Git 存儲庫重新部署到 Azure 中的特定部署。 要從視覺化工作室代碼重新發佈本地更新，[請重新發佈專案](#republish-project-files)。 |
| **重命名設置** | 更改 Azure 中現有函數應用設置的鍵名稱。 此命令不會影響本地.settings.json 檔中的設置。 在 Azure 中重命名設置後，應[將這些更改下載到本地專案](#download-settings-from-azure)。 |
| **重新啟動** | 重新開機 Azure 中的函數應用。 部署更新也會重新開機功能應用。 |
| **設置 Azure Web 作業存儲**| 設置`AzureWebJobsStorage`應用程式設定的值。 Azure 函數需要此設置。 它在 Azure 中創建函數應用時設置。 |
| **開始** | 在 Azure 中啟動已停止的函數應用。 |
| **啟動流日誌** | 在 Azure 中啟動函數應用的流式處理日誌。 如果需要近乎即時地查看日誌記錄資訊，則需要在 Azure 中的遠端故障排除期間使用流式處理日誌。 要瞭解更多資訊，請參閱[流式處理日誌](#streaming-logs)。 |
| **停止** | 停止在 Azure 中運行的函數應用。 |
| **停止流日誌** | 停止 Azure 中函數應用的流式處理日誌。 |
| **切換為插槽設置** | 啟用後，確保給定部署槽的應用程式設定保持不變。 |
| **卸載 Azure 函數核心工具** | 刪除擴展所需的 Azure 函數核心工具。 |
| **上傳本地設置** | 將設置從本地.settings.json 檔上載到 Azure 中所選的函數應用。 如果本地檔已加密，則再次解密、上載和加密。 如果兩個位置中存在具有衝突值的設置，系統將提示您選擇如何繼續。 在運行此命令之前，請確保將更改保存到本地.settings.json 檔。 |
| **在 GitHub 中查看提交** | 在函數應用連接到存儲庫時，顯示特定部署中的最新提交。 |
| **查看部署日誌** | 在 Azure 中顯示特定部署到函數應用的日誌。 |

## <a name="next-steps"></a>後續步驟

要瞭解有關 Azure 函數核心工具的更多詳細資訊，請參閱[使用 Azure 函數核心工具](functions-run-local.md)。

若要深入了解如何將函式開發為 .NET 類別庫，請參閱 [Azure Functions C# 開發人員參考](functions-dotnet-class-library.md)。 本文還提供了如何使用屬性聲明 Azure 函數支援的各種類型的綁定的示例。

[適用於 Visual Studio Code 的 Azure Functions 擴充功能]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure 函數核心工具]: functions-run-local.md
