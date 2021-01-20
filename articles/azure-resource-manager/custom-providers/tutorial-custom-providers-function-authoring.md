---
title: 建立 RESTful 端點
description: 本教學課程將說明如何撰寫自訂提供者的 RESTful 端點。 文中會詳述如何處理所支援 RESTful HTTP 方法的要求和回應。
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200204"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>撰寫自訂提供者的 RESTful 端點

自訂提供者是 Azure 和端點之間的合約。 透過自訂提供者，您可以在 Azure 上自訂工作流程。 本教學課程將說明如何撰寫自訂提供者 RESTful 端點。 如果您不熟悉 Azure 自訂提供者，請參閱[自訂資源提供者概觀](overview.md)。

> [!NOTE]
> 本教學課程以[設定適用於 Azure 自訂提供者的 Azure Functions](./tutorial-custom-providers-function-setup.md) 教學課程作為基礎。 本教學課程內的某些步驟只會在 Azure 函式應用程式已設定為使用自訂提供者時才適用。

## <a name="work-with-custom-actions-and-custom-resources"></a>使用自訂動作和自訂資源

您會在本教學課程中更新函式應用程式，使其作為自訂提供者的 RESTful 端點。 Azure 中的資源和動作會仿造下列基本 RESTful 規格：

- **PUT**：建立新的資源
- **GET (執行個體)** ：擷取現有資源
- **DELETE**：移除現有資源
- **POST**：觸發動作
- **GET (集合)** ：列出所有現有資源

 在本教學課程中，您會使用 Azure 資料表儲存體。 但任何資料庫或儲存體服務都適用。

## <a name="partition-custom-resources-in-storage"></a>分割儲存體中的自訂資源

因為您要建立 RESTful 服務，因此必須儲存已建立的資源。 若為 Azure 資料表儲存體，您必須為資料產生資料分割和資料列索引鍵。 若為自訂提供者，則應該將資料分割至自訂提供者。 當傳入要求傳送至自訂提供者時，自訂提供者會將 `x-ms-customproviders-requestpath` 標頭新增至端點的傳出要求。

以下範例將示範自訂資源的 `x-ms-customproviders-requestpath` 標頭：

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

根據範例的 `x-ms-customproviders-requestpath` 標頭，您可以建立儲存體的 partitionKey  和 rowKey  參數，如下表所示：

參數 | [範本] | 描述
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | partitionKey  參數會指定資料的分割方式。 通常會由自訂提供者執行個體來分割資料。
*rowKey* | `{myResourceType}:{myResourceName}` | rowKey  參數會指定資料的個別識別碼。 識別碼通常是資源的名稱。

您也需要建立新的類別，以便為自訂資源建模。 在本教學課程中，您會將下列 **CustomResource** 類別新增至您的函式應用程式：

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** 是簡單的泛型類別，可接受任何輸入資料。 並且以用來儲存資料的 **TableEntity** 為基礎。 **CustomResource** 類別會繼承 **TableEntity** 中的兩個屬性：**partitionKey** 和 **rowKey**。

## <a name="support-custom-provider-restful-methods"></a>支援自訂提供者 RESTful 方法

> [!NOTE]
> 如果您沒有要直接從教學課程複製程式碼，則回應內容必須是有效的 JSON，且 `Content-Type` 標頭應設定為 `application/json`。

現在您已設定好資料分割，接著可建立基本 CRUD，並為自訂資源和自訂動作觸發方法。 由於自訂提供者會作為 Proxy，因此 RESTful 端點必須建立要求和回應的模型並加以處理。 下列程式碼片段將示範如何處理基本的 RESTful 作業。

### <a name="trigger-a-custom-action"></a>觸發自訂動作

若為自訂提供者，自訂動作會透過 POST 要求來觸發。 自訂動作可以選擇性地接受含有一組輸入參數的要求主體。 然後，該動作會傳回回應來表示動作的結果，以及動作是成功還是失敗。

將下列 **TriggerCustomAction** 方法新增至您的函式應用程式：

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

**TriggerCustomAction** 方法會接受傳入要求，並只傳回有狀態碼的回應。

### <a name="create-a-custom-resource"></a>建立自訂資源

若為自訂提供者，自訂資源會透過 PUT 要求來建立。 自訂提供者會接受 JSON 要求主體，其內含一組自訂資源屬性。 Azure 中的資源會遵循 RESTful 模型。 您可以使用相同的要求 URL 來建立、擷取或刪除資源。

新增下列 **CreateCustomResource** 方法以建立新的資源：

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

**CreateCustomResource** 方法會更新傳入要求，以納入 Azure 的特定欄位：**id**、**name** 和 **type**。 這些欄位是 Azure 上所有服務所使用的最上層屬性。 其可讓自訂提供者與其他服務交互操作，例如 Azure 原則、Azure Resource Manager 範本和 Azure 活動記錄等服務。

屬性 | 範例 | 描述
---|---|---
**name** | {myCustomResourceName} | 自訂資源的名稱
**type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | 資源類型命名空間
**id** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | 資源識別碼

除了新增屬性外，您也已將 JSON 文件儲存到 Azure 資料表儲存體。

### <a name="retrieve-a-custom-resource"></a>擷取自訂資源

若為自訂提供者，自訂資源會透過 GET 要求來擷取。 自訂提供者「不會」  接受 JSON 要求主體。 如果是 GET 要求，端點會使用 `x-ms-customproviders-requestpath` 標頭來傳回已建立的資源。

新增下列 **RetrieveCustomResource** 方法以擷取現有的資源：

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

在 Azure 中，資源會遵循 RESTful 模型。 如果執行 GET 要求，建立資源的要求 URL 應該也會傳回資源。

### <a name="remove-a-custom-resource"></a>移除自訂資源

若為自訂提供者，自訂資源會透過 DELETE 要求來移除。 自訂提供者「不會」  接受 JSON 要求主體。 如果是 DELETE 要求，端點會使用 `x-ms-customproviders-requestpath` 標頭來刪除已建立的資源。

新增下列 **RemoveCustomResource** 方法以移除現有的資源：

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

在 Azure 中，資源會遵循 RESTful 模型。 如果執行 DELETE 要求，建立資源的要求 URL 應該也會刪除資源。

### <a name="list-all-custom-resources"></a>列出所有自訂資源

若為自訂提供者，您可以使用集合 GET 要求來列舉現有自訂資源的清單。 自訂提供者「不會」  接受 JSON 要求主體。 如果是 GET 要求的集合，端點應該會使用 `x-ms-customproviders-requestpath` 標頭來列舉已建立的資源。

新增下列 **EnumerateAllCustomResources** 方法以列舉現有的資源：

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey QueryComparisons.GreaterThan 和 QueryComparisons.LessThan 是用來針對字串執行 "startswith" 查詢的 Azure 資料表儲存體語法。

若要列出所有現有資源，應產生 Azure 資料表儲存體查詢，以確保資源會存在於自訂提供者資料分割底下。 然後，查詢會確認資料列索引鍵的開頭是否同樣為 `{myResourceType}` 值。

## <a name="integrate-restful-operations"></a>整合 RESTful 作業

所有 RESTful 方法都新增至函式應用程式後，更新主要的 **Run** 方法，以呼叫函式處理不同的 REST 要求：

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

更新的 **Run** 方法現在會包含您為 Azure 資料表儲存體新增的 tableStorage  輸入繫結。 此方法的第一個部分會讀取 `x-ms-customproviders-requestpath` 標頭，並使用 `Microsoft.Azure.Management.ResourceManager.Fluent` 程式庫來將值剖析為資源識別碼。 `x-ms-customproviders-requestpath` 標頭會由自訂提供者傳送，並指定傳入要求的路徑。

藉由使用剖析的資源識別碼，您可以為資料產生 **partitionKey** 和 **rowKey** 值以查閱或儲存自訂資源。

新增方法和類別之後，您必須更新函式的 **using** 方法。 將下列程式碼新增至 C# 檔案的頂端：

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

如果您在本教學課程的任何時間點退出，您可以在[自訂提供者C# RESTful 端點參照](./reference-custom-providers-csharp-endpoint.md)中找到完整的程式碼範例。 當您完成函式應用程式之後，請儲存函式應用程式 URL。 在稍後的教學課程中，您可以用它來觸發函式應用程式。

## <a name="next-steps"></a>後續步驟

在本文中，您已撰寫 RESTful 端點來使用 Azure 自訂提供者端點。 若要了解如何建立自訂提供者，請移至[教學課程：建立自訂提供者](./tutorial-custom-providers-create.md)一文。
