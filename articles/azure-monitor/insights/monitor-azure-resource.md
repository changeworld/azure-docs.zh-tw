---
title: 使用 Azure 監視器監視 Azure 資源 |Microsoft Docs
description: 說明如何使用 Azure 監視器，從 Azure 中的資源收集和分析監視資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79249265"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>使用 Azure 監視器監視 Azure 資源
當您的重要應用程式和商務程式依賴 Azure 資源時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure 資源所產生的監視資料，以及您可以如何使用 Azure 監視器的功能來分析此資料併發出警示。

> [!IMPORTANT]
> 本文適用于 Azure 中使用 Azure 監視器的所有服務。 計算資源（包括 Vm 和 App Service）會產生相同的監視資料，但也會有也會產生記錄和計量的客體作業系統。 如需如何收集和分析此資料的詳細資訊，請參閱這些服務的監視檔。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure 監視器是 Azure 中的完整堆疊監視服務，除了其他雲端和內部部署中的資源之外，還提供一組完整的功能來監視您的 Azure 資源。 [Azure 監視器資料平臺](../platform/data-platform.md)會將資料收集到[記錄](../platform/data-platform-logs.md)檔和[計量](../platform/data-platform-metrics.md)中，使用一組完整的監視工具來分析它們，如下列各節所述。

- [Azure 監視器計量可以做些什麼？](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Azure 監視器記錄可以做什麼？](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

一旦您建立 Azure 資源，就會啟用 Azure 監視器，並開始收集可[在 Azure 入口網站中查看和分析](#monitoring-in-the-azure-portal)的計量和活動記錄。 使用某些設定時，您可以收集額外的監視資料並啟用其他功能。 如需任何設定需求的詳細資訊，請參閱下方的[監視資料](#monitoring-data)。


## <a name="costs-associated-with-monitoring"></a>與監視相關聯的成本
分析預設收集的監視資料並不會產生任何費用。 這包括下列項目：

- 使用計量瀏覽器收集平臺計量並加以分析。
- 收集活動記錄，並在 Azure 入口網站中進行分析。
- 建立活動記錄警示規則。

收集和匯出記錄和計量並沒有 Azure 監視器的成本，但可能會有與目的地相關聯的相關成本：

- 在 Log Analytics 工作區中收集記錄和計量時，與資料內嵌和保留相關聯的成本。 請參閱[Log Analytics Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。
- 將記錄和計量收集至 Azure 儲存體帳戶時，與資料儲存體相關聯的成本。 請參閱[blob 儲存體 Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。
- 將記錄和計量轉送到 Azure 事件中樞時，與事件中樞串流相關聯的成本。 請參閱[Azure 事件中樞定價](https://azure.microsoft.com/pricing/details/event-hubs/)。

可能會有與下列相關聯的 Azure 監視器成本。 請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)：

- 正在執行記錄查詢。
- 建立度量或記錄查詢警示規則。
- 從任何警示規則傳送通知。
- 透過 API 存取計量。

## <a name="monitoring-data"></a>監視資料
Azure 中的資源會產生[記錄](../platform/data-platform-logs.md)和[計量](../platform/data-platform-metrics.md)，如下圖所示。 請參閱每個 Azure 服務的檔，以取得其所產生的特定資料，以及所提供的任何其他解決方案或深入解析。

![概觀](media/monitor-azure-resource/logs-metrics.png)



- [平臺計量](../platform/data-platform-metrics.md)-自動收集的數值，並在特定時間描述資源的某些層面。 
- [資源記錄](../platform/platform-logs-overview.md)-可讓您深入瞭解在 Azure 資源（資料平面）內執行的作業，例如從 Key Vault 取得秘密或對資料庫提出要求。 資源記錄的內容和結構會因 Azure 服務和資源類型而異。
- [活動記錄](../platform/platform-logs-overview.md)-可讓您深入瞭解訂用帳戶（管理平面）中的每個 Azure 資源上的作業，例如建立新的資源或啟動虛擬機器。 這是您訂用帳戶中資源上所採取的任何寫入作業（PUT、POST、DELETE）的相關資訊。


## <a name="configuration-requirements"></a>組態需求

### <a name="configure-monitoring"></a>設定監視
某些監視資料會自動收集，但您可能需要根據您的需求執行一些設定。 請參閱下列資訊，以取得每種監視資料類型的特定資訊。

- [平臺計量](../platform/data-platform-metrics.md)-平臺計量會自動收集到[Azure 監視器計量](../platform/data-platform-metrics.md)中，而不需要進行任何設定。 建立診斷設定，以將專案傳送至 Azure 監視器記錄，或將它們轉送到 Azure 外部。
- [資源記錄](../platform/platform-logs-overview.md)-資源記錄會由 Azure 資源自動產生，但不會在沒有診斷設定的情況下收集。  建立診斷設定，以將專案傳送至 Azure 監視器記錄，或將它們轉送到 Azure 外部。
- [活動記錄](../platform/platform-logs-overview.md)-活動記錄會自動收集，而不需要設定，而且可以在 Azure 入口網站中查看。 建立診斷設定，將它們複製到 Azure 監視器記錄，或將它們轉送到 Azure 外部。

### <a name="log-analytics-workspace"></a>Log Analytics 工作區
將資料收集到 Azure 監視器記錄檔需要 Log Analytics 工作區。 您可以藉由建立新的工作區來快速開始監視您的服務，但使用從其他服務收集資料的工作區可能會有價值。 如需有關建立工作區和[設計 Azure 監視器記錄部署](../platform/design-logs-deployment.md)的詳細資訊，請參閱[Azure 入口網站中的建立 Log Analytics 工作區](../learn/quick-create-workspace.md)，以協助判斷您的需求最佳工作區設計。 如果您在組織中使用現有的工作區，則您將需要如[管理 Azure 監視器中的記錄資料和工作區存取](../platform/manage-access.md)中所述的適當許可權。 





## <a name="diagnostic-settings"></a>診斷設定
診斷設定會定義應該將特定資源的資源記錄和計量傳送至何處。 可能的目的地包括：

- [Log Analytics 工作區](../platform/resource-logs-collect-workspace.md)，可讓您使用功能強大的記錄查詢 Azure 監視器所收集的其他監視資料來分析資料，也可以利用記錄警示和視覺效果等其他 Azure 監視器功能。 
- 將資料串流至外部系統（例如協力廠商 Siem 和其他 log analytics 解決方案）的[事件中樞](../platform/resource-logs-stream-event-hubs.md)。 
- 適用于 audit、靜態分析或備份的[Azure 儲存體帳戶](../platform/resource-logs-collect-storage.md)。

依照建立診斷設定中的程式，在[Azure 中收集平臺記錄和計量](../platform/diagnostic-settings.md)，以透過 Azure 入口網站來建立和管理診斷設定。 請參閱[使用 Resource Manager 範本在 Azure 中建立診斷設定](../platform/diagnostic-settings-template.md)，以在範本中加以定義，並在建立資源時啟用完整的監視功能。


## <a name="monitoring-in-the-azure-portal"></a>Azure 入口網站中的監視
 您可以從 Azure 入口網站中的資源功能表，存取大部分 Azure 資源的監視資料。 這可讓您使用標準 Azure 監視器工具來存取單一資源的資料。 某些 Azure 服務會提供不同的選項，因此您應該參考該服務的檔，以取得其他資訊。 使用 [ **Azure 監視器**] 功能表來分析來自所有受監視資源的資料。 

### <a name="overview"></a>概觀
許多服務都會在其 [概觀]**** 頁面上包含監視資料，以便快速瀏覽其作業。 這通常會以 Azure 監視器計量中儲存的平台計量子集為基礎。 其他監視選項通常會在服務的 [**監視**] 區段中提供。 功能表。

![概觀分頁](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>深入解析和解決方案 
有些服務會提供超出 Azure 監視器標準功能的工具。 [Insights](../insights/insights-overview.md)提供以 Azure 監視器資料平臺和標準功能為基礎的自訂監視體驗。 [解決方案](../insights/solutions.md)提供以 Azure 監視器記錄為基礎的預先定義監視邏輯。 

如果服務有 Azure 監視器深入解析，您可以從每個資源功能表中的 [**監視**] 存取它。 從 [ **Azure 監視器**] 功能表中存取所有見解和解決方案。

![深入資訊](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>計量
使用[計量瀏覽器](../platform/metrics-getting-started.md)（可從大部分服務的 [**計量**] 功能表項目取得）來分析 Azure 入口網站中的計量。 此工具可讓您使用個別的計量，或結合多個以識別相互關聯和趨勢。 

- 如需使用計量瀏覽器的基本概念，請參閱[開始使用 Azure 計量瀏覽器](../platform/metrics-getting-started.md)。
- 請參閱[Azure 計量瀏覽器的 advanced 功能](../platform/metrics-charts.md)，以取得計量瀏覽器的高階功能，例如使用多個計量，以及套用篩選和分割。

![計量](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>活動記錄檔 
在 Azure 入口網站中，以初始篩選設定為目前資源的方式，查看活動記錄中的專案。 將活動記錄複製到 Log Analytics 工作區以存取它，以便在記錄查詢和活頁簿中使用。 

- 如需有關使用各種方法來查看活動記錄和取得專案的詳細資訊，請參閱[查看和抓取 Azure 活動記錄事件](../platform/activity-log-view.md)。
- 請參閱 Azure 服務的檔，以取得所記錄的特定事件。

![活動記錄檔](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure 監視器記錄
Azure 監視器記錄會合並來自多個服務和其他資料來源的記錄和計量，以利用強大的查詢工具進行分析。 如上所述，建立診斷設定，以將平臺計量、活動記錄和資源記錄收集到 Azure 監視器中的 Log Analytics 工作區。

[Log Analytics](../log-query/get-started-portal.md)可讓您使用[記錄查詢](../log-query/log-query-overview.md)，這是 Azure 監視器的強大功能，可讓您使用完整功能的查詢語言來執行記錄資料的先進分析。 從 Azure 資源的 [**監視**] 功能表中的 [**記錄**] 開啟 log Analytics，以使用資源作為[查詢範圍](../log-query/scope.md#query-scope)來處理記錄查詢。 這可讓您只針對該資源分析多個資料表的資料。 使用 [Azure 監視器] 功能表中的 [**記錄**] 來存取所有資源的記錄。 

- 如需使用用於撰寫記錄查詢之查詢語言的教學課程，請參閱[開始使用 Azure 監視器中的記錄查詢](../log-query/get-started-queries.md)。
- 如需如何在 Azure 監視器記錄檔中收集資源記錄的資訊，以及如何在查詢中存取它們的詳細資料，請參閱[在 Azure 監視器中收集 Log Analytics 工作區中的 Azure 資源記錄](../platform/resource-logs-collect-workspace.md)。
- 如需如何在 Azure 監視器記錄中結構化資源記錄資料的說明，請參閱[收集模式](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode)。
- 請參閱每個 Azure 服務的檔，以取得其在 Azure 監視器記錄中之資料表的詳細資料。

![記錄](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>從命令列監視
您可以從命令列存取從資源收集的監視資料，或使用[Azure PowerShell](/powershell/azure/)或[Azure 命令列介面](/cli/azure/)包含在腳本中。 

- 請參閱[cli 計量參考](/cli/azure/monitor/metrics)以從 cli 存取度量資料。
- 如需使用 CLI 的記錄查詢來存取 Azure 監視器記錄資料，請參閱[Cli Log Analytics 參考](/cli/azure/ext/log-analytics/monitor/log-analytics)。
- 請參閱 Azure PowerShell 從 Azure PowerShell 存取計量資料的[參考](/powershell/module/azurerm.insights/get-azurermmetric)。
- 請參閱[Azure PowerShell 記錄查詢參考](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)，以使用 Azure PowerShell 的記錄查詢來存取 Azure 監視器記錄資料。

## <a name="monitoring-from-rest-api"></a>從 REST API 監視
包含使用 REST API 在自訂應用程式中從資源收集的監視資料。

- 如需從 Azure 監視器 REST API 存取計量的詳細資訊，請參閱[Azure 監視 REST API 逐步](../platform/rest-api-walkthrough.md)解說。
- 如需使用 Azure PowerShell 的記錄查詢來存取 Azure 監視器記錄資料的詳細資訊，請參閱[Azure Log Analytics REST API](https://dev.loganalytics.io/) 。

## <a name="alerts"></a>警示
當您的監視資料中發現重要條件時，[警示](../platform/alerts-overview.md)會主動通知您並可能採取動作。 您會建立警示規則來定義警示的目標、是否要建立警示的條件，以及要採取的任何動作以回應。

不同種類的監視資料會用於不同種類的警示規則。

- [活動記錄警示](../platform/alerts-activity-log.md)-在符合特定準則的活動記錄中建立專案時，建立警示。 這可讓您在建立特定類型的資源或設定變更失敗時，收到範例的通知。
- [度量警示](../platform/alerts-metric.md)-當計量值超過特定閾值時，建立警示。 計量警示比其他警示更具回應能力，並可在問題更正時自動解決。
- [記錄查詢警示](../platform/alerts-log.md)-定期執行記錄查詢，並在找到特定條件時建立警示。 這可讓您在多組資料和之間執行複雜的分析。

使用來自資源功能表的**警示**來查看警示，並管理該資源的警示規則。 只有活動記錄警示和計量警示會使用個別的 Azure 資源做為目標。 記錄查詢警示會使用 Log Analytics 工作區作為目標，並以可存取儲存在該工作區中之任何記錄的查詢為基礎。 使用 [Azure 監視器] 功能表來查看和管理所有資源的警示，以及記錄管理查詢警示規則。

- 如需有關建立警示規則的詳細資訊，請參閱上述不同警示類型的文章。
- 如需建立動作群組的詳細資訊，可讓您管理警示的回應，請參閱在[Azure 入口網站中建立和管理動作群組](../platform/action-groups.md)。



## <a name="next-steps"></a>後續步驟

* 如需不同 Azure 服務的資源記錄詳細資料，請參閱[支援的服務、架構和 Azure 資源記錄的類別](../platform/diagnostic-logs-schema.md)。  
