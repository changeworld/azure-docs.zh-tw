---
title: 使用 Azure 應用程式 Insights 設定 ASP.NET 監視 |Microsoft Docs
description: 針對裝載在內部部署環境或 Azure 的 ASP.NET 網站，設定效能、可用性及使用者行為分析工具。
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: contperfq1
ms.openlocfilehash: 970971082e684ebcb6efce07bb707ffbb20ed228
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904171"
---
# <a name="configure-application-insights-for-your-aspnet-website"></a>設定 ASP.NET 網站的 Application Insights

此程序會設定 ASP.NET web 應用程式以將遙測傳送至 [Azure Application Insights](./app-insights-overview.md) 服務。 它適用于在內部部署或雲端中裝載于您自己的 IIS 伺服器上的 ASP.NET 應用程式。 

## <a name="prerequisites"></a>Prerequisites
若要將 Application Insights 新增至您的 ASP.NET 網站，您必須：

- 使用下列工作負載安裝 [適用于 Windows](https://www.visualstudio.com/downloads/) 的最新版本 Visual Studio 2019：
    - ASP.NET 和 網頁程式開發。
    - Azure 開發

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 建立以 [工作區為基礎的 Application Insights 資源](create-workspace-resource.md)。

> [!IMPORTANT]
> 新的 Azure 區域 **需要** 使用連接字串，而不是檢測金鑰。 [連接字串](./sdk-connection-string.md?tabs=net) 會識別您想要與遙測資料相關聯的資源。 它也可讓您修改您的資源將用來做為遙測目的地的端點。 您必須複製連接字串，並將它加入應用程式的程式碼或加入環境變數。


## <a name="create-a-basic-aspnet-web-app"></a>建立基本 ASP.NET web 應用程式

1. 啟動 Visual Studio 2019。
2. 選取 [File] \(檔案\) >  [New] \(新增\) >  [Project] \(專案\)。
3. 選取 [ **ASP.NET Web 應用程式 ( .NET Framework) c #**]。
4. 輸入專案名稱 > **選取 [建立**]。
5. 選取 [ **MVC**  >  **建立**]。 

## <a name="add-application-insights-automatically"></a>自動新增 Application Insights

本節將引導您將 Application Insights 自動新增至以範本為基礎的 ASP.NET web 應用程式。 在 Visual Studio 中的 ASP.NET web 應用程式專案內：

1. 選取 [**新增 Application Insights 遙測**  >  **Application Insights Sdk (本機)**  >  **下一步**  >  **]**  >  **關閉**。
2. 開啟 `ApplicationInsights.config` 檔案。 
3. 在結束記號之前，請先 `</ApplicationInsights>` 加入一行，其中包含您 Application Insights 資源的檢測金鑰。  您可以在新建立的 Application Insights 資源的 [總覽] 窗格中，找到您在本文必要條件中所建立的檢測金鑰。

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```
4. 選取 [**專案**  >  **管理 NuGet 套件**  >  **更新**] > 將每個 `Microsoft.ApplicationInsights` NuGet 套件更新為最新的穩定版本。   
5. 選取 **IIS Express**，以執行您的應用程式。 將會啟動基本 ASP.NET 應用程式。 當您流覽網站遙測上的頁面時，將會傳送至 Application Insights。

## <a name="add-application-insights-manually"></a>手動新增 Application Insights

本節將引導您手動將 Application Insights 新增至以範本為基礎的 ASP.NET web 應用程式。 本節假設您使用以標準 ASP.NET Framework MVC web 應用程式範本為基礎的 web 應用程式。

1. 將下列 NuGet 套件及其相依性新增至專案：

    - [`Microsoft.ApplicationInsights.WindowsServer`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer)
    - [`Microsoft.ApplicationInsights.Web`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)
    - [`Microsoft.AspNet.TelemetryCorrelation`](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation)

2. 在某些情況下， `ApplicationInsights.config` 系統會自動為您建立檔案。 如果檔案已存在，請跳至步驟 #4。 如果不是自動建立的，您就必須自行建立。 在專案的相同層級上 `Global.asax` ，建立名為的新檔案 `ApplicationInsights.config`

3. 將下列 XML 設定複製到新建立的檔案：

     ```xml
     <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
      <TelemetryInitializers>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureRoleEnvironmentTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.Web.WebTestTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SyntheticUserAgentTelemetryInitializer, Microsoft.AI.Web">
          <!-- Extended list of bots:
                search|spider|crawl|Bot|Monitor|BrowserMob|BingPreview|PagePeeker|WebThumb|URL2PNG|ZooShot|GomezA|Google SketchUp|Read Later|KTXN|KHTE|Keynote|Pingdom|AlwaysOn|zao|borg|oegp|silk|Xenu|zeal|NING|htdig|lycos|slurp|teoma|voila|yahoo|Sogou|CiBra|Nutch|Java|JNLP|Daumoa|Genieo|ichiro|larbin|pompos|Scrapy|snappy|speedy|vortex|favicon|indexer|Riddler|scooter|scraper|scrubby|WhatWeb|WinHTTP|voyager|archiver|Icarus6j|mogimogi|Netvibes|altavista|charlotte|findlinks|Retreiver|TLSProber|WordPress|wsr-agent|http client|Python-urllib|AppEngine-Google|semanticdiscovery|facebookexternalhit|web/snippet|Google-HTTP-Java-Client-->
          <Filters>search|spider|crawl|Bot|Monitor|AlwaysOn</Filters>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ClientIpHeaderTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AzureAppServiceRoleNameFromHostNameHeaderInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationNameTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.OperationCorrelationTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.UserTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AuthenticatedUserIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AccountIdTelemetryInitializer, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.SessionTelemetryInitializer, Microsoft.AI.Web" />
      </TelemetryInitializers>
      <TelemetryModules>
        <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
          <ExcludeComponentCorrelationHttpHeadersOnDomains>
            <!-- 
            Requests to the following hostnames will not be modified by adding correlation headers.         
            Add entries here to exclude additional hostnames.
            NOTE: this configuration will be lost upon NuGet upgrade.
            -->
            <Add>core.windows.net</Add>
            <Add>core.chinacloudapi.cn</Add>
            <Add>core.cloudapi.de</Add>
            <Add>core.usgovcloudapi.net</Add>
          </ExcludeComponentCorrelationHttpHeadersOnDomains>
          <IncludeDiagnosticSourceActivities>
            <Add>Microsoft.Azure.EventHubs</Add>
            <Add>Microsoft.Azure.ServiceBus</Add>
          </IncludeDiagnosticSourceActivities>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
          <!--
          Use the following syntax here to collect additional performance counters:
          
          <Counters>
            <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
            ...
          </Counters>
          
          PerformanceCounter must be either \CategoryName(InstanceName)\CounterName or \CategoryName\CounterName
          
          NOTE: performance counters configuration will be lost upon NuGet upgrade.
          
          The following placeholders are supported as InstanceName:
            ??APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
            ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
            ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AppServicesHeartbeatTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.AzureInstanceMetadataTelemetryModule, Microsoft.AI.WindowsServer">
          <!--
          Remove individual fields collected here by adding them to the ApplicationInsighs.HeartbeatProvider 
          with the following syntax:
          
          <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights">
            <ExcludedHeartbeatProperties>
              <Add>osType</Add>
              <Add>location</Add>
              <Add>name</Add>
              <Add>offer</Add>
              <Add>platformFaultDomain</Add>
              <Add>platformUpdateDomain</Add>
              <Add>publisher</Add>
              <Add>sku</Add>
              <Add>version</Add>
              <Add>vmId</Add>
              <Add>vmSize</Add>
              <Add>subscriptionId</Add>
              <Add>resourceGroupName</Add>
              <Add>placementGroupId</Add>
              <Add>tags</Add>
              <Add>vmScaleSetName</Add>
            </ExcludedHeartbeatProperties>
          </Add>
                
          NOTE: exclusions will be lost upon upgrade.
          -->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule, Microsoft.AI.WindowsServer" />
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule, Microsoft.AI.WindowsServer">
          <!--</Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.FirstChanceExceptionStatisticsTelemetryModule, Microsoft.AI.WindowsServer">-->
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule, Microsoft.AI.Web">
          <Handlers>
            <!-- 
            Add entries here to filter out additional handlers: 
            
            NOTE: handler configuration will be lost upon NuGet upgrade.
            -->
            <Add>Microsoft.VisualStudio.Web.PageInspector.Runtime.Tracing.RequestDataHttpHandler</Add>
            <Add>System.Web.StaticFileHandler</Add>
            <Add>System.Web.Handlers.AssemblyResourceLoader</Add>
            <Add>System.Web.Optimization.BundleHandler</Add>
            <Add>System.Web.Script.Services.ScriptHandlerFactory</Add>
            <Add>System.Web.Handlers.TraceHandler</Add>
            <Add>System.Web.Services.Discovery.DiscoveryRequestHandler</Add>
            <Add>System.Web.HttpDebugHandler</Add>
          </Handlers>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule, Microsoft.AI.Web" />
        <Add Type="Microsoft.ApplicationInsights.Web.AspNetDiagnosticTelemetryModule, Microsoft.AI.Web" />
      </TelemetryModules>
      <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
      <TelemetrySinks>
        <Add Name="default">
          <TelemetryProcessors>
            <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryProcessor, Microsoft.AI.PerfCounterCollector" />
            <Add Type="Microsoft.ApplicationInsights.Extensibility.AutocollectedMetricsExtractor, Microsoft.ApplicationInsights" />
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <ExcludedTypes>Event</ExcludedTypes>
            </Add>
            <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
              <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
              <IncludedTypes>Event</IncludedTypes>
            </Add>
          </TelemetryProcessors>
          <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel" />
        </Add>
      </TelemetrySinks>
      <!-- 
        Learn more about Application Insights configuration with ApplicationInsights.config here: 
        http://go.microsoft.com/fwlink/?LinkID=513840
      -->
      <InstrumentationKey>your-instrumentation-key-here</InstrumentationKey>
    </ApplicationInsights>
     ```

4. 在結尾 `</ApplicationInsights>` 標記之前，新增您 Application Insights 資源的檢測金鑰。  您可以在新建立的 Application Insights 資源的 [總覽] 窗格中，找到您在本文必要條件中所建立的檢測金鑰。

    ```xml
    <InstrumentationKey>your-instrumentation-key-goes-here</InstrumentationKey>
    ```

5. 在專案的相同層級上 `ApplicationInsights.config` ，使用名為的新 c # 檔案來建立名為的資料夾 `ErrorHandler` `AiHandleErrorAttribute.cs` 。 檔案的內容看起來會像這樣：

    ```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;
    
    namespace WebApplication10.ErrorHandler //namespace will vary based on your project name
    {
        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)] 
        public class AiHandleErrorAttribute : HandleErrorAttribute
        {
            public override void OnException(ExceptionContext filterContext)
            {
                if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
                {
                    //If customError is Off, then AI HTTPModule will report the exception
                    if (filterContext.HttpContext.IsCustomErrorEnabled)
                    {   
                        var ai = new TelemetryClient();
                        ai.TrackException(filterContext.Exception);
                    } 
                }
                base.OnException(filterContext);
            }
        }
    }
    
    ```

6. 在 `App_Start` 資料夾中開啟檔案， `FilterConfig.cs` 並將其變更為符合範例：

    ```csharp
    using System.Web;
    using System.Web.Mvc;
    
    namespace WebApplication10 //Namespace will vary based on project name
    {
        public class FilterConfig
        {
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());
            }
        }
    }
    ```

7. 更新 Web.config 檔案，如下所示：

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <!--
      For more information on how to configure your ASP.NET application, please visit
      https://go.microsoft.com/fwlink/?LinkId=301880
      -->
    <configuration>
      <appSettings>
        <add key="webpages:Version" value="3.0.0.0" />
        <add key="webpages:Enabled" value="false" />
        <add key="ClientValidationEnabled" value="true" />
        <add key="UnobtrusiveJavaScriptEnabled" value="true" />
      </appSettings>
      <system.web>
        <compilation debug="true" targetFramework="4.7.2" />
        <httpRuntime targetFramework="4.7.2" />
        <httpModules>
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" />
        </httpModules>
      </system.web>
      <runtime>
        <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
          <dependentAssembly>
            <assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" />
            <bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" />
            <bindingRedirect oldVersion="0.0.0.0-12.0.0.0" newVersion="12.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Optimization" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-1.1.0.0" newVersion="1.1.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
            <bindingRedirect oldVersion="1.0.0.0-5.2.7.0" newVersion="5.2.7.0" />
          </dependentAssembly>
          <dependentAssembly>
            <assemblyIdentity name="System.Memory" publicKeyToken="cc7b13ffcd2ddd51" culture="neutral" />
            <bindingRedirect oldVersion="0.0.0.0-4.0.1.1" newVersion="4.0.1.1" />
          </dependentAssembly>
        </assemblyBinding>
      </runtime>
      <system.codedom>
        <compilers>
          <compiler language="c#;cs;csharp" extension=".cs" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.CSharpCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:1659;1699;1701" />
          <compiler language="vb;vbs;visualbasic;vbscript" extension=".vb" type="Microsoft.CodeDom.Providers.DotNetCompilerPlatform.VBCodeProvider, Microsoft.CodeDom.Providers.DotNetCompilerPlatform, Version=2.0.1.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" warningLevel="4" compilerOptions="/langversion:default /nowarn:41008 /define:_MYTYPE=\&quot;Web\&quot; /optionInfer+" />
        </compilers>
      </system.codedom>
      <system.webServer>
        <validation validateIntegratedModeConfiguration="false" />
        <modules>
          <remove name="TelemetryCorrelationHttpModule" />
          <add name="TelemetryCorrelationHttpModule" type="Microsoft.AspNet.TelemetryCorrelation.TelemetryCorrelationHttpModule, Microsoft.AspNet.TelemetryCorrelation" preCondition="managedHandler" />
          <remove name="ApplicationInsightsWebTracking" />
          <add name="ApplicationInsightsWebTracking" type="Microsoft.ApplicationInsights.Web.ApplicationInsightsHttpModule, Microsoft.AI.Web" preCondition="managedHandler" />
        </modules>
      </system.webServer>
    </configuration>
    
    ```

您現在已成功設定伺服器端應用程式監視。 如果您執行 web 應用程式，您將能夠看到遙測開始出現在 Application Insights 中。

## <a name="add-client-side-monitoring"></a>加入用戶端監視

上述各節提供自動和手動設定伺服器端監視的方法指引。 若要新增用戶端監視，您將需要使用用戶端 [JAVASCRIPT SDK](javascript.md)。 您可以藉由在頁面的 HTML 結束記號之前新增 [JavaScript 程式碼片段](javascript.md#snippet-based-setup) ，來監視任何網頁的用戶端交易 `</head>` 。 

雖然可以手動將程式碼片段加入至每個 HTML 網頁的標頭中，但建議您改為將程式碼片段加入至主頁面，這會將程式碼片段插入至網站的所有頁面。 針對本文中以範本為基礎的 ASP.NET MVC 應用程式，系統會呼叫您需要編輯的檔案， `_Layout.cshtml` 並在 [共用的 **視圖**] 下找到它  >  ****。

若要新增用戶端監視，請開啟檔案 `_Layout.cshtml` ，並遵循用戶端 JAVASCRIPT SDK 設定文章中的 [程式碼片段型安裝指示](javascript.md#snippet-based-setup) 。

## <a name="troubleshooting"></a>疑難排解

目前的 Visual Studio 2019 版本中有一個已知問題，當您將檢測金鑰儲存在使用者密碼中時，會中斷以 .NET Framework 為基礎的應用程式，而金鑰最後必須硬式編碼到 applicationinsights.config 檔中，才能解決這個錯誤。 本文的設計目的是為了避免此問題，不使用使用者秘密。  

## <a name="open-source-sdk"></a>開放原始碼 SDK

* [讀取和參與程式碼](https://github.com/microsoft/ApplicationInsights-dotnet)。

如需最新的更新和錯誤修正， [請參閱版本](./release-notes.md)資訊。

## <a name="next-steps"></a>後續步驟

* 加入綜合交易以測試您的網站是否可透過 [可用性監視](monitor-web-app-availability.md)從全球各地取得。
* [設定取樣](sampling.md) 以協助減少遙測流量和資料儲存體成本。


