---
title: 從 VM 設定備份 Azure VM
description: 在本文中，您將瞭解如何使用 Azure 備份服務來備份單一 Azure VM 或多個 Azure Vm。
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 55b71d2a2901cdde984df3ebfd68a2a643b78b74
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89667515"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>從 VM 設定備份 Azure VM

本文將說明如何使用 [Azure 備份](backup-overview.md)服務來備份 Azure VM。 您可以使用幾個方法來備份 Azure VM：

- 單一 Azure VM：本文中的指示說明如何直接從 VM 設定備份 Azure VM。
- 多個 Azure Vm：您可以設定復原服務保存庫，並設定多個 Azure Vm 的備份。 針對此案例，請依照[本文](backup-azure-arm-vms-prepare.md)中的指示。

## <a name="before-you-start"></a>開始之前

1. [了解](backup-architecture.md#how-does-azure-backup-work)備份的運作方式，以及[確認](backup-support-matrix.md#azure-vm-backup-support)支援需求。
2. [取得 Azure VM 備份的概觀](backup-azure-vms-introduction.md)。

### <a name="azure-vm-agent-installation"></a>Azure VM 代理程式安裝

若要備份 Azure Vm，Azure 備份在電腦上執行的 VM 代理程式上安裝擴充功能。 如果您的 VM 是從 Azure Marketplace 映射建立的，則代理程式將會執行。 在某些情況下（例如，如果您建立自訂 VM，或從內部部署遷移電腦），您可能需要手動安裝代理程式。

- 如果您需要以手動方式安裝 VM 代理程式，請依照適用於 [Windows](../virtual-machines/extensions/agent-windows.md) 或 [Linux](../virtual-machines/extensions/agent-linux.md) VM 的指示。
- 安裝代理程式之後，當您啟用備份時，Azure 備份就會將備份擴充功能安裝到代理程式。 Azure 備份會更新及修補擴充功能，無須使用者介入。

## <a name="back-up-from-azure-vm-settings"></a>從 Azure VM 設定進行備份

1. 登入 [Azure 入口網站](https://portal.azure.com/)。
2. 選取 [ **所有服務** ]，然後在篩選中輸入 **虛擬機器**，然後選取 [ **虛擬機器**]。
3. 從 Vm 清單中，選取您要備份的 VM。
4. 在 [VM] 功能表上，選取 [ **備份**]。
5. 在 [復原服務保存庫]**** 中，執行下列動作：
   - 如果您已經有保存庫，請選取 [ **選取現有**的]，然後選取保存庫。
   - 如果您沒有保存庫，請選取 [ **建立新**的]。 指定保存庫名稱。 保存庫會建立在與 VM 相同的區域和資源群組中。 若您直接從 VM 設定啟用備份，則無法修改這些設定。

        ![啟用備份精靈](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. 在 **[選擇備份原則**] 中，執行下列其中一項：

   - 保留預設原則。 此原則會每天一次地在指定時間備份 VM，並讓備份在保存庫中保留 30 天。
   - 如果有的話，請選取現有的備份原則。
   - 建立新的原則，並定義原則設定。  

       ![選取備份原則](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. 選取 [啟用備份]。 這會使備份原則與 VM 相關聯。

    ![啟用備份按鈕](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. 您可以在入口網站通知中，追蹤設定進度。
9. 作業完成後，請在 VM 功能表中選取 [ **備份**]。 頁面會顯示 VM 的備份狀態、復原點資訊、執行的作業及發出的警示。

   ![備份狀態](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. 啟用備份之後，就會執行初始備份。 您可以立即啟動初始備份，或根據備份排程來啟動。
    - 在完成初始備份之前，[上次備份狀態]**** 會顯示為 [警告 (待執行初始備份)]****。
    - 若要查看下一個排定的備份將在何時執行，請選取備份原則名稱。

## <a name="run-a-backup-immediately"></a>立即執行備份

1. 若要立即執行備份，請在 VM 功能表中選取**Backup**[  >  **立即**備份]。

    ![執行備份](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. 在 [ **立即備份**] 中，使用行事曆控制項來選取要保留復原點的時間，> 和 **[確定]**。

    ![備份保留日](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. 入口網站通知可讓您知道已觸發的備份工作。 若要監視備份進度，請選取 [ **查看所有作業**]。

## <a name="back-up-from-the-recovery-services-vault"></a>從復原服務保存庫進行備份

遵循 [本文中的指示，藉](backup-azure-arm-vms-prepare.md) 由設定 Azure 備份復原服務保存庫，並在保存庫中啟用備份來啟用 Azure vm 的備份。

## <a name="next-steps"></a>後續步驟

- 如果您對這篇文章中的程序有任何疑問，請參閱[疑難排解指南](backup-azure-vms-troubleshoot.md)。
- [深入了解](backup-azure-manage-vms.md)管理備份。
