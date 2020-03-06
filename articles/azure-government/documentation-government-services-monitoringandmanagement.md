---
title: Azure Government 監視和管理 | Microsoft Docs
description: 這為 Azure Government 的開發應用程式提供功能和指引的比較。
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: b6f395964c286aca1bc8a1c190edeea00ba6e15c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361847"
---
# <a name="azure-government-monitoring--management"></a>Azure Government 監視和管理
此文章概述 Azure Government 環境的監視和管理服務變化以及考量。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>建議程式
Advisor 已在 Azure Government 中正式推出。

如需詳細資訊，請參閱[Advisor 公開檔](../advisor/advisor-overview.md)。

### <a name="variations"></a>變化
Azure Government 目前無法使用下列 Advisor 建議：

* 高可用性
  * 將 VPN 閘道設定為主動-主動以提供連線恢復功能
  * 建立在 Azure 問題影響您時所要通知的 Azure 服務健康狀態警示
  * 設定流量管理員端點以供復原
  * 針對您的 Azure 儲存體帳戶使用虛刪除
* 效能
  * 改善 App Service 的效能和可靠性
  * 減少 DNS 在流量管理員設定檔上的存留時間，以更快速容錯移轉至健康情況良好的端點上
  * 改善 SQL 資料倉儲效能
  * 使用進階儲存體
  * 將您的儲存體帳戶遷移至 Azure Resource Manager
* 成本
  * 購買保留的虛擬機器實例以節省隨用隨付費用的費用
  * 排除未布建的 ExpressRoute 線路
  * 刪除或重新設定閒置的虛擬網路閘道

這項計算用來建議您應該適當調整或關閉使用量過低的虛擬機器，如下 Azure Government：

Advisor 會監視您的虛擬機器使用量7天，並找出低使用率的虛擬機器。 如果虛擬機器的 CPU 使用率為5% 或更低，而其網路使用率低於2%，或目前的工作負載可由較小的虛擬機器大小容納，則會視為低使用率。 如果您想要更積極地找出使用量過低的虛擬機器，您可以根據每個訂用帳戶來調整 CPU 使用率規則。

## <a name="automation"></a>自動化
自動化已在 Azure Government 中正式推出。

如需詳細資訊，請參閱[自動化公開文件](../automation/automation-intro.md)。

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate 已在 Azure Government 中正式推出。

如需詳細資訊，請參閱[Azure Migrate 檔](../migrate/migrate-overview.md)。

### <a name="variations"></a>變化
Azure Government 目前無法使用下列 Azure Migrate 功能：

* 相依性視覺效果功能無法在 Azure Government 中使用，因為 Azure Migrate 依存于目前無法在 Azure Government 中使用的相依性視覺效果服務對應。
* 您只能建立 Azure Government 的評量做為目的地區域，並使用 Azure Government 供應專案。

## <a name="backup"></a>Backup
備份已在 Azure Government 中正式推出。

如需詳細資訊，請參閱 [Azure Government 備份](documentation-government-services-backup.md)。

## <a name="policy"></a>原則
原則在 Azure Government 中已正式推出。

如需詳細資訊，請參閱 [Azure 原則](../governance/policy/overview.md)。

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery 已在 Azure Government 中正式推出。

如需詳細資訊，請參閱[Site Recovery 商業檔](../site-recovery/site-recovery-overview.md)。

### <a name="variations"></a>變化
下列 Site Recovery 功能目前無法在 Azure Government 中使用：
* 電子郵件通知

| Site Recovery | 傳統 | Resource Manager |
| --- | --- | --- |
| VMware/實體  | GA | GA |
| Hyper-V | GA | GA |
| 網站對網站 | GA | GA |

下列 Site Recovery URL 在 Azure Government 中不同：

| Azure 公用 | Azure Government | 注意 |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*.hypervrecoverymanager.windowsazure.us | Site Recovery 服務的存取權 |
| \*.backup.windowsazure.com  | \*.backup.windowsazure.us | 保護服務的存取權 |
| \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | 用於儲存 VM 快照集 |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | 下載 MySQL |

## <a name="monitor"></a>監視
Azure 監視器已在 Azure Government 中正式推出。

如需詳細資訊，請參閱[監視商業檔](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)。

### <a name="variations"></a>變化
下列各節詳細說明 Azure Government 中 Azure 監視器功能的差異和因應措施：

#### <a name="action-groups"></a>動作群組
動作群組已在 Azure Government 中正式運作，不會與商業 Azure 有任何差異。   

#### <a name="activity-log-alerts"></a>活動記錄警示
活動記錄警示已在 Azure Government 中正式運作，不會與商業 Azure 有任何差異。

#### <a name="alerts-experience"></a>警示體驗
整合警示 UI 體驗適用于 Azure Government 中的計量和記錄警示。

#### <a name="autoscale"></a>Autoscale
自動調整功能已在 Azure Government 中正式推出。

如果您使用 PowerShell/ARM/REST 呼叫來指定設定，請將自動調整的「位置」設定為 "USGov 佛吉尼亞" 或 "USGov 愛荷華州"。 自動調整的目標資源可以存在於任何區域中。 設定的範例如下：

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

如果您想要在資源上執行自動調整，請使用 PowerShell/ARM/Rest 呼叫來指定設定。

如需使用 PowerShell 的詳細資訊，請參閱[公開檔](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings)。

#### <a name="metrics"></a>度量
計量在 Azure Government 中已正式推出。 不過，只有透過 REST API 才支援多維度計量。 在 Azure Government 入口網站中，[顯示多維度計量](../azure-monitor/platform/metrics-charts.md)的功能處於預覽狀態。

#### <a name="metric-alerts"></a>計量警示
第一代的計量警示已在 Azure Government 和商業 Azure 中正式推出。 第一代稱為「*警示（傳統）」* 。 第二個產生的計量警示（也稱為[統一的警示體驗](../azure-monitor/platform/alerts-overview.md)）現在也可供使用，但[相較于公用雲端](../azure-monitor/platform/alerts-metric-near-real-time.md)，有一組精簡的資源提供者。 會隨著時間增加。 

第二個產生警示體驗目前支援的資源包括：
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft.EventGrid/domains
- Microsoft.EventGrid/topics
- Microsoft.EventHub/clusters
- Microsoft.EventHub/namespaces
- Microsoft Insights/元件
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft 網路/natGateways
- Microsoft 網路/privateEndpoints
- Microsoft 網路/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft.Relay/namespaces
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft.Storage/storageAccounts/fileServices
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

您仍然可以針對第二個產生警示中尚未提供的資源使用[傳統警示](../azure-monitor/platform/alerts-classic.overview.md)。 

使用 PowerShell/ARM/Rest 呼叫來建立計量警示時，您必須將計量警示的「位置」設定為 "USGov 佛吉尼亞" 或 "USGov 愛荷華州"。 設定的範例如下：

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

如需使用 PowerShell 的詳細資訊，請參閱[公開檔](../azure-monitor/platform/powershell-quickstart-samples.md)。

## <a name="application-insights"></a>Application Insights

> [!NOTE]
> **目前不支援**Azure App 服務的無程式碼代理程式/擴充功能監視。 一旦推出此功能，將會更新本文。

本節說明在 Azure Government 中使用 Application Insights 所需的補充設定。 若要深入瞭解 Azure 監視器和 Application Insights 簽出[完整的檔](https://docs.microsoft.com/azure/azure-monitor/overview)。

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>使用 Visual Studio 啟用 ASP.NET & ASP.NET Core 的 Application Insights

目前對於 Azure Government 客戶而言，在 Visual Studio 中透過傳統的 [**新增應用程式深入解析遙測**] 按鈕啟用 Application Insights 的唯一方式，需要進行少量的手動因應措施。 遇到相關問題的客戶可能會看到錯誤訊息，例如「沒有_與此帳戶相關聯的 Azure 訂用帳戶_」或「_選取的訂_用帳戶不支援 Application Insights 即使 `microsoft.insights` 資源提供者的訂閱狀態為 [已註冊] 也一樣。 若要減少此問題，您必須執行下列步驟：

1. 切換 Visual Studio 以[Azure Government 雲端為目標](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs)。

2. 建立（或現有設定） AzureGraphApiVersion 的使用者環境變數，如下所示：（若要建立使用者環境變數，請移至 **控制台**， > **系統** ** >  > ** **advanced** > **環境變數**。

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. 根據您的專案類型，為[ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig)或[ASP.NET Core](#aspnet-core)進行適當的 Application Insights SDK 端點修改。

### <a name="snapshot-debugger"></a>快照集偵錯工具

快照偵錯工具現在可供 Azure Government 客戶使用。 若要使用快照偵錯工具唯一的必要條件是確保您使用的是[Snapshot Collector 版本 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403)或更新版本。 然後只需遵循標準的[快照偵錯工具檔](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger)。

### <a name="sdk-endpoint-modifications"></a>SDK 端點修改

為了將資料從 Application Insights 傳送至 Azure Government 區域，您必須修改 Application Insights Sdk 所使用的預設端點位址。 每個 SDK 都需要稍微不同的修改。

### <a name="net-with-applicationinsightsconfig"></a>具有 applicationinsights 的 .NET

> [!NOTE]
> 每當執行 SDK 升級時，就會自動覆寫 applicationinsights。 執行 SDK 升級之後，請務必重新輸入區域特定的端點值。

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>ASP.NET Core

如下所示修改專案中的 appsettings，以調整主要端點：

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

即時計量和設定檔查詢端點的值只能透過程式碼來設定。 若要透過程式碼覆寫所有端點值的預設值，請在 `Startup.cs` 檔案的 `ConfigureServices` 方法中進行下列變更：

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure Functions

請在您的函式專案中安裝下列套件：

- ApplicationInsights 版本2.10。0
- ApplicationInsights. Microsoft.applicationinsights.perfcountercollector 版本2.10。0
- ApplicationInsights. WindowsServer. TelemetryChannel version 2.10。0

此外，也會新增（或修改）函數應用程式的啟動程式碼：

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

修改 applicationinsights，以變更預設的端點位址。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

修改 `application.properties` 檔案並新增：

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

您也可以透過環境變數來設定端點：

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>防火牆例外狀況

Azure 應用程式 Insights 服務會使用多個 IP 位址。 如果您所監視的應用程式裝載於防火牆後面，您可能需要知道這些位址。

> [!NOTE]
> 雖然這些位址是靜態的，但可能隨時需要變更。 所有 Application Insights 流量代表輸出流量，但可用性監視和 webhook 除外，這需要輸入防火牆規則。

### <a name="outgoing-ports"></a>連出連接埠
您需要在伺服器防火牆開啟某些連出連接埠，以允許 Application Insights SDK 和/或狀態監視器將資料傳送至入口網站：

| 目的 | URL | IP | 連接埠 |
| --- | --- | --- | --- |
| 遙測 | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Azure 監視器記錄
Azure 監視器記錄在 Azure Government 中已正式提供。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>變化

* Azure Government 中提供的解決方案包括：
  * [網路效能監控（NPM）](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) -NPM 是適用于公用和混合式雲端環境的雲端式網路監視解決方案。 組織會使用 NPM 來監視內部部署和雲端環境之間的網路可用性。  端點監視-NPM 的 subcapability，可監視應用程式的網路連線能力。

Azure Government 目前無法使用下列 Azure 監視器記錄功能和解決方案。

* 在 Microsoft Azure 中預覽的解決方案包括︰
  * 服務對應
  * Windows 10 Upgrade Analytics 解決方案
  * Application Insights 解決方案
  * Azure 網路安全性群組分析解決方案
  * Azure 自動化分析解決方案
  * Key Vault 分析解決方案
* 需要更新內部部署軟體的解決方案和功能包括：
  * Surface Hub 解決方案
* 全域 Azure 中處於預覽狀態的功能，包括：
  * 將資料匯出至 Power BI
* Azure 度量和 Azure 診斷

Azure Government 中 Azure 監視器記錄的 Url 不同：

| Azure 公用 | Azure Government | 注意 |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Log Analytics 工作區入口網站 |
| *workspaceId*.ods.opinsights.azure.com |*workspaceId*.ods.opinsights.azure.us |[資料收集器 API](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*.ods.opinsights.azure.us |代理程式通訊 - [設定防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*.oms.opinsights.azure.us |代理程式通訊 - [設定防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*.blob.core.usgovcloudapi.net |代理程式通訊 - [設定防火牆設定](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Advanced Analytics 入口網站-進行[防火牆設定](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Advanced Analytics 入口網站-進行[防火牆設定](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Advanced Analytics 入口網站-進行[防火牆設定](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*。 azure-automation.us |Azure 自動化進行[防火牆設定](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| N/A | *. usgovtrafficmanager.net | Azure 流量管理員-[正在進行防火牆設定](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

下列 Azure 監視器記錄功能在 Azure Government 中的行為不同：

* 若要將您的 System Center Operations Manager 管理群組連線到 Azure 監視器記錄檔，您必須下載並匯入更新的管理元件。
  + System Center Operations Manager 2016
    1. 安裝[適用於 System Center Operations Manager 2016 的更新彙總套件 2](https://support.microsoft.com/help/3209591)。
    2. 將包含在更新彙總套件 2 中的管理組件匯入至 Operations Manager。 如需如何從磁碟匯入管理組件的相關資訊，請參閱[如何匯入 Operations Manager 管理組件](https://technet.microsoft.com/library/hh212691.aspx)。
    3. 若要將 Operations Manager 連線至 Azure 監視器記錄，請依照[將 Operations Manager 連接到 Azure 監視器記錄](../azure-monitor/platform/om-agents.md)中的步驟進行。
  + System Center Operations Manager 2012 R2 UR3 (或更新版本) / Operations Manager 2012 SP1 UR7 (或更新版本)
    1. 下載及儲存[更新的管理組件](https://go.microsoft.com/fwlink/?LinkId=828749)。
    2. 將下載的檔案解壓縮。
    3. 將管理組件匯入到 Operations Manager。 如需如何從磁碟匯入管理組件的相關資訊，請參閱[如何匯入 Operations Manager 管理組件](https://technet.microsoft.com/library/hh212691.aspx)。
    4. 若要將 Operations Manager 連線至 Azure 監視器記錄，請依照[將 Operations Manager 連接到 Azure 監視器記錄](../azure-monitor/platform/om-agents.md)中的步驟進行。

* 如需從 Configuration Manager 使用電腦群組的詳細資訊，請參閱[將 Configuration Manager 連接到 Azure 監視器](../azure-monitor/platform/collect-sccm.md)。

### <a name="frequently-asked-questions"></a>常見問題集
* 我可以將資料從 Microsoft Azure 中的 Azure 監視器記錄遷移至 Azure Government 嗎？
  * 否。 不能將資料或您的工作區從 Microsoft Azure 移動至 Azure Government。
* 我可以從 Operations Management Suite 入口網站切換 Microsoft Azure 和 Azure Government 工作區嗎？
  * 否。 Microsoft Azure 和 Azure Government 的入口網站是分開的，而且不共用資訊。

如需詳細資訊，請參閱[Azure 監視器記錄公開檔](../log-analytics/log-analytics-overview.md)。

## <a name="scheduler"></a>排程器
如需此服務和其使用方式的詳細資訊，請參閱 Azure 排程器[檔](../scheduler/index.md)。

## <a name="azure-portal"></a>Azure 入口網站
您可以在[這裡](https://portal.azure.us)存取 Azure Government 入口網站。

## <a name="azure-resource-manager"></a>Azure Resource Manager
如需此服務和其使用方式的詳細資訊，請參閱[Azure Resource Manager 檔](../azure-resource-manager/management/overview.md)。

## <a name="next-steps"></a>後續步驟
* 訂閱 Azure Government 的[blog](https://blogs.msdn.microsoft.com/azuregov/)
* 使用[azure gov](https://stackoverflow.com/questions/tagged/azure-gov)取得有關 Stack Overflow 的說明
* 透過[Azure Government 意見反應論壇](https://feedback.azure.com/forums/558487-azure-government)提供意見反應或要求新功能
