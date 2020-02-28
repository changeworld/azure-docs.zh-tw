---
title: Azure 監視器中資料的來源 | Microsoft Docs
description: 描述可用於監視 Azure 資源 (以及在資源上執行的應用程式) 健康情況和效能的資料。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: b2ec9fd70d1eb64c5968de0312941bfbc98d3033
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670520"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure 監視器的監視資料來源
Azure 監視器是以常見的[監視資料平臺](data-platform.md)為基礎，其中包含[記錄](data-platform-logs.md)和[計量](data-platform-metrics.md)。 將資料收集到這個平臺可讓您使用 Azure 監視器中的一組常用工具來分析多個資源的資料。 監視資料也可能會傳送到其他位置以支援特定案例，某些資源可能會在其他位置寫入至記錄或計量。

本文說明除了 Azure 資源所建立的監視資料以外，Azure 監視器所收集的監視資料的不同來源。 提供有關將此資料收集到不同位置所需之設定的詳細資訊的連結。

## <a name="application-tiers"></a>應用程式層

來自 Azure 應用程式的監視資料來源可以組織成各層，最高層是您的應用程式本身，而較低層則是 Azure 平臺的元件。 從每一層存取資料的方法會有所不同。 下表摘要說明應用層，而每一層中的監視資料來源會在下列各節中提供。 請參閱[監視 Azure 中的資料位置](data-locations.md)，以取得每個資料位置的描述，以及您可以如何存取其資料。


![監視層](../media/overview/overview.png)


### <a name="azure"></a>Azure
下表簡要說明 Azure 特有的應用層。 遵循連結以取得下列各節中各項的進一步詳細資料。

| 層 | 描述 | 收集方法 |
|:---|:---|:---|
| [Azure 租使用者](#azure-tenant) | 租用戶層級 Azure 服務的作業相關資料，例如 Azure Active Directory。 | 在入口網站中查看 AAD 資料，或使用租使用者診斷設定將集合設定為 Azure 監視器。 |
| [Azure 訂用帳戶](#azure-subscription) | 與 Azure 訂用帳戶中跨資源服務的健康情況和管理相關的資料，例如 Resource Manager 和服務健康狀態。 | 在入口網站中觀看，或使用記錄設定檔將集合設定為 Azure 監視器。 |
| [Azure 資源](#azure-resources) |  有關每個 Azure 資源之作業和效能的資料。 | 自動收集的計量，請參閱計量瀏覽器。<br>設定診斷設定以收集 Azure 監視器中的記錄。<br>監視解決方案和深入解析，可提供特定資源類型的更詳細監視。 |

### <a name="azure-other-cloud-or-on-premises"></a>Azure、其他雲端或內部部署 
下表簡要說明可能位於 Azure、另一個雲端或內部部署中的應用層。 遵循連結以取得下列各節中各項的進一步詳細資料。

| 層 | 描述 | 收集方法 |
|:---|:---|:---|
| [作業系統（來賓）](#operating-system-guest) | 計算資源上作業系統的相關資料。 | 安裝 Log Analytics 代理程式以收集用戶端資料來源到 Azure 監視器和相依性代理程式，以收集支援適用於 VM 的 Azure 監視器的相依性。<br>若為 Azure 虛擬機器，請安裝 Azure 診斷擴充功能，以將記錄和計量收集到 Azure 監視器中。 |
| [應用程式代碼](#application-code) | 實際應用程式和程式碼的效能和功能相關資料，包括效能追蹤、應用程式記錄和使用者遙測。 | 檢測您的程式碼，以將資料收集到 Application Insights。 |
| [自訂來源](#custom-sources) | 來自外部服務或其他元件或裝置的資料。 | 從任何 REST 用戶端將記錄檔或計量資料收集到 Azure 監視器。 |

## <a name="azure-tenant"></a>Azure 租用戶
與您 Azure 租用戶相關的遙測，會從整個租用戶服務 (例如 Azure Active Directory) 收集。

![Azure 租用戶集合](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 稽核記錄
[Azure Active Directory 報告](../../active-directory/reports-monitoring/overview-reports.md)包含在特定租用戶內所進行登入活動的歷程記錄，以及所做變更的稽核線索。 

| Destination | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄 | 設定要在 Azure 監視器中收集 Azure AD 記錄，以使用其他監視資料進行分析。 | [整合 Azure AD 記錄與 Azure 監視器記錄（預覽）](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 儲存體 | 將 Azure AD 記錄匯出至 Azure 儲存體以進行封存。 | [教學課程：將 Azure AD 記錄封存到 Azure 儲存體帳戶（預覽）](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中樞 | 使用事件中樞，將 Azure AD 記錄串流至其他位置。 | [教學課程：將 Azure Active Directory 記錄串流至 Azure 事件中樞（預覽）](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。 |



## <a name="azure-subscription"></a>Azure 訂用帳戶
與您 Azure 訂用帳戶的健康情況和作業相關的遙測。

![Azure 訂用帳戶](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 活動記錄 
[Azure 活動記錄](platform-logs-overview.md)包含服務健康狀態記錄，以及對您的 Azure 訂用帳戶中的資源所做的任何設定變更記錄。 所有 Azure 資源都會有活動記錄，也代表了資源的_外部_檢視。

| Destination | 描述 | 參考 |
|:---|:---|
| 活動記錄檔 | 活動記錄會收集到自己的資料存放區中，您可以從 [Azure 監視器] 功能表查看，或用來建立活動記錄警示。 | [查詢 Azure 入口網站中的活動記錄](activity-log-view.md#azure-portal) |
| Azure 監視器記錄 | 設定 Azure 監視器記錄來收集活動記錄，以使用其他監視資料進行分析。 | [在 Azure 監視器的 Log Analytics 工作區中收集並分析 Azure 活動記錄](activity-log-collect.md) |
| Azure 儲存體 | 將活動記錄檔匯出至 Azure 儲存體以進行封存。 | [封存活動記錄](activity-log-export.md#archive-activity-log)  |
| 事件中心 | 使用事件中樞將活動記錄串流至其他位置 | [將活動記錄串流至事件中樞](activity-log-export.md#stream-activity-log-to-event-hub)。 |

### <a name="azure-service-health"></a>Azure 服務健康狀態
[Azure 服務健康狀態](../../service-health/service-health-overview.md)提供您有關應用程式和資源依賴的訂用帳戶中，Azure 服務健康狀態的資訊。

| Destination | 描述 | 參考 |
|:---|:---|:---|
| 活動記錄檔<br>Azure 監視器記錄 | 服務健康狀態記錄會儲存在 Azure 活動記錄中，因此您可以在 Azure 入口網站中加以查看，或執行可使用活動記錄來執行的任何其他活動。 | [使用 Azure 入口網站檢視服務健康情況通知](service-notifications.md) |


## <a name="azure-resources"></a>Azure 資源
計量和資源記錄提供 Azure 資源_內部_作業的相關資訊。 這些適用于大部分的 Azure 服務，而監視解決方案和深入解析會收集特定服務的其他資料。

![Azure 資源集合](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>平臺計量 
大部分的 Azure 服務會傳送[平臺計量](data-platform-metrics.md)，以直接反映計量資料庫的效能和作業。 特定[計量會因為各種類型的資源而異](metrics-supported.md)。 

| Destination | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器計量 | 平臺計量會寫入 Azure 監視器的計量資料庫，而不會進行任何設定。 從計量瀏覽器存取平臺計量。  | [開始使用 Azure 計量瀏覽器](metrics-getting-started.md)<br>[支援 Azure 監視器的計量](metrics-supported.md) |
| Azure 監視器記錄 | 使用 Log Analytics 將平臺計量複製到記錄，以進行趨勢和其他分析。 | [Azure 診斷 direct to Log Analytics](resource-logs-collect-workspace.md) |
| 事件中心 | 使用事件中樞將計量串流至其他位置。 |[將 Azure 監視資料串流至事件中樞以供外部工具取用](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>資源記錄
[資源記錄](platform-logs-overview.md)可讓您深入瞭解 Azure 資源的_內部_作業。  資源記錄會自動建立，但您必須建立診斷設定，以指定要針對每個資源收集的目的地。

資源記錄的設定需求和內容會因資源類型而異，但並非所有服務都已建立。 如需每個服務的詳細資訊和詳細設定程式的連結，請參閱[支援的服務、架構和 Azure 資源記錄的類別](diagnostic-logs-schema.md)。 如果這篇文章中未列出該服務，則該服務目前不會建立資源記錄。

| Destination | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄 | 將資源記錄檔傳送至 Azure 監視器記錄，以使用其他收集的記錄資料進行分析。 | [在 Azure 監視器的 Log Analytics 工作區中收集 Azure 資源記錄](resource-logs-collect-storage.md) |
| 儲存體 | 將資源記錄檔傳送至 Azure 儲存體以進行封存。 | [封存 Azure 資源記錄](resource-logs-collect-workspace.md) |
| 事件中心 | 使用事件中樞將資源記錄串流至其他位置。 |[將 Azure 資源記錄串流至事件中樞](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>作業系統（來賓）
Azure、其他雲端和內部部署中的計算資源具有要監視的客體作業系統。 安裝一或多個代理程式時，您可以將來自來賓的遙測收集到 Azure 監視器，以使用與 Azure 服務本身相同的監視工具進行分析。

![Azure 計算資源集合](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 診斷擴充功能
啟用 Azure 虛擬機器的 Azure 診斷延伸模組，可讓您從 Azure 計算資源的客體作業系統收集記錄和計量，包括 Azure 雲端服務（傳統） Web 和背景工作角色、虛擬機器、虛擬機器擴展集和 Service Fabric。

| Destination | 描述 | 參考 |
|:---|:---|:---|
| 儲存體 | Azure 診斷延伸模組一律會寫入 Azure 儲存體帳戶。 | [安裝和設定 Windows Azure 診斷擴充功能（WAD）](diagnostics-extension-windows-install.md)<br>[使用 Linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure 監視器計量 | 當您設定診斷擴充功能來收集效能計數器時，會將它們寫入 Azure 監視器計量資料庫中。 | [使用 Windows 虛擬機器的 Resource Manager 範本，將客體作業系統計量傳送至 Azure 監視器公制存放區](collect-custom-metrics-guestos-resource-manager-vm.md) |
| 事件中心 | 設定診斷延伸模組，使用事件中樞將資料串流至其他位置。  | [使用事件中樞串流 Azure 診斷資料](diagnostics-extension-stream-event-hubs.md)<br>[使用 Linux 診斷擴充功能來監視計量和記錄](../../virtual-machines/extensions/diagnostics-linux.md) |
| Application Insights 記錄 | 從支援您的應用程式的計算資源收集記錄和效能計數器，以使用其他應用程式資料進行分析。 | [將雲端服務、虛擬機器或 Service Fabric 診斷資料傳送至 Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics 代理程式 
安裝 Log Analytics 代理程式，以全面監視和管理您的 Windows 或 Linux 虛擬機器。 虛擬機器可以在 Azure、其他雲端或內部部署中執行。

| Destination | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄 | Log Analytics 代理程式會直接或透過 System Center Operations Manager 連線至 Azure 監視器，並可讓您從所設定的資料來源或從提供額外深入解析應用程式的監視解決方案收集資料正在虛擬機器上執行。 | [Azure 監視器中的代理程式資料來源](agent-data-sources.md)<br>[將 Operations Manager 連接到 Azure 監視器](om-agents.md) |
| VM 儲存體 | 適用於 VM 的 Azure 監視器會使用 Log Analytics 代理程式，將 heath 狀態資訊儲存在自訂位置。 如需詳細資訊，請參閱下一節。  |


### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器 
[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)提供自訂的監視體驗，讓虛擬機器提供核心 Azure 監視器功能以外的功能，包括服務狀態和 VM 健全狀況。 在與 Log Analytics 代理程式整合的 Windows 和 Linux 虛擬機器上需要 Dependency Agent，以收集有關虛擬機器上執行之進程的探索資料，以及外部進程相依性。

| Destination | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄 | 將有關進程和相依性的資料儲存在代理程式上。 | [使用適用於 VM 的 Azure 監視器（預覽）對應來瞭解應用程式元件](../insights/vminsights-maps.md) |
| VM 儲存體 | 適用於 VM 的 Azure 監視器會使用 Log Analytics 代理程式，將 heath 狀態資訊儲存在自訂位置。 這僅適用于 Azure 入口網站中的適用於 VM 的 Azure 監視器，以及[Azure 資源健康情況 REST API](/rest/api/resourcehealth/)。 | [瞭解 Azure 虛擬機器的健康情況](../insights/vminsights-health.md)<br>[Azure 資源健康狀態 REST API](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>應用程式程式碼
Azure 監視器中的詳細應用程式監視是透過[Application Insights](https://docs.microsoft.com/azure/application-insights/) ，它會從在各種平臺上執行的應用程式收集資料。 應用程式可以在 Azure、其他雲端或內部部署中執行。

![應用程式資料收集](media/data-sources/applications.png)


### <a name="application-data"></a>應用程式資料
當您藉由安裝檢測套件，針對應用程式啟用 Application Insights 時，它會收集與應用程式效能及作業相關的計量和記錄。 Application Insights 會將它收集的資料儲存在其他資料來源所使用的相同 Azure 監視器資料平臺中。 其中包含用來分析此資料的廣泛工具，但您也可以使用計量瀏覽器和 Log Analytics 等工具，利用來自其他來源的資料進行分析。

| Destination | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄 | 應用程式的相關運算元據，包括頁面流覽、應用程式要求、例外狀況和追蹤。 | [分析 Azure 監視器中的記錄資料](../log-query/log-query-overview.md) |
|                    | 應用程式元件之間的相依性資訊，以支援應用程式對應和遙測相互關聯。 | [Application Insights 中的遙測相互關聯](../app/correlation.md) <br> [應用程式對應](../app/app-map.md) |
|            | 可用性測試的結果，可從公用網際網路上的不同位置測試應用程式的可用性和回應性。 | [監視任何網站的可用性和回應性](../app/monitor-web-app-availability.md) |
| Azure 監視器計量 | Application Insights 會收集描述應用程式效能和作業的計量，以及您在應用程式中定義為 Azure 監視器計量資料庫的自訂計量。 | [Application Insights 中以記錄為基礎和預先匯總的計量](../app/pre-aggregated-metrics-log-metrics.md)<br>[適用于自訂事件和計量的 Application Insights API](../app/api-custom-events-metrics.md) |
| Azure 儲存體 | 將應用程式資料傳送至 Azure 儲存體以進行封存。 | [從 Application Insights 匯出遙測](../app/export-telemetry.md) |
|            | 可用性測試的詳細資料會儲存在 Azure 儲存體中。 使用 Azure 入口網站中的 Application Insights 下載以進行本機分析。 可用性測試的結果會儲存在 Azure 監視器記錄中。 | [監視任何網站的可用性和回應性](../app/monitor-web-app-availability.md) |
|            | Profiler 追蹤資料會儲存在 Azure 儲存體中。 使用 Azure 入口網站中的 Application Insights 下載以進行本機分析。  | [使用 Application Insights 分析 Azure 中的生產應用程式](../app/profiler-overview.md) 
|            | 針對例外狀況子集所捕捉的偵錯工具資料會儲存在 Azure 儲存體中。 使用 Azure 入口網站中的 Application Insights 下載以進行本機分析。  | [快照集的工作方式](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>監視解決方案和見解
[監視解決方案](../insights/solutions.md)和[見解](../insights/insights-overview.md)會收集資料，為特定服務或應用程式的作業提供額外的深入解析。 它們可以處理不同應用層中的資源，甚至是多層。

### <a name="monitoring-solutions"></a>監視解決方案

| Destination | 描述 | 參考
|:---|:---|:---|
| Azure 監視器記錄 | 監視解決方案會將資料收集到 Azure 監視器記錄檔，您可以使用通常包含在解決方案中的查詢語言或[視圖](view-designer.md)來分析它。 | [在 Azure 中監視解決方案的資料收集詳細資料](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器
[適用于容器的 Azure 監視器](../insights/container-insights-overview.md)可為[Azure Kubernetes Service （AKS）](/azure/aks/)提供自訂的監視體驗。 它會收集下表所述有關這些資源的其他資料。

| Destination | 描述 | 參考 |
|:---|:---|:---|
| Azure 監視器記錄 | 儲存 AKS 的監視資料，包括清查、記錄和事件。 計量資料也會儲存在記錄中，以便在入口網站中利用其分析功能。 | [使用適用於容器的 Azure 監視器來了解 AKS 叢集效能](../insights/container-insights-analyze.md) |
| Azure 監視器計量 | 度量資料會儲存在計量資料庫中，以驅動視覺效果和警示。 | [在計量瀏覽器中查看容器計量](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | 可直接存取入口網站中的 Azure Kubernetes Service （AKS）容器記錄（stdout/stderror）、事件和 pod 計量。 | [如何即時查看 Kubernetes 記錄、事件和 pod 計量](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
[適用於 VM 的 Azure 監視器](../insights/vminsights-overview.md)提供監視虛擬機器的自訂體驗。 適用於 VM 的 Azure 監視器所收集的資料說明包含在上述[作業系統（guest）](#operating-system-guest)一節中。

## <a name="custom-sources"></a>自訂來源
除了應用程式的標準層，您可能需要監視其他資源，這些資源具有無法與其他資料來源一起收集的遙測。 針對這些資源，請使用 Azure 監視器 API，將此資料寫入計量或記錄。

![自訂集合](media/data-sources/custom.png)

| Destination | 方法 | 描述 | 參考 |
|:---|:---|:---|:---|
| Azure 監視器記錄 | 資料收集器 API | 從任何 REST 用戶端收集記錄資料，並將其儲存在 Log Analytics 工作區中。 | [使用 HTTP 資料收集器 API 將記錄資料傳送至 Azure 監視器（公開預覽）](data-collector-api.md) |
| Azure 監視器計量 | 自訂計量 API | 從任何 REST 用戶端收集計量資料，並將其儲存在 Azure 監視器計量資料庫中。 | [使用 REST API，將 Azure 資源的自訂計量傳送至 Azure 監視器公制存放區](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>其他服務
Azure 中的其他服務會將資料寫入 Azure 監視器資料平臺。 這可讓您使用 Azure 監視器所收集的資料來分析這些服務所收集的資料，並利用相同的分析和視覺效果工具。

| Service | Destination | 描述 | 參考 |
|:---|:---|:---|:---|
| [Azure 資訊安全中心](/azure/security-center/) | Azure 監視器記錄 | Azure 資訊安全中心會將它所收集的安全性資料儲存在 Log Analytics 工作區中，讓它能夠使用 Azure 監視器所收集的其他記錄資料進行分析。  | [Azure 資訊安全中心的資料收集](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Azure 監視器記錄 | Azure Sentinel 會將它從不同資料來源收集的資料儲存在 Log Analytics 工作區中，以便使用 Azure 監視器所收集的其他記錄資料進行分析。  | [連接資料來源](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器所收集的監視資料類型](data-platform.md)，以及如何檢視及分析這項資料。
- 列出[Azure 資源儲存資料的不同位置](data-locations.md)，以及您可以存取的方式。 
