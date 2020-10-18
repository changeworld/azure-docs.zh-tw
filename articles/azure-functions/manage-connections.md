---
title: 管理 Azure Functions 中的連接
description: 了解如何使用靜態連線用戶端來避免 Azure Functions 中的效能問題。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: 6a426aff1721ac3565b53cf2eef7c5aa094dd7e2
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168302"
---
# <a name="manage-connections-in-azure-functions"></a>管理 Azure Functions 中的連接

函數應用程式中的函式會共用資源。 這些共用資源包括連接： HTTP 連線、資料庫連接，以及與服務（例如 Azure 儲存體）的連接。 同時執行許多函式時，可能會將可用的連線用完。 本文說明如何編寫函式的程式碼，以避免使用比所需更多的連接。

## <a name="connection-limit"></a>連線限制

可用的連接數目有限，部分原因是函式應用程式是在 [沙箱環境](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)中執行。 沙箱針對您的程式碼所強加的其中一項限制，就是輸出連接數目的限制，目前每個實例的600作用中 (1200 總) 連接數。 當您達到此限制時，函式執行時間會將下列訊息寫入至記錄檔： `Host thresholds exceeded: Connections` 。 如需詳細資訊，請參閱 [函數服務限制](functions-scale.md#service-limits)。

這是每個實例的限制。 當 [調整控制器新增函數應用程式實例](functions-scale.md#how-the-consumption-and-premium-plans-work) 來處理更多要求時，每個實例都有獨立的連接限制。 這表示沒有全域連線限制，而且您可以在所有使用中的實例上擁有超過600個使用中的連接。

進行疑難排解時，請確定您已為函數應用程式啟用 Application Insights。 Application Insights 可讓您查看函式應用程式（例如執行）的計量。 如需詳細資訊，請參閱 [Application Insights 中的查看遙測](analyze-telemetry-data.md#view-telemetry-in-application-insights)。  

## <a name="static-clients"></a>靜態用戶端

若要避免保有超過所需的連線，請重複使用用戶端執行個體，而不是在每次函式引動過程建立新的執行個體。 建議您針對可能撰寫函數的任何語言重複使用用戶端連接。 例如，如果您使用單一靜態用戶端，例如 [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true)、 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)和 Azure 儲存體用戶端的 .net 用戶端就可以管理連接。

以下是當您在 Azure Functions 應用程式中使用服務特定用戶端時，應遵循的一些指導方針：

- *請勿* 使用每個函式呼叫來建立新的用戶端。
- *請* 建立單一的靜態用戶端，讓每個函式呼叫都可以使用。
- 如果不同的函式使用相同的服務，*請考慮*在共用 helper 類別中建立單一靜態用戶端。

## <a name="client-code-examples"></a>用戶端程式碼範例

本節示範如何從您的函式程式碼建立和使用用戶端的最佳做法。

### <a name="httpclient-example-c"></a>HttpClient 範例 (C#)

以下是 c # 函式程式碼的範例，可建立靜態 [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) 實例：

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

在 .NET 中 [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1&preserve-view=true) 的常見問題是「我應該處置我的用戶端嗎？」 一般而言，您會處置使用完成時所執行的物件 `IDisposable` 。 但是您不會處置靜態用戶端，因為當函式結束時，不會使用它。 您希望靜態用戶端在您應用程式的使用期間存留。

### <a name="http-agent-examples-javascript"></a> (JavaScript) 的 HTTP 代理程式範例

因為它提供更好的連接管理選項，所以您應該使用原生 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 類別，而不是非原生方法，例如 `node-fetch` 模組。 連接參數是透過類別上的選項進行設定 `http.agent` 。 如需 HTTP 代理程式可用的詳細選項，請參閱 [ \[ \]) 的新代理程式 (選項 ](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)。

使用的全域類別，會 `http.globalAgent` `http.request()` 將所有這些值設定為其各自的預設值。 在 Functions 中設定連線限制的建議方式是全域設定最大數目。 下列範例會針對函式應用程式設定通訊端數目上限：

```js
http.globalAgent.maxSockets = 200;
```

 下列範例只會針對該要求，使用自訂 HTTP 代理程式來建立新的 HTTP 要求：

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient 程式碼範例 (C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) \(英文\) 會連線到 Azure Cosmos DB 執行個體。 Azure Cosmos DB 文件建議您[在應用程式存留期內使用單一 Azure Cosmos DB 用戶端](../cosmos-db/performance-tips.md#sdk-usage)。 下列範例顯示在函式中執行該作業的一種模式：

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
如果您使用的是 v3. x，您需要參考來 Microsoft.Azure.DocumentDB。 在程式碼中新增參考：

```cs
#r "Microsoft.Azure.DocumentDB.Core"
```
此外，針對您的觸發程式建立名為 "function" 的檔案，並新增下列內容：

```cs

<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netcoreapp3.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.DocumentDB.Core" Version="2.12.0" />
    </ItemGroup>
</Project>

```
### <a name="cosmosclient-code-example-javascript"></a> (JavaScript) 的 CosmosClient 程式碼範例
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) 會連接到 Azure Cosmos DB 實例。 Azure Cosmos DB 文件建議您[在應用程式存留期內使用單一 Azure Cosmos DB 用戶端](../cosmos-db/performance-tips.md#sdk-usage)。 下列範例顯示在函式中執行該作業的一種模式：

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

您的函式程式碼可以使用 .NET Framework Data Provider SQL Server ([SqlClient](/dotnet/api/system.data.sqlclient)) ，以連接到 SQL 關係資料庫。 這也是依賴 ADO.NET 的資料架構的基礎提供者，例如 [Entity Framework](/ef/ef6/)。 不同於 [HttpClient](/dotnet/api/system.net.http.httpclient) \(英文\) 和 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) \(英文\) 連線，ADO.NET 預設會實作連線共用。 但因為您仍然可以用完連接，所以您應該將資料庫的連接優化。 如需詳細資訊，請參閱 [SQL Server 連線共用 (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling) \(機器翻譯\)。

> [!TIP]
> 某些資料框架（例如 Entity Framework）通常會從設定檔的 **ConnectionStrings** 區段取得連接字串。 在此情況下，您必須明確地將 SQL 資料庫連接字串新增至函數應用程式設定的**連接字串**集合，以及您本機專案的 [local.settings.json 檔案](functions-run-local.md#local-settings-file)中。 如果您要在函式程式碼中建立 [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection) 的實例，您應該在 **應用程式設定** 中將連接字串值與其他連接一起儲存。

## <a name="next-steps"></a>後續步驟

如需建議靜態用戶端原因的詳細資訊，請參閱不當具現 [化反模式](/azure/architecture/antipatterns/improper-instantiation/)。

如需更多 Azure Functions 效能祕訣，請參閱[將 Azure Functions 效能和可靠性最佳化](functions-best-practices.md)。
