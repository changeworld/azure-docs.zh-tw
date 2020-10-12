---
title: 在 Azure 實驗室服務中設定 VM 的密碼 | Microsoft Docs
description: 了解如何在 Azure 實驗室服務的教室實驗室中設定和重設虛擬機器 (VM) 的密碼。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 49615b37875b8340f46ed900a1e7144256c7cb7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91334067"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>設定和管理虛擬機器集區 
本文示範如何執行下列工作：

- 增加實驗室中的虛擬機器 (VM) 數目
- 啟動所有 VM 或選取的 VM 
- 重設 VM

## <a name="update-the-lab-capacity"></a>更新實驗室容量
若要增加或減少實驗室容量 (實驗室中的虛擬機器數目)，請執行下列步驟：

1. 在 [虛擬機器集區] 頁面上，選取 [實驗室容量: &lt;number&gt; 個機器]。
2. 輸入您要在實驗室中擁有的新**VM 數目**。 此數目必須大於或等於實驗室中所註冊的使用者數目。 
3. 然後選取 [儲存]。 

    ![顯示 [實驗室容量] 視窗的螢幕擷取畫面，其中已選取 [實驗室中的最大電腦] 和 [儲存] 按鈕。](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 如果您增加了容量，便可以看到一或多個 VM 正在建立中。 如果您未在清單中看到新的 VM，請重新整理頁面。 

    ![正在建立 VM](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>啟動 VM

### <a name="start-ot-stop-all-vms"></a>啟動或停止所有 VM
1. 切換至 [虛擬機器集區] 頁面。 
2. 從工具列中選取 [全部啟動]。 

    ![[全部啟動] 按鈕](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 所有 VM 都啟動之後，您可以選取工具列上的 [全部停止] 按鈕來停止所有 VM。 

    ![[全部停止] 按鈕](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>啟動選取的 VM
有兩種方式可以啟動選取的 VM (一或多個)。 第一種方式是選取清單中的一或多個 VM，然後選取工具列上的 [啟動]。 

第二種方式是選取清單中的一或多個 VM，然後切換 [狀態] 資料行中的按鈕。 

![啟動選取的 VM](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

同樣地，您也可以藉由切換 [狀態] 資料行中的按鈕，或選取工具列上的 [停止]，來停止一或多個 VM。 

> [!NOTE]
> 當教育工作者開啟學生 VM 時，學生的配額不會受到影響。 使用者的配額會指定使用者可在排定的上課時間外使用的實驗室時數。 如需配額的詳細資訊，請參閱[為使用者設定配額](how-to-configure-student-usage.md?#set-quotas-for-users)。

## <a name="reset-vms"></a>重設 VM
若要重設一或多個 VM，請在清單中選取這些 VM，然後在工具列上選取 [重設]。 

![重設選取的 VM](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

在 [重設虛擬機器] 對話方塊中，選取 [重設]。 

![[重設 VM] 對話方塊](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>設定 VM 的密碼
實驗室擁有者 (授課者) 可以在建立實驗室時 (實驗室建立精靈)，或在 [範本] 頁面上建立實驗室之後，設定/重設 VM 的密碼。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在建立實驗室時設定密碼
實驗室擁有者 (授課者) 可以在實驗室建立精靈的 [虛擬機器認證] 頁面上，為實驗室中的 VM 設定密碼。

![[新增實驗室] 視窗](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

藉由啟用/停用此頁面上的 [對所有虛擬機器使用相同的密碼] 選項，授課者可以選擇為實驗室中的所有 VM 使用相同的密碼，也可以允許學生為其 VM 設定密碼。 根據預設，除了 Ubuntu 以外，所有 Windows 和 Linux 作業系統映像都會啟用此設定。 若此設定停用，學生將會在第一次嘗試連線到 VM 時，收到設定密碼的提示。 

### <a name="reset-password-later"></a>稍後重設密碼

1. 在實驗室的 [範本] 頁面上，選取工具列上的 [重設密碼]。 
1. 在 [重設密碼] 對話方塊中輸入密碼，然後選取 [重設密碼]。
    
    ![[設定密碼] 對話方塊](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>連線到學生 VM
如果符合下列條件，實驗室建立者 (授課者) 就可以連線到學生 VM： 

- 在建立實驗室時，已選取 [對所有虛擬機器使用相同的密碼] 選項
- VM 正在執行 

 若要連線到學生 VM，請將滑鼠停留在清單中的 VM 上，然後選取 [電腦] 按鈕。  

![[連線至學生 VM] 按鈕](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 當授課者啟動 VM 並與其連線時，學生配額不會受到影響。 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>將虛擬機器清單匯出至 CSV 檔案

1. 切換至 [虛擬機器集區] 索引標籤。
2. 選取工具列上的 [...] (省略號)，然後選取 [匯出 CSV]。 

    ![匯出虛擬機器清單](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>後續步驟
若要了解您 (身為實驗室擁有者) 可以設定的其他學生使用方式選項，請參閱下列文章：[設定學生使用方式](how-to-configure-student-usage.md)。

若要了解學生該如何為其 VM 重設密碼，請參閱[為教室實驗室 (學生) 中的虛擬機器設定或重設密碼](how-to-set-virtual-machine-passwords-student.md)。