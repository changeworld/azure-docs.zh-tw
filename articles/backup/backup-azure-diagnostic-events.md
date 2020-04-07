---
title: 使用回復服務保存庫的診斷設定
description: 描述如何使用 Azure 備份的新舊診斷事件的文章
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672789"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>使用適用於復原服務保存庫的診斷設定

Azure 備份發送可用於分析、警報和報告的診斷事件。 

您可以通過 Azure 門戶配置恢復服務保管庫的診斷設置,通過導航到保管庫並按一**下 「診斷設置」** 選單項。 按下 **「新增診斷設定**」允許您將一個或多個診斷事件發送到存儲帳戶、事件中心或日誌分析 (LA) 工作區。

![診斷設定刀片](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>可用於 Azure 備份使用者的診斷事件

Azure 備份提供以下診斷事件,每個診斷事件都提供有關一組與備份相關的特定專案的詳細資料:

* 核心Azure備份
* 載入項 Azure 備份警示
* 新增 Azure 備份保護實體
* 載入項目 Azure 備份工作
* 附加 Azure 備份原則
* 附加 Azure 備份儲存

[Azure 備份診斷事件的資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

這些事件的數據可以發送到存儲帳戶、LA 工作區或事件中心。 如果要將此資料發送到 LA 工作區,則需要在 **「診斷設定」** 螢幕中選擇 **「資源特定**」切換(請參閱以下各節中的詳細資訊)。

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>將診斷設定與紀錄分析 (LA) 一起使用

與 Azure 日誌分析路線圖一致,Azure 備份現在允許您將保管庫診斷數據發送到專用 LA 表進行備份。 這些稱為[特定於資源的表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

要將保管庫診斷數據發送到洛杉磯:

1.    導航到保管庫,然後單擊 **「診斷設置**」。。 按下 **+ 新增診斷設定**。
2.    為"診斷"設置指定名稱。
3.    選中「**發送到日誌分析」** 複選框,然後選擇日誌分析工作區。
4.    在切換中選擇**特定於資源**,並檢查以下六個事件 -**酷睿備份**、**附加Azure備份警報**、**附加Azure備份保護實例**,**附加Azure備份作業**,**附加Azure備份策略**,**附加Azure備份儲存**。
5.    按一下 [ **儲存**]。

![資源樣式模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

數據流入 LA 工作區後,將為每個事件在工作區中創建專用表。 您可以直接查詢這些表中的任何一個,並在必要時在這些表之間執行聯接或聯合。

> [!IMPORTANT]
> 上述六個事件,即核心 Azure 備份、附加 Azure 備份警報、AddonAzure 備份保護實例、附加Azure備份作業、附加Azure備份策略和附加Azure備份儲存,**僅在**[備份報告中](https://docs.microsoft.com/azure/backup/configure-reports)的資源特定模式下支援。 **請注意,如果嘗試在 Azure 診斷模式下發送這六個事件的數據,則備份報告中不會顯示任何數據。**

## <a name="legacy-event"></a>傳統活動

傳統上,保管庫的所有與備份相關的診斷數據都包含在稱為"AzureBackupReport"的單個事件中。 上述六個事件實質上是 AzureBackup 報告中包含的所有數據的分解。 

目前,我們將繼續支援 AzureBackupReport 事件,以進行向後相容性,如果使用者在此事件中存在自定義查詢,例如自定義日誌警報、自定義可視化等。但是,**我們建議儘早遷移到[新事件](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users)**,因為這使數據更容易在日誌查詢中處理,從而提供更好的架構及其結構的可發現性,從而提高了引入延遲和查詢時間的性能。 

**Azure 診斷模式下的舊事件最終將被棄用,因此選擇新事件可能有助於您避免以後的複雜遷移**。 我們利用日誌分析[的報告解決方案](https://docs.microsoft.com/azure/backup/configure-reports)也將停止支援來自舊事件的數據。

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>移至新診斷設定的步驟(到紀錄分析工作區)

1. 使用舊事件確定哪些保管庫將數據發送到日誌分析工作區,以及它們所屬的訂閱。 執行以下工作區以識別這些保管庫和訂閱:

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

2. 使用 Azure 備份的[內建 Azure 策略](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)為指定範圍內的所有保管庫添加新的診斷設置。 此策略向沒有診斷設置(或)僅具有舊診斷設置的保管庫添加新的診斷設置。 此策略可以一次分配給整個訂閱或資源組。 請注意,您需要對為其分配策略的每個訂閱的"擁有者"訪問許可權。

您可以選擇為 AzureBackupReport 和六個新事件設置單獨的診斷設置,直到遷移了所有自定義查詢以使用新表中的數據。 下圖顯示了具有兩個診斷設置的保管庫的示例。 第一個設置名為 **「設置1」,** 在 Azure 診斷模式下將 Azure Backup 報告事件的資料發送到 LA 工作區。 第二個設置名為 **「設置 2」,** 在資源特定模式下將六個新 Azure 備份事件的資料發送到 LA 工作區。

![兩個設定](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Azure 備份報告事件**僅在**Azure 診斷模式下受支援。 **請注意,如果您嘗試在資源特定模式下發送此事件的數據,則不會將資料流向 LA 工作區。**

> [!NOTE]
> 僅當使用者選中 **「發送到日誌分析**」時,才會顯示 Azure 診斷/特定於資源的切換。 要將數據發送到存儲帳戶或事件中心,使用者只需選擇所需的目標並檢查任何所需的事件,而無需任何其他輸入。 同樣,建議不要選擇舊事件 AzureBackupReport,向前看。

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>使用者將 Azure 網站復原事件傳送到紀錄分析 (LA)

Azure 備份和 Azure 網站恢復事件從同一恢復服務保管庫發送。 由於 Azure 網站恢復當前未連接到特定於資源的表,因此希望將 Azure 網站恢復事件發送到 LA 的使用者**將僅**使用 Azure 診斷模式(請參閱下圖)。 **為 Azure 網站復原事件選擇特定於資源的模式將阻止將所需的資料傳送到 LA 工作區**。

![網站復原事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

總結上述細微差別:

* 如果已使用 Azure 診斷設置 LA 診斷,並在其上面編寫了自定義查詢,請保持該設置**不變**,直到遷移查詢以使用新事件中的數據。
* 如果還希望加入新表(如建議的那樣),請**創建新**的診斷設置,選擇 **「資源特定」** 並選擇上述六個新事件。
* 如果目前正在向 LA 發送 Azure 網站恢復事件,**請不要**為這些事件選擇特定於資源的模式,否則這些事件的數據不會流入 LA 工作區。 相反,請創建**一個其他診斷設置**,選擇**Azure 診斷**,然後選擇相關的 Azure 網站恢復事件。

下圖顯示了使用者對保管庫具有三個診斷設置的示例。 第一個設定名為 **「設定1」,** 將資料從 Azure Backup 報告事件發送到 Azure 診斷模式下的 LA 工作區。 第二個設置名為 **「設置 2」,** 將六個新的 Azure 備份事件的數據發送到資源特定模式下的 LA 工作區。 第三個設置名為 **「設置3」,** 將數據從 Azure 網站恢復事件發送到 Azure 診斷模式下的 LA 工作區。

![三種設定](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>後續步驟

[瞭解診斷事件的紀錄分析資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
