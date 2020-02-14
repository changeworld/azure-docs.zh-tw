---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205738"
---
## <a name="local-settings-file"></a>本機設定檔

本機的設定檔案會儲存應用程式設定、連接字串，以及本機開發工具所使用的設定。 只有當您在本機執行專案時，才會使用本機. settings. json 檔案中的設定。 本機設定檔案具有下列結構：

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "<language worker>",
    "AzureWebJobsStorage": "<connection-string>",
    "AzureWebJobsDashboard": "<connection-string>",
    "MyBindingConnection": "<binding-connection-string>"
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

當您在本機執行專案時，支援這些設定：

| 設定      | 描述                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 當此設定設為 [`true`] 時，所有的值都會以本機電腦金鑰加密。 需搭配 `func settings` 命令使用。 預設值為 `false`。 |
| **`Values`** | 當專案在本機執行時所使用的應用程式設定和連接字串的陣列。 這些索引鍵/值（字串字串）組會對應至 Azure 中函數應用程式中的應用程式設定，例如[`AzureWebJobsStorage`]。 許多觸發程式和系結都有參考連接字串應用程式設定的屬性，例如[Blob 儲存體觸發](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)程式的 `Connection`。 針對這些屬性，您需要 `Values` 陣列中定義的應用程式設定。 <br/>[`AzureWebJobsStorage`]是 HTTP 以外的觸發程式所需的應用程式設定。 <br/>2\.x 版和更新版本的函式執行時間需要 [`FUNCTIONS_WORKER_RUNTIME`] 設定，這是由核心工具針對您的專案所產生的。 <br/> 當您已在本機安裝[Azure 儲存體模擬器](../articles/storage/common/storage-use-emulator.md)，並將[`AzureWebJobsStorage`]設定為 `UseDevelopmentStorage=true`時，Core Tools 會使用模擬器。 模擬器在開發期間很有用，但您應該在部署之前使用實際的儲存體連接進行測試。<br/> 值必須是字串，而不是 JSON 物件或陣列。 設定名稱不能包含冒號（`:`）或雙底線（`__`）。 這些字元是由執行時間所保留。  |
| **`Host`** | 當您在本機執行專案時，此區段中的設定會自訂函式主機進程。 這些設定與主機. json 設定不同，這也適用于您在 Azure 中執行專案時。 |
| **`LocalHttpPort`** | 設定於執行本機 Functions 主機 (`func host start` 和 `func run`) 時所使用的預設連接埠。 `--port` 命令列選項的優先順序高於此設定。 |
| **`CORS`** | 定義針對[跨來源資源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 所允許的來源。 來源是以不含空格的逗號分隔清單提供。 支援萬用字元值 (\*)，它允許來自任何來源的要求。 |
| **`CORSCredentials`** |  當設定為 `true`時，會允許 `withCredentials` 的要求。 |
| **`ConnectionStrings`** | 集合。 請勿將此集合用於函數系結所使用的連接字串。 此集合僅供通常從設定檔的 `ConnectionStrings` 區段取得連接字串的架構使用，例如[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 此物件中的連接字串會新增至具有 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) 提供者類型的環境。 此集合中的專案不會與其他應用程式設定一起發行至 Azure。 您必須明確地將這些值新增至函數應用程式設定的 `Connection strings` 集合。 如果您要在您的函式程式碼中建立[`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) ，您應該在入口網站的 [**應用程式設定**] 中，將連接字串值與其他連接儲存在一起。 |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
