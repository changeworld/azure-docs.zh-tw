---
title: 從 VM 設定備份 Azure VM
description: 在本文中，您將瞭解如何使用 Azure 備份服務來備份單一 Azure VM 或多個 Azure Vm。
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 722c24ce87edc692156a86338521aa3b2f9c7562
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286706"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>從 VM 設定備份 Azure VM

本文將說明如何使用 [Azure 備份](backup-overview.md)服務來備份 Azure VM。 您可以使用幾個方法來備份 Azure VM：

- 單一 Azure VM：本文中的指示會說明如何直接從 VM 設定來備份 Azure VM。
- 多個 Azure Vm：您可以設定復原服務保存庫，並為多個 Azure Vm 設定備份。 針對此案例，請依照[本文](backup-azure-arm-vms-prepare.md)中的指示。

## <a name="before-you-start"></a>在您開始使用 Intune 之前

1. [了解](backup-architecture.md#how-does-azure-backup-work)備份的運作方式，以及[確認](backup-support-matrix.md#azure-vm-backup-support)支援需求。
2. [取得 Azure VM 備份的概觀](backup-azure-vms-introduction.md)。

### <a name="azure-vm-agent-installation"></a>Azure VM 代理程式安裝

為備份 Azure VM，Azure 備份會在執行於機器上的 VM 代理程式上安裝擴充功能。 如果您的 VM 是從 Azure Marketplace 映射建立的，代理程式將會執行。 在某些情況下，例如，您建立自訂的 VM，或是從內部部署遷移機器。 您可能需要手動安裝代理程式。

- 如果您需要以手動方式安裝 VM 代理程式，請依照適用於 [Windows](../virtual-machines/extensions/agent-windows.md) 或 [Linux](../virtual-machines/extensions/agent-linux.md) VM 的指示。
- 安裝代理程式之後，當您啟用備份時，Azure 備份就會將備份擴充功能安裝到代理程式。 Azure 備份會更新及修補擴充功能，無須使用者介入。

## <a name="back-up-from-azure-vm-settings"></a>從 Azure VM 設定進行備份

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 按一下 [所有服務]****，然後在 [篩選] 中輸入**虛擬機器**，然後按一下 [虛擬機器]****。
3. 在 VM 清單中，選取要備份的 VM。
4. 在 VM 功能表中，按一下 [備份]****。
5. 在 [復原服務保存庫]**** 中，執行下列動作：
   - 如果您已經有保存庫，請按一下 [選取現有項目]****，然後選取保存庫。
   - 如果您沒有保存庫，請按一下 [新建]****。 指定保存庫名稱。 保存庫會建立在與 VM 相同的區域和資源群組中。 若您直接從 VM 設定啟用備份，則無法修改這些設定。

        ![啟用備份精靈](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. 在 **[選擇備份原則**] 中，執行下列其中一項：

   - 保留預設原則。 此原則會每天一次地在指定時間備份 VM，並讓備份在保存庫中保留 30 天。
   - 如果您已有備份原則，請選取現有的原則。
   - 建立新的原則，並定義原則設定。  

       ![選取備份原則](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. 按一下 [**啟用備份**]。 這會使備份原則與 VM 相關聯。

    ![啟用備份按鈕](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. 您可以在入口網站通知中，追蹤設定進度。
9. 作業完成後，請在 VM 功能表中按一下 [備份]****。 頁面會顯示 VM 的備份狀態、復原點資訊、執行的作業及發出的警示。

   ![備份狀態](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. 啟用備份之後，就會執行初始備份。 您可以立即啟動初始備份，或根據備份排程來啟動。
    - 在完成初始備份之前，[上次備份狀態]**** 會顯示為 [警告 (待執行初始備份)]****。
    - 若要查看下一個排定的備份會在何時執行，請按一下備份原則名稱。

## <a name="run-a-backup-immediately"></a>立即執行備份

1. 若要立即執行備份，請在 VM 功能表中，按一下 [立即**備份**  >  **備份**]。

    ![執行備份](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. 在 [立即備份]**** 中，使用行事曆控制項，選取復原點應保留到哪一天 > 然後按一下 [確定]****。

    ![備份保留日](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. 入口網站通知可讓您知道已觸發的備份工作。 若要監視備份進度，請按一下 [檢視所有作業]****。

## <a name="back-up-from-the-recovery-services-vault"></a>從復原服務保存庫進行備份

請遵循這篇文章中的指示，藉由設定 Azure 備份復原服務保存庫，以及在保存庫中啟用備份，來啟用 Azure VM 的備份。

>[!NOTE]
> **Azure 備份現在支援使用 Azure 虛擬機器備份解決方案進行選擇性磁碟備份和還原。**
>
>目前，Azure 備份支援使用虛擬機器備份解決方案來同時備份 VM 中的所有磁碟 (作業系統與資料)。 使用排除磁碟功能時，您可以從 VM 的諸多資料磁碟中，選擇備份一個或多個磁碟。 如此可提供有效率且符合成本效益的解決方案，以滿足您的備份和還原需求。 每個復原點都有備份作業所含磁碟的資料，這進一步可讓您在還原作業期間，從給定的復原點還原磁碟子集。 從快照集和保存庫還原時都是如此。
>
>**若要註冊預覽版，請將電子郵件寄到 AskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>後續步驟

- 如果您對這篇文章中的程序有任何疑問，請參閱[疑難排解指南](backup-azure-vms-troubleshoot.md)。
- [深入了解](backup-azure-manage-vms.md)管理備份。
