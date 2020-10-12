---
title: 使用 Azure 監視器監視 Azure 備份
description: 使用 Azure 監視器監視 Azure 備份工作負載並建立自訂警示。
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 3f5f663a2f0ed0f91cc414d352e975a2ff3b9649
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88827149"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>使用 Azure 監視器進行大規模監視

Azure 備份提供復原服務保存庫中的[內建監視和警示功能](backup-azure-monitoring-built-in-monitor.md)。 這些功能不需要任何額外的管理基礎結構即可供使用。 但在下列案例中，這項內建服務會受到限制：

- 如果您在訂用帳戶之間監視多個復原服務保存庫中的資料
- 如果慣用的通知通道*不是*電子郵件
- 如果使用者想要更多案例的警示
- 如果您想要從 Azure 中的內部部署元件 (例如 System Center Data Protection Manager) 中檢視資訊，但入口網站未顯示在[**備份作業**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)或[**備份警示**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>使用 Log Analytics 工作區

### <a name="create-alerts-by-using-log-analytics"></a>使用 Log Analytics 建立警示

在 Azure 監視器中，您可以在 Log Analytics 工作區建立自己的警示。 在工作區中，您可以使用 *Azure 動作群組*選取您慣用的通知機制。

> [!IMPORTANT]
> 如需建立此查詢成本的相關資訊，請參閱 [Azure 監視器價格](https://azure.microsoft.com/pricing/details/monitor/)。

開啟 Log Analytics 工作區的 [記錄] 區段，然後為您自己的記錄建立查詢。 在您選取 [新增警示規則] 時，Azure 監視器警示建立頁面會開啟，如下圖所示。

![在 Log Analytics 工作區中建立警示](media/backup-azure-monitoring-laworkspace/custom-alert.png)

這裡的資源已標示為 Log Analytics 工作區，並提供動作群組整合。

![Log Analytics 警示建立頁面](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>警示條件

警示的定義特性是它的觸發條件。 選取 [條件]，在 [記錄] 頁面上自動載入 Kusto 查詢，如下圖所示。 您可以在這裡編輯條件，以符合您的需求。 如需詳細資訊，請參閱 [範例 Kusto 查詢](#sample-kusto-queries)。

![設定警示條件](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

如果必要，您可以編輯 Kusto 查詢。 選擇閾值、期間和頻率。 閾值會決定何時會引發警示。 期間是執行查詢的時間範圍。 例如，如果臨界值大於 0，則期間為 5 分鐘，而頻率為 5 分鐘，則規則會每隔 5 分鐘執行一次查詢，並檢查前 5 分鐘。 如果結果數目大於 0，您就會收到所選動作群組的通知。

> [!NOTE]
> 若要每天執行一次警示規則，請在指定的一天內建立的所有事件/記錄檔中，將 [期間] 和 [頻率] 的值變更為1440，也就是24小時。

#### <a name="alert-action-groups"></a>警示動作群組

使用動作群組來指定通知通道。 若要檢視可用的通知機制，請在 [動作群組] 下，選取 [新建]。

![[新增動作群組] 視窗中的可用通知機制](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

您可以單獨滿足 Log Analytics 中的所有警示和監視需求，也可以使用 Log Analytics 補充內建通知。

如需詳細資訊，請參閱[使用 Azure 監視器建立、查看和記錄管理警示](../azure-monitor/platform/alerts-log.md)和[在 Azure 入口網站中建立和管理動作群組](../azure-monitor/platform/action-groups.md)。

### <a name="sample-kusto-queries"></a>範例 Kusto 查詢

預設圖形會提供您可以建立警示的基本案例有關的 Kusto 查詢。 您也可以修改查詢，以取得您想要收到警示的資料。 將下列範例 Kusto 查詢貼入 [記錄] 頁面，然後在查詢上建立警示：

- 所有成功的備份作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- 所有失敗的備份作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- 所有成功的 Azure VM 備份作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- 所有成功的 Azure 備份代理程式作業

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
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

- 每個備份項目使用的備份儲存體

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

保存庫中的診斷資料會抽出至 Log Analytics 工作區，並且有一些延遲。 每個事件都會在從復原服務保存庫推送之後的 *20 到 30 分鐘*抵達 Log Analytics 工作區。 以下是延遲的進一步詳細資料：

- 在所有解決方案中，備份服務的內建警示會在建立後立即推送。 因此，這些通常會在 20 到 30 分鐘後出現在 Log Analytics 工作區中。
- 在所有解決方案中，隨選備份作業和還原作業會在*完成*時立即推送。
- 對於 SQL 備份以外的所有解決方案，排程的備份作業會在*完成*時立即推送。
- 針對 SQL 備份，因為記錄備份可能每隔 15 分鐘發生一次，所以所有已完成的排程備份作業 (包括記錄) 的資訊每隔 6 小時會進行批次處理和推送。
- 在所有解決方案中，其他資訊 (例如，備份項目、原則、復原點、儲存體等等) 會*一天至少一次*推送。
- 備份設定的變更 (例如變更原則或編輯原則) 會觸發所有相關備份資訊的推送。

## <a name="using-the-recovery-services-vaults-activity-logs"></a>使用復原服務保存庫的活動記錄

> [!CAUTION]
> 下列步驟僅適用於 *Azure VM 備份*。 您無法將這些步驟用於 Azure 備份代理程式、Azure 中的 SQL 備份或 Azure 檔案儲存體等解決方案。

您也可以使用活動記錄來取得事件通知 (例如備份成功)。 若要開始，請依照下列步驟進行：

1. 登入 Azure 入口網站。
2. 開啟相關的復原服務保存庫。
3. 在保存庫的 [屬性] 中，開啟 [活動記錄] 區段。

若要識別適當的記錄並建立警示：

1. 套用下圖中顯示的篩選，以確認您是否正在接收成功備份的活動記錄。 視需要變更**時間範圍**值以檢視記錄。

   ![篩選以尋找 Azure VM 備份的活動記錄](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. 選取作業名稱以檢視相關的詳細資料。
3. 選取 [新增警示規則] 開啟 [建立規則] 頁面。
4. 遵循[使用 Azure 監視器建立、查看和管理活動記錄警示](../azure-monitor/platform/alerts-activity-log.md)中的步驟建立警示。

   ![新增警示規則](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

這裡的資源是復原服務保存庫本身。 針對您想要透過活動記錄收到通知的所有保存庫，重複相同的步驟。 條件不會有閾值、期間或頻率，因為此警示是以事件為基礎。 一旦產生相關的活動記錄檔，就會引發警示。

## <a name="using-log-analytics-to-monitor-at-scale"></a>使用 Log Analytics 大規模監視

您可以在 Azure 監視器中，從活動記錄和 Log Analytics 工作區中檢視建立的所有警示。 只要開啟左側的 [警示] 窗格即可。

雖然您可以透過活動記錄取得通知，但我們強烈建議使用 Log Analytics 而非活動記錄大規模進行監視。 原因如下：

- **有限的案例**：活動記錄的通知僅適用於 Azure VM 備份。 必須為每個復原服務保存庫設定通知。
- **定義符合**：排程的備份活動不符合最新的活動記錄定義。 相反地，它會與[資源記錄檔](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)一致。 當流經活動記錄通道的資料變更時，這種對齊方式會導致非預期的效果。
- **活動記錄通道的問題**：在復原服務保存庫中，從 Azure 備份抽出的活動記錄會遵循新的模型。 可惜的是，這項變更會影響 Azure Government、Azure 德國和 Azure China 21Vianet 的活動記錄產生。 如果這些雲端服務的使用者在 Azure 監視器中，從活動記錄建立或設定任何警示，則不會觸發警示。 此外，在所有 Azure 公用區域中，如果使用者[要將復原服務活動記錄收集到 Log Analytics 工作區](../azure-monitor/platform/activity-log.md)，則這些記錄也不會出現。

針對 Azure 備份保護的所有工作負載，使用 Log Analytics 工作區進行大規模監視和警示。

## <a name="next-steps"></a>後續步驟

若要建立自訂查詢，請參閱 [Log Analytics 資料模型](backup-azure-reports-data-model.md)。
