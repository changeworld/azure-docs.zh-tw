---
title: 使用 Azure 監視器監視 Azure 備份
description: 監視 Azure 備份工作負荷，並使用 Azure 監視器創建自訂警報。
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 547cef66be9902468f4e2755c31e5f586eccad5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459509"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure 監視器進行大規模監視

Azure 備份提供復原服務保存庫中的[內建監視和警示功能](backup-azure-monitoring-built-in-monitor.md)。 這些功能不需要任何額外的管理基礎結構即可供使用。 但是，在以下情況下，這種內置服務是有限的：

- 如果監視來自多個恢復服務保存庫的資料（跨訂閱）
- 如果首選通知通道*不是*電子郵件
- 如果使用者希望警報更多方案
- 如果要查看來自本機群組件（如 Azure 中的系統中心資料保護管理器）的資訊，門戶不會在[**備份作業**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或[**備份警報中**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)顯示這些資訊

## <a name="using-log-analytics-workspace"></a>使用日誌分析工作區

### <a name="create-alerts-by-using-log-analytics"></a>使用日誌分析創建警報

在 Azure 監視器中，可以在日誌分析工作區中創建自己的警報。 在工作區中，使用*Azure 操作組*選擇首選通知機制。

> [!IMPORTANT]
> 有關創建此查詢的成本的資訊，請參閱 Azure[監視器定價](https://azure.microsoft.com/pricing/details/monitor/)。

打開日誌分析工作區的**日誌**部分，並為您自己的日誌創建查詢。 選擇 **"新警報規則**"時，將打開 Azure 監視器警報創建頁，如下圖所示。

![在日誌分析工作區中創建警報](media/backup-azure-monitoring-laworkspace/custom-alert.png)

此處，資源已標記為日誌分析工作區，並提供操作組集成。

![日誌分析警報創建頁面](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警示準則

警報的定義特徵是其觸發條件。 選擇 **"條件**"可在 **"日誌"** 頁上自動載入 Kusto 查詢，如下圖所示。 在這裡，您可以編輯條件以滿足您的需要。 有關詳細資訊，請參閱示例[Kusto 查詢](#sample-kusto-queries)。

![設置警示準則](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如有必要，可以編輯 Kusto 查詢。 選擇閾值、週期和頻率。 閾值確定何時引發警報。 期間是執行查詢的時間視窗。 例如，如果閾值大於 0，則週期為 5 分鐘，頻率為 5 分鐘，則規則每 5 分鐘運行一次查詢，查看前 5 分鐘。 如果結果數大於 0，則通過選定的操作組通知您。

#### <a name="alert-action-groups"></a>警報操作組

使用操作組指定通知通道。 要查看可用的通知機制，請在 **"操作組"** 下，選擇 **"創建新**"。

!["添加操作組"視窗中的可用通知機制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

您可以僅滿足日誌分析中的所有警報和監視要求，也可以使用日誌分析來補充內置通知。

有關詳細資訊，請參閱使用[Azure 監視器創建、查看和管理日誌警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)，並在 Azure[門戶中創建和管理操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。

### <a name="sample-kusto-queries"></a>庫斯托查詢示例

預設圖形為您提供庫sto查詢的基本方案，您可以在其中生成警報。 您還可以修改查詢以獲取有關要收到警報的資料。 在 **"日誌"** 頁中粘貼以下示例 Kusto 查詢，然後在查詢上創建警報：

- 所有成功的備份作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    ````

- 所有失敗的備份作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Failed"
    ````

- 所有成功的 Azure VM 備份作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 所有成功的 SQL 記錄備份作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 所有成功的 Azure 備份代理作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- 每個備份專案消耗的備份存儲

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId 
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs 
    | sort by StorageConsumedInMBs desc
    ````


### <a name="diagnostic-data-update-frequency"></a>診斷資料更新頻率

從保存庫的診斷資料被泵送到日誌分析工作區，並帶有一些滯後性。 每個事件在從恢復服務保存庫推送*20 到 30 分鐘後*到達日誌分析工作區。 以下是有關延遲的進一步詳細資訊：

- 在所有解決方案中，備份服務的內置警報在創建後立即推送。 因此，它們通常在 20 到 30 分鐘後顯示在日誌分析工作區中。
- 在所有解決方案中，按需備份作業和恢復作業一*旦完成*，就會推送。
- 對於除 SQL 備份之外的所有解決方案，計畫備份作業在完成後立即推送 *。*
- 對於 SQL 備份，由於記錄備份可以每 15 分鐘進行一次，因此每 6 小時批次處理並推送所有已完成的計畫備份作業（包括日誌）的資訊。
- 在所有解決方案中，其他資訊（如備份項、策略、復原點、存儲等）每天至少推送*一次。*
- 備份配置的更改（如更改策略或編輯策略）會觸發所有相關備份資訊的推送。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用恢復服務保存庫的活動日誌

> [!CAUTION]
> 以下步驟僅適用于*Azure VM 備份。* 不能將這些步驟用於解決方案，如 Azure 備份代理、Azure 中的 SQL 備份或 Azure 檔。

您還可以使用活動日誌來獲取有關備份成功等事件的通知。 首先，請按照以下步驟操作：

1. 登入 Azure 入口網站。
1. 打開相關的恢復服務保存庫。
1. 在保存庫的屬性中，打開**活動日誌**部分。

要標識適當的日誌並創建警報，請進行以下工作：

1. 通過應用下圖中顯示的篩選器，驗證您正在接收成功備份的活動日誌。 根據需要更改**時間跨度**值以查看記錄。

   ![篩選以查找 Azure VM 備份的活動日誌](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. 選擇操作名稱以查看相關詳細資訊。
1. 選擇 **"新建警報規則**"以打開 **"創建規則"** 頁。
1. 使用 Azure 監視器，按照[創建、查看和管理活動日誌警報](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)中的步驟創建警報。

   ![新增警示規則](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

此處的資源是恢復服務保存庫本身。 對要通過活動日誌通知的所有保存庫重複相同的步驟。 條件沒有閾值、週期或頻率，因為此警報基於事件。 生成相關活動日誌後，將立即發出警報。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用日誌分析進行大規模監視

您可以查看 Azure 監視器中活動日誌和日誌分析工作區創建的所有警報。 只需打開左側的 **"警報"** 窗格。

儘管您可以通過活動日誌接收通知，但我們強烈建議使用日誌分析而不是活動日誌進行大規模監視。 原因如下：

- **有限方案**：通過活動日誌的通知僅適用于 Azure VM 備份。 必須為每個恢復服務保存庫設置通知。
- **定義擬合**：計畫的備份活動與活動日誌的最新定義不相符。 相反，它與[資源日誌](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-platform-logs-in-a-workspace)對齊。 當流經活動日誌通道的資料發生更改時，此對齊會導致意外影響。
- **活動日誌通道的問題**：在恢復服務保存庫中，從 Azure 備份中泵送的活動日誌遵循新模型。 遺憾的是，此更改會影響 Azure 政府、Azure 德國和 Azure 中國 21Vianet 中活動日誌的生成。 如果這些雲服務的使用者從 Azure 監視器中的活動日誌創建或配置任何警報，則不會觸發警報。 此外，在所有 Azure 公共區域中，如果使用者[將恢復服務活動日誌收集到日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs)中，則這些日誌不會顯示。

使用日誌分析工作區對受 Azure 備份保護的所有工作負荷進行大規模監視和警報。

## <a name="next-steps"></a>後續步驟

要創建自訂查詢，請參閱[日誌分析資料模型](backup-azure-reports-data-model.md)。
