---
title: Azure 監視器中資料的來源 | Microsoft Docs
description: 描述可用於監視 Azure 資源 (以及在資源上執行的應用程式) 健康情況和效能的資料。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/19/2019
ms.openlocfilehash: d1c0652844556b545cf0617032d21b80dd67d198
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479836"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Azure 監視器的監視資料來源
Azure 監視器基於一個[常見的監視資料平臺](data-platform.md)，其中包括[日誌](data-platform-logs.md)和[指標](data-platform-metrics.md)。 通過 Azure 監視器中的一組通用工具，將資料收集到此平臺，以便一起分析來自多個資源的資料。 監視資料也可以發送到其他位置以支援某些方案，某些資源可能會寫入其他位置，然後才能將其收集到日誌或指標中。

本文介紹 Azure 監視器收集的監視資料的不同來源以及 Azure 資源創建的監視資料。 提供連結，以獲取有關將這些資料收集到不同位置所需的配置的詳細資訊。

## <a name="application-tiers"></a>應用程式層

Azure 應用程式的監視資料來源可以組織成層，最高的層是應用程式本身，較低層是 Azure 平臺的元件。 從每個層訪問資料的方法各不相同。 應用程式層總結如下表，每個層中的監視資料來源在以下各節中顯示。 有關每個資料位置的說明以及如何訪問其資料，請參閱[監視 Azure 中的資料位置](data-locations.md)。


![監視層](../media/overview/overview.png)


### <a name="azure"></a>Azure
下表簡要描述了特定于 Azure 的應用程式層。 在以下各節中，請按照連結瞭解每個部分的詳細資訊。

| 層 | 描述 | 收集方法 |
|:---|:---|:---|
| [Azure 租戶](#azure-tenant) | 租用戶層級 Azure 服務的作業相關資料，例如 Azure Active Directory。 | 在門戶中查看 AAD 資料或使用租戶診斷設置將集合配置為 Azure 監視器。 |
| [Azure 訂閱](#azure-subscription) | 與 Azure 訂閱中跨資源服務的運行狀況和管理（如資源管理器和服務運行狀況）相關的資料。 | 在門戶中查看或使用日誌設定檔將集合配置為 Azure 監視器。 |
| [Azure 資源](#azure-resources) |  有關每個 Azure 資源的操作和性能的資料。 | 自動收集的指標，在指標資源管理器中查看。<br>將診斷設置配置為在 Azure 監視器中收集日誌。<br>可監控解決方案和見解，以便更詳細地監視特定資源類型。 |

### <a name="azure-other-cloud-or-on-premises"></a>Azure、其他雲或本地 
下表簡要描述了可能在 Azure、另一個雲或本地中的應用程式層。 在以下各節中，請按照連結瞭解每個部分的詳細資訊。

| 層 | 描述 | 收集方法 |
|:---|:---|:---|
| [作業系統（訪客）](#operating-system-guest) | 有關計算資源作業系統的資料。 | 安裝日誌分析代理以將用戶端資料來源收集到 Azure 監視器和依賴項代理中，以收集支援 VM Azure 監視器的依賴項。<br>對於 Azure 虛擬機器，安裝 Azure 診斷擴展以將日誌和指標收集到 Azure 監視器中。 |
| [應用程式代碼](#application-code) | 有關實際應用程式和代碼的性能和功能的資料，包括性能跟蹤、應用程式日誌和使用者遙測。 | 檢測代碼以將資料收集到應用程式見解中。 |
| [自訂來源](#custom-sources) | 來自外部服務或其他元件或設備的資料。 | 從任何 REST 用戶端將日誌或指標資料收集到 Azure 監視器中。 |

## <a name="azure-tenant"></a>Azure 租用戶
與您 Azure 租用戶相關的遙測，會從整個租用戶服務 (例如 Azure Active Directory) 收集。

![Azure 租用戶集合](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Azure Active Directory 稽核記錄
[Azure Active Directory 報告](../../active-directory/reports-monitoring/overview-reports.md)包含在特定租用戶內所進行登入活動的歷程記錄，以及所做變更的稽核線索。 

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| Azure 監視器記錄 | 將 Azure AD 日誌配置為在 Azure 監視器中收集，以便與其他監視資料一起分析它們。 | [將 Azure AD 記錄與 Azure 監視器記錄整合 (預覽)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure 儲存體 | 將 Azure AD 日誌匯出到 Azure 存儲以進行存檔。 | [教學課程：將 Azure AD 記錄封存到 Azure 儲存體帳戶 (預覽)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| 事件中樞 | 使用事件中心將 Azure AD 日誌資料流到其他位置。 | [教程：將 Azure 活動目錄日誌資料流到 Azure 事件中心（預覽）。](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |



## <a name="azure-subscription"></a>Azure 訂用帳戶
與 Azure 訂閱的運行狀況和操作相關的遙測資料。

![Azure 訂用帳戶](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure 活動記錄 
[Azure 活動日誌](platform-logs-overview.md)包括服務運行狀況記錄以及對 Azure 訂閱中資源所做的任何配置更改的記錄。 所有 Azure 資源都會有活動記錄，也代表了資源的_外部_檢視。

| Destination | 描述 | 參考資料 |
|:---|:---|
| 活動記錄檔 | 活動日誌被收集到其自己的資料存儲中，可以從 Azure 監視器功能表中查看，也可以用於創建活動日誌警報。 | [在 Azure 門戶中查詢活動日誌](activity-log-view.md#azure-portal) |
| Azure 監視器記錄 | 配置 Azure 監視器日誌以收集活動日誌以使用其他監視資料對其進行分析。 | [在 Azure 監視器中的日誌分析工作區中收集和分析 Azure 活動日誌](activity-log-collect.md) |
| Azure 儲存體 | 將活動日誌匯出到 Azure 存儲以進行存檔。 | [存檔活動日誌](resource-logs-collect-storage.md)  |
| 事件中樞 | 使用事件中心將活動日誌資料流到其他位置 | [將活動日誌資料流到事件中心](resource-logs-stream-event-hubs.md)。 |

### <a name="azure-service-health"></a>Azure 服務健康狀態
[Azure 服務健康狀態](../../service-health/service-health-overview.md)提供您有關應用程式和資源依賴的訂用帳戶中，Azure 服務健康狀態的資訊。

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| 活動記錄檔<br>Azure 監視器記錄 | 服務運行狀況記錄存儲在 Azure 活動日誌中，因此您可以在 Azure 門戶中查看這些記錄或執行可以使用活動日誌執行的任何其他活動。 | [使用 Azure 入口網站檢視服務健康情況通知](service-notifications.md) |


## <a name="azure-resources"></a>Azure 資源
指標和資源日誌提供有關 Azure 資源_的內部_操作的資訊。 這些可用於大多數 Azure 服務，監視解決方案和見解會為特定服務收集其他資料。

![Azure 資源集合](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>平臺指標 
大多數 Azure 服務會將反映其性能和操作[的平臺指標](data-platform-metrics.md)直接發送到指標資料庫。 特定[計量會因為各種類型的資源而異](metrics-supported.md)。 

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| Azure 監視器指標 | 平臺指標將寫入 Azure 監視器指標資料庫，無需配置。 從指標資源管理器訪問平臺指標。  | [開始使用 Azure 計量瀏覽器](metrics-getting-started.md)<br>[支援 Azure 監視器的計量](metrics-supported.md) |
| Azure 監視器記錄 | 使用日誌分析將平臺指標複製到日誌，以便進行趨勢分析和其他分析。 | [Azure 診斷直達 Log Analytics](resource-logs-collect-workspace.md) |
| 事件中樞 | 使用事件中心將指標資料流到其他位置。 |[將 Azure 監視資料串流至事件中樞以供外部工具取用](stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>資源記錄
[資源日誌](platform-logs-overview.md)提供對 Azure 資源_的內部_操作的見解。  資源日誌是自動創建的，但您必須創建一個診斷設置，以指定要為每個資源收集的目標。

資源日誌的配置要求和內容因資源類型而異，並非所有服務都創建它們。 有關每個服務的詳細資訊以及指向詳細配置過程的連結，請參閱[Azure 資源日誌的支援服務、架構和類別](diagnostic-logs-schema.md)。 如果本文中未列出該服務，則該服務當前不會創建資源日誌。

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| Azure 監視器記錄 | 將資源日誌發送到 Azure 監視器日誌，以便與其他收集的日誌資料進行分析。 | [在 Azure 監視器中的日誌分析工作區中收集 Azure 資源日誌](resource-logs-collect-storage.md) |
| 存放裝置 | 將資源日誌發送到 Azure 存儲以進行存檔。 | [存檔 Azure 資源日誌](resource-logs-collect-workspace.md) |
| 事件中樞 | 使用事件中心將資源日誌資料流到其他位置。 |[將 Azure 資源日誌資料流到事件中心](resource-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>作業系統（訪客）
Azure、其他雲端和內部部署中的計算資源具有要監視的客體作業系統。 安裝一個或多個代理後，可以將來自來賓的遙測收集到 Azure 監視器中，以便使用與 Azure 服務本身相同的監視工具進行分析。

![Azure 計算資源集合](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure 診斷擴充功能
為 Azure 虛擬機器啟用 Azure 診斷擴展，允許您從 Azure 計算資源的客體作業系統收集日誌和指標，包括 Azure 雲服務（經典）Web 和輔助角色、虛擬機器、虛擬機器縮放集和服務結構。

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| 存放裝置 | Azure 診斷擴展始終寫入 Azure 存儲帳戶。 | [安裝和配置 Windows Azure 診斷擴展 （WAD）](diagnostics-extension-windows-install.md)<br>[使用 Linux 診斷擴充功能監視計量與記錄](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure 監視器指標 | 將診斷擴展配置為收集效能計數器時，它們將寫入 Azure 監視器指標資料庫。 | [使用 Windows 虛擬機器的資源管理器範本將來賓 OS 指標發送到 Azure 監視器指標存儲](collect-custom-metrics-guestos-resource-manager-vm.md) |
| 事件中樞 | 配置診斷擴展以使用事件中心將資料流程式傳輸到其他位置。  | [使用事件中心資料流 Azure 診斷資料](diagnostics-extension-stream-event-hubs.md)<br>[使用 Linux 診斷擴充功能監視計量與記錄](../../virtual-machines/extensions/diagnostics-linux.md) |
| 應用程式見解日誌 | 從支援應用程式的計算資源收集日誌和效能計數器，以便與其他應用程式資料進行分析。 | [將雲端服務、虛擬機器或 Service Fabric 診斷資料傳送至 Application Insights](diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics 代理程式 
安裝日誌分析代理，以便全面監視和管理 Windows 或 Linux 虛擬機器。 虛擬機器可以在 Azure、其他雲端或內部部署中執行。

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| Azure 監視器記錄 | 日誌分析代理直接或通過系統中心操作管理器連接到 Azure 監視器，並允許您從配置的資料來源或提供應用程式其他見解的監視解決方案收集資料在虛擬機器上運行。 | [Azure 監視器中的代理程式資料來源](agent-data-sources.md)<br>[將操作管理器連接到 Azure 監視器](om-agents.md) |
| 虛擬機器存儲 | VM 的 Azure 監視器使用日誌分析代理在自訂位置存儲運行狀況資訊。 如需詳細資訊，請參閱下一節。  |


### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器 
[VM 的 Azure 監視器](../insights/vminsights-overview.md)為虛擬機器提供自訂的監視體驗，這些虛擬機器提供核心 Azure 監視器功能以外的功能。 它需要 Windows 和 Linux 虛擬機器上的依賴項代理與日誌分析代理集成，以收集有關虛擬機器上運行的進程和外部進程依賴項的已發現資料。

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| Azure 監視器記錄 | 存儲有關進程和對代理的依賴項的資料。 | [使用 AZURE 監視器進行 VM（預覽）映射以瞭解應用程式元件](../insights/vminsights-maps.md) |



## <a name="application-code"></a>應用程式程式碼
Azure 監視器中的詳細應用程式監視通過[應用程式見解](https://docs.microsoft.com/azure/application-insights/)完成，應用程式見解從在各種平臺上運行的應用程式收集資料。 應用程式可以在 Azure、其他雲端或內部部署中執行。

![應用程式資料收集](media/data-sources/applications.png)


### <a name="application-data"></a>應用程式資料
當您藉由安裝檢測套件，針對應用程式啟用 Application Insights 時，它會收集與應用程式效能及作業相關的計量和記錄。 應用程式見解將其收集的資料存儲在其他資料來源使用的同一 Azure 監視器資料平臺中。 它包括用於分析此資料的廣泛工具，但您也可以使用指標資源管理器和日誌分析等工具使用來自其他來源的資料進行分析。

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| Azure 監視器記錄 | 有關應用程式的運算元據，包括網頁檢視、應用程式請求、異常和跟蹤。 | [在 Azure 監視器中分析記錄資料](../log-query/log-query-overview.md) |
|                    | 應用程式元件之間的依賴項資訊，以支援應用程式映射和遙測相關性。 | [Application Insights 中的遙測相互關聯](../app/correlation.md) <br> [應用程式映射](../app/app-map.md) |
|            | 從公共 Internet 上的不同位置測試應用程式的可用性和回應性的可用性測試結果。 | [監視任何網站的可用性和回應性](../app/monitor-web-app-availability.md) |
| Azure 監視器指標 | 除了在應用程式中定義的自訂指標到 Azure 監視器指標資料庫中之外，應用程式見解還收集描述應用程式的性能和操作的指標。 | [Application Insights 中記錄型和預先彙總的計量](../app/pre-aggregated-metrics-log-metrics.md)<br>[自訂事件和度量的 Application Insights API](../app/api-custom-events-metrics.md) |
| Azure 儲存體 | 將應用程式資料發送到 Azure 存儲以進行存檔。 | [從 Application Insights 匯出遙測](../app/export-telemetry.md) |
|            | 可用性測試的詳細資訊存儲在 Azure 存儲中。 使用 Azure 門戶中的應用程式見解下載以進行本地分析。 可用性測試結果存儲在 Azure 監視器日誌中。 | [監視任何網站的可用性和回應性](../app/monitor-web-app-availability.md) |
|            | 探測器跟蹤資料存儲在 Azure 存儲中。 使用 Azure 門戶中的應用程式見解下載以進行本地分析。  | [使用 Application Insights 來分析 Azure 中的生產應用程式](../app/profiler-overview.md) 
|            | 為異常子集捕獲的調試快照資料存儲在 Azure 存儲中。 使用 Azure 門戶中的應用程式見解下載以進行本地分析。  | [快照集運作方式](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>監控解決方案和見解
[監視解決方案](../insights/solutions.md)和[見解](../insights/insights-overview.md)收集資料，以便對特定服務或應用程式的操作提供其他見解。 它們可能涉及不同應用程式層甚至多個層中的資源。

### <a name="monitoring-solutions"></a>監視解決方案

| Destination | 描述 | 參考資料
|:---|:---|:---|
| Azure 監視器記錄 | 監視解決方案將資料收集到 Azure 監視器日誌中，其中可以使用解決方案中通常包含的查詢語言或[視圖](view-designer.md)對其進行分析。 | [用於監視 Azure 中解決方案的資料收集詳細資訊](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>適用於容器的 Azure 監視器
[容器的 Azure 監視器](../insights/container-insights-overview.md)為 Azure[庫伯奈斯服務 （AKS）](/azure/aks/)提供了自訂的監視體驗。 它收集有關下表中描述的這些資源的其他資料。

| Destination | 描述 | 參考資料 |
|:---|:---|:---|
| Azure 監視器記錄 | 存儲 AKS 的監視資料，包括庫存、日誌和事件。 指標資料也存儲在日誌中，以便利用其在門戶中的分析功能。 | [使用適用於容器的 Azure 監視器來了解 AKS 叢集效能](../insights/container-insights-analyze.md) |
| Azure 監視器指標 | 指標資料存儲在指標資料庫中，以推動視覺化和警報。 | [在指標資源管理器中查看容器指標](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | 提供對門戶中的 Azure Kubernetes 服務 （AKS） 容器日誌（stdout/stderror）、事件和 pod 指標的直接存取。 | [如何即時查看 Kubernetes 日誌、事件和 pod 指標](../insights/container-insights-livedata-overview.md) |

### <a name="azure-monitor-for-vms"></a>適用於 VM 的 Azure 監視器
[VM 的 Azure 監視器](../insights/vminsights-overview.md)為監視虛擬機器提供了自訂體驗。 上面的[作業系統（來賓）](#operating-system-guest)部分包含 Azure 監視器為 VM 收集的資料的說明。

## <a name="custom-sources"></a>自訂來源
除了應用程式的標準層，您可能需要監視其他資源，這些資源具有無法與其他資料來源一起收集的遙測。 對於這些資源，使用 Azure 監視器 API 將此資料寫入指標或日誌。

![自訂集合](media/data-sources/custom.png)

| Destination | 方法 | 描述 | 參考資料 |
|:---|:---|:---|:---|
| Azure 監視器記錄 | 資料收集器 API | 從任何 REST 用戶端收集日誌資料，並存儲在日誌分析工作區中。 | [使用 HTTP 資料收集器 API 將記錄資料傳送給 Azure 監視器 (公開預覽)](data-collector-api.md) |
| Azure 監視器指標 | 自訂指標 API | 從任何 REST 用戶端收集指標資料並存儲在 Azure 監視器指標資料庫中。 | [使用 REST API 將 Azure 資源的自訂計量傳送至 Azure 監視器計量的存放區](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>其他服務
Azure 中的其他服務將資料寫入 Azure 監視器資料平臺。 這允許您使用 Azure 監視器收集的資料分析這些服務收集的資料，並使用相同的分析和視覺化檢視。

| 服務 | Destination | 描述 | 參考資料 |
|:---|:---|:---|:---|
| [Azure 安全中心](/azure/security-center/) | Azure 監視器記錄 | Azure 安全中心將其收集的安全資料存儲在日誌分析工作區中，從而可以使用 Azure 監視器收集的其他日誌資料對其進行分析。  | [Azure 資訊安全中心的資料收集](../../security-center/security-center-enable-data-collection.md) |
| [Azure 哨兵](/azure/sentinel/) | Azure 監視器記錄 | Azure Sentinel 會將其從不同資料來源收集的資料存儲在日誌分析工作區中，從而可以使用 Azure 監視器收集的其他日誌資料對其進行分析。  | [連線資料來源](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器所收集的監視資料類型](data-platform.md)，以及如何檢視及分析這項資料。
- 列出[Azure 資源存儲資料的不同位置](data-locations.md)以及如何訪問資料。 
