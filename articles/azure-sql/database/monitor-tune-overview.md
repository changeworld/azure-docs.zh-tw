---
title: 監視和效能微調
description: 概述 Azure SQL Database 和 Azure SQL 受控執行個體中的監視和效能微調功能和方法。
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 6e17e2a6e5c9151080facc3a2dd8c1a18c0580fe
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85982153"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 和 Azure SQL 受控執行個體中的監視和效能微調
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

若要監視 Azure SQL Database 和 Azure SQL 受控執行個體中資料庫的效能，請從監視您的工作負載所使用的 CPU 和 IO 資源，相對於您在選取特定服務層級和效能等級時所選擇的資料庫效能層級。 為達成此目的，Azure SQL Database 和 Azure SQL 受控執行個體會發出可在 Azure 入口網站中查看的資源計量，或使用下列其中一個 SQL Server 管理工具： [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)或[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) （SSMS）。

Azure SQL Database 提供一些資料庫顧問，以提供智慧型效能微調建議和自動調整選項，以改善效能。 此外，查詢效能深入解析會向您顯示有關負責單一和集區資料庫之最多 CPU 和 IO 使用量的查詢詳細資料。

Azure SQL Database 和 Azure SQL 受控執行個體提供人工智慧支援的先進監視和微調功能，以協助您進行疑難排解，並將資料庫和解決方案的效能最大化。 您可以選擇將這些[Intelligent Insights](intelligent-insights-overview.md)和其他資料庫資源記錄和計量的[串流匯出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)，設定為用於取用和分析的數個目的地之一，特別是使用[SQL 分析](../../azure-monitor/insights/azure-sql.md)）。 Azure SQL 分析是一種先進的雲端監視解決方案，可讓您在單一視圖中大規模監視所有資料庫的效能，以及跨多個訂用帳戶。 如需您可以匯出的記錄和計量清單，請參閱[匯出的診斷遙測](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export)

最後，SQL Server 有自己的監視和診斷功能，SQL Database 和 SQL 受控執行個體利用，例如[查詢存放區](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和[動態管理檢視（dmv）](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)。 請參閱[使用 Dmv 監視](monitoring-with-dmvs.md)腳本來監視各種效能問題。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure 入口網站中的監視和微調功能

在 Azure 入口網站中，Azure SQL Database 和 Azure SQL 受控執行個體會提供資源計量的監視。 此外，Azure SQL Database 提供資料庫顧問和查詢效能深入解析提供查詢微調建議和查詢效能分析。 最後，在 Azure 入口網站中，您可以啟用[邏輯 SQL 伺服器](logical-servers.md)及其單一和集區資料庫的自動。

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database 和 Azure SQL 受控執行個體資源監視

您可以在 [**計量**] 視圖的 Azure 入口網站中快速監視各種資源計量。 這些計量可讓您查看資料庫是否達到處理器、記憶體或 IO 資源的100%。 高 DTU 或處理器百分比，以及高 IO 百分比，表示您的工作負載可能需要更多的 CPU 或 IO 資源。 它也可能表示需要優化的查詢。

  ![資源度量](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Azure SQL Database 中的資料庫顧問

Azure SQL Database 包括可為單一和集區資料庫提供效能微調建議的[資料庫顧問](database-advisor-implement-performance-recommendations.md)。 這些建議可在 Azure 入口網站中取得，也可以使用[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)。 您也可以啟用[自動調整](automatic-tuning-overview.md)，讓 Azure SQL Database 可以自動執行這些微調建議。

### <a name="query-performance-insight-in-azure-sql-database"></a>Azure SQL Database 中的查詢效能深入解析

[查詢效能深入解析](query-performance-insight-use.md)顯示單一和集區資料庫的最耗用量和最長執行查詢 Azure 入口網站的效能。

## <a name="generate-intelligent-assessments-of-performance-issues"></a>產生效能問題的智慧型評量

Azure SQL Database 和 Azure SQL 受控執行個體的[Intelligent Insights](intelligent-insights-overview.md)會使用內建智慧功能，透過人工智慧持續監視資料庫使用量，並偵測導致效能不佳的干擾性事件。 Intelligent Insights 會根據查詢執行等候時間、錯誤或超時，自動偵測資料庫的效能問題。 一旦偵測到，就會執行詳細的分析來產生資源記錄（稱為 SQLInsights），並以智慧的方式[評估問題](intelligent-insights-troubleshoot-performance.md)。 此評估包含資料庫效能問題的根本原因分析，並在可能的情況下提供效能改進建議。

Intelligent Insights 是 Azure 內建智慧功能的一項獨特功能，可提供下列價值：

- 主動監視
- 量身打造的效能深入解析
- 提早偵測到資料庫效能降低
- 針對所偵測到問題的根本原因分析
- 效能改進建議
- 以數十萬個資料庫向外延展的功能
- 為 DevOps 資源及擁有權總成本帶來正面影響

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>啟用計量和資源記錄的串流匯出

您可以啟用和設定[診斷遙測的串流匯出](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)至數個目的地的其中一個，包括 Intelligent Insights 資源記錄檔。 使用[SQL 分析](../../azure-monitor/insights/azure-sql.md)和其他功能來取用此額外的診斷遙測，以識別並解決效能問題。

您可以設定診斷設定，以將單一資料庫、集區資料庫、彈性集區、受控實例和實例資料庫的計量和資源記錄分類，串流至下列其中一個 Azure 資源。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure 監視器中的 Log Analytics 工作區

您可以在 Azure 監視器中將計量和資源記錄串流至[Log Analytics 工作區](../../azure-monitor/platform/resource-logs-collect-workspace.md)。 這裡資料流程處理的資料可供[SQL 分析](../../azure-monitor/insights/azure-sql.md)使用，這是一種僅限雲端的監視解決方案，可提供您資料庫的智慧型監視，包括效能報告、警示和緩和建議。 串流至 Log Analytics 工作區的資料可以利用所收集的其他監視資料進行分析，也可讓您利用其他 Azure 監視器功能，例如警示和視覺效果。

### <a name="azure-event-hubs"></a>Azure 事件中心

您可以將計量和資源記錄串流至[Azure 事件中樞](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)。 將診斷遙測串流至事件中樞，以提供下列功能：

- **將記錄串流至協力廠商記錄和遙測系統**

  將您所有的計量和資源記錄串流至單一事件中樞，以將記錄資料傳送至協力廠商 SIEM 或 log analytics 工具。
- **建立自訂遙測和記錄平臺**

  事件中樞的高擴充性發佈訂閱特性，可讓您將計量和資源記錄彈性地內嵌到自訂遙測平臺中。 如需詳細資訊，請參閱[Azure 事件中樞上的全域調整遙測平臺的設計和調整大小](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。
- **藉由將資料串流至 Power BI 來查看服務健全狀況**

  使用事件中樞、串流分析和 Power BI，將您的診斷資料轉換為 Azure 服務上近乎即時的深入解析。 如需此解決方案的詳細資訊，請參閱[串流分析和 Power BI：串流資料的即時分析儀表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)。

### <a name="azure-storage"></a>Azure 儲存體

將計量和資源記錄串流至[Azure 儲存體](../../azure-monitor/platform/resource-logs-collect-storage.md)。 使用 Azure 儲存體來封存大量診斷遙測，以取得前兩個串流選項成本的一小部分。

## <a name="use-extended-events"></a>使用擴充事件 

此外，您可以使用 SQL Server 中的[擴充事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)，進行先進的監視和疑難排解。 「擴充事件」架構可讓使用者收集所需的資料量，或盡可能少，以進行疑難排解或識別效能問題。 如需在 Azure SQL Database 中使用擴充事件的詳細資訊，請參閱[Azure SQL Database 中的擴充事件](xevent-db-diff-from-svr.md)。

## <a name="next-steps"></a>後續步驟

- 如需有關單一和集區資料庫之智慧型效能建議的詳細資訊，請參閱[Database advisor 效能建議](database-advisor-implement-performance-recommendations.md)。
- 如需如何使用效能問題的自動化診斷與根本原因分析來自動監視資料庫效能的詳細資訊，請參閱 [Azure SQL Intelligent Insights](intelligent-insights-overview.md)。
