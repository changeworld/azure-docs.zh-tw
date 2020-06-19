---
title: 存取 Azure 實驗室服務中的教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會在由教師設定的教室實驗室中存取虛擬機器。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 241e20bdaf68c8660f4d78891c2167b56bbbfca0
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/17/2020
ms.locfileid: "84889451"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>教學課程：存取 Azure 實驗室服務中的教室實驗室
在本教學課程中，您會以學生的身分連線到教室實驗室中的虛擬機器 (VM)。 

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 向實驗室註冊
> * 啟動 VM
> * 連接至 VM

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
    1. 圖格上的進度列會顯示針對指派給您的[配額時數](how-to-configure-student-usage.md#set-quotas-for-users)所使用的時數。 此時間是除了實驗室的排程時間外額外分配給您的時間。 
    1. 您會在圖格底部看到用來啟動/停止 VM 和連線至 VM 的圖示/按鈕。 
    1. 在按鈕右邊，您會看到 VM 的狀態。 確認您看到的 VM 狀態為 [已停止]。 

        ![處於已停止狀態的 VM](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>啟動 VM
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

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已可以使用教師提供給您的註冊連結來存取教室實驗室。

身為實驗室擁有者，您可以檢視已向實驗室註冊的人員，並追蹤 VM 的使用情況。 前進到下一個教學課程，了解如何追蹤實驗室的使用情況：

> [!div class="nextstepaction"]
> [追蹤實驗室的使用情況](tutorial-track-usage.md) 
