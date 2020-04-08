---
title: 持久函數中的 HTTP 功能 - Azure 函數
description: 瞭解 Azure 函數的持久函數擴展中的整合 HTTP 功能。
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: fece1155d2f707f11dda9f3896bd8a08deff1557
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802378"
---
# <a name="http-features"></a>HTTP 功能

持久功能具有多種功能,可輕鬆將耐用的業務流程和實體合併到 HTTP 工作流中。 本文詳細介紹了其中一些功能。

## <a name="exposing-http-apis"></a>公開 HTTP API

可以使用 HTTP 請求調用和管理業務流程和實體。 持久函數擴展公開內置的 HTTP API。 它還提供 API,用於在 HTTP 觸發函數中與業務流程和實體進行互動。

### <a name="built-in-http-apis"></a>內建 HTTP API

持久函數延伸自動向 Azure 函數主機添加一組 HTTP API。 借助這些 API,您可以與業務流程和實體進行互動和管理,而無需編寫任何代碼。

支援以下內建 HTTP API。

* [開始新的業務流程](durable-functions-http-api.md#start-orchestration)
* [查詢業務流程實例](durable-functions-http-api.md#get-instance-status)
* [結束業務流程實體](durable-functions-http-api.md#terminate-instance)
* [傳送外部事件](durable-functions-http-api.md#raise-event)
* [清除業務流程歷程記錄](durable-functions-http-api.md#purge-single-instance-history)
* [傳送此實體](durable-functions-http-api.md#signal-entity)
* [取得實體的狀態](durable-functions-http-api.md#get-entity)
* [查詢實體清單](durable-functions-http-api.md#list-entities)

有關持久函數擴展公開的所有內置 HTTP API 的完整說明,請參閱[HTTP API 一文](durable-functions-http-api.md)。

### <a name="http-api-url-discovery"></a>HTTP API URL 探索

[業務流程用戶端綁定](durable-functions-bindings.md#orchestration-client)公開可以生成方便的 HTTP 回應負載的 API。 例如,它可以創建一個回應,其中包含指向特定業務流程實例的管理 API 的連結。 以下範例顯示了一個 HTTP 觸發器函數,該函式示範如何為新業務流程實例使用此 API:

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**function.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

使用前面顯示的 HTTP 觸發器函數啟動協調器函數可以使用任何 HTTP 客戶端來完成。 以下 cURL 命令啟動`DoWork`名為 的 業務流程函數:

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

接下來是具有`abc123`作為ID的業務流程的範例回應。 為了清楚起見,已刪除某些詳細資訊。

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

在前面的示例中,以結尾`Uri`的每個欄位都對應於一個內置的 HTTP API。 您可以使用這些 API 來管理目標業務流程實例。

> [!NOTE]
> Webhook URL 的格式取決於正在運行的 Azure 函數主機的版本。 前面的範例適用於 Azure 函數 2.0 主機。

有關所有內置 HTTP API 的說明,請參閱 HTTP [API 引用](durable-functions-http-api.md)。

### <a name="async-operation-tracking"></a>非同步作業追蹤

先前所述之 HTTP 回應的設計目的，是協助搭配長期函式實作長時間執行 HTTP 非同步 API。 此模式有時稱為*輪詢使用者模式*。 用戶端/伺服器流量運作方式如下：

1. 客戶端發出 HTTP 請求以啟動長時間運行的進程,如協調器函數。
1. 目標 HTTP 觸發器返回具有值「狀態查詢GetUri」的位置標頭的 HTTP 202 回應。
1. 用戶端輪詢位置標頭中的 URL。 用戶端繼續看到帶有位置標頭的 HTTP 202 回應。
1. 當實例完成或失敗時,位置標頭中的終結點將返回 HTTP 200。

此協議允許與外部用戶端或服務協調長時間運行的進程,這些服務可以輪詢 HTTP 終結點並遵循位置標頭。 此模式的用戶端和伺服器實現都內置到持久函數 HTTP API 中。

> [!NOTE]
> 根據預設，[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) 提供的所有 HTTP 型動作皆支援標準的非同步作業模式。 這項功能可以嵌入長期執行長期函式，作為 Logic Apps 工作流程的一部分。 您可以在[Azure 邏輯應用工作流操作和觸發器文件](../../logic-apps/logic-apps-workflow-actions-triggers.md)中尋找有關非同步 HTTP 模式的邏輯應用支援的更多詳細資訊。

> [!NOTE]
> 與業務流程的交互可以從任何函數類型完成,而不僅僅是 HTTP 觸發的函數。

有關如何使用用戶端 API 管理業務流程和實體的詳細資訊,請參閱[實例管理文章](durable-functions-instance-management.md)。

## <a name="consuming-http-apis"></a>使用 HTTP API

如[協調器函數代碼約束](durable-functions-code-constraints.md)中所述,協調器函數不能直接執行 I/O。 相反,它們通常呼叫 I/O 操作[的活動函數](durable-functions-types-features-overview.md#activity-functions)。

從持久函數 2.0 開始,業務流程可以使用[業務流程觸發器綁定](durable-functions-bindings.md#orchestration-trigger)本機使用 HTTP API。

以下範例代碼顯示發出出站 HTTP 請求的協調器函數:

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
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

以使用「調用 HTTP」操作,您可以在協調器函數中執行以下操作:

* 直接從業務流程調用 HTTP API,後面會提到一些限制。
* 自動支援客戶端 HTTP 202 狀態輪詢模式。
* 使用[Azure 託管識別](../../active-directory/managed-identities-azure-resources/overview.md)對其他 Azure 終結點進行授權 HTTP 調用。

直接從協調器函數使用 HTTP API 的功能是為了方便一組特定常見方案。 您可以使用活動函數自行實現所有這些功能。 在許多情況下,活動功能可能會為您提供更大的靈活性。

### <a name="http-202-handling"></a>HTTP 202 處理

"呼叫 HTTP"API 可以自動實現輪詢消費者模式的用戶端。 如果被調用的 API 傳回具有位置標頭的 HTTP 202 回應,協調器函數將自動輪詢位置資源,直到收到 202 以外的回應。 此回應將是返回給協調器函數代碼的回應。

> [!NOTE]
> 協調器函數還本機支援伺服器端輪詢消費者模式,如[Async 操作追蹤](#async-operation-tracking)中所述。 這種支援意味著一個函數應用中的業務流程可以輕鬆地協調其他函數應用中的業務流程函數。 這與[子業務流程](durable-functions-sub-orchestrations.md)概念類似,但支援跨應用通信。 此支持對於微服務式應用開發特別有用。

### <a name="managed-identities"></a>受控身分識別

持久函數本機支援對接受 Azure 活動目錄 (Azure AD) 令牌進行授權的 API 的調用。 此支援使用[Azure 託管識別](../../active-directory/managed-identities-azure-resources/overview.md)來獲取這些權杖。

以下代碼是 .NET 協調器函數的範例。 該函數使用 Azure 資源管理器[虛擬機器 REST API](https://docs.microsoft.com/rest/api/compute/virtualmachines)進行經過身份驗證的呼叫以重新啟動虛擬機器。

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

在前面的範例中,`tokenSource`參數設定為為 Azure 資源管理器獲取[Azure](../../azure-resource-manager/management/overview.md)AD 權杖。 令牌由資源URI`https://management.core.windows.net`標識。 該示例假定當前函數應用在本地運行,或者已部署為具有託管標識的函數應用。 假定本地識別或託管標識具有管理指定資源組中`myRG`的 VM 的許可權。

在運行時,配置的權杖來源會自動傳回 OAuth 2.0 存取權杖。 然後,源將令牌作為承載令牌添加到傳出請求的授權標頭。 此模型與手動向 HTTP 請求添加授權標頭的改進,原因如下:

* 令牌刷新將自動處理。 您無需擔心令牌過期。
* 令牌永遠不會存儲在持久業務流程狀態中。
* 您無需編寫任何代碼來管理權杖獲取。

您可以在[預編譯的 C# 重新啟動 VM 範例中](https://github.com/Azure/azure-functions-durable-extension/blob/dev/samples/precompiled/RestartVMs.cs)找到一個更完整的範例。

託管標識不僅限於 Azure 資源管理。 可以使用託管標識存取接受 Azure AD 承載權杖的任何 API,包括來自 Microsoft 的 Azure 服務和合作夥伴的 Web 應用。 合作夥伴的 Web 應用甚至可以是另一個功能應用。 有關 Microsoft 支援使用 Azure AD 進行認證的 Azure 服務的清單,請參閱[支援 Azure AD 認證的 Azure 服務](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)。

### <a name="limitations"></a>限制

對調用 HTTP API 的內置支援是一項方便的功能。 它不適合所有方案。

由協調器函數發送的 HTTP 請求及其回應作為佇列消息序列化並持久化。 此排隊行為可確保 HTTP 呼叫對於[業務流程重播是可靠和安全的](durable-functions-orchestrations.md#reliability)。 但是,排隊行為也有限制:

* 與本機 HTTP 用戶端相比,每個 HTTP 請求都涉及額外的延遲。
* 無法放入佇列消息的大型請求或回應消息可能會顯著降低業務流程性能。 將消息負載卸載到 Blob 儲存的開銷可能會導致潛在的性能下降。
* 不支援流式處理、分塊和二進位有效負載。
* 自定義 HTTP 客戶端行為的能力是有限的。

如果這些限制中的任何一個可能會影響您的用例,請考慮改用活動函數和特定於語言的 HTTP 用戶端庫進行出站 HTTP 調用。

> [!NOTE]
> 如果您是 .NET 開發人員,您可能會想知道為什麼此功能使用**持久 HTTPRequest**和**持久 HttpResponse**類型,而不是內置 .NET **HTTPRequestMessage**和**HttpResponseMessage**類型。
>
> 這是刻意設計的選擇。 主要原因是自定義類型有助於確保使用者不會對內部 HTTP 客戶端的支援行為做出不正確的假設。 特定於持久函數的類型還使得簡化 API 設計成為可能。 它們還可以更輕鬆地提供託管[身份集成](#managed-identities)和[輪詢消費者模式](#http-202-handling)等特殊功能。 

### <a name="extensibility-net-only"></a>可擴充性(僅限.NET)

可以使用[Azure 函數 .NET 依賴項注入](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)自訂業務流程的內部 HTTP 客戶端的行為。 這種能力可用於進行小的行為改變。 它還可用於透過注入類比物件來單元測試 HTTP 用戶端。

下面的範例展示使用依賴項注入來禁用對呼叫外部 HTTP 終結點的協調器函數的 TLS/SSL 憑證驗證。

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
> [瞭解持久實體](durable-functions-entities.md)
