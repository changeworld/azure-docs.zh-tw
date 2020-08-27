---
title: Azure 應用程式 Insights 中的遙測通道 |Microsoft Docs
description: 如何在適用于 .NET 和 .NET Core 的 Azure 應用程式 Insights Sdk 中自訂遙測通道。
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 41d2feefc5af1e795520d9b3d90809e625502fa6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918395"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights 中的遙測通道

遙測通道是 [Azure 應用程式 Insights sdk](./app-insights-overview.md)不可或缺的一部分。 它們管理將遙測資料緩衝處理和傳輸至 Application Insights 服務。 .NET 和 .NET Core 版本的 Sdk 有兩個內建的遙測通道： `InMemoryChannel` 和 `ServerTelemetryChannel` 。 本文將詳細說明每個通道，包括如何自訂通道行為。

## <a name="what-are-telemetry-channels"></a>什麼是遙測通道？

遙測通道負責緩衝處理遙測專案，並將它們傳送至 Application Insights 服務，以供其儲存以供查詢及分析。 遙測通道是實介面的任何類別 [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) 。

在 `Send(ITelemetry item)` 呼叫所有遙測初始化運算式和遙測處理器之後，會呼叫遙測通道的方法。 因此，遙測處理器捨棄的任何專案都不會到達通道。 `Send()` 通常不會立即將專案傳送至後端。 一般而言，它會在記憶體中將它們緩衝，並以批次方式傳送它們，以進行有效率的傳輸。

[即時計量資料流](live-stream.md) 也有自訂通道，可提供遙測的即時串流。 此通道與一般遙測通道無關，而這份檔並不適用。

## <a name="built-in-telemetry-channels"></a>內建遙測通道

Application Insights .NET 和 .NET Core Sdk 隨附兩個內建的通道：

* `InMemoryChannel`：輕量通道，會緩衝傳送記憶體中的專案，直到它們傳送為止。 專案會在記憶體中緩衝處理，並每隔30秒排清一次，或是每隔500個專案緩衝。 此通道提供的可靠性保證較低，因為它不會在失敗後重試傳送遙測。 此通道也不會將專案保留在磁片上，因此在應用程式關閉時，任何未傳送的專案都會永久遺失 (正常或不) 。 此通道會實 `Flush()` 用來強制對任何記憶體中的遙測專案進行同步的方法。 此通道適用于需要執行同步排清的短時間執行的應用程式。

    此通道是較大的 ApplicationInsights NuGet 套件的一部分，而且是 SDK 在未設定任何其他專案時所使用的預設通道。

* `ServerTelemetryChannel`：具有重試原則的更高階通道，以及在本機磁片上儲存資料的功能。 如果發生暫時性錯誤，此通道會重試傳送遙測。 此通道也會在網路中斷或高遙測磁片區期間，使用本機磁片儲存體來保留磁片上的專案。 由於這些重試機制和本機磁片儲存體，此通道會被視為更可靠，而且建議用於所有的生產案例。 此通道是根據官方檔設定的 [ASP.NET](./asp-net.md) 和 [ASP.NET Core](./asp-net-core.md) 應用程式的預設值。 此通道已針對具有長時間執行之進程的伺服器案例進行優化。 [`Flush()`](#which-channel-should-i-use)此通道所執行的方法不是同步的。

    此通道隨附于 ApplicationInsights. WindowsServer. TelemetryChannel NuGet 套件，並會在您使用 ApplicationInsights. Web 或 ApplicationInsights NuGet 套件時自動取得。

## <a name="configure-a-telemetry-channel"></a>設定遙測通道

您可以設定遙測通道，將其設定為作用中的遙測設定。 針對 ASP.NET 應用程式，設定牽涉到將遙測通道實例設為 `TelemetryConfiguration.Active` 或修改 `ApplicationInsights.config` 。 對於 ASP.NET Core 應用程式，設定牽涉到將通道新增至相依性插入容器。

下列各節顯示 `StorageFolder` 在各種應用程式類型中設定通道設定的範例。 `StorageFolder` 只是其中一項可設定的設定。 如需完整的設定清單，請參閱本文稍後 [的設定一節](#configurable-settings-in-channels) 。

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>使用 ApplicationInsights.config ASP.NET 應用程式進行設定

[ApplicationInsights.config](configuration-with-applicationinsights-config.md)的下一節顯示 `ServerTelemetryChannel` `StorageFolder` 設定為自訂位置的通道：

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

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 應用程式程式碼中的設定

下列程式碼會設定將 `StorageFolder` 設定為自訂位置的 ' ServerTelemetryChannel ' 實例。 在應用程式的開頭新增此程式碼，通常是在 `Application_Start()` Global.aspx.cs 的方法中。

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

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core 主控台應用程式程式碼中的設定

針對主控台應用程式，.NET 和 .NET Core 的程式碼都相同：

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel 的操作詳細資料

`ServerTelemetryChannel` 將到達的專案儲存在記憶體中的緩衝區。 每隔30秒就會將專案序列化、壓縮並儲存到 `Transmission` 實例一次，或在500專案經過緩衝處理時儲存。 單一 `Transmission` 實例最多包含500個專案，代表透過單一 HTTPS 呼叫傳送給 Application Insights 服務的遙測批次。

根據預設，最多 `Transmission` 可以平行傳送10個實例。 如果遙測以較快的速率抵達，或如果網路或 Application Insights 後端緩慢，則 `Transmission` 實例會儲存在記憶體中。 此記憶體中緩衝區的預設容量 `Transmission` 為 5 MB。 超過記憶體容量時， `Transmission` 實例會儲存在本機磁片上，最多可達 50 MB 的限制。 `Transmission` 當有網路問題時，實例也會儲存在本機磁片上。 只有儲存在本機磁片上的專案會在應用程式損毀時繼續。 每當應用程式重新開機時，就會傳送它們。

## <a name="configurable-settings-in-channels"></a>通道中可設定的設定

如需每個通道可設定之設定的完整清單，請參閱：

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

以下是最常使用的設定 `ServerTelemetryChannel` ：

1. `MaxTransmissionBufferCapacity`：通道用來緩衝記憶體中傳輸的最大記憶體數量（以位元組為單位）。 達到此容量時，會將新的專案直接儲存到本機磁片。 預設值是 5 MB。 設定較高的值會導致磁片使用量較少，但請記住，如果應用程式損毀，記憶體中的專案將會遺失。

1. `MaxTransmissionSenderCapacity`： `Transmission` 將同時傳送給 Application Insights 的實例數目上限。 預設值是 10。 這項設定可設定為較高的數位，如果產生大量遙測，則建議使用此設定。 高容量通常會在負載測試期間，或在關閉取樣時發生。

1. `StorageFolder`：通道用來將專案儲存至磁片的資料夾（如有需要）。 在 Windows 中，如果未明確指定其他路徑，則會使用% LOCALAPPDATA% 或% TEMP%。 在 Windows 以外的環境中，您必須指定有效的位置，否則遙測不會儲存到本機磁片。

## <a name="which-channel-should-i-use"></a>我應該使用哪一個通道？

`ServerTelemetryChannel` 建議在大部分涉及長時間執行之應用程式的生產案例中使用。 `Flush()`由所執行的方法 `ServerTelemetryChannel` 不是同步的，也不保證從記憶體或磁片傳送所有擱置專案。 如果您在應用程式即將關閉的情況下使用此通道，我們建議您在呼叫之後引進一些延遲 `Flush()` 。 您可能需要的確切延遲量不是可預測的。 這取決於各種因素，例如記憶體中有多少個專案或 `Transmission` 實例、磁片上有多少個、哪些磁片已傳輸到後端，以及通道是否處於指數的關機案例中。

如果您需要執行同步排清，建議您使用 `InMemoryChannel` 。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 通道是否保證遙測傳遞？ 如果沒有，遙測可能遺失的情況為何？

簡短的答案是，沒有任何內建通道提供將遙測傳遞給後端的交易類型保證。 `ServerTelemetryChannel` 比起提供 `InMemoryChannel` 可靠的傳遞更加先進，但它也只會嘗試傳送遙測。 在幾種情況下，遙測仍可能遺失，包括下列常見案例：

1. 當應用程式損毀時，記憶體中的專案就會遺失。

1. 遙測會在網路問題的延伸期間遺失。 遙測會在網路中斷期間或 Application Insights 後端發生問題時儲存至本機磁片。 不過，會捨棄超過48小時的專案。

1. 在 Windows 中儲存遙測的預設磁片位置為% LOCALAPPDATA% 或% TEMP%。 這些位置通常是電腦的本機位置。 如果應用程式從某個位置實際遷移到另一個位置，則儲存在原始位置的任何遙測都會遺失。

1. 在 Windows Web Apps 中，預設磁片儲存位置為 D:\local\LocalAppData。 此位置不會保存。 它會在應用程式重新開機、相應放大及其他這類作業中被抹除，導致遺失任何儲存在該處的遙測。 您可以覆寫預設值，並指定儲存體到保存的位置，例如 D:\home。 不過，這類保存的位置是由遠端存放服務所提供，因此可能會很慢。

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel 是否適用于 Windows 以外的系統？

雖然其封裝和命名空間的名稱包含 "WindowsServer"，但 Windows 以外的系統支援此通道，但有下列例外狀況。 在 Windows 以外的系統上，通道預設不會建立本機儲存體資料夾。 您必須建立本機儲存體資料夾，並設定通道以使用它。 設定本機儲存體之後，通道在所有系統上的運作方式都相同。

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK 是否會建立暫存本機儲存體？ 資料是否會在儲存體加密？

SDK 會在網路問題或節流期間，將遙測專案儲存在本機儲存體中。 此資料不會在本機加密。

若為 Windows 系統，SDK 會在% TEMP% 或% LOCALAPPDATA% 目錄中自動建立暫存本機資料夾，並限制只能存取系統管理員和目前的使用者。

針對 Windows 以外的系統，SDK 不會自動建立任何本機儲存體，因此預設不會在本機儲存任何資料。 您可以自行建立儲存體目錄，並設定通道以使用它。 在此情況下，您必須負責確保目錄受到保護。
閱讀有關 [資料保護和隱私權](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)的詳細資訊。

## <a name="open-source-sdk"></a>開放原始碼 SDK
如同 Application Insights 的每個 SDK，通道都是開放原始碼。 閱讀和參與 [官方 GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet)存放庫中的程式碼或回報問題。

## <a name="next-steps"></a>後續步驟

* [取樣](./sampling.md)
* [SDK 疑難排解](./asp-net-troubleshoot-no-data.md)

