---
title: 回復服務保管庫使用診斷設定
description: 本文介紹如何使用 Azure 備份的新舊診斷事件。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617307"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>回復服務保管庫使用診斷設定

Azure 備份發送可用於分析、警報和報告的診斷事件。

您可以通過存取保管庫並選擇診斷設定,透過 Azure 門戶設定回復服務保存式**庫 。** 選擇 **「新增診斷設定**」允許您將一個或多個診斷事件發送到存儲帳戶、事件中心或日誌分析工作區。

![診斷設定窗格](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>可用於 Azure 備份使用者的診斷事件

Azure 備份提供以下診斷事件。 每個事件都提供有關一組特定的備份相關項目的詳細資料:

* 核心Azure備份
* 載入項 Azure 備份警示
* 新增 Azure 備份保護實體
* 載入項目 Azure 備份工作
* 附加 Azure 備份原則
* 附加 Azure 備份儲存

有關詳細資訊,請參閱[Azure 備份診斷事件的資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)。

這些事件的數據可以發送到存儲帳戶、日誌分析工作區或事件中心。 如果要將此資料發送到日誌分析工作區,請在 **「診斷設置」** 螢幕上選擇 **「資源特定**切換」。 如需詳細資訊，請參閱下列幾節。

## <a name="use-diagnostics-settings-with-log-analytics"></a>將診斷設定與紀錄分析一起使用

現在,可以使用 Azure 備份將保管庫診斷數據發送到專用日誌分析表進行備份。 這些表稱為[特定於資源的表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

要將保管庫診斷資料傳送到日誌分析:

1. 轉到保管庫,然後選擇 **「診斷設置**」。。 選擇 **= 新增診斷設定**。
1. 為診斷設置指定名稱。
1. 勾選此選項 **, 傳送到日誌分析**「複選框,然後選擇日誌分析工作區。
1. 選擇切換中**特定於的資源**,然後選擇以下六個事件:核心**Azure 備份**、**附加 Azure 備份作業**、**附加 Azure 備份警報**、**附加 Azure 備份策略**,**附加 Azure 備份儲存**,**以及 AddonAzure 備份保護實體 。**
1. 選取 [儲存]  。

   ![特定於資源模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

將數據流入日誌分析工作區後,將為每個事件在工作區中創建專用表。 您可以直接查詢這些表中的任何一個。 如有必要,還可以在這些表之間執行聯接或聯合。

> [!IMPORTANT]
> 這六個事件,即 CoreAzure 備份、附加 Azure 備份作業、附加 Azure 備份警報、附加 Azure 備份策略、AddonAzure 備份儲存和 AddonAzure 備份保護實例,*僅在*[備份報告中](https://docs.microsoft.com/azure/backup/configure-reports)的資源特定模式下受支援。 *如果嘗試在 Azure 診斷模式下發送這六個事件的數據,則備份報告中不會顯示任何數據。*

## <a name="legacy-event"></a>傳統事件

傳統上,保管庫的所有與備份相關的診斷數據都包含在稱為 AzureBackupReport 的單個事件中。 這裡描述的六個事件實質上是 AzureBackup 報告中包含的所有數據的分解。 

目前,在使用者對此事件存在自定義查詢的情況下,我們將繼續支援 AzureBackupReport 事件,以進行向後相容性。 範例包括自定義日誌警報和自定義可視化效果。 *我們建議您儘早轉到[新事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)*。 新事件:

- 使數據更易於在日誌查詢中使用。
- 提供更好的架構及其結構的可發現性。
- 提高引入延遲和查詢時間的性能。 

*Azure 診斷模式下的舊事件最終將被棄用。選擇新事件可能有助於您避免以後的複雜移移*。 我們使用日誌分析[的報告解決方案](https://docs.microsoft.com/azure/backup/configure-reports)也將停止支援來自舊事件的數據。

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>移動到日誌分析工作區的新診斷設定的步驟

1. 使用舊事件及其所屬的訂閱,確定哪些保管庫將數據發送到日誌分析工作區。 運行以下工作區以標識這些保管庫和訂閱。

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

1. 使用 Azure 備份中的[內置 Azure 策略](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)為指定範圍內的所有保管庫添加新的診斷設置。 此策略向沒有診斷設置或僅具有舊診斷設置的保管庫添加新的診斷設置。 此策略可以一次分配給整個訂閱或資源組。 您必須具有擁有者對為其分配策略的每個訂閱的訪問許可權。

您可以選擇為 AzureBackupReport 和六個新事件設置單獨的診斷設置,直到遷移所有自定義查詢以使用新表中的數據。 下圖顯示了具有兩個診斷設置的保管庫的示例。 第一個設置名為 **「設定1」,** 在 Azure 診斷模式下將 AzureBackupReport 事件的資料發送到日誌分析工作區。 第二個設定名為 **「設定2」,** 以特定於資源的模式將六個新的 Azure 備份事件的資料傳送到日誌分析工作區。

![兩個設定](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Azure 備份報告事件*僅在*Azure 診斷模式下受支援。 *如果嘗試以特定於資源的模式發送此事件的數據,則不會將數據流向日誌分析工作區。*

> [!NOTE]
> 僅當使用者選擇 **「發送到日誌分析**」時,才會顯示**Azure 診斷**或**特定於資源的**切換。 要將數據發送到存儲帳戶或事件中心,用戶選擇所需的目標,併為任何所需事件選擇複選框,而無需任何其他輸入。 同樣,我們建議您不要選擇舊事件 AzureBackupReport。

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>將 Azure 網站回復事件傳送到日誌分析

Azure 備份和 Azure 網站恢復事件從同一恢復服務保管庫發送。 Azure 網站恢復當前不適用於特定於資源的表。 想要將 Azure 網站恢復事件發送到日誌分析的使用者將定向為*僅*使用 Azure 診斷模式,如映射所示。 *為 Azure 網站回復事件選擇特定於資源的模式將阻止將所需的資料傳送到紀錄分析工作區*。

![網站復原事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

總結：

* 如果已使用 Azure 診斷設置了日誌分析診斷,並在其上面編寫了自定義查詢,請保留該設置*不變*,直到遷移查詢以使用新事件中的數據。
* 如果還希望像我們建議的那樣,加入新表,請**創建新**的診斷設置,選擇**特定於"資源"** 並選擇六個新事件。
* 如果目前正在向日誌分析發送 Azure 網站恢復事件,*請不要*為這些事件選擇特定於資源的模式。 否則,這些事件的數據不會流入日誌分析工作區。 而是創建其他診斷設置,選擇**Azure 診斷**,然後選擇相關的 Azure 網站恢復事件。

下圖顯示了具有三個保管庫診斷設置的使用者的示例。 第一個設定名為 **「設定1」,** 將資料從 Azure BackupReport 事件發送到 Azure 診斷模式下的日誌分析工作區。 第二個設定名為 **「設定2」,** 以特定於資源的模式將六個新的 Azure 備份事件的資料傳送到日誌分析工作區。 第三個設置名為 **「設置3」,** 在 Azure 診斷模式下將數據從 Azure 網站恢復事件發送到日誌分析工作區。

![三種設定](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>後續步驟

[瞭解診斷事件的紀錄分析資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
