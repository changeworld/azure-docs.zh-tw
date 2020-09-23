---
title: 使用保留原則管理歷程記錄資料-Azure SQL Edge
description: 瞭解如何使用 Azure SQL Edge 中的保留原則來管理歷程記錄資料
keywords: SQL Edge，資料保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 45ce874ffb626f63b2239c66afdefd091114cbd2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888126"
---
# <a name="manage-historical-data-with-retention-policy"></a>使用保留原則管理歷程記錄資料

您可以在資料庫和任何基礎資料表上個別啟用資料保留，讓使用者為其資料表和資料庫建立彈性的過時原則。 套用資料保留很簡單：只需要在資料表建立期間設定一個參數或做為 alter table 作業的一部分。 

針對資料庫和基礎資料表定義之資料保留原則之後，會執行背景時間計時器工作來移除資料表中任何已過時的記錄，以供資料保留之用。 在排程並由系統執行的背景工作中，會以透明的方式，識別相符的資料列，並從資料表中移除它們。 資料表資料列的年齡條件會根據在資料表定義中做為的資料行來檢查 `filter_column` 。 例如，如果保留期限設定為一周，則符合清除資格的資料表資料列符合下列其中一個條件： 

- 如果篩選資料行使用 DATETIMEOFFSET 資料類型，則條件為 `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- 否則，條件為 `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>資料保留清除階段

資料保留清除作業包含兩個階段。 
- 探索階段-在這個階段中，清除作業會識別使用者資料庫中的所有資料表，以建立用於清除的清單。 每天執行一次探索。
- 清除階段-在這個階段中，會針對具有有限資料保留的所有資料表（在探索階段中識別）執行清除。 如果無法在資料表上執行清除作業，則會在目前的執行中略過該資料表，並在下一個反復專案中重試。 清除期間會使用下列原則
    - 如果另一個交易鎖定了過時的資料列，則會略過該資料列。 
    - 使用預設的5秒鎖定 timeout 設定來清除執行。 如果無法在 timeout 視窗內的資料表上取得鎖定，則會在目前的執行中略過該資料表，並在下一個反復專案中重試。
    - 如果在清除資料表期間發生錯誤，則會略過該資料表，並在下一個反復專案中選取該資料表。

## <a name="manual-cleanup"></a>手動清除

根據資料表的資料保留設定和資料庫上的工作負載本質而定，自動清除執行緒可能不會在其執行期間完全移除所有過時的資料列。 為了協助您進行這項操作，並允許使用者手動移除過時的資料列，已 `sys.sp_cleanup_data_retention` 在 AZURE SQL Edge 中引進預存程式。 

這個預存程式會採用三個參數。 
    - 架構名稱-資料表之擁有架構的名稱。 這是必要參數。 
    - 資料表名稱-正在執行手動清除的資料表名稱。 這是必要參數。 
    - rowcount (輸出) 輸出變數。 傳回手動清除 sp 所清除的資料列數。 這是選擇性參數。 

下列範例示範如何執行資料表的手動清除 sp `dbo.data_retention_table` 。

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>如何刪除過時的資料列

清除進程取決於資料表的索引配置。 建立背景工作，以針對具有有限保留期限的所有資料表執行過時的資料清除。 清除 rowstore (B 型樹狀目錄或堆積的邏輯) 索引會以較小的區塊刪除過時資料列 (最多 10K) 將資料庫記錄和 IO 子系統的壓力降至最低。 雖然清除邏輯會利用必要的 B 型樹狀目錄索引，但無法絶對保證早於保留期間之資料列的刪除順序。 因此，請勿在應用程式中對清除順序採用任何相依性。

叢集資料行存放區的清除工作會一次移除整個資料列群組 (通常會在每個) 都包含1000000的資料列，這非常有效率，特別是當資料產生並以較高的步調出現時。

![資料保留清除](./media/data-retention-cleanup/data-retention-cleanup.png)

絕佳的資料壓縮和有效率的保留清除讓叢集資料行存放區索引成為工作負載快速產生大量資料時的最佳選擇。

> [!Note]
> 在 B 型樹狀目錄索引和堆積的案例中，資料保留會在基礎資料表上執行刪除查詢，這可能會與資料表上的 delete 觸發程式發生衝突。 建議您移除資料表中的 delete 觸發程式，或不要在具有刪除 DML 觸發程式的資料表上啟用資料保留。

## <a name="monitoring-data-retention-cleanup"></a>監視資料保留清除

您可以使用 Azure SQL Edge 中 (XEvents) 的擴充事件來監視資料保留原則清除作業。 如需擴充事件的詳細資訊，請參閱 [XEvents 總覽](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)。 

下列六個擴充的事件有助於追蹤清除作業的狀態。 

| Name | 描述 |
|------| ------------|
| data_retention_task_started  | 當具有保留原則的資料表的清除背景工作開始時發生。 |
| data_retention_task_completed  | 在具有保留原則的資料表的清除背景工作結束時，即會發生。 |
| data_retention_task_exception  | 在資料表的特定保留清除進程以外的保留原則清除的背景工作失敗時，就會發生這種情況。 |
| data_retention_cleanup_started  | 當資料表的清除程式開始時發生。 |
| data_retention_cleanup_exception  | 具有保留原則的資料表清除處理失敗。 |
| data_retention_cleanup_completed  | 在具有資料保留原則的資料表清除處理常式結束時發生。 |  

此外，已將名為的新信號緩衝區類型新增 `RING_BUFFER_DATA_RETENTION_CLEANUP` 至 sys.dm_os_ring_buffers 動態管理檢視中。 此視圖可以用來監視資料保留清除作業。 


## <a name="next-steps"></a>後續步驟
- [資料保留原則](data-retention-overview.md)
- [啟用及停用資料保留原則](data-retention-enable-disable.md)
