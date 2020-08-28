---
title: Azure 監視器記錄資料模型
description: 在本文中，您將詳盡了解 Azure 備份資料的 Azure 監視器 Log Analytics 資料模型。
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: 1fcb4eb0c584f792132f19c8c4d66289342aa36e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020944"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>適用於 Azure 備份資料的 Log Analytics 資料模型

使用 Log Analytics 資料模型從 Log Analytics 建立自訂警示。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> 此資料模型會參考將診斷事件傳送至 Log Analytics (LA) 的 Azure 診斷模式。 若要了解新的資源特定模式適用的資料模型，可以參考下列文章：[Azure 備份診斷事件的資料模型](./backup-azure-reports-data-model.md)

## <a name="using-azure-backup-data-model"></a>使用 Azure 備份資料模型

您可以使用提供的下欄欄位做為資料模型的一部分，以根據您的需求建立視覺效果、自訂查詢和儀表板。

### <a name="alert"></a>警示

下表提供警示相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| AlertUniqueId_s |Text |所產生警示的唯一識別碼 |
| AlertType_s |Text |警示的類型，例如備份 |
| AlertStatus_s |Text |警示狀態 (例如，作用中) |
| AlertOccurrenceDateTime_s |日期/時間 |建立警示的日期和時間 |
| AlertSeverity_s |Text |警示嚴重性 (例如，重大) |
|AlertTimeToResolveInMinutes_s    | Number        |解決警示所花費的時間。 作用中警示的時間會呈現為空白。         |
|AlertConsolidationStatus_s   |Text         |識別警示是否為合併警示         |
|CountOfAlertsConsolidated_s     |Number         |合併警示時合併的警示數目          |
|AlertRaisedOn_s     |Text         |引發警示的實體類型         |
|AlertCode_s     |Text         |唯一識別警示類型的代碼         |
|RecommendedAction_s   |Text         |建議用來解決警示的動作         |
| EventName_s |Text |活動的名稱。 一律為 AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |與警示相關聯的備份項目所具備的唯一識別碼 |
| SchemaVersion_s |Text |結構描述的目前版本，例如 **V2** |
| State_s |Text |警示物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份的提供者類型，例如，IaaSVM，此警示所屬的 Filefolder)  |
| OperationName |Text |目前作業的名稱，例如 Alert |
| 類別 |Text |推送至 Azure 監視器記錄的診斷資料類別。 一律為 Always AzureBackupReport |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedContainerUniqueId_s |Text |與警示相關聯的受保護伺服器所具備的唯一識別碼 (V1 中的識別碼為 ProtectedServerUniqueId_s)|
| VaultUniqueId_s |Text |與警示相關聯的受保護保存庫所具備的唯一識別碼 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |要收集其相關資料的資源所具備的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
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
| BackupItemId_s |Text |備份項目的識別碼 (此欄位僅適用於 v1 結構描述) |
| BackupItemName_s |Text |備份項目名稱 |
| BackupItemFriendlyName_s |Text |備份項目的易記名稱 |
| BackupItemType_s |Text |備份項目類型 (例如，VM、FileFolder) |
| BackupItemProtectionState_s |Text |備份項目的保護狀態 |
| BackupItemAppVersion_s |Text |備份項目的應用程式版本 |
| ProtectionState_s |Text |備份項目的目前保護狀態 (例如，受保護、ProtectionStopped) |
| ProtectionGroupName_s |Text | 保護備份項目的保護群組名稱，用於 SC DPM 和 MABS (如果適用)|
| SecondaryBackupProtectionState_s |Text |是否要為備份項目啟用次要保護|
| SchemaVersion_s |Text |結構描述的版本，例如 **V2** |
| State_s |Text |備份項目物件的狀態，例如使用中、已刪除 |
| BackupManagementType_s |Text |執行備份的提供者類型，例如，此備份專案所屬的 IaaSVM、Filefolder)  |
| OperationName |Text |作業的名稱，例如 BackupItem |
| 類別 |Text |推送至 Azure 監視器記錄的診斷資料類別。 一律為 Always AzureBackupReport |
| 資源 |Text |要收集其資料的資源，例如復原服務保存庫名稱 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼，例如復原服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="backupitemassociation"></a>BackupItemAssociation

下表提供與各種實體關聯的備份項目相關詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此欄位代表此事件的名稱。 永遠為 azurebackupcentralreport |  
| BackupItemUniqueId_s |Text |備份項目的唯一識別碼 |
| SchemaVersion_s |Text |此欄位代表目前的架構版本。 它是 **V2** |
| State_s |Text |備份項目關聯物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| BackupItemSourceSize_s |Text | 備份項目的前端大小 |
| BackupManagementServerUniqueId_s |Text | 可唯一識別用來保護備份項目之備份管理伺服器的欄位 (如果適用) |
| 類別 |Text |此欄位代表推送至 Log Analytics 的診斷資料類別。 它是 AzureBackupReport |
| OperationName |Text |此欄位代表目前作業的名稱 - BackupItemAssociation |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedContainerUniqueId_s |Text |與備份項目相關聯的受保護伺服器所具備的唯一識別碼 (V1 中的識別碼為 ProtectedServerUniqueId_s) |
| VaultUniqueId_s |Text |包含備份項目的保存庫所具備的唯一識別碼 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |正在收集資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |正在收集資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |正在收集資料的資源提供者，例如 Microsoft.RecoveryServices |
| ResourceType |Text |正在收集資料的資源類型，例如保存庫 |

### <a name="backupmanagementserver"></a>BackupManagementServer

下表提供與各種實體關聯的備份項目相關詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |備份管理伺服器的名稱        |
|AzureBackupAgentVersion_s     |Text         |備份管理伺服器上的 Azure 備份代理程式版本          |
|BackupManagementServerVersion_s     |Text         |備份管理伺服器的版本|
|BackupManagementServerOSVersion_s    |Text            |備份管理伺服器的作業系統版本|
|BackupManagementServerType_s     |Text         |備份管理伺服器的類型，如 MABS、SC DPM|
|BackupManagementServerUniqueId_s     |Text         |可唯一識別備份管理伺服器的欄位       |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
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
| SchemaVersion_s |Text |結構描述的版本，例如 **V2** |
| State_s |Text |作業物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text |此欄位代表目前作業的名稱 - Job |
| 類別 |Text |此欄位代表推送至 Azure 監視器記錄的診斷資料類別。 它是 AzureBackupReport |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedServerUniqueId_s |Text |與作業相關聯的受保護伺服器所具備的唯一識別碼 |
| ProtectedContainerUniqueId_s |Text | 用來識別作業執行所在之受保護容器的唯一識別碼 |
| VaultUniqueId_s |Text |受保護保存庫的唯一識別碼 |
| JobOperation_s |Text |執行作業的操作 (例如，備份、還原、設定備份) |
| JobStatus_s |Text |已完成之作業的狀態 (例如，已完成、失敗) |
| JobFailureCode_s |Text |由於發生作業失敗而產生的失敗碼字串 |
| JobStartDateTime_s |日期/時間 |開始執行作業的日期和時間 |
| BackupStorageDestination_s |Text |備份儲存體的目的地 (例如，雲端、磁碟)  |
| AdHocOrScheduledJob_s |Text | 指定作業為臨機操作還是排程作業的欄位 |
| JobDurationInSecs_s | Number |總作業持續時間 (以秒為單位) |
| DataTransferredInMB_s | Number |此工作的資料轉送 (以 MB 為單位)|
| JobUniqueId_g |Text |用來識別作業的唯一識別碼 |
| RecoveryJobDestination_s |Text | 復原作業的目的地，資料會在此處復原 |
| RecoveryJobRPDateTime_s |Datetime | 要復原之復原點的建立日期（時間） |
| RecoveryJobRPLocation_s |Text | 要復原之復原點的儲存位置|
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼|
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="policy"></a>原則

下表提供原則相關欄位的詳細資料。

| 欄位 | 資料類型 | 適用的版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此欄位代表此事件的名稱。 永遠為 azurebackupcentralreport |
| SchemaVersion_s |Text ||此欄位代表目前的架構版本。 它是 **V2** |
| State_s |Text ||原則物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text ||執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此欄位代表目前作業的名稱 - Policy |
| 類別 |Text ||此欄位代表推送至 Azure 監視器記錄的診斷資料類別。 它是 AzureBackupReport |
| 資源 |Text ||這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| PolicyUniqueId_g |Text ||用來識別原則的唯一識別碼 |
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
| MonthlyRetentionWeeksOfTheMonth_s |Text ||每月保留期的設定，例如： First、Last |
| YearlyRetentionDuration_s |十進位數字 ||所設定備份的總保留期間 (以年為單位) |
| YearlyRetentionTimes_s |Text ||每年保留期的設定日期和時間 |
| YearlyRetentionMonthsOfTheYear_s |Text ||一年中所選用於每年保留期的月數 |
| YearlyRetentionFormat_s |Text ||每年保留期的設定類型 (例如，以天為基礎則每日、以週為基礎則每週) | |
| YearlyRetentionDaysOfTheMonth_s |Text ||一個月中所選用於每年保留期的日期 |
| SynchronisationFrequencyPerDay_s |整數 |v2|在一天之中為 SC DPM 和 MABS 同步處理檔案備份的次數 |
| DiffBackupFormat_s |Text |v2|Azure VM 備份中用於 SQL 差異備份的格式 |
| DiffBackupTime_s |Time |v2|Azure VM 備份中用於 SQL 差異備份的時間|
| DiffBackupRetentionDuration_s |十進位數字 |v2|Azure VM 備份中用於 SQL 差異備份的保留期間|
| LogBackupFrequency_s |十進位數字 |v2|SQL 記錄備份的頻率|
| LogBackupRetentionDuration_s |十進位數字 |v2|Azure VM 備份中用於 SQL 記錄備份的保留期間|
| DiffBackupDaysofTheWeek_s |Text |v2|Azure VM 備份中用於 SQL 差異備份的一週天數|
| SourceSystem |Text ||目前資料的來源系統 - Azure |
| ResourceId |Text ||正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text ||要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text ||要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text ||要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||要收集其資料的資源類型。 例如保存庫 |

### <a name="policyassociation"></a>PolicyAssociation

下表提供與各種實體關聯的原則相關詳細資料。

| 欄位 | 資料類型 | 適用的版本 | 描述 |
| --- | --- | --- | --- |
| EventName_s |Text ||此欄位代表此事件的名稱。 永遠為 azurebackupcentralreport |
| SchemaVersion_s |Text ||此欄位代表目前的架構版本。 它是 **V2** |
| State_s |Text ||原則物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text ||執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text ||此欄位代表目前作業的名稱 - PolicyAssociation |
| 類別 |Text ||此欄位代表推送至 Azure 監視器記錄的診斷資料類別。 它是 AzureBackupReport |
| 資源 |Text ||這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| PolicyUniqueId_g |Text ||用來識別原則的唯一識別碼 |
| VaultUniqueId_s |Text ||此原則所屬之保存庫的唯一識別碼 |
| BackupManagementServerUniqueId_s |Text |v2 |可唯一識別用來保護備份項目之備份管理伺服器的欄位 (如果適用)        |
| SourceSystem |Text ||目前資料的來源系統 - Azure |
| ResourceId |Text ||正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text ||要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text ||要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text ||要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text ||要收集其資料的資源類型。 例如保存庫 |

### <a name="protected-container"></a>受保護的容器

下表提供關於受保護容器的基本欄位。 (在 v1 中為 ProtectedServer)

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | 可唯一識別受保護容器的欄位 |
| ProtectedContainerOSType_s |Text |受保護容器的作業系統類型 |
| ProtectedContainerOSVersion_s |Text |受保護容器的作業系統版本 |
| AgentVersion_s |Text |在 SC DPM 和 MABS) 的情況下，代理程式備份或保護代理程式的版本號碼 ( |
| BackupManagementType_s |Text |執行備份的提供者類型。 例如 IaaSVM、Filefolder |
| EntityState_s |Text |受保護伺服器物件的目前狀態。 例如，作用中、已刪除 |
| ProtectedContainerFriendlyName_s |Text |受保護伺服器的易記名稱 |
| ProtectedContainerName_s |Text |受保護容器的名稱 |
| ProtectedContainerWorkloadType_s |Text |已備份的受保護容器類型。 例如 IaaSVMContainer |
| ProtectedContainerLocation_s |Text |受保護的容器位於內部部署環境還是 Azure 中 |
| ProtectedContainerType_s |Text |受保護的容器是伺服器還是容器 |
| ProtectedContainerProtectionState_s’  |Text |受保護容器的保護狀態 |

### <a name="storage"></a>儲存體

下表提供儲存體相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| CloudStorageInBytes_s |十進位數字 |備份所使用的雲端備份儲存體，會根據最新的值來計算 (此欄位僅適用於 v1 結構描述)|
| ProtectedInstances_s |十進位數字 |用於在帳單中計算前端儲存體的受保護執行個體數目，在計算時會以最後的值為依據 |
| EventName_s |Text |此欄位代表此事件的名稱。 永遠為 azurebackupcentralreport |
| SchemaVersion_s |Text |此欄位代表目前的架構版本。 它是 **V2** |
| State_s |Text |儲存體物件的目前狀態 (例如，作用中、已刪除) |
| BackupManagementType_s |Text |執行備份作業的伺服器所屬的提供者類型，例如 IaaSVM、FileFolder |
| OperationName |Text |此欄位代表目前作業的名稱 - Storage |
| 類別 |Text |此欄位代表推送至 Azure 監視器記錄的診斷資料類別。 它是 AzureBackupReport |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| ProtectedServerUniqueId_s |Text |進行儲存體計算之受保護伺服器的唯一識別碼 |
| VaultUniqueId_s |Text |計算儲存體之保存庫的唯一識別碼 |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |
| StorageUniqueId_s |Text |用來識別儲存體實體的唯一識別碼 |
| StorageType_s |Text |儲存體的類型，例如雲端、磁碟區、磁碟 |
| StorageName_s |Text |儲存體實體的名稱，例如 E:\ |
| StorageTotalSizeInGBs_s |Text |儲存體實體所耗用的儲存體大小總計 (以 GB 為單位)|

### <a name="storageassociation"></a>StorageAssociation

下表提供將儲存體連結至其他實體的基本儲存體相關欄位。

| 欄位 | 資料類型 | 描述 |
| --- | --- |  --- |
| StorageUniqueId_s |Text |用來識別儲存體實體的唯一識別碼 |
| SchemaVersion_s |Text |此欄位代表目前的架構版本。 它是 **V2** |
| BackupItemUniqueId_s |Text |用來對儲存體實體的相關備份項目進行識別的唯一識別碼 |
| BackupManagementServerUniqueId_s |Text |用來對儲存體實體的相關備份管理伺服器進行識別的唯一識別碼|
| VaultUniqueId_s |Text |用來對儲存體實體的相關保存庫進行識別的唯一識別碼|
| StorageConsumedInMBs_s |Number|對應儲存區中的對應備份項目所使用的儲存體大小 |
| StorageAllocatedInMBs_s |Number |對應儲存區 (屬於磁碟類型) 中的對應備份項目所配置的儲存體大小|

### <a name="vault"></a>保存庫

下表提供保存庫相關欄位的詳細資料。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| EventName_s |Text |此欄位代表此事件的名稱。 永遠為 azurebackupcentralreport |
| SchemaVersion_s |Text |此欄位代表目前的架構版本。 它是 **V2** |
| State_s |Text |保存庫物件的目前狀態 (例如，作用中、已刪除) |
| OperationName |Text |此欄位代表目前作業的名稱 - Vault |
| 類別 |Text |此欄位代表推送至 Azure 監視器記錄的診斷資料類別。 它是 AzureBackupReport |
| 資源 |Text |這是所收集資料的資源，會顯示復原服務保存庫名稱 |
| VaultUniqueId_s |Text |保存庫的唯一識別碼 |
| VaultName_s |Text |保存庫名稱 |
| AzureDataCenter_s |Text |保存庫所在的資料中心 |
| StorageReplicationType_s |Text |保存庫的儲存體複寫類型 (例如，GeoRedundant) |
| SourceSystem |Text |目前資料的來源系統 - Azure |
| ResourceId |Text |正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| SubscriptionId |Text |要收集其資料的資源 (例如復原服務保存庫) 的訂用帳戶識別碼 。 |
| ResourceGroup |Text |要收集其資料的資源 (例如復原服務保存庫) 的資源群組 。 |
| ResourceProvider |Text |要收集其資料的資源提供者。 例如 Microsoft.RecoveryServices |
| ResourceType |Text |要收集其資料的資源類型。 例如保存庫 |

### <a name="backup-management-server"></a>備份管理伺服器

下表提供關於備份管理伺服器的基本欄位。

|欄位  |資料類型  | 描述  |
|---------|---------|----------|
|BackupManagementServerName_s     |Text         |備份管理伺服器的名稱        |
|AzureBackupAgentVersion_s     |Text         |備份管理伺服器上的 Azure 備份代理程式版本          |
|BackupManagementServerVersion_s     |Text         |備份管理伺服器的版本|
|BackupManagementServerOSVersion_s     |Text            |備份管理伺服器的作業系統版本|
|BackupManagementServerType_s     |Text         |備份管理伺服器的類型，如 MABS、SC DPM|
|BackupManagementServerUniqueId_s     |Text         |可唯一識別備份管理伺服器的欄位       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

此表格指定與磁碟區相關聯的工作負載。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| StorageUniqueId_s |Text |用來識別儲存體實體的唯一識別碼 |
| BackupItemType_s |Text |以此磁碟區作為慣用儲存體的工作負載|

### <a name="protectedinstance"></a>ProtectedInstance

此表格提供基本的受保護執行個體相關欄位。

| 欄位 | 資料類型 |適用的版本 | 描述 |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|用來對使用 DPM、MABS 進行備份的 VM 識別其備份項目的唯一識別碼|
| ProtectedContainerUniqueId_s |Text |v2|用來對所有項目 (使用 DPM、MABS 進行備份的 VM 除外) 的受保護容器進行識別的唯一識別碼|
| ProtectedInstanceCount_s |Text |v2|相關聯的備份項目或受保護容器在該日期時間的受保護執行個體計數|

### <a name="recoverypoint"></a>RecoveryPoint

此表格提供基本的復原點相關欄位。

| 欄位 | 資料類型 | 描述 |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |用來對使用 DPM、MABS 進行備份的 VM 識別其備份項目的唯一識別碼|
| OldestRecoveryPointTime_s |Text |備份項目的最舊復原點的日期時間|
| OldestRecoveryPointLocation_s |Text |備份項目的最舊復原點所在的位置|
| LatestRecoveryPointTime_s |Text |備份項目的最新復原點的日期時間|
| LatestRecoveryPointLocation_s |Text |備份項目的最新復原點所在的位置|

## <a name="sample-kusto-queries"></a>範例 Kusto 查詢

以下幾個範例可協助您對位於 Azure 診斷資料表中的 Azure 備份資料撰寫查詢：

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

- 所有成功的 Azure 備份代理程式作業

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

## <a name="v1-schema-vs-v2-schema"></a>V1 結構描述與 V2 結構描述

先前，Azure 備份代理程式和 Azure VM 備份的診斷資料會傳送至名為 ***V1 結構描述***的結構描述中的 Azure 診斷資料表。 而在新增了新的資料行以支援其他案例和工作負載之後，診斷資料則會推送至名為 ***V2 結構描述***的新結構描述中。  

基於回溯相容性，Azure 備份代理程式和 Azure VM 備份的診斷資料現在會同時傳送至 V1 和 V2 結構描述中的 Azure 診斷資料表 (V1 結構描述現在位於淘汰的路徑上)。 您可以在記錄查詢中篩選 SchemaVersion_s=="V1" 的記錄，以識別 Log Analytics 中的哪些記錄屬於 V1 結構描述。

請參閱前述[資料模型](#using-azure-backup-data-model)中的第三個資料行「描述」，以識別哪些資料行僅屬於 V1 結構描述。

### <a name="modifying-your-queries-to-use-the-v2-schema"></a>修改您的查詢以使用 V2 架構

由於 V1 架構是在淘汰路徑上，建議您在 Azure 備份診斷資料的所有自訂查詢中只使用 V2 架構。 以下範例說明如何更新您的查詢，以移除 V1 架構的相依性：

1. 識別您的查詢是否使用僅適用于 V1 架構的任何欄位。 假設您有一個查詢可列出所有備份專案及其相關聯的受保護伺服器，如下所示：

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | distinct BackupItemUniqueId_s, ProtectedServerUniqueId_s
    ````

    上述查詢會使用欄位 ProtectedServerUniqueId_s，這僅適用于 V1 架構。 此欄位的 V2 架構對等專案是 ProtectedContainerUniqueId_s (參考上述的資料表) 。 欄位 BackupItemUniqueId_s 也適用于 V2 架構，而此查詢中也可以使用相同的欄位。

2. 將查詢更新為使用 V2 架構功能變數名稱。 建議的作法是在所有查詢中使用 **SchemaVersion_s = = "V2"** 的篩選準則，如此一來，查詢才會剖析對應至 V2 架構的記錄：

    ````Kusto
    AzureDiagnostics
    | where Category=="AzureBackupReport"
    | where OperationName=="BackupItemAssociation"
    | where SchemaVersion_s=="V2"
    | distinct BackupItemUniqueId_s, ProtectedContainerUniqueId_s
    ````

## <a name="next-steps"></a>後續步驟

檢閱資料模型之後，您即可開始在 Azure 監視器記錄中[建立自訂查詢](../azure-monitor/learn/tutorial-logs-dashboards.md)，以建置您自己的儀表板。
