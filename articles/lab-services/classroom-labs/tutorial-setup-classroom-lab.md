---
title: 使用 Azure 實驗室服務設定教室實驗室 | Microsoft Docs
description: 在本教學課程中，您會使用 Azure Lab Services 來設定教室實驗室，且其中包含您教室中學生所使用的虛擬機器。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c28375ce7252e93340f395b97224d292940ce65
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719172"
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
若要在實驗室帳戶中設定教室實驗室，您必須是實驗室帳戶中其中一個角色的成員：擁有者、實驗室建立者或參與者。 您用來建立實驗室帳戶的帳戶會自動新增至擁有者角色。

實驗室擁有者可以將其他使用者新增至 [實驗室建立者]  角色。 例如，實驗室擁有者可將教授新增至實驗室建立者角色。 然後，教授會為其班級建立包含 VM 的實驗室。 學生會使用教授提供的註冊連結向實驗室註冊。 註冊後，他們就可以使用實驗室中的 VM 來進行班級工作和在家工作。 如需將使用者新增至實驗室建立者角色的詳細步驟，請參閱[將使用者新增至實驗室建立者角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)。


## <a name="create-a-classroom-lab"></a>建立教室實驗室

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 請注意，目前不支援 Internet Explorer 11。 
2. 選取 [登入]  ，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。 
3. 選取 [新增實驗室]  。 
    
    ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. 在 [新增實驗室]  視窗中，執行下列動作： 
    1. 指定實驗室的**名稱**，然後選取 [下一步]  。  

        ![建立教室實驗室](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        如果您選取 Linux 映像，您會看到可啟用遠端桌面連接的選項。 如需詳細資料，請參閱[啟用 Linux 遠端桌面連線](how-to-enable-remote-desktop-linux.md)。
    2. 在 [虛擬機器認證]  頁面上，指定實驗室中所有 VM 的預設認證。 指定使用者的**名稱**和**密碼**，然後選取 [下一步]  。  

        ![[新增實驗室] 視窗](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > 請記下使用者名稱和密碼。 它們將不會再次顯示。
    3. 在 [實驗室原則]  頁面上，輸入在實驗室的排程時間外所配置給每位使用者的時數 (**每位使用者的配額**)，然後選取 [完成]  。 

        ![每位使用者的配額](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. 您應該會看到下列畫面，其中顯示範本 VM 的建立狀態。 在實驗室中建立範本最多需要 20 分鐘。 

    ![範本 VM 的建立狀態](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在 [範本]  頁面上，執行下列步驟：這些都是本教學課程的**選擇性**步驟。

    2. 選取 [連線]  以連線至範本 VM。 如果是 Linux 範本 VM，您可選擇是否要使用 SSH 或 RDP 連線 (若已啟用 RDP)。
    1. 選取 [重設密碼]  以重設 VM 的密碼。 
    1. 在您的範本 VM 上安裝並設定軟體。 
    1. **停止** VM。  
    1. 輸入範本的 [描述] 
10. 在 [範本]  頁面上，選取工具列上的 [發佈]  。 

    ![[發佈範本] 按鈕](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 在您發佈時，即無法取消發佈。 
8. 在 [發佈範本]  頁面上，輸入您要在實驗室中建立的虛擬機器數目，然後選取 [發佈]  。 

    ![發佈範本 - VM 的數目](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 您會在頁面上看到範本的**發佈狀態**。 此程序最多可能需要一小時。 

    ![發佈範本 - 進度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 選取左側功能表上的 [虛擬機器] 或選取 [虛擬機器] 圖格，以切換到 [虛擬機器集區]  頁面。 確認您看到處於 [未指派]  狀態的虛擬機器。 這些虛擬機器尚未指派給任何學生。 它們應處於 [已停止]  狀態。 您可以在此頁面上啟動學生 VM、連線到 VM、停止 VM，以及刪除 VM。 您可以在此頁面啟動 VM，或是讓學生啟動 VM。 

    ![已停止狀態的虛擬機器](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    您可以在此頁面上執行下列工作 (請勿對本教學課程執行這些步驟。 這些步驟僅供參考之用。)： 
    
    1. 若要變更實驗室容量 (實驗室中的 VM 數目)，請選取工具列上的 [實驗室容量]  。
    2. 若要一次啟動所有 VM，請選取工具列上的 [全部啟動]  。 
    3. 若要啟動特定 VM，請選取 [狀態]  中的向下箭號，然後選取 [啟動]  。 您也可以在第一個資料行中選取 VM，然後選取工具列上的 [啟動]  來啟動 VM。

    如需建立和管理範本，以及設定和管理學生虛擬機器的詳細資訊，請參閱下列文章： 
    
    - [建立和管理教室實驗室範本](how-to-create-manage-template.md)
    - [設定和管理虛擬機器集區](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室

1. 選取左側功能表上的 [使用者]  。 預設會啟用 [限制存取]  選項。 當此設定為開啟時，即使使用者有註冊連結，除非使用者位於使用者清單中，否則也無法向實驗室註冊。 只有清單中的使用者可以使用您傳送的註冊連結，向實驗室註冊。 在此程序中，您會在清單中新增使用者。 或者，您可以關閉 [限制存取]  ，讓使用者能向實驗室註冊 (只要他們有註冊連結)。 
2. 選取工具列上的 [新增使用者]  ，然後選取 [以電子郵件地址新增]  。 

    ![[新增使用者] 按鈕](../media/how-to-configure-student-usage/add-users-button.png)
1. 在 [新增使用者]  頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [儲存]  。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](../media/how-to-configure-student-usage/users-list-new.png)

    在使用者向實驗室註冊後，您會在清單中看到使用者的名稱。 
    
## <a name="set-a-schedule-for-the-lab"></a>設定實驗室的排程
建立實驗室的排程事件，讓實驗室中的 VM 在特定時間自動啟動/停止。 您稍早指定的使用者配額，便是會在此排程時間以外指派給每位使用者的額外時間。 

1. 切換至 [排程]  頁面，然後選取工具列上的 [新增已排定事件]  。 

    ![[排程] 頁面上的 [新增排程] 按鈕](../media/how-to-create-schedules/add-schedule-button.png)
2. 在 [新增已排定事件]  頁面上，執行下列步驟：
    1. 確認已為 [事件類型]  選取 [標準]  。  
    2. 指定班級的**開始日期**。 
    4. 指定您想要讓 VM 啟動的 [啟動時間]  。
    5. 指定要關閉 VM 的 [停止時間]  。 
    6. 針對您指定的啟動和停止時間指定 [時區]  。 
3. 在相同的 [新增已排定事件]  頁面上，選取 [重複]  區段中的目前排程。  

    ![[排程] 頁面上的 [新增排程] 按鈕](../media/how-to-create-schedules/select-current-schedule.png)
5. 在 [重複]  對話方塊中，執行下列步驟：
    1. 確認已為 [重複]  欄位設定 [每週]  。 
    2. 選取您想要讓排程生效的日期。 下列範例中選取了星期一至星期五。 
    3. 選取排程的**結束日期**。
    8. 選取 [儲存]  。 

        ![設定重複排程](../media/how-to-create-schedules/set-repeat-schedule.png)

3. 現在，在 [新增已排定事件]  頁面上的 [附註 (選擇性)]  中，輸入排程的描述或附註。 
4. 在 [新增已排定事件]  頁面上，選取 [儲存]  。 

    ![每週排程](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. 瀏覽至行事曆中的開始日期，確認已設定排程。
    
    ![行事曆中的排程](../media/how-to-create-schedules/schedule-calendar.png)

    如需建立和管理班級排程的詳細資訊，請參閱[建立和管理教室實驗室的排程](how-to-create-schedules.md)。

## <a name="send-invitation-emails-to-students"></a>傳送邀請電子郵件給學生

1. 如果您並未在該頁面上，請切換至 [使用者]  檢視，然後選取工具列上的 [全部邀請]  。 

    ![選取學員](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在 [透過電子郵件傳送邀請]  頁面上輸入選擇性訊息，然後選取 [傳送]  。 電子郵件會自動包含註冊連結。 您可以選取工具列上的 [...(省略符號)]  和 [註冊連結]  ，來取得此註冊連結。 

    ![透過電子郵件傳送註冊連結](../media/tutorial-setup-classroom-lab/send-email.png)
4. 您會在 [使用者]  清單中看到 [邀請]  的狀態。 其狀態應該會依序變更為 [傳送中]  和 [已於 &lt;date&gt; 傳送]  。 

    如需將學生新增到班級及管理其實驗室使用方式的詳細資訊，請參閱[如何設定學生使用方式](how-to-configure-student-usage.md)。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立教室實驗室並已設定實驗室。 若要了解學生可以如何使用註冊連結，來存取實驗室中的 VM，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [連線到教室實驗室中的 VM](tutorial-connect-virtual-machine-classroom-lab.md)

