---
title: 監視 Azure 監視器中的資料位置 |Microsoft Docs
description: 說明在 Azure 中儲存監視資料的不同位置，包括 Azure 監視器資料平臺。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/21/2019
ms.openlocfilehash: 7d4459867081d920fefb9471b1a682d21040da9d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77666610"
---
# <a name="monitoring-data-locations-in-azure-monitor"></a>監視 Azure 監視器中的資料位置

Azure 監視器是以[記錄](data-platform-logs.md)和[計量](data-platform-metrics.md)的[資料平臺](data-platform.md)為基礎，如[Azure 監視器資料平臺](data-platform.md)中所述。 從 Azure 資源的監視資料可能會在複製到 Azure 監視器或支援其他案例之前，寫入到其他位置。 

## <a name="monitoring-data-locations"></a>監視資料位置

下表將識別 Azure 中監視資料的傳送位置，以及用來存取它的不同方法。

| 位置 | 描述 | 存取方法 |
|:---|:---|:---|:--|
| Azure 監視器計量 | 已針對分析時間戳記資料優化的時間序列資料庫。 | [計量瀏覽器](metrics-getting-started.md)<br>[Azure 監視器計量 API](/rest/api/monitor/metrics) |
| Azure 監視器記錄    | 以 Azure 資料總管為基礎的 Log Analytics 工作區，可提供功能強大的分析引擎和豐富的查詢語言。 | [Log Analytics](../log-query/portals.md)<br>[Log Analytics API](https://dev.loganalytics.io/)<br>[Application Insights API](https://dev.applicationinsights.io/reference/get-query) |
| 活動記錄檔 | 活動記錄檔中的資料在傳送至 Azure 監視器記錄時最有用，它會以其他資料進行分析，但也會自行收集，以便在 Azure 入口網站中直接查看。 | [Azure 入口網站](activity-log-view.md#azure-portal)<br>[Azure 監視器事件 API](/rest/api/monitor/eventcategories) |
| Azure 儲存體 | 某些資料來源會直接寫入 Azure 儲存體，並要求設定將資料移入記錄中。 您也可以將資料傳送至 Azure 儲存體以進行封存，以及與外部系統整合。  | [儲存體分析](/rest/api/storageservices/storage-analytics)<br>[伺服器總管](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)<br>[儲存體總管](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) |
| 事件中樞 | 將資料傳送至 Azure 事件中樞，以將它串流至其他位置。 | [捕獲到儲存體](../../event-hubs/event-hubs-capture-overview.md)  |
| 適用於 VM 的 Azure 監視器 | 適用於 VM 的 Azure 監視器會將工作負載健康情況資料儲存在 Azure 入口網站中監視體驗所使用的自訂位置。 | [Azure 入口網站](../insights/vminsights-overview.md)<br>[工作負載監視 REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)<br>[Azure 資源健康狀態 REST API](https://docs.microsoft.com/rest/api/resourcehealth/)  |
| 警示 | Azure 監視器所建立的警示。 | [Azure 入口網站](alerts-managing-alert-instances.md)<br>[警示管理 REST API](https://docs.microsoft.com/rest/api/monitor/alertsmanagement/alerts) |



## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 監視器所收集的監視資料](data-sources.md)的不同來源。
- 瞭解 Azure 監視器所[收集的監視資料類型](data-platform.md)，以及如何查看和分析此資料。
