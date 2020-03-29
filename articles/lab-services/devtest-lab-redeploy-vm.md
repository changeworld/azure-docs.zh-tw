---
title: 在 Azure DevTest Labs 中重新部署實驗室中的 VM | Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中重新部署虛擬機器 (從一個 Azure 節點移至另一個節點)。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561592"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中重新部署實驗室中的 VM
如果無法通過遠端桌面連線連接到實驗室中的虛擬機器 （VM），請重新部署 VM 並嘗試再次連接到該虛擬機器。 當您重新部署 VM 時，DevTest Labs 會將 VM 從其執行所在的節點移至 Azure 基礎結構內的新節點。 接著，它會啟動 VM，同時仍保留您的設定選項及相關資源。 當您針對與實驗室中 Windows 型 VM 的遠端桌面連線問題或應用程式對這些 VM 的存取問題進行疑難排解時，此功能可節省所花費的時間。 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>重新部署實驗室中 VM 的步驟 
若要在 Azure DevTest Labs 中重新部署實驗室中的 VM，請進行下列步驟： 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
3. 從實驗室清單中，選取包含您想要重新部署之 VM 的實驗室。  
4. 在左面板中，選取 [我的虛擬機器]****。 
5. 從 VM 清單中，選取 VM。
6. 在您 VM 的 [虛擬機器] 頁面中，選取左側功能表中 [作業]**** 底下的 [重新部署]****。

    ![重新部署](media/devtest-lab-redeploy-vm/redeploy.png)
7. 閱讀頁面上的資訊，然後選取 [重新部署]**** 按鈕。 9. 查看 [通知]**** 視窗中重新部署作業的狀態。

    ![重新部署狀態](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>後續步驟
了解如何在 Azure DevTest Labs 中調整 VM 的大小，請參閱[調整 VM 的大小](devtest-lab-resize-vm.md)。


