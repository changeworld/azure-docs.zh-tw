---
title: 適用于 Azure Cosmos 容器的全域分散式交易和分析（在私人預覽中）儲存體
description: 瞭解適用于 Azure Cosmos 容器的交易式和分析儲存體及其設定選項。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77623386"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>適用于 Azure Cosmos 容器的全域分散式交易式和分析儲存體

Azure Cosmos 容器在內部由兩個儲存引擎支援-交易式儲存引擎和可更新的分析儲存引擎（在個人預覽版中）。 儲存引擎都有記錄結構化和寫入優化，以進行更快速的更新。 不過，每個都有不同的編碼方式：

* **交易式儲存引擎**–它是以資料列導向的格式編碼，以進行快速的交易式讀取和查詢。

* **分析儲存引擎**-以單欄式格式編碼，用於快速分析查詢和掃描。

![儲存引擎和 Azure Cosmos DB API 對應](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

交易式儲存引擎是由本機 Ssd 所支援，而分析儲存體則儲存在成本低廉的叢集 SSD 儲存體上。 下表將捕捉交易式和分析儲存體之間的明顯差異。


|功能  |交易式儲存體  |分析儲存體 |
|---------|---------|---------|
|每個 Azure Cosmos 容器的儲存體上限 |   無限制      |    無限制     |
|每個邏輯分割區索引鍵的最大儲存體   |   20 GB      |   無限制      |
|儲存體編碼  |   資料列導向，使用內部格式。   |   資料行導向，使用 Apache Parquet 格式。 |
|儲存位置 |   由本機/內部叢集 Ssd 所支援的複寫儲存體。 |  以便宜的遠端/叢集 Ssd 支援複寫的儲存體。       |
|耐久性  |    99.99999 （7-9 秒）     |  99.99999 （7-9 秒）       |
|存取資料的 Api  |   SQL、MongoDB、Cassandra、Gremlin、Tables 和 etcd。       | Apache Spark         |
|保留期（存留時間或 TTL）   |  原則導向，使用`DefaultTimeToLive`屬性在 Azure Cosmos 容器上設定。       |   原則導向，使用`ColumnStoreTimeToLive`屬性在 Azure Cosmos 容器上設定。      |
|每 GB 的價格    |   請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|儲存體交易的價格    |  請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>交易式和分析儲存體的優點

### <a name="no-etl-operations"></a>沒有 ETL 作業

傳統的分析管線很複雜，其中每個階段都需要在計算和儲存層進行「提取-轉換-載入」（ETL）作業。 這會導致複雜的資料處理架構。 這表示儲存和計算的多個階段的高成本，以及因為在儲存體和計算的各種階段之間傳輸大量資料而產生的高延遲。  

使用 Azure Cosmos DB 執行 ETL 作業並不會產生任何負擔。 每個 Azure Cosmos 容器都是由交易式和分析儲存引擎所支援，而且交易式和分析儲存引擎之間的資料傳輸是在資料庫引擎內完成，而且沒有任何網路躍點。 相較于傳統的分析解決方案，所產生的延遲和成本會明顯降低。 而且您會針對交易式和分析工作負載，取得單一的全域分散式儲存系統。  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>儲存多個版本、更新及查詢分析儲存體

分析儲存體是完全可更新的，它包含 Azure Cosmos 容器上發生的所有交易式更新的完整版本歷程記錄。

對交易式儲存體所做的任何更新，都保證在30秒內可看到分析儲存體。 

### <a name="globally-distributed-multi-master-analytical-storage"></a>全域散發的多宿主分析儲存體

如果您的 Azure Cosmos 帳戶範圍設定為單一區域，容器中儲存的資料（在交易式和分析儲存體中）也會限定在單一區域中。 另一方面，如果 Azure Cosmos 帳戶是全域散發的，則儲存在容器中的資料也會全域散發。

針對使用多個寫入區域設定的 Azure Cosmos 帳戶，一律會在本機區域中執行寫入至容器（對交易式和分析儲存體）的作業，因此速度很快。

針對單一或多個區域的 Azure Cosmos 帳戶，不論單一寫入區域（單一主機）或多個寫入區域（也稱為多宿主），交易式和分析讀取/查詢都會在指定區域的本機執行。

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>交易和分析工作負載之間的效能隔離

在指定的區域中，交易式工作負載會針對您容器的交易/資料列儲存體進行操作。 另一方面，分析工作負載會針對容器的分析/資料行儲存體運作。 這兩個儲存引擎會獨立運作，並在工作負載之間提供嚴格的效能隔離。

交易式工作負載會使用布建的輸送量（ru）。 與交易式工作負載不同的是，分析工作負載輸送量是以實際的耗用量為基礎。 分析工作負載會隨選耗用資源。

## <a name="next-steps"></a>後續步驟

* [在 Azure Cosmos DB 中的存留時間](time-to-live.md)
