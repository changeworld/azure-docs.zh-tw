---
title: 排除 SQL 伺服器資料庫備份故障
description: 適用於在 Azure VM 上執行並使用 Azure 備份進行備份之 SQL Server 資料庫的疑難排解資訊。
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 8d49adb0ab741903ccb2989cfeb4ceaef2e8a38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408611"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>使用 Azure 備份對 SQL Server 資料庫備份進行故障排除

本文為在 Azure 虛擬機器上運行的 SQL Server 資料庫提供故障排除資訊。

有關備份過程和限制的詳細資訊，請參閱[有關 Azure VM 中的 SQL Server 備份](sql-support-matrix.md#feature-consideration-and-limitations)。

## <a name="sql-server-permissions"></a>SQL Server 權限

要配置虛擬機器上的 SQL Server 資料庫保護，必須在該虛擬機器上安裝**AzureBackupWindows 工作負荷**擴展。 如果收到錯誤**使用者ErrorSQLNoSysadmin成員資格**，這意味著您的 SQL Server 實例沒有所需的備份許可權。 要修復此錯誤，請按照["設置 VM 許可權"](backup-azure-sql-database.md#set-vm-permissions)中的步驟操作。

## <a name="troubleshoot-discover-and-configure-issues"></a>疑難排解發現和配置問題

創建和配置恢復服務保存庫後，發現資料庫和配置備份是一個兩步過程。<br>

![sql](./media/backup-azure-sql-database/sql.png)

在備份配置期間，如果 SQL VM 及其實例在 VM 中的**發現 QB**中不可見，並且**配置備份**（請參閱上圖）可確保：

### <a name="step-1-discovery-dbs-in-vms"></a>第 1 步：在 VM 中發現 DB

- 如果 VM 未列在發現的 VM 清單中，並且也沒有在另一個保存庫中註冊 SQL 備份，請按照[發現 SQL Server 備份](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)步驟操作。

### <a name="step-2-configure-backup"></a>第 2 步：配置備份

- 如果 SQL VM 註冊在同一保存庫中的保存庫用於保護資料庫，則按照[配置備份](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)步驟操作。

如果 SQL VM 需要在新保存庫中註冊，則必須從舊保存庫取消註冊。  從保存庫中取消 SQL VM 的註冊要求停止保護所有受保護的資料來源，然後可以刪除備份的資料。 刪除備份的資料是一種破壞性操作。  查看並採取了取消註冊 SQL VM 的所有預防措施後，然後向新保存庫註冊此相同的 VM 並重試備份操作。

## <a name="troubleshoot-backup-and-recovery-issues"></a>排除備份和恢復問題  

有時，備份和還原操作中可能發生隨機故障，或者這些操作可能會卡住。 這可能是因為 VM 上的防毒程式。 作為最佳實踐，我們建議執行以下步驟：

1. 從防病毒掃描中排除以下資料夾：

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    替換為`C:\`*系統磁碟機*的字母。

1. 從防病毒掃描中排除在 VM 中運行的以下三個進程：

    - 亞斯WLPluginSvc.exe
    - 亞斯工作量庫迪奧服務.exe
    - 觸發器擴展作業.exe

1. SQL 還提供了一些有關使用防毒程式的指南。 有關詳細資訊，請參閱[此文章](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server)。

## <a name="error-messages"></a>錯誤訊息

### <a name="backup-type-unsupported"></a>不支援備份類型

| Severity | 描述 | 可能的原因 | 建議的動作 |
|---|---|---|---|
| 警告 | 此資料庫的當前設置不支援關聯策略中存在的某些備份類型。 | <li>只能在主資料庫上執行完整的資料庫備份操作。 差分備份和事務記錄備份都不可能實現。 </li> <li>簡單恢復模型中的任何資料庫都不允許備份事務日誌。</li> | 修改資料庫設定，使其支援原則中的所有備份類型。 或者，更改當前策略以僅包括受支援的備份類型。 否則，在計畫備份期間將跳過不支援的備份類型，否則備份作業將失敗，用於按需備份。

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此 SQL 資料庫不支援所要求的備份類型。 | 當資料庫復原模式不允許所要求的備份類型時便會發生。 此錯誤會於下列情況時發生： <br/><ul><li>使用簡單恢復模型的資料庫不允許記錄備份。</li><li>主資料庫不允許差異備份和記錄備份。</li></ul>有關詳細資訊，請參閱 SQL [Server 恢復模型](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)文檔。 | 如果簡單恢復模型中資料庫的記錄備份失敗，請嘗試以下選項之一：<ul><li>如果資料庫處於簡單復原模式，請停用記錄備份。</li><li>使用[SQL Server 文檔](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)將資料庫恢復模型更改為完整或批量記錄。 </li><li> 如果您不想變更復原模式，而且所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整和差異備份會依排程運作。 記錄備份則會略過，這符合此案例的預期。</li></ul>如果是主資料庫，並且已配置差分或記錄備份，請使用以下步驟之一：<ul><li>使用門戶將主資料庫的備份策略計畫更改為完整。</li><li>如果所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整備份會依排程運作。 差異或記錄備份則不會進行，這符合此案例的預期。</li></ul> |
| 作業遭到取消，原因是同一個資料庫上已在執行衝突的作業。 | 請參閱[博客條目，瞭解同時運行的備份和還原限制](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/)。| [使用 SQL 伺服器管理工作室 （SSMS） 監視備份作業](manage-monitor-sql-database-backup.md)。 衝突操作失敗後，重新開機該操作。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 資料庫不存在。 | 資料庫已遭到刪除或重新命名。 | 檢查資料庫是否已意外遭到刪除或重新命名。<br/><br/> 如果資料庫意外遭到刪除，卻要繼續備份，請將資料庫還原到原始位置。<br/><br/> 如果您刪除了資料庫，並且不需要將來的備份，請在恢復服務保存庫中選擇"**使用保留備份資料****停止備份"** 或 **"刪除備份資料**"。 有關詳細資訊，請參閱[管理和監視備份的 SQL Server 資料庫](manage-monitor-sql-database-backup.md)。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 記錄鏈結中斷。 | 資料庫或 VM 通過另一個備份解決方案進行備份，該備份解決方案會截截日誌鏈。|<ul><li>檢查是否有其他備份解決方案或指令碼正在使用中。 如果有，請停止其他備份解決方案。 </li><li>如果備份是按需記錄備份，請觸發完整備份以啟動新的日誌鏈。 對於計畫的記錄備份，無需執行任何操作，因為 Azure 備份服務將自動觸發完整備份以解決此問題。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份無法連線至 SQL 執行個體。 | Azure 備份無法連接到 SQL Server 實例。 | 使用 Azure 門戶錯誤功能表上的其他詳細資訊縮小根本原因。 請參閱 [SQL 備份疑難排解](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)以修正錯誤。<br/><ul><li>如果預設 SQL 設置不允許遠端連線，請更改設置。 有關更改設置的資訊，請參閱以下文章：<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果存在登錄問題，請使用以下連結來修復它們：<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此資料來源的第一個完整備份遺失。 | 資料庫的完整備份遺失。 記錄備份和差異備份是完整備份的父備份，因此請確保在觸發差異備份或記錄備份之前執行完整備份。 | 觸發按需完全備份。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 資料來源的交易記錄已滿，所以無法擷取備份。 | 資料庫的交易記錄空間已滿。 | 要解決此問題，請參閱[SQL Server 文檔](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 目標位置上已經有同名的資料庫存在。 | 目標還原目標已具有同名資料庫。  | <ul><li>更改目標資料庫名稱。</li><li>或者，在還原頁上使用強制覆蓋選項。</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 還原失敗，原因是無法讓資料庫離線。 | 執行還原時，需要使目標資料庫離線。 Azure 備份無法使此資料離線。 | 使用 Azure 門戶錯誤功能表上的其他詳細資訊縮小根本原因。 如需詳細資訊，請參閱 [SQL Server 文件](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)。 |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 在目標上找不到具有指紋的伺服器憑證。 | 目標實例上的主資料庫沒有有效的加密指紋。 | 將源實例上使用的有效證書指紋導入目標實例。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 用於復原的記錄備份包含大量記錄的變更。 根據 SQL 指導方針，其無法用來在任意時間點停止。 | 當資料庫處於批量記錄復原模式時，無法恢復批量記錄的事務和下一個日誌事務之間的資料。 | 選擇不同的恢復時間點。 [深入了解](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15)。

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 無法符合 SQL Always On 可用性群組的備份喜好設定，原因是可用性群組有某些節點並未註冊。 | 執行備份所需的節點未註冊或無法連線。 | <ul><li>確保執行此資料庫備份所需的所有節點都已註冊且正常，然後重試該操作。</li><li>更改 SQL Server 始終打開可用性組的備份首選項。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 伺服器 VM 已關閉，無法供 Azure 備份服務存取。 | 虛擬機器已關閉。 | 確保 SQL Server 實例正在運行。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份服務使用 Azure VM 客體代理程式來執行備份，但目標伺服器上無法使用客體代理程式。 | 來賓代理未啟用或不正常。 | 手動[安裝 VM 客體代理程式](../virtual-machines/extensions/agent-windows.md)。 |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 自動保護用途可能已移除或不再有效。 | 在 SQL Server 實例上啟用自動保護時，**配置備份**作業將運行該實例中的所有資料庫。 如果您停用自動保護，而工作正在執行，則**進行中**工作會取消，並出現此錯誤碼。 | 再次啟用自動保護以説明保護所有剩餘的資料庫。 |

### <a name="clouddosabsolutelimitreached"></a>雲多斯絕對極限實現

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
操作被阻止，因為您已達到 24 小時內允許的運算元限制。 | 當您在 24 小時內達到操作的最大允許限制時，此錯誤將出現。 <br> 例如：如果已達到每天可觸發的配置備份作業數的限制，並且嘗試在新專案上配置備份，您將看到此錯誤。 | 通常，在 24 小時後重試操作將解決此問題。 但是，如果問題仍然存在，您可以聯繫 Microsoft 支援人員尋求説明。

### <a name="clouddosabsolutelimitreachedwithretry"></a>雲多斯絕對限制與重試

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
由於保存庫已達到 24 小時內允許的此類操作的最大限制，因此操作被阻止。 | 當您在 24 小時內達到操作的最大允許限制時，此錯誤將出現。 當存在大規模操作（如修改策略或自動保護）時，通常會出現此錯誤。 與 CloudDos絕對Limit達到的情況不同，您解決此狀態的事務不多，事實上，Azure 備份服務將在內部重試所有相關項的操作。<br> 例如：如果大量資料來源受策略保護，並嘗試修改該策略，它將為每個受保護項觸發配置保護作業，有時可能會達到每天此類操作允許的最大限制。| Azure 備份服務將在 24 小時後自動重試此操作。

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
由於 Internet 連接問題，VM 無法連絡 Azure 備份服務。 | VM 需要與 Azure 備份服務、Azure 存儲或 Azure 活動目錄服務進行出站連接。| - 如果使用 NSG 限制連接，則應使用 Azure 備份服務標記允許出站訪問 Azure 備份服務、Azure 備份服務、Azure 存儲或 Azure 活動目錄服務。 按照[以下步驟](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags)授予存取權限。<br>- 確保 DNS 正在解析 Azure 終結點。<br>- 檢查 VM 是否位於阻止 Internet 訪問的負載等化器後面。 通過將公共 IP 分配給 VM，發現將起作用。<br>- 驗證沒有防火牆/防病毒/代理阻止對上述三個目標服務的調用。

## <a name="re-registration-failures"></a>重新註冊失敗

在觸發重新註冊操作之前，請檢查以下一個或多個症狀：

* 所有操作（如備份、還原和配置備份）在 VM 上失敗，並且具有以下錯誤代碼之一：**工作負載擴展不可訪問**、**使用者錯誤工作負載擴展未安裝**、**工作負載擴展不存在**、**工作負載擴展未執行佇列 Msg**。
* 如果備份項的 **"備份狀態**"區域顯示 **"無法訪問"，** 則排除可能導致相同狀態的所有其他原因：

  * 缺少在 VM 上執行與備份相關的操作的許可權。
  * 關閉 VM，因此無法進行備份。
  * 網路問題。

   ![重新註冊 VM](./media/backup-azure-sql-database/re-register-vm.png)



* 在"始終打開"可用性組的情況下，備份在更改備份首選項或容錯移轉後開始失敗。

這些症狀可能出現以下一個或多個原因：

* 擴展已從門戶中刪除或卸載。
* **在卸載或更改程式**下，從 VM 上的**控制台**卸載了擴展。
* 通過就地磁片還原及時還原 VM。
* VM 已關閉很長一段時間，因此其上的擴展配置已過期。
* 已刪除 VM，並且使用與已刪除的 VM 相同的名稱並在同一資源組中創建了另一個 VM。
* 其中一個可用性組節點未收到完整的備份配置。 當可用性組註冊到保存庫或添加新節點時，可能會發生這種情況。

在前面的方案中，我們建議您在 VM 上觸發重新註冊操作。 有關如何在 PowerShell 中執行此任務的說明，請參閱[此處](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup)。

## <a name="size-limit-for-files"></a>檔的大小限制

檔的總字串大小不僅取決於檔的數量，還取決於檔的名稱和路徑。 對於每個資料庫檔案，獲取邏輯檔案名和實體路徑。 您可以使用此 SQL 查詢：

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

現在以以下格式排列它們：

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

以下是範例：

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

如果內容的字串大小超過 20，000 位元組，則資料庫檔案的存儲方式不同。 在恢復期間，您將無法設置用於還原的目的檔案路徑。 這些檔將還原到 SQL Server 提供的預設 SQL 路徑。

### <a name="override-the-default-target-restore-file-path"></a>覆蓋預設目標還原檔路徑

通過在還原操作期間放置包含資料庫檔案映射到目標還原路徑的 JSON 檔，可以覆蓋目標還原檔路徑。 創建檔`database_name.json`並將其放置在位置*C：\程式檔\Azure 工作負載備份\bin\外掛程式\SQL*。

檔的內容應採用以下格式：

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

以下是範例：

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

在前面的內容是，可以使用以下 SQL 查詢獲取資料庫檔案的邏輯名稱：

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

在觸發還原操作之前，應放置此檔。

## <a name="next-steps"></a>後續步驟

有關 SQL 伺服器 VM 的 Azure 備份的詳細資訊（公共預覽），請參閱[SQL VM 的 Azure 備份](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。
