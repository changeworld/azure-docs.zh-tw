---
title: 針對 SQL Server 資料庫備份進行疑難排解
description: 適用於在 Azure VM 上執行並使用 Azure 備份進行備份之 SQL Server 資料庫的疑難排解資訊。
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 7ebe76fde344b1dabca9a3aee2d0cc9e1edb8df4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247822"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>使用 Azure 備份對 SQL Server 資料庫備份進行疑難排解

本文提供在 Azure 虛擬機器上執行之 SQL Server 資料庫的疑難排解資訊。

如需備份程式和限制的詳細資訊，請參閱[關於 Azure vm 中的 SQL Server 備份](backup-azure-sql-database.md#feature-consideration-and-limitations)。

## <a name="sql-server-permissions"></a>SQL Server 權限

若要設定虛擬機器上 SQL Server 資料庫的保護，您必須在該虛擬機器上安裝**AzureBackupWindowsWorkload**延伸模組。 如果您收到錯誤**UserErrorSQLNoSysadminMembership**，表示您的 SQL Server 實例沒有必要的備份許可權。 若要修正此錯誤，請依照[設定 VM 許可權](backup-azure-sql-database.md#set-vm-permissions)中的步驟進行。

## <a name="troubleshoot-discover-and-configure-issues"></a>針對探索和設定問題進行疑難排解

建立和設定復原服務保存庫之後，探索資料庫和設定備份是兩個步驟的程式。<br>

![sql](./media/backup-azure-sql-database/sql.png)

在備份設定期間，如果在**vm 的探索 db**中看不到 SQL VM 及其實例，並**設定備份**（請參閱上面的影像），請確定：

### <a name="step-1-discovery-dbs-in-vms"></a>步驟1： Vm 中的探索 Db

- 如果 VM 未列在 [探索到的 VM] 清單中，而且也未針對另一個保存庫中的 [SQL 備份] 註冊，則請遵循[探索 SQL Server 備份](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#discover-sql-server-databases)步驟。

### <a name="step-2-configure-backup"></a>步驟2：設定備份

- 如果在用來保護資料庫的相同保存庫中註冊 SQL VM 的保存庫，請遵循[設定備份](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#configure-backup)步驟。

如果需要在新的保存庫中註冊 SQL VM，則必須從舊的保存庫取消註冊。  從保存庫取消註冊 SQL VM 需要停止保護所有受保護的資料來源，然後您就可以刪除已備份的資料。 刪除備份的資料是破壞性作業。  在您檢查並採取取消註冊 SQL VM 的所有預防措施之後，請使用新的保存庫註冊此相同的 VM，然後重試備份作業。

## <a name="troubleshoot-backup-and-recovery-issues"></a>針對備份和復原問題進行疑難排解  

有時候，在備份和還原作業中可能會發生隨機失敗，或這些作業可能會停滯。 這可能是因為您的 VM 上有防毒程式。 建議的最佳作法是，建議您執行下列步驟：

1. 從防毒軟體掃描中排除下列資料夾：

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.Edp.AzureBackupWindowsWorkload`

    將 `C:\` 取代為*SystemDrive*的字母。

1. 從防毒軟體掃描中排除下列三個在 VM 中執行的處理常式：

    - IaasWLPluginSvc .exe
    - IaasWorkloadCoordinaorService .exe
    - TriggerExtensionJob .exe

1. SQL 也提供一些關於使用防毒程式的指導方針。 如需詳細資訊，請參閱[本文](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server)。

## <a name="error-messages"></a>錯誤訊息

### <a name="backup-type-unsupported"></a>不支援的備份類型

| Severity | 描述 | 可能的原因 | 建議的動作 |
|---|---|---|---|
| 警告 | 此資料庫的目前設定不支援相關聯原則中存在的特定備份類型。 | <li>只有完整資料庫備份作業可以在 master 資料庫上執行。 差異備份或交易記錄備份都不可行。 </li> <li>簡單復原模式中的任何資料庫都不允許備份交易記錄。</li> | 修改資料庫設定，使其支援原則中的所有備份類型。 或者，將目前的原則變更為僅包含支援的備份類型。 否則，在排程備份期間將會略過不支援的備份類型，否則備份作業將會因為隨選備份而失敗。

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此 SQL 資料庫不支援所要求的備份類型。 | 當資料庫復原模式不允許所要求的備份類型時便會發生。 此錯誤會於下列情況時發生： <br/><ul><li>使用簡單復原模式的資料庫不允許記錄備份。</li><li>Master 資料庫不允許差異和記錄備份。</li></ul>如需詳細資訊，請參閱[SQL Server 復原模式](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server)檔。 | 如果簡單復原模式中的資料庫記錄備份失敗，請嘗試下列其中一個選項：<ul><li>如果資料庫處於簡單復原模式，請停用記錄備份。</li><li>使用[SQL Server 檔](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server)，將資料庫復原模式變更為完整或大量記錄。 </li><li> 如果您不想變更復原模式，而且所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整和差異備份會依排程運作。 記錄備份則會略過，這符合此案例的預期。</li></ul>如果它是 master 資料庫，而且您已設定差異或記錄備份，請使用下列其中一個步驟：<ul><li>使用入口網站將 master 資料庫的備份原則排程變更為 [完整]。</li><li>如果所用來備份多個資料庫的標準原則無法變更，則請忽略此錯誤。 完整備份會依排程運作。 差異或記錄備份則不會進行，這符合此案例的預期。</li></ul> |
| 作業遭到取消，原因是同一個資料庫上已在執行衝突的作業。 | 請參閱[有關同時執行之備份和還原限制的 blog 專案](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/)。| [使用 SQL Server Management Studio （SSMS）來監視備份作業](manage-monitor-sql-database-backup.md)。 衝突的作業失敗之後，請重新開機作業。|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 資料庫不存在。 | 資料庫已遭到刪除或重新命名。 | 檢查資料庫是否已意外遭到刪除或重新命名。<br/><br/> 如果資料庫意外遭到刪除，卻要繼續備份，請將資料庫還原到原始位置。<br/><br/> 如果您已刪除資料庫，而不需要未來的備份，請在復原服務保存庫中，選取 [**停止備份**並**保留備份資料**] 或 [**刪除備份資料**]。 如需詳細資訊，請參閱[管理和監視備份的 SQL Server 資料庫](manage-monitor-sql-database-backup.md)。

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 記錄鏈結中斷。 | 資料庫或 VM 會透過另一個備份解決方案進行備份，這會截斷記錄鏈。|<ul><li>檢查是否有其他備份解決方案或指令碼正在使用中。 如果有，請停止其他備份解決方案。 </li><li>如果備份是隨選記錄備份，請觸發完整備份以啟動新的記錄鏈。 針對排定的記錄備份，不需要採取任何動作，因為 Azure 備份服務會自動觸發完整備份以修正此問題。</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份無法連線至 SQL 執行個體。 | Azure 備份無法連接到 SQL Server 實例。 | 使用 [Azure 入口網站錯誤] 功能表上的 [其他詳細資料] 來縮小根本原因。 請參閱 [SQL 備份疑難排解](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine)以修正錯誤。<br/><ul><li>如果預設的 SQL 設定不允許遠端連線，請變更設定。 如需變更設定的詳細資訊，請參閱下列文章：<ul><li>[MSSQLSERVER_-1](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error?view=sql-server-ver15)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>如果有登入問題，請使用下列連結來加以修正：<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 此資料來源的第一個完整備份遺失。 | 資料庫的完整備份遺失。 記錄和差異備份是完整備份的父系，因此請務必在觸發差異或記錄備份之前，先進行完整備份。 | 觸發隨選完整備份。   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 資料來源的交易記錄已滿，所以無法擷取備份。 | 資料庫的交易記錄空間已滿。 | 若要修正此問題，請參閱[SQL Server 檔](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)。 |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 目標位置上已經有同名的資料庫存在。 | 目標還原目的地已經有相同名稱的資料庫。  | <ul><li>變更目標資料庫名稱。</li><li>或者，使用 [還原] 頁面上的 [強制覆寫] 選項。</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 還原失敗，原因是無法讓資料庫離線。 | 當您執行還原時，目標資料庫必須離線。 Azure 備份無法使此資料離線。 | 使用 [Azure 入口網站錯誤] 功能表上的 [其他詳細資料] 來縮小根本原因。 如需詳細資訊，請參閱 [SQL Server 文件](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms)。 |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 在目標上找不到具有指紋的伺服器憑證。 | 目的地實例上的 master 資料庫沒有有效的加密指紋。 | 將來源實例上使用的有效憑證指紋匯入目標實例。 |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 用於復原的記錄備份包含大量記錄的變更。 根據 SQL 指導方針，其無法用來在任意時間點停止。 | 當資料庫處於大量記錄復原模式時，無法復原大量記錄交易和下一個記錄交易之間的資料。 | 請選擇不同的恢復時間點。 [詳細資訊](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver15)。

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 無法符合 SQL Always On 可用性群組的備份喜好設定，原因是可用性群組有某些節點並未註冊。 | 執行備份所需的節點未註冊或無法連線。 | <ul><li>請確認執行此資料庫備份所需的所有節點都已註冊且狀況良好，然後再次嘗試操作。</li><li>變更 SQL Server Always On 可用性群組的備份喜好設定。</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| SQL 伺服器 VM 已關閉，無法供 Azure 備份服務存取。 | VM 已關閉。 | 確定 SQL Server 實例正在執行。 |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| Azure 備份服務使用 Azure VM 客體代理程式來執行備份，但目標伺服器上無法使用客體代理程式。 | 來賓代理程式未啟用或狀況不良。 | 手動[安裝 VM 客體代理程式](../virtual-machines/extensions/agent-windows.md)。 |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
| 自動保護用途可能已移除或不再有效。 | 當您在 SQL Server 實例上啟用自動保護時，請設定該實例中所有資料庫的**備份**作業執行。 如果您停用自動保護，而工作正在執行，則**進行中**工作會取消，並出現此錯誤碼。 | 再次啟用自動保護，以協助保護所有剩餘的資料庫。 |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
作業遭到封鎖，因為您已達到24小時內允許的作業數目上限。 | 當您已達到一段24小時內所允許的作業上限時，就會發生此錯誤。 <br> 例如：如果您已達到每日可觸發的「設定備份」作業數目上限，而且您嘗試在新的專案上設定備份，則會看到此錯誤。 | 通常，在24小時後重試作業會解決此問題。 不過，如果問題持續發生，您可以聯繫 Microsoft 支援服務尋求協助。

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
因為保存庫已達24小時內允許的這類作業上限，所以已封鎖操作。 | 當您已達到一段24小時內所允許的作業上限時，就會發生此錯誤。 當有大規模的作業（例如修改原則或自動保護）時，通常就會發生此錯誤。 不同于 CloudDosAbsoluteLimitReached 的情況，您無法解決此狀態的問題，事實上，Azure 備份服務會在內部針對所有問題的專案重試作業。<br> 例如：如果您有大量使用原則保護的資料來源，而您嘗試修改該原則，則會觸發設定每個受保護專案的保護工作，有時可能會達到這類作業每天允許的最大限制。| Azure 備份服務會在24小時後自動重試此操作。

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| 錯誤訊息 | 可能的原因 | 建議的動作 |
|---|---|---|
因為網際網路連線問題，VM 無法與 Azure 備份服務聯繫。 | VM 需要 Azure 備份服務、Azure 儲存體或 Azure Active Directory 服務的輸出連線能力。| -如果您使用 NSG 來限制連線，則您應該使用 AzureBackup 服務標籤，以允許 Azure 備份 Azure 備份服務、Azure 儲存體或 Azure Active Directory 服務的輸出存取。 請遵循下列[步驟](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#allow-access-using-nsg-tags)來授與存取權。<br>-確認 DNS 正在解析 Azure 端點。<br>-檢查 VM 是否位於封鎖網際網路存取的負載平衡器後方。 藉由將公用 IP 指派給 Vm，探索將會正常執行。<br>-確認沒有防火牆/防毒程式正在封鎖對上述三個目標服務的呼叫。

## <a name="re-registration-failures"></a>重新註冊失敗

在觸發重新註冊作業之前，請先檢查下列一個或多個徵兆：

* 所有作業（例如備份、還原和設定備份）在 VM 上失敗，發生下列其中一個錯誤代碼： **WorkloadExtensionNotReachable**、 **UserErrorWorkloadExtensionNotInstalled**、 **WorkloadExtensionNotPresent**、 **WorkloadExtensionDidntDequeueMsg**。
* 如果備份專案的 [**備份狀態**] 區域顯示為 [**無法**連線]，請排除可能會導致相同狀態的所有其他原因：

  * 缺少在 VM 上執行備份相關作業的許可權。
  * 關閉 VM，因此無法進行備份。
  * 網路問題。

   ![重新註冊 VM](./media/backup-azure-sql-database/re-register-vm.png)



* 在 Always On 可用性群組的情況下，當您變更備份喜好設定或容錯移轉之後，備份就會開始失敗。

這些徵兆可能因下列一或多個原因而發生：

* 已從入口網站刪除或卸載擴充功能。
* 在 [**卸載或變更程式**] 下，已從 VM 上的 [**控制台**] 卸載擴充功能。
* VM 已透過就地磁片還原還原回來。
* VM 已關閉一段較長的時間，因此其上的延伸模組設定已過期。
* 已刪除 VM，並在與已刪除的 VM 相同的資源群組中建立了另一個 VM。
* 其中一個可用性群組節點未收到完整的備份設定。 當可用性群組註冊至保存庫，或加入新的節點時，就會發生這種情況。

在先前的案例中，我們建議您在 VM 上觸發重新註冊操作。 如需如何在 PowerShell 中執行這項工作的指示，請參閱[這裡](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup)。

## <a name="size-limit-for-files"></a>檔案的大小限制

檔案的總字串大小不只取決於檔案數目，同時也是在其名稱和路徑上。 針對每個資料庫檔案，取得邏輯檔案名和實體路徑。 您可以使用此 SQL 查詢：

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

現在請以下列格式排列：

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

以下是範例：

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

如果內容的字串大小超過20000個位元組，則會以不同的方式儲存資料庫檔案。 在復原期間，您將無法設定還原的目標檔案路徑。 檔案將還原為 SQL Server 所提供的預設 SQL 路徑。

### <a name="override-the-default-target-restore-file-path"></a>覆寫預設的目標還原檔案路徑

在還原作業期間，您可以將包含資料庫檔案對應的 JSON 檔案放到目標還原路徑，以覆寫目標還原檔案路徑。 建立 `database_name.json` 檔案，並將它放在 [location *C:\Program Files\Azure 工作負載] Backup\bin\plugins\SQL*中。

檔案的內容應該採用下列格式：

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

在上述內容中，您可以使用下列 SQL 查詢來取得資料庫檔案的邏輯名稱：

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```

在您觸發還原作業之前，應該先放置此檔案。

## <a name="next-steps"></a>後續步驟

如需 SQL Server Vm Azure 備份的詳細資訊（公開預覽），請參閱[適用于 SQL vm 的 Azure 備份](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup)。
