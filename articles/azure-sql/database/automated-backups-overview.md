---
title: 自動、異地冗余備份
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 和 Azure SQL 受控執行個體會每隔幾分鐘自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地冗余儲存體來進行異地複寫。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: shkale-msft
ms.author: shkale
ms.reviewer: mathoma, stevestein, danil
ms.date: 10/05/2020
ms.openlocfilehash: 43551ca17180cbb3614c670490a19aeaae6c7701
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91728653"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>自動備份-Azure SQL Database & SQL 受控執行個體

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>什麼是資料庫備份？

資料庫備份是任何商務持續性和嚴重損壞修復策略的重要部分，因為它們可保護您的資料免于損毀或刪除。 這些備份可讓資料庫還原至設定的保留期限內的時間點。 如果您的資料保護規則要求您的備份可供使用 (長達10年的時間) ，您可以設定單一和集區資料庫的 [長期保留](long-term-retention-overview.md) 。

### <a name="backup-frequency"></a>備份頻率

SQL Database 和 SQL 受控執行個體使用 SQL Server 技術來每週建立 [完整備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) 、每隔12-24 小時進行 [差異備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) ，以及每隔5到10分鐘執行一次 [交易記錄備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 。 交易記錄備份的頻率是根據計算大小和資料庫活動量。

當您還原資料庫時，服務會判斷需要還原的完整、差異及交易記錄備份。

### <a name="backup-storage-redundancy"></a>備份儲存體冗余

根據預設，SQL Database 和 SQL 受控執行個體會將資料儲存在異地多餘的 (GRS) [儲存體 blob](../../storage/common/storage-redundancy.md) ，以複寫到 [配對的區域](../../best-practices-availability-paired-regions.md)。 這有助於防止中斷影響主要區域中的備份儲存體，並可讓您在發生嚴重損壞時，將伺服器還原到不同的區域。 

設定備份儲存體冗余的選項，可讓您彈性地在 SQL 受控執行個體或 SQL Database 的本機冗余、區域冗余或異地冗余儲存體 blob 之間進行選擇。 為了確保您的資料在部署受控實例或 SQL database 的相同區域內，您可以變更預設的異地冗余備份儲存體冗余，並設定本機冗余的 (LRS) 或區域冗余的 (ZRS) 儲存體 blob 進行備份。 儲存體冗余機制會儲存資料的多個複本，使其受到未規劃和未規劃事件的保護，包括暫時性硬體故障、網路或電力中斷或大規模的災難。 設定的備份儲存體冗余會套用至用於長期還原的短期備份保留設定 (PITR) 和長期備份的長期保留備份 (LTR) 。 

若為 SQL Database 備份儲存體的複本可以在建立資料庫時設定，也可以針對現有的資料庫進行更新;對現有資料庫所做的變更僅適用于未來的備份。 更新現有資料庫的備份儲存體複本之後，最多可能需要48小時才會套用變更。 請注意，在資料庫更新為使用本機或區域多餘的儲存體時，就會立即停用異地還原。 


> [!IMPORTANT]
> 在受控實例建立程式期間設定備份儲存體冗余，因為布建資源之後，就無法再變更儲存體冗余。 

> [!IMPORTANT]
> 區域-多餘的儲存體目前僅適用于 [某些區域](../../storage/common/storage-redundancy.md#zone-redundant-storage)。 

> [!NOTE]
> 適用于 Azure SQL Database 的可設定備份儲存體冗余目前僅適用于東南亞 Azure 區域中的公開預覽。 超大規模層尚無法使用這項功能。 

### <a name="backup-usage"></a>備份使用量

您可以使用這些備份來︰

- **現有資料庫**  -  的時間點還原使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API，將[現有的資料庫還原至](recovery-using-backups.md#point-in-time-restore)保留期限內的過去時間點。 針對 SQL Database，此作業會在與原始資料庫相同的伺服器上建立新的資料庫，但會使用不同的名稱來避免覆寫原始資料庫。 還原完成之後，您可以刪除原始資料庫。 或者，您可以 [重新命名](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database) 原始資料庫，然後將還原的資料庫重新命名為原始的資料庫名稱。 同樣地，針對 SQL 受控執行個體，這項作業會在相同的訂用帳戶和相同區域中的相同或不同受控實例上建立資料庫複本。
- **已刪除資料庫**  -  的還原時間點將[已刪除的資料庫還原至刪除時間](recovery-using-backups.md#deleted-database-restore)或保留期限內的任何時間點。 已刪除的資料庫只能在建立原始資料庫的相同伺服器或受控實例上還原。 刪除資料庫時，服務會在刪除之前先進行最後的交易記錄備份，以防止任何資料遺失。
- **異地還原**  - 將[資料庫還原到另一個地理區域](recovery-using-backups.md#geo-restore)。 異地還原可讓您在無法存取主要區域中的資料庫或備份時，從地理災難中復原。 它會在任何 Azure 區域中的任何現有伺服器或受控實例上建立新的資料庫。
   > [!IMPORTANT]
   > 異地還原僅適用于使用地理位置冗余備份儲存體設定的 SQL 資料庫或受控實例。
- **從長期備份還原**  - 從單一資料庫或集區資料庫的[特定長期備份還原資料庫](long-term-retention-overview.md)，如果已使用長期保留原則設定資料庫 (LTR) 。 LTR 可讓您使用 [Azure 入口網站](long-term-backup-retention-configure.md#using-the-azure-portal) 或 [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) 來還原舊版本的資料庫，以滿足合規性要求或執行舊版的應用程式。 如需詳細資訊，請參閱 [長期保留](long-term-retention-overview.md)。

若要執行還原，請參閱 [從備份還原資料庫](recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞是指將 blob 從某個 *位置複製到* 另一個位置。 在 SQL 中， *資料庫* 複寫是指用來讓多個次要資料庫與主資料庫同步處理的各種技術。

您可以使用下列範例來嘗試備份設定和還原作業：

| 作業 | Azure 入口網站 | Azure PowerShell |
|---|---|---|
| **變更備份保留期** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL 受控執行個體](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL 受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **變更長期備份保留期** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL 受控執行個體-N/A  | [SQL Database](long-term-backup-retention-configure.md)<br/>[SQL 受控執行個體](../managed-instance/long-term-backup-retention-configure.md)  |
| **從某個時間點還原資料庫** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[SQL 受控執行個體](../managed-instance/point-in-time-restore.md) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL 受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **還原已刪除的資料庫** | [SQL Database](recovery-using-backups.md)<br>[SQL 受控執行個體](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL 受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **從 Azure Blob 儲存體還原資料庫** | SQL Database-N/A <br/>SQL 受控執行個體-N/A  | SQL Database-N/A <br/>[SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>備份排程

在建立或還原新資料庫之後，會立即排程第一次完整備份。 此備份通常會在30分鐘內完成，但當資料庫很大時，可能需要較長的時間。 例如，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間，這通常會大於新的資料庫。 第一次完整備份之後，將會自動排程和管理所有進一步的備份。 所有資料庫備份的確切時間取決於 SQL Database 或 SQL 受控執行個體服務，因為它會平衡整體系統工作負載。 您無法變更備份作業的排程，也無法加以停用。

> [!IMPORTANT]
> 若為新的、已還原或已複製的資料庫，時間點還原功能會在建立初始完整備份之後的初始交易記錄備份時變成可用。

## <a name="backup-storage-consumption"></a>備份儲存體耗用量

使用 SQL Server 備份和還原技術，將資料庫還原至某個時間點需要不中斷的備份鏈，其中包含一個完整備份、選擇性地備份一個差異備份，以及一或多個交易記錄備份。 SQL Database 和 SQL 受控執行個體備份排程每週包含一個完整備份。 因此，若要在整個保留期間內啟用 PITR，系統必須儲存額外的完整、差異及交易記錄備份，最多一周的時間超過設定的保留期限。 

換句話說，在保留期間內的任何時間點，都必須有早于保留期限最舊時間的完整備份，以及該完整備份的差異與交易記錄備份鏈，直到下一次完整備份為止。

> [!NOTE]
> 若要啟用 PITR，額外的備份會儲存超過設定的保留期間，最多一周。 備份儲存體會依所有備份的相同費率計費。 

不再需要用來提供 PITR 功能的備份會自動刪除。 由於差異備份和記錄備份需要較早的完整備份才能還原，因此所有三種備份類型都會在每週集合中一起清除。

所有資料庫（包括 [TDE 加密](transparent-data-encryption-tde-overview.md) 的資料庫）都會壓縮備份，以降低備份儲存體壓縮和成本。 平均備份壓縮比例為3-4 次，不過，它可能會根據資料的本質，以及資料庫中是否使用資料壓縮，而明顯較低或更高。

SQL Database 和 SQL 受控執行個體計算已使用的備份儲存體總數作為累計值。 每小時，此值會回報給 Azure 計費管線，該管線負責匯總此每小時使用量，以計算每個月結束時的耗用量。 刪除資料庫之後，會隨著備份存留期而減少耗用量，並予以刪除。 一旦刪除所有備份且無法再 PITR 之後，就會停止計費。
   
> [!IMPORTANT]
> 資料庫的備份會保留來啟用 PITR，即使資料庫已刪除也一樣。 刪除和重新建立資料庫可能會節省儲存體和計算成本，因此可能會增加備份儲存體成本，因為服務會在每次刪除時保留每個已刪除資料庫的備份。 

### <a name="monitor-consumption"></a>監視耗用量

針對 vCore 資料庫，每種類型的備份所使用的儲存體 (完整、差異和記錄) 都會以個別的計量回報在資料庫監視分頁上。 下圖顯示如何監視單一資料庫的備份儲存體耗用量。 這項功能目前不適用於受控實例。

![監視 Azure 入口網站中的資料庫備份耗用量](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微調備份儲存體耗用量

備份儲存體耗用量最多可達資料庫的資料大小上限，而不會收費。 超過備份儲存體耗用量將取決於工作負載和個別資料庫的大小上限。 請考慮下列一些微調技術，以減少您的備份儲存體耗用量：

- 將 [備份保留期限](#change-the-pitr-backup-retention-period-by-using-the-azure-portal) 縮短為您需求的最小可能值。
- 避免進行大型寫入作業（例如索引重建），比您所需更頻繁。
- 針對大型資料載入作業，請考慮使用叢集資料行存放區 [索引](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) 和遵循相關的 [最佳作法](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)，以及/或減少非叢集索引的數目。
- 在一般用途服務層級中，布建的資料儲存體比備份儲存體的價格便宜。 如果您持續增加過高的備份儲存體成本，則可以考慮增加資料儲存空間，以儲存在備份儲存體上。
- 在您的應用程式邏輯中使用 TempDB 來儲存暫時性的結果和/或暫時性資料，而非永久資料表。
- 如果可能的話，請使用本機冗余的備份儲存體 (例如開發/測試環境) 

## <a name="backup-retention"></a>備份保留

針對所有新的、已還原和複製的資料庫，Azure SQL Database 和 Azure SQL 受控執行個體保留足夠的備份，以允許在過去7天內 PITR。 除了超大規模資料庫之外，您可以在1-35 天的範圍內，變更每個使用中資料庫的 [備份保留期限](#change-the-pitr-backup-retention-period) 。 如 [備份儲存體耗用量](#backup-storage-consumption)所述，儲存來啟用 PITR 的備份可能會比保留期限還舊。 僅針對 Azure SQL 受控執行個體，在0-35 天的範圍內刪除資料庫之後，就可以設定 PITR 備份保留費率。 

如果您刪除資料庫，系統會保留備份的方式，就像是線上資料庫的特定保留期限一樣。 您無法變更已刪除之資料庫的備份保留期限。

> [!IMPORTANT]
> 如果您刪除伺服器或受控實例，則該伺服器或受控實例上的所有資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器或受控實例。 但是，如果您已針對資料庫或受控實例設定長期保留 (LTR) ，則不會刪除長期保留備份，並且可以用來將相同訂用帳戶中不同伺服器或受控實例上的資料庫還原到長期保留備份的時間點。

過去1-35 天內 PITR 用途的備份保留期有時稱為短期備份保留。 如果您需要保留備份的時間超過35天的最長短期保留期限，您可以啟用 [長期保留](long-term-retention-overview.md)。

### <a name="long-term-retention"></a>長期保留

針對 SQL Database 和 SQL 受控執行個體，您可以在 Azure Blob 儲存體中設定完整備份長期保留 (LTR) 長達10年。 設定 LTR 原則之後，每週都會自動將完整備份複製到不同的儲存體容器。 為了符合各種合規性需求，您可以針對每週、每月及/或每年完整備份選取不同的保留期限。 儲存體耗用量取決於所選的 LTR 備份頻率和保留期限。 您可以使用 [ltr 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database) 來預估 ltr 儲存體的成本。

> [!IMPORTANT]
> 更新現有 Azure SQL Database 的備份儲存體複本，只會套用到針對資料庫所做的未來備份。 資料庫的所有現有 LTR 備份都會繼續位於現有的儲存體 blob 中，而新的備份將會儲存在要求的儲存體 blob 類型上。 

如需 LTR 的詳細資訊，請參閱 [長期備份保留](long-term-retention-overview.md)。

## <a name="storage-costs"></a>儲存體成本

備份儲存體的價格取決於您的購買模型 (DTU 或 vCore) 、選擇的備份儲存體冗余選項，以及您的區域。 備份儲存體會依取用的每個 GB/月計費，如需定價，請參閱 [Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/) 頁面和 [Azure SQL 受控執行個體定價](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/) 頁面。

### <a name="dtu-model"></a>DTU 模型

在 DTU 模型中，資料庫和彈性集區的備份儲存體不會產生額外費用。 備份儲存體的價格是資料庫或集區價格的一部分。

### <a name="vcore-model"></a>虛擬核心模型

針對 SQL Database 中的單一資料庫，會提供等於資料庫的最大資料儲存體大小100% 的備份儲存體數量，而不會產生額外費用。 針對彈性集區和受控實例，提供的備份儲存體數量等於集區之最大資料儲存體的100% 或實例儲存體大小上限，而不會產生額外費用。 

若為單一資料庫，則會使用此方程式來計算可計費的備份儲存體使用量總計：

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

針對集區資料庫，可計費的備份儲存體大小總計是在集區層級匯總，其計算方式如下：

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

針對受控實例，可計費備份儲存體大小總計會在實例層級匯總，且計算方式如下：

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

計費的備份儲存體總計（如果有的話）會依據所使用的備份儲存體重複率，以 GB/月為單位收費。 這項備份儲存體耗用量將視工作負載和個別資料庫、彈性集區和受控實例的大小而定。 經過大量修改的資料庫具有較大的差異和記錄備份，因為這些備份的大小與資料變更量成正比。 因此，這類資料庫會有較高的備份費用。

SQL Database 和 SQL 受控執行個體會將您的可計費備份儲存體總數計算為所有備份檔案的累計值。 每小時，會將此值回報給 Azure 計費管線，此管線會匯總此每小時使用量，以在每個月結束時取得您的備份儲存體耗用量。 如果刪除了資料庫，備份儲存體的耗用量將會逐漸降低，因為較舊的備份存留期會隨之刪除。 由於差異備份和記錄備份需要較早的完整備份才能還原，因此所有三種備份類型都會在每週集合中一起清除。 一旦刪除所有備份，就會停止計費。 

簡單的範例是，假設資料庫累積了 744 GB 的備份儲存體，而且這個數量在整個月都保持不變，因為資料庫是完全閒置的。 若要將此累積儲存體耗用量轉換為每小時使用量，請將其除以 744.0 (每月31天 * 24 小時的每日) 。 SQL Database 會向 Azure 計費管線報告資料庫每小時耗用 1 GB 的 PITR 備份（以固定速率）。 Azure 計費將會匯總這項耗用量，並顯示整個月 744 GB 的使用量。 成本會以您區域中的金額/GB/月費率為基礎。

現在是更複雜的範例。 假設相同的閒置資料庫將其保留從7天增加到每月的14天。 這項增加會導致備份儲存體總數加倍到 1488 GB。 SQL Database 會回報 1 GB 的使用時間1到 372 () 的前半個月。 它會將小時373到744的使用量回報為 2 GB， () 的下半個月。 此使用方式會匯總為每月 1116 GB 的最終帳單。

實際的備份計費案例更為複雜。 由於資料庫中的變更速率取決於工作負載，且在一段時間內會變動，因此每個差異和記錄備份的大小也會不同，而導致每小時備份儲存體耗用量隨之波動。 此外，每個差異備份都包含自上次完整備份之後，在資料庫中所做的所有變更，因此，所有差異備份的總大小會在一周當中逐漸增加，並在一組較舊的完整、差異和記錄備份過期後逐漸下降。例如，如果在完整備份完成後才執行大量寫入活動（例如索引重建），則會將索引重建所做的修改包含在重建期間、下一次的差異備份中，以及在進行下一次完整備份之前所採取的每個差異備份。 針對較大型資料庫中的第二個案例，如果差異備份會非常大，則服務中的優化會建立完整備份，而不是差異備份。 這會減少所有差異備份的大小，直到下列完整備份為止。

您可以監視每一種備份類型的總備份儲存體耗用量， (完整、差異、交易記錄) 一段時間（如 [監視耗用量](#monitor-consumption)所述）。

### <a name="backup-storage-redundancy"></a>備份儲存體冗余

備份儲存體冗余會以下列方式影響備份成本：
- LRS price = x
- ZRS price = 1.25 x
- RA-GRS 價格 = 2x

如需有關備份儲存體定價的詳細資訊，請造訪 [Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/) 和 [Azure SQL 受控執行個體定價頁面](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

> [!IMPORTANT]
> SQL 受控實例的可設定備份儲存體複本可在所有 Azure 區域中使用，目前僅適用于 SQL Database 的東南亞 Azure 區域。 針對受控執行個體只能在建立受控實例進程期間指定。 布建資源之後，您就無法變更備份儲存體的冗余選項。

### <a name="monitor-costs"></a>監視成本

若要瞭解備份儲存體成本，請移至 Azure 入口網站中的 [ **成本管理 + 計費** ]，選取 [ **成本管理**]，然後選取 [ **成本分析**]。 選取所需的訂用帳戶作為 **範圍**，然後篩選您感興趣的時間週期和服務。

新增 **服務名稱**的篩選，然後選取下拉式清單中的 **[sql database** ]。 使用 [ **計量子類別** ] 篩選器來選擇您服務的帳單計數器。 針對單一資料庫或彈性資料庫集區，請選取 **單一/彈性集區 PITR 備份儲存體**。 針對受控實例，請選取 **MI PITR 備份儲存體**。 **儲存體**和**計算**子類別也可能會對您感興趣，但它們並不會與備份儲存體成本相關聯。

![備份儲存體成本分析](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > 計量僅適用于目前使用中的計數器。 如果無法使用計數器，可能是目前未使用該類別。 例如，未部署受控實例的客戶將不會顯示受控實例計數器。 同樣地，不使用儲存體的資源也看不到儲存體計數器。 

## <a name="encrypted-backups"></a>加密的備份

如果您的資料庫是以 TDE 加密，則備份會自動加密，包括 LTR 備份。 Azure SQL 中的所有新資料庫都設定為預設啟用 TDE。 如需有關 TDE 的詳細資訊，請參閱  [SQL Database & SQL 受控執行個體透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>備份完整性

Azure SQL 工程團隊會持續自動測試自動資料庫備份的還原。  (目前無法在 SQL 受控執行個體中使用這項測試 ) 。在時間點還原時，資料庫也會收到 DBCC CHECKDB 完整性檢查。

在完整性檢查期間找到的任何問題都會對工程小組發出警示。 如需詳細資訊，請參閱 [SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

系統會使用總和檢查碼選項來執行所有資料庫備份，以提供額外的備份完整性。

## <a name="compliance"></a>法規遵循

當您將資料庫從以 DTU 為基礎的服務層級遷移至以 vCore 為基礎的服務層級時，會保留 PITR 保留期，以確保您應用程式的資料修復原則不會遭到入侵。 如果預設保留不符合您的合規性需求，您可以變更 PITR 保留期限。 如需詳細資訊，請參閱 [變更 PITR 備份保留期限](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>變更 PITR 備份保留期限

您可以使用 Azure 入口網站、PowerShell 或 REST API 來變更預設的 PITR 備份保留期限。 下列範例說明如何將 PITR 保留變更為28天。

> [!WARNING]
> 如果您降低目前的保留期限，您將無法還原到比新保留期間還舊的時間點。 在新的保留期限內，不再需要用來提供 PITR 的備份會被刪除。 如果您增加目前的保留期間，則無法立即在新的保留期間內還原至較舊的時間點。 當系統開始保留備份的時間較長時，您會在一段時間內取得該功能。

> [!NOTE]
> 這些 Api 只會影響 PITR 保留期限。 如果您為資料庫設定 LTR，將不會受到影響。 如需如何變更 LTR 保留期間的詳細資訊，請參閱 [長期保留](long-term-retention-overview.md)。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 入口網站來變更 PITR 備份保留期限

若要使用 Azure 入口網站來變更作用中資料庫的 PITR 備份保留期限，請移至您想要變更其保留期限之資料庫的伺服器或受控實例。 

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

SQL Database 的 PITR 備份保留期的變更是在入口網站的 [伺服器] 頁面上完成。 若要變更伺服器上資料庫的 PITR 保留，請移至伺服器總覽分頁。 選取左窗格中的 [ **管理備份** ]，選取變更範圍內的資料庫，然後選取畫面頂端的 [ **設定保留** ]：

![變更 PITR 保留，伺服器層級](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL 受控執行個體](#tab/managed-instance)

SQL 受控執行個體的 PITR 備份保留變更是在個別資料庫層級進行。 若要從 Azure 入口網站變更實例資料庫的 PITR 備份保留，請移至個別資料庫總覽分頁。 然後選取畫面頂端的 [ **設定備份保留** ]：

![變更 PITR 保留期，受控實例](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 變更 PITR 備份保留期限

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> SQL Database 和 SQL 受控執行個體仍支援 PowerShell AzureRM 模組，但未來所有的開發都是針對 Az. Sql 模組。 如需詳細資訊，請參閱[AzureRM。](https://docs.microsoft.com/powershell/module/AzureRM.Sql/) Az 模組中命令的引數本質上與 AzureRm 模組中的引數相同。

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

若要變更作用中 Azure SQL 資料庫的 PITR 備份保留期，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL 受控執行個體](#tab/managed-instance)

若要變更 **個別 active** SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

若要變更 **所有** 使用中 SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

若要變更 **個別已刪除** 之 SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

若要變更 **所有已刪除** 之 SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

零 (0) 天保留期會表示立即刪除備份，且不會再保留已刪除的資料庫。
一旦刪除的資料庫已減少 PITR 備份保留，就不會再增加。

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>使用 REST API 來變更 PITR 備份保留期限

#### <a name="sample-request"></a>範例要求

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Request body

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>範例回應

狀態碼：200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

如需詳細資訊，請參閱[備份保留 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)。

#### <a name="sample-request"></a>範例要求

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>Request body

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>範例回應

狀態碼：200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

如需詳細資訊，請參閱[備份保留 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)。

## <a name="configure-backup-storage-redundancy"></a>設定備份儲存體冗余

> [!NOTE]
> 只有在建立受控實例進程期間，才能指定適用于 SQL 受控執行個體的可設定儲存體冗余。 布建資源之後，您就無法變更備份儲存體的冗余選項。 針對 SQL Database，這項功能的公開預覽版目前僅適用于東南亞 Azure 區域。 

只有在建立實例時，才可以設定受控實例的備份儲存體冗余。 針對 SQL Database 可以在建立資料庫時設定，也可以針對現有的資料庫進行更新。 預設值是異地冗余儲存體 (GRS) 。 如需在本機冗余 (LRS) 、區域冗余 (ZRS) 和異地冗余 (RA-GRS) 備份儲存體之間的定價差異，請造訪 [受控實例定價頁面](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>使用 Azure 入口網站設定備份儲存體冗余

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

在 Azure 入口網站中，您可以在 [ **建立 SQL Database** ] 分頁上設定備份儲存體冗余。 選項可在 [備份儲存體冗余] 區段下取得。 
![開啟 Create SQL Database blade](./media/automated-backups-overview/sql-database-backup-storage-redundancy.png)

#### <a name="sql-managed-instance"></a>[SQL 受控執行個體](#tab/managed-instance)

在 Azure 入口網站中，當您建立 SQL 受控執行個體時，可從 [**基本**] 索引標籤上的 [**設定受控執行個體**] 選項中，變更備份儲存體冗余的選項位於 [**計算 + 儲存體**] 分頁。
![開啟計算 + 儲存體設定-分頁](./media/automated-backups-overview/open-configuration-blade-managedinstance.png)

在 [ **計算 + 儲存體** ] 分頁上尋找選取備份儲存體冗余的選項。
![設定備份儲存體冗余](./media/automated-backups-overview/select-backup-storage-redundancy-managedinstance.png)

---

### <a name="configure-backup-storage-redundancy-by-using-powershell"></a>使用 PowerShell 設定備份儲存體冗余

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

若要在建立新資料庫時設定備份儲存體冗余，您可以指定-BackupStoageRedundancy 參數。 可能的值為 [地區]、[區域] 和 [本機]。 根據預設，所有 SQL 資料庫都會使用異地冗余儲存體進行備份。 如果是使用本機或區域多餘的備份儲存體來建立資料庫，則會停用異地還原。 

```powershell
# Create a new database with geo-redundant backup storage.  
New-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -ServerName "Server01" -DatabaseName "Database03" -Edition "GeneralPurpose" -Vcore 2 -ComputeGeneration "Gen5" -BackupStorageRedundancy Geo
```

如需詳細資訊，請造訪 [新的->set-azsqldatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)。

若要更新現有資料庫的備份儲存體冗余，您可以使用-BackupStorageRedundancy 參數。 可能的值為 [地區]、[區域] 和 [本機]。
請注意，在資料庫上套用變更最多可能需要48小時的時間。 從異地複寫備份存放裝置切換至本機或區域冗余儲存體時，會停用異地還原。 

```powershell
# Change the backup storage redundancy for Database01 to zone-redundant. 
Set-AzSqlDatabase -ResourceGroupName "ResourceGroup01" -DatabaseName "Database01" -ServerName "Server01" -BackupStorageRedundancy Zone
```

如需詳細資料，請造訪 [Set->set-azsqldatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)

> [!NOTE]
> 若要使用-BackupStorageRedundancy 參數搭配資料庫還原、資料庫複製或建立次要作業，請使用 Azure PowerShell Az. Sql 2.11.0 版版本。 


#### <a name="sql-managed-instance"></a>[SQL 受控執行個體](#tab/managed-instance)

若要在受控實例建立期間設定備份儲存體冗余，您可以指定-BackupStoageRedundancy 參數。 可能的值為 [地區]、[區域] 和 [本機]。

```powershell
New-AzSqlInstance -Name managedInstance2 -ResourceGroupName ResourceGroup01 -Location westcentralus -AdministratorCredential (Get-Credential) -SubnetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/resourcegroup01/providers/Microsoft.Network/virtualNetworks/vnet_name/subnets/subnet_name" -LicenseType LicenseIncluded -StorageSizeInGB 1024 -VCore 16 -Edition "GeneralPurpose" -ComputeGeneration Gen4 -BackupStorageRedundancy Geo
```

如需詳細資訊，請造訪 [新的-new-azsqlinstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)。

---

## <a name="use-azure-policy-to-enforce-backup-storage-redundancy"></a>使用 Azure 原則來強制執行備份儲存體冗余

如果您的資料落地需求需要您將所有資料保留在單一 Azure 區域中，您可能會想要使用 Azure 原則對 SQL Database 或受控執行個體強制執列區域冗余或本機多餘的備份。 Azure 原則是一項服務，可讓您用來建立、指派和管理將規則套用至 Azure 資源的原則。 Azure 原則可協助您讓這些資源符合公司標準和服務等級協定的規範。 如需詳細資訊，請參閱 [Azure 原則概觀](https://docs.microsoft.com/azure/governance/policy/overview)。 

### <a name="built-in-backup-storage-redundancy-policies"></a>內建的備份儲存體冗余原則 

新增新的內建原則之後，您可以在訂用帳戶或資源群組層級指派這些原則，以防止建立新的資料庫 (s) 或實例 (s，) 使用異地冗余備份儲存體。 

[SQL Database 應避免使用 GRS 備份備援](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb219b9cf-f672-4f96-9ab0-f5a3ac5e1c13)

[SQL 受控執行個體應避免使用 GRS 備份備援](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9934fd7-29f2-4e6d-ab3d-607ea38e9079)

您可以在 [這裡](https://docs.microsoft.com/azure/azure-sql/database/policy-reference)找到 SQL Database 和受控執行個體內建原則定義的完整清單。

若要在組織層級強制執行資料落地需求，可以將這些原則指派給訂用帳戶。 在訂用帳戶層級指派這些許可權之後，指定訂用帳戶中的使用者將無法透過 Azure 入口網站或 Azure PowerShell，建立具有異地冗余備份儲存體的資料庫或受控實例。 

> [!IMPORTANT]
> 透過 T-sql 建立資料庫時，不會強制執行 Azure 原則。 若要在使用 T-sql 建立資料庫時強制執行資料存放區，請 [使用 ' LOCAL ' 或 ' ZONE ' 作為 CREATE database 語句中 BACKUP_STORAGE_REDUNDANCY 辨識的輸入](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#create-database-using-zone-redundancy-for-backups)。

瞭解如何使用[Azure 入口網站](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal)或[Azure PowerShell](https://docs.microsoft.com/azure/governance/policy/assign-policy-powershell)指派原則


## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要瞭解其他 SQL Database 商務持續性解決方案，請參閱 [商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
- 取得有關如何 [使用 Azure 入口網站將資料庫還原至某個時間點](recovery-using-backups.md)的詳細資訊。
- 取得有關如何 [使用 PowerShell 將資料庫還原至某個時間點](scripts/restore-database-powershell.md)的詳細資訊。
- 如需有關如何使用 Azure 入口網站在 Azure Blob 儲存體中設定、管理自動備份的長期保留及從中還原的詳細資訊，請參閱 [使用 Azure 入口網站管理長期備份保留](long-term-backup-retention-configure.md)。
- 如需有關如何使用 PowerShell 在 Azure Blob 儲存體中設定、管理自動備份的長期保留及從中還原的詳細資訊，請參閱 [使用 Powershell 管理長期備份保留](long-term-backup-retention-configure.md)。
- 若要瞭解如何微調 Azure SQL 受控執行個體的備份儲存體保留和成本，請參閱 [受控執行個體上微調備份儲存體成本](https://aka.ms/mi-backup-tuning)。
