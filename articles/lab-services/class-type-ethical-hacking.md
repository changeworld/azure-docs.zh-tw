---
title: 使用 Azure 實驗室服務設定道德入侵實驗室 |Microsoft Docs
description: 瞭解如何使用 Azure 實驗室服務設定實驗室，以教授道德入侵。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 97cdf13f39cc73ee7f35fb402229469195f1456c
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616417"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>設定實驗室來教授道德入侵類別

本文說明如何設定著重于道德入侵之辯論的類別。 滲透測試 (由道德入侵社群所使用的作法) 會在有人嘗試取得系統或網路的存取權，以示範惡意攻擊者可能會利用的弱點時發生。

在道德入侵課程中，學生可以學習防禦弱點的現代化技術。 每個學生都會有 Windows Server 主機虛擬機器，該虛擬機器會有兩個巢狀虛擬機器，其中一個具有 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映像，另一個則具有 [Kali Linux](https://www.kali.org/) 映像。 Metasploitable 虛擬機器會用於盜用目的，而 Kali 虛擬機器則提供執行鑑識調查工作所需之工具的存取。

本文有兩個主要區段。 第一節說明如何建立教室實驗室。 第二節說明如何建立已啟用嵌套虛擬化的範本電腦，以及所需的工具和映射。 在此案例中，已啟用 Hyper-v 的電腦上的 Metasploitable 映射和 Kali Linux 映射可裝載映射。

## <a name="lab-configuration"></a>實驗室組態

若要設定此實驗室，您需要 Azure 訂用帳戶才能開始進行。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。 取得 Azure 訂用帳戶之後，您可以在 Azure 實驗室服務中建立新的實驗室帳戶，或使用現有的帳戶。 請參閱下列教學課程，以建立新的實驗室帳戶： [設定實驗室帳戶的教學](tutorial-setup-lab-account.md)課程。

遵循 [本教學](tutorial-setup-classroom-lab.md) 課程來建立新的實驗室，然後套用下列設定：

| 虛擬機器大小 | 影像 |
| -------------------- | ----- |
| 中型 (嵌套虛擬化)  | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>範本電腦

建立範本電腦之後，請啟動電腦並聯機到該電腦，以完成下列三項主要工作。

1. 設定電腦以進行嵌套式虛擬化。 它會啟用所有適當的 windows 功能（例如 Hyper-v），並將 Hyper-v 映射的網路功能設定為能夠彼此通訊，以及與網際網路通訊。
2. 設定 [Kali](https://www.kali.org/) Linux 映射。 Kali 是一種 Linux 發行版本，其中包含滲透測試和安全性審核的工具。
3. 設定 Metasploitable 映射。 在此範例中，將會使用 [Metasploitable3](https://github.com/rapid7/metasploitable3) 映射。 這是為了刻意有安全性弱點而建立的映射。

本文的其餘部分將討論完成上述工作的手動步驟。  或者，您也可以執行 [實驗室服務的 Hyper-v 腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) 和 [實驗室服務的道德入侵腳本](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking)。

### <a name="prepare-template-machine-for-nested-virtualization"></a>準備範本電腦以進行嵌套虛擬化

遵循指示來 [啟用「嵌套虛擬化](how-to-enable-nested-virtualization-template-vm.md) 」，以準備您的範本虛擬機器以進行嵌套式虛擬化。

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>使用 Kali Linux 映射設定嵌套的虛擬機器

Kali 是一種 Linux 發行版本，其中包含滲透測試和安全性審核的工具。

1. 從具冒犯性的 [安全性 Kali LINUX VM 映射](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/)下載映射。  請記住下載頁面上注明的預設使用者名稱和密碼。
    1. 下載適用于 VMware 的 **Kali Linux VMware 64 位 (.7z)** 映射。
    1. 將 .7z 檔案解壓縮。  如果您還沒有 7 zip，請從下載 [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) 。 請記住解壓縮資料夾的位置，因為您稍後將需要它。
1. 將解壓縮的 vmdk 檔案轉換成 vhdx 檔案，讓您可以將 vhdx 檔案與 Hyper-v 搭配使用。 有數個工具可用來將 VMware 映射轉換成 Hyper-v 映射。  我們將使用 [STARWIND V2V 轉換器](https://www.starwindsoftware.com/starwind-v2v-converter)。  若要下載，請參閱 [STARWIND V2V 轉換器下載頁面](https://www.starwindsoftware.com/starwind-v2v-converter#download)。
    1. 開始 **STARWIND V2V 轉換器**。
    1. 在 [ **選取要轉換之影像的位置** ] 頁面上，選擇 [ **本機** 檔案]。  選取 [下一步]。
    1. 在 [ **來源映射** ] 頁面上，流覽至並選取在上一個步驟中解壓縮的 Kali Linux vmdk 檔案，以取得 **檔案名** 設定。  檔案的格式會是 Kali-Linux-{version}-vmware-amd64。  選取 [下一步]。
    1. 在 [ **選取目的地映射的位置**] 上，選擇 [ **本機** 檔案]。  選取 [下一步]。
    1. 在 [ **選取目的地映射格式** ] 頁面上，選擇 [ **VHD/VHDX**]。  選取 [下一步]。
    1. 在 [ **選取 VHD/VHDX 映射格式的選項** ] 頁面上，選擇 [ **VHDX 可成長映射**]。  選取 [下一步]。
    1. 在 [ **選取目的地檔案名** ] 頁面上，接受預設的檔案名。  選取 [轉換]。
    1. 在 [ **轉換** ] 頁面上，等候影像轉換。  這可能需要數分鐘的時間。  當轉換完成時，請選取 **[完成]** 。
1. 建立新的 Hyper-v 虛擬機器。
    1. 開啟 [ **Hyper-v 管理員**]。
    1. 選擇[  ->  **新增**  ->  **虛擬機器**] 動作。
    1. 在 [**新增虛擬機器] 嚮導** 的 [在 **您開始前**] 頁面上，選取 **[下一步**]。
    1. 在 [ **指定名稱和位置** ] 頁面上，輸入 **Kali-Linux** 作為 **名稱**，然後選取 **[下一步]**。
    1. 在 [ **指定世代** ] 頁面上，接受預設值，然後選取 **[下一步]**。
    1. 在 [ **指派記憶體** ] 頁面上，輸入 **2048 MB** 作為 [ **啟動記憶體**]，然後選取 **[下一步]**。
    1. 在 [ **設定網路** ] 頁面上，將 [連接] 保持為 [ **未連線**]。 您稍後會設定網路介面卡。
    1. 在 [ **連接虛擬硬碟]** 頁面上，選取 [ **使用現有的虛擬硬碟**]。 流覽至在上一個步驟中建立的 **Kali-Linux-{version}-vmware-amd64 .vmdk** 檔案的位置，然後選取 **[下一步]**。
    1. 在 [ **完成新增虛擬機器]** 頁面上，選取 **[完成]**。
    1. 建立虛擬機器之後，請在 Hyper-v 管理員中選取它。 尚未開啟電腦。  
    1. 選擇 [**動作**  ->  **設定**]。
    1. 在的 [ **Kali-Linux 的設定** ] 對話方塊中，選取 [ **新增硬體**]。
    1. 選取 [ **傳統網路介面卡**]，然後選取 [ **新增**]。
    1. 在 [**傳統網路介面卡**] 頁面上，選取 **虛擬交換器** 設定的 [ **LabServicesSwitch** ]，然後選取 **[確定]**。 在「 **嵌套虛擬化準備範本** 」一節中準備 hyper-v 的範本機器時，就會建立 LabServicesSwitch。
    1. Kali-Linux 映射現在已準備好可供使用。 從 [ **hyper-v 管理員**] 中，選擇 [**動作**  ->  **開始**]，然後選擇 [**動作** 連線]  ->  以連線至虛擬機器。  預設使用者名稱是 **kali** ，而密碼為 **kali**。

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>使用 Metasploitable 映射設定嵌套 VM  

Rapid7 Metasploitable 映射是刻意設定有安全性弱點的影像。 您將使用此映射來測試和尋找問題。 下列指示說明如何使用預先建立的 Metasploitable 映射。 但是，如果需要較新版本的 Metasploitable 映射，請參閱 [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) 。

1. 下載 Metasploitable 映射。
    1. 瀏覽至 [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html)。 填寫表單以下載影像，然後選取 [ **提交** ] 按鈕。
    2. 選取 [ **立即下載 Metasploitable** ] 按鈕。
    3. 下載 zip 檔案時，請將 zip 檔案解壓縮，並記住 Metasploitable .vmdk 檔案的位置。
1. 將解壓縮的 vmdk 檔案轉換成 vhdx 檔案，讓您可以將 vhdx 檔案與 Hyper-v 搭配使用。 有數個工具可用來將 VMware 映射轉換成 Hyper-v 映射。  我們會再次使用 [STARWIND V2V 轉換器](https://www.starwindsoftware.com/starwind-v2v-converter) 。  若要下載，請參閱 [STARWIND V2V 轉換器下載頁面](https://www.starwindsoftware.com/starwind-v2v-converter#download)。
    1. 開始 **STARWIND V2V 轉換器**。
    1. 在 [ **選取要轉換之影像的位置** ] 頁面上，選擇 [ **本機** 檔案]。  選取 [下一步]。
    1. 在 [ **來源映射** ] 頁面上，流覽至並選取在上一個步驟中解壓縮的 Metasploitable，以取得 [ **檔案名** ] 設定。  選取 [下一步]。
    1. 在 [ **選取目的地映射的位置**] 上，選擇 [ **本機** 檔案]。  選取 [下一步]。
    1. 在 [ **選取目的地映射格式** ] 頁面上，選擇 [ **VHD/VHDX**]。  選取 [下一步]。
    1. 在 [ **選取 VHD/VHDX 映射格式的選項** ] 頁面上，選擇 [ **VHDX 可成長映射**]。  選取 [下一步]。
    1. 在 [ **選取目的地檔案名** ] 頁面上，接受預設的檔案名。  選取 [轉換]。
    1. 在 [ **轉換** ] 頁面上，等候影像轉換。  這可能需要數分鐘的時間。  當轉換完成時，請選取 **[完成]** 。
1. 建立新的 Hyper-v 虛擬機器。
    1. 開啟 [ **Hyper-v 管理員**]。
    1. 選擇[  ->  **新增**  ->  **虛擬機器**] 動作。
    1. 在 [**新增虛擬機器] 嚮導** 的 [在 **您開始前**] 頁面上，選取 **[下一步**]。
    1. 在 [ **指定名稱和位置** ] 頁面上，輸入 **Metasploitable** 作為 **名稱**，然後選取 **[下一步]**。

        ![新增 VM 映射嚮導](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. 在 [ **指定世代** ] 頁面上，接受預設值，然後選取 **[下一步]**。
    1. 在 [ **指派記憶體** ] 頁面上，輸入 **512 MB** 作為 [ **啟動記憶體**]，然後選取 **[下一步]**。

        ![指派記憶體頁面](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. 在 [ **設定網路** ] 頁面上，將 [連接] 保持為 [ **未連線**]。 您稍後會設定網路介面卡。
    1. 在 [ **連接虛擬硬碟]** 頁面上，選取 [ **使用現有的虛擬硬碟**]。 流覽至在上一個步驟中建立之 **metasploitable .vhdx** 檔案的位置，然後選取 **[下一步]**。

        ![連接虛擬網路磁片頁面](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. 在 [ **完成新增虛擬機器]** 頁面上，選取 **[完成]**。
    1. 建立虛擬機器之後，請在 Hyper-v 管理員中選取它。 尚未開啟電腦。  
    1. 選擇 [**動作**  ->  **設定**]。
    1. 在的 [ **Metasploitable 設定** ] 對話方塊中，選取 [ **新增硬體**]。
    1. 選取 [ **傳統網路介面卡**]，然後選取 [ **新增**]。

        ![[網路介面卡] 頁面](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. 在 [**傳統網路介面卡**] 頁面上，選取 **虛擬交換器** 設定的 [ **LabServicesSwitch** ]，然後選取 **[確定]**。 在「 **嵌套虛擬化準備範本** 」一節中準備 hyper-v 的範本機器時，就會建立 LabServicesSwitch。

        ![傳統網路介面卡頁面](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Metasploitable 映射現在已準備好可供使用。 從 [ **hyper-v 管理員**] 中，選擇 [**動作**  ->  **開始**]，然後選擇 [**動作** 連線]  ->  以連線至虛擬機器。  預設使用者名稱是 **msfadmin** ，而密碼為 **msfadmin**。

範本現在已更新，並擁有道德入侵滲透測試類別所需的影像、具有可進行滲透測試之工具的映射，以及要探索安全性弱點的另一個映射。 範本映射現在可以發佈至類別。 在範本頁面上選取 [發佈] 按鈕，即可將範本發佈至實驗室。

## <a name="cost"></a>成本  

如果您想要估計此實驗室的成本，可以使用下列範例：

針對 25 名學生的課程，其中 20 小時是已排定課程的時間，10 小時是家庭作業或指派工作的配額，此實驗室的價格為：

25名學生 \* (20 個以上的) 小時 \* 55 實驗室單位 \* 0.01 美元/小時 = 412.50 美元

>[!IMPORTANT]
>成本預估僅供範例之用。 如需定價的最新詳細資訊，請參閱 [Azure 實驗室服務定價](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="conclusion"></a>結論

本文將逐步引導您完成為道德入侵類別建立實驗室的步驟。 其中包含設定嵌套虛擬化的步驟，以在主機虛擬機器內建立兩部虛擬機器以進行滲透測試。

## <a name="next-steps"></a>後續步驟

接下來是設定任何實驗室的一般步驟：

- [新增使用者](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)
- [設定排程](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [學生的電子郵件註冊連結](how-to-configure-student-usage.md#send-invitations-to-users)。
