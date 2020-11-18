---
title: 將 Microsoft Authenticator 應用程式設定為您的驗證方法 - Azure AD
description: 如何設定您的 [安全性資訊] (預覽) 頁面，以使用 Microsoft Authenticator 應用程式作為驗證方法來驗證您的身分識別。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: c96be79a349478db7d0452cae1ff07ee1c48aaa3
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700081"
---
# <a name="set-up-the-microsoft-authenticator-app-as-your-verification-method"></a>將 Microsoft Authenticator 應用程式設定為您的驗證方法

您可以遵循下列步驟來新增雙因素驗證和密碼重設方法。 在您首次將此設定好之後，即可返回 [安全性資訊] 頁面來新增、更新或刪除安全性資訊。

如果系統提示您要在登入公司或學校帳戶之後立即設定此資訊，請參閱[從登入頁面的提示字元中設定安全性資訊](security-info-setup-signin.md)一文中的詳細步驟。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
> 如果您未看到驗證器應用程式選項，可能是您的組織不允許您使用這個選項進行驗證。 在這種情況下，您必須選擇其他方法，或連絡組織的技術支援中心以取得更多協助。

## <a name="security-vs-password-reset-verification"></a>安全性與密碼重設驗證

雙因素安全性驗證和密碼重設都會使用安全性資訊方法。 不過，並非所有方法都同時適用於這兩者。

| 方法 | 用於 |
| ------ | -------- |
| 驗證器應用程式 | 雙因素驗證與密碼重設驗證。 |
| 簡訊 | 雙因素驗證與密碼重設驗證。 |
| 電話 | 雙因素驗證與密碼重設驗證。 |
| 安全性金鑰 | 雙因素驗證與密碼重設驗證。 |
| 電子郵件帳戶 | 僅適用於密碼重設驗證。 您必須選擇另一種方法來進行雙因素驗證。 |
| 安全性問題 | 僅適用於密碼重設驗證。 您必須選擇另一種方法來進行雙因素驗證。 |

## <a name="set-up-the-microsoft-authenticator-app-from-the-security-info-page"></a>從安全性資訊頁面設定 Microsoft Authenticator 應用程式

根據您組織的設定，您可能可以使用驗證應用程式作為其中一個安全性資訊方法。 如果沒有一定要使用 Microsoft Authenticator 應用程式，您可以在設定期間選擇不同的應用程式。 不過，本文是使用 Microsoft Authenticator 應用程式。

>[!Important]
> 如果您在5部不同的裝置上設定 Microsoft Authenticator 應用程式或5個硬體權杖，您將無法設定第六個裝置，而且可能會看到下列錯誤訊息。
> 
> **因為您已經有五個驗證器應用程式或硬體權杖，所以無法設定 Microsoft Authenticator。請洽詢您的系統管理員，以刪除其中一個驗證器應用程式或硬體權杖。**

### <a name="to-set-up-the-microsoft-authenticator-app"></a>設定 Microsoft Authenticator 應用程式

1. 登入您的公司或學校帳戶，然後前往 https://myaccount.microsoft.com/ 頁面。

    ![[我的設定檔] 頁面，顯示醒目提示的 [安全性資訊] 連結](media/security-info/securityinfo-myprofile-page.png)

2. 以左側瀏覽窗格或是 [安全性資訊] 區塊中的連結選取 [安全性資訊]，然後在 [安全性資訊] 頁面選取 [新增方法]。

    ![醒目提示 [新增方法] 選項的 [安全性資訊] 頁面](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在 [新增方法] 頁面上，從下拉式清單中選取 [驗證器應用程式]，然後選取 [新增]。

    ![已選取驗證器應用程式的新增方法方塊](media/security-info/securityinfo-myprofile-addauthapp.png)

4. 在 [從取得應用程式開始] 頁面上選取 [立即下載]，以在您的行動裝置下載並安裝 Microsoft Authenticator 應用程式，然後選取 [下一步]。

    如需關於下載並安裝應用程式的詳細資訊，請參閱[下載並安裝 Microsoft Authenticator 應用程式](user-help-auth-app-download-install.md)。

    ![從取得應用程式開始的頁面](media/security-info/securityinfo-myprofile-getauthapp.png)

   > [!Note]
   > 如果您想要使用 Microsoft Authenticator 應用程式以外的驗證器應用程式，請選取 [我想要使用其他驗證器應用程式] 連結。
   >
   > 如果您的組織允許您選擇驗證器應用程式以外的方法，您可以選取 [我想要設定其他方法連結]。

5. 在行動裝置上設定 Microsoft Authenticator 應用程式時，請讓 [設定帳戶] 頁面保持開啟。

    ![設定驗證器應用程式的頁面](media/security-info/securityinfo-myprofile-setupauthapp.png)

6. 開啟 Microsoft Authenticator 應用程式、選取要允許通知 (若出現提示)、從右上角的 [自訂和控制] 圖示中選取 [新增帳戶]，然後選取 [公司或學校帳戶]。

    >[!Note]
    >如果這是您第一次設定 Microsoft Authenticator 應用程式，系統可能會顯示提示詢問您是否允許應用程式存取您的相機 (iOS)，或是否允許應用程式拍攝相片和錄製影片 (Android)。 您必須選取 [允許]，讓驗證器應用程式能夠存取您的相機，以在下一個步驟中拍攝 QR 代碼的相片。 如果您不允許存取相機，您仍可設定驗證器應用程式，但必須手動新增代碼資訊。 如需如何手動新增代碼的相關資訊，請參閱[手動新增帳戶至應用程式](user-help-auth-app-add-account-manual.md)。

7. 返回電腦上的 [設定帳戶] 頁面，然後選取 [下一步]。

    [掃描 QR 代碼] 頁面隨即出現。

    ![使用驗證器應用程式的 [掃描 QR 代碼]](media/security-info/securityinfo-myprofile-qrcodeauthapp.png)

8. 使用 Microsoft Authenticator 應用程式 QR 代碼讀取器，掃描您在步驟 6 建立工作或學校帳戶後顯示在行動裝置上的代碼。

    驗證器應用程式應會成功新增您的工作或學校帳戶，而不需要您提供任何額外的資訊。 不過，如果您的 QR 代碼讀取器無法讀取代碼，您可以選取 [無法掃描 QR 代碼] 連結，然後以手動方式將代碼和 URL 輸入到 Microsoft Authenticator 應用程式。 如需手動新增代碼的詳細資訊，請參閱[手動新增帳戶至應用程式](user-help-auth-app-add-account-manual.md)。

9. 在電腦的 [掃描 QR 代碼] 頁面上選取 [下一步]。

    此時會有通知傳送到您行動裝置上的 Microsoft Authenticator 應用程式，用以測試您的帳戶。

    ![使用驗證器應用程式測試您的帳戶](media/security-info/securityinfo-myprofile-tryitauthapp.png)

10. 在 Microsoft Authenticator 應用程式中核准通知，然後選取 [下一步]。

     ![成功通知，指出正在連線應用程式和您的帳戶](media/security-info/securityinfo-myprofile-successauthapp.png)

     您的安全性資訊會更新為使用 Microsoft Authenticator 應用程式作為預設方法，在使用雙步驟驗證或密碼重設時驗證您的身分識別。

## <a name="delete-your-authenticator-app-from-your-security-info-methods"></a>從您的安全性資訊方法中刪除驗證器應用程式

如果您不想再使用驗證器應用程式作為安全性資訊方法，您可以從 [安全性資訊] 頁面中刪除應用程式。 這適用於所有驗證器應用程式，並非只適用於 Microsoft Authenticator 應用程式。 刪除應用程式之後，您必須進入行動裝置上的驗證器應用程式，然後刪除帳戶。

>[!Important]
>您無法復原不小心刪除的驗證器應用程式。 您必須依照本文中[設定驗證器應用程式](#set-up-the-microsoft-authenticator-app-from-the-security-info-page)一節的步驟，再次新增驗證器應用程式。

### <a name="to-delete-the-authenticator-app"></a>刪除驗證器應用程式

1. 在 [安全性資訊] 頁面上，選取驗證器應用程式旁的 [刪除] 連結。

    ![從安全性資訊中刪除驗證器應用程式的連結](media/security-info/securityinfo-myprofile-deleteauthapp.png)

2. 從確認方塊中選取 [是]，以刪除驗證器應用程式。 刪除驗證器應用程式之後，該應用程式就會從您的安全性資訊中移除，並且從 [安全性資訊] 頁面上消失。 如果驗證器應用程式是預設方法，則預設值將會變更為另一個可用方法。

3. 開啟您行動裝置上的驗證器應用程式，選取 [編輯帳戶]，然後從驗證器應用程式中刪除您的公司或學校帳戶。

    您的帳戶已完全從用於雙因素驗證和密碼重設要求的驗證器應用程式中移除。

## <a name="change-your-default-security-info-method"></a>變更預設的安全性資訊方法

如果您想要在使用雙因素驗證登入公司或學校帳戶時，或提出密碼重設要求時，使用驗證器應用程式作為預設方法，您可以從安全性 **資訊** 頁面進行設定。

### <a name="to-change-your-default-security-info-method"></a>變更預設的安全性資訊方法

1. 在 [安全性資訊] 頁面上，選取 [預設登入方法] 資訊旁的 [變更] 連結。

    ![變更預設登入方法的連結](media/security-info/securityinfo-myprofile-changedefaultauthapp.png)

2. 從可用方法的下拉式清單中選擇 [Microsoft Authenticator - 通知]。 如果您不是使用 Microsoft Authenticator 應用程式，請選取 [驗證器應用程式或硬體權杖] 選項。

    ![選擇預設登入方法](media/security-info/securityinfo-myprofile-defaultauthapp.png)

3. 選取 [確認]。

    用於登入的預設方法會變更為 Microsoft Authenticator 應用程式。

## <a name="additional-security-info-methods"></a>其他安全性資訊方法

根據您要嘗試執行的動作，您會有組織如何連絡您來驗證您身分識別的其他選項。 選項包括：

- **行動裝置簡訊。** 輸入您的行動裝置號碼，然後取得將用來進行雙步驟驗證或密碼重設的代碼簡訊。 如需如何透過簡訊 (SMS) 驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用簡訊 (SMS)](security-info-setup-text-msg.md)。

- **行動裝置或公司電話。** 輸入您的行動裝置號碼，然後等候來電以進行雙步驟驗證或密碼重設。 如需如何透過電話號碼驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用電話](security-info-setup-phone-number.md)。

- **安全性金鑰。** 註冊與 Microsoft 相容的安全性金鑰，並搭配 PIN 使用以便進行雙步驟驗證或密碼重設。 如需如何透過安全性金鑰驗證身分識別的逐步指示，請參閱[將安全性資訊設定為使用安全性金鑰](security-info-setup-security-key.md)。

- **電子郵件地址。** 輸入您的公司或學校電子郵件地址以接收用於密碼重設的電子郵件。 此選項不適用於雙步驟驗證。 如需如何設定電子郵件的逐步指示，請參閱[將安全性資訊設定為使用電子郵件](security-info-setup-email.md)。

- **安全性問題。** 回答由您的系統管理員為組織所建立的一些安全性問題。 此選項僅適用於密碼重設，不適用雙步驟驗證。 如需如何設定安全性問題的逐步指示，請參閱[將安全性資訊設定為使用安全性問題](security-info-setup-questions.md)一文。

    >[!Note]
    >如果這其中一些選項遺失，很可能是因為您的組織不允許那些方法。 如果發生這種情況，您將必須選擇可用的方法，或連絡您的系統管理員以取得更多協助。

## <a name="next-steps"></a>後續步驟

- 若要使用 Microsoft Authenticator 應用程式登入，請遵循[使用雙步驟驗證或安全性資訊登入](security-info-setup-signin.md)一文中的步驟進行。

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)或遵循[重設公司或學校密碼](active-directory-passwords-update-your-own-password.md)一文中的步驟來重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
