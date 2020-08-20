---
title: 大規模設定保存庫診斷設定
description: 使用 Azure 原則為指定範圍中的所有保存庫設定 Log Analytics 診斷設定
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 58ef8af56bb3f44664ffaec6a17bab5f5e92808e
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612498"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>大規模設定保存庫診斷設定

Azure 備份所提供的報告解決方案會利用 Log Analytics (LA) 。 針對要傳送給 LA 的任何特定保存庫資料，必須為該保存庫建立 [診斷設定](./backup-azure-diagnostic-events.md) 。

通常，每個保存庫都會手動新增診斷設定，是一項繁瑣的工作。 此外，任何新建立的保存庫也需要啟用診斷設定，才能查看此保存庫的報表。

為了簡化以 LA 作為目的地)  (大規模診斷設定的建立，Azure 備份提供內建的 [Azure 原則](../governance/policy/index.yml)。 此原則會將 LA 診斷設定新增至指定訂用帳戶或資源群組中的所有保存庫。 下列各節提供如何使用此原則的指示。

## <a name="supported-scenarios"></a>支援的案例

* 原則可以一次套用至特定訂用帳戶中的所有復原服務保存庫 (或訂用帳戶) 內的資源群組。 指派原則的使用者必須擁有指派原則之訂用帳戶的「擁有者」存取權。

* 由使用者所指定的 LA 工作區 (要將診斷資料傳送到哪個訂用帳戶，) 可以與指派原則的保存庫位於不同的訂用帳戶中。 使用者必須擁有指定之 LA 工作區所在訂用帳戶的「讀取者」、「參與者」或「擁有者」存取權。

* 目前不支援管理群組範圍。

* 內建原則目前無法在國家雲端中使用。

## <a name="assigning-the-built-in-policy-to-a-scope"></a>將內建原則指派給範圍

若要在所需範圍內指派保存庫的原則，請遵循下列步驟：

1. 登入 Azure 入口網站，然後流覽至 **原則** 儀表板。
2. 選取左側功能表中的 [ **定義** ]，以取得跨 Azure 資源的所有內建原則清單。
3. 篩選 **分類 = 監視**的清單。 找出名為 **[預覽]：將復原服務保存庫的診斷設定部署至 Log Analytics 工作區，以取得資源專屬類別**的原則。

    ![原則定義窗格](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 選取原則的名稱。 系統會將您重新導向至此原則的詳細定義。

    ![詳細原則定義](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 選取窗格頂端的 [ **指派** ] 按鈕。 這會將您重新導向至 [ **指派原則** ] 窗格。

6. 在 [ **基本**] 底下，選取 [ **領域** ] 欄位旁的三個點。 這會開啟一個右邊的內容窗格，您可以在其中選取要套用原則的訂用帳戶。 您也可以選擇性地選取資源群組，讓原則僅適用于特定資源群組中的保存庫。

    ![原則指派基本概念](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. 在 [ **參數**] 下，輸入下列資訊：

    * **設定檔名稱** -將指派給原則所建立之診斷設定的名稱。
    * **Log Analytics 工作區** -應該與診斷設定相關聯的 Log Analytics 工作區。 原則指派範圍中所有保存庫的診斷資料都會推送至指定的 LA 工作區。

    * **排除標籤名稱 (選擇性) 和排除標記值 (選擇性) ** -您可以選擇從原則指派中排除包含特定標記名稱和值的保存庫。 例如，如果您 **不** 想要將診斷設定加入至將 ' isTest ' 設為值 ' yes ' 的保存庫，您必須在 [ **排除標記名稱** ] 欄位中輸入 ' isTest '，並在 [ **排除標記值** ] 欄位中輸入 [是]。 如果這兩個欄位的任何 (或兩個) 都是空的，則不論它們包含哪些標籤，原則都會套用至所有相關的保存庫。

    ![原則指派參數](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **建立補救** 工作-將原則指派給範圍之後，在該範圍中建立的所有新保存庫都會自動從建立保存庫) 的30分鐘內開始設定 (的 LA 診斷設定。 若要將診斷設定新增至範圍中現有的保存庫，您可以在原則指派時間觸發補救工作。 若要觸發補救工作，請選取 [ **建立補救工作**] 核取方塊。

    ![原則指派補救](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. 流覽至 [ **審核 + 建立** ] 索引標籤，然後選取 [ **建立**]。

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>在哪些條件下，補救工作會套用至保存庫？

補救工作會根據原則的定義套用至不符合規範的保存庫。 如果保存庫符合下列其中一個條件，則不符合規範：

* 保存庫不存在任何診斷設定。
* 保存庫有診斷設定，但這兩種設定都沒有以 LA 作為目的地啟用的 **所有** 資源特定事件，以及在切換中選取的 **資源** 。

因此，即使使用者有在 AzureDiagnostics 模式下啟用 AzureBackupReport 事件的保存庫 () 的備份報表 [支援，補救](./backup-azure-diagnostic-events.md#legacy-event)工作仍會套用至此保存庫，因為建立診斷設定的建議方法是使用資源專屬模式。

此外，如果使用者的保存庫只具有已啟用六個資源特定事件的子集，則會針對此保存庫套用補救工作，因為只有在已啟用六個資源特定事件的情況下，備份報告才會如預期般運作。

> [!NOTE]
>
> 如果保存庫具有已啟用的資源專屬類別 **子集** 的現有診斷設定，設定為將資料傳送至特定的 LA 工作區（例如 ' workspace x '），則如果原則指派中提供的目的地 LA 工作區是 **相同** 的「工作區 x」，則該保存) 庫的補救工作將會失敗 (。
>
>這是因為在相同資源上由兩個不同診斷設定所啟用的事件，在某個表單中重 **迭** ，因此設定不能具有與目的地相同的 LA 工作區。 您必須藉由流覽至相關保存庫，並使用不同的 LA 工作區設定診斷設定做為目的地，以手動解決此失敗。
>
> 請注意，如果現有的診斷設定只是以工作區 X 作為目的地啟用 AzureBackupReport，補救工作將 **不** 會失敗，因為在此情況下，現有設定所啟用的事件與補救工作所建立的設定所啟用的事件之間將不會有重迭。

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用備份報表](./configure-reports.md)
* [深入瞭解 Azure 原則](../governance/policy/index.yml)
* [使用 Azure 原則為指定範圍內的所有 Vm 自動啟用備份](./backup-azure-auto-enable-backup.md)
