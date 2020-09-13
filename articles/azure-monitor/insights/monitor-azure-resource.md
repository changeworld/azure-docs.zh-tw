---
title: 使用 Azure 監視器監視 Azure 資源 | Microsoft Docs
description: 說明如何使用 Azure 監視器，從 Azure 中的資源收集和分析監視資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 70ab387018fc54b4dad9ee911d4c7557e1e7805c
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030339"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>使用 Azure 監視器監視 Azure 資源
當您有依賴 Azure 資源的重要應用程式和商務程序時，您會想要監視這些資源的可用性、效能和操作。 本文說明 Azure 資源所產生的監視資料，以如何使用 Azure 監視器的功能來分析此資料並發出警示。

> [!IMPORTANT]
> 本文適用於 Azure 中會使用 Azure 監視器的所有服務。 計算資源 (包括 VM 和 App Service) 會產生如這裡所述的相同監視資料，但也會有也會產生記錄和計量的客體作業系統。 如需如何收集和分析此資料的詳細資訊，請參閱這些服務的監視文件。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure 監視器是 Azure 中的完整堆疊監視服務，其提供了一組完整功能以供您監視 Azure 資源，以及其他雲端和內部部署環境中的資源。 [Azure 監視器資料平臺](../platform/data-platform.md)會將資料收集到[記錄](../platform/data-platform-logs.md)檔和[度量](../platform/data-platform-metrics.md)，讓您可以使用一組完整的監視工具來將資料一起分析。 查看可由 [Azure 監視器監視](../monitor-reference.md)的 Azure 監視器的應用程式和服務的完整清單。

在建立 Azure 資源後，Azure 監視器便會啟用並開始收集計量和活動記錄，您則可以[在 Azure 入口網站中檢視和分析](#monitoring-in-the-azure-portal)這些項目。 若搭配某些設定，您就可以收集額外的監視資料並啟用額外的功能。 如需任何設定需求的詳細資訊，請參閱下方的[監視資料](#monitoring-data)。


## <a name="costs-associated-with-monitoring"></a>與監視相關聯的成本
分析依預設而收集的監視資料並不會產生任何費用。 這包括下列項目：

- 使用計量瀏覽器收集平台計量並加以分析。
- 在 Azure 入口網站中收集活動記錄並加以分析。
- 建立活動記錄警示規則。

收集和匯出記錄與計量不會有 Azure 監視器成本，但可能會有與目的地相關聯的相關成本：

- 在 Log Analytics 工作區中收集記錄和計量時，會有與資料擷取和保留相關聯的成本。 請參閱 [Log Analytics 的 Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。
- 將記錄和計量收集至 Azure 儲存體帳戶時，會有與資料儲存體相關聯的成本。 請參閱 [Blob 儲存體的 Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。
- 將記錄和計量轉送到 Azure 事件中樞時，會有與事件中樞串流相關聯的成本。 請參閱 [Azure 事件中樞定價](https://azure.microsoft.com/pricing/details/event-hubs/)。

可能會有與下列項目相關聯的 Azure 監視器成本。 請參閱 [Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)：

- 執行記錄查詢。
- 建立計量或記錄的查詢警示規則。
- 從任何警示規則傳送通知。
- 透過 API 存取計量。

## <a name="monitoring-data"></a>監視資料
Azure 中的資源會產生下圖所示的[記錄](../platform/data-platform-logs.md)和[計量](../platform/data-platform-metrics.md)。 請參閱每個 Azure 服務的文件，以取得服務所產生的特定資料，以及其所提供的任何其他解決方案或深入解析。

![概觀](media/monitor-azure-resource/logs-metrics.png)



- [平台計量](../platform/data-platform-metrics.md) - 會定期自動收集的數值，並且會在特定時間描述資源的某些層面。 
- [資源記錄](../platform/platform-logs-overview.md) - 針對在 Azure 資源 (資料平面) 內執行的作業提供深入解析，例如從 Key Vault 取得秘密，或對資料庫提出要求。 資源記錄的內容和結構會因 Azure 服務和資源類型而異。
- [活動記錄](../platform/platform-logs-overview.md) - 會針對外部訂用帳戶 (管理平面) 中每個 Azure 資源上的作業提供深入解析，例如建立新資源或啟動虛擬機器。 此資訊與訂用帳戶中針對資源所進行的寫入作業種類 (PUT、POST、DELETE) 執行者和執行時間有關。


## <a name="configuration-requirements"></a>組態需求

### <a name="configure-monitoring"></a>設定監視
某些監視資料會由系統自動收集，但您可能需要根據需求來執行一些設定。 請參閱下列資訊，以取得每種監視資料的特定資訊。

- [平台計量](../platform/data-platform-metrics.md) - 平台計量會自動收集到 [Azure 監視器計量](../platform/data-platform-metrics.md)中，而不需要進行任何設定。 請建立診斷設定，以將項目傳送至 Azure 監視器記錄，或將項目轉送到 Azure 外部。
- [資源記錄](../platform/platform-logs-overview.md) - 資源記錄會由 Azure 資源自動產生，但不會在沒有診斷設定的情況下進行收集。  請建立診斷設定，以將項目傳送至 Azure 監視器記錄，或將項目轉送到 Azure 外部。
- [活動記錄](../platform/platform-logs-overview.md) - 活動記錄會由系統自動收集而不需要進行任何設定，而且可以在 Azure 入口網站中加以檢視。 請建立診斷設定，以將項目複製到 Azure 監視器記錄，或將項目轉送到 Azure 外部。

### <a name="log-analytics-workspace"></a>Log Analytics 工作區
將資料收集到 Azure 監視器記錄內必須要有 Log Analytics 工作區。 您可以建立新的工作區來快速開始監視您的服務，但使用會從其他服務收集資料的工作區可能有其價值。 請參閱[在 Azure 入口網站中建立 Log Analytics 工作區](../learn/quick-create-workspace.md)以獲得如何建立工作區的詳細資訊，並參閱[設計您的 Azure 監視器記錄部署](../platform/design-logs-deployment.md)以協助判斷適合您需求的最佳工作區設計。 如果您使用組織中的現有工作區，則需要有適當權限，如[管理 Azure 監視器中記錄資料和工作區的存取](../platform/manage-access.md)所述。 





## <a name="diagnostic-settings"></a>診斷設定
診斷設定會定義應將特定資源的資源記錄和計量傳送到何處。 可能的目的地包括：

- [Log Analytics 工作區](../platform/resource-logs.md#send-to-log-analytics-workspace)，可讓您使用功能強大的記錄查詢透過 Azure 監視器所收集的其他監視資料來分析資料，也可以利用記錄警示和視覺效果等其他 Azure 監視器功能。 
- [事件中樞](../platform/resource-logs.md#send-to-azure-event-hubs)，可將資料串流至外部系統 (例如第三方 SIEM 和其他記錄分析解決方案)。 
- [Azure 儲存體帳戶](../platform/resource-logs.md#send-to-azure-storage)，適合用於稽核、靜態分析或備份。

請遵循[建立診斷設定以在 Azure中收集平台記錄和計量](../platform/diagnostic-settings.md)，以透過 Azure 入口網站建立和管理診斷設定。 請參閱[使用 Resource Manager 範本在 Azure 中建立診斷設定](../platform/diagnostic-settings-template.md)以在範本中定義診斷設定，並針對已建立的資源啟用完整的監視功能。


## <a name="monitoring-in-the-azure-portal"></a>Azure 入口網站中的監視
 您可以從資源位於 Azure 入口網站中的功能表來存取大部分 Azure 資源的監視資料。 這可讓您使用標準的 Azure 監視器工具來存取單一資源的資料。 某些 Azure 服務會提供不同的選項，因此請參考該服務的文件，以取得其他資訊。 使用 [Azure 監視器] 功能表可分析來自所有受監視資源的資料。 

### <a name="overview"></a>概觀
許多服務都會在其 [概觀] 頁面上包含監視資料，以便快速瀏覽其作業。 這通常會以 Azure 監視器計量中儲存的平台計量子集為基礎。 其他監視選項通常會在服務功能表的 [ **監視** ] 區段中提供。

![概觀分頁](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>深入解析和解決方案 
有些服務會提供超越 Azure 監視器標準功能的工具。 [深入解析](./insights-overview.md)可提供建置於 Azure 監視器資料平台和標準功能之上的自訂監視體驗。 [解決方案](./solutions.md)可提供建置於 Azure 監視器記錄之上的預先定義監視邏輯。 

如果服務有 Azure 監視器深入解析，您可以從資源各自功能表中的 [監視] 來加以存取。 從 [Azure 監視器] 功能表則可存取所有的深入解析和解決方案。

![Azure 入口網站中的深入解析](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>計量
請在 Azure 入口網站中使用[計量瀏覽器](../platform/metrics-getting-started.md)來分析計量，對於大部分的服務來說，計量瀏覽器可從 [計量] 功能表項目中取得。 此工具可讓您使用個別計量，或結合多個計量以識別相互關聯和趨勢。 

- 如需如何使用計量瀏覽器的基本知識，請參閱[開始使用 Azure 計量瀏覽器](../platform/metrics-getting-started.md)。
- 如需計量瀏覽器的進階功能 (例如，使用多個計量和套用篩選與分割)，請參閱 [Azure 計量瀏覽器的進階功能](../platform/metrics-charts.md)。

![Azure 入口網站中的計量瀏覽器](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>活動記錄檔 
在 Azure 入口網站中，能以初始篩選設定為目前資源的方式來檢視活動記錄中的項目。 將活動記錄複製到 Log Analytics 工作區來存取活動記錄，以便將其用於記錄查詢和活頁簿。 

- 如需如何使用各種方法來檢視活動記錄並擷取項目的詳細資訊，請參閱[檢視及擷取 Azure 活動記錄事件](../platform/activity-log.md#view-the-activity-log)。
- 請參閱 Azure 服務的文件，以了解所記錄的特定事件。

![活動記錄檔](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure 監視器記錄
Azure 監視器記錄會合併來自多個服務和其他資料來源的記錄與計量，以利用功能強大的查詢工具進行分析。 如上所述，請建立診斷設定，以將平台計量、活動記錄和資源記錄收集到 Azure 監視器中的 Log Analytics 工作區。

[Log Analytics](../log-query/get-started-portal.md) 可讓您使用[記錄查詢](../log-query/log-query-overview.md)，這是 Azure 監視器的強大功能，可讓您使用功能完整的查詢語言來為記錄資料執行進階分析。 請從 Azure 資源 [監視] 功能表中的 [記錄] 開啟 Log Analytics，以使用資源作為[查詢範圍](../log-query/scope.md#query-scope)來處理記錄查詢。 這可讓您只針對該資源分析多個資料表的資料。 使用 [Azure 監視器] 功能表中的 [記錄] 來存取所有資源的記錄。 

- 如需「如何使用用來撰寫記錄查詢的查詢語言」的教學課程，請參閱[開始使用 Azure 監視器中的記錄查詢](../log-query/get-started-queries.md)。
- 如需 Azure 監視器記錄如何收集資源記錄的相關資訊，以及如何在查詢中存取資源記錄的詳細資訊，請參閱[在 Azure 監視器中於 Log Analytics 工作區內收集 Azure 資源記錄](../platform/resource-logs.md#send-to-log-analytics-workspace)。
- 如需 Azure 監視器記錄如何定資源記錄資料結構的說明，請參閱[收集模式](../platform/resource-logs.md#send-to-log-analytics-workspace)。
- 請參閱每個 Azure 服務的文件，以深入了解其在 Azure 監視器記錄中的資料表。

![Azure 入口網站中的 Log Analytics](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>從命令列監視
您可以從命令列存取收集自您資源的監視資料，也可以使用 [Azure PowerShell](/powershell/azure/) 或 [Azure 命令列介面](/cli/azure/)納入指令碼。 

- 請參閱 [CLI 計量參考](/cli/azure/monitor/metrics)以從 CLI 存取計量資料。
- 請參閱 [CLI Log Analytics 參考](/cli/azure/ext/log-analytics/monitor/log-analytics)，以從 CLI 使用記錄查詢來存取 Azure 監視器記錄的資料。
- 請參閱 [Azure PowerShell 計量參考](/powershell/module/azurerm.insights/get-azurermmetric)，以從 Azure PowerShell 存取計量資料。
- 請參閱 [Azure PowerShell 記錄查詢參考](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)，以從 Azure PowerShell 使用記錄查詢來存取 Azure 監視器記錄的資料。

## <a name="monitoring-from-rest-api"></a>從 REST API 監視
使用 REST API 在自訂應用程式中納入從您資源收集而來的監視資料。

- 如需如何從 Azure 監視器 REST API 存取計量的詳細資訊，請參閱 [Azure 監視 REST API 逐步解說](../platform/rest-api-walkthrough.md)。
- 如需如何從 Azure PowerShell 使用記錄查詢來存取 Azure 監視器記錄資料的相關資訊，請參閱 [Azure Log Analytics REST API](https://dev.loganalytics.io/)。

## <a name="alerts"></a>警示
在您的監視資料中發現重要條件時，[警示](../platform/alerts-overview.md)會主動通知您並可能會採取動作。 請建立警示規則來定義警示的目標、要建立警示與否的條件，以及要採取的任何回應動作。

不同種類的監視資料會用於不同種類的警示規則。

- [活動記錄警示](../platform/alerts-activity-log.md) - 會在活動記錄中建立了符合特定準則的項目時建立警示。 這可讓您在 (舉例來說) 系統建立了特定類型的資源或設定變更失敗時收到通知。
- [計量警示](../platform/alerts-metric.md) - 在計量值超過特定閾值時建立警示。 計量警示的回應能力優於其他警示，並可在問題更正時自動獲得解決。
- [記錄查詢警示](../platform/alerts-log.md) - 會定期執行記錄查詢，並在找到特定條件時建立警示。 這可讓您跨多組資料執行複雜的分析。

請使用資源功能表中的 [警示] 來檢視警示，並管理該資源的警示規則。 只有活動記錄警示和計量警示會使用個別 Azure 資源來作為目標。 記錄查詢警示會使用 Log Analytics 工作區來作為目標，並以可存取該工作區所儲存任何記錄的查詢作為基礎。 請使用 [Azure 監視器] 功能表來檢視和管理所有資源的警示，以及管理記錄查詢警示規則。

- 如需如何建立警示規則的詳細資訊，請參閱上面適用於不同警示類型的文章。
- 如需如何建立動作群組以讓您管理警示回應的詳細資訊，請參閱[在 Azure 入口網站中建立和管理動作群組](../platform/action-groups.md)。



## <a name="next-steps"></a>後續步驟

* 如需不同 Azure 服務的資源記錄詳細資訊，請參閱[Azure 資源記錄的支援服務、結構描述和類別](../platform/resource-logs-schema.md)。  

