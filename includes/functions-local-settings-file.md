---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/14/2019
ms.author: glenga
ms.openlocfilehash: 1c2196f1f834002b76dbea555b54a5162655ec1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205738"
---
## <a name="local-settings-file"></a>本機設定檔

本地.settings.json 檔存儲應用設置、連接字串和本地開發工具使用的設置。 僅當在本地運行專案時，才會使用本地.settings.json 檔中的設置。 本地設置檔具有以下結構：

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

在本地運行專案時，支援這些設置：

| 設定      | 描述                            |
| ------------ | -------------------------------------- |
| **`IsEncrypted`** | 當此設置設置為`true`時，所有值都使用本地電腦金鑰加密。 需搭配 `func settings` 命令使用。 預設值為 `false`。 |
| **`Values`** | 專案在本地運行時使用的應用程式設定和連接字串的陣列。 這些鍵值（字串字串）對對應于 Azure 中的函數應用中的應用程式設定，如[`AzureWebJobsStorage`]。 許多觸發器和綁定具有引用連接字串應用設置的屬性，如`Connection`Blob[存儲觸發器](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#configuration)。 對於這些屬性，您需要在陣列中`Values`定義應用程式設定。 <br/>[`AzureWebJobsStorage`]是 HTTP 以外的觸發器所需的應用設置。 <br/>版本 2.x 和更高版本的函數運行時需要 *`FUNCTIONS_WORKER_RUNTIME`設置，該設置由核心工具為您的專案生成。 <br/> 當您在本地安裝[Azure 存儲模擬器](../articles/storage/common/storage-use-emulator.md)並設置為[`AzureWebJobsStorage`]`UseDevelopmentStorage=true`"核心工具"時，使用模擬器。 模擬器在開發期間很有用，但在部署之前應使用實際存儲連接進行測試。<br/> 值必須是字串，而不是 JSON 物件或陣列。 設置名稱不能包括冒號 （`:`） 或雙下劃`__`線 （ 。 這些字元由運行時保留。  |
| **`Host`** | 本節中的設置在本地運行專案時自訂"功能"主機進程。 這些設置與 host.json 設置是分開的，在 Azure 中運行專案時，這些設置也會應用。 |
| **`LocalHttpPort`** | 設定於執行本機 Functions 主機 (`func host start` 和 `func run`) 時所使用的預設連接埠。 命令`--port`行選項優先于此設置。 |
| **`CORS`** | 定義針對[跨來源資源共享 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 所允許的來源。 來源是以不含空格的逗號分隔清單提供。 支援萬用字元值 (\*)，它允許來自任何來源的要求。 |
| **`CORSCredentials`** |  設置為 時`true`，允許`withCredentials`請求。 |
| **`ConnectionStrings`** | 集合。 不要將此集合用於函數綁定使用的連接字串。 此集合僅由通常從設定檔`ConnectionStrings`部分（如[實體框架](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)）獲取連接字串的框架使用。 此物件中的連接字串會新增至具有 [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx) 提供者類型的環境。 此集合中的專案不會與其他應用設置一起發佈到 Azure。 您必須顯式將這些值添加到函數應用設置`Connection strings`的集合中。 如果要在函數代碼中創建[`SqlConnection`](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)，則應在門戶中的 **"應用程式設定"** 中存儲連接字串值和其他連接。 |

["Azure Web 作業存儲"]: ../articles/azure-functions/functions-app-settings.md#azurewebjobsstorage
