---
title: 備份和還原-快照集、異地冗余
description: 瞭解備份和還原在 Azure Synapse Analytics 專用 SQL 集區中的運作方式。 使用備份來將您的資料倉儲還原至主要區域中的還原點。 使用異地備援備份來還原至不同的地理區域。
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 254e07d5a3266927c9677107772b4ac2dbfd2ce0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454406"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Azure Synapse 專用 SQL 集區中的備份與還原

瞭解如何在 Azure Synapse 專用 SQL 集區中使用備份和還原。 使用專用的 SQL 集區還原點將您的資料倉儲復原或複製到主要區域中的先前狀態。 使用資料倉儲異地備援備份來還原至不同的地理區域。

## <a name="what-is-a-data-warehouse-snapshot"></a>什麼是資料倉儲快照集

「資料倉儲快照集」會建立還原點，您可以利用此還原點將資料倉儲還原或複製到先前的狀態。  由於專用的 SQL 集區是分散式系統，因此資料倉儲快照集是由位於 Azure 儲存體中的許多檔案所組成。 快照集會從資料倉儲中儲存的資料內擷取增量變更。

「資料倉儲還原」係指從現有或已刪除的資料倉儲還原點建立的新資料倉儲。 還原資料倉儲是所有商務持續性和災害復原策略中不可或缺的一部分，因為它可在您的資料發生意外損毀或刪除之後重新建立該資料。 資料倉儲也是功能強大的機制，可建立資料倉儲的副本，以用於測試或開發。 專用的 SQL 集區還原速率會根據來源和目標資料倉儲的資料庫大小和位置而有所不同。

## <a name="automatic-restore-points"></a>自動還原點

快照集是可建立還原點的內建功能。 您不需啟用此功能。 不過，專用的 SQL 集區應該處於作用中狀態，才能建立還原點。 如果經常暫停，可能不會建立自動還原點，因此請務必先建立使用者定義的還原點，然後再暫停專用的 SQL 集區。 使用者目前無法刪除自動還原點，因為服務會使用這些還原點來維護要復原的 Sla。

資料倉儲的快照集會在一天內建立可供七天使用的還原點。 此保留期無法變更。 專用的 SQL 集區支援八小時的復原點目標 (RPO) 。 您可以透過過去七天內所建立的任一個快照集，在主要區域中還原資料倉儲。

若要查看上一個快照集的開始時間，請在線上專用 SQL 集區上執行此查詢。

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>使用者定義的還原點

這項功能可讓您手動觸發快照集，以在進行大型修改之前和之後，建立資料倉儲的還原點。 這項功能可確保還原點在邏輯上是一致的，可在任何工作負載中斷時提供額外的資料保護，或在快速復原時提供使用者錯誤。 使用者定義的還原點可保留七天，而且會自動刪除。 您無法變更使用者定義還原點的保留期。 **只保證在任何時間點設定 42 個使用者定義的還原點**，因此必須先 [刪除還原點](https://go.microsoft.com/fwlink/?linkid=875299)，才能再建立另一個還原點。 您可以透過 [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) 或 Azure 入口網站觸發快照集，以建立使用者定義的還原點。

> [!NOTE]
> 如果您需要保留還原點超過 7 天，請[在此](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points)投票給這項功能。 您也可以建立使用者定義的還原點，並從新建立的還原點還原到新資料倉儲。 還原之後，您就可以在線上使用專用的 SQL 集區，並可無限期地暫停它來節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 如果您需要作用中的已還原資料倉儲副本，您可以讓其恢復上線 (只需幾分鐘的時間)。

### <a name="restore-point-retention"></a>還原點保留

以下列出還原點保留期的詳細資料：

1. 專用的 SQL 集區會在達到7天的保留期間 **，以及** 至少有42的還原 (點（包含使用者定義和自動) ）時，刪除還原點。
2. 當專用的 SQL 集區暫停時，不會執行快照集。
3. 還原點的存留期是以從還原點開始算起的絕對行事曆天數來測量，包括在 SQL 集區暫停的時間。
4. 在任何時間點，只要這些還原點尚未達到7天的保留期限，專用的 SQL 集區保證能夠儲存最多42的使用者定義還原點和42自動還原點
5. 如果建立快照集，則會將專用的 SQL 集區暫停超過7天，然後繼續進行，直到總共有42的還原點 (包括使用者定義和自動) 

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>卸載 SQL 集區時的快照集保留期

當您卸載專用的 SQL 集區時，會建立並儲存7天的最終快照集。 您可以將專用的 SQL 集區還原到刪除時建立的最後一個還原點。 如果以暫停狀態卸載專用 SQL 集區，則不會建立任何快照集。 在這種情況下，請務必先建立使用者定義的還原點，然後再卸載專用的 SQL 集區。

> [!IMPORTANT]
> 如果您刪除裝載專用 SQL 集區的伺服器/工作區，所有屬於該伺服器/工作區的資料庫也會一併刪除，而且無法復原。 您無法還原已刪除的伺服器。

## <a name="geo-backups-and-disaster-recovery"></a>異地備份和災害復原

每天建立一次異地備份至 [配對的資料中心](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 異地還原的 RPO 為 24 小時。 您可以將異地備份還原到支援專用 SQL 集區之任何其他區域中的伺服器。 萬一您無法存取主要地區中的還原點，異地備份可以確保您能夠還原資料倉儲。

> [!NOTE]
> 如果您的異地備份需要較短的 RPO，請[在此](https://feedback.azure.com/forums/307516-sql-data-warehouse)投票給這項功能。 您也可以建立使用者定義的還原點，並從新建立的還原點還原到不同區域中的新資料倉儲。 還原之後，資料倉儲會上線，而您可以無限期地暫停它來節省計算成本。 暫停的資料庫會產生儲存體費用，以「Azure 進階儲存體」費率計費。 如果您需要作用中的資料倉儲副本，您可以讓其恢復運上線 (只需幾分鐘的時間)。

## <a name="backup-and-restore-costs"></a>備份與還原成本

您將發現 Azure 帳單含有儲存體的明細項目以及災害復原儲存體的明細項目。 儲存體費用是將您的資料儲存在主要區域中的總成本，以及快照集所捕獲的增量變更。 如需快照集計費方式的詳細說明，請參閱[了解快照集產生費用的方式](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。 異地備援的費用則涵蓋儲存異地備份的成本。  

您主要資料倉儲和 7 天快照集變更的總成本會四捨五入到最接近的 TB。 例如，如果您的資料倉儲為 1.5 TB，且快照集取用 100 GB，就會以 Azure 進階儲存體費率向您收取 2 TB 資料費用。

如果您正在使用異地備援儲存體，您會收到個別的儲存體收費項目。 異地備援儲存體是依據標準讀取權限異地備援儲存體 (RA-GRS) 費率收費。

如需 Azure Synapse 定價的詳細資訊，請參閱 [Azure Synapse 定價](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)。 跨區域還原時，不會向您收取資料出口的費用。

## <a name="restoring-from-restore-points"></a>從還原點還原

每個快照都會建立一個代表快照開始時間的還原點。 若要還原資料倉儲，您需選擇一個還原點並發出還原命令。  

您可以保留已還原的資料倉儲和目前的資料倉儲，或是刪除它們其中之一。 如果您想要以還原的資料倉儲取代目前的資料倉儲，您可以使用 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 搭配 MODIFY NAME 選項將其重新命名。

若要還原資料倉儲，請參閱 [還原專用的 SQL 集](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)區。

若要還原已刪除或暫停的資料倉儲，您可以[建立支援票證](sql-data-warehouse-get-started-create-support-ticket.md)。

## <a name="cross-subscription-restore"></a>跨訂用帳戶還原

如果您需要在訂用帳戶之間直接還原，請 [在](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)此投票給這項功能。 還原至不同的伺服器，並在訂用帳戶之間「 [移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 」伺服器，以執行跨訂用帳戶還原。

## <a name="geo-redundant-restore"></a>異地備援還原

您可以將 [專用的 sql 集區還原](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) 到任何區域，以選擇的效能層級支援專用的 sql 集區。

> [!NOTE]
> 若要執行異地備援還原，您不可選擇退出這項功能。

## <a name="next-steps"></a>後續步驟

如需還原點的詳細資訊，請參閱 [使用者定義的還原點](sql-data-warehouse-restore-points.md)
