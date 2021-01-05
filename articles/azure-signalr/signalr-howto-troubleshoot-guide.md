---
title: Azure SignalR Service 的疑難排解指南
description: 瞭解如何針對常見的問題進行疑難排解
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: yajin1
ms.openlocfilehash: 505176758e1dbba1d6bf262554568edd8a197a4d
ms.sourcegitcommit: 17e9cb8d05edaac9addcd6e0f2c230f71573422c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2020
ms.locfileid: "97707668"
---
# <a name="troubleshooting-guide-for-azure-signalr-service-common-issues"></a>Azure SignalR Service 常見問題的疑難排解指南

本指南旨在根據客戶在過去幾年所符合和解決的常見問題，提供實用的疑難排解指南。

## <a name="access-token-too-long"></a>存取權杖太長

### <a name="possible-errors"></a>可能的錯誤：

* 用戶端 `ERR_CONNECTION_`
* 414 URI 太長
* 413 承載太大
* 存取權杖的長度不得超過4K。 413 要求實體太大

### <a name="root-cause"></a>根源：

若為 HTTP/2，單一標頭的最大長度為 **4 K**，因此，如果使用 browser 來存取 Azure 服務，這項限制將會發生錯誤 `ERR_CONNECTION_` 。

若為 HTTP/1.1 或 c # 用戶端，最大 URI 長度為 **12 k**，最大標頭長度為 **16 k**。

使用 SDK 版本 **1.0.6** 或更高版本 `/negotiate` 時，會 `413 Payload Too Large` 在產生的存取權杖大於 **4 K** 時擲回。

### <a name="solution"></a>解決方案：

根據預設，在 `context.User.Claims` 產生 JWT 存取權杖給 **ASRS** (Azure z) **S** ignal **R** **S** service) 時，會包含的宣告，以便在用戶端連接至時，將宣告保留，並可從 **ASRS** 傳遞至 `Hub` `Hub` 。

在某些情況下， `context.User.Claims` 會利用來儲存大量的應用程式伺服器資訊，但大部分都不是由和 `Hub` 其他元件使用。

產生的存取權杖會透過網路傳遞，而針對 WebSocket/SSE 連接，則會透過查詢字串傳遞存取權杖。 因此，最佳做法是，建議您只在中樞需要時，透過 **ASRS** 將 **必要** 的宣告從用戶端傳遞至您的應用程式伺服器。

您可以在 `ClaimsProvider` 存取權杖內自訂傳遞至 **ASRS** 的宣告。

針對 ASP.NET Core：
```cs
services.AddSignalR()
        .AddAzureSignalR(options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context => context.User.Claims.Where(...);
            });
```

針對 ASP.NET：
```cs
services.MapAzureSignalR(GetType().FullName, options =>
            {
                // pick up necessary claims
                options.ClaimsProvider = context.Authentication?.User.Claims.Where(...);
            });
```

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tls-12-required"></a>需要 TLS 1。2

### <a name="possible-errors"></a>可能的錯誤：

* ASP.NET 「沒有可用的伺服器」錯誤 [#279](https://github.com/Azure/azure-signalr/issues/279)
* ASP.NET 「連接不在作用中，無法將資料傳送至服務」。 錯誤 [#324](https://github.com/Azure/azure-signalr/issues/324)
* 「對 HTTPs://提出 HTTP 要求時發生錯誤 <API endpoint> 。 此錯誤可能是因為在 HTTPS 案例中，伺服器憑證未正確設定 HTTP.SYS。 這項錯誤也可能是因為用戶端與伺服器之間的安全性系結不相符所造成。」

### <a name="root-cause"></a>根源：

基於安全性考慮，Azure 服務僅支援 TLS 1.2。 使用 .NET framework 時，TLS 1.2 可能不是預設通訊協定。 因此，無法成功建立與 ASRS 的伺服器連接。

### <a name="troubleshooting-guide"></a>疑難排解指南

1. 如果此錯誤可以在本機重現，請取消核取 *Just My Code* 並擲回所有 CLR 例外狀況，並在本機偵測應用程式伺服器，以查看擲回的例外狀況。
    * 取消核取 *Just My Code*

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/uncheck-just-my-code.png" alt-text="取消核取 Just My Code":::

    * 擲回 CLR 例外狀況

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/throw-clr-exceptions.png" alt-text="擲回 CLR 例外狀況":::

    * 請參閱在偵錯工具伺服器端程式碼時擲回的例外狀況：
        
        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/tls-throws.png" alt-text="例外狀況擲回":::

2. 若為 ASP.NET，您也可以將下列程式碼加入至中， `Startup.cs` 以啟用詳細的追蹤並查看記錄檔中的錯誤。
```cs
app.MapAzureSignalR(this.GetType().FullName);
// Make sure this switch is called after MapAzureSignalR
GlobalHost.TraceManager.Switch.Level = SourceLevels.Information;
```

### <a name="solution"></a>解決方案：

將下列程式碼新增至您的啟動：
```cs
ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;
```

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="400-bad-request-returned-for-client-requests"></a>400針對用戶端要求傳回的要求錯誤

### <a name="root-cause"></a>根本原因

檢查您的用戶端要求是否有多個 `hub` 查詢字串。 `hub` 是保留的查詢參數，如果服務在查詢中偵測到多個，則會擲回 400 `hub` 。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="401-unauthorized-returned-for-client-requests"></a>針對用戶端要求傳回 401 未經授權

### <a name="root-cause"></a>根本原因

JWT 權杖存留期目前的預設值為1小時。

針對 ASP.NET Core SignalR，使用 WebSocket 傳輸類型時，這是正常的。

針對 ASP.NET Core SignalR 的其他傳輸類型（SSE 和長時間輪詢），這表示預設連接最多可保存1小時。

針對 ASP.NET SignalR，用戶端 `/ping` 會及時將 KeepAlive 要求傳送給服務，當 `/ping` 失敗時，用戶端會 **中止** 連線，而且永遠不會重新連接。 這表示，在 ASP.NET SignalR 中，預設權杖存留期會讓所有傳輸類型的連接 **最多** 持續1小時。

### <a name="solution"></a>解決方案

基於安全性考慮，不建議延伸 TTL。 建議您從用戶端新增重新連接邏輯，以便在發生這類401時重新開機連線。 當用戶端重新開機連線時，它會與應用程式伺服器協調以再次取得 JWT 權杖，並取得更新的權杖。

請 [在這裡](#restart_connection) 查看如何重新開機用戶端連接。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-client-requests"></a>針對用戶端要求傳回 404

針對 SignalR 持續連線，它會先連接 `/negotiate` 到 Azure SignalR 服務，然後建立與 Azure SignalR 服務的實際連線。

### <a name="troubleshooting-guide"></a>疑難排解指南

* 遵循 [如何查看傳出要求](#view_request) 以取得從用戶端到服務的要求。
* 在404發生時檢查要求的 URL。 如果 URL 的目標是您的 web 應用程式，而且類似于 `{your_web_app}/hubs/{hubName}` ，請檢查用戶端 `SkipNegotiation` 是否為 `true` 。 使用 Azure SignalR 時，用戶端會在第一次與應用程式伺服器協商時接收重新導向 URL。 使用 Azure SignalR 時，用戶端 **不** 應該略過協商。
* 當呼叫的連接要求超過 **5** 秒時，可能會發生另一個 404 `/negotiate` 。 檢查用戶端要求的時間戳記，如果對服務的要求回應緩慢，請向我們提出問題。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="404-returned-for-aspnet-signalrs-reconnect-request"></a>針對 ASP.NET SignalR 的 reconnect 要求傳回404

針對 ASP.NET SignalR，當 [用戶端](#client_connection_drop)連線中斷時，它會使用相同 `connectionId` 的進行重新連接，再停止連接。 `/reconnect` 可以協助您是否因為網路間歇性問題而中斷連線，而這些問題 `/reconnect` 可能會成功重新建立持續連線。 例如，在其他情況下，用戶端連接會因為路由伺服器連接中斷而中斷，或 SignalR Service 有一些內部錯誤，例如實例重新開機/容錯移轉/部署，連接已不存在，因此會傳回 `/reconnect` `404` 。 這是預期的行為 `/reconnect` ，以及在三次重試連接之後的預期行為。 當連接停止時，我們建議有 [連接重新開機](#restart_connection) 邏輯。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="429-too-many-requests-returned-for-client-requests"></a>429 (針對用戶端要求傳回的要求太多) 

有兩種案例。

### <a name="concurrent-connection-count-exceeds-limit"></a>**並行** 連接計數超過限制。

針對 **免費** 實例，**標準** 實例的 **並行** 連接計數限制為20，**每個單位** 的 **並行** 連線計數限制為 1 K，這表示 Unit100 允許 100-K 個並行連線。

連接包括用戶端和伺服器連接。 請 [在這裡](./signalr-concept-messages-and-connections.md#how-connections-are-counted) 查看連接的計算方式。

### <a name="too-many-negotiate-requests-at-the-same-time"></a>同時有太多的協商要求。

在重新連線之前，建議您先有隨機的延遲，請參閱 [這裡](#restart_connection) 以取得重試範例。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="500-error-when-negotiate-azure-signalr-service-is-not-connected-yet-please-try-again-later"></a>500當 negotiate： Azure SignalR Service 尚未連線時發生錯誤，請稍後再試一次。

### <a name="root-cause"></a>根本原因

沒有連接到 Azure SignalR Service 的伺服器連線時，就會回報此錯誤。

### <a name="troubleshooting-guide"></a>疑難排解指南

啟用伺服器端追蹤，以找出伺服器嘗試連接到 Azure SignalR Service 時的錯誤詳細資料。

#### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>針對 ASP.NET Core SignalR 啟用伺服器端記錄

ASP.NET Core SignalR 的伺服器端記錄會與 `ILogger` ASP.NET Core framework 中提供的 [記錄](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) 整合。 您可以使用的範例使用方式來啟用伺服器端記錄 `ConfigureLogging` ，如下所示：
```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```
Azure SignalR 的記錄器類別一律以開始 `Microsoft.Azure.SignalR` 。 若要從 Azure SignalR 啟用詳細記錄，請將前面的前置詞設定為檔案 `Debug` **appsettings.js的** 層級，如下所示：
```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Debug",
            ...
        }
    }
}
```

#### <a name="enable-server-side-traces-for-aspnet-signalr"></a>啟用 ASP.NET SignalR 的伺服器端追蹤

使用 SDK 版本 >= 時 `1.0.0` ，您可以將下列內容新增至 `web.config` ： ([詳細資料](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102) ，以啟用追蹤) 
```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

<a name="client_connection_drop"></a>

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-drops"></a>用戶端連接中斷

當用戶端連線到 Azure SignalR 時，用戶端與 Azure SignalR 之間的持續連線有時可能會因不同的原因而下降。 本節說明造成這類連線中斷的幾種可能性，並提供有關如何識別根本原因的一些指引。

### <a name="possible-errors-seen-from-the-client-side"></a>從用戶端看到的可能錯誤

* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`
* `{"type":7,"error":"Connection closed with an error."}`
* `{"type":7,"error":"Internal server error."}`

### <a name="root-cause"></a>根源：

用戶端連接可能會在各種情況下下降：
* 當 `Hub` 使用連入要求擲回例外狀況時。
* 當用戶端所路由傳送的伺服器連接中斷時，請參閱下一節，以取得 [伺服器連接](#server_connection_drop)中斷的詳細資料。
* 當用戶端與 SignalR Service 之間發生網路連線問題時。
* 當 SignalR Service 有一些內部錯誤，例如實例重新開機、容錯移轉、部署等等。

### <a name="troubleshooting-guide"></a>疑難排解指南

1. 開啟應用程式伺服器端記錄檔，以查看是否有任何異常發生
2. 檢查應用程式伺服器端事件記錄檔，以查看應用程式伺服器是否已重新開機
3. 為我們提供時間範圍的問題建立問題，並以電子郵件將資源名稱寄給我們

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="client-connection-increases-constantly"></a>用戶端連接不斷增加

這可能是因為用戶端連接不當使用所造成。 如果有人忘記停止/處置 SignalR 用戶端，連接會保持開啟。

### <a name="possible-errors-seen-from-the-signalrs-metrics-that-is-in-monitoring-section-of-azure-portal-resource-menu"></a>在 Azure 入口網站資源功能表的 [監視中] 區段中看到的 SignalR 計量可能發生的錯誤

用戶端連線在 Azure SignalR 的計量中持續增加一段長時間。

:::image type="content" source="./media/signalr-howto-troubleshoot-guide/client-connection-increasing-constantly.jpg" alt-text="用戶端連接不斷增加":::

### <a name="root-cause"></a>根源：

`DisposeAsync`永遠不會呼叫 SignalR 用戶端連接，連接會保持開啟。

### <a name="troubleshooting-guide"></a>疑難排解指南

1. 檢查 SignalR 用戶端是否 **永遠不會** 關閉。

### <a name="solution"></a>解決方案

檢查您是否關閉連接。 使用手動呼叫 `HubConnection.DisposeAsync()` 以停止連接。

例如：

```C#
var connection = new HubConnectionBuilder()
    .WithUrl(...)
    .Build();
try
{
    await connection.StartAsync();
    // Do your stuff
    await connection.StopAsync();
}
finally
{
    await connection.DisposeAsync();
}
```

### <a name="common-improper-client-connection-usage"></a>一般不當用戶端連接使用方式

#### <a name="azure-function-example"></a>Azure 函數範例 

當有人在 Azure 函式方法中建立 SignalR 用戶端連線，而不是將它設為函式類別的靜態成員時，通常就會發生此問題。 您可能只會建立一個用戶端連線，但您會在 [Azure 入口網站資源] 功能表的 [監視中] 區段中看到 [用戶端連線計數] 不斷增加，而這些連線只會在 Azure 函式或 Azure SignalR 服務重新開機後卸載。 這是因為對於 **每個** 要求，Azure 函式會建立 **一個** 用戶端連線，如果您未在函式方法中停止用戶端連線，用戶端會將連線保持在 Azure SignalR service 的運作狀態。

#### <a name="solution"></a>解決方案

* 如果您在 Azure 函式中使用 SignalR 用戶端，或使用 SignalR 用戶端做為 singleton，請記得關閉用戶端連線。
* 除了在 Azure 函式中使用 SignalR 用戶端之外，您還可以在其他任何地方建立 SignalR 用戶端，並使用 [Azure SignalR Service 的 Azure Functions](https://github.com/Azure/azure-functions-signalrservice-extension) 系結，將用戶端 [協商](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L22) 至 Azure SignalR。 此外，您也可以利用系結來 [傳送訊息](https://github.com/Azure/azure-functions-signalrservice-extension/blob/dev/samples/simple-chat/csharp/FunctionApp/Functions.cs#L40)。 您可以在 [這裡](https://github.com/Azure/azure-functions-signalrservice-extension/tree/dev/samples)找到 negotiate 用戶端和傳送訊息的範例。 您可以在 [這裡](https://github.com/Azure/azure-functions-signalrservice-extension)找到進一步的資訊。
* 當您在 Azure 函式中使用 SignalR 用戶端時，您的案例可能會有較佳的架構。 檢查您是否設計了適當的無伺服器架構。 您可以 [使用 Azure Functions 中的 SignalR Service 系結來參考即時無伺服器應用程式](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService)。

<a name="server_connection_drop"></a>

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="server-connection-drops"></a>伺服器連接中斷

當應用程式伺服器在背景中啟動時，Azure SDK 會開始起始與遠端 Azure SignalR 的伺服器連接。 如 [Azure SignalR Service 內部](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md)所述，Azure SignalR 會將傳入用戶端流量路由傳送到這些伺服器連線。 一旦伺服器連線中斷，它所提供的所有用戶端連接也會關閉。

由於應用程式伺服器與 SignalR Service 之間的連線是持續連線，因此可能會遇到網路連線問題。 在伺服器 SDK 中，我們一律會將策略 **重新連接** 到伺服器連接。 作為最佳作法，我們也鼓勵使用者將連續的重新連接邏輯新增至用戶端，並提供隨機的延遲時間，以避免對伺服器造成大量的要求。

Azure SignalR Service 會定期發行新的版本，而且有時會修補或升級整個 Azure 作業系統，或偶爾會中斷相依的服務。 這些可能會導致短暫的服務中斷，但只要用戶端有中斷連線/重新連線機制，影響就會很小，就像任何用戶端造成中斷連接一樣。

本節說明伺服器連接卸載的數個可能原因，並提供有關如何識別根本原因的一些指引。

### <a name="possible-errors-seen-from-server-side"></a>可能從伺服器端看到的錯誤：

* `[Error]Connection "..." to the service was dropped`
* `The remote party closed the WebSocket connection without completing the close handshake`
* `Service timeout. 30.00ms elapsed without receiving a message from service.`

### <a name="root-cause"></a>根源：

**ASRS** **(Azure z)** **S** ignal **R** **S** service) 來關閉伺服器服務連接。

### <a name="troubleshooting-guide"></a>疑難排解指南

1. 開啟應用程式伺服器端記錄檔，以查看是否有任何異常發生
2. 檢查應用程式伺服器端事件記錄檔，以查看應用程式伺服器是否已重新開機
3. 為我們提供時間範圍的問題建立問題，並以電子郵件將資源名稱寄給我們

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="tips"></a>提示

<a name="view_request"></a>

* 如何查看來自用戶端的傳出要求？
採用 ASP.NET Core 一個範例 (ASP.NET 一個類似) ：
    * 從瀏覽器：

        以 Chrome 為例，您可以使用 **F12** 來開啟主控台視窗，並切換至 [ **網路** ] 索引標籤。您可能需要使用 **F5** 重新整理頁面，以從頭開始捕獲網路。

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/chrome-network.gif" alt-text="Chrome 視圖網路":::

    * 從 c # 用戶端：

        您可以使用 [Fiddler](https://www.telerik.com/fiddler)來查看本機 web 流量。 自 Fiddler 4.5 起支援 WebSocket 流量。

        :::image type="content" source="./media/signalr-howto-troubleshoot-guide/fiddler-view-network-inline.png" alt-text="Fiddler View Network" lightbox="./media/signalr-howto-troubleshoot-guide/fiddler-view-network.png":::

<a name="restart_connection"></a>

* 如何重新開機用戶端連接？
    
    以下是包含使用「*永遠重試*」策略重新開機連接邏輯的 [範例代碼](https://github.com/Azure/azure-signalr/tree/dev/samples)：

    * [ASP.NET Core c # 用戶端](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample.CSharpClient/Program.cs#L64)

    * [ASP.NET Core JavaScript 用戶端](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample/wwwroot/index.html#L164)

    * [ASP.NET c # 用戶端](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.CSharpClient/Program.cs#L78)

    * [ASP.NET JavaScript 用戶端](https://github.com/Azure/azure-signalr/tree/dev/samples/AspNet.ChatSample/AspNet.ChatSample.JavaScriptClient/wwwroot/index.html#L71)

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何處理一般問題。 您也可以深入瞭解一般的疑難排解方法。 

> [!div class="nextstepaction"]
> [如何針對連線能力和訊息傳遞問題進行疑難排解](./signalr-howto-troubleshoot-method.md)