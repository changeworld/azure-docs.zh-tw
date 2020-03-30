---
title: Azure 應用程式見解中的遙測通道 |微軟文檔
description: 如何在 Azure 應用程式見解 SDK 中自訂 .NET 和 .NET 核心的遙測通道。
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275694"
---
# <a name="telemetry-channels-in-application-insights"></a>應用程式洞察中的遙測通道

遙測通道是[Azure 應用程式見解 SDK](../../azure-monitor/app/app-insights-overview.md)不可分割的一部分。 它們管理遙測的緩衝和傳輸到應用程式見解服務。 SDK 的 .NET 和 .NET Core 版本具有兩個內置遙測通道：`InMemoryChannel`和`ServerTelemetryChannel`。 本文詳細介紹了每個通道，包括如何自訂通道行為。

## <a name="what-are-telemetry-channels"></a>什麼是遙測通道？

遙測通道負責緩衝遙測項並將其發送到應用程式見解服務，並存儲它們以進行查詢和分析。 遙測通道是實現[`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet)介面的任何類。

調用`Send(ITelemetry item)`所有遙測初始化器和遙測處理器後，將調用遙測通道的方法。 因此，遙測處理器丟棄的任何專案都無法訪問通道。 `Send()`通常不會立即將專案發送到後端。 通常，它會將它們緩衝在記憶體中並分批發送，以便進行有效的傳輸。

[即時指標流](live-stream.md)還具有一個自訂通道，用於支援遙測的即時流。 此通道獨立于常規遙測通道，此文檔不適用於它。

## <a name="built-in-telemetry-channels"></a>內置遙測通道

應用見解 .NET 和 .NET 核心 SDK 附帶兩個內置通道：

* `InMemoryChannel`：一個羽量級通道，用於緩衝記憶體中的項，直到它們被發送。 專案在記憶體中緩衝，每 30 秒刷新一次，或者每當緩衝 500 個專案時。 此通道提供最低的可靠性保證，因為它不會在發生故障後重試發送遙測資料。 此通道也不會將專案保留在磁片上，因此任何未發送的專案在應用程式關閉時會永久丟失（正常與否）。 此通道實現一`Flush()`種方法，該方法可用於同步強制刷新任何記憶體中遙測項。 此通道非常適合同步刷新理想的短運行應用。

    此通道是更大的 Microsoft. ApplicationInsights NuGet 包的一部分，是 SDK 在未配置其他功能時使用的預設通道。

* `ServerTelemetryChannel`：具有重試策略和在本地磁片上存儲資料的功能的更高級通道。 如果發生暫時性錯誤，此通道重試發送遙測。 此通道還使用本地磁片存儲在網路中斷或高遙測卷期間將專案保留在磁片上。 由於這些重試機制和本地磁片存儲，因此此通道被認為更可靠，建議用於所有生產方案。 此通道是根據官方文檔[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)配置ASP.NET[和ASP.NET核心](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)應用程式的預設值。 此通道針對具有長時間運行進程的伺服器方案進行了優化。 此[`Flush()`](#which-channel-should-i-use)通道實現的方法不是同步的。

    此通道作為 Microsoft.應用程式 Insights.WindowsServer.遙測通道 NuGet 包提供，當您使用 Microsoft.ApplicationInsights.Web 或 Microsoft.應用程式見解.AspNetCore NuGet 時自動獲取包。

## <a name="configure-a-telemetry-channel"></a>配置遙測通道

通過將遙測通道設置為活動遙測配置來配置遙測通道。 對於ASP.NET應用程式，配置涉及將遙測通道實例設置為`TelemetryConfiguration.Active`或修改`ApplicationInsights.config`。 對於ASP.NET核心應用程式，配置涉及將通道添加到依賴項注入容器。

以下各節顯示了在各種應用程式類型中配置`StorageFolder`通道設置的示例。 `StorageFolder`只是可配置的設置之一。 有關配置設置的完整清單，請參閱本文後面的[設置部分](telemetry-channels.md#configurable-settings-in-channels)。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>使用應用程式 Insights.config 進行ASP.NET應用程式的配置

[應用Insights.config](configuration-with-applicationinsights-config.md)中的以下部分顯示了配置`ServerTelemetryChannel`為`StorageFolder`自訂位置的通道：

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET應用程式的代碼配置

以下代碼設置一個"伺服器遙測通道"實例，該`StorageFolder`實例設置為自訂位置。 在應用程式開頭添加此代碼，通常在Global.aspx.cs 中的方法`Application_Start()`中添加此代碼。

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET核心應用程式的代碼配置

修改類`ConfigureServices`的方法，`Startup.cs`如下所示：

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> 不建議使用配置通道`TelemetryConfiguration.Active`以ASP.NET核心應用程式。

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET 核心主控台應用程式的代碼配置

對於主控台應用，.NET 和 .NET 核心的代碼相同：

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>伺服器遙測通道的操作詳細資訊

`ServerTelemetryChannel`將到達的專案存儲在記憶體中緩衝區中。 這些專案每 30 秒或緩衝 500`Transmission`個專案時，將序列化、壓縮並存儲在實例中。 單個`Transmission`實例包含最多 500 個專案，表示通過單個 HTTPS 調用發送到應用程式見解服務的一批遙測資料。

預設情況下，最多可以並行發送 10`Transmission`個實例。 如果遙測以更快的速度到達，或者如果網路或應用程式見解後端速度較慢，`Transmission`則實例將存儲在記憶體中。 此記憶體`Transmission`緩衝器的預設容量為 5 MB。 當記憶體容量超過時，`Transmission`實例存儲在本地磁片上，限制高達 50 MB。 `Transmission`當存在網路問題時，實例也會存儲在本地磁片上。 只有存儲在本地磁片上的那些專案才能在應用程式崩潰中存活下來。 每當應用程式再次啟動時，它們都會發送。

## <a name="configurable-settings-in-channels"></a>通道中的可配置設置

有關每個通道可配置設置的完整清單，請參閱：

* [記憶體通道](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [伺服器遙測通道](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

以下是 最常用的設置`ServerTelemetryChannel`：

1. `MaxTransmissionBufferCapacity`：通道用於緩衝記憶體傳輸的最大記憶體量（以位元組為單位）。 達到此容量時，新專案將直接存儲在本地磁片上。 預設值是 5 MB。 設置較高的值會導致磁片使用量降低，但請記住，如果應用程式崩潰，記憶體中的專案將丟失。

1. `MaxTransmissionSenderCapacity`：將同時發送到應用程式`Transmission`見解的最大實例數。 預設值是 10。 此設置可以配置為更高的數位，建議在生成大量遙測時使用。 在負載測試期間或關閉採樣時，通常會發生高容量。

1. `StorageFolder`：通道用於根據需要將專案存儲到磁片的資料夾。 在 Windows 中，如果未顯式指定其他路徑，則使用 %LOCALAPPDATA% 或 %TEMP%。 在 Windows 以外的環境中，必須指定有效位置，否則遙測資料不會存儲到本地磁片。

## <a name="which-channel-should-i-use"></a>我應該使用哪個頻道？

`ServerTelemetryChannel`建議適用于涉及長時間運行的應用程式的大多數生產方案。 實現`Flush()``ServerTelemetryChannel`的方法不是同步的，它也不保證從記憶體或磁片發送所有掛起的項。 如果在應用程式即將關閉的情況下使用此通道，我們建議您在調用`Flush()`後引入一些延遲。 您可能需要的確切延遲量是不可預測的。 這取決於記憶體中有多少項或`Transmission`實例、磁片上有多少項、傳輸到後端的項或實例數以及通道是否處於指數級後退方案的中間等因素。

如果需要執行同步刷新，我們建議您使用`InMemoryChannel`。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>應用程式見解通道是否保證遙測交付？ 如果沒有，遙測可能丟失的情況是什麼？

簡短的回答是，沒有一個內置通道提供到後端的遙測的事務類型保證。 `ServerTelemetryChannel`與可靠的傳遞相比`InMemoryChannel`更先進，但它也只是盡力嘗試發送遙測資料。 遙測仍可在多種情況下丟失，包括以下常見情況：

1. 當應用程式崩潰時，記憶體中的項將丟失。

1. 在網路問題出現長時間期間，遙測將丟失。 遙測資料在網路中斷期間或應用程式見解後端出現問題時存儲到本地磁片。 但是，超過 48 小時的物品將被丟棄。

1. 用於在 Windows 中存儲遙測的預設磁片位置為 %LOCALAPPDATA% 或 %TEMP%。 這些位置通常是機器的本地位置。 如果應用程式從物理方式從一個位置遷移到另一個位置，則存儲在原始位置中的任何遙測資料將丟失。

1. 在 Windows 上的 Web 應用中，預設磁片存儲位置為 D：\local_localAppData。 此位置未持久化。 它在應用重新開機、橫向擴展和其他此類操作中已消失，從而導致存儲在那裡的任何遙測資料丟失。 您可以覆蓋預設值，並將存儲指定為保留位置（如 D：\home）。 但是，此類持久化位置由遠端存放提供，因此速度可能很慢。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>伺服器遙測頻道是否在 Windows 以外的系統上工作？

儘管其包和命名空間的名稱包括"WindowsServer"，但 Windows 以外的系統支援此通道，但以下情況除外。 在 Windows 以外的系統上，預設情況下，通道不會創建本機存放區資料夾。 您必須創建本機存放區資料夾，並將通道配置為使用它。 配置本機存放區後，通道在所有系統上的工作方式相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK 是否會建立暫存本機儲存體？ 資料存儲是否加密？

SDK 在網路問題期間或限制期間將遙測項存儲在本機存放區中。 此資料未在本地加密。

對於 Windows 系統，SDK 會自動在 %TEMP% 或 %LOCALAPPDATA% 目錄中創建臨時本地資料夾，並限制僅對管理員和當前使用者的存取權限。

對於 Windows 以外的系統，SDK 不會自動創建本機存放區，因此預設情況下不會本機存放區任何資料。 您可以自己創建存儲目錄，並將通道配置為使用它。 在這種情況下，您有責任確保目錄的安全。
閱讀更多有關[資料保護和隱私的資訊](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)。

## <a name="open-source-sdk"></a>開放原始碼 SDK
與應用程式洞察的每個 SDK 一樣，通道是開源的。 在[官方的 GitHub 存儲庫](https://github.com/Microsoft/ApplicationInsights-dotnet)中讀取並貢獻代碼或報告問題。

## <a name="next-steps"></a>後續步驟

* [採樣](../../azure-monitor/app/sampling.md)
* [SDK 故障排除](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
