---
title: 最佳化 Azure Cosmos DB 中多區域部署的成本
description: 這篇文章說明如何管理 Azure Cosmos DB 中多區域部署的成本。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 010ca40f4f3aacd6353aecd150e944672cc09066
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097498"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化多重區域成本
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

您可以隨時在 Azure Cosmos 帳戶中新增及移除區域。 您為各種 Azure Cosmos 資料庫和容器設定的輸送量，將保留在與您的帳戶相關聯的每個區域中。 如果每小時布建的輸送量，也就是在 Azure Cosmos 帳戶的所有資料庫和容器中設定的 RU/秒總和， `T` 以及與您資料庫帳戶相關聯的 Azure 區域數量 `N` ，則 Cosmos 帳戶的總布建輸送量會等於 `T x N RU/s` 。

單一寫入區域佈建的輸送量為每 100 RU/秒美元 0.008/小時，多個可寫入區域佈建的輸送量為每 100 RU/秒美元 0.016/小時。 若要深入了解，請參閱 Azure Cosmos DB [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。

## <a name="costs-for-multiple-write-regions"></a>多個寫入區域的成本

在多重區域寫入系統中，寫入作業的 net 可用 ru 會增加 `N` `N` 寫入區域數目的時間。 不同于單一區域寫入，每個區域現在都可寫入，並支援衝突解決。 從成本規劃的觀點來看，若要執行 `M` 全球的 RU/秒的寫入，您必須 `RUs` 在容器或資料庫層級布建 M。 然後，您可以根據需要加入任意數目的區域，並將其用於寫入以執行 `M` RU 價值的全球寫入。

### <a name="example"></a>範例

假設您在美國西部有一個容器設定為單一區域寫入，布建的輸送量為 10K RU/秒，並于本月儲存 1 TB 的資料。 讓我們假設您使用相同的儲存體和輸送量來新增區域美國東部，而且您想要能夠從您的應用程式寫入這兩個區域中的容器。 您每月帳單總計 (假設一個月 31 天) 如下所示：

|**Item**|**使用量 (每月)**|**費率**|**每個月的成本**|
|----|----|----|----|
|美國西部容器的輸送量帳單 (單一寫入區域)  |10K RU/秒 * 24 小時 * 31 天 |每小時 $0.008 每 100 RU/秒 |$584.06 |
|2個區域中容器的輸送量計費-美國西部 & 美國東部 (多個寫入區域)  |2 * 10K RU/秒 * 24 小時 * 31 天|每小時 $0.016 (每 100 RU/秒) |$2336.26 |
|美國西部容器的儲存體計費 |1 TB (或 1024 GB)  |$0.25/GB |$256 |
|2個區域的儲存體帳單-美國西部 & 美國東部 |2 * 1 TB (或 3072 GB)  |$0.25/GB |$768 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>改善每個區域的輸送量使用率

如果您的使用率不佳（例如，一或多個使用量過低的讀取區域），您可以在讀取區域中使用變更摘要，採取步驟來充分利用讀取區域中的 ru，或在過度使用時將其移至另一個次要資料庫。 您必須先確定將布建的輸送量優化 (ru 在寫入區域中) 。 除非非常大型的查詢才會將成本寫入超過讀取成本，否則可能會有挑戰性。 整體來說，請監視您區域中已取用的輸送量，並視需要新增或移除區域以調整讀取和寫入輸送量，以確定瞭解部署在相同區域中的任何應用程式對延遲的影響。

## <a name="next-steps"></a>下一步

接下來，您可以利用下列文章繼續深入了解 Azure Cosmos DB 中有關成本最佳化的詳細資訊：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](./optimize-cost-reads-writes.md)