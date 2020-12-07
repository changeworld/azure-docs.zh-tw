---
title: 'Azure 監視器與 Azure 資料總管 (preview 之間的跨服務查詢) '
description: 透過 Azure Log Analytics 工具來查詢 Azure 資料總管資料，反之亦然，可在單一位置加入及分析您的所有資料。
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: 0af9111223bb16f1c5843223e7fe1661ce99c623
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749238"
---
# <a name="cross-service-query---azure-monitor-and-azure-data-explorer-preview"></a>跨服務查詢-Azure 監視器和 Azure 資料總管 (Preview) 
在 [Azure 資料總管](https://docs.microsoft.com/azure/data-explorer/)、 [Application Insights](/azure/azure-monitor/app/app-insights-overview)與 [Log Analytics](/azure/azure-monitor/platform/data-platform-logs)之間建立跨服務查詢。
## <a name="azure-monitor-and-azure-data-explorer-cross-service-querying"></a>Azure 監視器和 Azure 資料總管跨服務查詢
這種體驗可讓您在 [Azure 資料總管和 Azure 監視器之間建立跨服務查詢](https://docs.microsoft.com/azure/data-explorer/query-monitor-data) ，以及在 [Azure 監視器和 Azure 資料總管之間建立跨服務查詢](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy)。

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="Azure 資料瀏覽器 proxy 流程。":::

例如， (從 Log Analytics) 查詢 Azure 資料總管：
```kusto
CustomEvents | where aField == 1
| join (adx("Help/Samples").TableName | where secondField == 3) on $left.Key == $right.key
```
如果外部查詢查詢工作區中的資料表，然後在 Azure 資料總管叢集中與另一個資料表聯結 (在此案例中，clustername = help，databasename = samples) 使用新的 "adx ( # A3" 函式，就像您如何在查詢文字中查詢另一個工作區一樣。

> [!NOTE]
> * 從 azure 資料總管直接從 Azure 資料總管用戶端工具查詢 Azure 監視器資料，或透過在 Azure 資料總管叢集上執行查詢來間接查詢資料的功能處於預覽模式。
> * 如有任何問題，請洽詢「 [跨服務查詢](mailto:adxproxy@microsoft.com) 」團隊。

## <a name="query-exported-log-analytics-data-from-azure-blob-storage-account"></a>從 Azure Blob 儲存體帳戶查詢匯出的 Log Analytics 資料

將資料從 Azure 監視器匯出至 Azure 儲存體帳戶可提供低成本的保留，以及將記錄重新配置到不同區域的能力。

使用 Azure 資料總管來查詢從 Log Analytics 工作區匯出的資料。 一旦設定之後，從您的工作區傳送到 Azure 儲存體帳戶的支援資料表將可作為 Azure 資料總管的資料來源。 [使用 Azure 資料總管 (preview) 從 Azure 監視器查詢匯出的資料 ](https://docs.microsoft.com/azure/azure-monitor/platform/azure-data-explorer-query-storage)。

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-query.png" alt-text="Azure 資料總管來自儲存體流程的查詢。":::

>[!tip] 
> * 若要將 Log Analytics 工作區中的所有資料匯出至 Azure 儲存體帳戶或事件中樞，請使用 Azure 監視器記錄的 Log Analytics 工作區資料匯出功能。 [請參閱 Azure 監視器 (preview) 中的 Log Analytics 工作區資料匯出 ](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)。

## <a name="next-steps"></a>後續步驟
深入了解：
* [在 Azure 資料總管和 Azure 監視器之間建立跨服務查詢](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)。 從 Azure 資料總管查詢 Azure 監視器資料
* [在 Azure 監視器和 Azure 資料總管之間建立跨服務查詢](https://docs.microsoft.com/azure/azure-monitor/platform/azure-monitor-data-explorer-proxy)。 從 Azure 監視器查詢 Azure 資料總管資料
* [Azure 監視器 (preview) 中的 Log Analytics 工作區資料匯出 ](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)。 使用 Log Analytics 匯出的資料連結及查詢 Azure Blob 儲存體帳戶。