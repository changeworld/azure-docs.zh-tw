---
title: 為 Azure 實驗室服務中的 VM 設置密碼 |微軟文檔
description: 瞭解如何在 Azure 實驗室服務的教室實驗室中設置和重置虛擬機器 （VM） 的密碼。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933801"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>設定和管理虛擬機器集區 
本文介紹如何執行以下任務：

- 增加實驗室中的虛擬機器 （VM） 數量
- 啟動所有 VM 或所選 VM 
- 重置 VM

## <a name="update-the-lab-capacity"></a>更新實驗室容量
要增加或減少實驗室容量（實驗室中的虛擬機器數量），執行以下步驟：

1. 在**虛擬機器池**頁上，選擇 **"實驗室容量：&lt;數位&gt;電腦**"。
2. 在實驗室中輸入所需的新**VM 數量**。 此數位必須大於或等於實驗室中註冊的使用者數。 
3. 然後，選擇 **"保存**"。 

    ![啟動所有按鈕](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. 如果增加了容量，可以看到正在創建的 VM 或 VM。 如果在清單中看不到新的 VM，請刷新頁面。 

    ![正在創建的 VM](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>啟動 VM

### <a name="start-ot-stop-all-vms"></a>開始停止所有 VM
1. 切換到**虛擬機器池**頁面。 
2. 從工具列**中選擇"全部開始**"。 

    ![啟動所有按鈕](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. 啟動所有 VM 後，您可以通過選擇工具列上的"**停止所有"** 按鈕來停止所有 VM。 

    ![停止所有按鈕](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>啟動所選 VM
有兩種方法可以啟動選定的 VM（一個或多個）。 第一種方法是在清單中選擇 VM 或 VM，然後在工具列上選擇 **"開始"。** 

第二種方法是在清單中選擇一個或多個 VM，並切換 **"狀態"** 列中的按鈕。 

![啟動所選 VM](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

同樣，您可以通過切換 **"狀態**"列中的按鈕或選擇工具列上的 **"停止"** 來停止一個或多個 VM。 

> [!NOTE]
> 當教育者打開學生 VM 時，學生的配額不受影響。 使用者的配額指定在計畫類時間之外可供使用者可用的實驗室小時數。 有關配額的詳細資訊，請參閱[為使用者設置配額](how-to-configure-student-usage.md?#set-quotas-for-users)。

## <a name="reset-vms"></a>重置 VM
要重置一個或多個 VM，請在清單中選擇它們，然後在工具列上選擇 **"重置**"。 

![重置選定的 VM](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

在 **"重置虛擬機器"對話方塊中**，選擇 **"重置**"。 

![重置 VM 對話方塊](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>為 VM 設置密碼
實驗室擁有者（教師）可以在創建實驗室時（實驗室創建嚮導）或**在範本**頁面上創建實驗室後設置/重置 VM 的密碼。 

### <a name="set-password-at-the-time-of-lab-creation"></a>在實驗室創建時設置密碼
實驗室擁有者（教師）可以在實驗室創建嚮導的**虛擬機器憑據**頁上為實驗室中的 VM 設置密碼。

![[新增實驗室] 視窗](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

通過啟用/禁用此頁面**上所有虛擬機器使用相同的密碼**選項，教師可以選擇對實驗室中的所有 VM 使用相同的密碼，或者允許學生為其 VM 設置密碼。 預設情況下，此設置適用于除 Ubuntu 之外的所有 Windows 和 Linux 作業系統映射。 禁用此設置後，將提示學生在首次嘗試連接到 VM 時設置密碼。 

### <a name="reset-password-later"></a>稍後重置密碼

1. 在實驗室的**範本**頁上，選擇工具列上的 **"重置密碼**"。 
1. 在 **"重置密碼"** 對話方塊中，輸入密碼，然後選擇 **"重置密碼**"。
    
    ![設置密碼對話方塊](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>連接到學生 VM
如果滿足以下條件，實驗室建立者（講師/教授）可以連接到學生 VM： 

- 創建實驗室時選擇了 **"對所有虛擬機器使用相同的密碼**"選項
- VM 正在運行 

 要連接到學生 VM，請將滑鼠懸停在清單中的 VM 上，然後選擇電腦按鈕。  

![連接到學生 VM 按鈕](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> 當教授啟動 VM 並連接到 VM 時，學生配額不受影響。 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>將虛擬機器清單匯出到 CSV 檔

1. 切換到**虛擬機器池**選項卡。
2. 在工具列上選擇 **...（** 省略號），然後選擇 **"匯出 CSV**"。 

    ![虛擬機器匯出清單](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>後續步驟
要瞭解您（作為實驗室擁有者）可以配置的其他學生使用選項，請參閱以下文章：[配置學生使用方式](how-to-configure-student-usage.md)。

要瞭解學生如何重置 VM 的密碼，請參閱[在教室實驗室（學生）中設置或重置虛擬機器的密碼](how-to-set-virtual-machine-passwords-student.md)。