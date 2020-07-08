---
title: 在 Azure DevTest Labs 中重新部署實驗室中的 VM | Microsoft Docs
description: 了解如何在 Azure DevTest Labs 中重新部署虛擬機器 (從一個 Azure 節點移至另一個節點)。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da0edf13adaa0d7ecd84ee2c190f376c19b398db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85480230"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中重新部署實驗室中的 VM
如果您無法透過遠端桌面連線連線到實驗室中的虛擬機器（VM），請重新部署 VM，然後再次嘗試連線。 當您重新部署 VM 時，DevTest Labs 會將 VM 從其執行所在的節點移至 Azure 基礎結構內的新節點。 接著，它會啟動 VM，同時仍保留您的設定選項及相關資源。 當您針對與實驗室中 Windows 型 VM 的遠端桌面連線問題或應用程式對這些 VM 的存取問題進行疑難排解時，此功能可節省所花費的時間。 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>重新部署實驗室中 VM 的步驟 
若要在 Azure DevTest Labs 中重新部署實驗室中的 VM，請進行下列步驟： 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
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


