---
title: 在 Azure DevTest Labs 的實驗室中重新啟動 VM | Microsoft Docs
description: 本文提供了在 Azure DevTest 實驗室中快速輕鬆地重新開機虛擬機器 （VM） 的步驟。
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759989"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 的對實驗室中重新啟動 VM
您可以依照這篇文章的步驟，快速且輕鬆地在 DevTest Labs 中重新啟動虛擬機器。 請先考慮下列事項再重新啟動 VM：

- VM 必須執行中，重新啟動功能才能啟用。
- 當他們執行重新啟動時，如果使用者連線到執行中的 VM，他們必須在開始備份後重新連線到 VM。
- 如果重新啟動 VM 時套用成品，您會收到可能無法套用成品的警告。

    ![套用成品時重新啟動時的警告](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > 如果 VM 在套用成品時已停止，您可以將重新啟動 VM 功能做為解決問題的可能方式。
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>在 Azure DevTest Labs 的實驗室中重新啟動 VM 的步驟
1. 登錄到 Azure[門戶](https://go.microsoft.com/fwlink/p/?LinkID=525040)。
1. 選取 [所有服務]****，然後從清單中選取 [DevTest Labs]****。
1. 從實驗室清單中，選取包含您想要重新啟動之 VM 的實驗室。
1. 在左面板中，選取 [我的虛擬機器]****。
1. 從 VM 清單中，選取執行中的 VM。
1. 在 VM 管理窗格頂端，選取 [重新啟動]****。

    ![重新啟動 VM 按鈕](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. 選取視窗右上角的 [通知]**** 圖示來監視重新啟動的狀態。

    ![檢視 VM 重新啟動的狀態](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

您也可以藉由在**我的虛擬機器**清單中選取其省略符號 (...) 以重新啟動執行中的 VM。

![透過省略符號重新啟動 VM](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>後續步驟
* 一旦重新啟動後，您可以選取其管理窗格上的 [連線]**** 來重新連線至 VM。
* 流覽[開發人員測試實驗室 Azure 資源管理器快速入門範本庫](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
