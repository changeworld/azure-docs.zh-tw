---
title: 備份和還原 - 快照，異地冗余
description: 瞭解備份和還原在 Azure 同步分析 SQL 池中的工作方式。 使用備份將資料倉儲還原到主區域中的還原點。 使用異地備援備份來還原至不同的地理區域。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: ae53380572e753a8bcfa20fcd165fa015766263e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349257"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Azure 突觸 SQL 池中的備份和還原

瞭解如何在 Azure Synapse SQL 池中使用備份和還原。 使用 SQL 池還原點恢復資料倉儲或將資料倉儲複製到主區域中的上一個狀態。 使用資料倉儲異地備援備份來還原至不同的地理區域。

## <a name="what-is-a-data-warehouse-snapshot"></a>什麼是資料倉儲快照

「資料倉儲快照集」** 會建立還原點，您可以利用此還原點將資料倉儲還原或複製到先前的狀態。  由於 SQL 池是分散式系統，因此資料倉儲快照由位於 Azure 存儲中的許多檔組成。 快照集會從資料倉儲中儲存的資料內擷取增量變更。

「資料倉儲還原」** 係指從現有或已刪除的資料倉儲還原點建立的新資料倉儲。 還原資料倉儲是所有商務持續性和災害復原策略中不可或缺的一部分，因為它可在您的資料發生意外損毀或刪除之後重新建立該資料。 資料倉儲也是功能強大的機制，可建立資料倉儲的副本，以用於測試或開發。  SQL 池還原速率可能因資料庫大小和目標資料倉儲的位置而異。 

## <a name="automatic-restore-points"></a>自動還原點

快照是創建還原點的服務的內置功能。 您不需啟用此功能。 但是，SQL 池應處於活動狀態，以便還原點創建。 如果 SQL 池經常暫停，則可能無法創建自動還原點，因此請確保在暫停 SQL 池之前創建使用者定義的還原點。 使用者當前無法刪除自動還原點，因為服務使用這些還原點來維護 SL 以進行恢復。

資料倉儲的快照全天拍攝，創建 7 天的還原點。 此保留期無法變更。 SQL 池支援八小時復原點目標 （RPO）。 您可以透過過去七天內所建立的任一個快照集，在主要區域中還原資料倉儲。

要查看上次快照何時啟動，請在連線 SQL 池上運行此查詢。

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>使用者定義的還原點

此功能使您能夠手動觸發快照，以在大型修改之前和之後創建資料倉儲的還原點。 此功能可確保還原點在邏輯上一致，從而在快速恢復時間出現任何工作負載中斷或使用者錯誤時提供額外的資料保護。 使用者定義的還原點可保留七天，而且會自動刪除。 您無法變更使用者定義還原點的保留期。 **只保證在任何時間點設定 42 個使用者定義的還原點**，因此必須先[刪除還原點](https://go.microsoft.com/fwlink/?linkid=875299)，才能再建立另一個還原點。 您可以透過 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint#examples) 或 Azure 入口網站觸發快照集，以建立使用者定義的還原點。

> [!NOTE]
> 如果您需要保留還原點超過 7 天，請[在此](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)投票給這項功能。 您也可以建立使用者定義的還原點，並從新建立的還原點還原到新資料倉儲。 還原後，SQL 池將連線，並可以無限期暫停它以節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 如果您需要作用中的已還原資料倉儲副本，您可以讓其恢復上線 (只需幾分鐘的時間)。

### <a name="restore-point-retention"></a>還原點保留

下面列出了還原點保留期的詳細資訊：

1. SQL 池在達到 7 天保留期**且**至少有 42 個總還原點（包括使用者定義和自動）時刪除還原點。
2. 暫停 SQL 池時不會拍攝快照。
3. 還原點的年齡由自採集還原點（包括暫停 SQL 池時）起的絕對日曆日來衡量。
4. 在任何時候，只要這些還原點未達到 7 天的保留期，SQL 池就保證能夠存儲多達 42 個使用者定義的還原點和 42 個自動還原點
5. 如果拍攝快照，SQL 池將暫停超過 7 天，然後繼續，還原點將一直持續到有 42 個總還原點（包括使用者定義和自動）

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>刪除 SQL 池時的快照保留

刪除 SQL 池時，將創建最終快照並保存 7 天。 您可以將 SQL 池還原到刪除時創建的最終還原點。 如果 SQL 池處於暫停狀態，則不會拍攝快照。 在這種情況下，請確保在刪除 SQL 池之前創建使用者定義的還原點。

> [!IMPORTANT]
> 如果您刪除邏輯 SQL Server 執行個體，所有屬於該執行個體的資料庫也會一併刪除，且無法復原。 您無法還原已刪除的伺服器。

## <a name="geo-backups-and-disaster-recovery"></a>異地備份和災害復原

地理備份每天創建一次到[配對的資料中心](../../best-practices-availability-paired-regions.md)。 異地還原的 RPO 為 24 小時。 您可以將地理備份還原到支援 SQL 池的任何其他區域中的伺服器。 萬一您無法存取主要地區中的還原點，異地備份可以確保您能夠還原資料倉儲。

> [!NOTE]
> 如果您的異地備份需要較短的 RPO，請[在此](https://feedback.azure.com/forums/307516-sql-data-warehouse)投票給這項功能。 您也可以建立使用者定義的還原點，並從新建立的還原點還原到不同區域中的新資料倉儲。 還原之後，資料倉儲會上線，而您可以無限期地暫停它來節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 如果您需要作用中的資料倉儲副本，您可以讓其恢復運上線 (只需幾分鐘的時間)。

## <a name="backup-and-restore-costs"></a>備份與還原成本

您將發現 Azure 帳單含有儲存體的明細項目以及災害復原儲存體的明細項目。 存儲費用是將資料存儲在主區域以及快照捕獲的增量更改的總成本。 如需快照集計費方式的詳細說明，請參閱[了解快照集產生費用的方式](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)。 異地備援的費用則涵蓋儲存異地備份的成本。  

您主要資料倉儲和 7 天快照集變更的總成本會四捨五入到最接近的 TB。 例如，如果您的資料倉儲為 1.5 TB，且快照集取用 100 GB，就會以 Azure 進階儲存體費率向您收取 2 TB 資料費用。

如果您正在使用異地備援儲存體，您會收到個別的儲存體收費項目。 異地備援儲存體是依據標準讀取權限異地備援儲存體 (RA-GRS) 費率收費。

有關 Azure 突觸定價的詳細資訊，請參閱[Azure 突觸定價](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。 跨區域進行還原時，不會向資料出口付費。

## <a name="restoring-from-restore-points"></a>從還原點還原

每個快照都會建立一個代表快照開始時間的還原點。 若要還原資料倉儲，您需選擇一個還原點並發出還原命令。  

您可以保留已還原的資料倉儲和目前的資料倉儲，或是刪除它們其中之一。 如果要將當前資料倉儲替換為還原的資料倉儲，可以使用[ALTER 資料庫 （SQL 池）](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)將資料倉儲重命名為"修改名稱"選項。

要還原資料倉儲，請參閱[還原 SQL 池](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)。

若要還原已刪除或暫停的資料倉儲，您可以[建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="cross-subscription-restore"></a>交叉訂閱還原

如果您需要直接跨訂閱進行還原，[請在此處](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)投票選出此功能。 還原到其他邏輯伺服器，並在訂閱之間["移動"](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)伺服器以執行交叉訂閱還原。 

## <a name="geo-redundant-restore"></a>異地備援還原

您可以將[SQL 池還原](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell)到任何支援 SQL 池的區域，以您選擇的性能級別。

> [!NOTE]
> 若要執行異地備援還原，您不可選擇退出這項功能。

## <a name="next-steps"></a>後續步驟

如需有關災害規劃的詳細資訊，請參閱[商務持續性概觀](../../sql-database/sql-database-business-continuity.md)
