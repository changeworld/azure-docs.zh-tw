---
title: 使用 Azure 實驗室服務設定教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會使用 Azure Lab Services 來設定教室實驗室，且其中包含您教室中學生所使用的虛擬機器。
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 3abbf5221382b46dbf4e73f9f4dc3b639bc5ecbd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602479"
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

當您新增使用者時，[限制存取] 選項會依預設開啟，除非位於使用者清單中，否則學生無法向實驗室註冊，即使他們有註冊連結也一樣。 只有清單中的使用者可以使用您傳送的註冊連結，向實驗室註冊。 您可以關閉 [限制存取]，讓學生能向實驗室註冊 (只要他們有註冊連結)。 

### <a name="add-users-from-an-azure-ad-group"></a>從 Azure AD 群組新增使用者

您可以將實驗室使用者清單同步到現有的 Azure Active Directory (Azure AD) 群組，這樣就不需要手動新增或刪除使用者。 

您可以在組織的 Azure Active Directory 中建立 Azure AD 群組，以管理組織資源和雲端式應用程式的存取權。 若要深入了解，請參閱 [Azure AD 群組](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)。 如果您的組織使用 Microsoft Office 365 或 Azure 服務，則您的組織已經有管理您 Azure Active Directory 的管理員。 

> [!IMPORTANT]
> 請確定使用者清單是空的。 如果實驗室中已有您手動新增或透過匯入 CSV 檔案來新增的現有使用者，則不會出現將實驗室同步至現有群組的選項。 

1. 在左側窗格中，選取 [使用者]。 
1. 按一下 [從群組同步]。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="藉由從 Azure AD 群組同步來新增使用者":::
    
1. 系統會提示您選取現有的 Azure AD 群組來同步實驗室。 
    
    如果您在清單中沒看到 Azure AD 群組，可能是因為下列原因：

    -   如果您是 Azure Active Directory 的來賓使用者 (通常是指您在擁有 Azure AD 的組織之外)，而且您無法搜尋 Azure AD 內的群組。 在此情況下，您無法將 Azure AD 群組新增至實驗室。 
    -   透過 Teams 建立的 Azure AD 群組不會顯示在這份清單中。 您可以在 Teams 內新增 Azure 實驗室服務應用程式，直接從其內部建立和管理實驗室。 深入了解[從 Teams 內部管理實驗室的使用者清單](how-to-manage-user-lists-within-teams.md)。 
1. 一旦選取要與實驗室同步的 Azure AD 群組後，請按一下 [新增]。
1. 實驗室開始同步之後，就會將 Azure AD 群組內的所有人以使用者身分提取到實驗室中，而您會看到已更新的使用者清單。 只有此 Azure AD 群組中的人員才能存取您的實驗室。 使用者清單會每隔 24 小時重新整理一次，以符合 Azure AD 群組的最新成員資格。 您也可以按一下 [使用者] 索引標籤中的 [同步] 按鈕，手動同步 Azure AD 群組中的最新變更。
1. 按一下 [邀請所有人] 按鈕可將使用者邀請至您的實驗室，而這會傳送具有實驗室註冊連結的電子郵件給所有使用者。 

### <a name="add-users-manually-from-emails-or-csv-file"></a>從電子郵件或 CSV 檔案手動新增使用者

在本節中，您會手動新增學生 (透過電子郵件地址或上傳 CSV 檔案)。 

#### <a name="add-users-by-email-address"></a>以電子郵件地址新增使用者

1. 在左側窗格中，選取 [使用者]。 
1. 按一下 [手動新增使用者]。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="手動新增使用者":::
1. 選取 [依電子郵件地址新增] (預設)，在不同行或以分號分隔的單行上輸入學生的電子郵件地址。 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="新增使用者的電子郵件地址":::
1. 選取 [儲存]。 

    此清單會顯示目前使用者的電子郵件地址和狀態 (無論他們是否已向實驗室註冊)。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="使用者清單":::

    > [!NOTE]
    > 學生向實驗室註冊後，清單會顯示其名稱。 清單中顯示的名稱都是使用 Azure Active Directory 中學生的名字和姓氏來建立的。 

#### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者

您也可以上傳具有使用者電子郵件地址的 CSV 檔案來新增使用者。 

CSV 文字檔會用來儲存以逗號分隔 (CSV) 的表格式資料 (數字和文字)。 CSV 檔案會儲存以逗號分隔的資訊，而不是將資訊儲存在資料列欄位中 (例如試算表)。 CSV 檔案中的每一行都有相同數目的逗點分隔「欄位」。 您可以使用 Excel 輕鬆地建立和編輯 CSV 檔案。

1. 在 Microsoft Excel 中，建立一個以單一資料行列出學生電子郵件地址的 CSV 檔案。

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="CSV 檔案中的使用者清單":::
1. 在 [使用者] 窗格的頂端選取 [新增使用者]，然後選取 [上傳 CSV]。
1. 選取包含學生電子郵件地址的 CSV 檔案，然後選取 [開啟]。

    [新增使用者] 視窗會顯示 CSV 檔案中的電子郵件地址清單。 
1. 選取 [儲存]。 
1. 在 [使用者] 窗格中，檢視已新增的學生清單。 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="[使用者] 窗格中的新增使用者清單"::: 

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

