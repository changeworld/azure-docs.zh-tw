---
title: 監視 Windows 傳統型應用程式的使用情況和效能
description: 使用 Application Insights 分析 Windows 傳統型應用程式的使用情况和效能。
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: ddb602536e1b8bbc987c4ba366e2007163c814ec
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499183"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>監視傳統型 Windows 桌面應用程式的使用情況和效能

裝載於內部部署、Azure 中和其他雲端中的應用程式，全都可使用 Application Insights。 唯一限制是需要[允許](../../azure-monitor/app/ip-addresses.md)與 Application Insights 服務進行通訊。 若要監視通用 Windows 平台 (UWP) 應用程式，建議您使用 [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md)。

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>將遙測資料從傳統型 Windows 應用程式傳送至 Application Insights
1. 在 [Azure 入口網站](https://portal.azure.com)中，建立 [Application Insights 資源](../../azure-monitor/app/create-new-resource.md )。 
2. 取得檢測金鑰的副本。
3. 在 Visual Studio 中，編輯應用程式專案的 NuGet 封裝，並新增 Microsoft.ApplicationInsights.WindowsServer。 （或者，如果您只想要基底 API，請選擇 ApplicationInsights，而不需要標準遙測收集模組）。
4. 在程式碼中設定檢測金鑰︰
   
    您的金鑰`TelemetryConfiguration.Active.InstrumentationKey = "` ** `";`
   
    或在 ApplicationInsights.config 中設定檢測金鑰 (如果您已安裝其中一個標準遙測封裝)︰
   
    `<InstrumentationKey>`*您的金鑰*`</InstrumentationKey>` 
   
    如果使用 ApplicationInsights.config，請確定其在方案總管中的屬性已設定為 [建置動作] = [內容]、[複製到輸出目錄] = [複製] ****。
5. [使用 API](../../azure-monitor/app/api-custom-events-metrics.md) 傳送遙測。
6. 執行您的應用程式，並查看您在 Azure 入口網站中建立之資源的遙測。

## <a name="example-code"></a><a name="telemetry"></a>範例程式碼

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(System.ComponentModel.CancelEventArgs e)
        {
            e.Cancel = true;

            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="override-storage-of-computer-name"></a>覆寫電腦名稱稱的儲存空間

根據預設，此 SDK 會收集並儲存發出遙測之系統的電腦名稱稱。

Application Insights[舊版企業（每個節點）定價層](./pricing.md#legacy-enterprise-per-node-pricing-tier)會使用電腦名稱稱進行內部計費。 根據預設，如果您使用遙測初始化運算式來覆寫 `telemetry.Context.Cloud.RoleInstance` ，則會傳送個別的屬性， `ai.internal.nodeName` 其中仍然會包含電腦名稱稱值。 此值不會與您的 Application Insights 遙測一起儲存，但是會在內部內嵌使用，以允許與舊版節點型計費模型回溯相容。

如果您是在[舊版企業（每個節點）的定價層](./pricing.md#legacy-enterprise-per-node-pricing-tier)，而且只需要覆寫電腦名稱稱的儲存體，請使用遙測初始化運算式：

**撰寫自訂 TelemetryInitializer，如下所示。**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

在下列方法中，將初始化運算式具現化，並 `Program.cs` `Main()` 設定檢測金鑰：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>覆寫電腦名稱稱的傳輸

如果您不是在[舊版企業（每個節點）定價層](./pricing.md#legacy-enterprise-per-node-pricing-tier)，而且想要完全避免傳送包含電腦名稱稱的任何遙測，則必須使用遙測處理器。

### <a name="telemetry-processor"></a>遙測處理器

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

以下列方法具現化遙測處理器 `Program.cs` `Main()` 設定檢測金鑰：

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> 雖然在技術上您可以使用上述的遙測處理器，即使您是在[舊版企業（每個節點）的定價層](./pricing.md#legacy-enterprise-per-node-pricing-tier)，這會導致過度計費的可能性，因為無法適當地區別每個節點定價的節點。

## <a name="next-steps"></a>後續步驟
* [建立儀表板](../../azure-monitor/app/overview-dashboard.md)
* [診斷搜尋](../../azure-monitor/app/diagnostic-search.md)
* [探索度量](../../azure-monitor/platform/metrics-charts.md)
* [撰寫分析查詢](../log-query/log-query-overview.md)
