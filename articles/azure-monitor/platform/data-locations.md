---
title: 監視 Azure 監視器中的資料位置 |Microsoft Docs
description: 描述監視資料儲存在 Azure 中的不同位置，包括 Azure 監視器資料平臺。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: bb7e92a676115d784bd19714178b3bd442798e26
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607325"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>監視 Azure 監視器中的資料位置

Azure 監視器是以[記錄](data-platform-logs.md)檔和[度量](data-platform-metrics.md)的[資料平臺](data-platform.md)為基礎，如[Azure 監視器資料平臺](data-platform.md)中所述。 不過，在將資料複製到 Azure 監視器或支援其他案例之前，可能會將來自 Azure 資源的監視資料寫入至其他位置。 

## <a name="monitoring-data-locations"></a>監視資料位置

下表識別在 Azure 中傳送監視資料的不同位置，以及用來存取它的不同方法。

| Location | 描述 | 存取方法 |
|:---|:---|:---|:--|
| Azure 監視器計量 | 時間序列資料庫，最適合用來分析時間戳記資料。 | [計量瀏覽器](metrics-getting-started.md)<br>[Azure 監視器計量 API](/rest/api/monitor/metrics) |
| Azure 監視器記錄    | 以 Azure 資料總管為基礎的 Log Analytics 工作區，可提供強大的分析引擎和豐富的查詢語言。 | [Log Analytics](../log-query/log-query-overview.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 活動記錄檔 | 當您將資料傳送至 Azure 監視器記錄檔，以使用其他資料進行分析時，活動記錄中的資料最有用，但是它也會自行收集，以便在 Azure 入口網站中直接查看。 | [Azure 入口網站](./activity-log.md#view-the-activity-log)<br>[Azure 監視器事件 API](/rest/api/monitor/eventcategories) |
| Azure 儲存體 | 某些資料來源會直接寫入 Azure 儲存體，並需要設定才能將資料移入記錄。 您也可以將資料傳送至 Azure 儲存體進行封存，並與外部系統整合。  | [Storage Analytics](/rest/api/storageservices/storage-analytics)<br>[Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[儲存體總管](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) |
| 事件中樞 | 將資料傳送至 Azure 事件中樞，以將資料串流到其他位置。 | [捕捉至儲存體](../../event-hubs/event-hubs-capture-overview.md)  |
| 適用於 VM 的 Azure 監視器 | 適用於 VM 的 Azure 監視器會將工作負載健全狀況資料儲存在其監視體驗在 Azure 入口網站中使用的自訂位置。 | [Azure 入口網站](../insights/vminsights-overview.md)<br>[工作負載監視器 REST API](/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 資源健康狀態 REST API](/rest/api/resourcehealth/)  |
| 警示 | Azure 監視器所建立的警示。 | [Azure 入口網站](alerts-managing-alert-instances.md)<br>[警示管理 REST API](/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 監視器所收集的監視資料](data-sources.md)的不同來源。
- 瞭解 Azure 監視器所 [收集的監視資料類型](data-platform.md) ，以及如何查看和分析此資料。
