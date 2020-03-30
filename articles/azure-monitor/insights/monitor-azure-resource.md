---
title: 使用 Azure 監視器監視 Azure 資源 |微軟文檔
description: 介紹如何使用 Azure 監視器從 Azure 中的資源收集和分析監視資料。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249265"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>使用 Azure 監視器監視 Azure 資源
當依賴于 Azure 資源的關鍵應用程式和業務流程時，需要監視這些資源的可用性、性能和操作。 本文介紹了 Azure 資源生成的監視資料，以及如何使用 Azure 監視器的功能來分析和警報此資料。

> [!IMPORTANT]
> 本文適用于 Azure 中使用 Azure 監視器的所有服務。 計算資源（包括 VM 和應用服務）生成此處描述的相同監視資料，但還有一個客體作業系統，該作業系統也可能生成日誌和指標。 有關如何收集和分析此資料的詳細資訊，請參閱這些服務的監視文檔。

## <a name="what-is-azure-monitor"></a>Azure 監視器是什麼？
Azure 監視器是 Azure 中的完整堆疊監視服務，它提供了一整套功能來監視 Azure 資源，以及其他雲和本地的資源。 [Azure 監視器資料平臺](../platform/data-platform.md)將資料收集到[日誌](../platform/data-platform-logs.md)和[指標中，](../platform/data-platform-metrics.md)可以使用以下各節所述的一組完整的監視工具一起分析這些資料。

- [使用 Azure 監視器指標可以執行哪些操作？](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [使用 Azure 監視器日誌可以執行什麼操作？](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

創建 Azure 資源後，Azure 監視器即啟用並開始收集指標和活動日誌，您可以在[Azure 門戶中查看和分析](#monitoring-in-the-azure-portal)這些指標和活動日誌。 通過某些配置，您可以收集其他監視資料並啟用其他功能。 有關任何配置要求的詳細資訊，請參閱下面的[監視資料](#monitoring-data)。


## <a name="costs-associated-with-monitoring"></a>與監視相關的成本
分析預設情況下收集的監視資料無需任何成本。 這包括下列項目：

- 收集平臺指標，並使用指標資源管理器對其進行分析。
- 收集活動日誌並在 Azure 門戶中分析它。
- 創建活動日誌警報規則。

收集和匯出日誌和指標沒有 Azure 監視器成本，但可能存在與目標相關的成本：

- 在日誌分析工作區中收集日誌和指標時，與資料引入和保留相關的成本。 請參閱[日誌分析的 Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。
- 向 Azure 存儲帳戶收集日誌和指標時與資料存儲相關的成本。 請參閱[Blob 存儲的 Azure 存儲定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。
- 將日誌和指標轉發到 Azure 事件中心時與事件中心流相關的成本。 請參閱[Azure 事件中心定價](https://azure.microsoft.com/pricing/details/event-hubs/)。

可能存在與以下內容關聯的 Azure 監視器成本。 請參閱[Azure 監視器定價](https://azure.microsoft.com/pricing/details/monitor/)：

- 運行日誌查詢。
- 創建指標或日誌查詢警報規則。
- 從任何警報規則發送通知。
- 通過 API 訪問指標。

## <a name="monitoring-data"></a>監視資料
Azure 中的資源生成[日誌](../platform/data-platform-logs.md)和[指標](../platform/data-platform-metrics.md)，顯示下圖。 有關每個 Azure 服務生成的特定資料及其提供的任何其他解決方案或見解，請參閱文檔。

![總覽](media/monitor-azure-resource/logs-metrics.png)



- [平臺指標](../platform/data-platform-metrics.md)- 定期自動收集的數位值，並描述特定時間資源的某些方面。 
- [資源日誌](../platform/platform-logs-overview.md)- 提供對在 Azure 資源（資料平面）中執行的操作的見解，例如從金鑰保存庫獲取機密或向資料庫發出請求。 資源日誌的內容和結構因 Azure 服務和資源類型而異。
- [活動日誌](../platform/platform-logs-overview.md)- 從外部（管理平面）提供對訂閱中每個 Azure 資源的操作的見解，例如創建新資源或啟動虛擬機器。 此資訊包括訂閱中資源上對資源採取的內容、操作和時間。


## <a name="configuration-requirements"></a>組態需求

### <a name="configure-monitoring"></a>設定監視
某些監視資料是自動收集的，但您可能需要根據您的要求執行某些配置。 有關每種監視資料類型的特定資訊，請參閱以下資訊。

- [平臺指標](../platform/data-platform-metrics.md)- 平臺指標自動收集到[Azure 監視器指標中](../platform/data-platform-metrics.md)，無需配置。 創建診斷設置以將條目發送到 Azure 監視器日誌或將其轉發到 Azure 外部。
- [資源日誌](../platform/platform-logs-overview.md)- 資源日誌由 Azure 資源自動生成，但無需診斷設置即可收集。  創建診斷設置以將條目發送到 Azure 監視器日誌或將其轉發到 Azure 外部。
- [活動日誌](../platform/platform-logs-overview.md)- 活動日誌會自動收集，無需配置，可以在 Azure 門戶中查看。 創建診斷設置以將它們複製到 Azure 監視器日誌或將其轉發到 Azure 之外。

### <a name="log-analytics-workspace"></a>Log Analytics 工作區
將資料收集到 Azure 監視器日誌需要日誌分析工作區。 您可以通過創建新工作區快速開始監視服務，但使用從其他服務收集資料的工作區可能具有價值。 有關創建工作區和[設計 Azure 監視器日誌部署](../platform/design-logs-deployment.md)以説明確定滿足需求的最佳工作區設計的詳細資訊，請參閱[在 Azure 門戶中創建日誌分析工作區](../learn/quick-create-workspace.md)。 如果在組織中使用現有工作區，則需要適當的許可權，如[管理 Azure 監視器 中的日誌資料和工作區的訪問](../platform/manage-access.md)中所述。 





## <a name="diagnostic-settings"></a>診斷設定
診斷設置定義應發送特定資源的資源日誌和指標的位置。 可能的目的地是：

- [日誌分析工作區](../platform/resource-logs-collect-workspace.md)，允許您使用強大的日誌查詢使用 Azure 監視器收集的其他監視資料分析資料，並利用其他 Azure 監視器功能（如日誌警報和視覺化）。 
- [事件中心](../platform/resource-logs-stream-event-hubs.md)將資料流程式傳輸到外部系統，如協力廠商 SIEM 和其他日誌分析解決方案。 
- [Azure 存儲帳戶](../platform/resource-logs-collect-storage.md)，可用於審核、靜態分析或備份。

按照["創建診斷設置"中的過程在 Azure 中收集平臺日誌和指標](../platform/diagnostic-settings.md)，通過 Azure 門戶創建和管理診斷設置。 請參閱[使用資源管理器範本在 Azure 中創建診斷設置](../platform/diagnostic-settings-template.md)，以便在範本中定義它們，並在創建資源時啟用對資源的完整監視。


## <a name="monitoring-in-the-azure-portal"></a>Azure 入口網站中的監視
 可以從 Azure 門戶中的資源功能表訪問大多數 Azure 資源的監視資料。 這將允許您使用標準 Azure 監視器工具訪問單個資源的資料。 某些 Azure 服務將提供不同的選項，因此應參考該服務的文檔以獲取其他資訊。 使用**Azure 監視器**功能表分析來自所有受監視資源的資料。 

### <a name="overview"></a>總覽
許多服務都會在其 [概觀]**** 頁面上包含監視資料，以便快速瀏覽其作業。 這通常會以 Azure 監視器計量中儲存的平台計量子集為基礎。 其他監視選項通常可在服務的**監視**部分提供。 功能表。

![概觀分頁](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>洞察和解決方案 
某些服務將提供超出 Azure 監視器標準功能的工具。 [Insights](../insights/insights-overview.md)提供基於 Azure 監視器資料平臺和標準功能構建的自訂監視體驗。 [解決方案](../insights/solutions.md)提供基於 Azure 監視器日誌構建的預定義監視邏輯。 

如果服務具有 Azure 監視器見解，則可以從每個資源功能表中的 **"監視"** 訪問它。 從**Azure 監視器**功能表訪問所有見解和解決方案。

![深入解析](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>計量
使用[指標資源管理器](../platform/metrics-getting-started.md)分析 Azure 門戶中的指標，這些指標資源管理器可從大多數服務的 **"指標"** 功能表項目中獲取。 此工具允許您使用單個指標或組合多個指標來識別相關性和趨勢。 

- 有關使用指標資源管理器的基礎知識，請參閱[使用 Azure 指標資源管理器入門](../platform/metrics-getting-started.md)。
- 有關指標資源管理器的高級功能（如使用多個指標和應用篩選器和應用拆分），請參閱[Azure 指標資源管理器的高級功能](../platform/metrics-charts.md)。

![計量](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>活動記錄檔 
在 Azure 門戶中查看活動日誌中的條目，將初始篩選器設置為當前資源。 將活動日誌複製到日誌分析工作區以訪問它，以在日誌查詢和活頁簿中使用它。 

- 有關查看活動日誌和使用各種方法檢索條目的詳細資訊，請參閱[查看和檢索 Azure 活動日誌事件](../platform/activity-log-view.md)。
- 有關記錄的特定事件，請參閱 Azure 服務的文檔。

![活動記錄檔](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure 監視器記錄
Azure 監視器日誌使用強大的查詢工具整合來自多個服務和其他資料來源的日誌和指標進行分析。 如上所述，創建診斷設置，將平臺指標、活動日誌和資源日誌收集到 Azure 監視器中的日誌分析工作區中。

[日誌分析](../log-query/get-started-portal.md)允許您使用[日誌查詢](../log-query/log-query-overview.md)，這是 Azure 監視器的一個強大功能，允許您使用功能齊全的查詢語言對日誌資料執行高級分析。 在 **"監視"** 功能表中打開**日誌**分析，以便 Azure 資源使用資源作為[查詢範圍](../log-query/scope.md#query-scope)處理日誌查詢。 這允許您僅分析多個表中的資料，以便進行該資源。 使用 Azure 監視器功能表中的**日誌**訪問所有資源的日誌。 

- 有關使用用於編寫日誌查詢的查詢語言的教程，請參閱[Azure 監視器中的日誌查詢](../log-query/get-started-queries.md)入門。
- 有關如何在 Azure 監視器日誌中收集資源日誌的資訊以及如何在查詢中訪問這些資源日誌的詳細資訊，請參閱[在 Azure 監視器中的日誌分析工作區中收集 Azure 資源日誌](../platform/resource-logs-collect-workspace.md)。
- 有關資源日誌資料在 Azure 監視器日誌中的結構方式的說明，請參閱[收集模式](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode)。
- 有關 Azure 監視器日誌表的詳細資訊，請參閱每個 Azure 服務的文檔。

![記錄](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>從命令列進行監視
可以使用[Azure PowerShell](/powershell/azure/)或[Azure 命令列介面](/cli/azure/)訪問從資源收集的監視資料，也可以包含在腳本中。 

- 有關從 CLI 訪問指標資料的[CLI 指標引用](/cli/azure/monitor/metrics)，請參閱 CLI 指標引用。
- 有關使用 CLI 的日誌查詢訪問 Azure 監視器日誌資料的[CLI 日誌分析參考](/cli/azure/ext/log-analytics/monitor/log-analytics)。
- 有關從 Azure PowerShell 訪問指標資料的[Azure PowerShell 指標引用](/powershell/module/azurerm.insights/get-azurermmetric)。這些指標來自 Azure PowerShell。
- 有關使用 Azure PowerShell 的日誌查詢訪問 Azure 監視器日誌資料的[Azure PowerShell 日誌查詢引用，請參閱 Azure PowerShell 日誌查詢。](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery)

## <a name="monitoring-from-rest-api"></a>從 REST API 進行監視
包括使用 REST API 在自訂應用程式中從資源中收集的監視資料。

- 有關從 Azure 監視器 REST API 訪問指標的詳細資訊，請參閱[Azure 監視 REST API 演練](../platform/rest-api-walkthrough.md)。
- 有關使用 Azure PowerShell 的日誌查詢訪問 Azure 監視器日誌資料的資訊，請參閱[Azure 日誌分析 REST API。](https://dev.loganalytics.io/)

## <a name="alerts"></a>警示
[警報](../platform/alerts-overview.md)主動通知您，並在監視資料中找到重要條件時可能採取行動。 創建警報規則，定義警報的目標、是否創建警報的條件以及回應時要執行的任何操作。

不同類型的監視資料用於不同類型的警報規則。

- [活動日誌警報](../platform/alerts-activity-log.md)- 在活動日誌中創建符合特定條件的活動日誌中創建條目時創建警報。 這允許您在創建特定類型的資源或配置更改失敗時通知您。
- [指標警報](../platform/alerts-metric.md)- 當指標值超過特定閾值時創建警報。 指標警報比其他警報回應更快，並且可以在問題得到糾正時自動解決。
- [日誌查詢警報](../platform/alerts-log.md)- 定期運行日誌查詢，並在發現特定條件時創建警報。 這允許您對多組資料和 執行複雜的分析。

使用資源功能表中的**警報**來查看警報並管理該資源的警報規則。 只有活動日誌警報和指標警報使用單個 Azure 資源作為目標。 日誌查詢警報使用日誌分析工作區作為目標，並且基於可以訪問該工作區中存儲的任何日誌的查詢。 使用 Azure 監視器功能表查看和管理所有資源的警報和管理日誌查詢警報規則。

- 有關創建警報規則的詳細資訊，請參閱上述不同類型的警報的文章。
- 有關創建操作組以管理警報回應的詳細資訊，請參閱[Azure 門戶中創建和管理操作組](../platform/action-groups.md)的詳細資訊。



## <a name="next-steps"></a>後續步驟

* 有關不同 Azure 服務的資源日誌的詳細資訊，請參閱[Azure 資源日誌的支援服務、架構和類別](../platform/diagnostic-logs-schema.md)。  
