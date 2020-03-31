---
title: Azure 備份診斷事件的資料模型
description: 此資料模型參考了將診斷事件發送到日誌分析 （LA） 的資源特定模式。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583379"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Azure 備份診斷事件的資料模型

## <a name="coreazurebackup"></a>核心Azure備份

此表提供有關核心備份實體（如保存庫和備份項）的資訊。

| **領域**                         | **資料類型** | **描述**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Text          | 正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼。 |
| OperationName                     | Text          | 此欄位表示當前操作的名稱 - 備份專案、備份專案關聯或受保護容器。 |
| 類別                          | Text          | 此欄位表示推送到 Azure 監視器日誌的診斷資料類別。 例如，核心 Azure 備份。 |
| 代理版本                      | Text          | 代理備份或保護代理的版本號（在 SC DPM 和 MABS 的情況下） |
| AzureBackupAgentVersion           | Text          | 備份管理伺服器上的 Azure 備份代理版本 |
| AzureDataCenter                   | Text          | 保存庫所在的資料中心                       |
| 備份專案應用版本              | Text          | 備份項的應用程式版本                       |
| BackupItemFriendlyName            | Text          | 備份項的易記名稱                             |
| BackupItemName                    | Text          | 備份項的名稱                                      |
| 備份專案保護狀態         | Text          | 備份項的保護狀態                          |
| 備份專案前端大小            | Text          | 備份項的前端大小                            |
| BackupItemType                    | Text          | 備份項的類型。 例如：VM、檔資料夾             |
| 備份專案唯一 ID                | Text          | 備份項目的唯一識別碼                         |
| 備份管理伺服器類型        | Text          | 備份管理伺服器的類型，如 MABS、SC DPM     |
| 備份管理伺服器唯一 ID    | Text          | 欄位以唯一標識備份管理伺服器      |
| BackupManagementType              | Text          | 伺服器執行備份作業的提供程式類型。 例如，IaaSVM，檔資料夾 |
| 備份管理伺服器名稱        | Text          | 備份管理伺服器的名稱                         |
| 備份管理伺服器作業系統版本   | Text          | 備份管理伺服器的作業系統版本                   |
| 備份管理伺服器版本     | Text          | 備份管理伺服器的版本                      |
| 最新復原點位置       | Text          | 備份專案的最新復原點的位置    |
| 最新復原點時間           | Datetime      | 備份專案的最新復原點的日期時間   |
| 最老的復原點位置       | Text          | 備份項最舊復原點的位置    |
| 最老的復原點時間           | Datetime      | 備份專案的最新復原點的日期時間   |
| PolicyUniqueId                    | Text          | 標識策略的唯一 ID                             |
| 受保護的容器易記名稱    | Text          | 受保護伺服器的易記名稱                        |
| 受保護的容器位置        | Text          | 受保護容器是位於本地還是位於 Azure 中 |
| 受保護的容器名稱            | Text          | 受保護容器的名稱                            |
| 受保護容器類型          | Text          | 受保護容器的作業系統類型                          |
| 受保護容器作業系統版本       | Text          | 受保護容器的作業系統版本                        |
| 受保護的容器保護狀態 | Text          | 受保護容器的保護狀態                  |
| 受保護容器類型            | Text          | 受保護容器是伺服器還是容器  |
| 受保護的容器唯一 ID        | Text          | 用於標識使用 DPM MABS 備份的 VM 以外的所有內容的受保護容器的唯一 ID |
| 受保護的容器工作負載類型    | Text          | 已備份的受保護容器的類型。 例如，IaaSVM 容器 |
| ProtectionGroupName               | Text          | 備份專案受保護組的名稱，適用于 SC DPM 和 MABS（如果適用） |
| resourceGroupName                 | Text          | 要收集的資料的資源組（例如，恢復服務保存庫） |
| schemaVersion                     | Text          | 此欄位表示架構的當前版本，它是**V2** |
| 輔助備份保護狀態    | Text          | 是否為備份項啟用了次要保護  |
| State                             | Text          | 備份項物件的狀態。 例如，活動，已刪除 |
| StorageReplicationType            | Text          | 保存庫的存儲複製類型。 例如，地理冗余 |
| SubscriptionId                    | Text          | 為其收集資料的資源（例如，恢復服務保存庫）的訂閱識別碼 |
| VaultName                         | Text          | 保存庫名稱                                            |
| VaultTags                         | Text          | 與保存庫資源關聯的標記                    |
| 庫庫唯一Id                     | Text          | 保存庫的唯一識別碼                             |
| SourceSystem                      | Text          | 當前資料的源系統 - Azure                  |

## <a name="addonazurebackupalerts"></a>載入項 Azure 備份警報

下表提供警示相關欄位的詳細資料。

| **領域**                      | **資料類型** | **描述**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 要收集其相關資料的資源所具備的唯一識別碼。 例如，恢復服務保存庫資源識別碼 |
| OperationName                  | Text          | 當前操作的名稱。 例如，警報            |
| 類別                       | Text          | 推送到 Azure 監視器日誌的診斷資料類別 - 附加 Azure 備份警報 |
| 警報代碼                      | Text          | 唯一標識警報類型的代碼                     |
| 警報整合狀態       | Text          | 確定警報是否為合併警報         |
| 警報發生日期時間        | Datetime      | 創建警報的日期和時間                     |
| 警報提示                  | Text          | 警報在                        |
| AlertSeverity                  | Text          | 警示的嚴重性。 例如，嚴重                 |
| AlertStatus                    | Text          | 警報的狀態。 例如，活動                     |
| 警報時間到解決分鐘數    | Number        | 解決警報所佔用的時間。 活動警報為空。     |
| AlertType                      | Text          | 警報類型。 例如，備份                           |
| AlertUniqueId                  | Text          | 生成的警報的唯一識別碼                    |
| 備份專案唯一 ID             | Text          | 與警報關聯的備份項的唯一識別碼 |
| 備份管理伺服器唯一 ID | Text          | 欄位，以唯一標識備份專案通過保護的備份管理伺服器（如果適用） |
| BackupManagementType           | Text          | 伺服器執行備份作業的提供程式類型，例如 IaaSVM、檔資料夾 |
| 警報計數合併      | Number        | 如果是合併警報的合併警報數  |
| 受保護的容器唯一 ID     | Text          | 與警報關聯的受保護伺服器的唯一識別碼 |
| 推薦行動              | Text          | 建議執行解決警報的操作                      |
| schemaVersion                  | Text          | 架構的當前版本，例如**V2**            |
| State                          | Text          | 警示物件的目前狀態 (例如，作用中、已刪除) |
| 存儲唯一 ID                | Text          | 用於標識存儲實體的唯一 ID                |
| 庫庫唯一Id                  | Text          | 用於標識與警報相關的保存庫的唯一 ID    |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>添加 Azure 備份保護實例

此表提供與實例相關的基本受保護欄位。

| **領域**                      | **資料類型** | **描述**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 收集資料的資源的唯一識別碼。 例如，恢復服務保存庫資源識別碼 |
| OperationName                  | Text          | 操作的名稱，例如受保護實例         |
| 類別                       | Text          | 推送到 Azure 監視器日誌的診斷資料類別 - AddonAzure 備份保護實例 |
| 備份專案唯一 ID             | Text          | 備份項的唯一 ID                                 |
| 備份管理伺服器唯一 ID | Text          | 欄位，以唯一標識備份專案通過保護的備份管理伺服器（如果適用） |
| BackupManagementType           | Text          | 伺服器執行備份作業的提供程式類型，例如 IaaSVM、檔資料夾 |
| 受保護的容器唯一 ID     | Text          | 標識作業上運行的受保護容器的唯一 ID |
| 受保護實例計數         | Text          | 該日期時間關聯備份項或受保護容器的受保護實例計數 |
| schemaVersion                  | Text          | 架構的當前版本，例如**V2**            |
| State                          | Text          | 備份項物件的狀態，例如"活動"、已刪除 |
| 庫庫唯一Id                  | Text          | 與受保護實例關聯的受保護保存庫的唯一識別碼 |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupjobs"></a>載入項 Azure 備份作業

下表提供作業相關欄位的詳細資料。

| **領域**                      | **資料類型** | **描述**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| OperationName                  | Text          | 此欄位代表目前作業的名稱 - Job    |
| 類別                       | Text          | 此欄位表示推送到 Azure 監視器日誌的診斷資料類別 - AddonAzure 備份作業 |
| 臨時計畫作業            | Text          | 要指定作業是臨時作業還是"計畫"的欄位           |
| 備份專案唯一 ID             | Text          | 用於標識與存儲實體相關的備份項的唯一 ID |
| 備份管理伺服器唯一 ID | Text          | 用於標識與存儲實體相關的備份管理伺服器的唯一 ID |
| BackupManagementType           | Text          | 用於執行備份的提供程式類型，例如，此警報所屬的 IaaSVM、檔資料夾 |
| 資料傳輸INMB            | Number        | 此工作的資料轉送 (以 MB 為單位)                          |
| 作業持續時間              | Number        | 總作業持續時間 (以秒為單位)                                |
| JobFailureCode                 | Text          | 由於發生作業失敗而產生的失敗碼字串    |
| JobOperation                   | Text          | 運行作業的操作，例如，備份、還原、配置備份 |
| 作業操作子類型            | Text          | 作業操作的子類型。 例如，在記錄備份作業中，"日誌" |
| 作業開始日期時間               | Datetime      | 開始執行作業的日期和時間                       |
| JobStatus                      | Text          | 已完成之作業的狀態 (例如，已完成、失敗)   |
| JobUniqueId                    | Text          | 標識作業的唯一 ID                                |
| 受保護的容器唯一 ID     | Text          | 與警報關聯的受保護伺服器的唯一識別碼 |
| 恢復作業目標         | Text          | 恢復作業的目標，其中恢復資料   |
| 恢復作業時間          | Datetime      | 創建正在恢復的復原點的日期、時間 |
| 恢復作業位置            | Text          | 正在恢復的復原點存儲的位置 |
| 恢復位置類型           | Text          | 恢復位置的類型                                |
| schemaVersion                  | Text          | 架構的當前版本，例如**V2**            |
| State                          | Text          | 警報物件的目前狀態，例如"活動"、已刪除 |
| 庫庫唯一Id                  | Text          | 與警報關聯的受保護保存庫的唯一識別碼 |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackuppolicy"></a>附加 Azure 備份策略

下表提供原則相關欄位的詳細資料。

| **領域**                       | **資料類型**  | **描述**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Text           | 收集資料的資源的唯一識別碼。 例如，恢復服務保存庫資源識別碼 |
| OperationName                   | Text           | 操作的名稱，例如策略或策略關聯 |
| 類別                        | Text           | 推送到 Azure 監視器日誌的診斷資料類別 - 附加 Azure 備份策略 |
| BackupDaysOfTheWeek             | Text           | 一週中已排定要備份的日期            |
| BackupFrequency                 | Text           | 運行備份的頻率。 例如，每日、每週 |
| BackupManagementType            | Text           | 伺服器執行備份作業的提供程式類型。 例如，IaaSVM，檔資料夾 |
| 備份管理伺服器唯一 ID  | Text           | 欄位，以唯一標識備份專案通過保護的備份管理伺服器（如果適用） |
| BackupTimes                     | Text           | 排定要備份的日期和時間                     |
| DailyRetentionDuration          | 整數   | 所設定備份的總保留期間 (以天為單位)      |
| DailyRetentionTimes             | Text           | 每日保留期的設定日期和時間            |
| 本周的"差異備份日"         | Text           | Azure VM 備份中 SQL 差異備份的一周天數 |
| DiffBackup格式                | Text           | Azure VM 備份中 SQL 差異備份的格式   |
| 差異備份保留時間     | 十進位數字 | Azure VM 備份中 SQL 差異備份的保留持續時間 |
| 差異備份時間                  | Time           | Azure VM 備份中 SQL 的差異備份時間     |
| 記錄備份頻率              | 十進位數字 | SQL 記錄備份的頻率                            |
| 記錄備份保留持續時間      | 十進位數字 | Azure VM 備份中 SQL 的記錄備份保留期 |
| MonthlyRetentionDaysOfTheMonth  | Text           | 配置每月保留的月份中的周。  例如，第一、最後等。 |
| MonthlyRetentionDaysOfTheWeek   | Text           | 一週中所選用於每月保留期的日期              |
| MonthlyRetentionDuration        | Text           | 所設定備份的總保留期間 (以月為單位)    |
| MonthlyRetentionFormat          | Text           | 用於每月保留的配置類型。 例如，每天基於一天，每週基於一周 |
| MonthlyRetentionTimes           | Text           | 每月保留期的設定日期和時間           |
| MonthlyRetentionWeeksOfTheMonth | Text           | 配置每月保留的月份中的周。   例如，第一、最後等。 |
| PolicyName                      | Text           | 所定義原則的名稱                                   |
| PolicyUniqueId                  | Text           | 標識策略的唯一 ID                             |
| 策略時區                  | Text           | 在日誌中指定策略時間欄位的時區 |
| 保留期               | Text           | 所設定備份的保留期間                    |
| RetentionType                   | Text           | 保留類型                                            |
| schemaVersion                   | Text           | 此欄位表示架構的當前版本，它是**V2** |
| State                           | Text           | 策略物件的目前狀態。 例如，活動，已刪除 |
| 同步頻率  | 整數   | 為 SC DPM 和 MABS 同步檔案備份的一天次數 |
| 庫庫唯一Id                   | Text           | 此策略所屬的保存庫的唯一 ID          |
| WeeklyRetentionDaysOfTheWeek    | Text           | 一週中所選用於每週保留期的日期               |
| WeeklyRetentionDuration         | 十進位數字 | 配置備份的每週總保留時間（周數） |
| WeeklyRetentionTimes            | Text           | 每週保留期的設定日期和時間            |
| YearlyRetentionDaysOfTheMonth   | Text           | 一個月中所選用於每年保留期的日期             |
| YearlyRetentionDaysOfTheWeek    | Text           | 一週中所選用於每年保留期的日期               |
| YearlyRetentionDuration         | 十進位數字 | 所設定備份的總保留期間 (以年為單位)     |
| YearlyRetentionFormat           | Text           | 年度保留的配置類型，例如，基於一天的每日配置類型，每週基於周的配置類型 |
| YearlyRetentionMonthsOfTheYear  | Text           | 一年中所選用於每年保留期的月數             |
| YearlyRetentionTimes            | Text           | 每年保留期的設定日期和時間            |
| YearlyRetentionWeeksOfTheMonth  | Text           | 為年度保留選擇的月份數             |
| SourceSystem                    | Text           | 目前資料的來源系統 - Azure                    |

## <a name="addonazurebackupstorage"></a>附加 Azure 備份存儲

下表提供儲存體相關欄位的詳細資料。

| **領域**                      | **資料類型** | **描述**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Text          | 正在收集資料的資源識別碼。 例如，恢復服務保存庫資源識別碼 |
| OperationName                  | Text          | 此欄位表示當前操作的名稱 - 存儲或存儲關聯 |
| 類別                       | Text          | 此欄位表示推送到 Azure 監視器日誌 - AddonAzure 備份存儲的診斷資料類別 |
| 備份專案唯一 ID             | Text          | 使用 DPM MABS 標識備份的 VM 備份專案的唯一 ID |
| 備份管理伺服器唯一 ID | Text          | 欄位，以唯一標識備份專案通過保護的備份管理伺服器（如果適用） |
| BackupManagementType           | Text          | 伺服器執行備份作業的提供程式類型。 例如，IaaSVM，檔資料夾 |
| 首選工作負載      | Text          | 此卷是首選存儲的工作負載      |
| 受保護的容器唯一 ID     | Text          | 與警報關聯的受保護伺服器的唯一識別碼 |
| schemaVersion                  | Text          | 架構的版本。 例如 **，V2**                   |
| State                          | Text          | 備份項物件的狀態。 例如，活動，已刪除 |
| 存儲分配在MB中          | Number        | 在類型磁片的相應存儲中由相應備份項分配的存儲大小 |
| 存儲在MB中消耗           | Number        | 相應存儲中相應備份項消耗的存儲大小 |
| StorageName                    | Text          | 存儲實體的名稱。 例如，E：*                      |
| 存儲總尺寸          | Text          | 存儲實體使用的存儲總大小（以 GB）表示     |
| StorageType                    | Text          | 存儲類型，例如雲、卷、磁片             |
| 存儲唯一 ID                | Text          | 用於標識存儲實體的唯一 ID                |
| 庫庫唯一Id                  | Text          | 用於標識與存儲實體相關的保存庫的唯一 ID |
| 卷易記名稱             | Text          | 存儲卷的易記名稱                          |
| SourceSystem                   | Text          | 目前資料的來源系統 - Azure                    |

## <a name="next-steps"></a>後續步驟

- [瞭解如何將診斷資料發送到日誌分析](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [瞭解如何在資源特定表上寫入查詢](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)