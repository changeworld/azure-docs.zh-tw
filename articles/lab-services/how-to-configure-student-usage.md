---
title: 在 Azure 實驗室服務的教室實驗室中設定使用方式設定
description: 瞭解如何設定實驗室的學生人數、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: d3100f1a7e67e3b0d403375de02cb3daf5fcfb31
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555714"
---
# <a name="add-and-manage-lab-users"></a>新增和管理實驗室使用者

本文說明如何將學生使用者新增至實驗室、向實驗室註冊、控制他們可以使用虛擬機器 (VM) 的額外時數，以及其他更多的時間。 

## <a name="add-users-to-a-lab"></a>將使用者新增至實驗室

在本節中，您會以手動方式或上傳 CSV 檔案的方式，將學生新增至實驗室。 執行下列動作：

1. 在左側窗格中，選取 [使用者]。 

    依預設，[ **限制存取** ] 選項會開啟，而且除非使用者在使用者清單中，否則學生無法向實驗室註冊，即使他們有註冊連結也一樣。 只有列出的使用者可以使用您傳送的註冊連結向實驗室註冊。 在此程序中，您會在清單中新增使用者。 或者，您可以關閉 **限制存取** ，讓學生可以向實驗室註冊，只要他們有註冊連結即可。 

1. 在 [ **使用者** ] 窗格的頂端，選取 [ **新增使用者** ]，然後選取 [透過 **電子郵件地址新增** ]。 

    ![[新增使用者] 按鈕](./media/how-to-configure-student-usage/add-users-button.png)

1. 在 [ **新增使用者** ] 窗格中，在個別行上或以分號分隔的單一行上輸入學生的電子郵件地址。 

    ![新增使用者的電子郵件地址](./media/how-to-configure-student-usage/add-users-email-addresses.png)

1. 選取 [儲存]。 

    此清單會顯示目前使用者的電子郵件地址和狀態，無論他們是否已向實驗室註冊。 

    ![使用者清單](./media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 學生向實驗室註冊之後，清單會顯示其名稱。 清單中顯示的名稱是使用 Azure Active Directory 中學生的名字和姓氏所構成。 

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者

您也可以透過上傳包含其電子郵件地址的 CSV 檔案來新增使用者。 

CSV 文字檔用來儲存逗點分隔的 (CSV) 表格式資料 (數位和文字) 。 CSV 檔案會儲存以逗號分隔的資訊，而不是將資訊儲存在資料列欄位中 (例如在試算表) 中。 CSV 檔案中的每一行都會有相同數目的逗點分隔「欄位」。 您可以使用 Excel 輕鬆地建立和編輯 CSV 檔案。

1. 在 Microsoft Excel 中建立 CSV 檔案，以在一個資料行中列出學生的電子郵件地址。

    ![CSV 檔案中的使用者清單](./media/how-to-configure-student-usage/csv-file-with-users.png)

1. 在 [ **使用者** ] 窗格的頂端，選取 [ **新增使用者** ]，然後選取 **[上傳 CSV** ]。

    ![[上傳 CSV] 按鈕](./media/how-to-configure-student-usage/upload-csv-button.png)

1. 選取包含學生電子郵件地址的 CSV 檔案，然後選取 [ **開啟** ]。

    [ **新增使用者** ] 視窗會顯示來自 CSV 檔案的電子郵件地址清單。 

    ![來自 CSV 檔案之電子郵件地址的 [新增使用者] 視窗](./media/how-to-configure-student-usage/add-users-window.png)

1. 選取 [儲存]。 

1. 在 [ **使用者** ] 窗格中，查看已新增的學生清單。 

    ![[使用者] 窗格中新增的使用者清單](./media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>傳送邀請電子郵件給使用者

若要將註冊連結傳送給新的使用者，請使用下列其中一種方法。 

如果實驗室啟用 [ **限制存取** ] 選項，則只有列出的使用者可以使用註冊連結向實驗室註冊。 這個選項預設為啟用。 

### <a name="invite-all-users"></a>邀請所有使用者

此方法會示範如何將含有註冊連結的電子郵件以及選用的訊息傳送給所有列出的學生。

1. 在 [ **使用者** ] 窗格中，選取 [ **全部邀請** ]。 

    ![[全部邀請] 按鈕](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在 [透過 **電子郵件傳送邀請** ] 視窗中，輸入選擇性訊息，然後選取 [ **傳送** ]。 

    電子郵件會自動包含註冊連結。 若要另外取得和儲存註冊連結，請選取 [ **使用者** ] 窗格頂端的省略號 ( **...** ) ，然後選取 [ **註冊連結** ]。 

    ![[透過電子郵件傳送註冊連結] 視窗](./media/tutorial-setup-classroom-lab/send-email.png)

    [ **使用者** ] 清單中的 [ **邀請** ] 欄會顯示每個新增使用者的邀請狀態。 狀態應該會 **變更為 [** **\<date> 傳送** ]，然後傳送至 [傳送]。 

### <a name="invite-selected-users"></a>邀請選取的使用者

此方法會示範如何只邀請某些學生，以及取得可與其他人共用的註冊連結。

1. 在 [ **使用者** ] 窗格中，選取清單中的一或多個學生。 

1. 在您所選取的學生的資料列中，選取 **信封** 圖示，或在工具列上選取 [ **邀請** ]。 

    ![邀請選取的使用者](./media/how-to-configure-student-usage/invite-selected-users.png)

1. 在 [透過 **電子郵件傳送邀請** ] 視窗中，輸入選擇性 **訊息** ，然後選取 [ **傳送** ]。 

    ![傳送電子郵件給選取的使用者](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    [ **使用者** ] 窗格會在資料表的 [ **邀請** ] 資料行中顯示這項作業的狀態。 邀請電子郵件包含學生可以用來向實驗室註冊的註冊連結。

## <a name="get-the-registration-link"></a>取得註冊連結

在本節中，您可以從入口網站取得註冊連結，然後使用您自己的電子郵件應用程式來傳送。 

1. 在 [ **使用者** ] 窗格中，選取 [ **註冊連結** ]。

    ![學生註冊連結](./media/how-to-configure-student-usage/registration-link-button.png)

1. 在 [ **使用者註冊** ] 視窗中，選取 [ **複製** ]，然後選取 [ **完成** ]。 

    ![[使用者註冊] 視窗](./media/how-to-configure-student-usage/registration-link.png)

    連結會複製到剪貼簿中。 
    
1. 在您的電子郵件應用程式中，貼上註冊連結，然後將電子郵件傳送給學生，讓學生可以註冊該課程。 

## <a name="view-registered-users"></a>檢視已註冊的使用者

1. 移至 [Azure 實驗室服務](https://labs.azure.com) 網站。 
1. 選取 [登 **入** ]，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。
1. 在 [ **我的實驗室** ] 頁面上，選取您要追蹤其使用方式的實驗室。 
1. 在左窗格中，選取 [ **使用者** ]，或選取 [ **使用者** ] 磚。 

    [ **使用者** ] 窗格會顯示已向實驗室註冊的學生清單。  

    ![註冊的使用者清單](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>設定使用者的配額

您可以執行下列動作來設定每個學生的小時配額： 

1. 在 [ **使用者** ] 窗格中，選取工具列上的 [ **每位使用者的配額： \<number> 小時 (s])** 。 
1. 在 [ **每位使用者的配額** ] 視窗中，指定您想要對每位學生提供的排程類別時間以外的時間數，然後選取 [ **儲存** ]。

    ![[每位使用者的配額] 視窗](./media/how-to-configure-student-usage/quota-per-user.png)    

    變更的值現在會顯示在工具列和 [使用者] 清單中的 [ **每位使用者的 \<number of hours> 配額：** ] 按鈕上，如下所示：

    ![每位使用者的配額時數](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Vm 的排程執行時間](how-to-create-schedules.md)不會計入已分配給學生的配額。 此配額適用于學生花費在 Vm 上的排程時間以外的時間。 

## <a name="set-additional-quotas-for-specific-users"></a>設定特定使用者的其他配額

您可以為某些學生指定配額，但不超過上一節中為所有使用者設定的一般配額。 例如，如果您是講師，將所有學生的配額設定為10小時，並為特定學生設定5小時的額外配額，則學生會取得 15 (10 + 5) 小時的配額。 如果您稍後變更一般配額（例如15），學生會取得 20 (15 + 5) 小時的配額。 請記住，此整體配額超出排程時間。 在排程的時間內，學生花費在實驗室 VM 的時間不會計入此配額。 

若要設定其他配額，請執行下列動作：

1. 在 [ **使用者** ] 窗格中，從清單中選取學生，然後選取工具列上的 [ **調整配額** ]。 

    ![[調整配額] 按鈕](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. 在 [ **調整配額 \<selected user or users email address>** ] 中，輸入您想要授與所選學生或學生的額外實驗室時數， **然後選取 [** 套用]。 

    ![「調整配額 ...」視窗](./media/how-to-configure-student-usage/additional-quota.png)

    [ **使用量** ] 欄會顯示所選學生的更新配額。 

    ![使用者的新使用量](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>學生帳戶

若要將學生新增至教室實驗室，請使用其電子郵件帳戶。 學生可能會有下列類型的電子郵件帳戶：

- 由您的大學 Azure Active Directory 實例提供的學生電子郵件帳戶。
- Microsoft 網域的電子郵件帳戶，例如 *outlook.com* 、 *hotmail.com* 、 *msn.com* 或 *live.com* 。
- 非 Microsoft 的電子郵件帳戶，例如 Yahoo！提供的帳戶 或 Google。 不過，這些類型的帳戶必須與 Microsoft 帳戶連結。
- GitHub 帳戶。 此帳戶必須與 Microsoft 帳戶連結。

### <a name="use-a-non-microsoft-email-account"></a>使用非 Microsoft 電子郵件帳戶

學生可以使用非 Microsoft 電子郵件帳戶來註冊並登入教室實驗室。  但是，註冊需要他們先建立連結至其非 Microsoft 電子郵件地址的 Microsoft 帳戶。

許多學生可能已經有連結至其非 Microsoft 電子郵件地址的 Microsoft 帳戶。 例如，如果學生將電子郵件地址與其他 Microsoft 產品或服務（例如 Office、Skype、OneDrive 或 Windows）搭配使用，則已有 Microsoft 帳戶。  

當學生使用註冊連結登入教室時，系統會提示他們輸入其電子郵件地址和密碼。 嘗試以未連結至 Microsoft 帳戶之非 Microsoft 帳戶登入的學生將會收到下列錯誤訊息： 

![登入時的錯誤訊息](./media/how-to-configure-student-usage/cant-find-account.png)

以下是學生 [註冊 Microsoft 帳戶](http://signup.live.com)的連結。  

> [!IMPORTANT]
> 當學生登入教室實驗室時，不會提供建立 Microsoft 帳戶的選項。 基於這個理由，我們建議您將此註冊連結包含 http://signup.live.com 在您傳送給使用非 Microsoft 帳戶之學生的教室實驗室註冊電子郵件中。

### <a name="use-a-github-account"></a>使用 GitHub 帳戶

學生也可以使用現有的 GitHub 帳戶註冊並登入教室實驗室。 如果他們已經有連結到其 GitHub 帳戶的 Microsoft 帳戶，學生可以登入並提供其密碼，如上一節所示。 

如果尚未將其 GitHub 帳戶連結至 Microsoft 帳戶，則可以執行下列作業：

1. 選取 [登 **入選項** ] 連結，如下所示：

    ![[登入選項] 連結](./media/how-to-configure-student-usage/signin-options.png)

1. 在 [登 **入選項** ] 視窗中，選取 [ **使用 GitHub 登入** ]。

    ![[使用 GitHub 登入] 連結](./media/how-to-configure-student-usage/signin-github.png)

    在提示中，學生接著建立連結至其 GitHub 帳戶的 Microsoft 帳戶。 連結會在選取 **[下一步]** 時自動進行。 然後，他們會立即登入並聯機到教室實驗室。

## <a name="export-a-list-of-users-to-a-csv-file"></a>將使用者清單匯出至 CSV 檔案

1. 移至 [ **使用者** ] 窗格。
1. 在工具列上，選取省略號 ( **...** ) ，然後選取 [ **匯出 CSV** ]。 

    ![[匯出 CSV] 按鈕](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>後續步驟

查看下列文章：

- 系統管理員： [建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- 實驗室擁有者： [建立和管理實驗室](how-to-manage-classroom-labs.md) ，並 [設定和發佈範本](how-to-create-manage-template.md)
- 實驗室使用者： [存取教室實驗室](how-to-use-classroom-lab.md)
