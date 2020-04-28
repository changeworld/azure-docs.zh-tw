---
title: 使用復原服務保存庫的診斷設定
description: 本文說明如何使用 Azure 備份的舊和新診斷事件。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 4efc00da96493c751c4a85dbdcc280d1ca0ef5ac
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82183699"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>使用復原服務保存庫的診斷設定

Azure 備份會傳送可收集的診斷事件，並用於分析、警示和報告。

您可以透過 [Azure 入口網站] 來設定復原服務保存庫的診斷設定，方法是前往保存庫並選取 [**診斷設定**]。 選取 [ **+ 新增診斷設定**] 可讓您將一或多個診斷事件傳送至儲存體帳戶、事件中樞或 Log Analytics 工作區。

![診斷設定窗格](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>適用于 Azure 備份使用者的診斷事件

Azure 備份提供下列診斷事件。 每個事件都會在一組特定的備份相關構件上提供詳細資料：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

如需詳細資訊，請參閱[Azure 備份診斷事件的資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)。

這些事件的資料可以傳送到儲存體帳戶、Log Analytics 工作區或事件中樞。 如果您要將此資料傳送至 Log Analytics 工作區，請選取 [**診斷設定**] 畫面上的 [**資源特定**] 切換。 如需詳細資訊，請參閱下列幾節。

## <a name="use-diagnostics-settings-with-log-analytics"></a>搭配使用診斷設定與 Log Analytics

您現在可以使用 Azure 備份，將保存庫診斷資料傳送至專用的 Log Analytics 資料表以進行備份。 這些資料表稱為[資源特定的資料表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

若要將您的保存庫診斷資料傳送至 Log Analytics：

1. 移至您的保存庫，然後選取 [**診斷設定**]。 選取 [ **+ 新增診斷設定**]。
1. 為 [診斷] 設定指定名稱。
1. 選取 [**傳送至 Log analytics** ] 核取方塊，然後選取 Log analytics 工作區。
1. 選取切換中的 [資源] [**特定**]，然後選取下列六個事件： **CoreAzureBackup**、 **AddonAzureBackupJobs**、 **AddonAzureBackupAlerts**、 **AddonAzureBackupPolicy**、 **AddonAzureBackupStorage**和**AddonAzureBackupProtectedInstance**。
1. 選取 [儲存]  。

   ![資源特定模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

將資料流程入 Log Analytics 工作區之後，就會在您的工作區中建立每個事件的專用資料表。 您可以直接查詢這些資料表中的任何一個。 如有需要，您也可以在這些資料表之間執行聯結或等位。

> [!IMPORTANT]
> 只有[備份報告](https://docs.microsoft.com/azure/backup/configure-reports)中的資源特定模式*才*支援六個事件，亦即 CoreAzureBackup、AddonAzureBackupJobs、AddonAzureBackupAlerts、AddonAzureBackupPolicy、AddonAzureBackupStorage 和 AddonAzureBackupProtectedInstance。 *如果您嘗試在 Azure 診斷模式中傳送這六個事件的資料，備份報表中就不會顯示任何資料。*

## <a name="legacy-event"></a>舊版事件

傳統上，保存庫的所有備份相關診斷資料都包含在名為 AzureBackupReport 的單一事件中。 這裡所述的六個事件基本上是 AzureBackupReport 中包含的所有資料分解。

目前，我們會繼續支援 AzureBackupReport 事件，以在使用者具有此事件的現有自訂查詢時，回溯相容性。 範例包括自訂記錄警示和自訂視覺效果。 *我們建議您儘快移至[新的事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)*。 新的事件：

* 讓資料更容易在記錄查詢中使用。
* 提供更好的架構探索和其結構。
* 提升內部內嵌延遲和查詢時間的效能。

*Azure 診斷模式中的舊版事件最終會被取代。選擇新的事件可能有助於您在日後避免複雜的遷移*。 使用 Log Analytics 的[報表解決方案](https://docs.microsoft.com/azure/backup/configure-reports)也會停止舊版事件的支援資料。

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>移至 Log Analytics 工作區的新診斷設定的步驟

1. 識別哪些保存庫正在使用舊版事件和其所屬的訂用帳戶，將資料傳送至 Log Analytics 工作區。 執行下列工作區以識別這些保存庫和訂用帳戶。

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

1. 使用 Azure 備份中的[內建 Azure 原則定義](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)，為指定範圍內的所有保存庫新增新的診斷設定。 此原則會將新的診斷設定新增至保存庫，其中可能沒有診斷設定或只有舊版的診斷設定。 此原則一次可以指派給整個訂用帳戶或資源群組。 您必須具有指派原則之每個訂用帳戶的擁有者存取權。

您可以針對 AzureBackupReport 和六個新事件選擇個別的診斷設定，直到您將所有自訂查詢遷移到使用新資料表中的資料為止。 下圖顯示具有兩個診斷設定的保存庫範例。 第一個設定（名為**Setting1**）會將 AzureBackupReport 事件的資料傳送至 Azure 診斷模式中的 Log Analytics 工作區。 第二個設定（名為**Setting2**）會將六個新 Azure 備份事件的資料，以資源特定模式傳送到 Log Analytics 工作區。

![兩個設定](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> 只有在 Azure 診斷模式下*才*支援 AzureBackupReport 事件。 *如果您嘗試在資源特定模式下傳送此事件的資料，則不會有任何資料流程向 Log Analytics 工作區。*

> [!NOTE]
> 只有當使用者選取 [**傳送至 Log Analytics**] 時，才會顯示**Azure 診斷**或**資源特定**的切換。 若要將資料傳送至儲存體帳戶或事件中樞，使用者可以選取所需的目的地，並選取任何所需事件的核取方塊，而不需要任何額外的輸入。 同樣地，我們建議您不要選擇未來的舊版事件 AzureBackupReport。

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>將 Azure Site Recovery 事件傳送至 Log Analytics

Azure 備份和 Azure Site Recovery 事件會從相同的復原服務保存庫傳送。 Azure Site Recovery 目前無法用於資源特定的資料表。 想要將 Azure Site Recovery 事件傳送至 Log Analytics 的使用者，會被導向為*僅*使用 Azure 診斷模式，如影像中所示。 *選擇 Azure Site Recovery 事件的資源特定模式，將會導致所需的資料不會傳送到 Log Analytics 工作區*。

![Site Recovery 事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

總結：

* 如果您已使用 Azure 診斷設定 Log Analytics 診斷，並在其上撰寫自訂查詢，請將該設定保持不*變*，直到您將查詢遷移到使用新事件的資料為止。
* 如果您也想要上架新的資料表（如我們所建議），請建立**新**的診斷設定，選取 [資源] [**特定**]，然後選取六個新的事件。
* 如果您目前正在將 Azure Site Recovery 事件傳送至 Log Analytics，*請勿*選擇這些事件的資源特定模式。 否則，這些事件的資料將不會傳送到您的 Log Analytics 工作區。 相反地，請建立額外的診斷設定，選取 [ **Azure 診斷**]，然後選取相關的 Azure Site Recovery 事件。

下圖顯示具有三個保存庫診斷設定之使用者的範例。 第一個設定（名為**Setting1**）會將 AzureBackupReport 事件的資料傳送至 Azure 診斷模式中的 Log Analytics 工作區。 第二個設定（名為**Setting2**）會從六個新的 Azure 備份事件，將資料傳送到 Log Analytics 工作區的資源特定模式。 第三個設定（名為**Setting3**）會將資料從 Azure Site Recovery 事件傳送至 Azure 診斷模式中的 Log Analytics 工作區。

![三個設定](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>後續步驟

[瞭解診斷事件的 Log Analytics 資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
