---
title: 自動異地冗余備份
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061769"
---
# <a name="automated-backups"></a>自動備份

Azure SQL 資料庫會自動創建在配置的保留期內保留的資料庫備份。 它使用 Azure[讀取存取異地冗余存儲 （RA-GRS）](../storage/common/storage-redundancy.md)來確保即使資料中心不可用，備份也會保留。

資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 如果安全規則要求備份在較長時間內（最多 10 年）可用，則可以在單例資料庫和彈性資料庫池上配置[長期保留](sql-database-long-term-retention.md)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>什麼是 SQL Database 備份？

SQL 資料庫使用 SQL Server 技術每週創建[完整備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)，每 12 小時創建[一次差異備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)，每 5 到 10 分鐘[創建一次事務記錄備份](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)。 備份存儲在[RA-GRS 存儲 Blob 中](../storage/common/storage-redundancy.md)，這些 Blob 被覆制到[配對的資料中心](../best-practices-availability-paired-regions.md)，以防範資料中心中斷。 還原資料庫時，該服務將確定需要還原的完整、差異和事務記錄備份。

您可以使用這些備份︰

- 通過使用 Azure 門戶、Azure PowerShell、Azure CLI 或 REST API，**將現有資料庫還原到過去保留期內的某一時間點**。 對於單個資料庫和彈性資料庫池，此操作將在與原始資料庫相同的伺服器上創建新資料庫。 在託管實例中，此操作可以創建資料庫的副本，也可以在同一訂閱下創建相同或不同的託管實例的副本。
- **將已刪除的資料庫還原到刪除時間**或保留期內的任何時間。 刪除的資料庫只能在創建原始資料庫的同一邏輯伺服器或託管實例上還原。
- **將資料庫還原到另一個地理區域**。 當無法訪問伺服器和資料庫時，地理還原允許您從地理災難中恢復。 它在世界上任何現有伺服器上創建新資料庫。
- 如果資料庫配置了長期保留原則 （LTR），則從單個資料庫或彈性資料庫池上**的特定長期備份還原資料庫**。 LTR 允許您使用[Azure 門戶](sql-database-long-term-backup-retention-configure.md#using-azure-portal)或[Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell)來還原資料庫的舊版本，以滿足合規性請求或運行應用程式的舊版本。 有關詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

要執行還原，請參閱[從備份還原資料庫](sql-database-recovery-using-backups.md)。

> [!NOTE]
> 在 Azure 存儲中，術語*複製*是指將檔從一個位置複製到另一個位置。 SQL Server*資料庫複製*是指使多個次要資料庫與主資料庫同步。

您可以使用以下示例嘗試其中一些操作：

| | Azure 入口網站 | Azure PowerShell |
|---|---|---|
| 更改備份保留 | [單一資料庫](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [託管實例](sql-database-automated-backups.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [單一資料庫](sql-database-automated-backups.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[託管實例](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 更改長期備份保留 | [單一資料庫](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>託管實例 - 不適用  | [單一資料庫](sql-database-long-term-backup-retention-configure.md)<br/>託管實例 - 不適用  |
| 從某個時間點還原資料庫 | [單一資料庫](sql-database-recovery-using-backups.md#point-in-time-restore) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [託管實例](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 還原已刪除的資料庫 | [單一資料庫](sql-database-recovery-using-backups.md) | [單一資料庫](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [託管實例](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| 從 Azure Blob 存儲還原資料庫 | 單個資料庫 - 不適用 <br/>託管實例 - 不適用  | 單個資料庫 - 不適用 <br/>[託管實例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>備份頻率

### <a name="point-in-time-restore"></a>時間點還原

SQL 資料庫通過自動創建完整備份、差分備份和事務記錄備份，支援時間點還原 （PITR） 的自助服務。 每週創建一次完整的資料庫備份，差異資料庫備份通常每 12 小時創建一次。 事務記錄備份通常每 5 到 10 分鐘創建一次。 事務記錄備份的頻率基於計算大小和資料庫活動量。 

建立資料庫之後，會立即排程第一次完整備份。 此備份通常在 30 分鐘內完成，但當資料庫較大時可能需要更長時間。 比方說，在還原的資料庫或資料庫複本上，初始備份可能需要較長的時間。 第一次完整備份之後，將會自動排程進一步的備份，並在背景中以無訊息方式管理。 資料庫備份的確切時間，依 SQL Database 服務整體系統工作負載維持平衡而決定。 您無法變更或停用備份作業。

PITR 備份受異地冗余存儲的保護。 如需詳細資訊，請參閱 [Azure 儲存體備援](../storage/common/storage-redundancy.md) \(部分機器翻譯\)。

有關 PITR 的詳細資訊，請參閱[時間點還原](sql-database-recovery-using-backups.md#point-in-time-restore)。

### <a name="long-term-retention"></a>長期保留

對於單個資料庫和池資料庫，您可以在 Azure Blob 存儲中配置完整備份的長期保留期 （LTR）長達 10 年。 如果啟用 LTR 策略，則每週完整備份將自動複製到其他 RA-GRS 存儲容器。 為了滿足各種合規性要求，您可以選擇不同的每週、每月和/或年度備份的保留期。 存儲消耗取決於所選的備份頻率以及保留期或期間。 您可以使用[LTR 定價計算機](https://azure.microsoft.com/pricing/calculator/?service=sql-database)來估算 LTR 存儲的成本。

與 PITR 備份一樣，LTR 備份也受異地冗余存儲的保護。 如需詳細資訊，請參閱 [Azure 儲存體備援](../storage/common/storage-redundancy.md) \(部分機器翻譯\)。

有關 LTR 的詳細資訊，請參閱[長期備份保留](sql-database-long-term-retention.md)。

## <a name="backup-storage-consumption"></a>備份存儲消耗

對於單個資料庫，此公式用於計算備份存儲的總使用方式：

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

對於彈性資料庫池，總備份存儲大小在池級別聚合，計算方式如下：

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

保留期之前發生的備份會根據其時間戳記自動清除。 由於差異備份和記錄備份需要更早的完整備份才能有用，因此它們按每週塊一起清除。

Azure SQL 資料庫將保留期備份存儲總額計算為累積值。 每小時都會向 Azure 計費管道報告此值，該管道負責聚合此每小時使用方式，以計算每個月底的消耗量。 刪除資料庫後，消耗量會隨著備份年齡的降低而減少。 備份超過保留期後，計費將停止。

   > [!IMPORTANT]
   > 資料庫的備份將保留指定保留期，即使資料庫已被刪除也是如此。 雖然刪除和重新創建資料庫通常可以節省存儲和計算成本，但可能會增加備份存儲成本，因為 Microsoft 為每個丟棄的資料庫保留指定的保留期（至少 7 天）的備份， 每個資料庫時間，它下降。

### <a name="monitor-consumption"></a>監控消耗

每種類型的備份（完整備份、差異備份和日誌）都報告在資料庫監視邊欄選項卡上，作為單獨的指標。 下圖演示如何監視單個資料庫的備份存儲消耗。 此功能當前對於託管實例不可用。

![監視 Azure 門戶中的資料庫備份消耗](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>微調備份存儲消耗

過多的備份存儲消耗將取決於工作負載和各個資料庫的大小。 請考慮以下一些調優技術，以減少備份存儲消耗：

* 根據您的需要，將[備份保留期](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)縮短到盡可能低。
* 避免執行大型寫入操作（如索引重建）的頻率高於您需要的頻率。
* 對於大型資料載入操作，請考慮使用[群集列存儲索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，減少非群集索引的數量，並考慮行數在 100 萬左右的批量載入操作。
* 在泛型服務層中，預配的資料存儲比超額備份存儲的價格便宜。 如果備份存儲成本持續過高，可以考慮增加資料存儲以節省備份存儲。
* 使用 TempDB 而不是 ETL 邏輯中的永久表來存儲臨時結果。 （僅適用于託管實例。
* 請考慮關閉不包含敏感性資料的資料庫（例如開發或測試資料庫）的 TDE 加密。 非加密資料庫的備份通常以更高的壓縮率進行壓縮。

> [!IMPORTANT]
> 對於分析資料集市和資料倉儲工作負載，我們強烈建議您使用[群集列存儲索引](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)，減少非群集索引的數量，並考慮行數約為 100 萬的批量載入操作，以減少多餘的備份存儲消耗。

## <a name="storage-costs"></a>儲存成本

存儲價格因您使用的 DTU 型號還是 vCore 模型而異。

### <a name="dtu-model"></a>DTU 模型

如果使用 DTU 模型，則資料庫和彈性資料庫池的備份存儲不收取額外費用。

### <a name="vcore-model"></a>虛擬核心模型

對於單個資料庫，提供至少相當於資料庫大小的 100% 的備份存儲量，無需支付額外費用。 對於彈性資料庫池和託管實例，最小備份存儲量分別等於池或實例大小的已分配資料存儲的 100%，無需支付額外費用。 備份儲存體的額外使用量會按每月每 GB 來收費。 這種額外的消耗將取決於各個資料庫的工作負載和大小。

Azure SQL 資料庫將計算保留中備份存儲的總資源作為累積值。 每小時都會向 Azure 計費管道報告此值，該管道負責聚合此每小時使用方式，以便在每個月底獲取使用。 資料庫刪除後，Microsoft 會隨著備份年齡的降低而減少消耗。 備份超過保留期後，計費將停止。 由於所有記錄備份和差異備份都保留在完全保留期內，因此經過大量修改的資料庫將收取更高的備份費用。

假設資料庫累積了 744 GB 的備份存儲，並且此金額在整個月份保持不變。 要將此累積存儲消耗轉換為每小時使用量，請將此消耗除以 744.0（每月 31 天 = 每天 24 小時）。 因此，SQL 資料庫將報告資料庫每小時消耗 1 GB 的 PITR 備份。 Azure 計費將聚合此消耗，並顯示整個月的使用量為 744 GB。 成本將基於您所在地區的 $/GB/月費率。

現在，一個更複雜的例子。 假設資料庫的保留時間在月中增加到 14 天。 假設此增加（假設）會導致備份存儲總數翻倍，達到 1，488 GB。 SQL 資料庫將報告 1 到 372 小時 1 GB 的使用方式。 它將報告使用時間為 2 GB，小時為 373 到 744。 此使用量將聚合到 1，116 GB/月的最終帳單。

### <a name="monitor-costs"></a>監控成本

要瞭解備份存儲成本，請轉到 Azure 門戶中的**成本管理 + 計費**，選擇**成本管理**，然後選擇**成本分析**。 選擇所需的訂閱作為**範圍**，然後篩選您感興趣的時間段和服務。

為**服務名稱**添加篩選器，然後在下拉清單中選擇**sql 資料庫**。 使用**儀錶子類別**篩選器為服務選擇計費計數器。 對於單個資料庫或彈性資料庫池，請選擇**單個/彈性池飛底器備份存儲**。 對於託管實例，選擇**mi pitr 備份存儲**。 **存儲**和**計算**子類別可能也讓您感興趣，但它們與備份存儲成本無關。

![備份存儲成本分析](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>備份保留期

所有 Azure SQL 資料庫（單個、池和託管實例資料庫）的預設備份保留期為 7 天。 您可以將[備份保留期更改為](#change-the-pitr-backup-retention-period)長達 35 天。

如果您刪除資料庫，則 SQL Database 會以保存線上資料庫備份的相同方式保存備份。 例如，如果刪除保留期為 7 天的"基本資料庫"，則備份的保留時間為 4 天，則備份將保存三天以上。

如果您需要保留備份的時間超過保留週期上限，則可以修改備份的屬性，以將一或多個長期保留週期新增至資料庫。 有關詳細資訊，請參閱[長期保留](sql-database-long-term-retention.md)。

> [!IMPORTANT]
> 如果刪除承載 SQL 資料庫的 Azure SQL 伺服器，則屬於該伺服器的所有彈性資料庫池和資料庫也會被刪除。 他們無法恢復。 無法還原已刪除的伺服器。 但是，如果配置了長期保留，則不會刪除具有 LTR 的資料庫的備份，並且可以還原這些資料庫。

## <a name="encrypted-backups"></a>加密的備份

如果您的資料庫使用 TDE 加密，則備份會在靜態時自動加密，包括 LTR 備份。 Azure SQL 資料庫啟用 TDE 時，也會加密備份。 所有新的 Azure SQL 資料庫預設都會設定為啟用 TDE。 如需 TDE 的詳細資訊，請參閱 [Azure SQL Database 的透明資料加密](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)。

## <a name="backup-integrity"></a>備份完整性

Azure SQL 資料庫工程團隊持續測試放置在邏輯伺服器和彈性資料庫池中的資料庫的自動資料庫備份的還原。 （此測試在託管實例中不可用。在時間點還原時，資料庫還會收到 DBCC CHECKDB 完整性檢查。

託管實例在遷移完成後，使用`CHECKSUM`使用本機`RESTORE`命令還原的資料庫或 Azure 資料移轉服務進行自動初始備份。

在完整性檢查期間找到的任何問題都會對工程小組發出警示。 有關詳細資訊，請參閱[Azure SQL 資料庫中的資料完整性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)。

## <a name="compliance"></a>法規遵循

當您將資料庫從基於 DTU 的服務層遷移到基於 vCore 的服務層時，將保留 PITR 保留，以確保應用程式的資料修復原則不會受到損害。 如果預設保留不符合合規性要求，則可以使用 PowerShell 或 REST API 更改 PITR 保留期。 有關詳細資訊，請參閱更改[PITR 備份保留期](#change-the-pitr-backup-retention-period)。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>更改 PITR 備份保留期

您可以使用 Azure 門戶、PowerShell 或 REST API 更改預設 PITR 備份保留期。 以下示例說明了如何將 PITR 保留期更改為 28 天。

> [!WARNING]
> 如果減少當前保留期，則所有早于新保留期的現有備份將不再可用。 如果增加當前保留期，SQL 資料庫將保留現有備份，直到達到較長的保留期結束。

> [!NOTE]
> 這些 API 將僅影響 PITR 保留期。 如果為資料庫配置 LTR，則不會受到影響。 有關如何更改 LTR 保留期的資訊，請參閱[長期保留](sql-database-long-term-retention.md)期。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>使用 Azure 門戶更改 PITR 備份保留期

要使用 Azure 門戶更改 PITR 備份保留期，請使用要在門戶中更改其保留期的伺服器物件。 然後根據要修改的伺服器物件選擇適當的選項。

#### <a name="single-database-and-elastic-database-pools"></a>[單個資料庫和彈性資料庫池](#tab/single-database)

對單個 Azure SQL 資料庫的 PITR 備份保留的更改是在伺服器級別完成的。 在伺服器級別所做的更改將應用於伺服器上的資料庫。 要從 Azure 門戶更改 Azure SQL 資料庫伺服器的 PITR 保留，請訪問伺服器概述邊欄選項卡。 選擇"在左側窗格中**管理備份**"，然後在螢幕頂部選擇 **"配置保留**"：

![更改 PITR 保留、伺服器級別](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[託管實例](#tab/managed-instance)

SQL 資料庫託管實例的 PITR 備份保留的更改是在單個資料庫級別完成的。 要從 Azure 門戶更改實例資料庫的 PITR 備份保留，請訪問各個資料庫概述邊欄選項卡。 然後選擇**在螢幕頂部配置備份保留**：

![更改 PITR 保留、託管實例](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>使用 PowerShell 更改 PITR 備份保留期

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell AzureRM 模組，但所有後續開發都針對 Az.Sql 模組。 有關詳細資訊，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組中命令的參數與 AzureRm 模組中的命令基本相同。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>使用 REST API 更改 PITR 備份保留期

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

## <a name="next-steps"></a>後續步驟

- 資料庫備份可保護資料免於意外損毀或刪除，是商務持續性和災害復原策略中不可或缺的一部分。 若要深入了解其他 Azure SQL Database 業務持續性解決方案，請參閱[業務持續性概觀](sql-database-business-continuity.md)。
- 獲取有關如何使用[Azure 門戶將資料庫還原到時間點](sql-database-recovery-using-backups.md)的詳細資訊。
- 獲取有關如何使用[PowerShell 將資料庫還原到時間點](scripts/sql-database-restore-database-powershell.md)的詳細資訊。
- 有關如何使用 Azure 門戶在 Azure Blob 存儲中長期保留自動備份來配置、管理和還原的資訊，請參閱[使用 Azure 門戶管理長期備份保留。](sql-database-long-term-backup-retention-configure.md)
- 有關如何使用 PowerShell 在 Azure Blob 存儲中長期保留自動備份，有關如何配置、管理和還原的資訊，請參閱[使用 PowerShell 管理長期備份保留](sql-database-long-term-backup-retention-configure.md)。
