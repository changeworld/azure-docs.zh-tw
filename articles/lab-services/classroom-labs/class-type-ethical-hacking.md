---
title: 使用 Azure 實驗室服務設置道德駭客實驗室 |微軟文檔
description: 瞭解如何使用 Azure 實驗室服務設置實驗室來教授道德駭客。
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
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 2b600edc4c360a2b2990be34e44bb8fbd1c8f721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133188"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>設置實驗室教授道德駭客課程 
本文介紹如何設置一個側重于道德駭客的取證方面的類。 滲透測試 (由道德入侵社群所使用的作法) 會在有人嘗試取得系統或網路的存取權，以示範惡意攻擊者可能會利用的弱點時發生。 

在道德入侵課程中，學生可以學習防禦弱點的現代化技術。 每個學生都會有 Windows Server 主機虛擬機器，該虛擬機器會有兩個巢狀虛擬機器，其中一個具有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像，另一個則具有 [Kali Linux](https://www.kali.org/) 映像。 Metasploitable 虛擬機器會用於盜用目的，而 Kali 虛擬機器則提供執行鑑識調查工作所需之工具的存取。

本文有兩個主要部分。 第一部分介紹如何創建課堂實驗室。 第二部分介紹如何在啟用嵌套虛擬化以及使用所需的工具和映射創建範本電腦。 在這種情況下，在啟用 Hyper-V 以承載映射的電腦上，電腦可處理映射和 Kali Linux 映射。

## <a name="lab-configuration"></a>實驗室組態
要設置此實驗，需要 Azure 訂閱才能開始。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 獲取 Azure 訂閱後，可以在 Azure 實驗室服務中創建新的實驗室帳戶或使用現有帳戶。 有關創建新實驗室帳戶，請參閱以下教程：[設置實驗室帳戶的教程](tutorial-setup-lab-account.md)。

按照[本教程](tutorial-setup-classroom-lab.md)創建新實驗室，然後應用以下設置：

| 虛擬機器大小 | 映像 |
| -------------------- | ----- | 
| 中等（嵌套虛擬化） | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>範本機 

創建範本電腦後，啟動電腦並連接到它以完成以下三個主要任務。 
 
1. 為嵌套虛擬化設置電腦。 它支援所有適當的視窗功能，如 Hyper-V，並設置網路，使 Hyper-V 圖像能夠相互通信和互聯網。
2. 設置[卡利](https://www.kali.org/)Linux 映射。 Kali 是一個 Linux 發行版本，包括用於滲透測試和安全審核的工具。
3. 設置可工作圖像。 在此示例中，將使用[Metasploit3](https://github.com/rapid7/metasploitable3)圖像。 創建此映射的目的是故意存在安全性漏洞。

[實驗室服務道德駭客腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)提供了自動執行上述任務的腳本。

### <a name="prepare-template-machine-for-nested-virtualization"></a>為嵌套虛擬化準備範本電腦
請按照[本文](how-to-enable-nested-virtualization-template-vm.md)中的說明為嵌套虛擬化準備範本虛擬機器。 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>使用卡利 Linux 映射設置嵌套虛擬機器
Kali 是一個 Linux 發行版本，包括用於滲透測試和安全審核的工具。

1. 從[https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)下載圖像。  
    1. 下載**Kali Linux Hyper-V 64 位**，用於超 V。
    1. 提取 .7z 檔。  如果您還沒有 7 個 zip，請從[https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)下載。 記住提取的資料夾的位置，因為稍後需要它。
2. 從管理工具打開**超 V 管理器**。
1. 選擇**操作**，然後選擇 **"導入虛擬機器**"。 
1. 在 **"導入虛擬機器"** 嚮導的 **"查找資料夾**"頁上，選擇保存 Kali Linux 映射的提取資料夾的位置。

    ![查找資料夾對話方塊](../media/class-type-ethical-hacking/locate-folder.png)
1. 在 **"選擇虛擬機器"** 頁上，選擇卡利 Linux 映射。  在這種情況下，圖像是**kali-linux-2019.3-hyperv**。

    ![選擇卡利圖像](../media/class-type-ethical-hacking/select-kali-image.png)
1.  在 **"選擇導入類型"** 頁上，選擇 **"複製虛擬機器"（創建新的唯一 ID）。**

    ![選擇導入類型](../media/class-type-ethical-hacking/choose-import-type.png)
1. 接受"**為虛擬機器檔選擇資料夾"** 的預設設置，**然後選擇"資料夾以存儲虛擬硬碟"** 頁，然後選擇 **"下一步**"。
1. 在 **"連接網路"** 頁上，選擇本文的"**準備範本"** 部分中較早創建的**LabServicesSwitch，** 然後選擇 **"下一步**"。

    ![連接網路頁面](../media/class-type-ethical-hacking/connect-network.png)
1. 在 **"摘要"** 頁上選擇 **"完成**"。 等待，直到複製和導入操作完成。 Kali Linux 虛擬機器現在將在 Hyper-V 中提供。
1. 從**Hyper-V 管理器**中，選擇 **"操作** -> **開始"，** 然後選擇 **"操作** -> **連接**"以連接到虛擬機器。  
12. 預設使用者名稱為 `root`，密碼為 `toor`。 

    > [!NOTE]
    > 如果需要解鎖圖像，請按 CTRL 鍵向上拖動滑鼠。

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>使用可分層映射設置嵌套 VM  
Rapid7 可美用圖像是專門配置安全性漏洞的圖像。 您將使用此圖像來測試和查找問題。 以下說明演示如何使用預先創建的 Metasploit 圖像。 但是，如果需要較新版本的 Metasploit 圖像，請參閱[https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)。

1. 導航到[https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)。 填寫表單以下載圖像並選擇"**提交**"按鈕。
1. 選擇"**立即下載可使用"** 按鈕。
1. 下載 ZIP 檔案時，提取 ZIP 檔案並記住位置。
1. 將提取的 vmdk 檔轉換為 vhdx 檔，以便可以使用 Hyper-V。 為此，請打開具有管理許可權的 PowerShell 並導航到 vmdk 檔所在的資料夾，並按照以下說明操作：
    1. 下載[微軟虛擬機器轉換器](https://www.microsoft.com/download/details.aspx?id=42497)，並在提示時運行mvmc_setup.msi檔。
    1. 匯入 PowerShell 模組。  安裝模組的預設位置是 C：[程式檔]微軟虛擬機器轉換器*

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. 將 vmdk 轉換為 Vhd 檔，該檔可用於 Hyper-V。 這項作業可能需要幾分鐘的時間。
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. 將新創建的美用.vhdx 複製到 C：\使用者\公共\文檔\Hyper-V\虛擬硬碟*。 
1. 創建新的超 V 虛擬機器。
    1. 打開**超 V 管理器**。
    1. 選擇**操作** -> **新** -> **虛擬機器**。
    1. 在 **"開始之前**"新**虛擬機器嚮導**的頁面上，按一下 **"下一步**"。
    1. 在 **"指定名稱和位置**"頁上，輸入**名稱**的 **"可使用**"，然後選擇 **"下一步**"。

        ![新的 VM 映射嚮導](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. 在 **"指定生成"** 頁上，接受預設值，然後選擇 **"下一步**"。
    1. 在 **"分配記憶體"** 頁上，為**啟動記憶體**輸入**512 MB，** 然後選擇 **"下一步**"。 

        ![指派記憶體頁面](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. 在 **"配置網路"** 頁上，將連接保留為 **"未連接**"。 稍後將設置網路介面卡。
    1. 在 **"連接虛擬硬碟"** 頁上，選擇 **"使用現有虛擬硬碟**"。 流覽到上一步中創建的**美用.vhdx**檔的位置，然後選擇 **"下一步**"。 

        ![連接虛擬網路磁片頁面](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. 在 **"完成新虛擬機器嚮導"** 頁上，然後選擇 **"完成**"。
    1. 創建虛擬機器後，請在 Hyper-V 管理器中選擇它。 還別打開機器。  
    1. 選擇**操作** -> **設置**。
    1. 在"可使用 **"對話方塊的"設置**"上，選擇 **"添加硬體**"。 
    1. 選擇**舊網路介面卡**，然後選擇 **"添加**"。

        ![網路介面卡頁面](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. 在 **"舊版網路介面卡**"頁上，為**虛擬交換器**設置選擇**LabServicesSwitch，** 然後選擇 **"確定**"。 LabServicesSwitch 是在"**準備嵌套虛擬化範本"** 部分中為 Hyper-V 準備範本時創建的。

        ![舊網路介面卡頁面](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. 可使用圖像現已準備就緒。 從**Hyper-V 管理器**中，選擇 **"操作** -> **開始"，** 然後選擇 **"操作** -> **連接**"以連接到虛擬機器。  預設使用者名是**msfadmin，** 密碼是**msfadmin**。 


該範本現已更新，並具有道德駭客滲透測試類所需的圖像、具有執行滲透測試的工具的圖像以及要發現的另一個具有安全性漏洞的圖像。 範本映射現在可以發佈到類。 選擇範本頁上的 **"發佈"** 按鈕，將範本發佈到實驗室。
  

## <a name="cost"></a>成本  
如果要估計本實驗的成本，可以使用以下示例： 
 
對於有 25 名學生的班級，有 20 小時的上課時間和 10 小時的家庭作業或作業配額，實驗室的價格是： 

25 名學生 = （20 + 10） 小時 = 55 個實驗室單位 = 每小時 0.01 USD = 412.50 USD。 

有關定價的詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文介紹了創建道德駭客類實驗室的步驟。 它包括設置嵌套虛擬化的步驟，用於在主機虛擬機器內創建兩個虛擬機器以進行穿透式測試。

## <a name="next-steps"></a>後續步驟
設置任何實驗室通常採取以下步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設置配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設置計畫](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [電子郵件註冊連結給學生](how-to-configure-student-usage.md#send-invitations-to-users)。 

