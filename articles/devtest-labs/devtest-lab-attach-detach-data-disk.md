---
title: 在 Azure DevTest Labs 中將資料磁碟連結至虛擬機器或中斷連結
description: 了解如何在 Azure DevTest Labs 中將資料磁碟連結至虛擬機器或中斷連結
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 222bf06dc07cc2538bc0512b4a41b7bd91bc1ebf
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653914"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中將資料磁碟連結至虛擬機器或中斷連結
[Azure 受控磁碟](../virtual-machines/managed-disks-overview.md)會管理與虛擬機器資料磁碟相關聯的儲存體帳戶。 使用者將新的資料磁碟連結至 VM、指定所需的磁碟類型和大小，Azure 就會自動建立和管理磁碟。 之後資料磁碟可以從 VM 中斷連結，稍後再重新連結至相同的 VM，或連結至屬於相同使用者的不同 VM。

這項功能對於管理每部個別虛擬機器外部的儲存體或軟體而言很方便。 如果儲存體或軟體已存在於資料磁碟內，可以輕鬆地連結、中斷連結然後再重新連結至擁有該資料磁碟之使用者所擁有的任何 VM。

## <a name="attach-a-data-disk"></a>連接資料磁碟
將資料磁碟連結至 VM 之前，請檢閱下列提示︰

- VM 的大小會控制您可以連結的資料磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](../virtual-machines/sizes.md)。
- 您只能將資料磁碟連結至執行中的 VM。 請確認 VM 正在執行，然後再嘗試連結資料磁碟。

### <a name="attach-a-new-disk"></a>附加新的磁碟
請遵循這些步驟來建立新的受控資料磁碟，並將其連結至 Azure DevTest Labs 中的 VM。

1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選取所需的實驗室。 
1. 從**我的虛擬機器**清單中，選取執行中的 VM。
1. 從左側功能表選取 [磁碟]****。
1. 選擇 [連結新項目]**** 以建立新的資料磁碟，並將它連結至 VM。

    ![將新的資料磁碟連結至虛擬機器](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. 藉由輸入資料磁碟名稱、類型和大小，來完成 [連結新磁碟]**** 窗格。

    ![完成「連結新磁碟」表單](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. 選取 [確定]。

經過幾分鐘之後，新的資料磁片會建立並連結至 VM，並顯示在該 VM 的 **資料磁片** 清單中。

### <a name="attach-an-existing-disk"></a>連接現有磁碟
請遵循這些步驟將現有可用的資料磁碟重新連結至執行中的 VM。 

1. 選取您要將資料磁碟重新連結至其中的執行中 VM。
1. 從左側功能表選取 [磁碟]****。
1. 選取 [連結新項目]**** 以將可用的資料磁碟連結至 VM。

    ![將現有的資料磁碟連結至虛擬機器](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. 從 [連結現有磁碟]**** 窗格中，選取 [確定]。

    ![將現有的資料磁碟連結至虛擬機器](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

經過幾分鐘之後，資料磁片會連接到 VM，並顯示在該 VM 的 **資料磁片** 清單中。

## <a name="detach-a-data-disk"></a>卸離資料磁碟
當您不再需要某個連結至 VM 的資料磁碟時，可以輕鬆將它中斷連結。 中斷連結會從 VM 移除磁碟，但是將它保留在儲存體中以供後續使用。

如果您想要再次使用磁碟上現有的資料，您可以將磁碟重新連結至相同或其他虛擬機器。

### <a name="detach-from-the-vms-management-pane"></a>從 VM 的管理窗格中斷連結
1. 從虛擬機器的清單中，選取具有已連結資料磁碟的 VM。
1. 從左側功能表選取 [磁碟]****。
1. 從 **資料磁片**清單中，選取您要卸離的資料磁片。

    ![選取虛擬機器的資料磁碟](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. 從磁碟的詳細資料窗格頂端選取 [中斷連結]****。

    ![卸離資料磁碟](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. 選取 [是]**** 以確認您想要中斷連結資料磁碟。

磁碟已中斷連結，且可用於連結至另一個 VM。 
### <a name="detach-from-the-labs-main-pane"></a>從實驗室的主窗格中斷連結
1. 在您的實驗室主窗格中，選取 [我的資料磁碟]****。
1. 以滑鼠右鍵按一下您要卸離的資料磁片–或選取其省略號 (**...**) –然後選擇 [卸 **離**]。

    ![卸離資料磁碟](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. 選取 [是]**** 以確認您想要中斷連結。

   > [!NOTE]
   > 如果資料磁碟已中斷連結，您可以藉由選取 [刪除]****，選擇從可用的資料磁碟清單將它移除。
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>升級非受控資料磁碟
如果您有現有的 VM 使用非受控資料磁碟，您可以輕鬆地將 VM 轉換為使用受控磁碟。 此程序會轉換 OS 磁碟和任何附加的資料磁碟。

若要升級非受控資料磁碟，請遵循本文中所述的步驟，從非受控 VM [中斷連結資料磁碟](#detach-a-data-disk)。 然後，[重新連結磁碟](#attach-an-existing-disk)至受控 VM，以自動將資料磁碟從非受控升級為受控。

## <a name="next-steps"></a>後續步驟
了解如何管理[可宣告虛擬機器](devtest-lab-add-claimable-vm.md#unclaim-a-vm)的資料磁碟。
