---
title: 最佳化 Azure Cosmos DB 中的讀取和寫入成本
description: 這篇文章說明如何在執行讀取和寫入資料的作業時，降低 Azure Cosmos DB 的成本。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 725876594a7e7c5f3b3a02802f487dc5fdfb64dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535930"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>最佳化 Azure Cosmos DB 中的讀寫成本

本文說明如何計算從 Azure Cosmos DB 讀取和寫入資料所需的成本。 讀取作業包括項目上的取得作業和寫入作業，包括項目的插入、取代、刪除和更新插入。  

## <a name="cost-of-reads-and-writes"></a>讀取和寫入的成本

Azure Cosmos DB 透過使用佈建的輸送量模型，在輸送量和延遲方面保證可預測的效能。 佈建的輸送量以每秒[要求單位](request-units.md)或 RU/每秒表示。 要求單位 (RU) 是對執行要求所需之運算資源 (例如 CPU、記憶體、IO 等) 的邏輯抽象概念。 佈建的輸送量 (RU) 被預留並專用於您的容器或資料庫，以提供可預測的輸送量和延遲。 佈建的輸送量使 Azure Cosmos DB 能夠以任何規模提供可預測且一致的效能、保證低延遲，以及高可用性。 要求單位代表標準化的貨幣，可簡化應用程式所需之資源的數量推論。 

您不需要思考區分讀取和寫入之間的要求單位。 要求單位的統一貨幣模型可以有效率地以交替方式使用相同的輸送量容量進行讀取和寫入。 下表顯示了 RU/每秒的讀取和寫入成本 (1KB 和 100KB 大小的項目)。

|**專案大小**  |**一次讀取的成本** |**一次寫入的成本**|
|---------|---------|---------|
|1 KB |1 RU |5 RU |
|100 KB |10 RU |50 RU |

讀取 1 KB大小的項目需要花費一個 RU 的成本。 寫入 1 KB大小的項目需要花費五個 RU 的成本。 使用預設的工作階段[一致性層級](consistency-levels.md)時，讀取和寫入成本都適用。  RU 的考慮因素包括：項目大小、屬性計數、資料一致性、已建立索引的屬性，編製索引和查詢模式。

## <a name="optimize-the-cost-of-writes-and-reads"></a>最佳化寫入和讀取的成本

當您執行寫入作業時，應該佈建足夠的容量以支援每秒所需的寫入數目。 您可以在執行寫入之前使用 SDK、入口網站，CLI 來增加佈建的輸送量，然後在寫入完成後減少輸送量。 寫入期間的輸送量是指定的資料所需的最小輸送量，加上插入工作負載所需的輸送量 (假設沒有其他工作負載正在執行)。 

如果您同時執行其他工作負載 (例如，查詢/讀取/更新/刪除)，則應該新增這些作業所需的額外要求單位。 如果寫入作業受速率限制，您就可以使用 Azure Cosmos DB SDK 自訂重試/輪詢原則。 例如，您可以增加負載，直到少量要求受到速率限制。 如果發生速率限制，用戶端應用程式應退出指定重試間隔的速率限制要求。 在重試寫入之前，重試之間應該有最小的時間間隔。 重試原則支援包含在 SQL.NET、Java、Node.js 和 Python SDK，以及所有支援的 .NET Core SDK 版本中。 

您還可以使用 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 將資料大量插入至 Azure Cosmos DB，或將資料從任何支援的來源資料存放區複製到 Azure Cosmos DB。 Azure Data Factory 可與 Azure Cosmos DB Bulk API 原生整合，以在您寫入資料時提供最理想的效能。

## <a name="next-steps"></a>後續步驟

接下來，您可以利用下列文章繼續深入了解 Azure Cosmos DB 中有關成本最佳化的詳細資訊：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化查詢成本](optimize-cost-queries.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)
