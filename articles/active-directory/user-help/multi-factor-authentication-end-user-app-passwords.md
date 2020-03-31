---
title: 如何管理應用密碼 - Azure 活動目錄 |微軟文檔
description: 瞭解應用密碼及其用於兩步驗證的功能。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253217"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>管理適用於雙步驟驗證的應用程式密碼

>[!Important]
>您的系統管理員可能不允許您使用應用程式密碼。 如果沒有看到 [應用程式密碼] **** 的選項，即是無法在貴組織使用此功能。

使用應用程式密碼時，請務必記住：

- 應用密碼是自動生成的，應創建並輸入每個應用一次。

- 每位使用者的密碼以 40 組為限。 如果您在到達該限制之後嘗試建立一個密碼，系統就會提示您先刪除現有的密碼，然後才能建立新密碼。

    >[!Note]
    >Office 2013 用戶端 (包括 Outlook) 支援新式驗證通訊協定，而且可搭配雙步驟驗證。 此支援表示在開啟雙步驟驗證之後，您將不再需要適用於 Office 2013 用戶端的應用程式密碼。 如需詳細資訊，請參閱 [Office 2013 和 Office 2016 用戶端應用程式的新式驗證運作方式](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)一文。

## <a name="create-new-app-passwords"></a>建立新的應用程式密碼

在最初的雙因素驗證註冊過程中，您會獲得單個應用密碼。 如果您需要多個，您必須自己創建它們。 您可以根據組織中如何設置雙重驗證，從多個區域創建應用密碼。 有關註冊以對工作或學校帳戶使用雙因素驗證的詳細資訊，請參閱[概述，瞭解雙因素驗證以及您的工作或學校帳戶](multi-factor-authentication-end-user-first-time.md)及其相關文章。

### <a name="where-to-create-and-delete-your-app-passwords"></a>在哪裡建立和刪除應用程式密碼

您可以根據使用雙重驗證的方式創建和刪除應用密碼：

- **您的組織使用雙重驗證和"附加安全驗證"頁。** 如果您在組織中使用工作或學校帳戶（如），alain@contoso.com具有雙重驗證功能，則可以從["其他安全驗證"頁](https://account.activedirectory.windowsazure.com/Proofup.aspx)管理應用密碼。 有關詳細說明，請參閱使用本文中的["其他安全驗證"頁創建和刪除應用密碼](#create-and-delete-app-passwords-from-the-additional-security-verification-page)。

- **您的組織使用雙重驗證和 Office 365 門戶。** 如果您正在組織中使用工作或學校帳戶（如alain@contoso.com、雙重驗證和 Office 365 應用），則可以從 Office [365 門戶頁](https://www.office.com)管理應用密碼。 有關詳細說明，請參閱使用本文[中的 Office 365 門戶創建和刪除應用密碼](#create-and-delete-app-passwords-using-the-office-365-portal)。

- **您使用的是個人 Microsoft 帳戶的雙因素驗證。** 如果您使用具有雙重驗證的個人 Microsoft 帳戶（例如alain@outlook.com），則可以從["安全基礎知識"頁](https://account.microsoft.com/security/)管理應用密碼。 有關詳細說明，請參閱[將應用密碼與不支援兩步驗證的應用一起使用](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)。

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>從"其他安全驗證"頁創建和刪除應用密碼

您可以在工作或學校帳戶的 **"其他安全驗證**"頁中創建和刪除應用密碼。

1. 登錄到["其他安全驗證"頁](https://account.activedirectory.windowsazure.com/Proofup.aspx)，然後選擇**應用密碼**。

    ![應用密碼頁面，突出顯示應用密碼選項卡](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. 選擇 **"創建**"鍵入需要應用密碼的應用的名稱，然後選擇 **"下一步**"。

    ![創建應用密碼頁面，包含需要密碼的應用名稱](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. 從**應用密碼**頁複製密碼，然後選擇 **"關閉**"。

    ![帶有指定應用密碼的應用密碼頁](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. 在 **"應用密碼**"頁中，確保列出你的應用。

     ![應用密碼頁面，清單中顯示新應用](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. 打開您為其創建應用密碼的應用（例如，Outlook 2010），然後在要求應用密碼時粘貼應用密碼。 每個應用只需執行此操作一次。

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>使用應用密碼頁面刪除應用密碼

1. 在 **"應用密碼**"頁中，選擇要刪除的應用密碼旁邊的 **"刪除**"。

   ![刪除應用程式密碼](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 選取 [是]**** 以確認您想要刪除密碼，然後選取 [關閉]****。

    已成功刪除應用程式密碼。

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>使用 Office 365 入口網站建立和刪除應用程式密碼

如果您搭配公司或學校帳戶和 Office 365 應用程式使用雙步驟驗證，您就可以使用 Office 365 入口網站來建立和刪除應用程式密碼。

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>使用 Office 365 入口網站建立應用程式密碼

1. 登錄到 Office 365，然後轉到["我的帳戶"頁面](https://portal.office.com)，選擇**安全&隱私**，然後展開**其他安全驗證**。

    ![顯示已展開 [其他安全性驗證] 區域的 Office 入口網站](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. 選擇顯示"**創建和管理應用密碼**"以打開**應用密碼**頁的文本。

    ![應用密碼頁面，突出顯示應用密碼選項卡](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. 選擇 **"創建**"鍵入需要應用密碼的應用的名稱，然後選擇 **"下一步**"。

    ![創建應用密碼頁面，包含需要密碼的應用名稱](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. 從**應用密碼**頁複製密碼，然後選擇 **"關閉**"。

    ![帶有指定應用密碼的應用密碼頁](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. 在 **"應用密碼**"頁中，確保列出你的應用。

     ![應用密碼頁面，清單中顯示新應用](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. 打開您為其創建應用密碼的應用（例如，Outlook 2010），然後在要求應用密碼時粘貼應用密碼。 每個應用只需執行此操作一次。

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>使用應用密碼頁面刪除應用密碼

1. 在 **"應用密碼**"頁中，選擇要刪除的應用密碼旁邊的 **"刪除**"。

   ![刪除應用程式密碼](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. 在確認框中選擇 **"是**"，然後選擇 **"關閉**"。

    已成功刪除應用程式密碼。

## <a name="if-your-app-passwords-arent-working-properly"></a>如果您的應用程式密碼無法正確運作

請確定您已正確輸入密碼。 如果您確定已正確輸入密碼，您可以再次嘗試登入，並建立新的應用程式密碼。 如果這兩個選項都不能解決問題，請與組織的説明台聯繫，以便他們可以刪除現有應用密碼，從而允許您創建全新的應用密碼。

## <a name="next-steps"></a>後續步驟

- [管理雙步驟驗證設定](multi-factor-authentication-end-user-manage-settings.md)

- 試用 [Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)，透過應用程式通知來驗證您的登入，而不是透過文字訊息或電話。
