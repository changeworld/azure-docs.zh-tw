---
title: 監視 Windows 傳統型應用程式的使用情況和效能
description: 使用 Application Insights 分析 Windows 傳統型應用程式的使用情况和效能。
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 8234b9ba2c92fc64cfa8f598db99954e00caab45
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670826"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>監視傳統型 Windows 桌面應用程式的使用情況和效能

裝載於內部部署、Azure 中和其他雲端中的應用程式，全都可使用 Application Insights。 唯一限制是需要[允許](../../azure-monitor/app/ip-addresses.md)與 Application Insights 服務進行通訊。 若要監視通用 Windows 平台 (UWP) 應用程式，建議您使用 [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md)。

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>將遙測資料從傳統型 Windows 應用程式傳送至 Application Insights
1. 在 [Azure 入口網站](https://portal.azure.com)中，建立 [Application Insights 資源](../../azure-monitor/app/create-new-resource.md )。 針對應用程式類型，選擇 ASP.NET 應用程式。
2. 取得檢測金鑰的副本。 在您剛才建立的新資源之 [Essentials] 下拉式清單中尋找金鑰。 
3. 在 Visual Studio 中，編輯應用程式專案的 NuGet 封裝，並新增 Microsoft.ApplicationInsights.WindowsServer。 (或選擇 Microsoft.ApplicationInsights，如果您只想要單純的 API，而不需要標準遙測集合模組。)
4. 在程式碼中設定檢測金鑰︰
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*您的金鑰*`";`
   
    或在 ApplicationInsights.config 中設定檢測金鑰 (如果您已安裝其中一個標準遙測封裝)︰
   
    `<InstrumentationKey>`您的金鑰`</InstrumentationKey>` 
   
    如果使用 ApplicationInsights.config，請確定其在方案總管中的屬性已設定為 [建置動作] = [內容]、[複製到輸出目錄] = [複製]。
5. [使用 API](../../azure-monitor/app/api-custom-events-metrics.md) 傳送遙測。
6. 執行您的應用程式，並查看您在 Azure 入口網站中建立之資源的遙測。

## <a name="telemetry"></a>程式碼範例
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

根據預設，此 SDK 會收集並儲存發出遙測之系統的電腦名稱稱。 若要覆寫集合，您必須使用遙測初始化運算式：

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
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                //set custom role name here, you can pass an empty string if needed.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```
在下列 `Program.cs` 中，將初始化運算式具現化 `Main()` 方法設定檢測金鑰：

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
        }
```

## <a name="next-steps"></a>後續步驟
* [建立儀表板](../../azure-monitor/app/overview-dashboard.md)
* [診斷搜尋](../../azure-monitor/app/diagnostic-search.md)
* [探索度量](../../azure-monitor/app/metrics-explorer.md)
* [撰寫分析查詢](../../azure-monitor/app/analytics.md)

