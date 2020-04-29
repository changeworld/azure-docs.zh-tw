---
title: 設定文字訊息做為您的驗證方法-Azure AD
description: 如何設定您的安全性資訊（預覽）頁面，以使用文字訊息作為驗證方法來驗證您的身分識別。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: curtand
ms.openlocfilehash: 0525ee6d0ac5df61113e5a97c9812394323ffb3d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062348"
---
# <a name="set-up-text-messaging-as-your-verification-method"></a>設定文字訊息做為您的驗證方法

您可以遵循下列步驟來新增雙因素驗證和密碼重設方法。 在您首次將此設定好之後，即可返回 [安全性資訊]**** 頁面來新增、更新或刪除安全性資訊。

如果系統提示您要在登入公司或學校帳戶之後立即設定此資訊，請參閱[從登入頁面的提示字元中設定安全性資訊](security-info-setup-signin.md)一文中的詳細步驟。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>如果您沒有看到電話選項，可能是您的組織不允許您使用此選項來進行驗證。 在此情況下，您必須選擇其他方法，或洽詢貴組織的技術支援人員以取得更多協助。

## <a name="set-up-text-messages-from-the-security-info-page"></a>從安全性資訊頁面設定簡訊

根據組織的設定，您或許可以使用簡訊來作為其中一個安全性資訊方法。 簡訊選項是電話選項的一部分，因此，將以您針對電話號碼所使用的相同方式來設定每個項目，但您將選擇使用簡訊，而不是讓 Microsoft 致電給您。

>[!Note]
>如果您想要接到來電，而非簡訊，請遵循[將安全性資訊設定為使用電話](security-info-setup-phone-number.md)一文中的步驟。

### <a name="to-set-up-text-messages"></a>設定簡訊

1. 登入您的公司或學校帳戶，然後前往 https://myprofile.microsoft.com/ 頁面。

    ![[我的設定檔] 頁面，顯示醒目提示的 [安全性資訊] 連結](media/security-info/securityinfo-myprofile-page.png)

2. 以左側瀏覽窗格或是 [安全性資訊]**** 區塊中的連結選取 [安全性資訊]****，然後在 [安全性資訊]**** 頁面選取 [新增方法]****。

    ![醒目提示 [新增方法] 選項的 [安全性資訊] 頁面](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在 [新增方法]**** 頁面上，從下拉式清單中選取 [電話]****，然後選取 [新增]****。

    ![已選取 [電話] 的 [新增方法] 方塊](media/security-info/securityinfo-myprofile-addphonetext.png)

4. 在 [電話]**** 頁面上，輸入行動裝置的電話號碼，選擇 [以簡訊傳送代碼給我]****，然後選取 [下一步]****。

    ![新增電話號碼，然後選擇簡訊](media/security-info/securityinfo-myprofile-phonetext-addnumber.png)

5. 將透過簡訊傳送給您的代碼輸入到行動裝置內，然後選取 [下一步]****。

    ![新增電話號碼，然後選擇簡訊](media/security-info/securityinfo-myprofile-phonetext-entercode.png)

    該頁面會變更以顯示您已成功。

    ![成功通知，連接電話號碼、接收文字訊息的選擇，以及您的帳戶](media/security-info/securityinfo-myprofile-phonetext-success.png)

    您的安全性資訊會更新，然後您就可以在使用雙步驟驗證或密碼重設時，使用簡訊驗證您的身分識別。 如果您想要讓簡訊變成預設方法，請參閱本文的[變更預設的安全性資訊方法](#change-your-default-security-info-method)一節。

## <a name="delete-text-messaging-from-your-security-info-methods"></a>從安全性資訊方法中刪除簡訊

如果您不想再使用簡訊作為安全性資訊方法，您可以從 [安全性資訊]**** 頁面中予以刪除。

>[!Important]
>若不小心刪除簡訊，就無法再復原。 您必須依照本文中[設定簡訊](#set-up-text-messages-from-the-security-info-page)一節的步驟，再次新增該方法。

### <a name="to-delete-text-messaging"></a>刪除簡訊

1. 在 [安全性資訊]**** 頁面上，選取 [電話]**** 選項旁的 [刪除]**** 連結。

    ![從安全性資訊刪除電話和簡訊方法的連結](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. 從確認方塊中選取 [是]****，以刪除 [電話]**** 號碼。 刪除電話號碼之後，該號碼就會從您的安全性資訊中移除，並且從 [安全性資訊]**** 頁面上消失。 如果 [電話]**** 是預設方法，則預設值將會變更為另一個可用方法。

## <a name="change-your-default-security-info-method"></a>變更預設的安全性資訊方法

如果您想要在使用雙因素驗證或密碼重設要求登入公司或學校帳戶時，使用文字訊息做為預設方法，您可以從 [**安全性資訊**] 頁面進行設定。

### <a name="to-change-your-default-security-info-method"></a>變更預設的安全性資訊方法

1. 在 [安全性資訊]**** 頁面上，選取 [預設登入方法]**** 資訊旁的 [變更]**** 連結。

    ![變更預設登入方法的連結](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. 從可用方法的下拉式清單中選取 [電話 - 傳簡訊到 (your_phone_number)]***__***，然後選取 [確認]****。

    ![選擇預設登入方法](media/security-info/securityinfo-myprofile-phonetext-changeddefault.png)

    用於登入的預設方法會變更為 [電話 - 傳簡訊到 (your_phone_number)]***__***。

## <a name="additional-security-info-methods"></a>其他安全性資訊方法

根據您要嘗試執行的動作，您會有組織如何連絡您來驗證您身分識別的其他選項。 選項包括：

- **驗證器應用程式。** 下載並使用驗證器應用程式，以取得核准通知或隨機產生的核准代碼來進行雙步驟驗證或密碼重設。 如需如何設定和使用 Microsoft Authenticator 應用程式的逐步指示，請參閱[將安全性資訊設定為使用驗證器應用程式](security-info-setup-auth-app.md)。

- **行動裝置或公司電話。** 輸入您的行動裝置號碼，然後等候來電以進行雙步驟驗證或密碼重設。 如需如何透過電話號碼驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用電話](security-info-setup-phone-number.md)。

- **安全性金鑰。** 註冊與 Microsoft 相容的安全性金鑰，並使用它搭配 PIN 進行雙步驟驗證或密碼重設。 如需如何使用安全性金鑰驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用安全性金鑰](security-info-setup-security-key.md)。

- **電子郵件地址。** 輸入您的公司或學校電子郵件地址以接收用於密碼重設的電子郵件。 此選項不適用於雙步驟驗證。 如需如何設定電子郵件的逐步指示，請參閱[將安全性資訊設定為使用電子郵件](security-info-setup-email.md)。

- **安全性問題。** 回答由您的系統管理員為組織所建立的一些安全性問題。 此選項僅適用於密碼重設，不適用雙步驟驗證。 如需如何設定安全性問題的逐步指示，請參閱[將安全性資訊設定為使用安全性問題](security-info-setup-questions.md)一文。

    >[!Note]
    >如果這其中一些選項遺失，很可能是因為您的組織不允許那些方法。 如果發生這種情況，您將必須選擇可用的方法，或連絡您的系統管理員以取得更多協助。

## <a name="next-steps"></a>後續步驟

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)或遵循[重設公司或學校密碼](active-directory-passwords-update-your-own-password.md)一文中的步驟來重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
