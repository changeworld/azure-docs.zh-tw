---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: d944d1d3e9c72471fab2435430a7d13e1770e807
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010442"
---
## <a name="local-settings-file"></a>本機設定檔

local.settings.json 檔案會儲存本機開發工具所使用的應用程式設定、連接字串和設定。 只有當您在本機執行專案時，才會使用 local.settings.json 檔案中的設定。 本機設定檔案具有以下結構：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>",
    "AzureWebJobs.HttpExample.Disabled": "true"
  },
  "Host": {
    "LocalHttpPort": 7071,
    "CORS": "*",
    "CORSCredentials": false
  },
  "ConnectionStrings": {
    "SQLConnectionString": "<sqlclient-connection-string>"
  }
}
```

當您在本機執行專案時，支援下列設定：

| 設定      | 描述                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 當此設定設為 `true` 時，所有的值都會以本機電腦金鑰加密。 需搭配 `func settings` 命令使用。 預設值為 `false`。 當本機電腦中包含秘密 (例如服務連接字串) 時，您可能想要對其進行加密。 主機會在執行時自動解密設定。 請先使用 `func settings decrypt` 命令，然後再嘗試讀取本機加密的設定。 |
| **`Values`** | 在本機執行專案時使用的應用程式設定與連接字串陣列。 這些索引鍵/值 (字串-字串) 組會對應至 Azure 中函式應用程式的應用程式設定，例如 [`AzureWebJobsStorage`]。 許多觸發程序和繫結都有參考連結字串應用程式設定的屬性，例如 [Blob 儲存體觸發程序](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)的 `Connection` 屬性。 對於這類屬性，您需要在 `Values` 陣列中定義的應用程式設定。 如需常用的設定清單，請參閱後續表格。 <br/>值必須是字串，而不是 JSON 物件或陣列。 設定名稱不能包含冒號 (`:`) 或雙底線 (`__`)。 執行階段會保留雙底線字元，並且會保留冒號以支援[相依性插入](../articles/azure-functions/functions-dotnet-dependency-injection.md#working-with-options-and-settings)。 |
| **`Host`** | 當您在本機執行專案時，本節中的設定會自訂 Functions 主機程序。 這些設定與 host.json 設定不同，也適用於您在 Azure 中執行專案時。 |
| **`LocalHttpPort`** | 設定於執行本機 Functions 主機 (`func host start` 和 `func run`) 時所使用的預設連接埠。 `--port` 命令列選項的優先順序高於此設定。 |
| **`CORS`** | 定義針對[跨來源資源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 所允許的來源。 來源是以不含空格的逗號分隔清單提供。 支援萬用字元值 (\*)，它允許來自任何來源的要求。 |
| **`CORSCredentials`** |  若設定為 `true`，則允許 `withCredentials` 要求。 |
| **`ConnectionStrings`** | 集合。 請勿將此集合用於您函式繫結所使用的連接字串。 此集合僅供架構使用，其通常會從組態檔的 `ConnectionStrings` 區段取得連接字串，例如 [Entity Framework](/ef/ef6/)。 此物件中的連接字串會新增至具有 [System.Data.SqlClient](/dotnet/api/system.data.sqlclient) 提供者類型的環境。 此集合中的項目不會發佈至具備其他應用程式設定的 Azure。 您必須明確地將這些值新增到函式應用程式設定的 `Connection strings` 集合。 如果要在函式程式碼中建立 [`SqlConnection`](/dotnet/api/system.data.sqlclient.sqlconnection)，則應該將連接字串值連同您的其他連線一起儲存在入口網站的 [應用程式設定] 中。 |

在本機執行時，下列應用程式設定可以包含在 **`Values`** 陣列中：

| 設定 | 值 | 描述 |
|-----|-----|-----|
|**`AzureWebJobsStorage`**| 儲存體帳戶連接字串，或<br/>`UseDevelopmentStorage=true`| 包含 Azure 儲存體帳戶的連接字串。 使用 HTTP 以外的觸發程式時為必要項。 如需詳細資訊，請參閱 [`AzureWebJobsStorage`] 參考。<br/>當您在本機安裝 [Azure 儲存體模擬器](../articles/storage/common/storage-use-emulator.md)並將 [`AzureWebJobsStorage`] 設定為 `UseDevelopmentStorage=true` 時，Core Tools 會使用模擬器。 此模擬器在開發期間非常實用，但您應在部署前先透過實際的儲存體連接進行測試。| 
|**`AzureWebJobs.<FUNCTION_NAME>.Disabled`**| `true`\|`false` | 若要在本機執行時停用函式，請將 `"AzureWebJobs.<FUNCTION_NAME>.Disabled": "true"` 新增至集合，其中 `<FUNCTION_NAME>` 是函式的名稱。 若要深入了解，請參閱[如何停用 Azure Functions 中的函式](../articles/azure-functions/disable-function.md#localsettingsjson) |
|**`FUNCTIONS_WORKER_RUNTIME`** | `dotnet`<br/>`node`<br/>`java`<br/>`powershell`<br/>`python`| 表示 Functions 執行階段的目標語言。 在 2.x 版和更高版本的 Functions 執行階段中為必要項。 這項設定是由核心工具會為您的專案所產生。 若要深入了解，請參閱 [`FUNCTIONS_WORKER_RUNTIME`](../articles/azure-functions/functions-app-settings.md#functions_worker_runtime) 參考。|
| **`FUNCTIONS_WORKER_RUNTIME_VERSION`** | `~7` |表示在本機執行時，使用 PowerShell 7。 如果未設定，則會使用 PowerShell Core 6。 只有在本機執行時，才會使用此設定。 在 Azure 中執行時，PowerShell 執行階段版本是由 `powerShellVersion` 網站組態設定所決定，其可[在入口網站中進行設定](../articles/azure-functions/functions-reference-powershell.md#changing-the-powershell-version)。 | 

[`AzureWebJobsStorage`]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage