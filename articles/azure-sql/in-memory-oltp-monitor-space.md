---
title: 監視 XTP 記憶體內部儲存體
description: 估計和監視 XTP 記憶體內儲存體使用量、容量；解決容量錯誤 41823
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: juliemsft
ms.author: jrasnick
ms.reviewer: genemi
ms.date: 01/25/2019
ms.openlocfilehash: 84e4ec1e7b3b5ec935bb9d5d819b4562e7fd3e5a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791693"
---
# <a name="monitor-in-memory-oltp-storage-in-azure-sql-database-and-azure-sql-managed-instance"></a>監視 Azure SQL Database 和 Azure SQL 受控執行個體中 In-Memory OLTP 儲存體
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

使用 [記憶體內部 OLTP](in-memory-oltp-overview.md)時，記憶體最佳化資料表中的資料和資料表變數位於記憶體內部 OLTP 儲存體中。

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>判斷資料是否在記憶體內部 OLTP 儲存容量上限內

判斷不同服務層級的儲存體上限。 每個進階和商務關鍵性服務層級都具備記憶體內部 OLTP 儲存體大小上限。

- [以 DTU 為基礎的資源限制-單一資料庫](database/resource-limits-dtu-single-databases.md)
- [以 DTU 為基礎的資源限制-彈性集區](database/resource-limits-dtu-elastic-pools.md)
- [以虛擬核心為基礎的資源限制 - 單一資料庫](database/resource-limits-vcore-single-databases.md)
- [以 vCore 為基礎的資源限制-彈性集區](database/resource-limits-vcore-elastic-pools.md)
- [以 vCore 為基礎的資源限制-受控實例](managed-instance/resource-limits.md)

估計記憶體優化資料表的記憶體需求，其運作方式與 Azure SQL Database 和 Azure SQL 受控執行個體中的 SQL Server 相同。 請花幾分鐘的時間查看 [估計的記憶體需求](/sql/relational-databases/in-memory-oltp/estimate-memory-requirements-for-memory-optimized-tables)。

資料表和資料表變數資料列以及索引，都會計入最大的使用者資料大小。 此外，ALTER TABLE 需要足夠的空間來建立新版的完整資料表及其索引。

一旦超過此限制，在 Azure SQL 受控執行個體的 Azure SQL Database 和資料庫中的單一資料庫，插入和更新作業可能會開始失敗，並出現錯誤41823，Azure SQL Database 中的彈性集區的錯誤41840。 屆時您需要將資料刪除以回收記憶體，或將資料庫的服務層級或計算大小升級。

## <a name="monitoring-and-alerting"></a>監視和警示

您可以在 [Azure 入口網站](https://portal.azure.com/)中，透過計算大小的儲存體上限百分比來監視記憶體內部儲存體使用情形：

1. 在 [資料庫] 刀鋒視窗上，找出 [資源使用率方塊] 並按一下 [編輯]。
2. 選取計量 `In-Memory OLTP Storage percentage`。
3. 若要新增警示，請按一下 [資源使用率] 方塊以開啟 [計量] 刀鋒視窗，然後按一下 [新增警示]。

或使用下列查詢來顯示記憶體內部儲存體使用率：

```sql
    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats
```

## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>更正記憶體內部 OLTP 儲存體不足之狀況 - 錯誤 41823 和 41840

在資料庫中達到記憶體內部 OLTP 儲存體上限時，會造成 INSERT、UPDATE、ALTER 和 CREATE 作業失敗，並出現錯誤訊息 41823 (適用於單一資料庫) 或錯誤 41840 (適用於彈性集區)。 這兩個錯誤都會造成使用中的交易中止。

錯誤訊息 41823 和 41840 表示經記憶體最佳化的資料表以及資料庫或集區中的資料表變數，已達到記憶體內部 OLTP 儲存體大小的上限。

若要解決此錯誤，您可以：

- 從記憶體最佳化資料表中刪除資料，可能會將資料卸載至傳統、以磁碟為基礎的資料表；或者，
- 將服務層級升級至具有足夠記憶體內部記憶體的服務層級，以便儲存您需要保留在記憶體最佳化資料表中的資料。

> [!NOTE]
> 在極少數的情況下，41823 和 41840 錯誤可能是暫時性的，這表示可用的記憶體內部 OLTP 儲存體是足夠的，重試此作業即可成功。 因此，建議您除了監視整體可用的記憶體內部 OLTP 儲存體，也要在首次遇到 41823 或 41840 錯誤時進行重試。 如需重試邏輯的詳細資訊，請參閱[記憶體內部 OLTP 的衝突偵測和重試邏輯](/sql/relational-databases/In-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic)。

## <a name="next-steps"></a>後續步驟

如需監視指引，請參閱 [使用動態管理檢視進行監視](database/monitoring-with-dmvs.md)。