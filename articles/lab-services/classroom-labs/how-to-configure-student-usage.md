---
title: 在 Azure 實驗室服務的教室實驗室中設定使用方式設定
description: 了解如何設定實驗室的使用者數目、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 9fda1295bcdcd44b8a92c101c2aa445197ce6d4a
ms.sourcegitcommit: 163be411e7cd9c79da3a3b38ac3e0af48d551182
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77539141"
---
# <a name="add-and-manage-lab-users"></a>新增和管理實驗室使用者
本文說明如何將使用者新增至實驗室、讓他們向實驗室註冊、控制他們可以使用 VM 的時數等等。 


## <a name="add-users-to-the-lab"></a>將使用者新增至實驗室

1. 選取左側功能表上的 [使用者]。 預設會啟用 [限制存取] 選項。 當此設定為開啟時，即使使用者有註冊連結，除非使用者位於使用者清單中，否則也無法向實驗室註冊。 只有清單中的使用者可以使用您傳送的註冊連結，向實驗室註冊。 在此程序中，您會在清單中新增使用者。 或者，您可以關閉 [限制存取]，讓使用者能向實驗室註冊 (只要他們有註冊連結)。 
2. 選取工具列上的 [新增使用者]，然後選取 [以電子郵件地址新增]。 

    ![[新增使用者] 按鈕](../media/how-to-configure-student-usage/add-users-button.png)
1. 在 [新增使用者] 頁面上的不同行或以分號隔開的單一行中，輸入使用者的電子郵件地址。 

    ![新增使用者電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. 選取 [儲存]。 您會在清單中看到使用者的電子郵件地址及其狀態 (是否已註冊)。 

    ![使用者清單](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 在使用者向實驗室註冊後，您會在清單中看到使用者的名稱。 清單中顯示的名稱是在 Azure Active Directory 中使用使用者的名字和姓氏所建立。 

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者
您也可以上傳具有使用者電子郵件地址的 CSV 檔案，藉此新增使用者。

1. 建立將使用者的電子郵件地址都放在一個資料行中的 CSV 檔案。

    ![具有使用者的 CSV 檔案](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. 在實驗室的 [**使用者**] 頁面上，選取工具列上的 [**新增使用者**]，然後選取 **[上傳 CSV**]。

    ![[上傳 CSV] 按鈕](../media/how-to-configure-student-usage/upload-csv-button.png)
3. 選取具有使用者電子郵件地址的 CSV 檔案。 當您在選取 CSV 檔案之後選取 [開啟] 時，會看到以下的 [新增使用者] 視窗。 電子郵件地址清單中會填入來自 CSV 檔案的電子郵件地址。 

    ![[新增使用者] 視窗中填入來自 CSV 檔案的電子郵件地址](../media/how-to-configure-student-usage/add-users-window.png)
4. 在 [新增使用者] 視窗中選取 [儲存]。 
5. 確認您在使用者清單中看到使用者。 

    ![已新增的使用者清單](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>傳送邀請給使用者
若要將註冊連結傳送給學生，請使用下列其中一種方法。 第一種方法會示範如何使用註冊連結和選擇性訊息，將電子郵件傳送給學生。 第二種方法會向您示範如何取得註冊連結，以您想要的方式與其他人共用。 

如果實驗室啟用 [限制存取]，則只有使用者清單中的使用者可以使用註冊連結向實驗室註冊。 此選項預設為啟用。 

### <a name="invite-all-users"></a>邀請所有使用者
1. 如果您並未在該頁面上，請切換至 [使用者] 檢視，然後選取工具列上的 [全部邀請]。 

    ![選取學員](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在 [透過電子郵件傳送邀請] 頁面上輸入選擇性訊息，然後選取 [傳送]。 電子郵件會自動包含註冊連結。 您可以選取工具列上的 [...(省略符號)] 和 [註冊連結]，來取得此註冊連結。 

    ![透過電子郵件傳送註冊連結](../media/tutorial-setup-classroom-lab/send-email.png)
4. 您會在 [使用者] 清單中看到 [邀請] 的狀態。 狀態應該會**變更為 [** 傳送中]，然後**在 \<日期 > 上傳送**。 

    如需將學生新增到班級及管理其實驗室使用方式的詳細資訊，請參閱[如何設定學生使用方式](how-to-configure-student-usage.md)。

### <a name="invite-selected-users"></a>邀請選取的使用者

1. 選取清單中的使用者或多個使用者。 
2. 然後，選取您在選取的資料列中看到的**信封**圖示（或）在工具列上選取 [**邀請**]。 

    ![邀請選取的使用者](../media/how-to-configure-student-usage/invite-selected-users.png)
3. 在 [透過**電子郵件傳送邀請**] 視窗中，輸入選用的**訊息**，然後選取 [**傳送**]。 

    ![傳送電子郵件給選取的使用者](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    您會在 [**使用者**] 清單的 [**邀請**] 資料行中看到這項作業的狀態。 邀請電子郵件包含註冊連結，可讓使用者用來向實驗室註冊。

1. 如果您還不在頁面上，請切換到 [**使用者**] 視圖。 

## <a name="get-registration-link"></a>取得註冊連結
您也可以從入口網站取得註冊連結，並使用您自己的電子郵件客戶程式來傳送。 

1. 選取左側功能表上的 [使用者]，以切換至 [使用者] 檢視。 
2. 選取 **.。。（省略號）** 在工具列上，然後選取 [**註冊連結**]。

    ![學生註冊連結](../media/how-to-configure-student-usage/registration-link-button.png)
1. 在 [**使用者註冊**] 對話方塊中，選取 [**複製**] 按鈕。 連結會複製到剪貼簿中。 將連結貼到電子郵件編輯器中，然後將電子郵件傳送給學生。 

    ![學生註冊連結](../media/how-to-configure-student-usage/registration-link.png)
2. 在 [**使用者註冊**] 對話方塊上，選取 [**完成**]。 
4. 將**註冊連結**傳送給學生，讓學生可以註冊類別。 

## <a name="view-registered-users"></a>查看已註冊的使用者

1. 瀏覽至 [Azure 實驗室服務網站](https://labs.azure.com)。 
2. 選取 [登入]，然後輸入您的認證。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。
3. 在 [我的實驗室] 頁面上，選取您要追蹤其使用情況的實驗室。 
4. 選取左側功能表或 [使用者] 圖格上的 [使用者]。 您會看到已向實驗室註冊的學生。  

    ![已註冊的使用者](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>設定使用者的配額
您可以使用下列步驟來設定每位使用者的配額： 

1. 如果頁面還不是在作用中，請選取左側功能表上的 [使用者]。 
2. 選取工具列上的 **每位使用者的配額： \< > 小時數**。 
3. 在 [**每位使用者的配額**] 頁面上，指定您想要提供給每位使用者（student）在排定的類別時間之外的時數，然後選取 [**儲存**]。

    ![每位使用者的配額](../media/how-to-configure-student-usage/quota-per-user.png)    
5. 您會立即在工具列上看到變更的值：**每位使用者的配額： \<> 時數**，也會在 [使用者] 清單中。

    ![每位使用者的配額-之後](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > VM 的排程執行時間不會計入[分配給使用者的配額](how-to-create-schedules.md)中。 該配額用於學生在排程時間以外花費於 VM 上的時間。 

## <a name="set-additional-quota-for-a-specific-user"></a>為特定使用者設定額外的配額
您可以為使用者指定額外的配額。 此配額除了上一節中所有使用者所設定的一般配額之外。 例如，如果您（身為講師）將所有使用者的配額設為10小時，並為特定使用者設定5小時的額外配額，則該使用者會取得15（10 + 5）小時的配額。 如果您稍後將一般配額變更為（例如15），則使用者會取得20（15 + 5）小時的配額。 請記住，此整體配額是在排定的時間外。 在排程時間期間，學生在實驗室 VM 上花費的時間，不會計入此配額。 

若要執行此作業，請遵循下列步驟：

1. 在 [**使用者**] 頁面上，從使用者清單中選取使用者（student）。
2. 然後，從工具列選取 [**調整配額**]。 

    ![調整配額按鈕](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. 輸入所選使用者或使用者的**額外**時數，然後選取 [套用] **。** 

    ![使用者的額外配額](../media/how-to-configure-student-usage/additional-quota.png)
4. 您會在 [**使用量**] 資料行中看到使用者的已更新使用方式。 

    ![使用者的新使用方式](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>學生帳戶
若要將學生新增至教室實驗室，請使用其電子郵件帳戶。 可能會使用下列類型的電子郵件帳戶：

- 大學的 Office 365 Azure Active Directory （AAD）所提供的學生電子郵件帳戶。 
- Microsoft 電子郵件帳戶，例如 `@outlook.com`、`@hotmail.com`、`@msn.com`或 `@live.com`。
- 非 Microsoft 的電子郵件帳戶，例如 Yahoo 或 Google 提供的帳戶。 不過，這些類型的帳戶必須與 Microsoft 帳戶連結。
- GitHub 帳戶。 此帳戶必須與 Microsoft 帳戶連結。

### <a name="using-a-non-microsoft-email-account"></a>使用非 Microsoft 電子郵件帳戶
學生可以使用非 Microsoft 的電子郵件帳戶來註冊並登入教室實驗室。  不過，註冊要求學生必須先建立一個連結到其非 Microsoft 電子郵件地址的 Microsoft 帳戶。

許多學生可能已經有一個 Microsoft 帳戶連結到其非 Microsoft 的電子郵件地址。 例如，如果學生已將其電子郵件地址與 Microsoft 的其他產品或服務（例如 Office、Skype、OneDrive 或 Windows）搭配使用，他們就已經有 Microsoft 帳戶。  

當學生按一下註冊 URL 來登入教室時，系統會提示他們輸入其電子郵件地址和密碼。 如果學生嘗試使用未連結 Microsoft 帳戶的非 Microsoft 帳戶進行登入，學生將會收到下列錯誤訊息： 

![錯誤訊息](../media/how-to-configure-student-usage/cant-find-account.png)

若要註冊 Microsoft 帳戶，學生應前往[http://signup.live.com](http://signup.live.com)。  

> [!IMPORTANT]
> 當學生登入教室實驗室時，不會提供建立 Microsoft 帳戶的選項。 因此，我們建議您將此註冊連結包含在您傳送給使用非 Microsoft 帳戶之學生的課堂實驗室註冊電子郵件中。

### <a name="using-a-github-account"></a>使用 GitHub 帳戶
學生也可以使用現有的 GitHub 帳戶來註冊並登入教室實驗室。 如果學生 Microsoft 帳戶已連結至其 GitHub 帳戶，則他們可以登入並提供其密碼，如上一節所示。 如果他們尚未將其 GitHub 帳戶連結到 Microsoft 帳戶，他們應該選取 [登**入選項**]：

![登入選項連結](../media/how-to-configure-student-usage/signin-options.png)

在 [登**入選項**] 頁面上，選取 [**使用 GitHub 登入**]。

![使用 GitHub 連結進行登入](../media/how-to-configure-student-usage/signin-github.png)

最後，系統會提示他們建立連結至其 GitHub 帳戶的 Microsoft 帳戶。 當學生選取 **[下一步]** 時，就會自動發生。  然後，學生會立即登入並聯機到教室實驗室。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [以管理員身分建立及管理實驗室帳戶](how-to-manage-lab-accounts.md)
- [以實驗室擁有者身分建立及管理實驗室](how-to-manage-classroom-labs.md)
- [以實驗室擁有者身分設定及發佈範本](how-to-create-manage-template.md)
- [以實驗室使用者的身分存取教室實驗室](how-to-use-classroom-lab.md)
