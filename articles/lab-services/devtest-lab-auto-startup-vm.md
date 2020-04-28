---
title: 在 Azure DevTest Labs 中設定 VM 的自動啟動設定 |Microsoft Docs
description: 瞭解如何在實驗室中設定 Vm 的自動啟動設定。 此設定可讓實驗室中的 Vm 依排程自動啟動。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74807910"
---
# <a name="auto-startup-lab-virtual-machines"></a>自動啟動實驗室虛擬機器  
Azure DevTest Labs 可讓您將實驗室中的虛擬機器設定為根據排程自動啟動和關閉。 如需設定 autoshutdown 設定的詳細資訊，請參閱[在 Azure DevTest Labs 中管理實驗室的 autoshutdown 原則](devtest-lab-auto-shutdown.md)。 

不同于 autoshutdown，在原則開啟時包含所有 Vm，自動啟動原則會要求實驗室使用者明確選取 VM，並加入宣告此排程。 如此一來，您將無法輕易地進入不小心自動啟動的不必要 Vm，而造成非預期的支出。

本文說明如何設定實驗室的自動啟動原則。

## <a name="configure-autostart-settings-for-a-lab"></a>設定實驗室的自動啟動設定 
1. 流覽至您實驗室的首頁。 
2. 選取左側功能表上的 [設定**和原則**]。 

    ![[設定和原則] 功能表](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. 在 [設定**和原則**] 頁面上，執行下列步驟：
    
    1. 針對 [**允許自動啟動的虛擬機器] 排程**選取 [**開啟**]，以啟用此實驗室的自動啟動功能。 
    2. 針對 [**排程開始**] 欄位，選取 [開始時間] （例如：上午8:00:00）。 
    3. 選取要使用的**時區**。 
    4. 選取**一周**中需要自動啟動 vm 的天數。 
    5. 然後，選取工具列上的 [**儲存**] 來儲存設定。 

        ![自動啟動設定](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 此原則不會自動將自動啟動套用至實驗室中的任何虛擬機器。 若要**選擇**使用個別的虛擬機器，請移至 [虛擬機器] 頁面，並啟用該 VM 的 [**自動啟動**]。

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>針對實驗室中的 VM 啟用自動啟動
下列程式會提供步驟，讓您在實驗室中選擇 VM 的自動啟動原則。 

1. 在您實驗室的首頁上，選取 [**我的虛擬機器**] 清單中的**VM** 。 

    ![[設定和原則] 功能表](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. 在 [**虛擬機器**] 頁面上，選取左側功能表**或 [排程**] 清單中的 [**自動啟動**]。 

    ![選取自動啟動功能表](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. 在 [**自動**啟動] 頁面上，針對 [**允許此虛擬機器排程為自動啟動**] 選項選取 [**開啟**]。

    ![為 VM 啟用自動啟動](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 然後，選取工具列上的 [**儲存**] 來儲存設定。 


## <a name="next-steps"></a>後續步驟
若要瞭解實驗室的設定 autoshutdown 原則，請參閱[在 Azure DevTest Labs 中管理實驗室的 autoshutdown 原則。](devtest-lab-auto-shutdown.md)
