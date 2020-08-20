---
title: 使用復原服務保存庫的診斷設定
description: 本文描述如何針對 Azure 備份使用舊的和新的診斷事件。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 2b562ee7fc4afbc28119aa36cfa071291dd61f12
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612617"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>使用復原服務保存庫的診斷設定

Azure 備份傳送可針對分析、警示和報告用途收集和使用的診斷事件。

您可以透過 Azure 入口網站設定復原服務保存庫的診斷設定，方法是前往保存庫，然後選取 [ **診斷設定**]。 選取 [ **+ 新增診斷設定** ] 可讓您將一或多個診斷事件傳送至儲存體帳戶、事件中樞或 Log Analytics 工作區。

![診斷設定窗格](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure 備份使用者可以使用的診斷事件

Azure 備份提供下列診斷事件。 每個事件都會提供一組特定備份相關構件的詳細資料：

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

如果您使用的是 [舊版事件](#legacy-event) AzureBackupReport，建議您儘早切換為使用上述事件。

如需詳細資訊，請參閱 [Azure 備份診斷事件的資料模型](./backup-azure-reports-data-model.md)。

這些事件的資料可以傳送至儲存體帳戶、Log Analytics 工作區或事件中樞。 如果您要將此資料傳送至 Log Analytics 工作區，請選取 [**診斷設定**] 畫面上的**資源特定**切換。 如需詳細資訊，請參閱下列幾節。

## <a name="use-diagnostics-settings-with-log-analytics"></a>使用 Log Analytics 的診斷設定

您現在可以使用 Azure 備份將保存庫診斷資料傳送到專用的 Log Analytics 資料表進行備份。 這些資料表稱為 [資源特定的資料表](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace)。

若要將您的保存庫診斷資料傳送至 Log Analytics：

1. 移至您的保存庫，然後選取 [ **診斷設定**]。 選取 [ **+ 新增診斷設定**]。
1. 提供診斷設定的名稱。
1. 選取 [ **傳送至 Log analytics** ] 核取方塊，然後選取 Log analytics 工作區。
1. 選取切換中 **特定的資源** ，然後選取下列六個事件： **CoreAzureBackup**、 **AddonAzureBackupJobs**、 **AddonAzureBackupAlerts**、 **AddonAzureBackupPolicy**、 **AddonAzureBackupStorage**和 **AddonAzureBackupProtectedInstance**。
1. 選取 [儲存]。

   ![資源特定模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

資料流程入 Log Analytics 工作區之後，每個事件的專用資料表都會建立在您的工作區中。 您可以直接查詢這些資料表中的任何一個。 您也可以視需要在這些資料表之間執行聯結或等位。

> [!IMPORTANT]
> CoreAzureBackup、AddonAzureBackupJobs、AddonAzureBackupAlerts、AddonAzureBackupPolicy、AddonAzureBackupStorage 和 AddonAzureBackupProtectedInstance 這六個事件 *僅* 在 [備份報告](./configure-reports.md)中的資源特定模式下受到支援。 *如果您嘗試在 Azure 診斷模式下傳送這六個事件的資料，備份報表中將不會顯示任何資料。*

## <a name="legacy-event"></a>舊版事件

傳統上，保存庫的所有備份相關診斷資料都包含在名為 AzureBackupReport 的單一事件中。 此處所述的六個事件其實是 AzureBackupReport 中包含的所有資料的分解。

目前，在使用者對此事件具有現有自訂查詢的情況下，我們會繼續支援 AzureBackupReport 事件以提供回溯相容性。 範例包括自訂記錄警示和自訂視覺效果。 *建議您儘早移至新的[事件](#diagnostics-events-available-for-azure-backup-users)*。 新的事件：

* 讓資料更容易在記錄查詢中使用。
* 提供架構及其結構的更佳探索能力。
* 改善內嵌延遲和查詢時間之間的效能。

*Azure 診斷模式中的舊版事件最終將會被取代。選擇新的事件可能有助於避免在日後進行複雜的遷移*。 使用 Log Analytics 的 [報告解決方案](./configure-reports.md) 也會停止支援來自舊版事件的資料。

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>移至 Log Analytics 工作區的新診斷設定的步驟

1. 使用舊版事件及其所屬的訂用帳戶，識別哪些保存庫正在將資料傳送至 Log Analytics 工作區。 在每個工作區中執行下列查詢，以識別這些保存庫和訂閱。

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
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
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

    以下是在其中一個工作區中執行查詢的螢幕擷取畫面：

    ![工作區查詢](./media/backup-azure-diagnostics-events/workspace-query.png)

2. 使用 Azure 備份中內 [建的 Azure 原則定義](./azure-policy-configure-diagnostics.md) ，為指定範圍內的所有保存庫新增診斷設定。 此原則會將新的診斷設定新增至保存庫，其中可能沒有診斷設定或只有舊版診斷設定。 您可以一次將此原則指派給整個訂用帳戶或資源群組。 您必須擁有指派原則之每個訂用帳戶的擁有者存取權。

您可以針對 AzureBackupReport 和六個新的事件選擇個別的診斷設定，直到您將所有自訂查詢遷移為使用新資料表中的資料為止。 下圖顯示具有兩個診斷設定的保存庫範例。 第一個設定（名為 **Setting1**）會將 AzureBackupReport 事件的資料傳送至 Azure 診斷模式中的 Log Analytics 工作區。 第二個設定（名為 **Setting2**）會將六個新 Azure 備份事件的資料傳送到資源特定模式中的 Log Analytics 工作區。

![兩個設定](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> 只有在 Azure 診斷模式下 *才* 支援 AzureBackupReport 事件。 *如果您嘗試在資源特定模式下傳送此事件的資料，則不會有任何資料流程向 Log Analytics 工作區。*

> [!NOTE]
> 只有當使用者選取 [**傳送至 Log Analytics**] 時，才會顯示**Azure 診斷**或**特定資源**的切換。 若要將資料傳送至儲存體帳戶或事件中樞，使用者可以選取所需的目的地，然後選取任何所需事件的核取方塊，而不需要任何額外的輸入。 同樣地，建議您不要選擇舊版事件 AzureBackupReport 繼續進行。

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>將 Azure Site Recovery 事件傳送至 Log Analytics

系統會從相同的復原服務保存庫傳送 Azure 備份和 Azure Site Recovery 事件。 資源特定資料表目前無法使用 Azure Site Recovery。 想要將 Azure Site Recovery 事件傳送至 Log Analytics 的使用者， *只*會導向使用 Azure 診斷模式，如下圖所示。 *選擇 Azure Site Recovery 事件的資源特定模式，會導致無法將所需的資料傳送至 Log Analytics 工作區*。

![Site Recovery 事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

總括來說：

* 如果您已經使用 Azure 診斷設定 Log Analytics 診斷，並且在其上撰寫自訂查詢，請將該設定保持不 *變* ，直到您遷移查詢來使用新事件中的資料為止。
* 如果您也想要上架到新的資料表，我們建議您建立 **新** 的診斷設定，選取 [ **特定資源**]，然後選取六個新的事件。
* 如果您目前正在將 Azure Site Recovery 事件傳送至 Log Analytics， *請勿* 選擇這些事件的資源特定模式。 否則，這些事件的資料將不會流入您的 Log Analytics 工作區。 相反地，請建立額外的診斷設定、選取 [ **Azure 診斷**]，然後選取相關的 Azure Site Recovery 事件。

下圖顯示有三個保存庫診斷設定的使用者範例。 第一個設定（名為 **Setting1**）會將資料從 AzureBackupReport 事件傳送至 Azure 診斷模式中的 Log Analytics 工作區。 第二個設定（名為 **Setting2**）會將六個新 Azure 備份事件中的資料傳送到資源特定模式中的 Log Analytics 工作區。 第三個設定（名為 **Setting3**）會將資料從 Azure Site Recovery 事件傳送至 Azure 診斷模式中的 Log Analytics 工作區。

![三項設定](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>後續步驟

[瞭解診斷事件的 Log Analytics 資料模型](./backup-azure-reports-data-model.md)
