---
title: 設定大規模的保存庫診斷設定
description: 使用 Azure 原則，為指定範圍中的所有保存庫設定 Log Analytics 診斷設定
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: e6ba8eb98ca1d6af9fc745d9baf3840ccd1ac224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82195701"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>設定大規模的保存庫診斷設定

Azure 備份所提供的報告解決方案會利用 Log Analytics （LA）。 針對要傳送至 LA 的任何給定保存庫資料，必須為該保存庫建立[診斷設定](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)。

通常，每個保存庫手動新增診斷設定可能會是一件麻煩的工作。 此外，任何建立的新保存庫也需要啟用診斷設定，才能查看此保存庫的報告。

為了簡化大規模的診斷設定建立（使用 LA 作為目的地），Azure 備份提供內建[Azure 原則](https://docs.microsoft.com/azure/governance/policy/)。 此原則會將 LA 診斷設定新增至指定訂用帳戶或資源群組中的所有保存庫。 下列各節提供有關如何使用此原則的指示。

## <a name="supported-scenarios"></a>支援的案例

* 原則可以一次套用至特定訂用帳戶中的所有復原服務保存庫（或訂用帳戶內的資源群組）。 指派原則的使用者必須具有指派給該原則之訂用帳戶的「擁有者」存取權。

* 使用者所指定的 LA 工作區（將用來傳送診斷資料的目標），可以與指派原則的保存庫位於不同的訂用帳戶中。 使用者必須具有指定的 LA 工作區所在之訂用帳戶的「讀取者」、「參與者」或「擁有者」存取權。

* 目前不支援管理群組範圍。

* 內建原則目前無法在國家雲端中使用。

## <a name="assigning-the-built-in-policy-to-a-scope"></a>將內建原則指派給範圍

若要為所需範圍內的保存庫指派原則，請遵循下列步驟：

1. 登入 Azure 入口網站，然後流覽至 [**原則**] 儀表板。
2. 選取左側功能表中的 [**定義**]，以取得所有 Azure 資源的內建原則清單。
3. 篩選**Category = Monitoring**的清單。 找出名為 **[預覽] 的原則：將復原服務保存庫的診斷設定部署到 Log Analytics 工作區，以取得資源的特定類別**。

    ![原則定義分頁](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. 按一下原則的名稱。 系統會將您重新導向至此原則的詳細定義。

    ![詳細原則定義](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. 按一下分頁頂端的 [**指派**] 按鈕。 這會將您重新導向至 [**指派原則**] 分頁。

6. 在 [**基本**] 底下，按一下 [**領域**] 欄位旁的三個點。 這會開啟右內容分頁，您可以在其中選取要套用之原則的訂用帳戶。 您也可以選擇性地選取資源群組，讓原則僅適用于特定資源群組中的保存庫。

    ![原則指派基本概念](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. 在 [**參數**] 底下，輸入下列資訊：

    * **設定檔名稱**-將指派給原則所建立之診斷設定的名稱。
    * **Log Analytics 工作區**-診斷設定應該關聯的 Log Analytics 工作區。 原則指派範圍中所有保存庫的診斷資料將會推送至指定的 LA 工作區。

    * **排除標記名稱（選擇性）和排除標記值（選擇性）** -您可以選擇從原則指派中排除包含特定標記名稱和值的保存庫。 例如，如果您**不**想要將診斷設定加入至將標記 ' isTest ' 設定為值 ' yes ' 的保存庫，您必須在排除標記**名稱**欄位中輸入 ' isTest '，並在 [排除標籤**值**] 欄位中輸入 ' yes '。 如果這兩個欄位的任何（或兩者）保留空白，則不論其包含的標記為何，原則都會套用至所有相關的保存庫。

    ![原則指派參數](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **建立補救**工作-將原則指派給範圍之後，在該範圍內建立的任何新保存庫都會自動取得設定的 LA 診斷設定（從保存庫建立時間開始的30分鐘內）。 若要將診斷設定新增至範圍中現有的保存庫，您可以在原則指派時間觸發補救工作。 若要觸發補救工作，請選取 [**建立補救工作**] 核取方塊。

    ![原則指派補救](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. 流覽至 [**審核**] 和 [建立] 索引標籤，然後按一下 [**建立**]。

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>補救工作會在何種情況下套用至保存庫？

補救工作會根據原則的定義，套用至不符合規範的保存庫。 如果保存庫符合下列任一條件，則不符合規範：

* 保存庫沒有任何診斷設定。
* 保存庫有診斷設定，但這兩個設定都沒有以 LA 作為目的地啟用的**所有**資源特定事件，以及切換中選取的**資源特定**。

因此，即使使用者的保存庫已在 AzureDiagnostics 模式中啟用 AzureBackupReport 事件（由備份報告支援），補救工作仍然會套用到此保存[庫，因為](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#legacy-event)資源特定模式是建議的方法來建立診斷設定。

此外，如果使用者的保存庫僅具有六個已啟用資源特定事件的子集，則會針對此保存庫套用補救工作，因為只有在所有六個資源的特定事件都已啟用時，備份報表才會如預期般運作。

> [!NOTE]
>
> 如果保存庫具有已啟用**資源特定分類子集的**現有診斷設定，並設定為將資料傳送至特定的 LA 工作區（例如「工作區 x」），則如果原則指派中提供的目的地 LA 工作區是**相同**的「工作區 x」，則補救工作將會失敗（僅適用于該保存庫）。
>
>這是因為，如果相同資源上的兩個不同診斷設定所啟用的事件在某種形式上重**迭**，則設定不能有與目的地相同的 LA 工作區。 您必須手動解決此錯誤，方法是流覽至相關的保存庫，並使用不同的 LA 工作區作為目的地來設定診斷設定。
>
> 請注意，如果現有的診斷設定為僅以工作區 X 作為目的地啟用的 AzureBackupReport，則補救工作將**不**會失敗，因為在此情況下，現有設定所啟用的事件與由補救工作所建立之設定所啟用的事件之間，將不會重迭。

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用備份報告](https://docs.microsoft.com/azure/backup/configure-reports)
* [深入瞭解 Azure 原則](https://docs.microsoft.com/azure/governance/policy/)
* [使用 Azure 原則自動啟用授與範圍中所有 Vm 的備份](https://docs.microsoft.com/azure/backup/backup-azure-auto-enable-backup)
