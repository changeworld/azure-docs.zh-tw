---
title: 在 Azure 實驗室服務的實驗室中設定使用方式設定
description: 瞭解如何設定實驗室的學生人數、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 380a587eecb276c457b93ca3c3f3ac08b2239275
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791958"
---
# <a name="add-and-manage-lab-users"></a>新增和管理實驗室使用者

本文說明如何將學生使用者新增至實驗室、向實驗室註冊、控制他們可以使用虛擬機器 (VM) 的額外時數，以及其他更多的時間。 

當您新增使用者時，[限制存取] 選項會依預設開啟，除非位於使用者清單中，否則學生無法向實驗室註冊，即使他們有註冊連結也一樣。 只有清單中的使用者可以使用您傳送的註冊連結，向實驗室註冊。 您可以關閉 [限制存取]，讓學生能向實驗室註冊 (只要他們有註冊連結)。 

本文說明如何將使用者新增至實驗室。

## <a name="add-users-from-an-azure-ad-group"></a>從 Azure AD 群組新增使用者

### <a name="overview"></a>概觀

您現在可以將實驗室使用者清單同步至現有的 Azure Active Directory (Azure AD) 群組，如此就不需要手動新增或刪除使用者。 

您可以在組織的 Azure Active Directory 中建立 Azure AD 群組，以管理組織資源和雲端式應用程式的存取權。 若要深入了解，請參閱 [Azure AD 群組](../active-directory/fundamentals/active-directory-manage-groups.md)。 如果您的組織使用 Microsoft Office 365 或 Azure 服務，則您的組織已經有管理您 Azure Active Directory 的管理員。 

### <a name="sync-users-with-azure-ad-group"></a>使用 Azure AD 群組同步處理使用者

> [!IMPORTANT]
> 請確定使用者清單是空的。 如果實驗室中已有您手動新增或透過匯入 CSV 檔案來新增的現有使用者，則不會出現將實驗室同步至現有群組的選項。 

1. 登入 [Azure 實驗室服務網站](https://labs.azure.com/)。
1. 選取您要使用的實驗室。
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

### <a name="automatic-management-of-virtual-machines-based-on-changes-to-the-azure-ad-group"></a>根據 Azure AD 群組的變更，自動管理虛擬機器 

實驗室同步到 Azure AD 群組之後，實驗室中的虛擬機器數目就會自動符合群組中的使用者數目。 您將無法再手動更新實驗室容量。 當使用者新增至 Azure AD 群組時，實驗室會自動新增該使用者的虛擬機器。 從 Azure AD 群組中刪除使用者時，實驗室會自動從實驗室刪除使用者的虛擬機器。 

## <a name="add-users-manually-from-emails-or-csv-file"></a>從電子郵件或 CSV 檔案手動新增使用者

在本節中，您會手動新增學生 (透過電子郵件地址或上傳 CSV 檔案)。 

### <a name="add-users-by-email-address"></a>以電子郵件地址新增使用者

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

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者

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

## <a name="send-invitations-to-users"></a>傳送邀請電子郵件給使用者

若要將註冊連結傳送給新的使用者，請使用下列其中一種方法。 

如果實驗室啟用 [ **限制存取** ] 選項，則只有列出的使用者可以使用註冊連結向實驗室註冊。 這個選項預設為啟用。 

### <a name="invite-all-users"></a>邀請所有使用者

此方法會示範如何將含有註冊連結的電子郵件以及選用的訊息傳送給所有列出的學生。

1. 在 [ **使用者** ] 窗格中，選取 [ **全部邀請**]。 

    ![[全部邀請] 按鈕](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在 [透過 **電子郵件傳送邀請** ] 視窗中，輸入選擇性訊息，然後選取 [ **傳送**]。 

    電子郵件會自動包含註冊連結。 若要另外取得和儲存註冊連結，請選取 [**使用者**] 窗格頂端的省略號 (**...**) ，然後選取 [**註冊連結**]。 

    ![[透過電子郵件傳送註冊連結] 視窗](./media/tutorial-setup-classroom-lab/send-email.png)

    [**使用者**] 清單中的 [**邀請**] 欄會顯示每個新增使用者的邀請狀態。 狀態應該會 **變更為 [** **\<date> 傳送**]，然後傳送至 [傳送]。 

### <a name="invite-selected-users"></a>邀請選取的使用者

此方法會示範如何只邀請某些學生，以及取得可與其他人共用的註冊連結。

1. 在 [ **使用者** ] 窗格中，選取清單中的一或多個學生。 

1. 在您所選取的學生的資料列中，選取 **信封** 圖示，或在工具列上選取 [ **邀請**]。 

    ![邀請選取的使用者](./media/how-to-configure-student-usage/invite-selected-users.png)

1. 在 [透過 **電子郵件傳送邀請** ] 視窗中，輸入選擇性 **訊息**，然後選取 [ **傳送**]。 

    ![傳送電子郵件給選取的使用者](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    [ **使用者** ] 窗格會在資料表的 [ **邀請** ] 資料行中顯示這項作業的狀態。 邀請電子郵件包含學生可以用來向實驗室註冊的註冊連結。

## <a name="get-the-registration-link"></a>取得註冊連結

在本節中，您可以從入口網站取得註冊連結，然後使用您自己的電子郵件應用程式來傳送。 

1. 在 [ **使用者** ] 窗格中，選取 [ **註冊連結**]。

    ![學生註冊連結](./media/how-to-configure-student-usage/registration-link-button.png)

1. 在 [ **使用者註冊** ] 視窗中，選取 [ **複製**]，然後選取 [ **完成**]。 

    ![[使用者註冊] 視窗](./media/how-to-configure-student-usage/registration-link.png)

    連結會複製到剪貼簿中。 
    
1. 在您的電子郵件應用程式中，貼上註冊連結，然後將電子郵件傳送給學生，讓學生可以註冊該課程。 

## <a name="view-registered-users"></a>檢視已註冊的使用者

1. 移至 [Azure 實驗室服務](https://labs.azure.com) 網站。 
1. 選取 [登 **入**]，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。
1. 在 [ **我的實驗室** ] 頁面上，選取您要追蹤其使用方式的實驗室。 
1. 在左窗格中，選取 [ **使用者**]，或選取 [ **使用者** ] 磚。 

    [ **使用者** ] 窗格會顯示已向實驗室註冊的學生清單。  

    ![註冊的使用者清單](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>設定使用者的配額

您可以執行下列動作來設定每個學生的小時配額： 

1. 在 [ **使用者** ] 窗格中，選取工具列上的 [ **每位使用者的配額： \<number> 小時 (s])** 。 
1. 在 [ **每位使用者的配額** ] 視窗中，指定您想要對每位學生提供的排程類別時間以外的時間數，然後選取 [ **儲存**]。

    ![[每位使用者的配額] 視窗](./media/how-to-configure-student-usage/quota-per-user.png)    

    變更的值現在會顯示在工具列和 [使用者] 清單中的 [**每位使用者的 \<number of hours> 配額：** ] 按鈕上，如下所示：

    ![每位使用者的配額時數](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Vm 的排程執行時間](how-to-create-schedules.md)不會計入已分配給學生的配額。 此配額適用于學生花費在 Vm 上的排程時間以外的時間。 

## <a name="set-additional-quotas-for-specific-users"></a>設定特定使用者的其他配額

您可以為某些學生指定配額，但不超過上一節中為所有使用者設定的一般配額。 例如，如果您是講師，將所有學生的配額設定為10小時，並為特定學生設定5小時的額外配額，則學生會取得 15 (10 + 5) 小時的配額。 如果您稍後變更一般配額（例如15），學生會取得 20 (15 + 5) 小時的配額。 請記住，此整體配額超出排程時間。 在排程的時間內，學生花費在實驗室 VM 的時間不會計入此配額。 

若要設定其他配額，請執行下列動作：

1. 在 [ **使用者** ] 窗格中，從清單中選取學生，然後選取工具列上的 [ **調整配額** ]。 

    ![[調整配額] 按鈕](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. 在 [**調整配額 \<selected user or users email address>**] 中，輸入您想要授與所選學生或學生的額外實驗室時數，**然後選取 [** 套用]。 

    ![「調整配額 ...」視窗](./media/how-to-configure-student-usage/additional-quota.png)

    [ **使用量** ] 欄會顯示所選學生的更新配額。 

    ![使用者的新使用量](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>學生帳戶

若要將學生新增至教室實驗室，請使用其電子郵件帳戶。 學生可能會有下列類型的電子郵件帳戶：

- 由您的大學 Azure Active Directory 實例提供的學生電子郵件帳戶。
- Microsoft 網域的電子郵件帳戶，例如 *outlook.com*、 *hotmail.com*、 *msn.com* 或 *live.com*。
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

1. 在 [登 **入選項** ] 視窗中，選取 [ **使用 GitHub 登入**]。

    ![[使用 GitHub 登入] 連結](./media/how-to-configure-student-usage/signin-github.png)

    在提示中，學生接著建立連結至其 GitHub 帳戶的 Microsoft 帳戶。 連結會在選取 **[下一步]** 時自動進行。 然後，他們會立即登入並聯機到教室實驗室。

## <a name="export-a-list-of-users-to-a-csv-file"></a>將使用者清單匯出至 CSV 檔案

1. 移至 [ **使用者** ] 窗格。
1. 在工具列上，選取省略號 (**...**) ，然後選取 [ **匯出 CSV**]。 

    ![[匯出 CSV] 按鈕](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)

## <a name="next-steps"></a>後續步驟

查看下列文章：

- 系統管理員： [建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- 實驗室擁有者： [建立和管理實驗室](how-to-manage-classroom-labs.md) ，並 [設定和發佈範本](how-to-create-manage-template.md)
- 實驗室使用者： [存取實驗室](how-to-use-classroom-lab.md)