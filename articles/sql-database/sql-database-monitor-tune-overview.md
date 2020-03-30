---
title: 監視和效能微調
description: Azure SQL 資料庫中的監視和性能調優功能和方法概述。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268726"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Azure SQL 資料庫中的監視和性能調優

要監視 Azure SQL 資料庫中資料庫的性能，請首先監視工作負荷相對於選擇特定服務層和性能級別的資料庫性能級別使用的 CPU 和 IO 資源。 為此，Azure SQL 資料庫會發出可在 Azure 門戶中查看的資源指標，或者通過使用以下 SQL 管理工具之一[：Azure 資料工作室](https://docs.microsoft.com/sql/azure-data-studio/what-is)或[SQL 伺服器管理工作室](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)（SSMS） 進行查看。

對於單個資料庫和池資料庫，Azure SQL 資料庫提供了許多資料庫顧問，以提供智慧性能調優建議和自動調優選項以提高性能。 此外，查詢性能洞察顯示有關負責單個資料庫和池資料庫 CPU 和 IO 使用率最多的查詢的詳細資訊。

Azure SQL 資料庫提供由人工智慧支援的其他監視和調優功能，以説明您疑難排解並最大限度地提高資料庫和解決方案的性能。 您可以選擇將這些[智慧見解](sql-database-intelligent-insights.md)和其他 SQL 資料庫資源日誌和指標的[流式匯出](sql-database-metrics-diag-logging.md)配置為多個用於消費和分析的目標之一，尤其是使用[SQL 分析](../azure-monitor/insights/azure-sql.md)。 Azure SQL 分析是一種高級雲監視解決方案，用於大規模監視所有 Azure SQL 資料庫的性能，並在單個視圖中跨多個訂閱進行監視。 有關可以匯出的日誌和指標的清單，請參閱[匯出的診斷遙測資料](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)

最後，SQL 具有自己的監視和診斷功能，具有[SQL Server 查詢存儲](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)和[動態管理檢視 （DMV）。](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) 請參閱[使用 DMV 監視](sql-database-monitoring-with-dmvs.md)腳本以監視各種性能問題。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure 門戶中的監視和調優功能

在 Azure 門戶中，Azure SQL 資料庫提供所有部署類型的資源指標的監視。 另外，對於單個資料庫和池資料庫，資料庫顧問和查詢性能透視提供查詢調優建議和查詢性能分析。 最後，在 Azure 門戶中，可以為邏輯伺服器及其單個資料庫和池資料庫啟用自動。

### <a name="sql-database-resource-monitoring"></a>SQL 資料庫資源監視

您可以在 **"指標"** 視圖中的 Azure 門戶中快速監視以下資源指標：

- **DTU 使用**

  檢查資料庫或彈性池是否在較長時間內達到 DTU 使用量的 100%。 DTU 使用率高表示您的工作負載可能需要更多 CPU 或 IO 資源。 它還可能指示需要優化的查詢。
- **CPU 使用率**

  檢查資料庫、彈性池或託管實例是否在較長時間內達到 CPU 使用率的 100%。 高 CPU 表示您的工作負載可能需要更多 CPU 或 IO 資源。 它還可能指示需要優化的查詢。
- **IO 使用方式**

  檢查資料庫、彈性池或管理實例是否達到基礎存儲的 IO 限制。 IO 使用率高表示您的工作負載可能需要更多 CPU 或 IO 資源。 它還可能指示需要優化的查詢。

  ![資源度量](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>資料庫顧問
Azure SQL 資料庫包括[資料庫顧問](sql-database-advisor.md)，這些顧問為單個資料庫和池資料庫提供性能調優建議。 這些建議在 Azure 門戶中使用，並且通過使用[PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor)。 您還可以啟用[自動調優](sql-database-automatic-tuning.md)，以便 SQL 資料庫可以自動實現這些調優建議。

### <a name="query-performance-insight"></a>查詢效能深入解析

[查詢性能洞察](sql-database-query-performance.md)顯示 Azure 門戶中用於單個和池資料庫的最長消耗和最長執行查詢的性能。

## <a name="generate-intelligent-assessments-of-performance-issues"></a>生成性能問題的智慧評估

Azure SQL 資料庫[智慧見解](sql-database-intelligent-insights.md)使用內置智慧通過人工智慧持續監視資料庫使用方式，並檢測導致性能不佳的破壞性事件。 智慧見解根據查詢執行等待時間、錯誤或超時自動檢測 Azure SQL 資料庫中資料庫的性能問題。 檢測到後，將執行詳細的分析，生成資源日誌（稱為 SQLInsights），並[智慧評估問題](sql-database-intelligent-insights-troubleshoot-performance.md)。 此評估包含資料庫效能問題的根本原因分析，並在可能的情況下提供效能改進建議。

Intelligent Insights 是 Azure 內建智慧功能的一項獨特功能，可提供下列價值：

- 主動監視
- 量身打造的效能深入解析
- 提早偵測到資料庫效能降低
- 針對所偵測到問題的根本原因分析
- 效能改進建議
- 以數十萬個資料庫向外延展的功能
- 為 DevOps 資源及擁有權總成本帶來正面影響

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>啟用指標和資源日誌的流式匯出

您可以啟用和配置[診斷遙測的流式匯出](sql-database-metrics-diag-logging.md)到多個目標之一，包括智慧見解資源日誌。 使用[SQL 分析](../azure-monitor/insights/azure-sql.md)和其他功能使用此附加診斷遙測來識別和解決性能問題。

將診斷設置配置為將單個資料庫、池資料庫、彈性池、託管實例和實例資料庫的指標和資源日誌類別資料流到以下 Azure 資源之一。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure 監視器中的日誌分析工作區

可以將指標和資源日誌資料流到[Azure 監視器 中的日誌分析工作區](../azure-monitor/platform/resource-logs-collect-workspace.md)。 此處流的資料可由 SQL [Analytics （SQL Analytics）](../azure-monitor/insights/azure-sql.md)使用，這是一種僅雲的監視解決方案，提供對資料庫的智慧監視，其中包括性能報告、警報和緩解建議。 資料流到日誌分析工作區的資料可以使用收集的其他監視資料進行分析，還使您能夠利用其他 Azure 監視器功能（如警報和視覺化）。

### <a name="azure-event-hubs"></a>Azure 事件中心

可以將指標和資源日誌資料流到 Azure[事件中心](../azure-monitor/platform/resource-logs-stream-event-hubs.md)。 將診斷遙測資料流到事件中心，以提供以下功能：

- **將日誌資料流到協力廠商日誌記錄和遙測系統**

  將所有指標和資源日誌資料流到單個事件中心，以將日誌資料傳送到協力廠商 SIEM 或日誌分析工具。
- **構建自訂遙測和日誌記錄平臺**

  事件中心的高度可擴展的發佈-訂閱特性允許您靈活地將指標和資源日誌引入自訂遙測平臺。 有關詳細資訊[，請參閱在 Azure 事件中心上設計和調整全域縮放遙測平臺的尺寸](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。
- **通過將資料流程式傳輸到 Power BI 來查看服務運行狀況**

  使用事件中心、流分析和 Power BI 將診斷資料轉換為有關 Azure 服務的近乎即時的見解。 有關此解決方案的詳細資訊[，請參閱流分析和 Power BI：有關流資料的即時分析儀表板](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)。

### <a name="azure-storage"></a>Azure 儲存體

將指標和資源日誌資料流到[Azure 存儲](../azure-monitor/platform/resource-logs-collect-storage.md)。 使用 Azure 存儲存檔大量診斷遙測，但成本只是前兩個流式處理選項的一小部分。

## <a name="use-extended-events-in-the-sql-database-engine"></a>在 SQL 資料庫引擎中使用擴展事件

此外，您還可以在 SQL 中使用[擴展事件](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)進行其他高級監視和故障排除。 擴展事件體系結構使使用者能夠收集盡可能多的或盡可能少的資料，以疑難排解或識別性能問題。 有關在 SQL 資料庫中使用擴展事件的資訊，請參閱[SQL 資料庫中的擴展事件](sql-database-xevent-db-diff-from-svr.md)。

## <a name="next-steps"></a>後續步驟

- 有關單個資料庫和池資料庫的智慧性能建議的詳細資訊，請參閱[資料庫顧問性能建議](sql-database-advisor.md)。
- 如需如何使用效能問題的自動化診斷與根本原因分析來自動監視資料庫效能的詳細資訊，請參閱 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)。
'''''''''