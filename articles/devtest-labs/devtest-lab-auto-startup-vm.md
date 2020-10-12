---
title: 針對 Azure DevTest Labs 中的 VM 設定自動啟動設定 |Microsoft Docs
description: 瞭解如何針對實驗室中的 Vm 設定自動啟動設定。 這項設定可讓實驗室中的 Vm 依排程自動啟動。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91328474"
---
# <a name="auto-startup-lab-virtual-machines"></a>自動啟動實驗室虛擬機器  
Azure DevTest Labs 可讓您在實驗室中設定虛擬機器，以根據排程自動啟動和關閉。 如需有關設定自動關機設定的詳細資訊，請參閱 [在 Azure DevTest Labs 中管理實驗室的自動關機原則](devtest-lab-auto-shutdown.md)。 

不同于自動關機（當原則開啟時，會包含所有 Vm），自動啟動原則需要實驗室使用者明確地選取 VM，並加入宣告此排程。 如此一來，您就不會輕易地遇到不必要的 Vm 意外自動啟動的情況，並導致非預期的費用。

本文說明如何設定實驗室的自動啟動原則。

## <a name="configure-autostart-settings-for-a-lab"></a>設定實驗室的自動啟動設定 
1. 流覽至您實驗室的首頁。 
2. 選取左側功能表上的 [設定 **與原則** ]。 

    ![顯示 DevTest 實驗室中 [設定和原則] 功能表的螢幕擷取畫面。](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. 在 [設定 **和原則** ] 頁面上，執行下列步驟：
    
    1. 選取 [**允許將虛擬機器排程為自動啟動** **]，以**啟用此實驗室的自動啟動功能。 
    2. 針對 [ **排程開始** ] 欄位選取 [開始時間] (例如： 8:00:00 AM) 。 
    3. 選取要**使用的時區。** 
    4. 選取 **一周** 中需要自動啟動 vm 的天數。 
    5. 然後，選取工具列上的 [ **儲存** ] 以儲存設定。 

        ![自動啟動設定](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 此原則不會自動將自動啟動套用至實驗室中的任何虛擬機器。 若要 **選擇** 個別的虛擬機器，請移至 [虛擬機器] 頁面，並啟用該 VM 的 **自動啟動** 。

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>為實驗室中的 VM 啟用 autostart
下列程式提供將 VM 加入實驗室的自動啟動原則的步驟。 

1. 在實驗室的首頁上，選取 [**我的虛擬機器**] 清單中的**VM** 。 

    ![設定和原則功能表](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. 在 [**虛擬機器**] 頁面上，選取左側功能表或 [排程]**清單中的 [** **自動啟動**]。 

    ![選取自動啟動功能表](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. 在 [**自動**啟動] 頁面上，針對 [**允許此虛擬機器排程為自動啟動**] 選項選取 [**開啟**]。

    ![為 VM 啟用 autostart](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 然後，選取工具列上的 [ **儲存** ] 以儲存設定。 


## <a name="next-steps"></a>後續步驟
若要瞭解實驗室的設定自動關機原則，請參閱 [在 Azure DevTest Labs 中管理實驗室的自動關機原則](devtest-lab-auto-shutdown.md)
