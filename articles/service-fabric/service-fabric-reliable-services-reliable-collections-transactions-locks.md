---
title: 可靠集合中的交易和鎖定模式
description: Azure Service Fabric Reliable State Manager 和 Reliable Collections 交易和鎖定。
ms.topic: conceptual
ms.date: 5/1/2017
ms.custom: sfrev
ms.openlocfilehash: 5f7b3a4d43d35f0d2965dd33c8f69143f4b3a8f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938917"
---
# <a name="transactions-and-lock-modes-in-azure-service-fabric-reliable-collections"></a>Azure Service Fabric Reliable Collections 中的交易和鎖定模式

## <a name="transaction"></a>交易

交易就是以單一工作邏輯單元執行的一連串作業。 它會展示資料庫交易的常見[ACID](https://en.wikipedia.org/wiki/ACID) *（不可*部分完成性、*一致性*、*隔離*、*耐久性*）屬性：

* **不可部分完成性**︰交易必須是不可部分完成的工作單位。 換句話說，執行其所有資料修改，或完全不執行。
* **一致性**︰交易完成時，所有資料必須維持一致的狀態。 所有內部資料結構在交易結束時必須是正確的。
* **隔離**︰並行交易所做的修改，必須與任何其他並行交易所做的修改隔離。 在[ITransaction](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.itransaction?view=azure-dotnet)內用於作業的隔離等級是由執行作業的[IReliableState](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.data.ireliablestate?view=azure-dotnet)所決定。
* **耐久性**：交易完成之後，其作用會永久存在系統中。 即使發生系統失敗仍會保存修改。

### <a name="isolation-levels"></a>隔離層級

隔離層級定義交易必須與其他交易所做的修改隔離的程度。
可靠的集合支援兩種隔離等級：

* **可重複讀取**：指定陳述式無法讀取已經修改但尚未由其他交易確認的資料，以及指定在目前的交易完成之前，任何其他交易都不能修改已經由目前交易讀取的資料。
* **快照集**：指定交易中任何陳述式所讀取的資料都會與交易開始時就存在的資料版本一致。
  交易只能辨識交易開始之前所認可的資料修改。
  在目前交易中執行的陳述式，看不到其他交易在目前交易開始之後所進行的資料修改。
  效果就如同交易中的陳述式會取得認可資料的快照集，因為這項資料於交易開始時就存在。
  可靠的集合的快照集都是一致的。

可靠的集合會依據交易建立時的作業和複本角色，自動選擇要用於指定讀取作業的隔離層級。
下表說明可靠的字典和佇列作業的隔離等級預設值。

| 作業 \ 角色 | Primary | 次要 |
| --- |:--- |:--- |
| 單一實體讀取 |可重複讀取 |快照式 |
| 列舉、計數 |快照式 |快照式 |

> [!NOTE]
> 單一實體作業的常見範例包括 `IReliableDictionary.TryGetValueAsync`、`IReliableQueue.TryPeekAsync`。
> 

可靠的字典和可靠的佇列都支援*讀取您的寫入*。
換句話說，在屬於相同交易的下列讀取作業皆可看到交易內的任何寫入作業。

## <a name="locks"></a>鎖定

在 Reliable Collections 中，所有交易都會實作嚴格的兩階段鎖定：在以中止或認可來終止交易之前，交易不會釋放它所取得的鎖定。

可靠的字典會針對所有單一實體作業使用資料列層級鎖定。
可靠的佇列則會針對嚴格交易的 FIFO 屬性交換並行。
可靠的佇列會使用作業層級的鎖定， `TryPeekAsync`允許一次`TryDequeueAsync`有和/或`EnqueueAsync`的一筆交易，並使用一筆交易。
請注意，為維持 FIFO，如果 `TryPeekAsync` 或 `TryDequeueAsync` 曾觀察到可靠的佇列是空的，則它們也會鎖定 `EnqueueAsync`。

寫入作業一律會採取「獨佔」鎖定。
對於讀取作業，鎖定取決於幾個因素：

- 使用快照集隔離完成的任何讀取作業都是無鎖定。
- 任何可重複讀取作業預設都會採用共用鎖定。
- 不過，針對支援可重複讀取的任何讀取作業，使用者可以要求更新鎖定，而不是共用鎖定。
更新鎖定是一種非對稱式鎖定，用來避免常見的死結，這些死結通常會在多個交易鎖定資源稍後進行可能更新時發生。

下表中可找到鎖定相容性矩陣：

| 要求 \ 授與 | 無 | Shared | 更新 | 獨佔 |
| --- |:--- |:--- |:--- |:--- |
| Shared |無衝突 |無衝突 |衝突 |衝突 |
| 更新 |無衝突 |無衝突 |衝突 |衝突 |
| 獨佔 |無衝突 |衝突 |衝突 |衝突 |

可靠的集合 Api 中的 timeout 引數會用於偵測鎖死。
例如，有兩筆交易 (T1 和 T2) 嘗試讀取和更新 K1。
這樣很可能會形成死結，因為它們最後都會有共用鎖定。
在此情況下，其中一項或兩項作業將會超時。我在此案例中，更新鎖定可能會阻止這類的鎖死。

## <a name="next-steps"></a>後續步驟

* [使用可靠的集合](service-fabric-work-with-reliable-collections.md)
* [Reliable Services 通知](service-fabric-reliable-services-notifications.md)
* [備份與還原 Reliable Services (災害復原)](service-fabric-reliable-services-backup-restore.md)
* [Reliable State Manager 組態](service-fabric-reliable-services-configuration.md)
* [可靠的集合的開發人員參考資料](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
