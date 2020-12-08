---
title: 使用 Azure 實驗室服務設定教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會使用 Azure Lab Services 來設定教室實驗室，且其中包含您教室中學生所使用的虛擬機器。
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 8981a03b53b1cfb67b03d89f8a1468511d9b1b93
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434850"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>教學課程：設定教室實驗室 
您將在本教學課程中設定教室實驗室，且其中包含教室中學生所使用的虛擬機器。  

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立教室實驗室
> * 將使用者新增至實驗室
> * 設定實驗室的排程
> * 傳送邀請電子郵件給學生

## <a name="prerequisites"></a>Prerequisites
在本教學課程中，您會為課程設定具有虛擬機器的實驗室。 若要在實驗室帳戶中設定教室實驗室，您必須是實驗室帳戶中其中一個角色的成員：擁有者、實驗室建立者或參與者。 您用來建立實驗室帳戶的帳戶會自動新增至擁有者角色。 因此，您可以使用用來建立實驗室帳戶的使用者帳戶來建立教室實驗室。 

以下是使用 Azure 實驗室服務時的一般工作流程：

1. 實驗室帳戶建立者將其他使用者新增至 **實驗室建立者** 角色。 例如，實驗室帳戶建立者/管理員將教師新增至 **實驗室建立者** 角色，讓這些教師可為其課程建立實驗室。 
2. 然後，教師為其課程建立具有 VM 的實驗室，並將註冊連結傳送給修習此課程的學生。 
3. 學生會使用教師提供的註冊連結向實驗室註冊。 註冊後，他們就可以使用實驗室中的 VM 來進行班級工作和在家工作。 

## <a name="create-a-classroom-lab"></a>建立教室實驗室
在此步驟中，您會在 Azure 中為課程建立實驗室。 

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 請注意，目前不支援 Internet Explorer 11。 
2. 選取 [登入]，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 選取 [新增實驗室]。 
    
    ![顯示「Azure 實驗室服務」並已選取 [新建實驗室] 按鈕的螢幕擷取畫面。](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. 在 [新增實驗室] 視窗中，執行下列動作： 
    1. 指定實驗室的 **名稱**，然後選取 [下一步]。  

        ![建立教室實驗室](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. 在 [虛擬機器認證] 頁面上，指定實驗室中所有 VM 的預設認證。 指定使用者的 **名稱** 和 **密碼**，然後選取 [下一步]。  

        ![[新增實驗室] 視窗](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 在 [實驗室原則] 頁面上，選取 [完成]。 

        ![每位使用者的配額](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 您應該會看到下列畫面，其中顯示範本 VM 的建立狀態。 此作業最多需要 20 分鐘的時間。 

    ![範本 VM 的建立狀態](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在 [範本] 頁面上，執行下列步驟：這些都是本教學課程的 **選擇性** 步驟。

    1. 選取 [連線] 以連線至範本 VM。 如果是 Linux 範本 VM，您可選擇是否要使用 SSH 或 RDP 連線 (若已啟用 RDP)。
    3. 在範本 VM 上安裝和設定您的類別所需的軟體。 
    4. **停止** 範本 VM。  

    > [!NOTE]
    > 範本 VM 會在執行時產生 **成本**，因此確保當您不需要範本 VM 執行時，其已關閉。 

## <a name="publish-the-template-vm"></a>發佈範本 VM
在此步驟中，請發佈範本 VM。 當您發佈範本 VM 時，Azure 實驗室服務會使用範本在實驗室中建立 VM。 所有虛擬機器都有與範本相同的設定。

1. 在 [範本] 頁面上，選取工具列上的 [發佈]。 

    ![[發佈範本] 按鈕](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 在您發佈時，即無法取消發佈。 
2. 在 [發佈範本] 頁面上，輸入您要在實驗室中建立的虛擬機器數目，然後選取 [發佈]。 

    ![發佈範本 - VM 的數目](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. 您會在頁面上看到範本的 **發佈狀態**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 等候發佈完成，然後藉由選取左側功能表上的 [虛擬機器] 或選取 [虛擬機器] 圖格，以切換到 [虛擬機器集區] 頁面。 確認您看到處於 [未指派] 狀態的虛擬機器。 這些虛擬機器尚未指派給任何學生。 它們應處於 [已停止] 狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > 當教育工作者開啟學生 VM 時，學生的配額不會受到影響。 使用者的配額會指定使用者可在排定的上課時間外使用的實驗室時數。 如需配額的詳細資訊，請參閱[為使用者設定配額](how-to-configure-student-usage.md?#set-quotas-for-users)。

## <a name="set-a-schedule-for-the-lab"></a>設定實驗室的排程
建立實驗室的排程事件，讓實驗室中的 VM 在特定時間自動啟動/停止。 您稍早指定的使用者配額 (預設值：10 小時) 便是會在此排程時間以外指派給每位使用者的額外時間。 

1. 切換至 [排程] 頁面，然後選取工具列上的 [新增已排定事件]。 

    ![此螢幕擷取畫面顯示 [排程] 頁面上的 [新增已排定事件] 按鈕。](./media/how-to-create-schedules/add-schedule-button.png)
2. 在 [新增已排定事件] 頁面上，執行下列步驟：
    1. 確認已為 [事件類型] 選取 [標準]。  
    2. 選取課程的 [開始日期]。 
    4. 選取您想要讓 VM 啟動的 [啟動時間]。
    5. 選取要關閉 VM 的 [停止時間]。 
    6. 針對您指定的開始和停止時間選取 [時區]。 
3. 在相同的 [新增已排定事件] 頁面上，選取 [重複] 區段中的目前排程。  

    ![[排程] 頁面上的 [新增排程] 按鈕](./media/how-to-create-schedules/select-current-schedule.png)
5. 在 [重複] 對話方塊中，執行下列步驟：
    1. 確認已為 [重複] 欄位設定 [每週]。 
    2. 選取您想要讓排程生效的日期。 下列範例中選取了星期一至星期五。 
    3. 選取排程的 **結束日期**。
    8. 選取 [儲存]。 

        ![設定重複排程](./media/how-to-create-schedules/set-repeat-schedule.png)

3. 現在，在 [新增已排定事件] 頁面上的 [附註 (選擇性)] 中，輸入排程的描述或附註。 
4. 在 [新增已排定事件] 頁面上，選取 [儲存]。 

    ![每週排程](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. 瀏覽至行事曆中的開始日期，確認已設定排程。
    
    ![行事曆中的排程](./media/how-to-create-schedules/schedule-calendar.png)

    如需建立和管理班級排程的詳細資訊，請參閱[建立和管理實驗室的排程](how-to-create-schedules.md)。


## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室

1. 選取左側功能表上的 [使用者]。 預設會啟用 [限制存取] 選項。 當此設定為開啟時，即使使用者有註冊連結，除非使用者位於使用者清單中，否則也無法向實驗室註冊。 只有清單中的使用者可以使用您傳送的註冊連結，向實驗室註冊。 在此程序中，您會在清單中新增使用者。 或者，您可以關閉 [限制存取]，讓使用者能向實驗室註冊 (只要他們有註冊連結)。 
2. 選取工具列上的 [新增使用者]，然後選取 [以電子郵件地址新增]。 

    ![[新增使用者] 按鈕](./media/how-to-configure-student-usage/add-users-button.png)
1. 在 [新增使用者] 頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](./media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [儲存]。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](./media/how-to-configure-student-usage/users-list-new.png)

    在使用者向實驗室註冊後，您會在清單中看到使用者的名稱。 
    

## <a name="send-invitation-emails-to-users"></a>傳送邀請電子郵件給使用者

1. 如果您並未在該頁面上，請切換至 [使用者] 檢視，然後選取工具列上的 [全部邀請]。 

    ![選取學員](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. 在 [透過電子郵件傳送邀請] 頁面上輸入選擇性訊息，然後選取 [傳送]。 電子郵件會自動包含註冊連結。 您可以選取工具列上的 [...(省略符號)] 和 [註冊連結]，來取得此註冊連結。 

    ![透過電子郵件傳送註冊連結](./media/tutorial-setup-classroom-lab/send-email.png)
4. 您會在 [使用者] 清單中看到 [邀請] 的狀態。 其狀態應該會依序變更為 [傳送中] 和 [已於 &lt;date&gt; 傳送]。 

    如需將學生新增到班級及管理其實驗室使用方式的詳細資訊，請參閱[如何設定學生使用方式](how-to-configure-student-usage.md)。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已在 Azure 中為課程建立實驗室。 若要了解學生可以如何使用註冊連結，來存取實驗室中的 VM，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [連線到教室實驗室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

