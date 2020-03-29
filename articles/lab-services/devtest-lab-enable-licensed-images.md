---
title: 在 Azure DevTest Labs 中啟用授權映像 | Microsoft Docs
description: 了解如何使用 Azure 入口網站在 Azure DevTest Labs 中啟用授權映像
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 221390d2-8d3b-4e1f-b454-43d33f8072b7
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 11b6553fe8aceef0d3d15977998dd870c275128a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "61294211"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中啟用授權映像

在 Azure DevTest Labs 中，授權映像當中包含條款及條件 (通常是來自第三方)，實驗室中的使用者必須先接受這些條款和條件之後才能存取映像。 下列各節說明如何使用授權映像，使其可用來建立虛擬機器。

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>判斷授權映像是否可供使用者使用
讓使用者能夠從授權映像建立 VM 的第一步是確定已接受授權映像的條款和條件。 下列步驟顯示如何檢視授權映像的供應項目狀態，並且必要時接受當中的條款和條件。

1. 登錄到 Azure[門戶](https://go.microsoft.com/fwlink/p/?LinkID=525040)。

1. 選擇**所有服務**，然後從清單中選擇**DevTest 實驗室**。

1. 從實驗室清單中，選取所需的實驗室。  

1. 在左側面板的 [設定]**** 下，選取 [設定與原則]****。

1. 在左側面板的 [虛擬機器基底]**** 下，選取 [Marketplace 映像]****。 

    ![[Marketplace 映像] 功能表項目](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    此時會顯示所有可用 Marketplace 映像的清單，包括每個映像的 [供應項目狀態]****。

    ![顯示每個映像供應項目狀態的 Marketplace 映像清單](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    授權映像會顯示下列項目的供應項目狀態： 
    
    - **接受條款：** 授權映像可供使用者建立 VM。 
    - **必須檢閱條款：** 使用者目前無法使用授權映像。 實驗室使用者必須先接受授權的條款和條件，才能使用它來建立 VM。 

## <a name="making-a-licensed-image-available-to-lab-users"></a>讓授權映像可供實驗室使用者使用
為了確保授權映像可供實驗室使用者使用，具有管理員權限的實驗室擁有者必須先接受該授權映像的條款和條件。 啟用以程式設計方式部署與授權映像相關聯的訂用帳戶，會自動接受該映像的法律條款和隱私權聲明。 [在 Azure Resource Manager 上使用 Marketplace 映像](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) \(英文\) 提供以程式設計方式部署 Marketplace 映像的其他資訊。

您可以依照下列步驟，啟用以程式設計方式部署授權映像：

1. 在 [Azure 入口網站](https://go.microsoft.com/fwlink/p/?LinkID=525040)中，移至 [Marketplace 映像]**** 的清單。

1. 找出您想要讓使用者存取，但還未接受條款的授權映像。 例如，您可能會看到一個「資料科學虛擬機器」，顯示 [接受條款]**** 或 [必須檢閱條款]**** 其中一種狀態。

    ![[設定以程式設計方式部署] 視窗](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > 「資料科學 VM」是預先安裝、設定及測試過數個熱門工具的 Azure 虛擬機器映像，這些工具常用於資料分析、機器學習服務和 AI 訓練。 [適用於 Linux 和 Windows 的 Azure 資料科學虛擬機器簡介](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/overview)提供有關 DSVM 的大量相關資訊。
   >
   >

1. 在映像的 [供應項目狀態]**** 欄中，選取 [必須檢閱條款]****。

1. 在 [設定以程式設計方式部署] 視窗中，選取 [啟用]****。

    ![[設定以程式設計方式部署] 視窗](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > 您可能會看到 [設定以程式設計方式部署] 視窗中列出多個訂用帳戶。 請確定您只為想要的訂用帳戶，啟用以程式設計方式部署。
   >
   >


1. 選取 [儲存]****。 

    在 Marketplace 映像的清單中，該映像現在會顯示 [接受條款]****，並且可供使用者建立虛擬機器。

> [!NOTE]
> 使用者可以從授權的映像建立自訂映像。 如需詳細資訊，請參閱[從 VHD 檔案建立自訂映像](devtest-lab-create-template.md)。
>
>


## <a name="related-blog-posts"></a>相關部落格文章

- [自訂映像或公式？](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [在 Azure DevTest Labs 之間複製自訂映像](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>後續步驟

- [從 VM 建立自訂映像](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [從 VHD 檔案建立自訂映像](devtest-lab-create-template.md)
- [將 VM 新增到實驗室](devtest-lab-add-vm.md)