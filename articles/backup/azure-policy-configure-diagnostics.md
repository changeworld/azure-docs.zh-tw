---
title: 大規模配置保存庫診斷設置
description: 使用 Azure 策略為給定作用域中的所有保存庫配置日誌分析診斷設置
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: c92957cab3e1ed745e7031e3c6f32e7ecda550a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584501"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>大規模配置保存庫診斷設置

Azure 備份提供的報告解決方案利用日誌分析 （LA）。 要將任何給定保存庫的資料發送到 LA，需要為該保存庫創建[診斷設置](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

通常，每個保存庫手動添加診斷設置可能是一項繁瑣的任務。 此外，創建的任何新保存庫都需要啟用診斷設置，以便能夠查看此保存庫的報告。 

為了簡化大規模創建診斷設置（以 LA 為目標），Azure 備份提供了內置的 Azure[策略](https://docs.microsoft.com/azure/governance/policy/)。 此策略將 LA 診斷設置添加到給定訂閱或資源組中的所有保存庫。 以下各節提供有關如何使用此策略的說明。

## <a name="supported-scenarios"></a>支援的案例

* 該策略可以一次應用於特定訂閱中的所有恢復服務保存庫（或訂閱中的資源組）。 分配策略的使用者需要具有對策略分配的訂閱的"擁有者"存取權限。

* 使用者指定的 LA 工作區（將診斷資料發送到該工作區）可以位於與策略分配到的保存庫不同的訂閱中。 使用者需要對存在指定 LA 工作區的訂閱具有"讀者"、"貢獻者"或"擁有者"存取權限。

* 管理組作用域當前不受支援。

* 內置策略目前不在國家雲中。

## <a name="assigning-the-built-in-policy-to-a-scope"></a>將內置策略分配給作用域

要在所需範圍內為保存庫分配策略，請按照以下步驟操作：

1. 登錄到 Azure 門戶並導航到**策略**儀表板。
2. 選擇左側功能表中**的定義**，以獲取有關 Azure 資源的所有內置策略的清單。
3. 篩選**類別=監視**的清單。 找到名為 **[預覽]的策略：將恢復服務保存庫的診斷設置部署到資源特定類別的日誌分析工作區**。

![策略定義邊欄選項卡](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 按一下策略的名稱。 您將被重定向到此策略的詳細定義。

![詳細的策略定義](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 按一下邊欄選項卡頂部的 **"分配"** 按鈕。 這將將您重定向到 **"分配策略"** 邊欄選項卡。

6. 在 **"基礎知識"** 下，按一下 **"範圍"** 欄位旁邊的三個點。 這將打開一個正確的上下文邊欄選項卡，您可以在其中選擇要應用的策略的訂閱。 您還可以選擇資源組，以便策略僅應用於特定資源組中的保存庫。

![策略分配基礎知識](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. 在**參數**下，輸入以下資訊：

* **設定檔名稱**- 將分配給策略創建的診斷設置的名稱。
* **日誌分析工作區**- 應關聯診斷設置的日誌分析工作區。 策略分配範圍內所有保存庫的診斷資料將推送到指定的 LA 工作區。

* **排除標記名稱（可選）和排除標記值（可選）** - 您可以選擇從策略分配中排除包含特定標記名稱和值的保存庫。 例如，如果您**不希望**將診斷設置添加到標記"isTest"設置為值"是"的保存庫，則必須在 **"排除標記名稱**"欄位中輸入"isTest"，在 **"排除標記值"** 欄位中輸入"是"。 如果這兩個欄位中的任何一個（或兩個）都留空，則策略將應用於所有相關保存庫，而不管它們包含的標記。

![策略分配參數](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **創建修正任務**- 將策略分配給作用域後，該作用域中創建的任何新保存庫將自動設定 LA 診斷設置（自創建保存庫之日起 30 分鐘內）。 要將診斷設置添加到作用域中的現有保存庫，可以在策略分配時觸發修正任務。 要觸發修正任務，請選擇核取方塊 **"創建修正任務**"。 

![策略分配補救](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. 導航到 **"審閱+創建**"選項卡，然後按一下"**創建**"。

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>補救任務在什麼條件下將應用於保存庫？

根據策略的定義，修正任務應用於不符合要求的保存庫。 如果保存庫滿足以下任一條件，則不符合該保存庫：

* 保存庫不存在診斷設置。
* 保存庫存在診斷設置，但兩個設置均未啟用**所有**資源特定事件，LA 作為目標，並在切換中選擇**特定于資源**的事件。 

因此，即使使用者在 Azure 診斷模式下啟用了 AzureBackupReport 事件（備份報告支援），修復任務仍將應用於此保存庫，因為資源特定模式是創建診斷設置的建議方法，[並且將繼續](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)使用 。

此外，如果使用者只有啟用了六個資源特定事件的子集，則修正任務將應用於此保存庫，因為備份報告僅在啟用了所有六個資源特定事件時按預期工作。

> [!NOTE]
>
> 如果保存庫具有啟用**了資源特定類別子集**的現有診斷設置，配置為將資料發送到特定的 LA 工作區（如"工作區 X"），則如果策略分配中提供的目標 LA 工作區**與"** 工作區 X"相同，則修正任務將失敗（僅針對該保存庫）。 
>
>這是因為，如果同一資源上的兩個不同的診斷設置啟用的事件以某種形式**重疊**，則這些設置不能與目標具有相同的 LA 工作區。 您必須手動解決此故障，方法是導航到相關保存庫，並將具有其他 LA 工作區的診斷設置配置為目標。
>
> 請注意，如果現有診斷設置僅啟用 AzureBackupReport，並將工作區 X 作為目標，則修正任務**不會**失敗，因為在這種情況下，現有設置啟用的事件與修復任務創建的設置啟用的事件之間沒有重疊。

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用備份報告](https://docs.microsoft.com/azure/backup/configure-reports)
* [瞭解有關 Azure 策略的更多](https://docs.microsoft.com/azure/governance/policy/)
* [使用 Azure 策略自動啟用給範圍中所有 VM 的備份](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
