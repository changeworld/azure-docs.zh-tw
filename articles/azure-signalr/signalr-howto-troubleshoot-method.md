---
title: Azure SignalR Service 的疑難排解作法
description: 瞭解如何針對連線能力和訊息傳遞問題進行疑難排解
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: 2e22777b747ae24c3e643cbd43bfdb0604d453a2
ms.sourcegitcommit: 17e9cb8d05edaac9addcd6e0f2c230f71573422c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/21/2020
ms.locfileid: "97707651"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>如何針對連線能力和訊息傳遞問題進行疑難排解

本指南引進數種方式來協助自行診斷，以直接找出根本原因或縮小問題範圍。 將自我診斷結果回報給我們以進行進一步調查時，也會很有用。

首先，您必須檢查 Azure 入口網站 [ServiceMode](./concept-service-mode.md) 是 Azure SignalR Service (也稱為 **ASRS**) 設定為。

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* 針對 `Default` 模式，請參閱 [預設模式疑難排解](#default_mode_tsg)

* 針對 `Serverless` 模式，請參閱 [無伺服器模式疑難排解](#serverless_mode_tsg)

* 針對 `Classic` 模式，請參閱 [傳統模式疑難排解](#classic_mode_tsg)

<a name="default_mode_tsg"></a>

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="default-mode-troubleshooting"></a>預設模式疑難排解

當 **ASRS** 處於 *預設* 模式時，有 **三個** 角色： *用戶端*、 *伺服器* 和 *服務*：

* *客戶* 端： *客戶* 端代表連接至 **ASRS** 的用戶端。 連接用戶端和 **ASRS** 的持續連線，在本指南中稱為 *用戶端* 連線。

* *Server*： *server* 代表提供用戶端協商和裝載 SignalR 邏輯的伺服器 `Hub` 。 而且 *伺服器* 與 **ASRS** 之間的持續連線，在本指南中稱為 *伺服器連接* 。

* *服務*： *服務* 是本指南中 **ASRS** 的簡短名稱。

請參閱 [Azure SignalR Service 的內部](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) ，以深入瞭解整個架構和工作流程。

有數種方式可協助您縮小問題的範圍。 

* 如果問題是直接發生，或是可重現的，那麼直接的方法就是查看進行中的流量。 

* 如果問題很難重現，追蹤和記錄檔可能會有所説明。

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>如何查看流量並將問題縮小

捕捉流量的最簡單方式，就是縮小問題的最簡單方法。 您可以使用以下所述的選項來捕捉 [網路追蹤](/aspnet/core/signalr/diagnostics#network-traces) ：

* [使用 Fiddler 收集網路追蹤](/aspnet/core/signalr/diagnostics#network-traces)

* [使用 tcpdump 收集網路追蹤](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [在瀏覽器中收集網路追蹤](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>用戶端要求

針對 SignalR 持續連線，它會先連接 `/negotiate` 到您託管的應用程式伺服器，然後重新導向至 Azure SignalR 服務，然後建立與 Azure SignalR 服務的真正持續連線。 如需詳細步驟，請參閱 [Azure SignalR Service 的內部](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) 。

在用戶端的網路追蹤中，檢查哪些要求失敗，並顯示狀態碼和回應的內容，以及在 [疑難排解指南](./signalr-howto-troubleshoot-guide.md)內尋找解決方案。

#### <a name="server-requests"></a>伺服器要求

SignalR *伺服器* 會維護 *伺服器* 與 *服務* 之間的 *伺服器連接*。 當應用程式伺服器啟動時，會啟動與 Azure SignalR service 的 **WebSocket** 連線。 所有用戶端流量都會透過 Azure SignalR 服務路由傳送到這些 *伺服器連接*，然後分派至 `Hub` 。 當 *伺服器連接* 中斷時，路由至此 *伺服器* 連線的用戶端將會受到影響。 我們的 Azure SignalR SDK 有邏輯「永遠重試」，可將 *伺服器連接* 重新連接到最多1分鐘的延遲，以將影響降至最低。

*伺服器* 連線可能會因為網路不穩定或 Azure SignalR Service 的定期維護或您託管的應用程式伺服器更新/維護而下降。 只要用戶端有中斷連線/重新連線機制，影響就會很簡單，就像任何用戶端造成中斷連線一樣。

查看伺服器端網路追蹤，以找出 *伺服器連接* 卸載或被 *服務* 拒絕的狀態碼和錯誤詳細資料，並尋找 [疑難排解指南](./signalr-howto-troubleshoot-guide.md)內的根本原因。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>如何新增記錄

記錄有助於診斷問題和監視執行狀態。

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>如何啟用用戶端記錄檔

用戶端記錄體驗與使用自我裝載 SignalR 的方式完全相同。

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>啟用的用戶端記錄 `ASP.NET Core SignalR`

* [JavaScript 用戶端記錄](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [.NET 用戶端記錄](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>啟用的用戶端記錄 `ASP.NET SignalR`

* [.NET 用戶端](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [在 Windows Phone 8 用戶端中啟用追蹤](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [在 JavaScript 用戶端中啟用追蹤](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>如何啟用伺服器端記錄檔

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>啟用的伺服器端記錄 `ASP.NET Core SignalR`

的伺服器端記錄可 `ASP.NET Core SignalR` 與 `ILogger` 架構中提供的 [記錄](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&view=aspnetcore-2.1) 整合 `ASP.NET Core` 。 您可以使用的範例使用方式來啟用伺服器端記錄 `ConfigureLogging` ，如下所示：

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Azure SignalR 的記錄器類別一律以開始 `Microsoft.Azure.SignalR` 。 若要從 Azure SignalR 啟用詳細記錄，請將前面的前置詞設定為檔案 `Information` **appsettings.js的** 層級，如下所示：

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

檢查是否有任何異常的警告/錯誤記錄檔。 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>啟用的伺服器端追蹤 `ASP.NET SignalR`

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

檢查是否有任何異常的警告/錯誤記錄檔。 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>如何在 Azure SignalR service 內啟用記錄

您也可以啟用 Azure SignalR service 的 [診斷記錄](./signalr-howto-diagnostic-logs.md) ，這些記錄會提供連線到 azure SignalR 服務之每個連線的詳細資訊。

<a name="serverless_mode_tsg"></a>

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>無伺服器模式疑難排解

當 **ASRS** 處於 *無伺服器* 模式時，只有 **ASP.NET Core SignalR** 支援 `Serverless` 模式，而 **ASP.NET SignalR** 不支援此模式。 

若要在模式中診斷連線問題 `Serverless` ，最直接的方式就是 [查看用戶端流量](#view_traffic_client)。 啟用 [用戶端記錄](#add_logs_client) 和 [服務端記錄](#add_logs_server) 也很有説明。

<a name="classic_mode_tsg"></a>

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>傳統模式疑難排解

`Classic` 模式已過時，不建議使用。 在此模式中，Azure SignalR service 會使用連接的 *伺服器連接* ，判斷目前的服務是否處於 `default` 模式或 `serverless` 模式。 這可能會導致某些中繼用戶端連線問題，因為當所有連線的 *伺服器* 連線突然中斷時（例如因為網路不穩定），Azure SignalR 相信它現在已切換至 `serverless` 模式，而在這段期間內連線的用戶端永遠不會路由傳送到託管的應用程式伺服器。 啟用 [服務端記錄](#add_logs_server) ，並檢查是否有任何用戶端記錄為裝載的 `ServerlessModeEntered` 應用程式伺服器，但有些用戶端永遠不會到達應用程式伺服器端。 如果有的話，請 [中止這些用戶端](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) 連線，讓用戶端重新開機可提供協助。

疑難排解 `classic` 模式連接和訊息傳遞問題類似于 [疑難排解預設模式問題](#default_mode_tsg)。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>服務健康情況

您可以檢查健康情況 api 的服務健康情況。

* 要求： GET `https://{instance_name}.service.signalr.net/api/v1/health`

* 回應狀態碼：
  * 200：狀況良好。
  * 503：您的服務狀況不良。 您可以：
    * 等候數分鐘以進行自動回復。
    * 請檢查 ip 位址與入口網站中的 ip 位址是否相同。
    * 或重新開機實例。
    * 如果上述所有選項都無法運作，請在 Azure 入口網站中加入新的支援要求，以聯繫我們。

有關嚴重損壞 [修復](./signalr-concept-disaster-recovery.md)的詳細資訊。

[有關于疑難排解的問題或意見反應？請讓我們知道。](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>後續步驟

在本指南中，您已瞭解如何針對連線能力和訊息傳遞問題進行疑難排解。 您也可以瞭解如何處理常見的問題。 

> [!div class="nextstepaction"]
> [疑難排解指南](./signalr-howto-troubleshoot-guide.md)