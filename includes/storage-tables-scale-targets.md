---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/09/2020
ms.author: tamram
ms.openlocfilehash: 27617da97ced9ac775beae71e4c25202944b2aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78940947"
---
下表描述了表存儲的容量、可擴充性和性能目標。

| 資源 | 目標 |
|----------|---------------|
| Azure 儲存體帳戶中的資料表數目 | 僅受限於儲存體帳戶的容量 |
| 資料表中的資料分割數目 | 僅受限於儲存體帳戶的容量 |
| 資料分割中的實體數目 | 僅受限於儲存體帳戶的容量 |
| 單個表的最大大小 | 500 TiB |
| 單個實體的最大大小，包括所有屬性值 | 1 MiB |
| 表實體中最大屬性數 | 255 （包括三個系統屬性，**分區鍵**，**行鍵**， 和**時間戳記**） |
| 實體中單個屬性的最大總大小 | 因屬性類型而異。 有關詳細資訊，請參閱[瞭解表服務資料模型的屬性](/rest/api/storageservices/understanding-the-table-service-data-model)**類型**。 |
| **PartitionKey** | 大小高達 1 KiB 的字串 |
| **RowKey** | 大小高達 1 KiB 的字串 |
| 實體組交易記錄的大小 | 事務最多隻能包含 100 個實體，有效負載的大小必須小於 4 MiB。 實體組事務只能包含對實體的更新一次。 |
| 每個表存儲訪問策略的最大數量 | 5 |
| 每一儲存體帳戶的要求率上限 | 每秒 20，000 個事務，假定 1-KiB 實體大小 |
| 單個表分區（1 個 KiB 實體）的目標輸送量 | 每秒最多 2，000 個實體 |