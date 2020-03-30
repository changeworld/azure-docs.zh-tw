---
title: 監視 Azure 監視器中的資料位置 |微軟文檔
description: 描述監視資料存儲在 Azure 中的不同位置，包括 Azure 監視器資料平臺。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666610"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>監視 Azure 監視器中的資料位置

Azure 監視器基於[日誌](data-platform-logs.md)和[指標](data-platform-metrics.md)[的資料平臺](data-platform.md)，如[Azure 監視器資料平臺](data-platform.md)中所述。 但是，監視 Azure 資源的資料可能會寫入其他位置，無論是在複製到 Azure 監視器之前，也可以用於支援其他方案。 

## <a name="monitoring-data-locations"></a>監控資料位置

下表標識了在 Azure 中發送監視資料的不同位置以及訪問資料的不同方法。

| Location | 描述 | 存取方法 |
|:---|:---|:---|:--|
| Azure 監視器指標 | 時間序列資料庫，用於分析時間戳記資料。 | [指標資源管理器](metrics-getting-started.md)<br>[Azure 監視器指標 API](/rest/api/monitor/metrics) |
| Azure 監視器記錄    | 基於 Azure 資料資源管理器的日誌分析工作區，它提供了強大的分析引擎和豐富的查詢語言。 | [日誌分析](../log-query/portals.md)<br>[日誌分析 API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 活動記錄檔 | 當將活動日誌中的資料發送到 Azure 監視器日誌以與其他資料分析時，它最有用，但它也是自己收集的，因此可以直接在 Azure 門戶中查看。 | [Azure 門戶](activity-log-view.md#azure-portal)<br>[Azure 監視器事件 API](/rest/api/monitor/eventcategories) |
| Azure 儲存體 | 某些資料來源將直接寫入 Azure 存儲，並且需要配置才能將資料移動到日誌中。 還可以將資料發送到 Azure 存儲以進行存檔和與外部系統集成。  | [儲存體分析](/rest/api/storageservices/storage-analytics)<br>[伺服器總管](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[儲存體總管](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| 事件中樞 | 將資料發送到 Azure 事件中心，以將其資料流到其他位置。 | [捕獲到存儲](../../event-hubs/event-hubs-capture-overview.md)  |
| 適用於 VM 的 Azure 監視器 | VM 的 Azure 監視器將工作負載運行狀況資料存儲在 Azure 門戶中其監視體驗使用的自訂位置。 | [Azure 門戶](../insights/vminsights-overview.md)<br>[工作負載監視器 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 資源運行狀況 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 警示 | Azure 監視器創建的警報。 | [Azure 門戶](alerts-managing-alert-instances.md)<br>[警報管理 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>後續步驟

- 查看[Azure 監視器收集的監視資料](data-sources.md)的不同來源。
- 瞭解[Azure 監視器收集的監視資料類型](data-platform.md)以及如何查看和分析此資料。
