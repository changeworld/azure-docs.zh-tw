---
title: 使用 Azure 實驗室服務設定道德入侵實驗室 |Microsoft Docs
description: 瞭解如何使用 Azure 實驗室服務來設定實驗室，以教授道德的駭客。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74133188"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>設定實驗室來教道德的駭客課程 
本文說明如何設定一個著重于道德入侵辯論的類別。 滲透測試 (由道德入侵社群所使用的作法) 會在有人嘗試取得系統或網路的存取權，以示範惡意攻擊者可能會利用的弱點時發生。 

在道德入侵課程中，學生可以學習防禦弱點的現代化技術。 每個學生都會有 Windows Server 主機虛擬機器，該虛擬機器會有兩個巢狀虛擬機器，其中一個具有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像，另一個則具有 [Kali Linux](https://www.kali.org/) 映像。 Metasploitable 虛擬機器會用於盜用目的，而 Kali 虛擬機器則提供執行鑑識調查工作所需之工具的存取。

本文有兩個主要區段。 第一節涵蓋如何建立教室實驗室。 第二節涵蓋如何使用已啟用的嵌套虛擬化和所需的工具和映射來建立範本機器。 在此情況下，電腦上的 Metasploitable 映射和 Kali Linux 映射已啟用 Hyper-v 來裝載映射。

## <a name="lab-configuration"></a>實驗室組態
若要設定此實驗室，您需要 Azure 訂用帳戶才能開始使用。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶，或使用現有的帳戶。 請參閱下列教學課程，以建立新的實驗室帳戶：[設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。

遵循[此教學](tutorial-setup-classroom-lab.md)課程來建立新的實驗室，然後套用下列設定：

| 虛擬機器大小 | Image |
| -------------------- | ----- | 
| 中（嵌套虛擬化） | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>範本機器 

建立範本機器之後，請啟動電腦並聯機到它，以完成下列三項主要工作。 
 
1. 設定電腦以進行嵌套虛擬化。 它會啟用所有適當的 windows 功能（例如 Hyper-v），並設定讓 Hyper-v 映射能夠與彼此和網際網路通訊的網路。
2. 設定[Kali](https://www.kali.org/) Linux 映射。 Kali 是一種 Linux 散發套件，其中包含用於滲透測試和安全性審核的工具。
3. 設定 Metasploitable 映射。 在此範例中，將會使用[Metasploitable3](https://github.com/rapid7/metasploitable3)映射。 建立此映射的目的是為了刻意擁有安全性弱點。

[實驗室服務道德駭客腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)提供自動化上述工作的腳本。

### <a name="prepare-template-machine-for-nested-virtualization"></a>準備用於嵌套虛擬化的範本電腦
依照[這篇文章](how-to-enable-nested-virtualization-template-vm.md)中的指示，準備您的範本虛擬機器以進行嵌套虛擬化。 

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>設定具有 Kali Linux 映射的嵌套虛擬機器
Kali 是一種 Linux 散發套件，其中包含用於滲透測試和安全性審核的工具。

1. 從[https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)下載映射。  
    1. 下載適用于 Hyper-v 的**Kali Linux hyper-v 64 位**。
    1. 解壓縮7z 檔案。  如果您還沒有 7 zip，請從[https://www.7-zip.org/download.html](https://www.7-zip.org/download.html)下載。 請記住解壓縮資料夾的位置，因為您稍後需要用到它。
2. 從 [系統管理工具] 中開啟 [ **Hyper-v 管理員**]。
1. 選取 [**動作**]，然後選取 [匯**入虛擬機器**]。 
1. 在 [匯**入虛擬機器**] 嚮導的 [**尋找資料夾**] 頁面上，選擇包含 Kali Linux 映射的解壓縮資料夾位置。

    ![尋找資料夾對話方塊](../media/class-type-ethical-hacking/locate-folder.png)
1. 在 [**選取虛擬機器**] 頁面上，選取 Kali Linux 映射。  在此情況下，映射為**kali-linux-2019.3-hyperv**。

    ![選取 Kali 影像](../media/class-type-ethical-hacking/select-kali-image.png)
1.  在 [**選擇匯入類型**] 頁面上，選取 [**複製虛擬機器（建立新的唯一識別碼）**]。

    ![選擇匯入類型](../media/class-type-ethical-hacking/choose-import-type.png)
1. 接受 [**選擇虛擬機器檔案的資料夾**] 的預設設定，並**選擇 [儲存虛擬硬碟的資料夾**] 頁面，然後選取 **[下一步]**。
1. 在 [連線**網路]** 頁面上，選擇本文的**準備範本**中稍早建立的**LabServicesSwitch** ，然後選取 **[下一步]**。

    ![[連線網路] 頁面](../media/class-type-ethical-hacking/connect-network.png)
1. 在 [**摘要**] 頁面上選取 **[完成]** 。 等待複製和匯入作業完成。 Kali Linux 虛擬機器現在會在 Hyper-v 中提供。
1. 從 [ **hyper-v 管理員**] 中，選擇 [**動作** -> ] [**開始**]，然後選擇 [**動作** -> **]** [連線] 以連線至虛擬機器。  
12. 預設使用者名稱為 `root`，密碼為 `toor`。 

    > [!NOTE]
    > 如果您需要解除鎖定影像，請按 CTRL 鍵並向上拖曳滑鼠。

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>設定具有 Metasploitable 映射的嵌套 VM  
Rapid7 Metasploitable 映射是刻意設定了安全性弱點的映射。 您將使用此影像來測試及尋找問題。 下列指示示範如何使用預先建立的 Metasploitable 映射。 不過，如果需要較新版本的 Metasploitable 映射，請參閱[https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3)。

1. 瀏覽至 [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)。 填寫表單以下載影像，然後選取 [**提交**] 按鈕。
1. 選取 [**立即下載 Metasploitable** ] 按鈕。
1. 下載 zip 檔案時，請將 zip 檔案解壓縮，並記住位置。
1. 將已解壓縮的 vmdk 檔案轉換成 vhdx 檔案，讓您可以搭配使用 Hyper-v。 若要這麼做，請以系統管理許可權開啟 PowerShell，並流覽至 vmdk 檔案所在的資料夾，並遵循下列指示：
    1. 下載[Microsoft 虛擬機器轉換器](https://www.microsoft.com/download/details.aspx?id=42497)，並在出現提示時執行 mvmc_setup .msi 檔案。
    1. 匯入 PowerShell 模組。  安裝模組的預設位置是 C:\Program Files\Microsoft 虛擬機器轉換器 \

        ```powershell
        Import-Module 'C:\Program Files\Microsoft Virtual Machine Converter\MvmcCmdlet.psd1'
        ```
    1. 將 vmdk 轉換成可供 Hyper-v 使用的 vhd 檔案。 這項作業可能需要幾分鐘的時間。
    
        ```powershell
        ConvertTo-MvmcVirtualHardDisk -SourceLiteralPath .\Metasploitable.vmdk -DestinationLiteralPath .\Metasploitable.vhdx -VhdType DynamicHardDisk -VhdFormat vhdx
        ```
    1. 將新建立的 metasploitable 複製到 C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\。 
1. 建立新的 Hyper-v 虛擬機器。
    1. 開啟 [ **Hyper-v 管理員**]。
    1. 選擇 [**動作** -> ] [**新增** -> ] [**虛擬機器**]。
    1. 在 [**新增虛擬機器] 嚮導**的 [**開始之前**] 頁面上，按 **[下一步]**。
    1. 在 [**指定名稱和位置**] 頁面上，針對 [**名稱**] 輸入**Metasploitable** ，然後選取 **[下一步]**。

        ![新增 VM 映射嚮導](../media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. 在 [**指定世代**] 頁面上，接受預設值，然後選取 **[下一步]**。
    1. 在 [**指派記憶體**] 頁面上，輸入**512 MB**作為**啟動記憶體**，然後選取 **[下一步]**。 

        ![指派記憶體頁面](../media/class-type-ethical-hacking/assign-memory-page.png)
    1. 在 [**設定網路**] 頁面上，將 [連線] 保持為 [**未連接**]。 您稍後將會設定網路介面卡。
    1. 在 [**連接虛擬硬碟]** 頁面上，選取 [**使用現有的虛擬硬碟**]。 流覽至在上一個步驟中建立之**metasploitable**檔案的位置，然後選取 **[下一步]**。 

        ![[連線虛擬網路磁片] 頁面](../media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. 在 [**完成新增虛擬機器嚮導]** 頁面上，選取 **[完成]**。
    1. 建立虛擬機器之後，請在 [Hyper-v 管理員] 中選取它。 尚未開啟電腦。  
    1. 選擇 [**動作** -> **設定**]。
    1. 在的 [ **Metasploitable 的設定**] 對話方塊中，選取 [**新增硬體**]。 
    1. 選取 [**傳統網路介面卡**]，然後選取 [**新增**]。

        ![[網路介面卡] 頁面](../media/class-type-ethical-hacking/network-adapter-page.png)
    1. 在 [**傳統網路介面卡**] 頁面上，選取 [ **LabServicesSwitch** ] 作為 [**虛擬交換器**] 設定，然後選取 **[確定]**。 在 [**適用于嵌套虛擬化的準備範本**] 區段中準備 hyper-v 的範本機器時，已建立 LabServicesSwitch。

        ![[傳統網路介面卡] 頁面](../media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable 映射現已準備好可供使用。 從 [ **hyper-v 管理員**] 中，選擇 [**動作** -> ] [**開始**]，然後選擇 [**動作** -> **]** [連線] 以連線至虛擬機器。  預設的使用者名稱是**msfadmin** ，密碼則是**msfadmin**。 


此範本現在已更新，並具有道德入侵入侵測試類別所需的影像、具有工具可執行滲透測試的影像，以及另一個具有探索安全性弱點的映射。 範本映射現在可以發行至類別。 選取 [範本] 頁面上的 [**發佈**] 按鈕，將範本發佈至實驗室。
  

## <a name="cost"></a>成本  
如果您想要估計此實驗室的成本，可以使用下列範例： 
 
對於25名學生的課程，其中有20小時的排程類別時間，以及10小時的家庭作業或指派配額，實驗室的價格如下： 

25名學生 * （20 + 10）小時 * 55 實驗室單位 * 每小時0.01 美元 = 412.50 美元。 

如需價格的詳細資訊，請參閱[Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論
本文逐步解說建立適用于道德攻擊類別的實驗室的步驟。 其中包含設定嵌套虛擬化的步驟，以在主機虛擬機器內建立兩部虛擬機器以進行滲透測試。

## <a name="next-steps"></a>後續步驟
下一步是設定任何實驗室的一般步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)。 

