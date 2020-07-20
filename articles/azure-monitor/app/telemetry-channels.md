---
title: Azure 應用程式 Insights 中的遙測通道 |Microsoft Docs
description: 如何在適用于 .NET 和 .NET Core 的 Azure 應用程式 Insights Sdk 中自訂遙測通道。
ms.topic: conceptual
ms.date: 05/14/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9c292246f947e4d3a364f79b31fe7a1deebd33d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84691946"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights 中的遙測通道

遙測通道是[Azure 應用程式 Insights sdk](../../azure-monitor/app/app-insights-overview.md)不可或缺的一部分。 它們會管理遙測的緩衝處理和傳輸到 Application Insights 服務。 .NET 和 .NET Core 版本的 Sdk 有兩個內建的遙測通道： `InMemoryChannel` 和 `ServerTelemetryChannel` 。 本文詳細說明每個通道，包括如何自訂通道行為。

## <a name="what-are-telemetry-channels"></a>什麼是遙測通道？

遙測通道會負責緩衝處理遙測專案，並將它們傳送至 Application Insights 服務，並在其中儲存以供查詢和分析。 遙測通道是任何可執行介面的類別 [`Microsoft.ApplicationInsights.ITelemetryChannel`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) 。

`Send(ITelemetry item)`呼叫所有遙測初始化運算式和遙測處理器之後，會呼叫遙測通道的方法。 因此，遙測處理器捨棄的任何專案都不會到達通道。 `Send()`通常不會立即將專案傳送到後端。 一般來說，它會在記憶體中將它們緩衝，並以批次方式傳送它們，以有效率地傳輸。

[即時計量資料流](live-stream.md)也有自訂通道，可為遙測的即時串流提供動力。 此通道與一般遙測通道無關，這份檔並不適用。

## <a name="built-in-telemetry-channels"></a>內建遙測通道

Application Insights .NET 和 .NET Core Sdk 隨附兩個內建的通道：

* `InMemoryChannel`：輕量通道，會在傳送記憶體中的專案之前將它們緩衝。 專案會在記憶體中緩衝處理，並每隔30秒排清一次，或每次緩衝處理500專案。 此通道提供最低可靠性保證，因為它不會在失敗後重試傳送遙測。 此通道也不會將專案保留在磁片上，因此任何未傳送的專案都會在應用程式關閉時永久遺失（正常或不會）。 這個通道會執行 `Flush()` 可用來以同步方式強制清除任何記憶體中遙測專案的方法。 此通道非常適合執行同步排清的短期應用程式。

    此通道是較大 ApplicationInsights NuGet 套件的一部分，而且是 SDK 在未設定任何其他內容時所使用的預設通道。

* `ServerTelemetryChannel`：具有重試原則的更先進通道，以及將資料儲存在本機磁片上的功能。 若發生暫時性錯誤，此通道會重試傳送遙測。 此通道也會使用本機磁片儲存體，在網路中斷或高遙測磁片區期間，將專案保留在磁片上。 由於這些重試機制和本機磁片儲存體，此通道會被視為較可靠，而且建議用於所有生產案例。 此頻道是根據官方檔設定之[ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)和[ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)應用程式的預設值。 此通道已針對具有長時間執行之進程的伺服器案例進行優化。 [`Flush()`](#which-channel-should-i-use)此通道所執行的方法不是同步的。

    此頻道隨附于 ApplicationInsights. WindowsServer. TelemetryChannel NuGet 套件，並會在您使用 ApplicationInsights 或 ApplicationInsights NuGet 套件時自動取得。

## <a name="configure-a-telemetry-channel"></a>設定遙測通道

您可以設定遙測通道，方法是將它設為作用中的遙測設定。 針對 ASP.NET 應用程式，設定牽涉到將遙測通道實例設為 `TelemetryConfiguration.Active` ，或藉由修改來進行 `ApplicationInsights.config` 。 針對 ASP.NET Core 的應用程式，設定牽涉到將通道加入至相依性插入容器。

下列各節顯示 `StorageFolder` 在各種應用程式類型中設定通道之設定的範例。 `StorageFolder`只是其中一個可設定的設定。 如需完整的設定清單，請參閱本文稍後[的設定一節](telemetry-channels.md#configurable-settings-in-channels)。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>使用適用于 ASP.NET 應用程式的 ApplicationInsights.config 進行設定

下一節的[ApplicationInsights.config](configuration-with-applicationinsights-config.md)顯示 `ServerTelemetryChannel` `StorageFolder` 設定為自訂位置的通道：

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

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 應用程式的程式碼設定

下列程式碼會設定 `StorageFolder` 設定為自訂位置的 ' ServerTelemetryChannel ' 實例。 在應用程式的開頭新增此程式碼，通常是在 `Application_Start()` Global.aspx.cs 的方法中。

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

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 應用程式的程式碼設定

修改 `ConfigureServices` 類別的方法 `Startup.cs` ，如下所示：

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
> `TelemetryConfiguration.Active`ASP.NET Core 的應用程式不建議使用設定通道。

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>適用于 .NET/.NET Core 主控台應用程式的程式碼設定

針對主控台應用程式，.NET 和 .NET Core 的程式碼都相同：

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的操作詳細資料

`ServerTelemetryChannel`將抵達的專案儲存在記憶體內部緩衝區中。 每隔30秒就會將這些專案序列化、壓縮並儲存到 `Transmission` 實例中，或在已緩衝處理500專案時一次。 單一 `Transmission` 實例包含最多500個專案，代表透過單一 HTTPS 呼叫傳送至 Application Insights 服務的遙測批次。

根據預設，最多 `Transmission` 可以平行傳送10個實例。 如果遙測以較快的速率抵達，或如果網路或 Application Insights 後端變慢， `Transmission` 實例就會儲存在記憶體中。 此記憶體內部緩衝區的預設容量 `Transmission` 為 5 MB。 超過記憶體中的容量時， `Transmission` 實例會儲存在本機磁片上，最多可達 50 MB 的限制。 `Transmission`當網路有問題時，實例也會儲存在本機磁片上。 只有儲存在本機磁片上的專案會在應用程式損毀時存留下來。 每當應用程式重新開機時，就會傳送它們。

## <a name="configurable-settings-in-channels"></a>通道中的可設定設定

如需每個通道可設定之設定的完整清單，請參閱：

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

以下是最常使用的設定 `ServerTelemetryChannel` ：

1. `MaxTransmissionBufferCapacity`：通道在記憶體中用來緩衝傳輸的最大記憶體數量（以位元組為單位）。 當達到此容量時，新的專案會直接儲存到本機磁片。 預設值是 5 MB。 設定較高的值會導致磁片使用量較少，但請記住，如果應用程式當機，記憶體中的專案將會遺失。

1. `MaxTransmissionSenderCapacity`： `Transmission` 將同時傳送至 Application Insights 的實例數目上限。 預設值是 10。 這項設定可以設定為較高的數目，這在產生大量遙測資料時建議使用。 高容量通常發生于負載測試或關閉取樣時。

1. `StorageFolder`：通道用來將專案儲存至磁片的資料夾（如有需要）。 在 Windows 中，如果未明確指定其他路徑，則會使用% LOCALAPPDATA% 或% TEMP%。 在 Windows 以外的環境中，您必須指定有效的位置，否則遙測不會儲存到本機磁片。

## <a name="which-channel-should-i-use"></a>我應該使用哪一個頻道？

`ServerTelemetryChannel`對於涉及長時間執行之應用程式的大部分生產案例，建議使用。 所 `Flush()` 執行的方法 `ServerTelemetryChannel` 不是同步的，也不保證會從記憶體或磁片傳送所有暫止的專案。 如果您在應用程式即將關閉的案例中使用此通道，建議您在呼叫之後引進一些延遲 `Flush()` 。 您可能需要的確切延遲量不是可預測的。 這取決於數個因素 `Transmission` ，例如記憶體中有多少專案或實例、磁片上的數量、要傳輸到後端的數目，以及通道是否在指數輪詢後的情況下。

如果您需要執行同步排清，建議使用 `InMemoryChannel` 。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 通道是否保證遙測傳遞？ 如果不是，可能會遺失遙測的案例有哪些？

最簡短的答案是，沒有任何內建通道提供對後端的遙測傳遞交易類型保證。 `ServerTelemetryChannel`相較 `InMemoryChannel` 于可靠的傳遞，比更先進，但它也只會盡力傳送遙測資料。 在數種情況下，遙測仍然可能遺失，包括下列常見案例：

1. 當應用程式當機時，記憶體中的專案就會遺失。

1. 遙測會在網路問題延伸期間遺失。 遙測會在網路中斷時或 Application Insights 後端發生問題時，儲存到本機磁片。 不過，超過48小時的專案會被捨棄。

1. 在 Windows 中儲存遙測資料的預設磁片位置為% LOCALAPPDATA% 或% TEMP%。 這些位置通常是本機電腦。 如果應用程式從一個位置實際遷移到另一個位置，則儲存在原始位置的任何遙測都會遺失。

1. 在 Windows 的 Web Apps 中，預設磁片儲存位置為 D:\local\LocalAppData。 此位置不會保存。 它會在應用程式重新開機、相應放大和其他這類作業中被抹除，因而導致遺失儲存在該處的任何遙測。 您可以覆寫預設值，並指定儲存體到保存的位置，例如 D:\home。 不過，這類保存的位置是由遠端存放裝置提供，因此可能會變慢。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel 是否能在非 Windows 的系統上運作？

雖然其封裝和命名空間的名稱包含 "WindowsServer"，但 Windows 以外的系統也支援此通道，但有下列例外狀況。 在 Windows 以外的系統上，通道預設不會建立本機儲存體資料夾。 您必須建立本機儲存體資料夾，並設定通道來使用它。 設定本機儲存體之後，通道在所有系統上的運作方式都相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK 是否會建立暫存本機儲存體？ 資料是否會在儲存體中加密？

SDK 會在網路問題或節流期間，將遙測專案儲存在本機儲存體中。 此資料不會在本機加密。

對於 Windows 系統，SDK 會在% TEMP% 或% LOCALAPPDATA% 目錄中自動建立暫存本機資料夾，並限制只有系統管理員和目前使用者的存取權。

對於 Windows 以外的系統，SDK 不會自動建立本機儲存體，因此預設不會在本機儲存任何資料。 您可以自行建立儲存體目錄，並設定通道來使用它。 在此情況下，您必須負責確保目錄受到保護。
閱讀更多有關[資料保護和隱私權](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)的資訊。

## <a name="open-source-sdk"></a>開放原始碼 SDK
和每個 Application Insights 的 SDK 一樣，通道都是開放原始碼。 閱讀並參與[官方 GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet)存放庫中的程式碼或報告問題。

## <a name="next-steps"></a>後續步驟

* [取樣](../../azure-monitor/app/sampling.md)
* [SDK 疑難排解](../../azure-monitor/app/asp-net-troubleshoot-no-data.md)
