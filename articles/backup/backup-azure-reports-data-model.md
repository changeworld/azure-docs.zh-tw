---
title: Azure 備份診斷事件的資料模型
description: 此資料模型參考將診斷事件傳送至 Log Analytics (LA) 的資源特定模式。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: c2c5d37596be104c4b1dc7e865586a4728a27bae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91569591"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure 備份診斷事件的資料模型

## <a name="coreazurebackup"></a>CoreAzureBackup

下表提供核心備份實體的相關資訊，例如保存庫和備份專案。

| **欄位**                         | **資料類型** | **說明**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | 正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼。 |
| OperationName                     | Text          | 此欄位代表目前作業的名稱-BackupItem、BackupItemAssociation 或 ProtectedContainer。 |
| 類別                          | Text          | 此欄位代表推送至 Azure 監視器記錄的診斷資料類別。 例如，CoreAzureBackup。 |
| AgentVersion                      | Text          | 在 SC DPM 和 MABS) 的情況下，代理程式備份或保護代理程式的版本號碼 ( |
| AzureBackupAgentVersion           | Text          | 備份管理伺服器上的 Azure 備份代理程式版本 |
| AzureDataCenter                   | Text          | 保存庫所在的資料中心                       |
| BackupItemAppVersion              | Text          | 備份項目的應用程式版本                       |
| BackupItemFriendlyName            | Text          | 備份專案的易記名稱                             |
| BackupItemName                    | Text          | 備份專案的名稱                                      |
| BackupItemProtectionState         | Text          | 備份項目的保護狀態                          |
| BackupItemFrontEndSize            | Text          | 備份項目的前端大小                            |
| BackupItemType                    | Text          | 備份專案的類型。 例如： VM、Filefolder)              |
| BackupItemUniqueId                | Text          | 備份項目的唯一識別碼                         |
| BackupManagementServerType        | Text          | 備份管理伺服器的類型，如 MABS、SC DPM     |
| BackupManagementServerUniqueId    | Text          | 可唯一識別備份管理伺服器的欄位      |
| BackupManagementType              | Text          | 執行備份作業的伺服器提供者類型。 例如 IaaSVM、Filefolder |
| BackupManagementServerName        | Text          | 備份管理伺服器的名稱                         |
| BackupManagementServerOSVersion   | Text          | 備份管理伺服器的作業系統版本                   |
| BackupManagementServerVersion     | Text          | 備份管理伺服器的版本                      |
| LatestRecoveryPointLocation       | Text          | 備份項目的最新復原點所在的位置    |
| LatestRecoveryPointTime           | Datetime      | 備份項目的最新復原點的日期時間   |
| OldestRecoveryPointLocation       | Text          | 備份項目的最舊復原點所在的位置    |
| OldestRecoveryPointTime           | Datetime      | 備份項目的最新復原點的日期時間   |
| PolicyUniqueId                    | Text          | 用來識別原則的唯一識別碼                             |
| ProtectedContainerFriendlyName    | Text          | 受保護伺服器的易記名稱                        |
| ProtectedContainerLocation        | Text          | 受保護的容器是否位於內部部署或 Azure |
| ProtectedContainerName            | Text          | 受保護容器的名稱                            |
| ProtectedContainerOSType          | Text          | 受保護容器的 OS 類型                          |
| ProtectedContainerOSVersion       | Text          | 受保護容器的作業系統版本                        |
| ProtectedContainerProtectionState | Text          | 受保護容器的保護狀態                  |
| ProtectedContainerType            | Text          | 受保護的容器是伺服器還是容器  |
| ProtectedContainerUniqueId        | Text          | 用來對所有項目 (使用 DPM、MABS 進行備份的 VM 除外) 的受保護容器進行識別的唯一識別碼 |
| ProtectedContainerWorkloadType    | Text          | 備份的受保護容器的類型。 例如 IaaSVMContainer |
| ProtectionGroupName               | Text          | 備份專案受保護的保護組名，適用于 SC DPM 和 MABS （如果適用） |
| resourceGroupName                 | Text          | 資源的資源群組 (例如，復原服務保存庫) 收集的資料 |
| schemaVersion                     | Text          | 此欄位代表目前的架構版本。 它是 **V2** |
| SecondaryBackupProtectionState    | Text          | 是否要為備份項目啟用次要保護  |
| State                             | Text          | 備份專案物件的狀態。 例如，作用中、已刪除 |
| StorageReplicationType            | Text          | 保存庫的儲存體複寫類型。 例如，異地備援 |
| SubscriptionId                    | Text          | 資源的訂用帳戶識別碼 (例如，收集資料的復原服務保存庫)  |
| VaultName                         | Text          | 保存庫名稱                                            |
| VaultTags                         | Text          | 與保存庫資源相關聯的標記                    |
| VaultUniqueId                     | Text          | 保存庫的唯一識別碼                             |
| SourceSystem                      | Text          | 目前資料的來源系統-Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

下表提供警示相關欄位的詳細資料。

| **欄位**                      | **資料類型** | **說明**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 要收集其相關資料的資源所具備的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | Text          | 目前作業的名稱。 例如，警示            |
| 類別                       | Text          | 推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupAlerts |
| AlertCode                      | Text          | 用來唯一識別警示類型的程式碼                     |
| AlertConsolidationStatus       | Text          | 識別警示是否為合併警示         |
| AlertOccurrenceDateTime        | Datetime      | 建立警示的日期和時間                     |
| AlertRaisedOn                  | Text          | 引發警示的實體類型                        |
| AlertSeverity                  | Text          | 警示的嚴重性。 例如，重大                 |
| AlertStatus                    | Text          | 警示的狀態。 例如，Active                     |
| AlertTimeToResolveInMinutes    | Number        | 解決警示所花費的時間。 作用中警示的時間會呈現為空白。     |
| AlertType                      | Text          | 警示的類型。 例如，備份                           |
| AlertUniqueId                  | Text          | 所產生警示的唯一識別碼                    |
| BackupItemUniqueId             | Text          | 與警示相關聯之備份專案的唯一識別碼 |
| BackupManagementServerUniqueId | Text          | 用以唯一識別備份管理伺服器的欄位（如果適用的話） |
| BackupManagementType           | Text          | 執行備份作業的伺服器提供者類型，例如 IaaSVM、Filefolder)  |
| CountOfAlertsConsolidated      | 數字        | 合併警示時合併的警示數目  |
| ProtectedContainerUniqueId     | Text          | 與警示相關聯之受保護伺服器的唯一識別碼 |
| RecommendedAction              | Text          | 建議用來解決警示的動作                      |
| schemaVersion                  | Text          | 結構描述的目前版本，例如 **V2**            |
| State                          | Text          | 警示物件的目前狀態 (例如，作用中、已刪除) |
| StorageUniqueId                | Text          | 用來識別儲存體實體的唯一識別碼                |
| VaultUniqueId                  | Text          | 用來識別與警示相關之保存庫的唯一識別碼    |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

此表格提供基本的受保護執行個體相關欄位。

| **欄位**                      | **資料類型** | **說明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 要收集資料的資源的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | Text          | 作業的名稱，例如 ProtectedInstance         |
| 類別                       | Text          | 推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Text          | 備份項目的唯一識別碼                                 |
| BackupManagementServerUniqueId | Text          | 用以唯一識別備份管理伺服器的欄位（如果適用的話） |
| BackupManagementType           | Text          | 執行備份作業的伺服器提供者類型，例如 IaaSVM、Filefolder)  |
| ProtectedContainerUniqueId     | Text          | 用來識別工作執行所在之受保護容器的唯一識別碼 |
| ProtectedInstanceCount         | Text          | 相關聯的備份專案或該日期時間受保護容器的受保護實例計數 |
| schemaVersion                  | Text          | 結構描述的目前版本，例如 **V2**            |
| State                          | Text          | 備份專案物件的狀態，例如 [作用中]、[已刪除] |
| VaultUniqueId                  | Text          | 與受保護的實例相關聯之受保護保存庫的唯一識別碼 |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

下表提供作業相關欄位的詳細資料。

| **欄位**                      | **資料類型** | **說明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | Text          | 此欄位代表目前作業的名稱 - Job    |
| 類別                       | Text          | 此欄位代表推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Text          | 指定作業為臨機操作還是排程作業的欄位           |
| BackupItemUniqueId             | Text          | 用來對儲存體實體的相關備份項目進行識別的唯一識別碼 |
| BackupManagementServerUniqueId | Text          | 用來對儲存體實體的相關備份管理伺服器進行識別的唯一識別碼 |
| BackupManagementType           | Text          | 用於執行本作業所屬之備份的提供者類型 (例如，IaaSVM、FileFolder) |
| DataTransferredInMB            | Number        | 此工作的資料轉送 (以 MB 為單位)                          |
| JobDurationInSecs              | Number        | 總作業持續時間 (以秒為單位)                                |
| JobFailureCode                 | Text          | 由於發生作業失敗而產生的失敗碼字串    |
| JobOperation                   | Text          | 執行作業的作業，例如備份、還原、設定備份 |
| JobOperationSubType            | Text          | 作業作業的子類型。 例如，在記錄備份作業的情況下，' Log ' |
| JobStartDateTime               | Datetime      | 開始執行作業的日期和時間                       |
| JobStatus                      | Text          | 已完成之作業的狀態 (例如，已完成、失敗)   |
| JobUniqueId                    | Text          | 用來識別作業的唯一識別碼                                |
| ProtectedContainerUniqueId     | Text          | 與作業相關聯之受保護伺服器的唯一識別碼 |
| RecoveryJobDestination         | Text          | 復原作業的目的地，資料會在此處復原   |
| RecoveryJobRPDateTime          | Datetime      | 要復原之復原點的建立日期（時間） |
| RecoveryJobLocation            | Text          | 要復原之復原點的儲存位置 |
| RecoveryLocationType           | Text          | 復原位置的類型                                |
| schemaVersion                  | Text          | 結構描述的目前版本，例如 **V2**            |
| State                          | Text          | 工作物件的目前狀態（例如，作用中、已刪除） |
| VaultUniqueId                  | Text          | 與作業相關聯之受保護保存庫的唯一識別碼 |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

下表提供原則相關欄位的詳細資料。

| **欄位**                       | **資料類型**  | **說明**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | 要收集資料的資源的唯一識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                   | Text           | 作業的名稱，例如原則或 PolicyAssociation |
| 類別                        | Text           | 推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Text           | 一週中已排定要備份的日期            |
| BackupFrequency                 | Text           | 執行備份的頻率。 例如，每日、每週 |
| BackupManagementType            | Text           | 執行備份作業的伺服器提供者類型。 例如，IaaSVM、Filefolder)  |
| BackupManagementServerUniqueId  | Text           | 用以唯一識別備份管理伺服器的欄位（如果適用的話） |
| BackupTimes                     | Text           | 排定要備份的日期和時間                     |
| DailyRetentionDuration          | 整數   | 所設定備份的總保留期間 (以天為單位)      |
| DailyRetentionTimes             | Text           | 每日保留期的設定日期和時間            |
| DiffBackupDaysOfTheWeek         | Text           | Azure VM 備份中的 SQL 差異備份在一周中的哪幾天 |
| DiffBackupFormat                | Text           | Azure VM 備份中用於 SQL 差異備份的格式   |
| DiffBackupRetentionDuration     | 十進位數字 | Azure VM 備份中的 SQL 差異備份保留期間 |
| DiffBackupTime                  | 時間           | Azure VM 備份中用於 SQL 差異備份的時間     |
| LogBackupFrequency              | 十進位數字 | SQL 記錄備份的頻率                            |
| LogBackupRetentionDuration      | 十進位數字 | Azure VM 備份中的 SQL 記錄備份保留期間 |
| MonthlyRetentionDaysOfTheMonth  | Text           | 每月保留期設定時的周數。  例如，First、Last |
| MonthlyRetentionDaysOfTheWeek   | Text           | 一週中所選用於每月保留期的日期              |
| MonthlyRetentionDuration        | Text           | 所設定備份的總保留期間 (以月為單位)    |
| MonthlyRetentionFormat          | Text           | 每月保留期的設定類型。 例如，每日，以天為基礎，以每週為基礎 |
| MonthlyRetentionTimes           | Text           | 每月保留期的設定日期和時間           |
| MonthlyRetentionWeeksOfTheMonth | Text           | 每月保留期設定時的周數。   例如，First、Last |
| PolicyName                      | Text           | 所定義原則的名稱                                   |
| PolicyUniqueId                  | Text           | 用來識別原則的唯一識別碼                             |
| PolicyTimeZone                  | Text           | 在記錄中指定原則時間欄位的時區 |
| RetentionDuration               | Text           | 所設定備份的保留期間                    |
| RetentionType                   | Text           | 保留類型                                            |
| schemaVersion                   | Text           | 此欄位代表目前的架構版本，其為 **V2** |
| State                           | Text           | 原則物件的目前狀態。 例如，使用中、已刪除 |
| SynchronisationFrequencyPerDay  | 整數   | 在一天內，SC DPM 和 MABS 的檔案備份同步處理的次數 |
| VaultUniqueId                   | Text           | 此原則所屬之保存庫的唯一識別碼          |
| WeeklyRetentionDaysOfTheWeek    | Text           | 一週中所選用於每週保留期的日期               |
| WeeklyRetentionDuration         | 十進位數字 | 設定備份的每週保留持續時間總計（以周為單位） |
| WeeklyRetentionTimes            | Text           | 每週保留期的設定日期和時間            |
| YearlyRetentionDaysOfTheMonth   | Text           | 一個月中所選用於每年保留期的日期             |
| YearlyRetentionDaysOfTheWeek    | Text           | 一週中所選用於每年保留期的日期               |
| YearlyRetentionDuration         | 十進位數字 | 所設定備份的總保留期間 (以年為單位)     |
| YearlyRetentionFormat           | Text           | 每年保留期的設定類型，例如，每天以天為基礎、以每週為基礎 |
| YearlyRetentionMonthsOfTheYear  | Text           | 一年中所選用於每年保留期的月數             |
| YearlyRetentionTimes            | Text           | 每年保留期的設定日期和時間            |
| YearlyRetentionWeeksOfTheMonth  | Text           | 選取每年保留的月中周數             |
| SourceSystem                    | Text           | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupStorage

下表提供儲存體相關欄位的詳細資料。

| **欄位**                      | **資料類型** | **說明**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 正在收集資料的資源識別碼。 例如，復原服務保存庫資源識別碼 |
| OperationName                  | Text          | 此欄位代表目前作業的名稱-儲存體或 StorageAssociation |
| 類別                       | Text          | 此欄位代表推送至 Azure 監視器記錄的診斷資料類別-AddonAzureBackupStorage |
| BackupItemUniqueId             | Text          | 唯一識別碼，用來識別使用 DPM、MABS 備份的 Vm 備份專案 |
| BackupManagementServerUniqueId | Text          | 用以唯一識別備份管理伺服器的欄位（如果適用的話） |
| BackupManagementType           | Text          | 執行備份作業的伺服器提供者類型。 例如，IaaSVM、Filefolder)  |
| PreferredWorkloadOnVolume      | Text          | 此磁片區是慣用儲存體的工作負載      |
| ProtectedContainerUniqueId     | Text          | 與備份專案相關聯之受保護容器的唯一識別碼 |
| schemaVersion                  | Text          | 架構的版本。 例如， **V2**                   |
| State                          | Text          | 備份專案物件的狀態。 例如，使用中、已刪除 |
| StorageAllocatedInMBs          | 數字        | 對應的備份專案在類型磁片的對應儲存體中所配置的儲存體大小 |
| StorageConsumedInMBs           | 數字        | 對應儲存體中對應的備份專案所耗用的儲存體大小 |
| StorageName                    | Text          | 儲存體實體的名稱。 例如，E:\                      |
| StorageTotalSizeInGBs          | Text          | 儲存體實體所耗用的儲存體大小總計 (以 GB 為單位)     |
| StorageType                    | Text          | 儲存體的類型，例如雲端、磁碟區、磁碟             |
| StorageUniqueId                | Text          | 用來識別儲存體實體的唯一識別碼                |
| VaultUniqueId                  | Text          | 用來識別與儲存體實體相關之保存庫的唯一識別碼 |
| VolumeFriendlyName             | Text          | 儲存體磁片區的易記名稱                          |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="valid-operation-names-for-each-table"></a>每個資料表的有效操作名稱

上述資料表中的每筆記錄都有相關聯的作業 **名稱**。 作業名稱會描述記錄 (的類型，也會指出資料表中的哪些欄位會針對該記錄) 填入。 每個資料表 (類別目錄) 支援一或多個相異的作業名稱。 以下是每個上述資料表所支援之作業名稱的摘要。

| **資料表名稱/類別目錄**                   | **支援的作業名稱** | **說明**              |
| ------------------------------------------- | ------------------------------|----------------------------- |
| CoreAzureBackup | BackupItem | 表示記錄，其中包含給定備份專案的所有詳細資料，例如識別碼、名稱、類型等等。 |
| CoreAzureBackup | BackupItemAssociation | 代表備份專案與其相關聯的受保護容器之間的對應 (如適用) 。 |
| CoreAzureBackup | BackupItemFrontEndSizeConsumption | 代表備份專案與其前端大小之間的對應。 |
| CoreAzureBackup | ProtectedContainer | 表示記錄，其中包含指定受保護容器的所有詳細資料，例如識別碼、名稱、類型等等。 |
| CoreAzureBackup | ProtectedContainerAssociation | 代表受保護的容器與用於其備份的保存庫之間的對應。 |
| CoreAzureBackup | 保存庫 | 表示記錄，其中包含指定之保存庫的所有詳細資料，例如 識別碼、名稱、標記、位置等。 |
| CoreAzureBackup | RecoveryPoint | 表示記錄，其中包含指定備份專案的最舊和最新復原點。 |
| AddonAzureBackupJobs | 工作 (Job) |  表示記錄，其中包含指定工作的所有詳細資料。 例如，[作業]、[開始時間]、[狀態] 等等。 |
| AddonAzureBackupAlerts | 警示 | 表示包含指定警示之所有詳細資料的記錄。 例如，警示建立時間、嚴重性、狀態等等。  |
| AddonAzureBackupStorage | 儲存體 | 表示記錄，其中包含給定儲存實體的所有詳細資料。 例如，[儲存體名稱]、[類型] 等等。 |
| AddonAzureBackupStorage | StorageAssociation | 代表備份專案與備份專案所耗用的雲端儲存體總數之間的對應。 |
| AddonAzureBackupProtectedInstance | ProtectedInstance | 表示記錄，其中包含每個容器或備份專案的受保護實例計數。 針對 Azure VM 備份，受保護的實例計數可在備份專案層級取得，而針對其他工作負載，則可在受保護的容器層級使用。 |
| AddonAzureBackupPolicy | 原則 |  表示記錄，其中包含備份和保留原則的所有詳細資料。 例如，識別碼、名稱、保留設定等等。 |
| AddonAzureBackupPolicy | PolicyAssociation | 代表備份專案與其套用的備份原則之間的對應。 |   

通常，您必須在不同的資料表以及屬於相同資料表的不同記錄集之間執行聯結， (由作業名稱) 區分，以取得分析所需的所有欄位。 請參閱 [範例查詢](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries) 以開始使用。 

## <a name="next-steps"></a>後續步驟

- [瞭解如何將診斷資料傳送至 Log Analytics](./backup-azure-diagnostic-events.md)
- [瞭解如何在資源特定資料表上撰寫查詢](./backup-azure-monitoring-use-azuremonitor.md#sample-kusto-queries)
