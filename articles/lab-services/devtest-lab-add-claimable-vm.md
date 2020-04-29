---
title: 在 Azure DevTest Labs 中建立和管理可宣告 Vm |Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中對實驗室新增可宣告的虛擬機器
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270793"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中建立和管理可認領 VM
您可以從[自訂映像](devtest-lab-create-template.md)、[公式](devtest-lab-manage-formulas.md)或 [Marketplace 映像](devtest-lab-configure-marketplace-images.md)等「基底」**，使用和[新增標準 VM](devtest-lab-add-vm.md) 相似的方法將可宣告 VM 新增至實驗室。 本教學課程會逐步引導您使用 Azure 入口網站，新增可認領 VM 至 DevTest Labs 中的實驗室，並為使用者示範認領與取消認領 VM 的流程。

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>將可宣告 VM 新增至 Azure DevTest Labs 中實驗室的步驟
1. 登入 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [所有服務]****，然後選取 [DEVOPS]**** 區段中的 [DevTest Labs]****。 如果您選取 [DEVOPS]**** 區段中 [DevTest Labs]**** 旁邊的 * (星號)， 此動作會將 [DevTest Labs]**** 新增到左側導覽功能表，以便您下次輕鬆存取。 然後，您便可以選取左側導覽功能表上的 [DevTest Labs]****。

    ![所有服務 - 選取 DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. 從實驗室清單中，選取您想要在其中建立 VM 的實驗室。
2. 在實驗室的 [概觀]**** 頁面上，選取 [+ 新增]****。

    ![加入 VM 按鈕](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. 在 [選擇基底]**** 頁面上，選取 VM 的 Marketplace 映像。
1. 在 [虛擬機器]**** 頁面的 [基本設定]**** 索引標籤上，執行下列動作：
    1. 在 [虛擬機器名稱]**** 文字方塊中，輸入 VM 的名稱。 此文字方塊中會為您預先填入一個自動產生的唯一名稱。 此名稱會對應至您電子郵件地址中的使用者名稱，並在其後方加上唯一的 3 位數數字。 這項功能可節省您思考機器名稱的時間，以及每次建立機器時輸入名稱的時間。 如果您想要的話，也可以使用您選擇的名稱來覆寫此自動填入的欄位。 若要覆寫自動填入的 VM 名稱，請在 [虛擬機器名稱]**** 文字方塊中輸入名稱。
    2. 輸入**使用者名稱**，此名稱會被授與虛擬機器上的系統管理員權限。 機器的**使用者名稱**會預先填入自動產生的唯一名稱。 此名稱會對應至您電子郵件地址中的使用者名稱。 這項功能可節省您每次建立新機器時，決定使用者名稱的時間。 同樣地，如果您想要的話，也可以使用您選擇的使用者名稱來覆寫此自動填入的欄位。 若要覆寫使用者名稱的自動填入值，請在 [使用者名稱]**** 文字方塊中輸入值。 此使用者會獲得虛擬機器上的**系統管理員**權限。
    3. 如果是在實驗室中建立第一個 VM，請為使用者輸入**密碼**。 若要讓此密碼以預設密碼的形式，儲存在與實驗室相關聯的 Azure 金鑰保存庫中，請選取 [儲存為預設密碼]****。 預設密碼會以下列名稱儲存在金鑰保存庫中：**VmPassword**。 當您嘗試在實驗室中建立後續的 VM 時，系統會自動選取 **VmPassword** 作為**密碼**。 若要覆寫此值，請清除 [使用儲存的祕密]**** 核取方塊，然後輸入密碼。

        您可以也先將祕密儲存在金鑰保存庫中，然後在實驗室中建立 VM 時使用此祕密。 如需詳細資訊，請參閱[在金鑰保存庫中儲存祕密](devtest-lab-store-secrets-in-key-vault.md)。 若要使用儲存在金鑰保存庫中的密碼，請選取 [使用儲存的祕密]****，並指定與祕密 (密碼) 對應的金鑰值。
    4. 在 [更多選項]**** 區段中，選取 [變更大小]****。 選取其中一個預先定義的項目，以指定要建立之 VM 的處理器核心、RAM 大小及硬碟大小。
    5. 選取 [新增或移除成品]****。 選取並設定您想要新增到基底映像中的成品。
    **附註：** 如果您對 DevTest Labs 或設定構件並不熟悉，請參閱[將現有的構件加入至 VM](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) 一節，完成該節之後再返回此處。
2. 切換至頂端的 [進階設定]**** 索引標籤，然後執行下列動作：
    1. 若要變更 VM 所在的虛擬網路，請選取 [變更 VNet]****。
    2. 若要變更子網路，請選取 [變更子網路]****。
    3. 指定 VM 的 IP 位址是 [公用]、[私人] 還是 [共用]****。
    4. 若要自動刪除 VM，請指定**到期日和時間**。
    5. 若要將 VM 設為可供實驗室使用者認領，請針對 [允許宣告此機器]**** 選項選取 [是]****。
    6. 指定要提供給實驗室使用者使用的 **VM 執行個體**數目。
3. 選取 [建立] **** ，將指定的 VM 加入實驗室。

   實驗室頁面會顯示 VM 的建立狀態 - 一開始會是 [正在建立]****，然後在啟動該 VM 之後則是 [正在執行]****。

> [!NOTE]
> 如果您是透過  [Azure Resource Manager 範本](devtest-lab-create-environment-from-arm.md)部署實驗室 VM，您可以在 [屬性] 區段中將 **allowClaim** 屬性設定為 true，來建立可宣告 VM。


## <a name="using-a-claimable-vm"></a>使用可宣告 VM

使用者可以透過執行下列其中一項步驟，來宣告 [可宣告的虛擬機器] 清單上的任何 VM：

* 在實驗室 [概觀] 窗格底部的 [可認領的虛擬機器] 清單中，以滑鼠右鍵按一下清單中的其中一個 VM，並選擇 [認領機器]****。

  ![要求特定可宣告 VM。](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* 在 [概觀] 窗格的頂端，選擇 [認領任何項目]****。 系統將會從可宣告 VM 清單中隨機指派虛擬機器。

  ![要求任何可宣告 VM。](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


在使用者宣告 VM 之後，DevTest Labs 會啟動電腦，並將其移至實驗室使用者的「我的虛擬機器」清單。 這表示實驗室使用者現在會擁有這部電腦的擁有者許可權。 此步驟所需的時間可能會根據啟動時間以及在宣告事件期間執行的任何其他自訂動作而有所不同。 一旦宣告後，電腦就無法再于可宣告集區中使用。  

## <a name="unclaim-a-vm"></a>取消認領 VM

使用者使用完已認領 VM，想將 VM 提供給其他人使用時，可執行以下其中一個步驟，將已認領 VM 傳回至可認領虛擬機器的清單：

- 從 [我的虛擬機器] 清單中，以滑鼠右鍵按一下清單中的其中一個 VM，或是選取其省略符號 (...)，然後選擇 [取消認領]****。

  ![在 VM 清單上取消認領 VM。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- 在 [我的虛擬機器] 清單中，選取 VM 以開啟其管理窗格，然後從頂端功能表列中選取 [取消認領]****。

  ![在 VM 的管理窗格上取消認領 VM。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

當使用者 unclaims VM 時，他們不再具有該特定實驗室 VM 的擁有者許可權，且可供任何其他實驗室使用者在其 retured 至集區的狀態下宣告。 

### <a name="transferring-the-data-disk"></a>傳送資料磁碟
若可認領 VM 有附加資料磁碟，而使用者取消認領該 VM，資料磁碟會與該 VM 一同保留。 若有另一位使用者認領該 VM，則該名新使用者會連同 VM 一併認領資料磁碟。

此稱為「傳送資料磁碟」。 隨後，資料磁碟即可在新使用者的 [我的資料磁碟]**** 清單中供其進行管理。

![取消認領資料磁碟。](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>後續步驟
* 建立完成後，您可選取其管理窗格上的 [連線]**** 來重新連線至 VM。
* 探索[DevTest Labs Azure Resource Manager 快速入門範本資源庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)。
