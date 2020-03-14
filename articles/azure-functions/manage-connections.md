---
title: 管理 Azure Functions 中的連接
description: 了解如何使用靜態連線用戶端來避免 Azure Functions 中的效能問題。
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: 872ad9a1b8f0a7da6fe410e68f08469ac11045a5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276448"
---
# <a name="manage-connections-in-azure-functions"></a>管理 Azure Functions 中的連接

函數應用程式中的函式會共用資源。 這些共用的資源包括連線： HTTP 連線、資料庫連接，以及與服務（例如 Azure 儲存體）的連接。 同時執行許多函式時，可能會將可用的連線用完。 本文說明如何撰寫函式的程式碼，以避免使用超過所需的連接。

## <a name="connection-limit"></a>連接限制

可用的連線數目有限，部分原因是函式應用程式會在[沙箱環境](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)中執行。 沙箱對您程式碼施加的其中一項限制是輸出連線數目的限制，這是目前每個實例600個作用中（1200 total）個連線。 當您達到此限制時，函數執行時間會將下列訊息寫入記錄檔： `Host thresholds exceeded: Connections`。 如需詳細資訊，請參閱[函數服務限制](functions-scale.md#service-limits)。

這是每個實例的限制。 當[調整控制器新增函式應用程式實例](functions-scale.md#how-the-consumption-and-premium-plans-work)來處理更多要求時，每個實例都有獨立的連接限制。 這表示沒有全域連線限制，而且在所有作用中的實例上，您可以擁有超過600個作用中的連接。

進行疑難排解時，請確定您已啟用函數應用程式的 Application Insights。 Application Insights 可讓您查看函數應用程式的計量，例如執行。 如需詳細資訊，請參閱[View 遙測 in Application Insights](functions-monitoring.md#view-telemetry-in-application-insights)。  

## <a name="static-clients"></a>靜態用戶端

若要避免保有超過所需的連線，請重複使用用戶端執行個體，而不是在每次函式引動過程建立新的執行個體。 我們建議您針對可能會在其中撰寫函式的任何語言重複使用用戶端連接。 例如，如果您使用單一靜態用戶端，則[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)、 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)和 Azure 儲存體的 .net 用戶端可以管理連接。

以下是當您在 Azure Functions 應用程式中使用服務特定的用戶端時，應遵循的一些指導方針：

- *請勿*使用每個函式呼叫來建立新的用戶端。
- *請*建立單一靜態用戶端，每個函式呼叫都可以使用。
- 如果不同的函式使用相同的服務，*請考慮*在共用的 helper 類別中建立單一靜態用戶端。

## <a name="client-code-examples"></a>用戶端程式碼範例

本節示範如何從您的函式程式碼建立和使用用戶端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 範例 (C#)

以下是建立靜態C# [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)實例的函式程式碼範例：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

在 .NET 中[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx)的常見問題是「我應該處置我的用戶端嗎？」 一般來說，您會在使用完畢時，處置用來執行 `IDisposable` 的物件。 但是您不會處置靜態用戶端，因為在函式結束時不會使用它。 您希望靜態用戶端在您應用程式的使用期間存留。

### <a name="http-agent-examples-javascript"></a>HTTP 代理程式範例（JavaScript）

因為它提供更好的連線管理選項，所以，您應該使用原生的 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) \(英文\) 類別而不是非原生的方法，例如 `node-fetch` 模組。 連接參數是透過 `http.agent` 類別上的選項來設定。 如需 HTTP 代理程式可用的詳細選項，請參閱[新的代理程式（\[選項\]）](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)。

`http.request()` 使用的全域 `http.globalAgent` 類別，會將這些值都設定為各自的預設值。 在 Functions 中設定連線限制的建議方式是全域設定最大數目。 下列範例會針對函式應用程式設定通訊端數目上限：

```js
http.globalAgent.maxSockets = 200;
```

 下列範例只會針對該要求，使用自訂 HTTP 代理程式建立新的 HTTP 要求：

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

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient 程式碼範例（JavaScript）
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient)會連接到 Azure Cosmos DB 實例。 Azure Cosmos DB 文件建議您[在應用程式存留期內使用單一 Azure Cosmos DB 用戶端](../cosmos-db/performance-tips.md#sdk-usage)。 下列範例顯示在函式中執行該作業的一種模式：

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

您的函式程式碼可以使用 .NET Framework Data Provider SQL Server （[SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)）來連接到 SQL 關係資料庫。 這也是依賴 ADO.NET 之資料架構的基礎提供者，例如[Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)。 不同於 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) \(英文\) 和 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) \(英文\) 連線，ADO.NET 預設會實作連線共用。 但是，因為您仍然可以用完連接，所以您應該將資料庫的連接優化。 如需詳細資訊，請參閱 [SQL Server 連線共用 (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling) \(機器翻譯\)。

> [!TIP]
> 某些資料架構（例如 Entity Framework）通常會從設定檔的**ConnectionStrings**區段取得連接字串。 在此情況下，您必須明確地將 SQL 資料庫連接字串新增至函數應用程式設定的**連接字串**集合，以及您本機專案的 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中。 如果您要在函式程式碼中建立[SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx)的實例，您應該將連接字串值儲存在**應用程式設定**中，與其他連接。

## <a name="next-steps"></a>後續步驟

如需為何建議靜態用戶端的詳細資訊，請參閱不適當的具現[化反模式](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)。

如需更多 Azure Functions 效能祕訣，請參閱[將 Azure Functions 效能和可靠性最佳化](functions-best-practices.md)。
