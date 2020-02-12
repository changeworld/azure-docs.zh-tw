---
title: 最佳化 Azure Cosmos DB 中多區域部署的成本
description: 這篇文章說明如何管理 Azure Cosmos DB 中多區域部署的成本。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753310"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化多重區域成本

您可以隨時在 Azure Cosmos 帳戶中新增及移除區域。 您為各種 Azure Cosmos 資料庫和容器設定的輸送量，將保留在與您的帳戶相關聯的每個區域中。 如果每小時佈建輸送量，亦即在 Azure Cosmos 帳戶的所有資料庫和容器中設定的 RU/秒總和為 `T` 且與您的資料庫帳戶相關聯的 Azure 區域數目為 `N`，則指定小時的 Cosmos 帳戶的總佈建輸送量等於：

1. `T x N RU/s` 如果您的 Azure Cosmos 帳戶已設定為使用單一寫入區域。 

1. `T x (N+1) RU/s`如果您的 Azure Cosmos 帳戶設定可處理寫入的所有區域。 

單一寫入區域佈建的輸送量為每 100 RU/秒美元 0.008/小時，多個可寫入區域佈建的輸送量為每 100 RU/秒美元 0.016/小時。 若要深入了解，請參閱 Azure Cosmos DB [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="costs-for-multiple-write-regions"></a>多個寫入區域的成本

在多重主機系統中，寫入作業的淨可用 RU 會增加 `N` 次，其中 `N` 是寫入區域的數目。 不同於單一區域寫入，每個區域現在都是可寫入，且應該支援衝突解決。 寫入器的工作負載量增加。 從成本規劃的觀點來看，若要在全球執行 `M` RU/秒的寫入，您將需要在容器或資料庫層級布建 M `RUs`。 然後，您可以根據需要加入任意數目的區域，並將其用於寫入以執行 `M` RU 價值的全球寫入。 

### <a name="example"></a>範例

假設您本月在美國西部以每秒 10,000 RU 的輸送量佈建了一個容器，並儲存了 1TB 的資料。 假設您新增三個區域 - 美國東部、北歐和東亞，每個區域都有相同的儲存體和輸送量，且希望能從您全球散發的應用程式寫入所有四個區域中的容器。 您每月帳單總計 (假設一個月 31 天) 如下所示：

|**Item**|**使用量 (每月)**|**費率**|**每個月的成本**|
|----|----|----|----|
|美國西部容器的輸送量計費 (多個寫入區域) |10K RU/秒 * 24 * 31 |每小時 $0.016 (每 100 RU/秒) |$1,190.40 |
|3 個新增區域 (美國東部、北歐和東亞) 的輸送量計費 (多個寫入區域) |(3 + 1) * 10K RU/秒 * 24 * 31 |每小時 $0.016 (每 100 RU/秒) |$4,761.60 |
|美國西部容器的儲存體計費 |1 TB （或 1024 GB） |$0.25/GB |$256 |
|其他 3 個區域的儲存體計費 - 美國東部、北歐和東亞 |3 * 1 TB （或 3072 GB） |$0.25/GB |$768 |
|**總計**|||**$6976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>改善每個區域的輸送量使用率

如果您的使用率很低，例如，一或多個未充分利用或過度使用的區域，您可以採取下列步驟來改善輸送量使用率：  

1. 請確定首先最佳化寫入區域中佈建的輸送量 (RU)，然後透過使用讀取區域中的變更摘要等，在讀取區域中最大限度地使用 RU。 

2. 可以在與 Azure Cosmos 帳戶相關聯的所有區域上，調整多個寫入區域的讀取和寫入。 

3. 監控您區域中的活動，您可以依需求新增和移除區域，以調整您的讀取和寫入輸送量。

## <a name="next-steps"></a>後續步驟

接下來，您可以利用下列文章繼續深入了解 Azure Cosmos DB 中有關成本最佳化的詳細資訊：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 上的計費](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](optimize-cost-queries.md)

