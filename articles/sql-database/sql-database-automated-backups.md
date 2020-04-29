---
title: 自動、異地多餘備份
description: SQL Database 每隔幾鐘會自動建立一個本機資料庫備份，並使用 Azure 讀取權限異地備援儲存體來進行異地備援。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 9ac6927df63d51830a58773e32ad0968920c0867
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80061769"
---
# <a name="automated-backups"></a>自動備份

Azure SQL Database 會自動建立在設定的保留期間內保留的資料庫備份。 它會使用 Azure[讀取權限異地冗余儲存體（RA-GRS）](../storage/common/storage-redundancy.md)來確保即使資料中心無法使用，仍會保留備份。

資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如果您的安全性規則要求您的備份可延長時間（最多10年），您可以在單一資料庫和彈性資料庫集區上設定[長期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份？

SQL Database 使用 SQL Server 的技術，每週建立[完整備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)、每12小時進行[差異備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，以及每隔5到10分鐘的[交易記錄備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 備份會儲存在[GRS 儲存體 blob](../storage/common/storage-redundancy.md)中，並複寫到[配對的資料中心](../best-practices-availability-paired-regions.md)，以防止資料中心中斷。 當您還原資料庫時，服務會判斷需要還原的完整、差異和交易記錄備份。

您可以使用這些備份︰

- 使用 [Azure 入口網站]、[Azure PowerShell]、[Azure CLI] 或 [REST API]，將**現有的資料庫還原到**保留期限內的某個時間點。 針對單一資料庫和彈性資料庫集區，此作業會在與原始資料庫相同的伺服器上建立新的資料庫。 在受控實例中，這項作業可以在相同的訂用帳戶下建立資料庫複本或相同或不同的受控實例。
- 將**已刪除的資料庫還原至刪除的時間**或保留期間內的任何時間。 只有在建立原始資料庫的相同邏輯伺服器或受控實例上，才可以還原已刪除的資料庫。
- **將資料庫還原到另一個地理區域**。 異地還原可讓您在無法存取您的伺服器和資料庫時，從地理災難中復原。 它會在世界各地的任何現有伺服器上建立新的資料庫。
- 如果資料庫是使用長期保留原則（LTR）進行設定，請從單一資料庫或彈性資料庫集區上**的特定長期備份還原資料庫**。 LTR 可讓您使用[Azure 入口網站](sql-database-long-term-backup-retention-configure.md#using-azure-portal)或[Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell)來還原舊版本的資料庫，以滿足合規性要求或執行舊版的應用程式。 如需詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

若要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 儲存體中，「複寫」一詞指的是將檔案從一個*位置複製到*另一個位置。 SQL Server*資料庫*複寫是指讓多個次要資料庫與主資料庫同步處理。

您可以使用下列範例來嘗試其中一些作業：

| | Azure 入口網站 | Azure PowerShell |
|---|---|---|
| 變更備份保留期 | [單一資料庫](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [受控執行個體](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [單一資料庫](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 變更長期備份保留期 | [單一資料庫](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>受控實例-N/A  | [單一資料庫](sql-database-long-term-backup-retention-configure.md)<br/>受控實例-N/A  |
| 從某個時間點還原資料庫 | [單一資料庫](sql-database-recovery-using-backups.md#point-in-time-restore) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 還原已刪除的資料庫 | [單一資料庫](sql-database-recovery-using-backups.md) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [受控執行個體](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| 從 Azure Blob 儲存體還原資料庫 | 單一資料庫-N/A <br/>受控實例-N/A  | 單一資料庫-N/A <br/>[受控執行個體](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>備份頻率

### <a name="point-in-time-restore"></a>時間點還原

SQL Database 藉由自動建立完整備份、差異備份和交易記錄備份，支援以自助式進行時間點還原（PITR）。 完整資料庫備份會每週建立，差異資料庫備份通常每隔12小時建立一次。 交易記錄備份通常每隔5到10分鐘就會建立一次。 交易記錄備份的頻率是以計算大小和資料庫活動量為基礎。 

建立資料庫之後，會立即排程第一次完整備份。 此備份通常會在30分鐘內完成，但當資料庫很大時，可能會花費較長的時間。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 您無法變更或停用備份作業。

PITR 備份會受到異地多餘儲存體的保護。 如需詳細資訊，請參閱 [Azure 儲存體備援](../storage/common/storage-redundancy.md) \(部分機器翻譯\)。

如需 PITR 的詳細資訊，請參閱[還原時間點](sql-database-recovery-using-backups.md#point-in-time-restore)。

### <a name="long-term-retention"></a>長期保留

針對單一和集區資料庫，您可以在 Azure Blob 儲存體中設定完整備份的長期保留（LTR），最長可達10年。 如果您啟用 LTR 原則，每週完整備份會自動複製到不同的 GRS 儲存體容器。 若要符合各種合規性需求，您可以針對每週、每月和/或每年備份選取不同的保留週期。 儲存體耗用量取決於選取的備份頻率和保留期限或期間。 您可以使用[ltr 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database)來預估 LTR 儲存體的成本。

就像 PITR 備份，LTR 備份會以異地多餘儲存體來保護。 如需詳細資訊，請參閱 [Azure 儲存體備援](../storage/common/storage-redundancy.md) \(部分機器翻譯\)。

如需 LTR 的詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

## <a name="backup-storage-consumption"></a>備份儲存體耗用量

若為單一資料庫，此方程式會用來計算備份儲存體的總使用量：

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

針對彈性資料庫集區，備份儲存體大小總計會匯總在集區層級，計算方式如下：

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

在保留期限之前進行的備份會根據其時間戳記自動清除。 由於差異備份和記錄備份需要較舊的完整備份，因此它們會在每週的區塊中一起清除。

Azure SQL Database 會將您的保留備份儲存體總計計算為累計值。 此值每小時會回報給 Azure 計費管線，負責匯總此每小時使用量，以計算每個月結束時的耗用量。 卸載資料庫之後，耗用量會隨著備份存留期而降低。 備份早于保留期間之後，就會停止計費。

   > [!IMPORTANT]
   > 資料庫的備份會保留給指定的保留期間，即使已卸載資料庫也一樣。 在卸載和重新建立資料庫時，通常會儲存儲存體和計算成本，因此可能會增加備份儲存體成本，因為在每次卸載後，Microsoft 都會針對每個卸載的資料庫保留指定保留期限的備份（至少7天）。

### <a name="monitor-consumption"></a>監視耗用量

每種類型的備份（完整、差異和記錄）都會在 [資料庫監視] 分頁上報告為個別的計量。 下圖顯示如何監視單一資料庫的備份儲存體耗用量。 此功能目前無法供受控實例使用。

![監視 Azure 入口網站中的資料庫備份耗用量](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微調備份儲存體耗用量

多餘的備份儲存體耗用量將取決於工作負載和個別資料庫的大小。 請考慮下列一些微調技術，以減少您的備份儲存體耗用量：

* 將[備份保留期限](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)縮減為您需求的最小值。
* 請避免執行大型寫入作業，例如索引重建，而不是您所需的頻率。
* 對於大型資料載入作業，請考慮使用叢集資料行存放區[索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)、減少非叢集索引的數目，以及考慮資料列計數大約1000000的大量載入作業。
* 在一般用途服務層級中，布建的資料儲存體比超額備份儲存體的價格便宜。 如果您持續過高的備份儲存體成本，您可以考慮增加要儲存在備份儲存體上的資料儲存空間。
* 使用 TempDB，而不是 ETL 邏輯中的永久資料表來儲存暫存結果。 （僅適用于受控實例）。
* 請考慮關閉未包含敏感性資料之資料庫的 TDE 加密（例如開發或測試資料庫）。 非加密資料庫的備份通常會以較高的壓縮比率進行壓縮。

> [!IMPORTANT]
> 對於分析資料超市 \ 資料倉儲工作負載，我們強烈建議您使用叢集資料行存放區[索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)、減少非叢集索引的數目，並考慮以1000000的資料列計數進行大量載入作業，以減少多餘的備份儲存體耗用量。

## <a name="storage-costs"></a>儲存成本

儲存體的價格會根據您使用的是 DTU 模型還是 vCore 模型而有所不同。

### <a name="dtu-model"></a>DTU 模型

如果您使用的是 DTU 模型，資料庫和彈性資料庫集區的備份儲存體不會額外收費。

### <a name="vcore-model"></a>虛擬核心模型

針對單一資料庫，會免費提供等於資料庫大小100% 的最小備份儲存體數量。 若為彈性資料庫集區和受控實例，則會免費提供最少的備份儲存體數量，分別等於針對集區或實例大小所配置之資料儲存體的100%。 備份儲存體的額外使用量會按每月每 GB 來收費。 這項額外的耗用量將取決於個別資料庫的工作負載和大小。

Azure SQL Database 會將您的保留備份儲存體總計計算為累計值。 此值每小時會回報給 Azure 計費管線，負責匯總此每小時使用量，以在每個月結束時取得您的耗用量。 在卸載資料庫之後，Microsoft 會在備份存留期間降低耗用量。 備份早于保留期間之後，就會停止計費。 因為所有記錄備份和差異備份都會保留一段完整的保留期間，所以大量修改的資料庫會有較高的備份費用。

假設資料庫已累積 744 GB 的備份儲存體，且此數量在整個月內都保持不變。 若要將此累計儲存體耗用量轉換成每小時使用量，請將它除以744.0 （每月31天 * 每日24小時）。 因此 SQL Database 會報告資料庫每小時耗用 1 GB 的 PITR 備份。 Azure 計費會匯總此耗用量，並顯示整個月 744 GB 的使用量。 成本會以您的區域中的 $/Gb/月費率為基礎。

現在是更複雜的範例。 假設資料庫的保留期在當月中間已增加至14天。 假設這項增加（假設情況下）會導致備份儲存體總計加倍到 1488 GB。 SQL Database 會報告 1 GB 的使用量，小時1到372。 它會將時數373到744的使用量報告為 2 GB。 此使用量會匯總為每月 1116 GB 的最終帳單。

### <a name="monitor-costs"></a>監視成本

若要瞭解備份儲存體成本，請前往 Azure 入口網站的**成本管理 + 帳單**，選取 [**成本管理**]，然後選取 [**成本分析**]。 選取所需的訂用帳戶作為**範圍**，然後篩選您感興趣的時段和服務。

新增 [**服務名稱**] 的篩選，然後在下拉式清單中選取 **[sql database** ]。 使用 [**計量子類別**目錄] 篩選器可選擇服務的計費計數器。 針對單一資料庫或彈性資料庫集區，選取 [**單一/彈性集區 pitr 備份儲存體**]。 針對受控實例，選取 [ **mi] [pitr 備份儲存體**]。 **儲存體**和**計算**子類別也可能會對您感興趣，但不會與備份儲存體成本相關聯。

![備份儲存體成本分析](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>備份保留期

所有 Azure SQL 資料庫（單一、集區和受控實例資料庫）的預設備份保留期限為7天。 您可以[將備份保留期限變更](#change-the-pitr-backup-retention-period)為長達35天。

如果您刪除資料庫，則 SQL Database 會以保存線上資料庫備份的相同方式保存備份。 例如，如果您刪除保留期間為七天的基本資料庫，則會儲存為期四天的備份，並在三天內儲存。

如果您需要保留備份的時間超過保留週期上限，則可以修改備份的屬性，以將一或多個長期保留週期新增至資料庫。 如需詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果您刪除裝載 SQL 資料庫的 Azure SQL server，則也會一併刪除所有屬於該伺服器的彈性資料庫集區和資料庫。 它們無法復原。 您無法還原已刪除的伺服器。 但是，如果您設定了長期保留，將不會刪除以 LTR 進行的資料庫備份，而且可以還原這些資料庫。

## <a name="encrypted-backups"></a>加密的備份

如果您的資料庫使用 TDE 加密，則會自動加密待用備份，包括 LTR 備份。 Azure SQL 資料庫啟用 TDE 時，也會加密備份。 所有新的 Azure SQL 資料庫預設都會設定為啟用 TDE。 如需 TDE 的詳細資訊，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>備份完整性

Azure SQL Database 工程小組會持續自動測試已放置於邏輯伺服器和彈性資料庫集區之資料庫的自動資料庫備份的還原。 （此測試無法在受控實例中使用）。在還原時間點時，資料庫也會收到 DBCC CHECKDB 完整性檢查。

在完成遷移之後，受控實例`CHECKSUM`會使用原生`RESTORE`命令所還原的資料庫，或使用 Azure 資料移轉服務來進行自動初始備份。

在完整性檢查期間找到的任何問題都會對工程小組發出警示。 如需詳細資訊，請參閱[Azure SQL Database 中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="compliance"></a>合規性

當您將資料庫從以 DTU 為基礎的服務層級遷移至以 vCore 為基礎的服務層級時，會保留 PITR 保留，以確保應用程式的資料恢復原則不會受到危害。 如果預設保留不符合您的合規性需求，您可以使用 PowerShell 或 REST API 來變更 PITR 保留期限。 如需詳細資訊，請參閱[變更 PITR 備份保留期限](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>變更 PITR 備份保留期限

您可以使用 Azure 入口網站、PowerShell 或 REST API 來變更預設的 PITR 備份保留期限。 下列範例說明如何將 PITR 保留變更為28天。

> [!WARNING]
> 如果您降低目前的保留期間，所有早于新保留期間的現有備份都將無法再使用。 如果您增加目前的保留期間，SQL Database 會保留現有的備份，直到達到較長的保留期間結束為止。

> [!NOTE]
> 這些 Api 只會影響 PITR 保留期間。 如果您為資料庫設定 LTR，則不會受到影響。 如需如何變更 LTR 保留期間的詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 入口網站變更 PITR 備份保留期限

若要使用 Azure 入口網站來變更 PITR 備份保留期限，請移至您想要在入口網站中變更其保留期限的伺服器物件。 然後根據您要修改的伺服器物件，選取適當的選項。

#### <a name="single-database-and-elastic-database-pools"></a>[單一資料庫和彈性資料庫集區](#tab/single-database)

單一 Azure SQL 資料庫的 PITR 備份保留變更會在伺服器層級完成。 在伺服器層級進行的變更會套用至伺服器上的資料庫。 若要從 Azure 入口網站變更 Azure SQL Database 伺服器的 PITR 保留，請移至 [伺服器總覽] 分頁。 選取左窗格中的 [**管理備份**]，然後選取畫面頂端的 [**設定保留**]：

![變更 PITR 保留，伺服器層級](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[受控執行個體](#tab/managed-instance)

SQL Database 受控實例的 PITR 備份保留變更會在個別資料庫層級完成。 若要從 Azure 入口網站變更實例資料庫的 PITR 備份保留，請移至 [個別資料庫總覽] 分頁。 然後選取畫面頂端的 [**設定備份保留**]：

![變更 PITR 保留，受控實例](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 變更 PITR 備份保留期限

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍支援 PowerShell AzureRM 模組，但所有未來的開發都是針對 Az .Sql 模組。 如需詳細資訊，請參閱[AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組中命令的引數主要與 AzureRm 模組中的參數相同。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>使用 REST API 變更 PITR 備份保留期限

#### <a name="sample-request"></a>範例要求

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>要求本文

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

## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。
- 取得有關如何[使用 Azure 入口網站將資料庫還原到某個時間點](sql-database-recovery-using-backups.md)的詳細資訊。
- 取得有關如何[使用 PowerShell 將資料庫還原至某個時間點](scripts/sql-database-restore-database-powershell.md)的詳細資訊。
- 如需如何使用 Azure 入口網站在 Azure Blob 儲存體中設定、管理自動備份的長期保留及從中還原的詳細資訊，請參閱[使用 Azure 入口網站管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。
- 如需有關如何使用 PowerShell 在 Azure Blob 儲存體中設定、管理自動備份的長期保留及從中還原的詳細資訊，請參閱[使用 Powershell 管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。
