---
title: 開始使用 Azure 實驗室服務
description: 本文說明如何開始使用 Azure 實驗室服務。
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: 33e052931b0c3bd1bb1434b7eeefeed7a2a7ceab
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93380154"
---
# <a name="get-started-with-lab-services"></a>開始使用實驗室服務 

如果您是學生，您可以使用 Azure 實驗室服務，在 (VM) 的虛擬機器上，存取您的研究計畫所需的業界標準軟體。 

老師需要知道如何透過一對一學生髮出的硬體，告訴學生如何在其指示中使用 Azure 實驗室服務。

本文提供的資訊可讓您瞭解如何存取、管理及教授學生利用 Azure 實驗室服務。

## <a name="overview"></a>總覽

什麼是 VM？其運作方式為何？

虛擬機器 (VM) 是作為虛擬電腦的虛擬環境。 Vm 有自己的處理器、記憶體和儲存體。 Vm 提供實際電腦的替代方案，可讓使用者存取作業系統和軟體，而不需要在他們自己的裝置上使用它們。 Azure Lab Services 提供一種工具，可讓學生存取及流覽 Vm，並讓員工管理其虛擬電腦實驗室。 

如需詳細資訊，請參閱 [建立和管理教室實驗室](how-to-manage-classroom-labs.md)。

## <a name="lab-dashboards"></a>實驗室儀表板

Azure 實驗室服務中的教室實驗室儀表板可提供特定實驗室的不同層面的快照，包括 VM 資訊、指派和未指派 Vm 的數目、已註冊和未註冊的使用者數目，以及有關實驗室排程的資訊。 

> [!NOTE]
> 雖然老師可以看到大部分的儀表板和 [Azure 實驗室服務網站](https://labs.azure.com/) 的系統管理層面，但角色特有的許可權可能會影響您在儀表板中修改特定準則的能力。 如果您遇到特定實驗室設定的問題，請洽詢您的 CTE 系統管理員。

:::image type="content" source="./media/use-dashboard/dashboard.png" alt-text="Azure 實驗室服務入口網站":::

1. 流覽並登入 [Azure 實驗室服務網站](https://labs.azure.com/)。
1. 選取您的實驗室。
1. 您會在視窗的左側看到 **儀表板** 。 按一下 [ **儀表板** ]，您將會在儀表板中看到數個磚。
1. 在 **成本 & 計費** 圖格的下方，也有範本、虛擬機器集區、使用者和排程的磚，可讓您修改各個層面，並查看有關教室實驗室的詳細資料。

    1. 範本-描述範本的建立日期和上次發佈日期。 
    1. 虛擬機器集區：指派和未指派的 Vm 數目。
    1. 使用者-已新增至實驗室但未註冊的已註冊使用者和使用者數目。
    1. 排程-顯示即將推出的實驗室排程事件，以及可查看更多事件的連結。

如需詳細資訊，請參閱 [使用儀表板](use-dashboard.md)。

## <a name="quota-hours"></a>配額時數

學生可以在排程的類別時間內隨時存取其 Vm，而不會影響其配額時數。 系統會為整個半年度設定配額時數，並決定學生可以在定期排程的類別時間以外的時間使用其 VM 的時數。

每週8小時，在星期日重設-非累計。

如需詳細資訊，請參閱 [設定配額](how-to-configure-student-usage.md#set-quotas-for-users)。

### <a name="automatic-shut-down"></a>自動關機

為協助降低成本並節省學生的配額時數，將會為我們的實驗室啟用自動關機。 自動關機會在閒置一段時間後關閉 Vm， (沒有滑鼠或鍵盤輸入) 。 自動關機的工作分為兩個階段，首先會在一段時間沒有活動之後，將學生與 VM 中斷連線。 此時，VM 仍在執行中， **而且學生** 能夠連接。 在中斷連線的另一段時間後，VM 會自行關閉。

自動關機是一項重要的節省成本工具，不過對於儲存工作和轉譯大型專案檔的學生來說，會有一項挑戰。 如果您的學生經常中斷連線，或 Vm 很快就關閉了。 請洽詢您的 CTE 系統管理員。 

如需詳細資訊，請參閱 [設定實驗室帳戶的 vm 自動關機](how-to-configure-lab-accounts.md)。

## <a name="managing-virtual-machines"></a>管理虛擬機器

管理實驗室可讓老師控制實驗室容量 (可供學生使用的 Vm 數目) 以及手動啟動、停止或重設 Vm。 教師也可以連線到 Vm 來體驗學生介面、存取檔案，以及針對軟體或 VM 本身的問題進行疑難排解。

管理 Vm 時最重要的一點是，當 **機器正在執行時，我們** 會產生費用，即使沒有任何人連接到 VM 也是一樣。

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
1. 在快顯視窗中，按一下 [ **重設** ]。

    :::image type="content" source="./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png" alt-text="重設您的 VM":::

    > [!NOTE]
    > 開啟學生 VM 不會影響學生的配額。 使用者的配額會指定使用者可以在排程的類別時間之外使用的實驗室時數。

### <a name="connect-to-virtual-machines"></a>連接至虛擬機器

教師只要開啟，即可連線至學生 VM，而學生也無法連線至 VM。 藉由連接到 VM，您將能夠存取 VM 上的本機檔案，並協助學生針對問題進行疑難排解。

1. 若要連線至學生 VM，請將滑鼠停留在清單中的 VM 上，然後按一下 [連線 **]** 按鈕。 
1. 然後遵循 Chromebook、Mac 或 Pc 的學生入門指南

:::image type="content" source="./media/how-to-set-virtual-machine-passwords/connect-student-vm.png" alt-text="[連線至學生 VM] 按鈕":::

## <a name="add-and-manage-lab-users"></a>新增和管理實驗室使用者

教師能夠將學生使用者新增至實驗室，並監視其小時配額。 

### <a name="add-users-by-email-address"></a>依電子郵件地址新增使用者

1. 從 [Azure 實驗室服務網站](https://labs.azure.com/) 按一下視窗左側的 [ **使用者** ]。
1. 在視窗頂端，按一下 [ **新增使用者** ]，然後選取 [透過 **電子郵件地址新增** ]。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-button.png" alt-text="[新增使用者] 按鈕":::
1. 在顯示于右側的 [ **新增使用者** ] 窗格中，在個別行上輸入學生的電子郵件地址，或在同一行上輸入學生的電子郵件地址，並以分號分隔。
1. 按一下 [儲存]。

    :::image type="content" source="./media/get-started-manage-labs/add-students.png" alt-text="將學生新增至實驗室":::
1. 現在會使用電子郵件、狀態、邀請和配額時數來更新您的使用者清單。

    註冊實驗室的學生之後，其名稱將會以 MP 目錄的名字和姓氏更新。

    > [!NOTE]
    > 讓使用者可以開啟 [限制存取] 選項切換。 這表示只有您列出的使用者可以使用您傳送的註冊連結向實驗室註冊。

### <a name="add-users-using-a-spreadsheet"></a>使用試算表新增使用者 

您也可以透過上傳包含其電子郵件地址的 CSV 檔案來新增使用者。

1. 在 Microsoft Excel 中建立 CSV 檔案，以在一個資料行中列出學生的電子郵件地址。
1. 從 [Azure 實驗室服務網站](https://labs.azure.com/)的 [ **使用者** ] 頁面頂端，按一下 [ **新增使用者** ] 按鈕。
1. 選取 **[上傳 CSV** ]。
1. 選取包含學生電子郵件地址的 CSV 檔案，然後按一下 [ **開啟** ]。

    :::image type="content" source="./media/get-started-manage-labs/add-users-spreadsheet.png" alt-text="使用試算表新增使用者":::
1. 電子郵件現在會出現在右側的視窗中。 按一下 [儲存]。

    :::image type="content" source="./media/get-started-manage-labs/register-users.png" alt-text="註冊使用者":::

### <a name="register-users"></a>註冊使用者

一旦將使用者新增至實驗室，他們必須註冊，才能存取 Vm。 這可以透過從 Azure Web 服務入口網站邀請使用者來完成，這會傳送包含實驗室註冊連結的電子郵件。 或者，將註冊連結複製並貼到電子郵件或其他與學生通訊的形式。

1. 從 [ **使用者** ] 頁面的清單中，選取一或多個學生。

    在您選取之學生的資料列中，選取清單中的信封圖示，或按一下畫面頂端的 [ **邀請** ]。

    :::image type="content" source="./media/get-started-manage-labs/send-invitation.png" alt-text="傳送邀請":::
    
    在 [透過電子郵件 **傳送邀請** ] 視窗中，輸入選用的郵件 (例如) 給學生的使用者名稱和密碼，然後按一下 [ **傳送** ]。 
    
    :::image type="content" source="./media/get-started-manage-labs/send-invitation-mail.png" alt-text="傳送電子郵件邀請":::

    或者，您也可以在 [相同的 **使用者** ] 頁面上，按一下畫面頂端的 [ **註冊連結** ] 按鈕。 

    :::image type="content" source="./media/get-started-manage-labs/registration-link.png" alt-text="使用者註冊連結":::
    
    從文字欄位複製註冊連結，並將其貼到電子郵件或您慣用的安全訊息工具中。  
    
    :::image type="content" source="./media/get-started-manage-labs/user-registration.png" alt-text="傳送使用者註冊":::

當您透過 Azure 入口網站或共用連結來邀請使用者之後，您將能夠在 [ **狀態** ] 資料行的 [ **使用者** ] 頁面中，監視哪些使用者已成功註冊。 

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用您在本快速入門中建立的資源，請刪除資源。

## <a name="next-steps"></a>後續步驟

[設定實驗室帳戶](tutorial-setup-lab-account.md)