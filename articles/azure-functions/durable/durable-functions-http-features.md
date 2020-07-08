---
title: Durable Functions Azure Functions 中的 HTTP 功能
description: 瞭解 Azure Functions 的 Durable Functions 延伸模組中的整合式 HTTP 功能。
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 1ffa116f6877b58d54c22f918b4e83574b85860c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82800714"
---
# <a name="http-features"></a>HTTP 功能

Durable Functions 有數個功能，可讓您輕鬆地將長期協調流程和實體納入 HTTP 工作流程中。 本文將詳細說明其中一些功能。

## <a name="exposing-http-apis"></a>公開 HTTP Api

您可以使用 HTTP 要求叫用和管理協調流程和實體。 Durable Functions 擴充功能會公開內建的 HTTP Api。 它也提供 Api，可讓您從 HTTP 觸發的函式中與協調流程和實體互動。

### <a name="built-in-http-apis"></a>內建的 HTTP Api

Durable Functions 延伸模組會自動將一組 HTTP Api 新增至 Azure Functions 主機。 使用這些 Api，您可以不需要撰寫任何程式碼，即可與協調流程和實體互動並加以管理。

支援下列內建 HTTP Api。

* [啟動新的協調流程](durable-functions-http-api.md#start-orchestration)
* [查詢協調流程實例](durable-functions-http-api.md#get-instance-status)
* [終止協調流程實例](durable-functions-http-api.md#terminate-instance)
* [將外來事件傳送至協調流程](durable-functions-http-api.md#raise-event)
* [清除協調流程歷程記錄](durable-functions-http-api.md#purge-single-instance-history)
* [將作業事件傳送至實體](durable-functions-http-api.md#signal-entity)
* [取得實體的狀態](durable-functions-http-api.md#get-entity)
* [查詢實體清單](durable-functions-http-api.md#list-entities)

如需 Durable Functions 延伸模組所公開之所有內建 HTTP Api 的完整說明，請參閱[HTTP api 文章](durable-functions-http-api.md)。

### <a name="http-api-url-discovery"></a>HTTP API URL 探索

[協調流程用戶端](durable-functions-bindings.md#orchestration-client)系結會公開可產生便利 HTTP 回應裝載的 api。 例如，它可以建立回應，其中包含特定協調流程實例的管理 Api 連結。 下列範例顯示的 HTTP 觸發程式函式示範如何將此 API 用於新的協調流程實例：

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

使用先前所示的 HTTP 觸發程式函式來啟動協調器函數，可以使用任何 HTTP 用戶端來完成。 下列捲曲命令會啟動名為的協調器函式 `DoWork` ：

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

接下來是協調流程的範例回應， `abc123` 其識別碼為。 為了清楚起見，已移除一些詳細資料。

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

在上述範例中，以結尾的每個欄位都會 `Uri` 對應至內建的 HTTP API。 您可以使用這些 Api 來管理目標協調流程實例。

> [!NOTE]
> Webhook Url 的格式取決於您正在執行的 Azure Functions 主機版本。 上一個範例是針對 Azure Functions 2.0 主機。

如需所有內建 HTTP Api 的說明，請參閱[HTTP API 參考](durable-functions-http-api.md)。

### <a name="async-operation-tracking"></a>非同步作業追蹤

先前所述之 HTTP 回應的設計目的，是協助搭配長期函式實作長時間執行 HTTP 非同步 API。 此模式有時稱為「*輪詢取用者模式*」。 用戶端/伺服器流量運作方式如下：

1. 用戶端發出 HTTP 要求來啟動長時間執行的進程，例如協調器函式。
1. 目標 HTTP 觸發程式會傳回 HTTP 202 回應，其位置標頭的值為 "statusQueryGetUri"。
1. 用戶端會輪詢 Location 標頭中的 URL。 用戶端會繼續查看具有 Location 標頭的 HTTP 202 回應。
1. 當實例完成或失敗時，Location 標頭中的端點會傳回 HTTP 200。

此通訊協定可協調長時間執行的進程與外部用戶端或服務，以輪詢 HTTP 端點並遵循 Location 標頭。 此模式的用戶端和伺服器都內建于 Durable Functions 的 HTTP Api。

> [!NOTE]
> 根據預設，[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) 提供的所有 HTTP 型動作皆支援標準的非同步作業模式。 這項功能可以嵌入長期執行長期函式，作為 Logic Apps 工作流程的一部分。 您可以在[Azure Logic Apps 工作流程動作和觸發程式檔](../../logic-apps/logic-apps-workflow-actions-triggers.md)中找到 LOGIC APPS 非同步 HTTP 模式支援的詳細資料。

> [!NOTE]
> 與協調流程的互動可以從任何函式類型來完成，而不只是透過 HTTP 觸發的函數。

如需有關如何使用用戶端 Api 來管理協調流程和實體的詳細資訊，請參閱[實例管理一文](durable-functions-instance-management.md)。

## <a name="consuming-http-apis"></a>使用 HTTP Api

如協調器函式程式[代碼條件約束](durable-functions-code-constraints.md)中所述，協調器函式無法直接執行 i/o。 相反地，它們通常會呼叫執行 i/o 作業的[活動函數](durable-functions-types-features-overview.md#activity-functions)。

從 Durable Functions 2.0 開始，協調流程可以使用[協調流程觸發](durable-functions-bindings.md#orchestration-trigger)程式系結，以原生方式使用 HTTP api。

下列範例程式碼顯示發出輸出 HTTP 要求的協調器函式：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = yield context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

藉由使用「呼叫 HTTP」動作，您可以在協調器函式中執行下列動作：

* 直接從協調流程函式呼叫 HTTP Api，並在稍後提到一些限制。
* 自動支援用戶端 HTTP 202 狀態輪詢模式。
* 使用[Azure 受控](../../active-directory/managed-identities-azure-resources/overview.md)識別向其他 azure 端點進行經授權的 HTTP 呼叫。

直接從協調器函式取用 HTTP Api 的功能，是為了方便特定一組常見的案例使用。 您可以使用活動功能自行執行所有這些功能。 在許多情況下，活動函數可能會讓您有更大的彈性。

### <a name="http-202-handling"></a>HTTP 202 處理

「呼叫 HTTP」 API 可以自動執行輪詢取用者模式的用戶端。 如果呼叫的 API 傳回具有 Location 標頭的 HTTP 202 回應，協調器函式會自動輪詢位置資源，直到收到202以外的回應為止。 此回應會是傳回給協調器函式程式碼的回應。

> [!NOTE]
> 協調器函式也原本就支援伺服器端輪詢取用者模式，如[非同步作業追蹤](#async-operation-tracking)中所述。 這項支援表示一個函式應用程式中的協調流程可以輕鬆地協調其他函數應用程式中的協調器函式。 這類似于[子協調流程](durable-functions-sub-orchestrations.md)概念，但支援跨應用程式通訊。 這種支援特別適用于微服務樣式的應用程式開發。

### <a name="managed-identities"></a>受控身分識別

Durable Functions 原本就支援對接受 Azure Active Directory （Azure AD）權杖以進行授權的 Api 呼叫。 這項支援使用[Azure 受控](../../active-directory/managed-identities-azure-resources/overview.md)識別來取得這些權杖。

下列程式碼是 .NET 協調器函式的範例。 此函式會使用 Azure Resource Manager[虛擬機器 REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)，進行已驗證的呼叫以重新開機虛擬機器。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

在上述範例中， `tokenSource` 參數設定為取得[Azure Resource Manager](../../azure-resource-manager/management/overview.md)的 Azure AD token。 權杖是由資源 URI 所識別 `https://management.core.windows.net` 。 此範例假設目前的函式應用程式正在本機執行，或已部署為具有受控識別的函式應用程式。 本機身分識別或受控識別會假設有許可權可管理所指定資源群組中的 Vm `myRG` 。

在執行時間，設定的權杖來源會自動傳回 OAuth 2.0 存取權杖。 接著，來源會將權杖做為持有人權杖新增至傳出要求的授權標頭。 基於下列原因，此模型是將授權標頭手動新增至 HTTP 要求的改進：

* 權杖重新整理會自動處理。 您不需要擔心過期的權杖。
* 權杖永遠不會以持久的協調流程狀態儲存。
* 您不需要撰寫任何程式碼來管理權杖取得。

您可以在先行[編譯 c # RestartVMs 範例](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)中找到更完整的範例。

受控識別不限於 Azure 資源管理。 您可以使用受控識別來存取任何接受 Azure AD 持有人權杖的 API，包括來自 Microsoft 的 Azure 服務和合作夥伴的 web 應用程式。 合作夥伴的 web 應用程式甚至可以是另一個函數應用程式。 如需 Microsoft 提供支援使用 Azure AD 驗證的 Azure 服務清單，請參閱[支援 Azure AD 驗證的 azure 服務](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

### <a name="limitations"></a>限制

呼叫 HTTP Api 的內建支援是一個方便的功能。 並非適用于所有案例。

協調器函式和其回應所傳送的 HTTP 要求會序列化，並持續成為佇列訊息。 此佇列行為可確保 HTTP 呼叫在[協調流程重新執行時是可靠且安全的](durable-functions-orchestrations.md#reliability)。 不過，佇列行為也有限制：

* 相較于原生 HTTP 用戶端，每個 HTTP 要求都會牽涉到額外的延遲。
* 無法納入佇列訊息的大型要求或回應訊息可能會大幅降低協調流程效能。 將訊息承載卸載至 blob 儲存體的額外負荷，可能會導致效能降低。
* 不支援串流、區塊和二進位承載。
* 自訂 HTTP 用戶端行為的能力有限。

如果任何這些限制可能會影響您的使用案例，請考慮改為使用活動函式和語言特定的 HTTP 用戶端程式庫來發出輸出 HTTP 呼叫。

> [!NOTE]
> 如果您是 .NET 開發人員，您可能會想知道這項功能為何會使用**DurableHttpRequest**和**DurableHttpResponse**類型，而不是內建的 .net **HttpRequestMessage**和**HttpResponseMessage**類型。
>
> 這是刻意設計的選擇。 主要的原因是自訂類型有助於確保使用者不會對內部 HTTP 用戶端支援的行為做出不正確的假設。 Durable Functions 的特定類型也可以簡化 API 設計。 它們也可以更輕鬆地建立可用的特殊功能，例如[受控識別整合](#managed-identities)和[輪詢取用者模式](#http-202-handling)。 

### <a name="extensibility-net-only"></a>擴充性（僅限 .NET）

您可以使用[Azure Functions .net](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)相依性插入，自訂協調流程內部 HTTP 用戶端的行為。 這種功能對於進行小型行為變更很有用。 它也適用于透過插入模擬物件來對 HTTP 用戶端進行單元測試。

下列範例示範如何使用相依性插入，針對呼叫外部 HTTP 端點的協調器函式停用 TLS/SSL 憑證驗證。

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [瞭解持久性實體](durable-functions-entities.md)
