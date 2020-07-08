---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77205738"
---
## <a name="local-settings-file"></a>本機設定檔

檔案上的 local.settings.js會儲存應用程式設定、連接字串，以及本機開發工具所使用的設定。 只有當您在本機執行專案時，才會使用檔案上 local.settings.js中的設定。 本機設定檔案具有下列結構：

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
| **`IsEncrypted`** | 當此設定設為時 `true` ，所有的值都會以本機電腦金鑰加密。 需搭配 `func settings` 命令使用。 預設值為 `false`。 |
| **`Values`** | 當專案在本機執行時所使用的應用程式設定和連接字串的陣列。 這些索引鍵/值（字串字串）組會對應至 Azure 中函數應用程式中的應用程式設定，例如 [`AzureWebJobsStorage`] 。 許多觸發程式和系結都有參考連接字串應用程式設定的屬性， `Connection` 例如[Blob 儲存體觸發](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)程式。 針對這些屬性，您需要在陣列中定義的應用程式設定 `Values` 。 <br/>[`AzureWebJobsStorage`]是 HTTP 以外的觸發程式所需的應用程式設定。 <br/>2.x 版和更新版本的函式執行時間需要 [ `FUNCTIONS_WORKER_RUNTIME` ] 設定，這是由核心工具針對您的專案所產生的。 <br/> 當您已在本機安裝[Azure 儲存體模擬器](../articles/storage/common/storage-use-emulator.md)，並將設定 [`AzureWebJobsStorage`] 為時 `UseDevelopmentStorage=true` ，Core Tools 會使用模擬器。 模擬器在開發期間很有用，但您應該在部署之前使用實際的儲存體連接進行測試。<br/> 值必須是字串，而不是 JSON 物件或陣列。 設定名稱不能包含冒號（ `:` ）或雙底線（ `__` ）。 這些字元是由執行時間所保留。  |
| **`Host`** | 當您在本機執行專案時，此區段中的設定會自訂函式主機進程。 這些設定與 [設定] 上的 host.js不同，這也適用于在 Azure 中執行專案時。 |
| **`LocalHttpPort`** | 設定於執行本機 Functions 主機 (`func host start` 和 `func run`) 時所使用的預設連接埠。 `--port`命令列選項的優先順序高於此設定。 |
| **`CORS`** | 定義針對[跨來源資源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 所允許的來源。 來源是以不含空格的逗號分隔清單提供。 支援萬用字元值 (\*)，它允許來自任何來源的要求。 |
| **`CORSCredentials`** |  當設定為時 `true` ，會允許 `withCredentials` 要求。 |
| **`ConnectionStrings`** | 集合。 請勿將此集合用於函數系結所使用的連接字串。 只有通常會從設定檔的區段取得連接字串的架構 `ConnectionStrings` ，例如[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)，才會使用這個集合。 此物件中的連接字串會新增至具有 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) 提供者類型的環境。 此集合中的專案不會與其他應用程式設定一起發行至 Azure。 您必須明確地將這些值新增至 `Connection strings` 函數應用程式設定的集合。 如果您要在函式 [`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 程式碼中建立，您應該將連接字串值與您的其他連接儲存在入口網站的 [**應用程式設定**] 中。 |

[AzureWebJobsStorage]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
