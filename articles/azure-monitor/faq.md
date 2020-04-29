---
title: Azure 監視器常見問題 |Microsoft Docs
description: 有關 Azure 監視器常見問題的解答。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: db63ce2d56eb78bf6b361d530511b6902c1cb6d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80637778"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure 監視器常見問題

此 Microsoft 常見問題是關於 Azure 監視器的常見問題清單。

## <a name="general"></a>一般

### <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
[Azure 監視器](overview.md)是 azure 中的一項服務，可針對 azure、其他雲端環境或內部部署中的應用程式和服務，提供效能和可用性監視功能。 Azure 監視器會將多個來源的資料收集到通用資料平臺，以便在其中分析趨勢和異常。 Azure 監視器中豐富的功能，可協助您快速識別並回應可能會影響應用程式的重大情況。

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure 監視器、Log Analytics 和 Application Insights 之間有何差異？
在2018年9月，Microsoft 將 Azure 監視器、Log Analytics 和 Application Insights 結合成單一服務，為您的應用程式及其依賴的元件提供強大的端對端監視。 Log Analytics 和 Application Insights 中的功能並未變更，雖然某些功能已更名為 Azure 監視器，以便更清楚地反映新的範圍。 Log Analytics 的記錄資料引擎和查詢語言現在稱為 Azure 監視器記錄。 請參閱[Azure 監視器詞彙更新](terminology.md)。

### <a name="what-does-azure-monitor-cost"></a>Azure 監視器的成本為何？
自動啟用的 Azure 監視器功能，例如收集計量和活動記錄，皆免費提供。 與其他功能相關聯的成本，例如記錄查詢和警示。 如需詳細定價資訊，請參閱[Azure 監視器定價頁面](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="how-do-i-enable-azure-monitor"></a>如何? 啟用 Azure 監視器嗎？
Azure 監視器會在您建立新的 Azure 訂用帳戶時啟用，而且會自動收集[活動記錄](platform/activity-logs-overview.md)和平臺[計量](platform/data-platform-metrics.md)。 建立[診斷設定](platform/diagnostic-settings.md)以收集有關 Azure 資源作業的更詳細資訊，並新增[監視解決方案](insights/solutions.md)和[深入](insights/insights-overview.md)解析，以針對特定服務的收集資料提供額外的分析。 

### <a name="how-do-i-access-azure-monitor"></a>如何? 存取 Azure 監視器嗎？
從 Azure 入口網站的 [**監視**] 功能表中，存取所有 Azure 監視器功能和資料。 不同 Azure 服務功能表的 [**監視**] 區段，可讓您存取與篩選至特定資源的資料相同的工具。 使用 CLI、PowerShell 和 REST API 的各種案例也可以存取 Azure 監視器資料。

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>是否有 Azure 監視器的內部部署版本？
否。 Azure 監視器是可調整的雲端服務，可處理並儲存大量資料，但 Azure 監視器可以監視內部部署和其他雲端中的資源。

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Azure 監視器可以監視內部部署資源嗎？
是的，除了從 Azure 資源收集監視資料之外，Azure 監視器還可以從其他雲端和內部部署中的虛擬機器和應用程式收集資料。 如需 Azure 監視器，請參閱[監視資料的來源](platform/data-sources.md)。

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure 監視器是否與 System Center Operations Manager 整合？
您可以將現有的 System Center Operations Manager 管理群組連線到 Azure 監視器，以將代理程式中的資料收集到 Azure 監視器記錄中。 這可讓您使用記錄查詢和方案來分析從代理程式收集到的資料。 您也可以設定現有的 System Center Operations Manager 代理程式，直接將資料傳送至 Azure 監視器。 請參閱[將 Operations Manager 連接到 Azure 監視器](platform/om-agents.md)。

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure 監視器使用哪些 IP 位址？
請參閱[Application Insights 和 Log Analytics 使用的 ip 位址](app/ip-addresses.md)，以取得代理程式和其他外部資源所需的 ip 位址和埠清單，以存取 Azure 監視器。 

## <a name="monitoring-data"></a>監視資料

### <a name="where-does-azure-monitor-get-its-data"></a>Azure 監視器取得其資料的位置？
Azure 監視器會從各種來源收集資料，包括來自 Azure 平臺和資源的記錄和計量、自訂應用程式，以及在虛擬機器上執行的代理程式。 Azure 資訊安全中心和網路監看員等其他服務會將資料收集到 Log Analytics 工作區中，以便使用 Azure 監視器資料進行分析。 您也可以使用記錄或計量的 REST API，將自訂資料傳送至 Azure 監視器。 如需 Azure 監視器，請參閱[監視資料的來源](platform/data-sources.md)。

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure 監視器會收集哪些資料？ 
Azure 監視器會將各種來源的資料收集到[記錄](platform/data-platform-logs.md)或[計量](platform/data-platform-metrics.md)中。 每種資料類型都有自己的相對優點，而且每個都支援 Azure 監視器中的一組特定功能。 每個 Azure 訂用帳戶都有單一計量資料庫，而您可以根據您的需求建立多個 Log Analytics 工作區來收集記錄。 請參閱[Azure 監視器資料平臺](platform/data-platform.md)。

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Azure 監視器中是否有可收集的最大資料量？
您可以收集的計量資料量沒有限制，但這項資料會儲存最多93天。 請參閱[計量的保留期](platform/data-platform-metrics.md#retention-of-metrics)。 您可以收集的記錄資料量沒有限制，但可能會受到您為 Log Analytics 工作區選擇的定價層所影響。 請參閱[定價詳細資料](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>如何? 存取 Azure 監視器所收集的資料嗎？
深入解析和解決方案提供使用儲存在 Azure 監視器中之資料的自訂體驗。 您可以使用以 Kusto 查詢語言（KQL）撰寫的記錄查詢，直接處理記錄資料。 在 Azure 入口網站中，您可以使用 Log Analytics 來撰寫和執行查詢，並以互動方式分析資料。 使用計量瀏覽器分析 Azure 入口網站中的計量。 請參閱[在 Azure 監視器中分析記錄資料](log-query/log-query-overview.md)和[開始使用 Azure 計量瀏覽器](platform/metrics-getting-started.md)。

## <a name="solutions-and-insights"></a>解決方案和見解

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure 監視器中的深入解析為何？
深入解析可為特定 Azure 服務提供自訂的監視體驗。 它們使用相同的計量和記錄作為 Azure 監視器中的其他功能，但可能會收集額外的資料，並在 Azure 入口網站中提供獨特的體驗。 請參閱[Azure 監視器中的深入](insights/insights-overview.md)解析。

若要在 Azure 入口網站中查看深入解析，請參閱 [**監視**] 功能表或服務功能表的 [**監視**] 區段中的 [**見解**] 區段。

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure 監視器中的解決方案是什麼？
監視解決方案是一組封裝的邏輯，可根據 Azure 監視器功能來監視特定的應用程式或服務。 它們會在 Azure 監視器中收集記錄資料，並使用 Azure 入口網站中的常見體驗，為分析提供記錄查詢和查看。 請參閱[Azure 監視器中的監視解決方案](insights/solutions.md)。

若要在 Azure 入口網站中查看方案，請在 [**監視**] 功能表的 [**深入**解析] 區段中按一下 [**更多**]。 按一下 [**新增**]，將其他解決方案新增至工作區。

## <a name="logs"></a>記錄

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure 監視器記錄和 Azure 資料總管之間有何差異？
Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 監視器記錄建置於 Azure 資料總管上，並使用相同的 Kusto 查詢語言（KQL），但有一些小差異。 請參閱[Azure 監視器記錄查詢語言差異](log-query/data-explorer-difference.md)。

### <a name="how-do-i-retrieve-log-data"></a>如何? 取出記錄資料嗎？
所有資料都是使用以 Kusto 查詢語言（KQL）所撰寫的記錄查詢，從 Log Analytics 工作區取得。 您可以撰寫自己的查詢，或使用包含特定應用程式或服務之記錄查詢的解決方案和深入解析。 請參閱[Azure 監視器中記錄查詢的總覽](log-query/log-query-overview.md)。

### <a name="what-is-a-log-analytics-workspace"></a>什麼是 Log Analytics 工作區？
Azure 監視器收集的所有記錄資料都會儲存在 Log Analytics 工作區中。 工作區基本上是從各種來源收集記錄資料的容器。 您的所有監視資料可能都有單一 Log Analytics 工作區，或可能有多個工作區的需求。 請參閱[設計您的 Azure 監視器記錄部署](platform/design-logs-deployment.md)。

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>您可以將現有的 Log Analytics 工作區移至另一個 Azure 訂用帳戶嗎？
您可以在資源群組或訂用帳戶之間移動工作區，但不能將它移到不同的區域。 請參閱[將 Log Analytics 工作區移至不同的訂用帳戶或資源群組](platform/move-workspace.md)。

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>為什麼在 Log Analytics 中看不到 [查詢瀏覽器] 和 [儲存] 按鈕？

當[查詢範圍](log-query/scope.md)設定為特定資源時，無法使用 [**查詢瀏覽器**]、[**儲存**] 和 [**新增警示規則**] 按鈕。 若要建立警示、儲存或載入查詢，Log Analytics 必須限定于工作區。 若要在工作區內容中開啟 Log Analytics，請從 [ **Azure 監視器**] 功能表中選取 [**記錄**]。 已選取上次使用的工作區，但您可以選取任何其他工作區。 請參閱[Azure 監視器 Log Analytics 中的記錄查詢範圍和時間範圍](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>為什麼我會收到錯誤：「為此訂用帳戶註冊資源提供者 ' Microsoft 深入解析」，以便在從 VM 開啟 Log Analytics 時啟用此查詢？ 
許多資源提供者會自動註冊，但您可能需要手動註冊某些資源提供者。 註冊範圍一律是訂用帳戶。 如需詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>從 VM 開啟 Log Analytics 時，為何無法取得存取錯誤訊息？ 
若要檢視 VM 記錄，您必須獲得存放 VM 記錄的工作區讀取權限。 在這些情況下，系統管理員必須授與您 Azure 權限。

## <a name="alerts"></a>警示

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure 監視器中的警示為何？
當您的監視資料中發現重要條件時，警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 警示有多種類型：

- 度量-計量值超過閾值。
- 記錄查詢-記錄檔查詢符合所定義準則的結果。
- 活動記錄-活動記錄事件符合已定義的準則。
- Web 測試-可用性測試符合已定義準則的結果。


請參閱[Microsoft Azure 中的警示總覽](platform/alerts-overview.md)。


### <a name="what-is-an-action-group"></a>什麼是動作群組？
「動作群組」是可由警示觸發的通知和動作集合。 多個警示可以使用單一動作群組，讓您運用一般的通知和動作集合。 請參閱[建立和管理 Azure 入口網站中的動作群組](platform/action-groups.md)。


### <a name="what-is-an-action-rule"></a>什麼是動作規則？
動作規則可讓您修改一組符合特定準則的警示行為。 這可讓您在維護期間停用警示動作，以執行這類需求。 您也可以將動作群組套用至一組警示，而不是直接將其套用至警示規則。 請參閱[動作規則](platform/alerts-action-rules.md)。

## <a name="agents"></a>代理程式

### <a name="does-azure-monitor-require-an-agent"></a>Azure 監視器需要代理程式嗎？
只有在虛擬機器中從作業系統和工作負載收集資料時，才需要代理程式。 虛擬機器可以位於 Azure、另一個雲端環境或內部部署中。 請參閱[Azure 監視器代理](platform/agents-overview.md)程式的總覽。


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure 監視器代理程式之間有何差異？
Azure 診斷擴充功能適用于 Azure 虛擬機器，並會收集資料以 Azure 監視器計量、Azure 儲存體和 Azure 事件中樞。 Log Analytics 代理程式適用于 Azure 中的虛擬機器、另一個雲端環境或內部部署，並會收集資料以 Azure 監視器記錄。 相依性代理程式需要 Log Analytics 代理程式和收集的進程詳細資料和相依性。 請參閱[Azure 監視器代理](platform/agents-overview.md)程式的總覽。


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>我的代理程式流量是否會使用我的 ExpressRoute 連線？
Azure 監視器的流量會使用 Microsoft 對等互連 ExpressRoute 線路。 如需不同類型 ExpressRoute 流量的說明，請參閱[expressroute 檔](../expressroute/expressroute-faqs.md#supported-services)。 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>如何確認 Log Analytics 代理程式可以與 Azure 監視器通訊？
從代理程式電腦上的 [控制台] 中，選取 [**安全性] & [設定**]， **Microsoft Monitoring Agent** ]。 在 [ **Azure Log Analytics （OMS）** ] 索引標籤下，綠色核取記號圖示可確認代理程式能夠與 Azure 監視器通訊。 黃色的警告圖示表示代理程式發生問題。 其中一個常見的原因是**Microsoft Monitoring Agent**服務已停止。 使用服務控制管理員來重新啟動服務。

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>如何? 停止 Log Analytics 代理程式與 Azure 監視器通訊？
針對直接連線到 Log Analytics 的代理程式，開啟 [控制台]，然後選取 [**安全性] & [設定**]， **Microsoft Monitoring Agent**]。 在 [ **Azure Log Analytics （OMS）** ] 索引標籤下，移除所有列出的工作區。 在 System Center Operations Manager 中，從 [Log Analytics 管理的電腦] 清單中移除電腦。 Operations Manager 會將代理程式的設定更新為不再向 Log Analytics 回報。 

### <a name="how-much-data-is-sent-per-agent"></a>每個代理程式會傳送多少資料？
每個代理程式所傳送的資料量取決於：

* 您已啟用的解決方案
* 記錄和要收集之效能計數器的數目
* 記錄中的資料量

如需詳細資訊，請參閱使用[Azure 監視器記錄來管理使用量和成本](platform/manage-cost-storage.md)。

對於可執行 WireData 代理程式的電腦，請使用下列查詢查看正在傳送的資料量：

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>將資料傳送至 Azure 監視器時，Microsoft 管理代理程式（MMA）會使用多少網路頻寬？
頻寬是關於傳送的資料量的功能。 透過網路傳送資料時，會壓縮資料。


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>當 Log Analytics 代理程式的資料收集停止時，如何收到通知？

使用[建立新記錄警示](platform/alerts-metric.md)中所述的步驟，以在資料收集停止時收到通知。 針對警示規則使用下列設定：

- **定義警示條件**：將您的 Log Analytics 工作區指定為資源目標。
- **警示準則** 
   - **信號名稱**：*自訂記錄搜尋*
   - **搜尋查詢**：`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **警示邏輯**：**根據***結果數目*，**條件***大於*，**臨界值** *0*
   - **評估依據**：**期間（以分鐘為單位）** *30*，**頻率（以分鐘為單位）** *10*
- **定義警示詳細資料** 
   - **名稱**：*資料收集已停止*
   - **嚴重性**：*警告*

指定現有或新的[動作群組](platform/action-groups.md)，以便在記錄警示符合準則時，如果您有超過15分鐘的時間，則會通知您。


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure 監視器代理程式的防火牆需求為何？
如需防火牆需求的詳細資訊，請參閱[網路防火牆需求](platform/log-analytics-agent.md#network-requirements)。


## <a name="visualizations"></a>視覺效果

### <a name="why-cant-i-see-view-designer"></a>為什麼我看不到 View Designer？

View Designer 僅適用于在 Log Analytics 工作區中獲派「參與者」許可權或更高版本的使用者。

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>組態問題
*我在設定下列項目時有問題：*

* [.NET 應用程式](app/asp-net-troubleshoot-no-data.md)
* [監視已在執行的應用程式](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure 診斷](platform/diagnostics-extension-to-application-insights.md)
* [JAVA web 應用程式](app/java-troubleshoot.md)

我的伺服器沒有傳回資料**

* [設定防火牆例外狀況](app/ip-addresses.md)
* [設定 ASP.NET 伺服器](app/monitor-performance-live-website-now.md)
* [設定 Java 伺服器](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>我是否可以搭配 ... 來使用 Application Insights 嗎？

* [Azure VM 或 Azure 虛擬機器擴展集中 IIS 伺服器上的 Web 應用程式](app/azure-vm-vmss-apps.md)
* [IIS 伺服器 (內部部署或 VM 中) 上的 Web 應用程式](app/asp-net.md)
* [JAVA web 應用程式](app/java-get-started.md)
* [Node.js 應用程式](app/nodejs.md)
* [Azure 上的 Web 應用程式](app/azure-web-apps.md)
* [Azure 上的雲端服務](app/cloudservices.md)
* [在 Docker 中執行的應用程式伺服器](app/docker.md)
* [單一頁面 Web 應用程式](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows 傳統型應用程式](app/windows-desktop.md)
* [其他平台](app/platforms.md)

### <a name="is-it-free"></a>它是免費的嗎？

是，可作為實驗用途。 在基本定價方案中，您的應用程式每個月可以免費傳送一定額度的資料。 免費額度足夠因應開發用途和為少量使用者發佈應用程式。 您可以設定上限以防止處理超過指定的資料量。

較大量的遙測資料是以 Gb 計費。 我們提供一些有關如何[限制費用](app/pricing.md)的秘訣。

「企業」方案會針對每個 Web 伺服器節點傳送遙測資料的每一天計費。 如果您想要大規模使用「連續匯出」，便適用此方案。

[請參閱定價方案](https://azure.microsoft.com/pricing/details/application-insights/)。

### <a name="how-much-does-it-cost"></a>費用是多少？

* 在 Application Insights 資源中開啟 [使用量和估計成本]**** 頁面。 系統會顯示一張最近使用量的圖表。 您可以視需要設定資料量上限。
* 開啟 [Azure 帳單刀鋒視窗](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview)以查看您所有資源的帳單。

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights 在我的專案中修改什麼？
詳細資料視專案類型而定。 若是 Web 應用程式：

* 將這些檔案新增至您的專案：
  * ApplicationInsights.config
  * ai.js
* 安裝這些 NuGet 套件：
  * *Application Insights API* - 核心 API
  * *Application Insights API for Web Applications* - 用來從伺服器傳送遙測
  * *Application Insights API for JavaScript Applications* - 用來從用戶端傳送遙測
* 套件包含這些組件：
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 將項目插入至：
  * Web.config
  * packages.config
* （僅限新專案-如果您[將 Application Insights 新增至現有的專案][start]，就必須手動執行此動作）。在用戶端和伺服器程式碼中插入程式碼片段，以 Application Insights 資源識別碼將它們初始化。 例如，在 MVC 應用程式中，系統會將程式碼插入至主版頁面 Views\_/Shared/Layout。 cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>如何從舊版 SDK 升級？
請參閱您的應用程式類型所適用之 SDK 的[版本資訊](app/release-notes.md)。

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>如何變更我的專案將資料傳送到哪一個 Azure 資源？
在 [方案總管] 中，以滑鼠右鍵按一下 `ApplicationInsights.config` ，然後選擇 [ **更新 Application Insights**]。 您可以將資料傳送至 Azure 中的現有資源或新資源。 更新精靈會變更 ApplicationInsights.config 中的檢測金鑰，這決定伺服器 SDK 將您的資料送往何處。 除非您取消選取 [全部更新]，否則也會變更金鑰出現在您網頁中的位置。

### <a name="what-is-status-monitor"></a>什麼是狀態監視器？

一個傳統型應用程式，您可以在 IIS Web 伺服器中使用它，以協助您在 Web 應用程式中設定 Application Insights。 它不會收集遙測資料：當您沒有在設定應用程式時，可以將它停止。 

[深入了解](app/monitor-performance-live-website-now.md#questions)。

### <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights 會收集什麼遙測資料？

從伺服器 Web 應用程式：

* HTTP 要求
* 相依[性。](app/asp-net-dependencies.md) 對下列各項的呼叫：SQL Database、對外部服務的 HTTP 呼叫、Azure Cosmos DB、資料表、Blob 儲存體及佇列。 
* [例外狀況](app/asp-net-exceptions.md)和堆疊追蹤。
* [效能計數器](app/performance-counters.md)-如果您使用[狀態監視器](app/monitor-performance-live-website-now.md)、[適用于應用程式服務的 AZURE 監視](app/azure-web-apps.md)、[適用于 VM 或虛擬機器擴展集的 azure 監視](app/azure-vm-vmss-apps.md)，或[Application Insights collectd 寫入器](app/java-collectd.md)。
* 您以程式碼撰寫的[自訂事件和計量](app/api-custom-events-metrics.md)。
* [追蹤記錄](app/asp-net-trace-logs.md) - 如果您設定適當的收集器。

從[用戶端網頁](app/javascript.md)：

* [頁面檢視計數](app/usage-overview.md)
* [AJAX 呼叫](app/asp-net-dependencies.md) - 從執行中指令碼發出的要求。
* 頁面檢視載入資料
* 使用者和工作階段計數
* [已驗證的使用者識別碼](app/api-custom-events-metrics.md#authenticated-users)

從其他來源 (如果您設定它們的話)：

* [Azure 診斷](platform/diagnostics-extension-to-application-insights.md)
* [匯入到分析](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>我是否可以篩選掉或修改某些遙測？

是，您可以在伺服器中撰寫：

* 「遙測處理器」以在從您的應用程式傳送選取的遙測項目之前，先篩選它們的屬性或為它們新增屬性。
* 「初始設定式」以為所有遙測項目新增屬性。

深入了解 [ASP.NET](app/api-filtering-sampling.md) 或 [Java](app/java-filter-telemetry.md)。

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>城市、國家/地區和其他地理位置資料的計算方式為何？

我們會使用 [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 來查詢 Web 用戶端的 IP 位址 (IPv4 或 IPv6)。

* 瀏覽器遙測：我們會收集傳送者的 IP 位址。
* 伺服器遙測：Application Insights 模組會收集用戶端 IP 位址。 如果已設定 `X-Forwarded-For`，則不會收集該位址。
* 若要深入瞭解如何在 Application Insights 中收集 IP 位址和地理位置資料，請參閱這[篇文章](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)。


您可以設定 `ClientIpHeaderTelemetryInitializer` 以從不同的標頭取得 IP 位址。 例如，在某些系統中，Proxy、負載平衡器或 CDN 會將它移至 `X-Originating-IP`。 [深入了解](https://apmtips.com/blog/2016/07/05/client-ip-address/)。

您可以[使用 Power BI](app/export-power-bi.md ) 在地圖上顯示您的要求遙測資料。


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>資料保留在入口網站多久的時間？ 是否安全？
請參閱[資料保留和隱私權][data]。

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>當伺服器或裝置失去與 Azure 的連線時，Application insights 的遙測會發生什麼事？

所有的 Sdk （包括 web SDK）都包含「可靠的傳輸」或「健全的傳輸」。 當伺服器或裝置失去與 Azure 的連線時，遙測會[儲存在本機檔案系統](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage)（伺服器 sdk）或 HTML5 會話儲存體（Web SDK）中。 SDK 會定期重試傳送此遙測，直到我們的內嵌服務將其視為「過時」（記錄檔為48小時，計量為30分鐘）。 將捨棄過時的遙測。 在某些情況下，例如當本機儲存體已滿時，就不會進行重試。


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>可以在遙測中傳送個人資料嗎？

如果您的程式碼會傳送這類資料，就有可能。 如果堆疊追蹤中的變數包含個人資料，也可能發生這種情況。 您的開發小組應該進行風險評估，以確保正確處理個人資料。 [深入了解資料保留和隱私權](app/data-retention-privacy.md)。

在查閱地理位置屬性之後，用戶端 Web 位址的**所有**八位元資料一律會設定為 0。

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>我的檢測金鑰會顯示在 [我的網頁] 來源中。 

* 這在監視解決方案中是常見的做法。
* 無法使用它來竊取您的資料。
* 可以使用它來扭曲您的資料或觸發警示。
* 我們尚未聽到有任何客戶有這類問題。

您可以：

* 針對用戶端和伺服器資料，請使用兩個個別的檢測金鑰（個別的 Application Insights 資源）。 或者
* 撰寫一個在您伺服器中執行的 Proxy，並讓 Web 用戶端透過該 Proxy 傳送資料。

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>如何在診斷搜尋中查看 POST 資料？
我們不會自動記錄 POST 資料，但您可以使用 TrackTrace 呼叫：將資料放置到訊息參數中。 相較於字串屬性的限制，此呼叫可容許較長的大小，不過您無法篩選。

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>我應該使用單一還是多個 Application Insights 資源？

對於單一商務系統中的所有元件或角色，請使用單一資源。 對於開發、測試和發行版本以及獨立的應用程式，則請使用不同的資源。

* [請參閱這裡的討論](app/separate-resources.md)
* [範例 - 具有背景工作角色和 Web 角色的雲端服務](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>我如何動態變更檢測金鑰？

* [這裡的討論](app/separate-resources.md)
* [範例 - 具有背景工作角色和 Web 角色的雲端服務](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>什麼是使用者和和工作階段計數？

* JavaScript SDK 會在 Web 用戶端上設定使用者 Cookie 來識別傳回使用者，以及設定一個工作階段 Cookie 來將活動分組。
* 如果沒有任何用戶端指令碼，則您可以[在伺服器設定 Cookie](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)。
* 如果有一個真實的使用者以不同的瀏覽器使用您的站台，或是使用 InPrivate/Incognito 瀏覽，或透過不同的電腦，則系統會將其計算多次。
* 若要跨電腦和瀏覽器識別登入的使用者，請新增對 [setAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users) 的呼叫。

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> 我是否已啟用 Application Insights 中的所有項目？
| 您應該會看到 | 如何取得 | 取得原因 |
| --- | --- | --- |
| 可用性圖表 |[Web 測試](app/monitor-web-app-availability.md) |知道您的 Web 應用程式已啟動 |
| 伺服器應用程式效能：回應時間... |[將 Application Insights 新增至您的專案](app/asp-net.md)或[在伺服器上安裝 AI 狀態監視器](app/monitor-performance-live-website-now.md) (或撰寫您自己的程式碼來[追蹤相依性](app/api-custom-events-metrics.md#trackdependency)) |偵測效能問題 |
| 相依性遙測 |[在伺服器上安裝 AI 狀態監視器](app/monitor-performance-live-website-now.md) |診斷資料庫或其他外部元件的問題 |
| 取得例外狀況的堆疊追蹤 |[在程式碼中插入 TrackException 呼叫](app/asp-net-exceptions.md) (但部分會自動報告) |偵測並診斷例外狀況 |
| 搜尋記錄追蹤 |[新增記錄配接器](app/asp-net-trace-logs.md) |診斷例外狀況、效能問題 |
| 用戶端使用基本概念：頁面檢視、工作階段... |[網頁中的 JavaScript 初始設定式](app/javascript.md) |流量分析 |
| 用戶端自訂度量 |[追蹤網頁中的呼叫](app/api-custom-events-metrics.md) |增強使用者經驗 |
| 伺服器自訂度量 |[追蹤伺服器中的呼叫](app/api-custom-events-metrics.md) |商業智慧 |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>為什麼 [搜尋] 和 [計量] 圖表中的計數不相等？

[取樣](app/sampling.md)會減少從您應用程式實際傳送到入口網站的遙測項目 (要求、自訂事件等等) 數目。 在「搜尋」中，您會看到實際收到的項目數目。 在顯示事件計數的計量圖表中，您會看到發生的原始事件數目。 

每個傳輸的項目都帶有一個 `itemCount` 屬性，此屬性會顯示該項目代表的原始事件數目。 若要觀察取樣的運作情況，您可以在 [分析] 中執行下列查詢：

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>自動化

#### <a name="configuring-application-insights"></a>設定 Application Insights

您可以[撰寫 PowerShell 指令碼](app/powershell.md)以使用「Azure 資源監視器」來：

* 建立和更新 Application Insights 資源。
* 設定定價方案。
* 取得檢測金鑰。
* 新增計量警示。
* 新增可用性測試。

您無法設定「計量瀏覽器」報告或設定連續匯出。

#### <a name="querying-the-telemetry"></a>查詢遙測

請使用 [REST API](https://dev.applicationinsights.io/) 來執行[分析](app/analytics.md)查詢。

### <a name="how-can-i-set-an-alert-on-an-event"></a>我要如何在事件上設定警示？

Azure 警示僅針對計量。 請建立一個會在每次事件發生時超出值臨界值的自訂計量。 然後在該計量上設定警示。 當計量的任一方向超出閾值時，您就會收到通知;不論初始值是高或低，您都不會收到通知，直到第一次跨越為止;一律會有幾分鐘的延遲。

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Azure Web 應用程式與 Application Insights 之間的資料傳輸需要收費嗎？

* 如果您的 Azure Web 應用程式是裝載在具有 Application Insights 集合端點的資料中心內，就不會有費用。 
* 如果您的裝載資料中心內沒有任何集合端點，您應用程式的遙測資料就會產生 [Azure 傳出費用](https://azure.microsoft.com/pricing/details/bandwidth/)。

這並不取決於您 Application Insights 資源的裝載位置。 而是取決於我們端點的分佈。

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>我是否可以將遙測資料傳送給 Application Insights 入口網站？

建議您使用我們的 SDK 和使用 [SDK API](app/api-custom-events-metrics.md)。 SDK 有適用於各種[平台](app/platforms.md)的各種變體。 這些 SDK 可處理緩衝、壓縮、節流、重試等。 不過，[擷取結構描述](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema)和[端點通訊協定](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md)是公用的。

### <a name="can-i-monitor-an-intranet-web-server"></a>我是否可以監視內部網路 Web 伺服器？

是，但您必須藉由防火牆例外或 Proxy 重新導向允許流量進入至我們的服務。
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


請在[這裡](app/ip-addresses.md)檢閱我們的服務和 IP 位址完整清單。

#### <a name="firewall-exception"></a>防火牆例外

允許您的 Web 伺服器將遙測資料傳送至我們的端點。 

#### <a name="gateway-redirect"></a>閘道重新導向

藉由在您的組態中覆寫「端點」，將流量從您的伺服器路由到內部網路上的閘道。 如果您的設定中沒有這些「端點」屬性，這些類別將會使用下列 ApplicationInsights.config 範例中顯示的預設值。 

您的閘道應將流量路由到我們端點的基底位址。 在您的組態中，請將預設值取代為 `http://<your.gateway.address>/<relative path>`。


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>使用預設端點的範例 ApplicationInsights.config：
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> 從 v 2.6.0 開始可以使用 ApplicationIdProvider。



#### <a name="proxy-passthrough"></a>Proxy 傳遞

藉由設定電腦層級或應用層級 proxy，即可達到 Proxy 傳遞。
如需詳細資訊，請參閱[DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)上的 dotnet 文章。
 
 範例 Web.config：
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>我是否可以在內部網路伺服器上執行可用性 Web 測試？

我們的 [Web 測試](app/monitor-web-app-availability.md)是在分散於全球各地的網路節點上執行。 有兩個解決方案：

* 防火牆門 - 允許從[這個又長又可變更的 Web 測試代理程式清單](app/ip-addresses.md)傳送要求給您的伺服器。
* 撰寫您自己的程式碼，以從內部網路內傳送定期要求給您的伺服器。 您可以針對這個目的執行 Visual Studio Web 測試。 測試者可以使用 TrackAvailability() API 將結果傳送給 Application Insights。

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>收集遙測資料需要花費多少時間？

大部分的 Application Insights 資料有 5 分鐘以內的延遲。 某些資料可能會更久；通常是較大的記錄檔。 如需詳細資訊，請參閱 [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)。



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器

此 Microsoft 常見問題集是適用於容器的 Azure 監視器常見問題清單。 若您有任何關於解決方案的其他問題，請前往[討論論壇](https://feedback.azure.com/forums/34192--general-feedback)並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

### <a name="what-does-other-processes-represent-under-the-node-view"></a>*其他進程*在節點視圖下代表什麼？

**其他處理**程式旨在協助您清楚瞭解節點上高資源使用量的根本原因。 這可讓您區分容器化進程與非容器化程式之間的使用方式。

這些**其他處理**程式是什麼？ 

這些是在您的節點上執行的非容器化進程。  

如何計算這種情況？

**其他進程** = 從*容器化進程*CAdvisor - 使用量*的總使用量*

**其他處理**套裝程式括：

- 自我管理或受控 Kubernetes 非容器化進程 

- 容器執行時間進程  

- Kubelet  

- 在您的節點上執行的系統進程 

- 在節點硬體或 VM 上執行的其他非 Kubernetes 工作負載 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>當我查詢 ContainerLog 資料表時，看不到已填入的 [影像] 和 [名稱] 屬性值。

針對代理程式版本 ciprod12042019 和更新版本，預設不會針對每個記錄行填入這兩個屬性，以將收集的記錄資料所產生的成本降至最低。 有兩個選項可查詢包含這些屬性的資料表及其值：

#### <a name="option-1"></a>選項 1 

聯結其他資料表以在結果中包含這些屬性值。

藉由聯結 ContainerID 屬性，修改您的查詢以```ContainerInventory```包含資料表中的 Image 和 ImageTag 屬性。 藉由聯結 ContainerID 屬性，您可以在 KubepodInventory 資料表的 ContaineName ```ContainerLog```欄位中包含 Name 屬性（如先前出現在資料表中）。這是建議的選項。

下列範例是範例詳細查詢，說明如何使用聯結來取得這些域值。

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>選項 2

針對每個容器記錄行重新啟用這些屬性的集合。

如果第一個選項因為涉及查詢變更而不方便，您可以在 [[資料收集設定](insights/container-insights-agent-config.md)] 中啟用代理程式設定```log_collection_settings.enrich_container_logs```對應，藉以重新啟用收集這些欄位的功能。

> [!NOTE]
> 第二個選項不建議用於具有超過50個節點的大型叢集，因為它會從叢集中的每個節點產生 API 伺服器呼叫，以執行此擴充。 此選項也會針對每個收集的記錄行增加資料大小。

### <a name="can-i-view-metrics-collected-in-grafana"></a>我可以在 Grafana 中查看收集的計量嗎？

容器的 Azure 監視器支援在 Grafana 儀表板中，查看 Log Analytics 工作區中儲存的計量。 我們提供的範本可讓您從 Grafana 的[儀表板儲存](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)機制下載，協助您瞭解如何從受監視的叢集查詢其他資料，以在自訂的 Grafana 儀表板中進行視覺化。 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>我可以使用容器的 Azure 監視器來監視 AKS 引擎叢集嗎？

容器的 Azure 監視器支援監視部署至 Azure 上裝載的 AKS 引擎（先前稱為 ACS 引擎）叢集的容器工作負載。 如需進一步的詳細資料，以及針對此案例啟用監視所需步驟的總覽，請參閱針對[AKS 引擎的容器使用 Azure 監視器](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>為什麼我在 Log Analytics 工作區中看不到資料？

如果您無法在每天的特定時間看到 Log Analytics 工作區中的任何資料，您可能已達到預設的 500 MB 限制，或指定每日上限來控制要每日收集的資料量。 一天符合限制時，資料收集只會在隔天停止並繼續。 若要根據您預期的使用模式來檢查您的資料使用量，並更新至不同的定價層，請參閱[記錄資料使用量和成本](platform/manage-cost-storage.md)。 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory 資料表中指定的容器狀態為何？

ContainerInventory 資料表包含已停止和執行中容器的相關資訊。 此資料表由代理程式內的工作流程填入，該工作流程會查詢所有 (執行中和已停用) 容器的 Docker，並將該資料轉送到 Log Analytics 工作區。
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何? 解決*遺失的訂閱註冊*錯誤？

如果您收到**Microsoft.operationsmanagement 缺少訂閱註冊**的錯誤，您可以在定義工作區的訂用帳戶中註冊**microsoft.operationsmanagement**資源提供者來解決此問題。 您可以在[這裡](../azure-resource-manager/templates/error-register-resource-provider.md)找到作法的相關文件。

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否支援已啟用 RBAC 的 AKS 叢集？

容器監視解決方案不支援 RBAC，但支援容器的 Azure 監視器。 解決方案詳細資料頁面不會在顯示這些叢集資料的刀鋒視窗中顯示正確的資訊。

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>我如何透過 Helm 在 kube 系統命名空間中啟用容器的記錄檔收集？

kube 系統命名空間中容器的記錄檔收集預設為停用。 透過設定 omsagent 上的環境變數，可啟用記錄檔收集。 如需詳細資訊，請參閱[適用于容器的 Azure 監視器](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)GitHub 頁面。 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何將 omsagent 更新為最新的發行版本？

若要了解如何升級代理程式，請參閱[代理程式管理](insights/container-insights-manage-agent.md)。

### <a name="how-do-i-enable-multi-line-logging"></a>如何啟用多行記錄？

容器目前 Azure 監視器不支援多行記錄，但有可用的因應措施。 您可以設定使用 JSON 格式寫入所有的服務，之後 Docker/Moby會將所有服務寫成一行。

例如，您可以將記錄檔包裝在 JSON 物件，如以下範例中範例 node.js 應用程式所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

當您查詢記錄時，這項資料看起來會像下列範例中的 Azure 監視器：

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

如需此問題的詳細資訊，請參閱下列[GitHub 連結](https://github.com/moby/moby/issues/22920)。

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>當我啟用即時記錄時，如何? 解決 Azure AD 錯誤？ 

您可能會看到下列錯誤：**在要求中指定的回復 url 不符合為應用程式設定的回復 url： ' <應用程式識別碼\>'**。 若要解決此問題，您可以在[如何使用適用于容器的 Azure 監視器來即時查看容器資料](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)一文中找到此解決方案。 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>為什麼我在上架之後無法升級叢集？

如果您在啟用 AKS 叢集的容器 Azure 監視器之後，刪除叢集傳送其資料的 Log Analytics 工作區，當嘗試升級叢集時，將會失敗。 若要解決此情況，您必須停用監視，然後重新啟用它參考您訂用帳戶中不同的有效工作區。 當您嘗試再次執行叢集升級時，應該會處理並順利完成。  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>我需要針對代理程式開啟/列入白名單的埠和網域為何？

如需適用于 Azure、Azure 美國政府和 Azure 中國世紀雲端的容器化代理程式，請參閱[網路防火牆需求](insights/container-insights-onboard.md#network-firewall-requirements)中的 proxy 和防火牆設定資訊。

## <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
此 Microsoft 常見問題集是適用於 VM 的 Azure 監視器常見問題清單。 若您有任何關於解決方案的其他問題，請前往[討論論壇](https://feedback.azure.com/forums/34192--general-feedback)並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

### <a name="can-i-onboard-to-an-existing-workspace"></a>我可以將現有的工作區上線嗎？
如果您的虛擬機器已經連線到 Log Analytics 工作區，則您可以在上線到適用於 VM 的 Azure 監視器時繼續使用該工作區，但前提是它位於[此處](insights/vminsights-enable-overview.md#prerequisites)所列的其中一個支援區域。


### <a name="can-i-onboard-to-a-new-workspace"></a>我可以上線到新的工作區嗎？ 
如果您的 VM 目前並未連線到現有的 Log Analytics 工作區，您需要建立新的工作區來儲存資料。 如果您透過 Azure 入口網站為適用於 VM 的 Azure 監視器設定單一 Azure VM，建立新的預設工作區即會自動完成。

如果您選擇使用以腳本為基礎的方法，這些步驟將在[使用 Azure PowerShell 或 Resource Manager 範本啟用適用於 VM 的 Azure 監視器](insights/vminsights-enable-at-scale-powershell.md)一文中討論。 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果我的 VM 已經向現有的工作區回報，我該怎麼做？
如果您已經從虛擬機器收集資料，則您可能已經將它設為向現有的 Log Analytics 工作區回報資料。  只要該工作區位於我們支援的其中一個區域，您就可以針對該預先存在的工作區啟用適用於 VM 的 Azure 監視器。  如果您已在使用的工作區不在我們支援的其中一個區域，您目前將無法上架到適用於 VM 的 Azure 監視器。  我們正積極努力地支援更多區域。


### <a name="why-did-my-vm-fail-to-onboard"></a>為什麼無法將我的 VM 上線？
從 Azure 入口網站將 Azure VM 上線時，請執行下列步驟：

* 如果已選取該選項，即會建立預設的 Log Analytics 工作區。
* 如果決定需要使用 Log Analytics 代理程式，可使用 VM 延伸模組在 Azure VM 上進行安裝。  
* 如果決定需要使用適用於 VM 的 Azure 監視器對應 Dependency Agent，可使用延伸模組在 Azure VM 上進行安裝。 

在上線過程中，我們會檢查上述每一項的狀態，以便在入口網站中將通知狀態傳回給您。 設定工作區與代理程式安裝通常需要 5 至 10 分鐘。 在入口網站中查看監視資料需要額外的5至10分鐘。  

如果您已初始上線，並看到一則訊息指出必須將 VM 上線，則允許 VM 最多 30 分鐘的時間來完成此程序。 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我在 VM 的效能圖表中看不到部分或任何資料
我們的效能圖表已更新為使用儲存在*InsightsMetrics*資料表中的資料。  若要查看這些圖表中的資料，您將需要升級以使用新的 VM Insights 解決方案。  如需其他資訊，請參閱我們的[GA 常見問題](insights/vminsights-ga-release-faq.md)。

如果您在磁片資料表或部分效能圖表中看不到效能資料，則可能無法在工作區中設定效能計數器。 若要解決此問題，請執行下列 [PowerShell 指令碼](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)。


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>適用於 VM 的 Azure 監視器對應功能與服務對應有何不同？
適用於 VM 的 Azure 監視器對應功能會以服務對應為根據，但有下列差異：

* [對應] 檢視可以從 VM 刀鋒視窗，以及從 Azure 監視器下方適用於 VM 的 Azure 監視器進行存取。
* [對應] 中的連線現在可供點選，並在所選連線的側邊面板中顯示連線計量資料的檢視。
* 有一個新的 API 可用來建立對應，以便為更複雜的對應提供更好的支援。
* 受監視的 VM 現在包含於用戶端群組節點中，而環圈圖會顯示群組中受監視與未受監視的虛擬機器比例。  它也可以在展開群組時用來篩選機器清單。
* 受監視的虛擬機器現在包含於伺服器連接埠群組節點中，而環圈圖會顯示群組中受監視與未受監視的機器比例。  它也可以在展開群組時用來篩選機器清單。
* 對應樣式已更新，以便與 Application Insights 的應用程式對應更為一致。
* 側邊面板已更新，而且沒有服務對應更新管理、變更追蹤、安全性及服務台支援的完整整合集合。 
* 用來選擇要對應之群組與機器的選項已更新，現在支援訂用帳戶、資源群組、Azure 虛擬機器擴展集和雲端服務。
* 您無法在適用於 VM 的 Azure 監視器對應功能中建立新的服務對應機器群組。  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>為什麼我的效能圖表會顯示虛線？
發生的原因有數種。  萬一資料集合中有間隔，我們就會將線條描繪為虛線。  如果您針對啟用的效能計數器修改了資料取樣頻率 (預設設定是每隔 60 秒收集一次資料)，若您針對圖表選擇較窄的時間範圍，而且您的取樣頻率小於圖表中所使用的貯體大小 (例如，取樣頻率為每隔 10 分鐘，而圖表上的每個貯體是 5 分鐘)，則您會在圖表中看到虛線。  在此案例中，選擇更寬的時間範圍來檢視，應該會讓圖表線條顯示為實線，而非虛線。

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器支援群組嗎？
是，在您安裝相依性代理程式後，我們就會從 VM 收集資訊，以根據訂用帳戶、資源群組、虛擬機器擴展集和雲端服務顯示群組。  如果您先前使用服務對應，並已建立電腦群組，這些群組將會一併顯示。  如果您為您所檢視的工作區建立了電腦群組，這些群組也會出現在群組篩選條件中。 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>如何查看彙總效能圖表中衍生第 95 條百分位數線之項目的詳細資料？
根據預設，此清單會排序以顯示針對所選計量的第 95 個百分位數具有最高值的 VM，但可用記憶體圖表除外，因為它會顯示具有第 5 個百分位數之最小值的機器。  按一下圖表，將利用已選取的適當計量來開啟 [N 大排行榜]**** 檢視。

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>對應功能如何跨不同的 VNet 和子網路處理重複的 IP？
如果您跨子網路和 VNet 使用 VM 或 Azure 虛擬機器擴展集來複製 IP 範圍，它可能會造成適用於 VM 的 Azure 監視器對應功能顯示不正確的資訊。 這是個已知的問題，而我們正在研究改善此體驗的選項。

### <a name="does-map-feature-support-ipv6"></a>對應功能支援 IPv6 嗎？
對應功能目前僅支援 IPv4，而我們正在研究對於 IPv6 的支援。 我們也支援在 IPv6 內部設定通道的 IPv4。

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>當我針對資源群組或其他大型群組載入對應時，很難檢視該對應
儘管我們已改進對應來處理大型且複雜的設定，但我們理解到對應可擁有許多節點、連線，以及作為叢集使用的節點。  我們承諾會繼續加強支援以提高延展性。   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>[效能] 索引標籤上的網路圖表為什麼看起來與 [Azure VM 概觀] 頁面上的網路圖表不同呢？

適用於 Azure 虛擬機器的概觀頁面會根據客體 VM 中主機的活動量值來顯示圖表。  至於 [Azure VM 概觀] 上的網路圖表，它只會顯示將收費的網路流量。  這不包括虛擬網路間的流量。  針對適用於 VM 的 Azure 監視器所顯示的資料和圖表是以來賓 VM 的資料為基礎，而網狀圖表會顯示所有輸入和輸出到該 VM 的 TCP/IP 流量，包括虛擬網路之間。

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>如何針對儲存在 VMConnection 中的資料來測量回應時間，並顯示在連接面板和活頁簿中？

回應時間為近似值。 因為我們不會檢測應用程式的程式碼，所以我們不會真正知道何時會開始要求，以及回應抵達的時間。 相反地，我們會觀察在連線上傳送的資料，然後再傳回該連接上的資料。 我們的代理程式會追蹤這些傳送和接收，並嘗試將它們配對：一連串的傳送，後面接著一系列的接收會被視為要求/回應組。 這些作業之間的時間是回應時間。 它會包含網路延遲和伺服器處理時間。

此近似值適用于以要求/回應為基礎的通訊協定：單一要求會傳出連線，而單一回應會抵達。 這適用于 HTTP （S）（不含管線），但不符合其他通訊協定的條件。

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>如果我採用 Log Analytics 免費定價方案，有何限制？
如果您使用「免費」** 定價層設定了具有 Log Analytics 工作區的 Azure 監視器，則適用於 VM 的 Azure 監視器的對應功能僅支援五部已連線到工作區的電腦。 如果您有五部連線至免費工作區的 VM，您可中斷其中一部 VM 的連線，然後連線新的 VM，則新的 VM 不受監視，而且不會反映於 [對應] 頁面。  

在這種情況下，當您開啟 VM 並從左側窗格中選取 [**見解**] 時，系統會提示您使用 [**立即試用**] 選項，即使已在 vm 上安裝它之後也一樣。  不過，如果此 VM 並未在適用於 VM 的 Azure 監視器上線，就不會以通常會出現的選項提示您。 


## <a name="next-steps"></a>後續步驟
如果這裡沒有解答您的問題，您可以參考下列論壇以瞭解其他問題和答案。

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

如需 Azure 監視器的一般意見反應，請流覽[意見反應論壇](https://feedback.azure.com/forums/34192--general-feedback)。
