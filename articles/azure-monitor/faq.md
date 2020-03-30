---
title: Azure 監視器常見問題 |微軟文檔
description: 回答有關 Azure 監視器的常見問題的解答。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: 777e4e1f8fdd05345d949fe8c78b4a5b1953b8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298254"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure 監視器常見問題

此 Microsoft 常見問題解答是關於 Azure 監視器的常見問題清單。

## <a name="general"></a>一般

### <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
[Azure 監視器](overview.md)是 Azure 中的一項服務，它為 Azure、其他雲環境或本地中的應用程式和服務提供性能和可用性監視。 Azure 監視器將資料從多個源收集到一個公共資料平臺中，可以分析其趨勢和異常。 Azure 監視器中的豐富功能可説明您快速識別和回應可能影響應用程式的關鍵情況。

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Azure 監視器、日誌分析和應用程式見解之間的區別是什麼？
2018 年 9 月，Microsoft 將 Azure 監視器、日誌分析和應用程式見解合併到單個服務中，以便對應用程式及其所依賴的元件提供強大的端到端監視。 日誌分析和應用程式見解中的功能沒有改變，儘管某些功能已重命名為 Azure 監視器，以便更好地反映其新範圍。 日誌分析的日誌資料引擎和查詢語言現在稱為 Azure 監視器日誌。 請參閱[Azure 監視器術語更新](terminology.md)。

### <a name="what-does-azure-monitor-cost"></a>Azure 監視器的成本是多少？
自動啟用的 Azure 監視器功能（如收集指標和活動日誌）不收取任何費用。 與其他功能（如日誌查詢和警報）相關聯的成本。 有關詳細的定價資訊，請參閱[Azure 監視器定價頁](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="how-do-i-enable-azure-monitor"></a>如何啟用 Azure 監視器？
創建新 Azure 訂閱的瞬間啟用 Azure 監視器，並自動收集[活動日誌](platform/activity-logs-overview.md)和平臺[指標](platform/data-platform-metrics.md)。 創建[診斷設置](platform/diagnostic-settings.md)以收集有關 Azure 資源操作的更多詳細資訊，並添加[監視解決方案](insights/solutions.md)和[見解](insights/insights-overview.md)，以便對特定服務收集的資料提供其他分析。 

### <a name="how-do-i-access-azure-monitor"></a>如何訪問 Azure 監視器？
從 Azure 門戶中的 **"監視器"** 功能表訪問所有 Azure 監視器功能和資料。 不同 Azure 服務的功能表的 **"監視**"部分提供對相同工具的訪問，並篩選到特定資源的資料。 Azure 監視器資料對於使用 CLI、PowerShell 和 REST API 的各種方案也可訪問。

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>是否有本地版本的 Azure 監視器？
否。 Azure 監視器是一種可擴展的雲服務，用於處理和存儲大量資料，儘管 Azure 監視器可以監視本地和其他雲中的資源。

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Azure 監視器是否可以監視本地資源？
是的，除了從 Azure 資源收集監視資料外，Azure 監視器還可以從其他雲和本地中的虛擬機器和應用程式收集資料。 請參閱[Azure 監視器的監視資料來源](platform/data-sources.md)。

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure 監視器是否與系統中心操作管理器集成？
您可以將現有的系統中心操作管理器管理組連接到 Azure 監視器，以便從代理收集資料到 Azure 監視器日誌中。 這允許您使用日誌查詢和解決方案來分析從代理收集的資料。 還可以配置現有的系統中心操作管理器代理，將資料直接發送到 Azure 監視器。 請參閱[將操作管理器連接到 Azure 監視器](platform/om-agents.md)。

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Azure 監視器使用哪些 IP 位址？
有關代理和其他外部資源訪問 Azure 監視器所需的 IP 位址和埠的清單，請參閱[應用程式見解和日誌分析使用的 IP 位址](app/ip-addresses.md)。 

## <a name="monitoring-data"></a>監視資料

### <a name="where-does-azure-monitor-get-its-data"></a>Azure 監視器從何處獲取其資料？
Azure 監視器從各種源收集資料，包括來自 Azure 平臺和資源的日誌和指標、自訂應用程式和在虛擬機器上運行的代理。 其他服務（如 Azure 安全中心和網路觀察程式）將資料收集到日誌分析工作區中，以便可以使用 Azure 監視器資料對其進行分析。 您還可以使用 REST API 將自訂資料發送到 Azure 監視器，用於日誌或指標。 請參閱[Azure 監視器的監視資料來源](platform/data-sources.md)。

### <a name="what-data-is-collected-by-azure-monitor"></a>Azure 監視器收集了哪些資料？ 
Azure 監視器將資料從各種源收集到[日誌](platform/data-platform-logs.md)或[指標中](platform/data-platform-metrics.md)。 每種類型的資料都有其各自的相對優勢，並且每種資料都支援 Azure 監視器中的特定功能集。 每個 Azure 訂閱都有一個指標資料庫，同時您可以創建多個日誌分析工作區來收集日誌，具體取決於您的要求。 請參閱[Azure 監視器資料平臺](platform/data-platform.md)。

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>是否可以在 Azure 監視器中收集最大數量的資料？
您可以收集的指標資料量沒有限制，但此資料存儲最多為 93 天。 請參閱[保留指標](platform/data-platform-metrics.md#retention-of-metrics)。 您可以收集的日誌資料量沒有限制，但可能會受您為日誌分析工作區選擇的定價層的影響。 請參閱[定價詳細資訊](https://azure.microsoft.com/pricing/details/monitor/)。

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>如何訪問 Azure 監視器收集的資料？
見解和解決方案為處理存儲在 Azure 監視器中的資料提供了自訂體驗。 您可以使用用 Kusto 查詢語言 （KQL） 編寫的日誌查詢直接處理日誌資料。 在 Azure 門戶中，可以使用日誌分析編寫和執行查詢並互動式分析資料。 使用指標資源管理器分析 Azure 門戶中的指標。 請參閱[在 Azure 監視器中分析日誌資料](log-query/log-query-overview.md)，然後[開始使用 Azure 指標資源管理器](platform/metrics-getting-started.md)。

## <a name="solutions-and-insights"></a>解決方案和見解

### <a name="what-is-an-insight-in-azure-monitor"></a>Azure 監視器中的見解是什麼？
見解為特定的 Azure 服務提供自訂的監視體驗。 它們使用與 Azure 監視器中的其他功能相同的指標和日誌，但可能會收集其他資料並在 Azure 門戶中提供獨特的體驗。 請參閱[Azure 監視器 中的見解](insights/insights-overview.md)。

要查看 Azure 門戶中的見解，請參閱 **"監視器"** 功能表的 **"見解"** 部分或服務功能表的 **"監視"** 部分。

### <a name="what-is-a-solution-in-azure-monitor"></a>Azure 監視器中的解決方案是什麼？
監視解決方案是打包的邏輯集，用於根據 Azure 監視器功能監視特定應用程式或服務。 它們在 Azure 監視器中收集日誌資料，並使用 Azure 門戶中的常見體驗為其分析提供日誌查詢和視圖。 請參閱[Azure 監視器 中的監視解決方案](insights/solutions.md)。

要查看 Azure 門戶中的解決方案，請在 **"監視器"** 功能表的 **"見解"** 部分中按一下 **"更多**"。 按一下"**添加**"以向工作區添加其他解決方案。

## <a name="logs"></a>記錄

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Azure 監視器日誌和 Azure 資料資源管理器之間的區別是什麼？
Azure 資料總管是一項快速又可高度調整的資料探索服務，可用於處理記錄和遙測資料。 Azure 監視器日誌構建在 Azure 資料資源管理器之上，使用相同的 Kusto 查詢語言 （KQL），但存在一些細微差異。 請參閱[Azure 監視器日誌查詢語言差異](log-query/data-explorer-difference.md)。

### <a name="how-do-i-retrieve-log-data"></a>如何檢索日誌資料？
使用使用 Kusto 查詢語言 （KQL） 編寫的日誌查詢從日誌分析工作區檢索所有資料。 您可以編寫自己的查詢或使用包含特定應用程式或服務的日誌查詢的解決方案和見解。 請參閱[Azure 監視器 中的日誌查詢概述](log-query/log-query-overview.md)。

### <a name="what-is-a-log-analytics-workspace"></a>什麼是 Log Analytics 工作區？
Azure 監視器收集的所有日誌資料都存儲在日誌分析工作區中。 工作區本質上是一個容器，用於從各種源收集日誌資料。 您可能具有用於所有監視資料的單個日誌分析工作區，或者可能具有多個工作區的要求。 請參閱[設計 Azure 監視器日誌部署](platform/design-logs-deployment.md)。

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>是否可以將現有的日誌分析工作區移動到另一個 Azure 訂閱？
可以在資源組或訂閱之間移動工作區，但不能移動到其他區域。 請參閱[將日誌分析工作區移動到不同的訂閱或資源組](platform/move-workspace.md)。

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>為什麼我無法在日誌分析中看到查詢資源管理器和保存按鈕？

[當查詢範圍](log-query/scope.md)設置為特定資源時，**查詢資源管理器**、**保存**和**新建警報規則**按鈕不可用。 要創建警報、保存或載入查詢，日誌分析必須限定為工作區。 要在工作區上下文中打開日誌分析，請從**Azure 監視器**功能表中選擇 **"日誌**"。 已選取上次使用的工作區，但您可以選取任何其他工作區。 請參閱[Azure 監視器日誌分析中的日誌查詢範圍和時間範圍](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>為什麼在從 VM 打開日誌分析時，我會收到錯誤："註冊此訂閱的資源供應商'Microsoft.Insights'以啟用此查詢"？ 
許多資來源提供者都將自動註冊，但您可能需要手動註冊某些資來源提供者。 註冊範圍一律是訂用帳戶。 如需詳細資訊，請參閱[資源提供者和類型](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)。

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>為什麼從 VM 打開日誌分析時沒有訪問錯誤訊息？ 
若要檢視 VM 記錄，您必須獲得存放 VM 記錄的工作區讀取權限。 在這些情況下，系統管理員必須授與您 Azure 權限。

## <a name="alerts"></a>警示

### <a name="what-is-an-alert-in-azure-monitor"></a>Azure 監視器中的警報是什麼？
當您的監視資料中發現重要條件時，警示會主動通知您。 它們可讓您在系統使用者注意到問題之前，找出並解決問題。 警報有多種類型：

- 指標 - 指標值超過閾值。
- 日誌查詢 - 日誌查詢的結果與定義的條件匹配。
- 活動日誌 - 活動日誌事件與定義的條件匹配。
- Web 測試 - 可用性測試與定義的條件匹配的結果。


請參閱[Microsoft Azure 中的警報概述](platform/alerts-overview.md)。


### <a name="what-is-an-action-group"></a>什麼是行動組？
操作組是警報可以觸發的通知和操作的集合。 多個警報可以使用單個操作組，允許您利用常見的通知和操作集。 請參閱[在 Azure 門戶中創建和管理操作組](platform/action-groups.md)。


### <a name="what-is-an-action-rule"></a>什麼是操作規則？
操作規則允許您修改一組符合特定條件的警報的行為。 這允許您執行維護時段內禁用警報操作等要求。 還可以將操作組應用於一組警報，而不是將其直接應用於警報規則。 請參閱[操作規則](platform/alerts-action-rules.md)。

## <a name="agents"></a>代理程式

### <a name="does-azure-monitor-require-an-agent"></a>Azure 監視器是否需要代理？
只需代理即可從虛擬機器中的作業系統和工作負載收集資料。 虛擬機器可以位於 Azure、另一個雲環境或本地。 請參閱[Azure 監視器代理的概述](platform/agents-overview.md)。


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Azure 監視器代理之間的區別是什麼？
Azure 診斷擴展適用于 Azure 虛擬機器，並將資料收集到 Azure 監視器指標、Azure 存儲和 Azure 事件中心。 日誌分析代理適用于 Azure 中的虛擬機器、另一個雲環境或本地虛擬機器，並將資料收集到 Azure 監視器日誌。 依賴項代理需要日誌分析代理並收集進程詳細資訊和依賴項。 請參閱[Azure 監視器代理的概述](platform/agents-overview.md)。


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>我的代理流量是否使用我的 ExpressRoute 連接？
到 Azure 監視器的流量使用 Microsoft 對等快速路由電路。 有關不同類型的快速路由流量的說明，請參閱[ExpressRoute 文檔](../expressroute/expressroute-faqs.md#supported-services)。 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>如何確認日誌分析代理能夠與 Azure 監視器通信？
從代理電腦上的控制台中，選擇 **"安全&設置**"，**微軟監控代理**。 在**Azure 日誌分析 （OMS）** 選項卡下，綠色核取記號圖示確認代理能夠與 Azure 監視器通信。 黃色警告圖示表示代理出現問題。 一個常見原因是**Microsoft 監視代理**服務已停止。 使用服務控制管理員來重新啟動服務。

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>如何阻止日誌分析代理與 Azure 監視器通信？
對於直接連接到日誌分析的代理，打開控制台並選擇 **"安全&設置**"，**即 Microsoft 監視代理**。 在**Azure 日誌分析 （OMS）** 選項卡下，刪除列出的所有工作區。 在"系統中心操作管理器"中，從日誌分析託管電腦清單中刪除電腦。 Operations Manager 會將代理程式的設定更新為不再向 Log Analytics 回報。 

### <a name="how-much-data-is-sent-per-agent"></a>每個代理程式會傳送多少資料？
每個代理程式所傳送的資料量取決於：

* 您已啟用的解決方案
* 記錄和要收集之效能計數器的數目
* 記錄中的資料量

有關詳細資訊，請參閱[使用 Azure 監視器日誌管理使用方式和成本](platform/manage-cost-storage.md)。

對於可執行 WireData 代理程式的電腦，請使用下列查詢查看正在傳送的資料量：

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>向 Azure 監視器發送資料時，Microsoft 管理代理 （MMA） 使用了多少網路頻寬？
頻寬是關於傳送的資料量的功能。 透過網路傳送資料時，會壓縮資料。


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>當日志分析代理的資料收集停止時，如何通知我？

使用[建立新記錄警示](platform/alerts-metric.md)中所述的步驟，以在資料收集停止時收到通知。 對警報規則使用以下設置：

- **定義警示準則**：將日誌分析工作區指定為資源目標。
- **警示準則** 
   - **信號名稱**：*自訂日誌搜索*
   - **搜索查詢**：`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **警報邏輯**：**基於***結果數*，**條件***大於*，**閾值** *0*
   - **評估基於**：**期間 （以分鐘）** *30*，**頻率 （以分鐘）** *10*
- **定義警示詳細資料** 
   - **名稱**：*資料收集已停止*
   - **嚴重性**：*警告*

指定現有或新的[操作組](platform/action-groups.md)，以便在日誌警報與條件匹配時，如果活動訊號丟失超過 15 分鐘，則會通知您。


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Azure 監視器代理的防火牆要求是什麼？
有關防火牆要求的詳細資訊，請參閱[網路防火牆要求](platform/log-analytics-agent.md#network-firewall-requirements)。


## <a name="visualizations"></a>視覺效果

### <a name="why-cant-i-see-view-designer"></a>為什麼我看不到視圖設計器？

視圖設計器僅適用于在日誌分析工作區中分配具有參與者許可權或更高版本的使用者。

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>組態問題
*我在設定下列項目時有問題：*

* [.NET 應用程式](app/asp-net-troubleshoot-no-data.md)
* [監視已在執行的應用程式](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure 診斷](platform/diagnostics-extension-to-application-insights.md)
* [JAVA Web 應用](app/java-troubleshoot.md)

我的伺服器沒有傳回資料**

* [設定防火牆例外狀況](app/ip-addresses.md)
* [設定 ASP.NET 伺服器](app/monitor-performance-live-website-now.md)
* [設定 Java 伺服器](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>我是否可以搭配 ... 來使用 Application Insights 嗎？

* [Azure VM 或 Azure 虛擬機器縮放集中的 IIS 伺服器上的 Web 應用](app/azure-vm-vmss-apps.md)
* [IIS 伺服器 (內部部署或 VM 中) 上的 Web 應用程式](app/asp-net.md)
* [JAVA Web 應用](app/java-get-started.md)
* [Node.js 應用程式](app/nodejs.md)
* [Azure 上的 Web 應用程式](app/azure-web-apps.md)
* [Azure 上的雲端服務](app/cloudservices.md)
* [在 Docker 中執行的應用程式伺服器](app/docker.md)
* [單一頁面 Web 應用程式](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows 傳統型應用程式](app/windows-desktop.md)
* [其他平臺](app/platforms.md)

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
* （僅限新專案 - 如果將[應用程式見解添加到現有專案][start]，則必須手動執行此操作。將程式碼片段插入到用戶端和伺服器代碼中，以便使用應用程式見解資源識別碼 初始化它們。 例如，在 MVC 應用中，代碼插入到母版頁視圖/共用/\_佈局.cshtml 中。

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
* [相依項目](app/asp-net-dependencies.md)。 對下列各項的呼叫：SQL Database、對外部服務的 HTTP 呼叫、Azure Cosmos DB、資料表、Blob 儲存體及佇列。 
* [例外狀況](app/asp-net-exceptions.md)和堆疊追蹤。
* [效能計數器](app/performance-counters.md)- 如果使用[狀態監視器](app/monitor-performance-live-website-now.md)、[應用服務的 Azure 監視](app/azure-web-apps.md)[、VM 或虛擬機器縮放集的 Azure 監視](app/azure-vm-vmss-apps.md)，或者[應用程式見解收集編寫器](app/java-collectd.md)。
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
* [日誌分析](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>我是否可以篩選掉或修改某些遙測？

是，您可以在伺服器中撰寫：

* 「遙測處理器」以在從您的應用程式傳送選取的遙測項目之前，先篩選它們的屬性或為它們新增屬性。
* 「初始設定式」以為所有遙測項目新增屬性。

深入了解 [ASP.NET](app/api-filtering-sampling.md) 或 [Java](app/java-filter-telemetry.md)。

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>如何計算城市、國家/地區和其他地理位置資料？

我們會使用 [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) 來查詢 Web 用戶端的 IP 位址 (IPv4 或 IPv6)。

* 瀏覽器遙測：我們會收集傳送者的 IP 位址。
* 伺服器遙測：Application Insights 模組會收集用戶端 IP 位址。 如果已設定 `X-Forwarded-For`，則不會收集該位址。
* 要瞭解有關如何在應用程式見解中收集 IP 位址和地理位置資料的詳細資訊，請參閱[本文](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)。


您可以設定 `ClientIpHeaderTelemetryInitializer` 以從不同的標頭取得 IP 位址。 例如，在某些系統中，Proxy、負載平衡器或 CDN 會將它移至 `X-Originating-IP`。 [深入了解](https://apmtips.com/blog/2016/07/05/client-ip-address/)。

您可以[使用 Power BI](app/export-power-bi.md ) 在地圖上顯示您的要求遙測資料。


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>資料保留在入口網站多久的時間？ 是否安全？
請參閱[資料保留和隱私權][data]。

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>當伺服器或設備與 Azure 失去連接時，應用程式洞察的遙測會發生什麼情況？

我們所有的 SDK，包括 Web SDK，都包括"可靠的傳輸"或"強大的傳輸"。 當伺服器或設備與 Azure 失去連接時，遙測資料將存儲在檔案系統（伺服器 SDK）或 HTML5 會話存儲 （Web SDK）[中。](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) SDK 將定期重試發送此遙測資料，直到我們的引入服務認為它"過時"（日誌為 48 小時，指標為 30 分鐘）。 過時的遙測資料將被刪除。 在某些情況下，例如當本機存放區已滿時，不會發生重試。


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>可以在遙測中傳送個人資料嗎？

如果您的程式碼會傳送這類資料，就有可能。 如果堆疊追蹤中的變數包含個人資料，也可能發生這種情況。 您的開發小組應該進行風險評估，以確保正確處理個人資料。 [深入了解資料保留和隱私權](app/data-retention-privacy.md)。

在查閱地理位置屬性之後，用戶端 Web 位址的**所有**八位元資料一律會設定為 0。

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>我的檢測金鑰在我的網頁源中可見。 

* 這在監視解決方案中是常見的做法。
* 無法使用它來竊取您的資料。
* 可以使用它來扭曲您的資料或觸發警示。
* 我們尚未聽到有任何客戶有這類問題。

您可以：

* 對用戶端和伺服器資料使用兩個單獨的檢測金鑰（單獨的應用程式見解資源）。 Or
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

Azure 警示僅針對計量。 請建立一個會在每次事件發生時超出值臨界值的自訂計量。 然後在該計量上設定警示。 每當指標在任一方向上超過閾值時，您都會收到通知;無論初始值是高還是低，直到第一次交叉，您都得不到通知;總是有幾分鐘的延遲。

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

#### <a name="gateway-redirect"></a>閘道重定向

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
> 應用程式 IdProvider 在 v2.6.0 中開始可用。



#### <a name="proxy-passthrough"></a>代理傳遞

可以通過配置電腦級別或應用程式級代理來實現代理傳遞。
有關詳細資訊，請參閱 dotnet 關於[預設代理](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)的文章。
 
 例如 Web.config：
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

### <a name="what-does-other-processes-represent-under-the-node-view"></a>"其他*進程*"視圖下表示什麼？

**其他進程**旨在説明您清楚地瞭解節點上高資源使用的根本原因。 這使您能夠區分容器化流程和非容器化流程的使用方式。

這些**其他流程**是什麼？ 

這些是在節點上運行的非容器化進程。  

我們如何計算？

**其他流程** = *容器化流程的**CAdvisor* - 使用率

**其他流程**包括：

- 自我管理或管理的庫伯奈斯非容器化流程 

- 容器運行時進程  

- Kubelet  

- 在節點上運行的系統進程 

- 在節點硬體或 VM 上運行的其他非庫伯內特工作負載 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>在查詢容器日誌表時，看不到填充的"圖像和名稱"屬性值。

對於代理版本 ciprod12042019 及更高版本，預設情況下，不會為每個日誌行填充這兩個屬性，以最大程度地降低收集的日誌資料產生的成本。 有兩個選項用於查詢包含這些屬性及其值的表：

#### <a name="option-1"></a>選項 1 

加入其他表，在結果中包括這些屬性值。

通過在容器 ID 屬性上聯接來修改查詢以包括```ContainerInventory```表中的圖像和 ImageTag 屬性。 您可以通過在"容器 ID"屬性上聯接 KubepodInventory 表的"包含名稱"欄位，包括"名稱"屬性（如之前在```ContainerLog```表中出現）。這是推薦的選項。

下面的示例是一個示例詳細查詢，它解釋了如何使用聯接獲取這些欄位值。

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

為每個容器日誌行重新啟用這些屬性的集合。

如果第一個選項由於涉及的查詢更改而不方便，則可以通過在代理配置映射中啟用代理配置映射中的設置```log_collection_settings.enrich_container_logs```來重新啟用收集這些欄位，如[資料收集配置設置](insights/container-insights-agent-config.md)中所述。

> [!NOTE]
> 對於具有 50 個以上節點的大型群集，不建議使用第二個選項，因為它從群集中的每個節點生成 API 伺服器調用以執行此擴充。 此選項還會增加收集的每個日誌行的資料大小。

### <a name="can-i-view-metrics-collected-in-grafana"></a>我可以查看在格拉法納收集的指標嗎？

容器的 Azure 監視器支援查看存儲在 Grafana 儀表板中的日誌分析工作區中的指標。 我們提供了一個範本，您可以從 Grafana 的[儀表板存儲庫](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)下載該範本，以便開始並參考，以説明您瞭解如何從受監視的群集中查詢其他資料，以便在自訂 Grafana 儀表板中視覺化。 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>是否可以使用 Azure 監視器監視我的 AKS 引擎群集來監視容器？

容器的 Azure 監視器支援監視部署到 Azure 上託管的 AKS 引擎（以前稱為 ACS 引擎）群集的容器工作負載。 有關此方案啟用監視所需的步驟的詳細資訊和概述，請參閱[使用 Azure 監視器訪問 AKS 引擎的容器](https://github.com/microsoft/OMS-docker/tree/aks-engine)。

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>為什麼我看不到日誌分析工作區中的資料？

如果您在每天的某個時間無法在日誌分析工作區中看到任何資料，則可能已達到預設的 500 MB 限制或指定用於控制每日收集的資料量的每日上限。 當滿足當天的限制時，資料收集將停止，僅在第二天恢復。 要查看資料使用方式並根據您的預期使用模式更新到不同的定價層，請參閱[記錄資料使用方式和成本](platform/manage-cost-storage.md)。 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>容器清單表中指定的容器狀態是什麼？

ContainerInventory 資料表包含已停止和執行中容器的相關資訊。 此資料表由代理程式內的工作流程填入，該工作流程會查詢所有 (執行中和已停用) 容器的 Docker，並將該資料轉送到 Log Analytics 工作區。
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>如何解決*缺少的訂閱註冊*錯誤？

如果您收到**錯誤缺少 Microsoft.操作管理訂閱註冊**，可以通過註冊資來源提供者**Microsoft.操作管理**來解決它。 您可以在[這裡](../azure-resource-manager/templates/error-register-resource-provider.md)找到作法的相關文件。

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>是否支援支援支援 RBAC 的 AKS 群集？

容器監視解決方案不支援 RBAC，但容器的 Azure 監視器支援它。 解決方案詳細資料頁面不會在顯示這些叢集資料的刀鋒視窗中顯示正確的資訊。

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>我如何透過 Helm 在 kube 系統命名空間中啟用容器的記錄檔收集？

kube 系統命名空間中容器的記錄檔收集預設為停用。 透過設定 omsagent 上的環境變數，可啟用記錄檔收集。 有關詳細資訊，請參閱容器 GitHub 頁的[Azure 監視器](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)。 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>如何將 omsagent 更新為最新的發行版本？

若要了解如何升級代理程式，請參閱[代理程式管理](insights/container-insights-manage-agent.md)。

### <a name="how-do-i-enable-multi-line-logging"></a>如何啟用多行記錄？

目前，容器的 Azure 監視器不支援多行日誌記錄，但有可用的解決方法。 您可以設定使用 JSON 格式寫入所有的服務，之後 Docker/Moby會將所有服務寫成一行。

例如，您可以將記錄檔包裝在 JSON 物件，如以下範例中範例 node.js 應用程式所示：

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

此資料在查詢日誌時，與 Azure 監視器中日誌中的以下示例類似：

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

有關此問題的詳細資訊，請查看以下[GitHub 連結](https://github.com/moby/moby/issues/22920)。

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>啟用即時日誌時如何解決 Azure AD 錯誤？ 

您可能會看到以下錯誤：**請求中指定的回復 URL 與為應用程式佈建的回復 URL 不匹配："<應用程式 ID"。\> ** 解決方法可以在"[如何使用容器 Azure 監視器即時查看容器資料](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)"一文中找到。 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>為什麼在入職後無法升級群集？

如果在為 AKS 群集啟用 Azure 監視器的容器後，將刪除群集正在將其資料發送到的日誌分析工作區，則在嘗試升級群集時，該群集將失敗。 要解決此問題，您必須禁用監視，然後重新啟用它引用訂閱中不同的有效工作區。 當您嘗試再次執行群集升級時，它應該成功處理和完成。  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>我需要為代理打開/白名單哪些埠和域？

有關使用 Azure、Azure US 政府和 Azure 中國 21Vianet 雲的容器化代理所需的代理和防火牆配置資訊，請參閱[網路防火牆要求](insights/container-insights-onboard.md#network-firewall-requirements)。

## <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
此 Microsoft 常見問題集是適用於 VM 的 Azure 監視器常見問題清單。 若您有任何關於解決方案的其他問題，請前往[討論論壇](https://feedback.azure.com/forums/34192--general-feedback)並張貼您的問題。 當問到常見問題時，我們會將其新增至此文章，以便其他人可以快速輕鬆地找到此問題。

### <a name="can-i-onboard-to-an-existing-workspace"></a>我可以將現有的工作區上線嗎？
如果您的虛擬機器已經連線到 Log Analytics 工作區，則您可以在上線到適用於 VM 的 Azure 監視器時繼續使用該工作區，但前提是它位於[此處](insights/vminsights-enable-overview.md#prerequisites)所列的其中一個支援區域。


### <a name="can-i-onboard-to-a-new-workspace"></a>我可以上線到新的工作區嗎？ 
如果您的 VM 目前並未連線到現有的 Log Analytics 工作區，您需要建立新的工作區來儲存資料。 如果您透過 Azure 入口網站為適用於 VM 的 Azure 監視器設定單一 Azure VM，建立新的預設工作區即會自動完成。

如果選擇使用基於腳本的方法，這些步驟將[包含在使用 Azure PowerShell 或資源管理器範本的"為 VM 啟用 Azure 監視器"範本](insights/vminsights-enable-at-scale-powershell.md)一文中。 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>如果我的 VM 已經向現有的工作區回報，我該怎麼做？
如果您已經從虛擬機器收集資料，則您可能已經將它設為向現有的 Log Analytics 工作區回報資料。  只要該工作區位於我們支援的其中一個區域，您就可以針對該預先存在的工作區啟用適用於 VM 的 Azure 監視器。  如果您已在使用的工作區不在我們支援的區域之一，此時您將無法將 VM 的 Azure 監視器載板。  我們正積極努力地支援更多區域。


### <a name="why-did-my-vm-fail-to-onboard"></a>為什麼無法將我的 VM 上線？
從 Azure 入口網站將 Azure VM 上線時，請執行下列步驟：

* 如果已選取該選項，即會建立預設的 Log Analytics 工作區。
* 如果決定需要使用 Log Analytics 代理程式，可使用 VM 延伸模組在 Azure VM 上進行安裝。  
* 如果決定需要使用適用於 VM 的 Azure 監視器對應 Dependency Agent，可使用延伸模組在 Azure VM 上進行安裝。 

在上線過程中，我們會檢查上述每一項的狀態，以便在入口網站中將通知狀態傳回給您。 設定工作區與代理程式安裝通常需要 5 至 10 分鐘。 在門戶中查看監視資料需要額外 5 到 10 分鐘。  

如果您已初始上線，並看到一則訊息指出必須將 VM 上線，則允許 VM 最多 30 分鐘的時間來完成此程序。 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>我的 VM 的性能圖表中看不到部分或全部資料
我們的性能圖表已更新，以使用存儲在*InsightsMetrics*表中的資料。  要查看這些圖表中的資料，您需要升級才能使用新的 VM 見解解決方案。  有關其他資訊，請參閱我們的[GA 常見問題解答](insights/vminsights-ga-release-faq.md)。

如果在磁片表或某些性能圖表中看不到效能資料，則效能計數器可能無法在工作區中配置。 若要解決此問題，請執行下列 [PowerShell 指令碼](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)。


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>適用於 VM 的 Azure 監視器對應功能與服務對應有何不同？
適用於 VM 的 Azure 監視器對應功能會以服務對應為根據，但有下列差異：

* [對應] 檢視可以從 VM 刀鋒視窗，以及從 Azure 監視器下方適用於 VM 的 Azure 監視器進行存取。
* [對應] 中的連線現在可供點選，並在所選連線的側邊面板中顯示連線計量資料的檢視。
* 有一個新的 API 可用來建立對應，以便為更複雜的對應提供更好的支援。
* 受監視的 VM 現在包含於用戶端群組節點中，而環圈圖會顯示群組中受監視與未受監視的虛擬機器比例。  它也可以在展開群組時用來篩選機器清單。
* 受監視的虛擬機器現在包含於伺服器連接埠群組節點中，而環圈圖會顯示群組中受監視與未受監視的機器比例。  它也可以在展開群組時用來篩選機器清單。
* 對應樣式已更新，以便與 Application Insights 的應用程式對應更為一致。
* 側面板已更新，並且沒有服務映射中支援的完整集成集 - 更新管理、更改跟蹤、安全和服務台。 
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

適用於 Azure 虛擬機器的概觀頁面會根據客體 VM 中主機的活動量值來顯示圖表。  至於 [Azure VM 概觀] 上的網路圖表，它只會顯示將收費的網路流量。  這不包括虛擬網路間流量。  為 VM Azure 監視器顯示的資料和圖表基於來自來賓 VM 的資料，網狀圖表顯示該 VM 入站和出站的所有 TCP/IP 流量，包括虛擬間網路。

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>如何測量 VMConnect 中存儲並顯示在連接面板和活頁簿中的資料的回應時間？

回應時間是近似值。 由於我們不檢測應用程式的代碼，所以我們並不真正知道請求何時開始以及回應何時到達。 相反，我們會觀察在連接上發送的資料，然後看到該連接上返回的資料。 我們的代理跟蹤這些發送和接收並嘗試配對：發送序列，後跟接收序列被解釋為請求/回應對。 這些操作之間的計時是回應時間。 它將包括網路延遲和伺服器處理時間。

此近似值適用于基於請求/回應的協定：連接上發出單個請求，單個回應到達。 HTTP（S）（沒有管道），但對其他協定不滿意。"

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>如果我採用 Log Analytics 免費定價方案，有何限制？
如果您使用「免費」** 定價層設定了具有 Log Analytics 工作區的 Azure 監視器，則適用於 VM 的 Azure 監視器的對應功能僅支援五部已連線到工作區的電腦。 如果您有五部連線至免費工作區的 VM，您可中斷其中一部 VM 的連線，然後連線新的 VM，則新的 VM 不受監視，而且不會反映於 [對應] 頁面。  

在這種情況下，當您打開 VM 並從左側窗格中選擇 **"見解"** 時，即使已在 VM 上安裝"**立即試用"** 選項，系統也會提示您。  不過，如果此 VM 並未在適用於 VM 的 Azure 監視器上線，就不會以通常會出現的選項提示您。 


## <a name="next-steps"></a>後續步驟
如果您的問題未在此處回答，您可以參考以下論壇，以查找其他問題和答案。

- [日誌分析](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

有關 Azure 監視器的一般回饋，請訪問[回饋論壇](https://feedback.azure.com/forums/34192--general-feedback)。
