---
title: 使用流式引入將資料引入 Azure 資料資源管理器
description: 瞭解如何使用流式引入將資料引入（載入）到 Azure 資料資源管理器中。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297079"
---
# <a name="streaming-ingestion-preview"></a>流式引入（預覽）

當您需要低延遲且攝入時間小於 10 秒時，對於不同的卷資料，請使用流式處理。 它用於優化許多表的操作處理，在一個或多個資料庫中，其中資料流程到每個表相對較小（每秒記錄很少），但總體資料引入量很高（每秒數千條記錄）。 

當資料量增長到每表超過每秒 1 MB 時，請使用批量引入，而不是流式處理。 閱讀[資料引入概述](/azure/data-explorer/ingest-data-overview)，瞭解有關各種攝入方法的詳細資訊。

## <a name="prerequisites"></a>Prerequisites

* 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費 Azure 帳戶](https://azure.microsoft.com/free/)。
* 登錄到 Web [UI](https://dataexplorer.azure.com/)。
* [創建 Azure 資料資源管理器群集和資料庫](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>在群集上啟用流式處理

> [!WARNING]
> 在啟用蒸汽攝入之前，請查看[限制](#limitations)。

1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 **"設置"** 中，選擇 **"配置**"。 
1. 在 **"配置"** 窗格中，選擇 **"打開**"以啟用**流式處理**。
1. 選取 [儲存]****。
 
    ![流引入](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. 在[Web UI](https://dataexplorer.azure.com/)中，在接收流資料的表或資料庫上定義[流引入策略](/azure/kusto/management/streamingingestionpolicy)。 

    > [!NOTE]
    > * 如果在資料庫級別定義了策略，則資料庫中的所有表都啟用以進行流式處理。
    > * 應用的策略只能引用新引入的資料，而不能參考資料庫中的其他表。

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>使用流式引入將資料引入群集

有兩種受支援的流式處理類型：


* [**事件中心**](/azure/data-explorer/ingest-data-event-hub)， 用作資料來源
* **自訂引入**要求您編寫使用 Azure 資料資源管理器用戶端庫之一的應用程式。 請參閱[應用程式範例的流式引入示例](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample)。

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>選擇適當的流式引入類型

|   |事件中樞  |自訂引入  |
|---------|---------|---------|
|引入啟動和可用於查詢的資料之間的資料延遲   |    更長的延遲     |   更短的延遲      |
|開發開銷    |   快速、簡便的設置，無需開發開銷    |   應用程式處理錯誤並確保資料一致性的高開發開銷     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>禁用群集上的流式引入

> [!WARNING]
> 禁用流式處理可能需要幾個小時。

1. 從所有相關表和資料庫中刪除[流式引入策略](/azure/kusto/management/streamingingestionpolicy)。 流式引入策略刪除觸發從初始存儲到列存儲中的永久存儲（範圍或分片）的流式引入資料移動。 資料移動可以持續幾秒鐘到幾個小時，具體取決於初始存儲中的資料量以及群集使用 CPU 和記憶體的方式。
1. 在 Azure 入口網站中，移至您的 Azure 資料總管叢集。 在 **"設置"** 中，選擇 **"配置**"。 
1. 在 **"配置"** 窗格中，選擇 **"關閉**"以禁用**流式處理**。
1. 選取 [儲存]****。

    ![資料流引入](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>限制

* 流式引入不支援[資料庫游標](/azure/kusto/management/databasecursor)或[資料對應](/azure/kusto/management/mappings)。 僅支援[預先創建](/azure/kusto/management/create-ingestion-mapping-command)的資料對應。 
* 流式處理性能和容量隨 VM 和群集大小增加而擴展。 併發引入僅限於每個核心六個引入。 例如，對於 16 個核心 SKU（如 D14 和 L16），最大支援的負載為 96 個併發引入。 對於兩個核心 SKU（如 D11），最大支援的負載為 12 個併發引入。
* 每個引入請求的資料大小限制為 4 MB。
* 架構更新（如創建和修改表和引入映射）可能需要長達五分鐘的時間進行流式處理服務。
* 在群集上啟用流式處理（即使資料不是通過流引入）也會使用群集電腦的本地 SSD 磁片的一部分來資料流引入資料，並減少可用於熱緩存的存儲。
* 無法在流式處理資料上設置[擴展盤區標記](/azure/kusto/management/extents-overview#extent-tagging)。

## <a name="next-steps"></a>後續步驟

* [Azure 資料資源管理器中的查詢資料](web-query-data.md)
