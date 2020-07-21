---
title: 用來追蹤活動的使用者內容識別碼 - Azure Application Insights
description: 追蹤使用者如何在您的服務移動，方法是為每個使用者指派 Application Insights 中唯一的持續性識別碼字串。
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: a3a59b6658e6ea0229f65492b20b5f5f2882d12a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499234"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>傳送使用者內容識別碼以啟用 Azure Application Insights 中的使用體驗

## <a name="tracking-users"></a>追蹤使用者

Application Insights 可讓您透過一組產品使用量工具來監控並追蹤使用者：

- [使用者、工作階段、事件](./usage-segmentation.md)
- [漏斗圖](./usage-funnels.md)
- [保留](./usage-retention.md)世代
- [活頁簿](../platform/workbooks-overview.md)

若要追蹤使用者在一段時間內所做的行為，Application Insights 需要每個使用者或工作階段的識別碼。 包括下列每個自訂事件或頁面檢視畫面中的識別碼。

- 使用者、漏斗圖、保留期和世代：包含使用者識別碼。
- 工作階段：包含工作階段識別碼。

> [!NOTE]
> 這是概述使用 Application Insights 追蹤使用者活動之手動步驟的進階文章。 在 Web 應用程式很多的情況下，**可能不必遵循這些步驟**，原因是預設伺服器端搭配[用戶端/伺服器端 JavaScript SDK](../../azure-monitor/app/website-monitoring.md ) 通常就足以自動追蹤使用者活動。 除了伺服器端 SDK 之外，若您尚未設定[用戶端監視](../../azure-monitor/app/website-monitoring.md )，請先予以設定並測試，確認使用者行為分析工具是否如預期般執行。

## <a name="choosing-user-ids"></a>選擇使用者識別碼

使用者識別碼應該在使用者工作階段期間持續存在，以追蹤使用者在一段時間內的行為。 有多種方法可持續使用識別碼。

- 您的服務中已有使用者定義。
- 如果服務可存取瀏覽器，該服務可利用識別碼將 cookie 傳遞給瀏覽器。 只要 cookie 保留在使用者的瀏覽器中，識別碼就會持續存在。
- 如有必要，您可以每個工作階段都使用新的識別碼，但與使用者相關的結果會受到限制。 例如，您將無法看到使用者的行為如何隨著時間而變化。

識別碼應該是 Guid 或足夠複雜的另一個字串，以專門識別每個使用者。 例如，它可能是一個長的隨機數字。

如果識別碼包含使用者的個人識別資訊，則該值不適合傳送至 Application Insights 做為使用者識別碼。 您可以傳送此類識別碼做為[已驗證的使用者識別碼](./api-custom-events-metrics.md#authenticated-users)，但不符合使用案例的使用者識別碼需求。

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>ASP.NET 應用程式：在 ITelemetryInitializer 中設定使用者內容

建立遙測初始設定式，詳細說明請見[這裡](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)。 透過要求遙測傳遞工作階段識別碼，並設定 Context.User.Id 和 Context.Session.Id。

此範例會將使用者識別碼設定為在工作階段之後到期的識別碼。 如果可能，請使用工作階段期間持續存在的使用者識別碼。

### <a name="telemetry-initializer"></a>遙測初始設定式

```csharp
using System;
using System.Web;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that sets the user ID.
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟

- 若要啟用使用體驗，請開始傳送[「自訂事件」](./api-custom-events-metrics.md#trackevent) 或 [「頁面檢視」](./api-custom-events-metrics.md#page-views)。
- 如果您已傳送自訂事件或頁面檢視，請探索「使用量工具」，以了解使用者如何使用您的服務。
    - [使用量概觀](usage-overview.md)
    - [使用者、工作階段和事件](usage-segmentation.md)
    - [漏斗圖](usage-funnels.md)
    - [保留](usage-retention.md)
    - [活頁簿](../../azure-monitor/platform/workbooks-overview.md)
