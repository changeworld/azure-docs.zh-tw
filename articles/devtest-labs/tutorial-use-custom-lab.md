---
title: 在 Azure DevTest Labs 中存取實驗室 | Microsoft Docs
description: 在本教學課程中，您會存取使用 Azure DevTest Labs 建立的實驗室、認領虛擬機器並加以使用，以及取消認領虛擬機器。
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: b4477e0b98ef534b8170ee297edf88ac6fa62dd7
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "85476439"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>教學課程：在 Azure DevTest Labs 中存取實驗室
在本教學課程中，您會使用[教學課程：在 Azure DevTest Labs 中建立實驗室](tutorial-create-custom-lab.md)中建立的實驗室。

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 宣告實驗室中的虛擬機器 (VM)
> * 連接至 VM
> * 取消認領 VM

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="access-the-lab"></a>存取實驗室

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側功能表上，選取 [所有資源]  。 
3. 選取 [DevTest Labs]  作為資源類型。 
4. 選取實驗室。 

    ![選取實驗室](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>認領 VM

1. 在 [可認領虛擬機器]  清單中，選取 [...]  (省略符號)，然後選取 [認領機器]  。

    ![認領虛擬機器](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. 確認您有在 [我的虛擬機器]  清單中看到虛擬機器。

    ![我的虛擬機器](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>連接至 VM

1. 在清單中選取您的 VM。 您會看到 VM 的**虛擬機器頁面**。 選取工具列上的 [連線]  。

    ![連線至虛擬機器](./media/tutorial-use-custom-lab/connect-button.png)
2. 將下載的 **RDP** 檔案儲存在您的硬碟，並用它來連線到虛擬機器。 指定在上一節中建立 VM 時提及的使用者名稱和密碼。 

    若要連線至 Linux VM，則必須啟用 VM 的 SSH 和/或 RDP 存取。 如需透過 RDP 連線到 Linux VM 的步驟，請參閱[安裝和設定遠端桌面來連線至 Azure 中的 Linux VM](../virtual-machines/linux/use-remote-desktop.md)。 

    > [!NOTE]
    > 有其他方式可以進入 VM 的 [虛擬機器] 頁面。 以下說明其中一部分： 
    > 
    > 1. 搜尋您訂用帳戶中的所有 VM。 在 VM 清單中選取您的 VM，以進入 [虛擬機器]  頁面。
    > 2. 瀏覽至資源群組的 [資源群組]  頁面。 然後，從資源群組的資源清單中選取您的 VM，以進入 [虛擬機器]  頁面。 
    >
    > 在您使用這些選項進入的 [虛擬機器] 頁面上，請不要使用工具列上的 [連線] 按鈕。 此時，請瀏覽至 [DevTest Labs] 頁面中的 [虛擬機器] 頁面 (如本文所說明)，然後使用工具列上的 [連線] 按鈕。


## <a name="unclaim-the-vm"></a>取消認領 VM
當您使用完 VM 之後，請依照下列步驟取消認領 VM： 

1. 在 [虛擬機器] 頁面的工具列上，選取 [取消認領]  。 

    ![取消認領 VM](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. VM 會先關機然後再取消認領。 您可以在通知中查看此作業的狀態。  
3. 按一下頂端麵包屑導航功能表中的實驗室名稱，以瀏覽回 DevTest Lab 頁面。 
    
    ![巡覽回實驗室](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. 確認您在底部的 [可認領虛擬機器]  清單中看到 VM。

    
## <a name="next-steps"></a>後續步驟
本教學課程會示範如何存取和使用透過 Azure DevTest Labs 建立的實驗室。 若要深入了解如何存取和使用實驗室中的 VM，請參閱 

> [!div class="nextstepaction"]
> [作法：在實驗室中使用 VM](devtest-lab-add-vm.md)

