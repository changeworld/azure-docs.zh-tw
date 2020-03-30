---
title: 使用恢復服務保存庫的診斷設置
description: 描述如何使用 Azure 備份的新舊診斷事件的文章
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e3919d120e5f741af6cd30dd27e5a1dfa2b06cf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136934"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>使用適用於復原服務保存庫的診斷設定

Azure 備份發送可用於分析、警報和報告的診斷事件。 

您可以通過 Azure 門戶配置恢復服務保存庫的診斷設置，通過導航到保存庫並按一下 **"診斷設置"** 功能表項目。 按一下 **" 添加診斷設置**"允許您將一個或多個診斷事件發送到存儲帳戶、事件中心或日誌分析 （LA） 工作區。

![診斷設置刀片](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>可用於 Azure 備份使用者的診斷事件

Azure 備份提供以下診斷事件，每個診斷事件都提供有關一組與備份相關的特定專案的詳細資料：

* 核心Azure備份
* 載入項 Azure 備份警報
* 添加 Azure 備份保護實例
* 載入項 Azure 備份作業
* 附加 Azure 備份策略
* 附加 Azure 備份存儲

[Azure 備份診斷事件的資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

這些事件的資料可以發送到存儲帳戶、LA 工作區或事件中心。 如果要將此資料發送到 LA 工作區，則需要在 **"診斷設置"** 螢幕中選擇 **"資源特定**"切換（請參閱以下各節中的詳細資訊）。

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>將診斷設置與日誌分析 （LA） 一起使用

與 Azure 日誌分析路線圖一致，Azure 備份現在允許您將保存庫診斷資料發送到專用 LA 表進行備份。 這些稱為[特定于資源的表](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)。

要將保存庫診斷資料發送到洛杉磯：

1.  導航到保存庫，然後按一下 **"診斷設置**"。 按一下 **+ 添加診斷設置**。
2.  為"診斷"設置指定名稱。
3.  選中"**發送到日誌分析"** 核取方塊，然後選擇日誌分析工作區。
4.  在切換中選擇**特定于資源**，並檢查以下六個事件 -**酷睿備份**、**附加Azure備份警報**、**附加Azure備份保護實例**、**附加Azure備份作業**、**附加Azure備份策略**和**附加Azure備份存儲**。
5.  按一下 [ **儲存**]。

![資源特定模式](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

資料流程入 LA 工作區後，將為每個事件在工作區中創建專用表。 您可以直接查詢這些表中的任何一個，並在必要時在這些表之間執行聯接或聯合。

> [!IMPORTANT]
> 上述六個事件，即核心 Azure 備份、附加 Azure 備份警報、附加 Azure 備份保護實例、附加Azure備份作業、附加Azure備份策略和附加Azure備份存儲僅在資源特定**模式下支援。** **請注意，如果嘗試在 Azure 診斷模式下發送這六個事件的資料，則不會將資料流程向 LA 工作區。**

## <a name="legacy-event"></a>傳統活動

傳統上，保存庫的所有與備份相關的診斷資料都包含在稱為"AzureBackupReport"的單個事件中。 上述六個事件實質上是 AzureBackup 報告中包含的所有資料的分解。 

目前，我們將繼續支援 AzureBackupReport 事件，以進行向後相容性，如果使用者在此事件中存在自訂查詢，例如自訂日誌警報、自訂視覺化等。但是，**我們建議儘早遷移到新事件**，因為這使資料更容易在日誌查詢中處理，從而提供更好的架構及其結構的可發現性，從而提高了引入延遲和查詢時間的性能。 **對使用 Azure 診斷模式的支援最終將被淘汰，因此選擇新事件可能有助於您避免以後的複雜遷移**。

使用 Azure 備份的內置策略為指定範圍內的所有保存庫添加新的診斷設置，包括 6 個新事件：[大規模配置保存庫診斷設置](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics)

您可以選擇為 AzureBackupReport 和六個新事件創建單獨的診斷設置，直到遷移所有自訂查詢以使用新表中的資料。 下圖顯示了具有兩個診斷設置的保存庫的示例。 第一個設置名為 **"設置1"，** 在 Azure 診斷模式下將 Azure Backup 報告事件的資料發送到 LA 工作區。 第二個設置名為 **"設置 2"，** 在資源特定模式下將六個新 Azure 備份事件的資料發送到 LA 工作區。

![兩個設置](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Azure 備份報告事件**僅在**Azure 診斷模式下受支援。 **請注意，如果您嘗試在資源特定模式下發送此事件的資料，則不會將資料流程向 LA 工作區。**

> [!NOTE]
> 僅當使用者選中 **"發送到日誌分析**"時，才會顯示 Azure 診斷/特定于資源的切換。 要將資料發送到存儲帳戶或事件中心，使用者只需選擇所需的目標並檢查任何所需的事件，而無需任何其他輸入。 同樣，建議不要選擇舊事件 AzureBackupReport，向前看。

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>使用者將 Azure 網站恢復事件發送到日誌分析 （LA）

Azure 備份和 Azure 網站恢復事件從同一恢復服務保存庫發送。 由於 Azure 網站恢復當前未連接到特定于資源的表，因此希望將 Azure 網站恢復事件發送到 LA 的使用者**將僅**使用 Azure 診斷模式（請參閱下圖）。 **為 Azure 網站恢復事件選擇特定于資源的模式將阻止將所需的資料發送到 LA 工作區**。

![網站恢復事件](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

總結上述細微差別：

* 如果已使用 Azure 診斷設置 LA 診斷，並在其上面編寫了自訂查詢，請保持該設置**不變**，直到遷移查詢以使用新事件中的資料。
* 如果還希望加入新表（如建議的那樣），請**創建新**的診斷設置，選擇 **"資源特定"** 並選擇上述六個新事件。
* 如果當前正在向 LA 發送 Azure 網站恢復事件，**請不要**為這些事件選擇特定于資源的模式，否則這些事件的資料不會流入 LA 工作區。 相反，請創建**一個其他診斷設置**，選擇**Azure 診斷**，然後選擇相關的 Azure 網站恢復事件。

下圖顯示了使用者對保存庫具有三個診斷設置的示例。 第一個設置名為 **"設置1"，** 將資料從 Azure Backup 報告事件發送到 Azure 診斷模式下的 LA 工作區。 第二個設置名為 **"設置 2"，** 將六個新的 Azure 備份事件的資料發送到資源特定模式下的 LA 工作區。 第三個設置名為 **"設置3"，** 將資料從 Azure 網站恢復事件發送到 Azure 診斷模式下的 LA 工作區。

![三種設置](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>後續步驟

[瞭解診斷事件的日誌分析資料模型](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
