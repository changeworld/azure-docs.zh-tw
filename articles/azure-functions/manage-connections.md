---
title: 在 Azure 函數中管理連接
description: 了解如何使用靜態連線用戶端來避免 Azure Functions 中的效能問題。
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276448"
---
# <a name="manage-connections-in-azure-functions"></a>在 Azure 函數中管理連接

函數應用中的函數共用資源。 這些共用資源包括連接：HTTP 連接、資料庫連接和與 Azure 存儲等服務的連接。 同時執行許多函式時，可能會將可用的連線用完。 本文介紹如何對函數進行編碼，以避免使用比所需更多的連接。

## <a name="connection-limit"></a>連接限制

可用連接的數量是有限的，部分原因是函數應用在[沙箱環境中](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)運行。 沙箱對代碼施加的限制之一是限制出站連接數，當前每個實例有 600 個活動連接（共 1，200 個）。 達到此限制時，函數運行時將以下消息寫入日誌： `Host thresholds exceeded: Connections`。 有關詳細資訊，請參閱[函數服務限制](functions-scale.md#service-limits)。

此限制是每個實例。 當[縮放控制器添加函數應用實例](functions-scale.md#how-the-consumption-and-premium-plans-work)以處理更多請求時，每個實例都有獨立的連接限制。 這意味著沒有全域連接限制，並且所有活動實例之間可以有超過 600 個活動連接。

進行故障排除時，請確保已為函數應用啟用了應用程式見解。 應用程式見解允許您查看函數應用的指標，如執行。 有關詳細資訊，請參閱[在應用程式見解中查看遙測](functions-monitoring.md#view-telemetry-in-application-insights)。  

## <a name="static-clients"></a>靜態用戶端

若要避免保有超過所需的連線，請重複使用用戶端執行個體，而不是在每次函式引動過程建立新的執行個體。 我們建議將用戶端連接重新用於可能編寫函數的任何語言。 例如，如果使用單個靜態用戶端，則 .NET 用戶端（如[HttpClient、DocumentClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)） 和 Azure 存儲用戶端可以管理連接。 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)

在 Azure 函數應用程式中使用特定于服務的用戶端時，應遵循以下一些準則：

- *不要*創建具有每個函式呼叫的新用戶端。
- *創建*單個靜態用戶端，每個函式呼叫都可以使用該用戶端。
- 如果不同的函數使用相同的服務，*請考慮*在共用説明器類中創建單個靜態用戶端。

## <a name="client-code-examples"></a>用戶端程式碼範例

本節示範如何從您的函式程式碼建立和使用用戶端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 範例 (C#)

下面是創建靜態[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)實例的 C# 函數代碼的示例：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

關於 .NET 中的[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)的常見問題是"我應該處置我的用戶端嗎？ 通常，在使用完物件後，可以`IDisposable`釋放實現的物件。 但是，您不會釋放靜態用戶端，因為函數結束時尚未使用它。 您希望靜態用戶端在您應用程式的使用期間存留。

### <a name="http-agent-examples-javascript"></a>HTTP 代理示例（JavaScript）

因為它提供更好的連接管理選項，所以應該使用本機[`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent)類而不是非本機方法，如`node-fetch`模組。 連接參數通過類上`http.agent`的選項進行配置。 有關 HTTP 代理提供的詳細選項，請參閱[新的代理（\[選項\]）。](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)

使用的`http.request()`全域`http.globalAgent`類將所有這些值設置為其各自的預設值。 在 Functions 中設定連線限制的建議方式是全域設定最大數目。 下列範例會針對函式應用程式設定通訊端數目上限：

```js
http.globalAgent.maxSockets = 200;
```

 以下示例僅針對該請求創建具有自訂 HTTP 代理的新 HTTP 要求：

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient 程式碼範例 (C#)

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 會連線到 Azure Cosmos DB 執行個體。 Azure Cosmos DB 文件建議您[在應用程式存留期內使用單一 Azure Cosmos DB 用戶端](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage)。 下列範例顯示在函式中執行該作業的一種模式：

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>宇宙用戶端代碼示例（JavaScript）
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient)連接到 Azure 宇宙資料庫實例。 Azure Cosmos DB 文件建議您[在應用程式存留期內使用單一 Azure Cosmos DB 用戶端](../cosmos-db/performance-tips.md#sdk-usage)。 下列範例顯示在函式中執行該作業的一種模式：

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient 連線

函數代碼可以使用 SQL Server 的 .NET 框架資料提供程式[（SqlClient）](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)與 SQL 關係資料庫建立連接。 這也是依賴于ADO.NET的資料框架（如[實體框架](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)）的基礎提供程式。 不同於 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) \(英文\) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 連線，ADO.NET 預設會實作連線共用。 但是，由於您仍然可能耗盡連接，因此應優化到資料庫的連接。 如需詳細資訊，請參閱 [SQL Server 連線共用 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling) \(機器翻譯\)。

> [!TIP]
> 某些資料框架（如實體框架）通常從設定檔的**ConnectStrings**部分獲取連接字串。 在此情況下，您必須明確地將 SQL 資料庫連接字串新增至函數應用程式設定的**連接字串**集合，以及您本機專案的 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中。 如果要在函數代碼中創建[SqlConnect](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)實例，則應將連接字串值與其他連接一起存儲在**應用程式設定**中。

## <a name="next-steps"></a>後續步驟

有關我們推薦靜態用戶端的原因的詳細資訊，請參閱[不當具現化反模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

如需更多 Azure Functions 效能祕訣，請參閱[將 Azure Functions 效能和可靠性最佳化](functions-best-practices.md)。
