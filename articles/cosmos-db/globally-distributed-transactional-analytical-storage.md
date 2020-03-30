---
title: Azure Cosmos 容器的全球分散式交易和分析（在專用預覽中）存儲
description: 瞭解 Azure Cosmos 容器的事務和分析存儲及其配置選項。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/30/2019
ms.reviewer: sngun
ms.openlocfilehash: 1c2b79f8d0641b1a1386329a2add14ded766bf5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623386"
---
# <a name="globally-distributed-transactional-and-analytical-storage-for-azure-cosmos-containers"></a>Azure Cosmos 容器的全球分散式交易和分析存儲

Azure Cosmos 容器內部由兩個儲存引擎支援 - 事務儲存引擎和可更新的分析儲存引擎（在專用預覽中）。 兩個儲存引擎都是日誌結構化和寫入優化，以便更快地更新。 但是，它們中的每一個的編碼都不同：

* **事務儲存引擎**– 它以面向行的格式編碼，用於快速事務讀取和查詢。

* **分析儲存引擎**- 以柱格式編碼，用於快速分析查詢和掃描。

![儲存引擎和 Azure 宇宙資料庫 API 映射](./media/globally-distributed-transactional-analytical-storage/storage-engines-api-mapping.png)

事務儲存引擎由本地 SSD 支援，而分析存儲存儲在廉價的非群集 SSD 存儲上。 下表捕獲事務存儲和分析存儲之間的顯著差異。


|功能  |事務存儲  |分析存儲 |
|---------|---------|---------|
|每個 Azure Cosmos 容器的最大存儲 |   無限制      |    無限制     |
|每個邏輯分區金鑰的最大存儲量   |   20 GB      |   無限制      |
|存儲編碼  |   面向行，使用內部格式。   |   面向列，使用阿帕奇鑲木地板格式。 |
|存儲區域 |   由本地/群集內 SSD 支援的複製存儲。 |  由廉價的遠端/群集外 SSD 支援的複製存儲。       |
|耐久性  |    99.99999 （7-9 s）     |  99.99999 （7-9 s）       |
|訪問資料的 API  |   SQL、蒙戈DB、卡珊多拉、格雷姆林、表格等。       | Apache Spark         |
|保留（存留時間或 TTL）   |  策略驅動，使用`DefaultTimeToLive`屬性在 Azure Cosmos 容器上配置。       |   策略驅動，使用`ColumnStoreTimeToLive`屬性在 Azure Cosmos 容器上配置。      |
|每 GB 的價格    |   查看[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)     |   查看[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)        |
|存儲事務的價格    |  查看[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)         |   查看[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)        |

## <a name="benefits-of-transactional-and-analytical-storage"></a>事務和分析存儲的好處

### <a name="no-etl-operations"></a>無 ETL 操作

傳統的分析管道非常複雜，每個階段都需要提取轉換載入 （ETL） 操作才能與計算層和存儲層進行。 它導致複雜的資料處理架構。 這意味著多個存儲和計算階段的高成本，以及由於在存儲和計算的不同階段之間傳輸的大量資料而導致的高延遲。  

使用 Azure Cosmos DB 執行 ETL 操作沒有開銷。 每個 Azure Cosmos 容器都由事務和分析儲存引擎支援，事務和分析儲存引擎之間的資料傳輸在資料庫引擎中完成，並且沒有任何網路躍點。 與傳統分析解決方案相比，由此產生的延遲和成本要低得多。 您還可以為事務和分析工作負載提供單個全域分散式存儲系統。  

### <a name="store-multiple-versions-update-and-query-the-analytical-storage"></a>存儲多個版本、更新和查詢分析存儲

分析存儲是完全可更新的，它包含 Azure Cosmos 容器上發生的所有事務更新的完整版本歷程記錄。

對事務存儲所做的任何更新都保證在 30 秒內對分析存儲可見。 

### <a name="globally-distributed-multi-master-analytical-storage"></a>全球分散式多主分析存儲

如果 Azure Cosmos 帳戶的範圍限定為單個區域，則容器中存儲的資料（在事務和分析存儲中）也限定為單個區域。 另一方面，如果 Azure Cosmos 帳戶是全域分佈的，則存儲在容器中的資料也是全域分佈的。

對於配置了多個寫入區域的 Azure Cosmos 帳戶，寫入容器（寫入事務和分析存儲）始終在本地區域中執行，因此速度很快。

對於單個或多區域 Azure Cosmos 帳戶，無論單個寫入區域（單個主控形狀）還是多個寫入區域（也稱為多主控區），事務性讀取和分析讀取/查詢都在給定區域的本地執行。

### <a name="performance-isolation-between-transactional-and-analytical-workloads"></a>事務性工作負載和分析工作負載之間的性能隔離

在給定區域中，事務工作負載針對容器的事務/行存儲進行操作。 另一方面，分析工作負載針對容器的分析/列存儲運行。 兩個儲存引擎獨立運行，並在工作負載之間提供嚴格的性能隔離。

事務工作負載使用預配輸送量 （R） 。 與事務工作負載不同，分析工作負載輸送量基於實際消耗。 分析工作負載按需消耗資源。

## <a name="next-steps"></a>後續步驟

* [在 Azure Cosmos DB 中的存留時間](time-to-live.md)
