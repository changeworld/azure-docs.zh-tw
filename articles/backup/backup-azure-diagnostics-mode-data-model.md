---
title: Azure 監視器記錄資料模型
description: 在本文中，瞭解 Azure 備份資料的 Azure 監視器日誌分析資料模型詳細資訊。
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586371"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>適用於 Azure 備份資料的 Log Analytics 資料模型

使用日誌分析資料模型從日誌分析創建自訂警報。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> 此資料模型參考了將診斷事件發送到日誌分析 （LA） 的 Azure 診斷模式。 要瞭解新資源特定模式的資料模型，請參閱以下文章[：Azure 備份診斷事件的資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>使用 Azure 備份資料模型

若要根據您的需求建立視覺效果、自訂查詢及儀表板，您可以使用下列提供作為資料模型一部分的欄位。

### <a name="alert"></a>警示

下表提供警示相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| AlertUniqueId_s |Text |所產生警示的唯一識別碼 |
| AlertType_s |Text |警示的類型，例如備份 |
| AlertStatus_s |Text |警示狀態 (例如，作用中) |
| AlertOccurrenceDateTime_s |日期/時間 |建立警示的日期和時間 |
| AlertSeverity_s |Text |警示嚴重性 (例如，重大) |
|AlertTimeToResolveInMinutes_s    | Number        |解決警報所佔用的時間。 活動警報為空。         |
|AlertConsolidationStatus_s   |Text         |確定警報是否為合併警報         |
|CountOfAlertsConsolidated_s     |Number         |如果是合併警報的合併警報數          |
|AlertRaisedOn_s     |Text         |警報在         |
|AlertCode_s     |Text         |唯一標識警報類型的代碼         |
|RecommendedAction_s   |Text         |建議執行解決警報的操作         |
| EventName_s |Text |活動的名稱。 一律為 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |與警示相關聯的備份項目所具備的唯一識別碼 |
| SchemaVersion_s |Text |架構的當前版本，例如**V2** |
| State_s |Text |警示物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |用於執行本警示所屬之備份的提供者類型 (例如，IaaSVM、FileFolder) |
| OperationName |Text |目前作業的名稱，例如 Alert |
| 類別 |Text |推送到 Azure 監視器日誌的診斷資料類別。 一律為 Always AzureBackupReport |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedContainerUniqueId_s |Text |與警報關聯的受保護伺服器的唯一識別碼（在 V1 中ProtectedServerUniqueId_s）|
| VaultUniqueId_s |Text |與警示相關聯的受保護保存庫所具備的唯一識別碼 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |要收集其相關資料的資源所具備的唯一識別碼。 例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="backupitem"></a>BackupItem

下表提供備份項目相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |活動的名稱。 一律為 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |備份項目的唯一識別碼 |
| BackupItemId_s |Text |備份項的識別碼（此欄位僅適用于 v1 架構） |
| BackupItemName_s |Text |備份項目名稱 |
| BackupItemFriendlyName_s |Text |備份項目的易記名稱 |
| BackupItemType_s |Text |備份項目類型 (例如，VM、FileFolder) |
| BackupItemProtectionState_s |Text |備份項的保護狀態 |
| BackupItemAppVersion_s |Text |備份項的應用程式版本 |
| ProtectionState_s |Text |備份項目的目前保護狀態 (例如，受保護、ProtectionStopped) |
| ProtectionGroupName_s |Text | 備份專案受保護組的名稱，適用于 SC DPM 和 MABS（如果適用）|
| SecondaryBackupProtectionState_s |Text |是否為備份項啟用了次要保護|
| SchemaVersion_s |Text |架構的版本，例如**V2** |
| State_s |Text |備份項目物件的狀態，例如使用中、已刪除 |
| BackupManagementType_s |Text |用於執行本備份項目所屬之備份的提供者類型 (例如，IaaSVM、FileFolder) |
| OperationName |Text |作業的名稱，例如 BackupItem |
| 類別 |Text |推送到 Azure 監視器日誌的診斷資料類別。 一律為 Always AzureBackupReport |
| 資源 |Text |要收集其資料的資源，例如復原服務保存庫名稱 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集的資料的資源識別碼，例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="backupitemassociation"></a>BackupItemAssociation

下表提供與各種實體關聯的備份項目相關詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |備份項的唯一 ID |
| SchemaVersion_s |Text |此欄位表示架構的當前版本，它是**V2** |
| State_s |Text |備份項目關聯物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| BackupItemSourceSize_s |Text | 備份項的前端大小 |
| BackupManagementServerUniqueId_s |Text | 欄位，以唯一標識備份專案通過保護的備份管理伺服器（如果適用） |
| 類別 |Text |此欄位代表推送到 Log Analytics 的診斷資料類別，則是 AzureBackupReport |
| OperationName |Text |此欄位代表目前作業的名稱 - BackupItemAssociation |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedContainerUniqueId_s |Text |與備份項關聯的受保護伺服器的唯一識別碼（在 V1 中ProtectedServerUniqueId_s） |
| VaultUniqueId_s |Text |包含備份項目的保存庫所具備的唯一識別碼 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="backupmanagementserver"></a>備份管理伺服器

下表提供與各種實體關聯的備份項目相關詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |備份管理伺服器的名稱        |
|AzureBackupAgentVersion_s     |Text         |備份管理伺服器上的 Azure 備份代理版本          |
|BackupManagementServerVersion_s     |Text         |備份管理伺服器的版本|
|BackupManagementServerOSVersion_s    |Text            |備份管理伺服器的作業系統版本|
|BackupManagementServerType_s     |Text         |備份管理伺服器的類型，如 MABS、SC DPM|
|BackupManagementServerUniqueId_s     |Text         |欄位以唯一標識備份管理伺服器       |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="job"></a>工作 (Job)

下表提供作業相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |活動的名稱。 一律為 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |備份項目的唯一識別碼 |
| SchemaVersion_s |Text |架構的版本，例如**V2** |
| State_s |Text |作業物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text |此欄位代表目前作業的名稱 - Job |
| 類別 |Text |此欄位表示推送到 Azure 監視器日誌的診斷資料類別，它是 Azure 備份報告 |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedServerUniqueId_s |Text |與作業相關聯的受保護伺服器所具備的唯一識別碼 |
| ProtectedContainerUniqueId_s |Text | 標識作業上運行的受保護容器的唯一 ID |
| VaultUniqueId_s |Text |受保護保存庫的唯一識別碼 |
| JobOperation_s |Text |執行作業的操作 (例如，備份、還原、設定備份) |
| JobStatus_s |Text |已完成之作業的狀態 (例如，已完成、失敗) |
| JobFailureCode_s |Text |由於發生作業失敗而產生的失敗碼字串 |
| JobStartDateTime_s |日期/時間 |開始執行作業的日期和時間 |
| BackupStorageDestination_s |Text |備份儲存體的目的地 (例如，雲端、磁碟)  |
| AdHocOrScheduledJob_s |Text | 要指定作業是臨時作業還是"計畫"的欄位 |
| JobDurationInSecs_s | Number |總作業持續時間 (以秒為單位) |
| DataTransferredInMB_s | Number |此工作的資料轉送 (以 MB 為單位)|
| JobUniqueId_g |Text |標識作業的唯一 ID |
| RecoveryJobDestination_s |Text | 恢復作業的目標，其中恢復資料 |
| RecoveryJobRPDateTime_s |Datetime | 創建正在恢復的復原點的日期、時間 |
| RecoveryJobRPLocation_s |Text | 正在恢復的復原點存儲的位置|
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼|
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="policy"></a>原則

下表提供原則相關欄位的詳細資料。

| 欄位 | 資料類型 | 適用版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此欄位表示架構的當前版本，它是**V2** |
| State_s |Text ||原則物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text ||執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此欄位代表目前作業的名稱 - Policy |
| 類別 |Text ||此欄位表示推送到 Azure 監視器日誌的診斷資料類別，它是 Azure 備份報告 |
| 資源 |Text ||這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| PolicyUniqueId_g |Text ||標識策略的唯一 ID |
| PolicyName_s |Text ||所定義原則的名稱 |
| BackupFrequency_s |Text ||備份的執行頻率 (例如，每日、每週) |
| BackupTimes_s |Text ||排定要備份的日期和時間 |
| BackupDaysOfTheWeek_s |Text ||一週中已排定要備份的日期 |
| RetentionDuration_s |整數 ||所設定備份的保留期間 |
| DailyRetentionDuration_s |整數 ||所設定備份的總保留期間 (以天為單位) |
| DailyRetentionTimes_s |Text ||每日保留期的設定日期和時間 |
| WeeklyRetentionDuration_s |十進位數字 ||所設定備份的每週保留總期間 (以週為單位) |
| WeeklyRetentionTimes_s |Text ||每週保留期的設定日期和時間 |
| WeeklyRetentionDaysOfTheWeek_s |Text ||一週中所選用於每週保留期的日期 |
| MonthlyRetentionDuration_s |十進位數字 ||所設定備份的總保留期間 (以月為單位) |
| MonthlyRetentionTimes_s |Text ||每月保留期的設定日期和時間 |
| MonthlyRetentionFormat_s |Text ||每月保留期的設定類型 (例如，以天為基礎則每日、以週為基礎則每週) |
| MonthlyRetentionDaysOfTheWeek_s |Text ||一週中所選用於每月保留期的日期 |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||一個月中每月保留期的設定週數 (例如，第一週、最後一週等等)。 |
| YearlyRetentionDuration_s |十進位數字 ||所設定備份的總保留期間 (以年為單位) |
| YearlyRetentionTimes_s |Text ||每年保留期的設定日期和時間 |
| YearlyRetentionMonthsOfTheYear_s |Text ||一年中所選用於每年保留期的月數 |
| YearlyRetentionFormat_s |Text ||每年保留期的設定類型 (例如，以天為基礎則每日、以週為基礎則每週) | |
| YearlyRetentionDaysOfTheMonth_s |Text ||一個月中所選用於每年保留期的日期 |
| SynchronisationFrequencyPerDay_s |整數 |v2|為 SC DPM 和 MABS 同步檔案備份的一天次數 |
| DiffBackupFormat_s |Text |v2|Azure VM 備份中 SQL 差異備份的格式 |
| DiffBackupTime_s |Time |v2|Azure VM 備份中 SQL 的差異備份時間|
| DiffBackupRetentionDuration_s |十進位數字 |v2|Azure VM 備份中 SQL 差異備份的保留持續時間|
| LogBackupFrequency_s |十進位數字 |v2|SQL 記錄備份的頻率|
| LogBackupRetentionDuration_s |十進位數字 |v2|Azure VM 備份中 SQL 的記錄備份保留期|
| DiffBackupDaysofTheWeek_s |Text |v2|Azure VM 備份中 SQL 差異備份的一周天數|
| SourceSystem |Text ||目前資料的來源系統 - Azure |
| ResourceId |Text ||正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text ||要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text ||要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text ||要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||要收集其資料的資源類型。 例如保存庫 |

### <a name="policyassociation"></a>PolicyAssociation

下表提供與各種實體關聯的原則相關詳細資料。

| 欄位 | 資料類型 | 適用版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text ||此欄位表示架構的當前版本，它是**V2** |
| State_s |Text ||原則物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text ||執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此欄位代表目前作業的名稱 - PolicyAssociation |
| 類別 |Text ||此欄位表示推送到 Azure 監視器日誌的診斷資料類別，它是 Azure 備份報告 |
| 資源 |Text ||這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| PolicyUniqueId_g |Text ||標識策略的唯一 ID |
| VaultUniqueId_s |Text ||此策略所屬的保存庫的唯一 ID |
| BackupManagementServerUniqueId_s |Text |v2 |欄位，以唯一標識備份專案通過保護的備份管理伺服器（如果適用）        |
| SourceSystem |Text ||目前資料的來源系統 - Azure |
| ResourceId |Text ||正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text ||要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text ||要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text ||要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||要收集其資料的資源類型。 例如保存庫 |

### <a name="protected-container"></a>受保護的容器

此表提供有關受保護容器的基本欄位。 （在 v1 中保護伺服器）

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | 用於唯一標識受保護容器的欄位 |
| ProtectedContainerOSType_s |Text |受保護容器的作業系統類型 |
| ProtectedContainerOSVersion_s |Text |受保護容器的作業系統版本 |
| AgentVersion_s |Text |代理備份或保護代理的版本號（在 SC DPM 和 MABS 的情況下） |
| BackupManagementType_s |Text |用於執行備份的提供程式類型。 例如，IaaSVM，檔資料夾 |
| EntityState_s |Text |受保護伺服器物件的目前狀態。 例如，活動，已刪除 |
| ProtectedContainerFriendlyName_s |Text |受保護伺服器的易記名稱 |
| ProtectedContainerName_s |Text |受保護容器的名稱 |
| ProtectedContainerWorkloadType_s |Text |已備份的受保護容器的類型。 例如，IaaSVM 容器 |
| ProtectedContainerLocation_s |Text |受保護容器是位於本地還是位於 Azure 中 |
| ProtectedContainerType_s |Text |受保護容器是伺服器還是容器 |
| ProtectedContainerProtectionState_s'  |Text |受保護容器的保護狀態 |

### <a name="storage"></a>存放裝置

下表提供儲存體相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| CloudStorageInBytes_s |十進位數字 |備份使用的雲備份存儲，根據最新值計算（此欄位僅適用于 v1 架構）|
| ProtectedInstances_s |十進位數字 |用於在帳單中計算前端儲存體的受保護執行個體數目，在計算時會以最後的值為依據 |
| EventName_s |Text |此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text |此欄位表示架構的當前版本，它是**V2** |
| State_s |Text |儲存體物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text |此欄位代表目前作業的名稱 - Storage |
| 類別 |Text |此欄位表示推送到 Azure 監視器日誌的診斷資料類別，它是 Azure 備份報告 |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedServerUniqueId_s |Text |為其計算存儲的受保護伺服器的唯一 ID |
| VaultUniqueId_s |Text |計算存儲保存庫的唯一 ID |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |
| StorageUniqueId_s |Text |用於標識存儲實體的唯一 ID |
| StorageType_s |Text |存儲類型，例如雲、卷、磁片 |
| StorageName_s |Text |存儲實體的名稱，例如 E：* |
| StorageTotalSizeInGBs_s |Text |存儲實體使用的存儲總大小（以 GB）表示|

### <a name="storageassociation"></a>存儲關聯

此表提供將存儲連接到其他實體的基本存放裝置相關欄位。

| 欄位 | 資料類型 | 描述 |
| --- | --- |  --- |
| StorageUniqueId_s |Text |用於標識存儲實體的唯一 ID |
| SchemaVersion_s |Text |此欄位表示架構的當前版本，它是**V2** |
| BackupItemUniqueId_s |Text |用於標識與存儲實體相關的備份項的唯一 ID |
| BackupManagementServerUniqueId_s |Text |用於標識與存儲實體相關的備份管理伺服器的唯一 ID|
| VaultUniqueId_s |Text |用於標識與存儲實體相關的保存庫的唯一 ID|
| StorageConsumedInMBs_s |Number|相應存儲中相應備份項消耗的存儲大小 |
| StorageAllocatedInMBs_s |Number |在類型磁片的相應存儲中由相應備份項分配的存儲大小|

### <a name="vault"></a>保存庫

下表提供保存庫相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此欄位代表這個事件的名稱，它一律為 AzureBackupCentralReport |
| SchemaVersion_s |Text |此欄位表示架構的當前版本，它是**V2** |
| State_s |Text |保存庫物件的目前狀態 (例如，作用中、已刪除) |
| OperationName |Text |此欄位代表目前作業的名稱 - Vault |
| 類別 |Text |此欄位表示推送到 Azure 監視器日誌的診斷資料類別，它是 Azure 備份報告 |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| VaultUniqueId_s |Text |保存庫的唯一 ID |
| VaultName_s |Text |保存庫名稱 |
| AzureDataCenter_s |Text |保存庫所在的資料中心 |
| StorageReplicationType_s |Text |保存庫的儲存體複寫類型 (例如，GeoRedundant) |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="backup-management-server"></a>備份管理伺服器

此表提供有關備份管理伺服器的基本欄位。

|欄位  |資料類型  | 描述  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |備份管理伺服器的名稱        |
|AzureBackupAgentVersion_s     |Text         |備份管理伺服器上的 Azure 備份代理版本          |
|BackupManagementServerVersion_s     |Text         |備份管理伺服器的版本|
|BackupManagementServerOSVersion_s     |Text            |備份管理伺服器的作業系統版本|
|BackupManagementServerType_s     |Text         |備份管理伺服器的類型，如 MABS、SC DPM|
|BackupManagementServerUniqueId_s     |Text         |欄位以唯一標識備份管理伺服器       |

### <a name="preferredworkloadonvolume"></a>首選工作負載

此表指定卷關聯的工作負載。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| StorageUniqueId_s |Text |用於標識存儲實體的唯一 ID |
| BackupItemType_s |Text |此卷是首選存儲的工作負載|

### <a name="protectedinstance"></a>受保護實例

此表提供與實例相關的基本受保護欄位。

| 欄位 | 資料類型 |適用版本 | 描述 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|使用 DPM MABS 標識備份的 VM 備份專案的唯一 ID|
| ProtectedContainerUniqueId_s |Text |v2|用於標識使用 DPM MABS 備份的 VM 以外的所有內容的受保護容器的唯一 ID|
| ProtectedInstanceCount_s |Text |v2|該日期時間關聯備份項或受保護容器的受保護實例計數|

### <a name="recoverypoint"></a>RecoveryPoint

此表提供基本復原點相關欄位。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |使用 DPM MABS 標識備份的 VM 備份專案的唯一 ID|
| OldestRecoveryPointTime_s |Text |備份項最舊復原點的日期時間|
| OldestRecoveryPointLocation_s |Text |備份項最舊復原點的位置|
| LatestRecoveryPointTime_s |Text |備份專案的最新復原點的日期時間|
| LatestRecoveryPointLocation_s |Text |備份專案的最新復原點的位置|

## <a name="sample-kusto-queries"></a>庫斯托查詢示例

下面是一些示例，可説明您編寫駐留在 Azure 診斷表中的 Azure 備份資料的查詢：

- 所有成功的備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- 所有失敗的備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- 所有成功的 Azure VM 備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- 所有成功的 SQL 記錄備份作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- 所有成功的 Azure 備份代理作業

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````
    
## <a name="next-steps"></a>後續步驟

查看資料模型後，可以在 Azure 監視器日誌中開始[創建自訂查詢](../azure-monitor/learn/tutorial-logs-dashboards.md)，以構建自己的儀表板。
