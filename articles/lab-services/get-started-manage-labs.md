---
title: 開始使用 Azure 實驗室服務
description: 本文說明如何開始使用 Azure 實驗室服務。
ms.topic: article
ms.date: 11/18/2020
ms.openlocfilehash: d260ace7d7819c3ca2db96d6c4984bf834e170da
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165037"
---
# <a name="get-started-with-lab-services"></a>開始使用實驗室服務 

Azure 實驗室服務可讓學生和教師直接從自己的電腦存取虛擬電腦實驗室。

老師需要知道如何透過一對一位學生髮出的硬體，來教授學生/家長利用實驗室服務的指示。 如此一來，學生就能透過 (VM) 的虛擬機器，來存取其研究計畫所需的產業標準軟體。 

VM 是作為虛擬電腦的虛擬環境。 Vm 有自己的處理器、記憶體和儲存體。 Vm 提供實際電腦的替代方案，可讓使用者存取作業系統和軟體，而不需要在他們自己的裝置上使用它們。 Azure Lab Services 提供一種工具，可讓學生存取及流覽 Vm，並讓員工管理其虛擬電腦實驗室。 

本文提供有關如何存取、管理及教授學生/家長以利用 Azure 實驗室服務的資訊。

## <a name="key-concepts"></a>重要概念

### <a name="quota-hours"></a>配額時數

學生可以在排程的類別時間內隨時存取其 Vm，而不會影響其配額時數。 系統會為整個半年度設定配額時數，並決定學生可以在定期排程的類別時間以外的時間使用其 VM 的時數。

每週8小時，在星期日重設-非累計。

如需詳細資訊，請參閱 [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="automatic-shut-down"></a>自動關機

為協助降低成本並節省學生的配額時數，系統會為實驗室啟用自動關機。 自動關機會在閒置一段時間後關閉 Vm， (沒有滑鼠或鍵盤輸入) 。 自動關機的工作分為兩個階段，首先會在一段時間沒有活動之後，將學生與 VM 中斷連線。 此時，VM 仍在執行中， **而且學生** 能夠連接。 在中斷連線的另一段時間後，VM 會自行關閉。

自動關機是一項重要的節省成本工具，不過對於儲存工作和轉譯大型專案檔的學生來說，會有一項挑戰。 如果您的學生經常中斷連線，或 Vm 很快就關閉，請與您的 CTE 系統管理員聯繫。 

如需詳細資訊，請參閱 [設定實驗室帳戶的 vm 自動關機](how-to-configure-lab-accounts.md)。

### <a name="managing-virtual-machines"></a>管理虛擬機器

管理實驗室可讓老師控制實驗室容量 (可供學生使用的 Vm 數目) 以及手動啟動、停止或重設 Vm。 教師也可以連線到 Vm 來體驗學生介面、存取檔案，以及針對軟體或 VM 本身的問題進行疑難排解。

管理 Vm 時最重要的一點是，當 **機器正在執行時，我們** 會產生費用，即使沒有任何人連接到 VM 也是一樣。

## <a name="lab-dashboards"></a>實驗室儀表板

### <a name="overview"></a>總覽

適用于 Azure 實驗室服務中的實驗室的儀表板可提供特定實驗室的不同層面，包括 VM 資訊、指派和未指派 Vm 的數目、已註冊和未註冊的使用者數目，以及實驗室排程的相關資訊。 

> [!NOTE]
> 雖然老師可以看到大部分的儀表板和 [Azure 實驗室服務網站](https://labs.azure.com/) 的系統管理層面，但角色特有的許可權可能會影響您在儀表板中修改特定準則的能力。 如果您遇到特定實驗室設定的問題，請洽詢您的 CTE 系統管理員。

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Azure 實驗室服務入口網站":::

### <a name="examine-a-dashboard"></a>檢查儀表板

1. 流覽並登入 [Azure 實驗室服務網站](https://labs.azure.com/)。
1. 選取您的實驗室。
1. 您會在視窗的左側看到 **儀表板** 。 按一下 [ **儀表板** ]，您將會在儀表板中看到數個磚。
1. 在 **成本 & 計費** 圖格的下方，也有範本、虛擬機器集區、使用者和排程的磚，可讓您修改各個層面，並查看有關教室實驗室的詳細資料。

    * 範本-描述範本的建立日期和上次發佈日期。 
    * 虛擬機器集區：指派和未指派的 Vm 數目。
    * 使用者-已新增至實驗室但未註冊的已註冊使用者和使用者數目。
    * 排程-顯示即將推出的實驗室排程事件，以及可查看更多事件的連結。

如需詳細資訊，請參閱 [使用儀表板](use-dashboard.md)。

### <a name="manually-starting-vms"></a>手動啟動 Vm

1. 從 [ **虛擬機器集** 區] 頁面中，按一下頁面頂端的 [ **全部啟動** ] 按鈕，即可啟動實驗室中的所有 vm。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png" alt-text="啟動您的 Vm":::
1. 您可以按一下 [狀態] 切換來啟動個別 Vm。 

    當 vm 啟動時，切換會 **開始** 讀取，然後在 vm **啟動之後執行** 。
1. 您也可以使用 [ **名稱** ] 資料行左邊的檢查來選取多個 vm。 

    選取所需的 Vm 之後，請按一下畫面頂端的 [ **開始** ] 按鈕。
1. 啟動後，您可以按一下 [ **全部停止** ] 按鈕以停止所有 vm。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png" alt-text="停止您的 Vm":::

### <a name="stopping-and-resetting-vms"></a>停止和重設 Vm

* 您可以按一下狀態切換來停止個別 Vm。
* 您也可以使用檢查來停止多個 Vm，然後按一下畫面頂端的 [停止] 按鈕。

如果學生在連線到 VM 時遇到問題，或因為任何其他原因而必須重設 VM，您可以使用重設函式。
1. 若要重設一或多個 Vm，請使用檢查來選取它們，然後按一下頁面頂端的 [ **重設** ] 按鈕。
1. 在快顯視窗中，按一下 [ **重設**]。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="重設您的 VM":::

    > [!NOTE]
    > 開啟學生 VM 不會影響學生的配額。 使用者的配額會指定使用者可以在排程的類別時間之外使用的實驗室時數。

### <a name="connect-to-vms"></a>連接至 Vm

教師只要開啟，即可連線至學生 VM，而學生也無法連線至 VM。 藉由連接到 VM，您將能夠存取 VM 上的本機檔案，並協助學生針對問題進行疑難排解。

1. 若要連線至學生 VM，請將滑鼠停留在清單中的 VM 上，然後按一下 [連線 **]** 按鈕。 
1. 然後遵循 Chromebook、Mac 或 Pc 的學生入門指南

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="[連線至學生 VM] 按鈕":::

## <a name="manage-users-in-a-lab"></a>管理實驗室中的使用者

教師能夠將學生使用者新增至實驗室，並監視其小時配額。 如需有關如何透過電子郵件地址新增使用者或使用電子表格清單和註冊使用者的詳細資訊，請參閱 [新增和管理實驗室使用者](how-to-configure-student-usage.md)。

當您邀請使用者或共用連結之後，您就可以在 [**狀態**] 資料行的 [**使用者**] 頁面中，監視哪些使用者已成功註冊。 

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用您在本快速入門中建立的資源，請刪除資源。

## <a name="next-steps"></a>後續步驟

[設定實驗室帳戶](tutorial-setup-lab-account.md)
