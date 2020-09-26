---
title: 如何存取 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 瞭解如何註冊至教室實驗室、查看您可以存取的所有實驗室、啟動/停止實驗室中的 VM，以及連線至 VM。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: cd06bfc2dff27f1ef1bc84f01b001b8dbd282cf4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91334050"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>如何存取 Azure 實驗室服務中的教室實驗室
本文說明如何向教室實驗室註冊、檢視您可以存取的所有實驗室、啟動/停止實驗室中的 VM，以及連線至 VM。 

## <a name="register-to-the-lab"></a>向實驗室註冊

1. 瀏覽至教師提供給您的**註冊 URL**。 完成註冊之後，您不需要使用註冊 URL。 請改用 URL：[https://labs.azure.com](https://labs.azure.com)。 目前尚未支援 Internet Explorer 11。 

    ![向實驗室註冊](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. 使用學校帳戶登入服務，以完成註冊。 

    > [!NOTE]
    > 使用 Azure 實驗室服務時需要 Microsoft 帳戶。 如果您嘗試使用非 Microsoft 帳戶 (例如 Yahoo 或 Google 帳戶) 來登入入口網站，請依照指示來建立將連結至非 Microsoft 帳戶的 Microsoft 帳戶。 然後遵循步驟來完成註冊程序。 
1. 註冊之後，請確認您有看到可存取的實驗室虛擬機器。 

    ![可存取的 VM](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. 請等候虛擬機器準備就緒。 在 [VM] 圖格上，請注意下欄欄位：
    1. 在圖格頂端，您會看到**實驗室的名稱**。
    1. 在圖格右邊，您會看到代表 VM **作業系統 (OS)** 的圖示。 在此範例中為 Windows OS。 
    1. 您會在圖格底部看到用來啟動/停止 VM 和連線至 VM 的圖示/按鈕。 
    1. 在按鈕右邊，您會看到 VM 的狀態。 確認您看到的 VM 狀態為 [已停止]。

        ![處於已停止狀態的 VM](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>啟動或停止 VM
1. 選取第一個按鈕來**啟動** VM，如下圖所示。 這個程序需要一些時間。  

    ![啟動 VM](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. 確認 VM 的狀態已設定為 [執行中]。 

    ![處於執行中狀態的 VM](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    請注意，第一個按鈕的圖示會變更為代表**停止**作業。 您可以選取此按鈕來停止 VM。 

## <a name="connect-to-the-vm"></a>連接至 VM

1. 選取第二個按鈕 (如下圖所示)，以**連線**至實驗室的 VM。 

    ![連接到 VM](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. 執行下列其中一個步驟： 
    1. 針對 **Windows** 虛擬機器，將 **RDP** 檔案儲存至硬碟。 開啟 RDP 檔案以連線至虛擬機器。 使用教師提供給您的**使用者名稱**和**密碼**來登入機器。 
    3. 對於 **Linux** 虛擬機器，您可以使用 **SSH** 或 **RDP** (若已啟用) 進行連線。 如需詳細資訊，請參閱[啟用 Linux 機器的遠端桌面連線](how-to-enable-remote-desktop-linux.md)。 
    1. 如果您使用 **Mac** 連線至實驗室 VM，請依照下一節中的指示操作。 

## <a name="progress-bar"></a>進度列 
圖格上的進度列會顯示針對指派給您的[配額時數](how-to-configure-student-usage.md#set-quotas-for-users)所使用的時數。 此時間是除了實驗室的排程時間外額外分配給您的時間。 進度列的色彩和進度列底下的文字會依下列案例而有所不同：

- 如果某個課程正在進行中 (在課程的排程內)，進度列會呈現灰色，表示並未使用配額時數。 

    ![灰色的進度列](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- 如果未指派配額 (零小時)，則會顯示**僅在課程期間可用**的文字來取代進度列。 
    
    ![未設定配額時的狀態](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- 如果您**用完配額**，進度列的色彩會變成**紅色**。 

    ![紅色的進度列](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- 超出實驗室的排程時間，而且已使用一些配額時間時，進度列色彩會變成**藍色**。 

    ![藍色的進度列](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>檢視所有教室實驗室
向實驗室註冊之後，您可以採取下列步驟來檢視所有教室實驗室： 

1. 瀏覽至 [https://labs.azure.com](https://labs.azure.com)。 目前尚未支援 Internet Explorer 11。 
2. 使用您用來向實驗室註冊的使用者帳戶登入服務。 
3. 確認您看到您有權存取的所有實驗室。 

    ![檢視所有連結](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室擁有者身分設定及控制實驗室的使用方式](how-to-configure-student-usage.md)
 