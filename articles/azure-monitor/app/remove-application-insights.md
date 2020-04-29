---
title: 移除 Visual Studio Azure 監視器中的 Application Insights
description: 如何在 Visual Studio 中移除適用于 ASP.NET 和 ASP.NET Core 的 Application Insights SDK。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80805101"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>如何移除 Visual Studio 中的 Application Insights

本文將說明如何在 Visual Studio 中移除 ASP.NET 和 ASP.NET Core Application Insights SDK。

若要移除 Application Insights，您必須從應用程式中的 API 移除 NuGet 套件和參考。 您可以使用 [套件管理主控台] 或 Visual Studio 中的 [管理 NuGet 解決方案] 來卸載 NuGet 套件。 下列各節將示範兩種移除 NuGet 套件的方法，以及在專案中自動新增的功能。 請務必確認已加入的檔案，以及您自己的程式碼中，已移除您對 API 進行呼叫的區域。

## <a name="uninstall-using-the-package-management-console"></a>使用套件管理主控台卸載

# <a name="net"></a>[.NET](#tab/net)

1. 若要開啟套件管理主控台，請在頂端功能表中選取 [工具] [> NuGet 套件管理員] > [套件管理員主控台]。
     
    ![在頂端功能表中，按一下 [工具] > NuGet 套件管理員 > 套件管理員主控台](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > 如果已啟用追蹤集合，您必須先卸載 ApplicationInsights。 TraceListener。 輸入`Uninstall-package Microsoft.ApplicationInsights.TraceListener` ，然後遵循下列步驟來移除 ApplicationInsights。

1. 輸入下列命令： `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    輸入命令之後，將會從專案中卸載 Application Insights 套件及其所有相依性。
    
    ![在主控台中輸入命令](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. 若要開啟套件管理主控台，請在頂端功能表中選取 [工具] [> NuGet 套件管理員] > [套件管理員主控台]。

    ![在頂端功能表中，按一下 [工具] > NuGet 套件管理員 > 套件管理員主控台](./media/remove-application-insights/package-manager.png)

1. 輸入下列命令： ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    輸入命令之後，將會從專案中卸載 Application Insights 套件及其所有相依性。

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>使用 Visual Studio NuGet UI 卸載

# <a name="net"></a>[.NET](#tab/net)

1. 在右側的 [ *方案總管* 中，以滑鼠右鍵按一下 [ **方案** ]，然後選取 [ **管理方案的 NuGet 套件**]。

    然後您會看到一個畫面，讓您編輯屬於專案一部分的所有 NuGet 套件。
    
     ![在 [方案總管中，以滑鼠右鍵按一下 [方案]，然後選取 [管理方案的 NuGet 套件]](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > 如果已啟用追蹤集合，您必須先卸載 ApplicationInsights，而不選取 [移除相依性]，然後遵循下列步驟來卸載已選取 [移除相依性] 的 ApplicationInsights。
    
1. 按一下 [ApplicationInsights] 套件。核取右側的 [ *專案* ] 旁的核取方塊，以選取所有專案。
    
1. 若要在卸載時移除所有相依性，請選取您選取專案之區段下方的 [ **選項** ] 下拉式按鈕。

    在 [ *卸載選項*] 底下，選取 [ *移除*相依性] 旁的核取方塊。

1. 選取 [解除安裝]****。
    
    ![勾選 [移除相依性]，然後卸載](./media/remove-application-insights/uninstall-framework.png)

    隨即顯示一個對話方塊，顯示要從應用程式移除的所有相依性。選取 **[確定]** 以卸載。
    
    ![勾選 [移除相依性]，然後卸載](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  卸載所有專案之後，您可能還是會在 *方案總管*中看到 "ApplicationInsights" 和 "AiHandleErrorAttribute.cs"。您可以手動刪除這兩個檔案。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. 在右側的 [ *方案總管* 中，以滑鼠右鍵按一下 [ **方案** ]，然後選取 [ **管理方案的 NuGet 套件**]。

    然後您會看到一個畫面，讓您編輯屬於專案一部分的所有 NuGet 套件。

    ![在 [方案總管中，以滑鼠右鍵按一下 [方案]，然後選取 [管理方案的 NuGet 套件]](./media/remove-application-insights/manage-nuget-core.png)

1. 按一下 [ApplicationInsights AspNetCore] 套件。 核取右側的 [*專案*] 旁的核取方塊，選取 [所有專案]，然後選取 [**卸載**]。

    ![勾選 [移除相依性]，然後卸載](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>當您新增 Application Insights 時所建立的內容

當您將 Application Insights 新增至專案時，它會建立檔案，並將程式碼新增至您的某些檔案。 僅卸載 NuGet 套件不一定會捨棄檔案和程式碼。 若要完全移除 Application Insights，您應該檢查並手動刪除新增的程式碼或檔案，以及您在專案中新增的任何 API 呼叫。

# <a name="net"></a>[.NET](#tab/net)

當您將 Application Insights 遙測新增至 Visual Studio ASP.NET 專案時，它會新增下列檔案：

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

新增下列程式碼段：

- [您的專案名稱] .csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- 封裝 .config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- 版面配置. cshtml

    如果您的專案具有配置. cshtml 檔案，則會新增下列程式碼。
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

當您將 Application Insights 遙測新增至 Visual Studio ASP.NET Core 範本專案時，它會新增下列程式碼：

- [您的專案名稱] .csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings. json：

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>後續步驟

- [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)