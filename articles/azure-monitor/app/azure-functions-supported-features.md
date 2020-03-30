---
title: Azure 應用程式見解 - Azure 功能支援的功能
description: 支援 Azure Functions 的 Application Insights 功能
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: cf0c97fd65f9966bf42fa22e2c8f92263952cb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655645"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>適用於 Azure Functions 的 Application Insights 支援的功能

Azure Functions 提供與 Application Insights 的[內建整合](../../azure-functions/functions-monitoring.md)，可透過 ILogger 介面來使用。 以下列出目前支援的功能。 請檢閱 Azure Functions 的[開始使用](../../azure-functions/functions-monitoring.md#enable-application-insights-integration)指南。

有關函數執行階段版本的詳細資訊，請參閱[此處](../../azure-functions/functions-versions.md)。

有關相容版本的應用程式見解的詳細資訊，請參閱[依賴項](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)。

## <a name="supported-features"></a>支援的功能

| Azure Functions                       | V1                | V2 & V3   | 
|-----------------------------------    |---------------    |------------------ |
| | | | 
| **自動收集**        |                 |                   |               
| &bull; 要求                     | 是             | 是               | 
| &bull; 例外狀況                   | 是             | 是               | 
| &bull; 效能計數器         | 是             | 是               |
| &bull; 相依性                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | 是               | 
| | | | 
| **支援的功能**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | 是             | 是               | 
| &nbsp;&nbsp;&nbsp;&mdash; 保護控制通道|                 | 是               | 
| &bull; 取樣                     | 是             | 是               | 
| &bull; 活動訊號                   |                 | 是               | 
| | | | 
| **相關**                       |                   |                   |               
| &bull; ServiceBus                     |                   | 是               | 
| &bull; EventHub                       |                   | 是               | 
| | | | 
| **配置**                      |                   |                   |           
| &bull;可完整設定。<br/>如需相關指示，請參閱 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)。<br/>請參閱 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) 以了解所有選項。               |                   | 是                   | 


## <a name="performance-counters"></a>效能計數器

只有 Windows 機器才能進行效能計數器的自動收集。


## <a name="live-metrics--secure-control-channel"></a>即時計量和保護控制通道

您指定的自訂篩選條件準則會傳回給 Application Insights SDK 中的即時計量元件。 篩選條件可能會包含機密資訊，例如 customerIDs。 您可以利用祕密 API 金鑰來保護通道安全。 如需指示，請參閱[保護控制通道](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)。

## <a name="sampling"></a>取樣

Azure Functions 依預設會在其設定中啟用取樣。 如需詳細資訊，請參閱[設定取樣](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)。

如果專案依賴于應用程式見解 SDK 來執行手動遙測跟蹤，則如果採樣配置與函數的採樣配置不同，則可能會遇到奇怪的行為。 

我們建議使用與函數相同的配置。 使用**函數 v2**，您可以使用建構函式中的依賴項注入獲得相同的配置：

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
