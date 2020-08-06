---
title: 自動、異地多餘備份
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database 和 Azure SQL 受控執行個體會每隔幾分鐘自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地多餘儲存體來進行異地冗余。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 08/04/2020
ms.openlocfilehash: 205e99303cd53adf6aa952ccd65441b72471f3a2
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810257"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>自動備份-Azure SQL Database & SQL 受控執行個體

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>什麼是資料庫備份？

資料庫備份是任何商務持續性和嚴重損壞修復策略不可或缺的一部分，因為它們會保護您的資料免于損毀或刪除。 這些備份可讓您在設定的保留期間內，將資料庫還原到某個時間點。 如果您的資料保護規則要求您的備份可延長時間 (最多10年) ，您可以為單一和集區資料庫設定[長期保留](long-term-retention-overview.md)。

### <a name="backup-frequency"></a>備份頻率

SQL Database 和 SQL 受控執行個體都使用 SQL Server 技術來建立每週的[完整備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)、每隔12-24 小時的[差異備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，以及每隔5到10分鐘的[交易記錄備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 交易記錄備份的頻率是以計算大小和資料庫活動量為基礎。

當您還原資料庫時，服務會判斷需要還原的完整、差異和交易記錄備份。

### <a name="backup-storage-redundancy"></a>備份儲存體冗余

> [!IMPORTANT]
> 備份的可設定儲存體冗余目前僅適用于 SQL 受控執行個體，而且只能在建立受控實例進程期間指定。 布建資源之後，您就無法變更備份儲存體的冗余選項。

設定備份儲存體冗余的選項可讓您彈性地在本機-多餘的 (LRS) 、區域冗余 (ZRS) 或異地冗余 (RA-GRS) [儲存體 blob](../../storage/common/storage-redundancy.md)之間進行選擇。 儲存體冗余機制會儲存資料的多個複本，以防止計畫和未規劃的事件，包括暫時性硬體故障、網路或電源中斷，或大規模的嚴重災難。 這項功能目前僅適用于 SQL 受控執行個體。

GRS 儲存體 blob 會複寫到[配對的區域](../../best-practices-availability-paired-regions.md)，以防止中斷影響主要區域中的備份儲存體，並可讓您在發生嚴重損壞的情況時，將伺服器還原到不同的區域。 

相反地，LRS 和 ZRS 儲存體 blob 可確保您的資料會保留在 SQL Database 或 SQL 受控執行個體部署所在的相同區域中。 區域冗余儲存體 (ZRS) 目前僅適用于) 的[特定區域](../../storage/common/storage-redundancy.md#zone-redundant-storage)。

> [!IMPORTANT]
> 在 SQL 受控執行個體中，設定的備份冗余會套用到短期備份保留設定，以用於時間點還原 (PITR) 以及長期備份所使用的長期保留備份 (LTR) 。

### <a name="backup-usage"></a>備份使用量

您可以使用這些備份來︰

- **現有資料庫**  -  的時間點還原使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API，將[現有的資料庫還原至](recovery-using-backups.md#point-in-time-restore)保留期限內過去的時間點。 針對 SQL Database，此作業會在與原始資料庫相同的伺服器上建立新的資料庫，但會使用不同的名稱來避免覆寫原始資料庫。 還原完成之後，您可以刪除原始資料庫。 或者，您可以[重新命名](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database)原始資料庫，然後將還原的資料庫重新命名為原始的資料庫名稱。 同樣地，針對 SQL 受控執行個體，此作業會在相同的訂用帳戶和相同的區域中，于相同或不同的受控實例上建立資料庫複本。
- **已刪除資料庫**  -  的時間點還原將[已刪除的資料庫還原至刪除的時間](recovery-using-backups.md#deleted-database-restore)或保留期間內的任何時間點。 已刪除的資料庫只能在原始資料庫建立所在的相同伺服器或受控實例上還原。 刪除資料庫時，服務會在刪除之前先取得最後的交易記錄備份，以避免任何資料遺失。
- **異地還原**  - [將資料庫還原到另一個地理區域](recovery-using-backups.md#geo-restore)。 異地還原可讓您在無法存取您的資料庫或主要區域中的備份時，從地理災難中復原。 它會在任何 Azure 區域中的任何現有伺服器或受控實例上建立新的資料庫。
   > [!IMPORTANT]
   > 異地還原僅適用于已設定異地冗余 (RA GRS) 備份儲存體的受控實例。
- **從長期備份還原**  - 如果資料庫已設定長期保留原則 (LTR) ，請從單一資料庫或集區資料庫[的特定長期備份還原資料庫](long-term-retention-overview.md)。 LTR 可讓您使用[Azure 入口網站](long-term-backup-retention-configure.md#using-the-azure-portal)或[Azure PowerShell](long-term-backup-retention-configure.md#using-powershell)來還原舊版本的資料庫，以滿足合規性要求或執行舊版的應用程式。 如需詳細資訊，請參閱[長期保留](long-term-retention-overview.md)。

若要執行還原，請參閱[從備份還原資料庫](recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將 blob 從一個*位置複製到*另一個位置。 在 SQL 中，*資料庫*複寫是指用來讓多個次要資料庫與主資料庫同步的各種技術。

您可以使用下列範例來嘗試備份設定和還原作業：

| 作業 | Azure 入口網站 | Azure PowerShell |
|---|---|---|
| **變更備份保留期** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL 受控執行個體](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL 受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **變更長期備份保留期** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL 受控執行個體-N/A  | [SQL Database](long-term-backup-retention-configure.md)<br/>[SQL 受控執行個體](../managed-instance/long-term-backup-retention-configure.md)  |
| **從某個時間點還原資料庫** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[SQL 受控執行個體](../managed-instance/point-in-time-restore.md) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL 受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **還原已刪除的資料庫** | [SQL Database](recovery-using-backups.md)<br>[SQL 受控執行個體](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL 受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **從 Azure Blob 儲存體還原資料庫** | SQL Database-N/A <br/>SQL 受控執行個體-N/A  | SQL Database-N/A <br/>[SQL 受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>備份排程

在建立或還原新資料庫之後，會立即排程第一次完整備份。 此備份通常會在30分鐘內完成，但當資料庫很大時，可能會花費較長的時間。 例如，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間，這通常會大於新的資料庫。 第一次完整備份之後，會自動排程和管理所有進一步的備份。 所有資料庫備份的確切時間取決於 SQL Database 或 SQL 受控執行個體服務，因為它會平衡整體系統工作負載。 您無法變更備份作業的排程或停用它們。

> [!IMPORTANT]
> 若為新的、已還原或已複製的資料庫，則會在建立初始完整備份之後的初始交易記錄備份時，使用時間點還原功能。

## <a name="backup-storage-consumption"></a>備份儲存體耗用量

有了 SQL Server 備份和還原技術，將資料庫還原到某個時間點需要不中斷的備份鏈，其中包含一個完整備份、選擇性地備份一個差異，以及一個或多個交易記錄備份。 SQL Database 和 SQL 受控執行個體備份排程每週包含一個完整備份。 因此，若要在整個保留期間內啟用 PITR，系統必須儲存額外的完整、差異和交易記錄備份，且最多可達設定的保留期限一周。 

換句話說，在保留期間內的任何時間點，都必須有比最舊的保留期限時間還舊的完整備份，以及從該完整備份到下一次完整備份之前，不中斷的差異和交易記錄備份鏈。

> [!NOTE]
> 若要啟用 PITR，額外的備份會儲存最多一周，超過設定的保留期間。 所有備份的備份儲存體費用都相同。 

不再需要提供 PITR 功能的備份也會自動刪除。 由於差異備份和記錄備份需要較舊的完整備份才能還原，因此所有三種備份類型都會在每週集合中一起清除。

針對包含[TDE 加密](transparent-data-encryption-tde-overview.md)資料庫的所有資料庫，會壓縮備份以減少備份儲存體壓縮和成本。 平均備份壓縮比率為3-4 倍，不過，視資料的本質和資料庫中是否使用資料壓縮而定，它可能會大幅降低或更高。

SQL Database 和 SQL 受控執行個體會計算已使用的備份儲存體總計作為累計值。 此值每小時會回報給 Azure 計費管線，負責匯總此每小時使用量，以計算每個月結束時的耗用量。 刪除資料庫之後，當備份過期並刪除時，耗用量就會減少。 刪除所有備份且不再可能 PITR 之後，就會停止計費。
   
> [!IMPORTANT]
> 即使資料庫已遭刪除，仍會保留資料庫備份來啟用 PITR。 在刪除和重新建立資料庫時，可能會節省儲存空間和計算成本，因為服務會在每次刪除時保留每個已刪除資料庫的備份，因此可能會增加備份儲存體成本。 

### <a name="monitor-consumption"></a>監視耗用量

針對 vCore 資料庫，每個備份類型所使用的儲存體 (完整、差異和記錄) 會在 [資料庫監視] 分頁上報告為個別的計量。 下圖顯示如何監視單一資料庫的備份儲存體耗用量。 此功能目前無法供受控實例使用。

![監視 Azure 入口網站中的資料庫備份耗用量](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微調備份儲存體耗用量

資料庫的最大資料大小的備份儲存體耗用量不會收費。 多餘的備份儲存體耗用量將取決於個別資料庫的工作負載和大小上限。 請考慮下列一些微調技術，以減少您的備份儲存體耗用量：

- 將[備份保留期限](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)縮減為您需求的最小值。
- 請避免執行大型寫入作業，例如索引重建，而不是您所需的頻率。
- 對於大型資料載入作業，請考慮使用叢集資料行存放區[索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，並遵循相關的[最佳作法](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)，並（或）減少非叢集索引的數目。
- 在一般用途服務層級中，布建的資料儲存體會比備份儲存體的價格便宜。 如果您持續過高的備份儲存體成本，您可以考慮增加資料儲存空間，以儲存在備份儲存體上。
- 在應用程式邏輯中使用 TempDB，而不是永久資料表來儲存暫存結果和/或暫時性資料。
- 盡可能使用本機-多餘的備份儲存體 (例如開發/測試環境) 

## <a name="backup-retention"></a>備份保留期

對於所有新的、已還原和複製的資料庫，Azure SQL Database 和 Azure SQL 受控執行個體會保留足夠的備份，以允許過去7天內的 PITR。 除了超大規模資料庫資料庫以外，您可以在1-35 天範圍內變更每個作用中資料庫的[備份保留期限](#change-the-pitr-backup-retention-period)。 如[備份儲存體耗用量](#backup-storage-consumption)中所述，儲存來啟用 PITR 的備份可能會比保留期間還舊。 僅適用于 Azure SQL 受控執行個體，在0-35 天的範圍內刪除資料庫後，就可以設定 PITR 備份保留速率。 

如果您刪除資料庫，系統會以與線上資料庫的特定保留期限相同的方式來保留備份。 您無法變更已刪除之資料庫的備份保留期限。

> [!IMPORTANT]
> 如果您刪除伺服器或受控實例，則該伺服器或受控實例上的所有資料庫也會一併刪除且無法復原。 您無法還原已刪除的伺服器或受控實例。 但是，如果您已為資料庫或受控實例設定了長期保留 (LTR) ，就不會刪除長期保留備份，而且可以用來將不同伺服器或相同訂用帳戶中的受控實例上的資料庫還原到進行長期保留備份的時間點。

過去1-35 天內 PITR 的用途備份保留期有時稱為短期備份保留。 如果您需要保留備份的時間超過35天的最短短期保留期限，您可以啟用[長期保留](long-term-retention-overview.md)。

### <a name="long-term-retention"></a>長期保留

對於 SQL Database 和 SQL 受控執行個體，您可以在 Azure Blob 儲存體中設定完整備份長期保留 (LTR) 最多10年。 設定 LTR 原則之後，會每週自動將完整備份複製到不同的儲存體容器。 若要符合各種合規性需求，您可以針對每週、每月和/或每年完整備份選取不同的保留週期。 儲存體耗用量取決於選取的頻率和 LTR 備份的保留期間。 您可以使用[ltr 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database)來預估 LTR 儲存體的成本。

如需 LTR 的詳細資訊，請參閱[長期備份保留](long-term-retention-overview.md)。

## <a name="storage-costs"></a>儲存體費用

備份儲存體的價格會有所不同，並取決於您的購買模型 (DTU 或 vCore) 、選擇的備份儲存體冗余選項，以及您的區域。 備份儲存體是依取用的每 GB/月計費，如需定價，請參閱[Azure SQL Database 定價](https://azure.microsoft.com/pricing/details/sql-database/single/)頁面和[Azure SQL 受控執行個體定價](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)頁面。

### <a name="dtu-model"></a>DTU 模型

在 DTU 模型中，資料庫和彈性集區的備份儲存體不需要額外付費。 備份儲存體的價格是資料庫或集區價格的一部分。

### <a name="vcore-model"></a>虛擬核心模型

針對 SQL Database 中的單一資料庫，會免費提供與資料庫的資料儲存體大小上限等於100% 的備份儲存體數量。 對於彈性集區和受控實例而言，免費提供的備份儲存體數量等於集區最大資料儲存體的100% 或最大實例儲存體大小。 

若為單一資料庫，此方程式會用來計算可計費的備份儲存體使用量總計：

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

針對集區資料庫，可計費的備份儲存體大小總計會匯總在集區層級，計算方式如下：

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

針對受控實例，可計費的備份儲存體大小總計會在實例層級進行匯總，其計算方式如下：

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

計費備份儲存體總計（若有的話）將以 GB/月計費。 此備份儲存體耗用量將取決於個別資料庫、彈性集區和受控實例的工作負載和大小。 高度修改的資料庫具有較大的差異和記錄備份，因為這些備份的大小與資料變更的數量成正比。 因此，這類資料庫會有較高的備份費用。

SQL Database 和 SQL 受控執行個體會在所有備份檔案中，以累計值來計算您的計費備份儲存體總計。 此值每小時會回報給 Azure 計費管線，這會匯總此每小時使用量，以在每個月結束時取得您的備份儲存體耗用量。 如果刪除資料庫，備份儲存體的耗用量會隨著較舊的備份存留期而逐漸降低，並予以刪除。 由於差異備份和記錄備份需要較舊的完整備份才能還原，因此所有三種備份類型都會在每週集合中一起清除。 刪除所有備份之後，就會停止計費。 

簡單的例子是，假設資料庫已累積 744 GB 的備份儲存體，而此數量在整個月內都維持不變，因為資料庫已完全閒置。 若要將此累計儲存體耗用量轉換成每小時使用量，請將它除以 744.0 (每月31天 * 每日24小時) 。 SQL Database 會向 Azure 計費管線報告資料庫每小時耗用 1 GB 的 PITR 備份（以固定費率計算）。 Azure 計費會匯總此耗用量，並顯示整個月 744 GB 的使用量。 成本會以您的區域中的金額/GB/月費率為基礎。

現在是更複雜的範例。 假設相同的閒置資料庫在該月的中間，將其保留時間增加7天到14天。 這項增加會導致備份儲存體總計加倍到 1488 GB。 SQL Database 會報告 1 GB 的使用量，小時1到 372 () 的前半個月。 它會將時數373到744的使用量報告為 2 GB， () 的後半個月。 此使用量會匯總為每月 1116 GB 的最終帳單。

實際的備份計費案例較複雜。 因為資料庫中的變更速率取決於工作負載，且在一段時間內是可變的，所以每個差異和記錄備份的大小也會不同，導致每小時備份儲存體耗用量有相應的波動。 此外，每個差異備份都會包含自上次完整備份之後，資料庫中所做的所有變更，因此，所有差異備份的總大小會逐漸增加一周的時間，然後在一組較舊的完整、差異和記錄備份過期之後，變得非常顯著。例如，如果在完整備份完成後才執行大量寫入活動（例如索引重建），則索引重建所做的修改將會包含在重建期間所建立的交易記錄備份中、下一次的差異備份中，以及在下一次完整備份之前所進行的每個差異備份。 對於較大資料庫中的第二個案例，服務中的優化會建立完整備份，而不是差異備份（如果差異備份會過度大）。 這會減少所有差異備份的大小，直到下列完整備份為止。

您可以依照[監視耗用量](#monitor-consumption)中所述，監視每個備份類型的備份儲存體總耗用量 (完整、差異、交易記錄) 一段時間。

### <a name="backup-storage-redundancy"></a>備份儲存體冗余

備份儲存體冗余會以下列方式影響備份成本：
- LRS price = x
- ZRS price = 1.25 x
- RA-GRS price = 2x

如需備份儲存體定價的詳細資訊，請參閱[Azure SQL Database 定價頁面](https://azure.microsoft.com/pricing/details/sql-database/single/)和[Azure SQL 受控執行個體定價頁面](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

> [!IMPORTANT]
> 備份的可設定儲存體冗余目前僅適用于 SQL 受控執行個體，而且只能在建立受控實例進程期間指定。 布建資源之後，您就無法變更備份儲存體的冗余選項。

### <a name="monitor-costs"></a>監視成本

若要瞭解備份儲存體成本，請前往 Azure 入口網站的**成本管理 + 帳單**，選取 [**成本管理**]，然後選取 [**成本分析**]。 選取所需的訂用帳戶作為**範圍**，然後篩選您感興趣的時段和服務。

新增 [**服務名稱**] 的篩選，然後在下拉式清單中選取 **[sql database** ]。 使用 [**計量子類別**目錄] 篩選器可選擇服務的計費計數器。 針對單一資料庫或彈性資料庫集區，選取 [**單一/彈性集區 pitr 備份儲存體**]。 針對受控實例，選取 [ **mi] [pitr 備份儲存體**]。 **儲存體**和**計算**子類別也可能會對您感興趣，但不會與備份儲存體成本相關聯。

![備份儲存體成本分析](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > 計量只會顯示在目前使用中的計數器。 如果計數器無法使用，可能是目前未使用該類別。 例如，對於未部署受控實例的客戶，將不會顯示受控實例計數器。 同樣地，不會對未耗用儲存體的資源顯示儲存體計數器。 

## <a name="encrypted-backups"></a>加密的備份

如果您的資料庫使用 TDE 加密，則會自動加密待用備份，包括 LTR 備份。 Azure SQL 中的所有新資料庫都會設定預設為啟用 TDE。 如需有關 TDE 的詳細資訊，請參閱[使用 SQL Database & SQL 受控執行個體的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>備份完整性

Azure SQL 工程小組會持續不斷地測試自動資料庫備份的還原。  (SQL 受控執行個體目前無法使用這項測試。 ) 在還原時間點時，資料庫也會收到 DBCC CHECKDB 完整性檢查。

在完整性檢查期間找到的任何問題都會對工程小組發出警示。 如需詳細資訊，請參閱[SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

所有資料庫備份都會以總和檢查碼選項來執行，以提供額外的備份完整性。

## <a name="compliance"></a>法規遵循

當您將資料庫從以 DTU 為基礎的服務層級遷移至以 vCore 為基礎的服務層級時，會保留 PITR 保留，以確保應用程式的資料恢復原則不會受到危害。 如果預設保留不符合您的合規性需求，您可以變更 PITR 保留期限。 如需詳細資訊，請參閱[變更 PITR 備份保留期限](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>變更 PITR 備份保留期限

您可以使用 Azure 入口網站、PowerShell 或 REST API 來變更預設的 PITR 備份保留期限。 下列範例說明如何將 PITR 保留變更為28天。

> [!WARNING]
> 如果您降低目前的保留期間，就會失去還原至比新保留期間還舊的時間點的能力。 在新的保留期限內，不再需要用來提供 PITR 的備份會被刪除。 如果您增加目前的保留期限，就無法立即在新的保留期間內還原至較舊的時間點。 隨著時間的增加，您可以在系統開始保留較長的備份時取得該功能。

> [!NOTE]
> 這些 Api 只會影響 PITR 保留期間。 如果您為資料庫設定 LTR，則不會受到影響。 如需如何變更 LTR 保留期間的詳細資訊，請參閱[長期保留](long-term-retention-overview.md)。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 入口網站變更 PITR 備份保留期限

若要使用 Azure 入口網站來變更作用中資料庫的 PITR 備份保留期限，請移至伺服器或受控實例，其中包含您要變更其保留期限的資料庫。 

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

SQL Database 的 PITR 備份保留變更會在入口網站的 [伺服器] 頁面上完成。 若要變更伺服器上資料庫的 PITR 保留，請移至 [伺服器總覽] 分頁。 選取左窗格中的 [**管理備份**]，選取變更範圍內的資料庫，然後選取畫面頂端的 [**設定保留**]：

![變更 PITR 保留，伺服器層級](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL 受控執行個體](#tab/managed-instance)

SQL 受控執行個體的 PITR 備份保留變更會在個別資料庫層級完成。 若要從 Azure 入口網站變更實例資料庫的 PITR 備份保留，請移至 [個別資料庫總覽] 分頁。 然後選取畫面頂端的 [**設定備份保留**]：

![變更 PITR 保留，受控實例](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 變更 PITR 備份保留期限

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> SQL Database 和 SQL 受控執行個體仍然支援 PowerShell AzureRM 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需詳細資訊，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組中命令的引數主要與 AzureRm 模組中的參數相同。

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

若要變更作用中 Azure SQL 資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL 受控執行個體](#tab/managed-instance)

若要變更**個別 active** SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

若要變更**所有 active** SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

若要變更**個別已刪除**之 SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

若要變更**所有已刪除**之 SQL 受控執行個體資料庫的 PITR 備份保留，請使用下列 PowerShell 範例。

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

零 (0) 天保留期表示備份會立即刪除，而且不會再保留給已刪除的資料庫。
一旦刪除的資料庫的 PITR 備份保留已縮減，就不再可以增加。

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>使用 REST API 變更 PITR 備份保留期限

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
> 備份的可設定儲存體冗余目前僅適用于 SQL 受控執行個體，而且只能在建立受控實例進程期間指定。 布建資源之後，您就無法變更備份儲存體的冗余選項。

受控實例的備份儲存體冗余只能在實例建立期間設定。 預設值為 [異地冗余儲存體] (GRS) 。 如需當地 (LRS) 之間的定價差異，區域冗余 (ZRS) 和異地冗余 (RA-GRS) 備份儲存體，請造訪[受控實例定價頁面](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)。

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>使用 Azure 入口網站設定備份儲存體冗余

在 Azure 入口網站中，當您建立 SQL 受控執行個體時，[**基本**] 索引標籤上的 [**設定受控執行個體**] 選項可**存取 [變更**備份儲存體冗余] 的選項。
![開啟計算 + 儲存設定-分頁](./media/automated-backups-overview/open-configuration-blade-mi.png)

在 [**計算 + 儲存體**] 分頁上尋找選取備份儲存體冗余的選項。
![設定備份儲存體冗余](./media/automated-backups-overview/select-backup-storage-redundancy-mi.png)

## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入瞭解其他 SQL Database 商務持續性解決方案，請參閱[商務持續性總覽](business-continuity-high-availability-disaster-recover-hadr-overview.md)。
- 取得有關如何[使用 Azure 入口網站將資料庫還原到某個時間點](recovery-using-backups.md)的詳細資訊。
- 取得有關如何[使用 PowerShell 將資料庫還原至某個時間點](scripts/restore-database-powershell.md)的詳細資訊。
- 如需如何使用 Azure 入口網站在 Azure Blob 儲存體中設定、管理自動備份的長期保留及從中還原的詳細資訊，請參閱[使用 Azure 入口網站管理長期備份保留](long-term-backup-retention-configure.md)。
- 如需有關如何使用 PowerShell 在 Azure Blob 儲存體中設定、管理自動備份的長期保留及從中還原的詳細資訊，請參閱[使用 Powershell 管理長期備份保留](long-term-backup-retention-configure.md)。
- 若要瞭解如何微調 Azure SQL 受控執行個體的備份儲存體保留和成本，請參閱[受控執行個體的微調備份儲存體成本](https://aka.ms/mi-backup-tuning)。
