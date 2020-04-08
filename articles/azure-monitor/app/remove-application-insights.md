---
title: 移除視覺化工作室中的應用程式見解 - Azure 監視器
description: 如何刪除應用程式見解 SDK 以用於視覺工作室中ASP.NET和ASP.NET核心。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805101"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>如何在視覺化工作室中移除應用程式見解

本文將介紹如何在可視化工作室中刪除ASP.NET和ASP.NET核心應用程式見解 SDK。

要移除應用程式見解,您需要從應用程式中的 API 中刪除 NuGet 包和引用。 您可以使用套件管理主控台或在可視化工作室中管理 NuGet 解決方案卸載 NuGet 包。 以下各節將介紹刪除 NuGet 包的兩種方法以及專案中自動添加的內容。 請務必確認添加的檔,並刪除在您自己的代碼中調用 API 的區域。

## <a name="uninstall-using-the-package-management-console"></a>使用套件管理主控台卸載

# <a name="net"></a>[.NET](#tab/net)

1. 要打開包管理主控台,請在頂部功能表中選擇"工具> NuGet 包管理器>包管理器主控台。
     
    ![在頂部選單中按下「工具> NuGet」包管理器>包管理員主控台](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > 如果啟用了跟蹤收集,您需要首先卸載 Microsoft.應用程式見解.TraceListener。 然後`Uninstall-package Microsoft.ApplicationInsights.TraceListener`按照以下步驟刪除 Microsoft.應用程式見解.Web。

1. 輸入以下指令: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    輸入命令後,應用程式見解包及其所有依賴項將從專案卸載。
    
    ![在控制中輸入命令](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. 要打開包管理主控台,請在頂部功能表中選擇"工具> NuGet 包管理器>包管理器主控台。

    ![在頂部選單中按下「工具> NuGet」包管理器>包管理員主控台](./media/remove-application-insights/package-manager.png)

1. 輸入以下指令: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    輸入命令後,應用程式見解包及其所有依賴項將從專案卸載。

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>使用視覺化工作室 NuGet UI 卸載

# <a name="net"></a>[.NET](#tab/net)

1. 在右側 *的解決方案資源管理器* 中,右鍵單擊 **"解決方案** "並選擇 **"管理解決方案 NuGet 包**"。

    然後,您將看到一個螢幕,允許您編輯屬於專案的所有 NuGet 包。
    
     ![右鍵單擊解決方案,在解決方案資源管理器中,然後選擇"管理解決方案的 NuGet 包"](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > 如果啟用了跟蹤收集,則需要首先卸載 Microsoft.Application Insights.TraceListener,而無需刪除選定的依賴項,然後按照以下步驟卸載 Microsoft.Application Insights.Web,並選擇了刪除依賴項。
    
1. 點擊「微軟.應用程式洞察.Web」包。在右側,選中 *「專案」* 旁邊的複選框以選擇所有專案。
    
1. 要在卸載時刪除所有依賴項,請選擇所選專案部分下方的 **「選項** 」下拉按鈕。

    在 *「卸載選項」* 下,選擇 *「刪除依賴項*」旁邊的複選框。

1. 選取 [解除安裝]****。
    
    ![檢查刪除依賴項,然後卸載](./media/remove-application-insights/uninstall-framework.png)

    將顯示一個對話框,顯示要從應用程式中刪除的所有依賴項。選擇 **「確定」** 以卸載。
    
    ![檢查刪除依賴項,然後卸載](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  卸載所有內容後,您仍可能在 *解決方案資源管理器*中看到"應用程式見解.config"和"AiHandleErrorAttribute.cs"。您可以手動刪除這兩個檔。

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. 在右側 *的解決方案資源管理器* 中,右鍵單擊 **"解決方案** "並選擇 **"管理解決方案 NuGet 包**"。

    然後,您將看到一個螢幕,允許您編輯屬於專案的所有 NuGet 包。

    ![右鍵單擊解決方案,在解決方案資源管理器中,然後選擇"管理解決方案的 NuGet 包"](./media/remove-application-insights/manage-nuget-core.png)

1. 點擊「微軟.應用程式洞察.AspNetCore」包。 在右側,選中 *「專案」* 旁邊的複選框以選擇所有專案,然後選擇 **「卸載**」。

    ![檢查刪除依賴項,然後卸載](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>新增應用程式的程式來建立內容

將應用程式見解添加到專案中時,它會創建檔案並將代碼添加到某些檔中。 僅卸載 NuGet 包並不總是會丟棄檔和代碼。 要完全刪除應用程式見解,應檢查和手動刪除添加的代碼或檔以及您在專案中添加的任何 API 呼叫。

# <a name="net"></a>[.NET](#tab/net)

將應用程式見解遙測添加到可視化工作室ASP.NET專案中,它會添加以下檔:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

新增以下代碼:

- 【您的項目名稱】csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- 包裝. 設定

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

- 佈局.cshtml

    如果您的專案具有 Layout.cshtml 檔,則添加以下代碼。
    
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

將應用程式見解遙測添加到 Visual Studio ASP.NET核心範本項目時,它會添加以下代碼:

- 【您的項目名稱】csproj

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

- 套用設定.json:

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