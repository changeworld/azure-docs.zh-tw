---
title: 在 Azure 開發人員測試實驗室中配置 VM 的自動啟動設置 |微軟文檔
description: 瞭解如何在實驗室中配置 VM 的自動啟動設置。 此設置允許按計劃自動啟動實驗室中的 VM。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807910"
---
# <a name="auto-startup-lab-virtual-machines"></a>自動啟動實驗室虛擬機器  
Azure 開發人員測試實驗室允許您根據計畫配置實驗室中的虛擬機器，以便自動啟動和關閉。 有關配置自動關機設置的資訊，請參閱[在 Azure DevTest 實驗室中管理實驗室的自動關閉策略](devtest-lab-auto-shutdown.md)。 

與自動關閉不同，在打開策略時包含所有 VM，自動啟動策略要求實驗室使用者明確選取 VM 並加入宣告此計畫。 這樣，您就不會輕易遇到不需要的 VM 意外自動啟動並導致意外支出的情況。

本文介紹如何為實驗室配置自動啟動策略。

## <a name="configure-autostart-settings-for-a-lab"></a>為實驗室配置自動啟動設置 
1. 導航到實驗室的主頁。 
2. 選擇左側功能表上的 **"配置"和"策略**"。 

    ![配置和策略功能表](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. 在 **"配置和策略"** 頁上，執行以下步驟：
    
    1. 選擇 **"打開**"以**允許自動啟動虛擬機器**，以啟用此實驗室的自動啟動功能。 
    2. 為 **"計畫開始"** 欄位選擇開始時間（例如：上午 8：00）。 
    3. 選擇要使用的**時區**。 
    4. 選擇需要自動啟動 VM 的**一周中的天數**。 
    5. 然後，選擇 **"在**工具列上保存"以保存設置。 

        ![自動啟動設置](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > 此策略不會自動應用於實驗室中的任何虛擬機器。 要**選擇單個**虛擬機器，請訪問虛擬機器頁面並為該 VM 啟用**自動啟動**。

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>在實驗室中為 VM 啟用自動啟動
以下過程為您提供了選擇將 VM 納入實驗室自動啟動策略的步驟。 

1. 在實驗室的主頁上，選擇"**我的虛擬機器**"清單中的**VM。** 

    ![配置和策略功能表](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. 在 **"虛擬機器**"頁上，在左側功能表或 **"計畫"** 清單中選擇 **"自動啟動**"。 

    ![選擇自動啟動功能表](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. 在 **"自動啟動"** 頁上，**On**選擇"**允許為此虛擬機器計畫自動啟動**"選項。

    ![為 VM 啟用自動啟動](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. 然後，選擇 **"在**工具列上保存"以保存設置。 


## <a name="next-steps"></a>後續步驟
要瞭解實驗室的配置自動關閉策略，請參閱在[Azure 開發人員測試實驗室中管理實驗室的自動關閉策略](devtest-lab-auto-shutdown.md)
