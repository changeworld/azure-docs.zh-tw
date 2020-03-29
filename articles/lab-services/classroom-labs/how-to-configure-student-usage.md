---
title: 在 Azure 實驗室服務的教室實驗室中配置使用設置
description: 瞭解如何配置實驗室的學生人數、讓他們在實驗室註冊、控制他們可以使用 VM 的小時數等。
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
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159450"
---
# <a name="add-and-manage-lab-users"></a>新增和管理實驗室使用者

本文介紹如何將學生使用者添加到實驗室、向實驗室註冊、控制他們可以使用虛擬機器 （VM） 的額外小時數等。 

## <a name="add-users-to-a-lab"></a>將使用者新增至實驗室

在本節中，您可以手動將學生添加到實驗室或通過上傳 CSV 檔。 執行下列動作：

1. 在左側窗格中，選取 [使用者]****。 

    預設情況下，"**限制訪問**"選項處於打開狀態，除非他們在使用者清單中，否則即使學生有註冊連結，也不能在實驗室註冊。 只有列出的使用者可以使用您發送的註冊連結向實驗室註冊。 在此程序中，您會在清單中新增使用者。 或者，您可以關閉 **"限制訪問**"，允許學生註冊實驗室，只要他們有註冊連結。 

1. 在 **"使用者"** 窗格的頂部，選擇 **"添加使用者**"，然後選擇"**按電子郵件地址添加**"。 

    !["添加使用者"按鈕](../media/how-to-configure-student-usage/add-users-button.png)

1. 在"**添加使用者"** 窗格中，在單獨的行或由分號分隔的一行上輸入學生的電子郵件地址。 

    ![添加使用者的電子郵件地址](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. 選取 [儲存]****。 

    該清單顯示當前使用者的電子郵件地址和狀態，無論他們是否在實驗室註冊。 

    ![使用者清單](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > 學生註冊到實驗室後，清單將顯示他們的姓名。 清單中顯示的名稱是使用 Azure 活動目錄中學生的名字和姓氏構造的。 

### <a name="add-users-by-uploading-a-csv-file"></a>上傳 CSV 檔案以新增使用者

您還可以通過上傳包含其電子郵件地址的 CSV 檔來添加使用者。

1. 在 Microsoft Excel 中，創建一個 CSV 檔，該檔將學生的電子郵件地址列在一列中。

    ![CSV 檔中的使用者清單](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. 在 **"使用者"** 窗格的頂部，選擇 **"添加使用者**"，然後選擇"**上載 CSV**"。

    !["上傳 CSV"按鈕](../media/how-to-configure-student-usage/upload-csv-button.png)

1. 選擇包含學生電子郵件地址的 CSV 檔，然後選擇 **"打開**"。

    "**添加使用者"** 視窗顯示來自 CSV 檔的電子郵件地址清單。 

    ![包含 CSV 檔的電子郵件地址的"添加使用者"視窗](../media/how-to-configure-student-usage/add-users-window.png)

1. 選取 [儲存]****。 

1. 在"**使用者"** 窗格中，查看添加的學生清單。 

    !["使用者"窗格中添加的使用者清單](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>向使用者發送邀請

要向新使用者發送註冊連結，請使用以下方法之一。 

如果為實驗室啟用了 **"限制訪問**"選項，則只有列出的使用者可以使用註冊連結註冊到實驗室。 這個選項預設為啟用。 

### <a name="invite-all-users"></a>邀請所有使用者

此方法演示如何向所有列出的學生髮送包含註冊連結和可選消息的電子郵件。

1. 在"**使用者"** 窗格中，選擇 **"邀請所有**"。 

    !["全部邀請"按鈕](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. 在"**通過電子郵件發送邀請"** 視窗中，輸入可選郵件，然後選擇"**發送**"。 

    電子郵件會自動包含註冊連結。 要單獨獲取和保存註冊連結，請選擇 **"使用者"** 窗格頂部的省略號 **（.），** 然後選擇 **"註冊連結**"。 

    !["通過電子郵件發送註冊連結"視窗](../media/tutorial-setup-classroom-lab/send-email.png)

    **"使用者"** 清單**的"邀請"** 列顯示每個添加使用者的邀請狀態。 狀態應更改為 **"發送"，** 然後更改為 **"發送\<日期>。** 

### <a name="invite-selected-users"></a>邀請所選使用者

此方法演示如何僅邀請某些學生並獲得註冊連結，您可以與其他人共用。

1. 在"**使用者"** 窗格中，在清單中選擇一個學生或多個學生。 

1. 在所選學生的行中，選擇**信封**圖示，或在工具列上選擇 **"邀請**"。 

    ![邀請所選使用者](../media/how-to-configure-student-usage/invite-selected-users.png)

1. 在"**通過電子郵件發送邀請"** 視窗中，輸入可選**郵件**，然後選擇"**發送**"。 

    ![向所選使用者發送電子郵件](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    "**使用者"** 窗格在表的"**邀請"** 列中顯示此操作的狀態。 邀請電子郵件包括學生可用於在實驗室註冊的註冊連結。

## <a name="get-the-registration-link"></a>獲取註冊連結

在本節中，您可以從門戶獲取註冊連結，並使用您自己的電子郵件應用程式發送該連結。 

1. 在 **"使用者"** 窗格中，選擇 **"註冊"連結**。

    ![學生註冊連結](../media/how-to-configure-student-usage/registration-link-button.png)

1. 在 **"使用者註冊**"視窗中，選擇 **"複製**"，然後選擇 **"完成**"。 

    !["使用者註冊"視窗](../media/how-to-configure-student-usage/registration-link.png)

    連結會複製到剪貼簿中。 
    
1. 在電子郵件應用程式中，粘貼註冊連結，然後將電子郵件發送給學生，以便學生可以註冊課程。 

## <a name="view-registered-users"></a>檢視已註冊的使用者

1. 轉到[Azure 實驗室服務](https://labs.azure.com)網站。 
1. 選擇 **"登錄**"，然後輸入憑據。 Azure 實驗室服務支援組織帳戶和 Microsoft 帳戶。
1. 在 **"我的實驗室"** 頁面上，選擇要跟蹤其使用方式的實驗室。 
1. 在左側窗格中，選擇 **"使用者**"或"**選擇"使用者**"磁貼。 

    "**使用者"** 窗格顯示在實驗室註冊的學生清單。  

    ![註冊使用者清單](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>設定使用者的配額

您可以通過執行以下操作為每個學生設置一小時配額： 

1. 在 **"使用者"** 窗格中，選擇**工具列上的\<"每個使用者配額：>小時數**"。 
1. 在 **"每個使用者配額"** 視窗中，指定要在計畫上課時間之外給每個學生的小時數，然後選擇 **"保存**"。

    !["每個使用者的配額"視窗](../media/how-to-configure-student-usage/quota-per-user.png)    

    更改的值現在顯示在 **"每個使用者的\<配額：工具列>** 按鈕和使用者清單中的小時數，如下所示：

    ![每個使用者的配額小時數](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [VM 的計畫執行時間](how-to-create-schedules.md)不計入分配給學生的配額。 配額是學生花費在 VM 上的計畫時間以外的時間。 

## <a name="set-additional-quotas-for-specific-users"></a>為特定使用者設置其他配額

您可以為某些學生指定配額，超出上一節中為所有使用者設置的通用配額。 例如，如果您作為教師，將所有學生的配額設置為 10 小時，並為特定學生設置 5 小時的額外配額，則該學生將獲得 15 （10 + 5） 小時的配額。 如果您稍後將公共配額更改為 15，則學生將獲得 20 小時（15 + 5） 小時的配額。 請記住，此總配額超出計畫時間。 學生在計畫時間內在實驗室 VM 上花費的時間不計入此配額。 

要設置其他配額，可以執行以下操作：

1. 在"**使用者"** 窗格中，從清單中選擇一個學生，然後在工具列上選擇 **"調整配額**"。 

    !["調整配額"按鈕](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. 在**\<"調整所選使用者或使用者電子郵件地址>的"配額"中**，輸入要授予所選學生或學生的額外實驗室小時數，然後選擇"**應用**"。 

    !["調整配額..."視窗](../media/how-to-configure-student-usage/additional-quota.png)

    "**使用方式"** 列顯示所選學生的更新配額。 

    ![使用者的新用法](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>學生帳戶

要將學生添加到教室實驗室，請使用他們的電子郵件帳戶。 學生可能具有以下類型的電子郵件帳戶：

- 由大學為 Office 365 提供的 Azure 活動目錄實例提供的學生電子郵件帳戶。 
- 微軟域電子郵件帳戶，如*outlook.com*outlook.com、hotmail.com、msn.com*hotmail.com*或*live.com*。 *msn.com*
- 非微軟電子郵件帳戶，如雅虎提供的電子郵件帳戶 或谷歌。 但是，這些類型的帳戶必須與 Microsoft 帳戶連結。
- GitHub 帳戶。 此帳戶必須與 Microsoft 帳戶連結。

### <a name="use-a-non-microsoft-email-account"></a>使用非 Microsoft 電子郵件帳戶
學生可以使用非 Microsoft 電子郵件帳戶註冊並登錄到教室實驗室。  但是，註冊要求他們首先創建連結到其非 Microsoft 電子郵件地址的 Microsoft 帳戶。

許多學生可能已經擁有連結到其非 Microsoft 電子郵件地址的 Microsoft 帳戶。 例如，如果學生將電子郵件地址與其他 Microsoft 產品或服務（如 Office、Skype、OneDrive 或 Windows）一起使用，則他們已擁有 Microsoft 帳戶。  

當學生使用註冊連結登錄到教室時，系統會提示他們輸入電子郵件地址和密碼。 嘗試使用未連結到 Microsoft 帳戶的非 Microsoft 帳戶登錄的學生將收到以下錯誤訊息： 

![登錄時出現錯誤訊息](../media/how-to-configure-student-usage/cant-find-account.png)

下面是一個連結，供學生[註冊一個微軟帳戶](http://signup.live.com)。  

> [!IMPORTANT]
> 當學生登錄到教室實驗室時，他們無法選擇創建 Microsoft 帳戶。 因此，我們建議您在教室實驗室註冊電子郵件中包括此註冊連結http://signup.live.com，以及您發送給使用非 Microsoft 帳戶的學生的教室實驗室註冊電子郵件中。

### <a name="use-a-github-account"></a>使用 GitHub 帳戶
學生還可以使用現有的 GitHub 帳戶註冊並登錄到教室實驗室。 如果他們已經有一個 Microsoft 帳戶連結到其 GitHub 帳戶，學生可以登錄並提供其密碼，如上一節所示。 

如果他們尚未將其 GitHub 帳戶連結到 Microsoft 帳戶，則可以執行以下操作：

1. 選擇**登錄選項**連結，如下所示：

    !["登錄選項"連結](../media/how-to-configure-student-usage/signin-options.png)

1. 在 **"登錄"選項**視窗中，選擇**使用 GitHub 登錄**。

    !["使用 GitHub 登錄"連結](../media/how-to-configure-student-usage/signin-github.png)

    提示後，學生將創建連結到其 GitHub 帳戶的 Microsoft 帳戶。 當他們選擇 **"下一步**"時，連結會自動發生。 然後，他們立即登錄並連接到教室實驗室。

## <a name="export-a-list-of-users-to-a-csv-file"></a>將使用者清單匯出到 CSV 檔

1. 轉到 **"使用者"** 窗格。
1. 在工具列上，選擇省略號 **（...），** 然後選擇 **"匯出 CSV**"。 

    !["匯出 CSV"按鈕](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>後續步驟
查看下列文章：

- 對於管理員：[創建和管理實驗室帳戶](how-to-manage-lab-accounts.md)
- 對於實驗室擁有者：[創建和管理實驗室](how-to-manage-classroom-labs.md)以及[設置和發佈範本](how-to-create-manage-template.md)
- 對於實驗室使用者：[訪問教室實驗室](how-to-use-classroom-lab.md)
