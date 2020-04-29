---
title: 註冊驗證資訊以重設您自己的密碼-Azure AD
description: 註冊 Azure AD 自助式密碼重設的驗證方法資訊，如此一來，您就可以在沒有系統管理員協助的情況下重設自己的密碼。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 397e1fd7695fd7e74e1f22959d0f9f24af7d1ea3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062637"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>註冊您的驗證方法資訊以重設您自己的密碼

如果您忘記公司或學校密碼、組織不曾提供密碼給您，或您的帳戶遭鎖定，您可以使用安全性資訊和行動裝置來重設公司或學校密碼。

您的系統管理員必須開啟這項功能，才能註冊您的資訊並重設您自己的密碼。 如果您沒有看到 [**忘記密碼**] 選項，表示您的系統管理員尚未為您的組織開啟此功能。 如果您認為情況有誤，請連絡技術支援中心以尋求協助。

>[!Important]
>本文適用于嘗試使用註冊自助式密碼重設的使用者。 這表示您將能夠重設自己的工作或學校密碼（例如alain@contoso.com），而不需要系統管理員的協助。 如果您是系統管理員，尋找如何為您的員工或其他使用者開啟自助式密碼重設的相關資訊，請參閱[部署 Azure AD 自助式密碼重設和其他文章](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)。

## <a name="set-up-your-password-reset-verification-method"></a>設定密碼重設驗證方法

1. 在您的裝置上開啟網頁瀏覽器，並移至 [[安全性資訊] 頁面](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)。

2. 根據系統管理員設定組織的方式而定，您可以使用下列其中一個或多個選項，將其設定為您的安全性驗證方法。 如果有多個選項可供使用，則強烈建議您使用多個做為安全性驗證方法，以免其中一個方法變成無法使用。

    - **驗證應用程式。** 選擇使用 Microsoft Authenticator 應用程式或其他驗證器應用程式做為您的安全性驗證方法。 如需設定應用程式的詳細資訊，請參閱[將 Microsoft Authenticator 應用程式設定為您的驗證方法](security-info-setup-auth-app.md)。

    - **文字訊息。** 選擇將文字訊息傳送至您的行動裝置。 如需設定文字訊息的詳細資訊，請參閱[設定文字訊息做為驗證方法](security-info-setup-text-msg.md)。

    - **通話。** 選擇撥打電話給您已註冊的電話號碼。 如需設定電話的詳細資訊，請參閱[將電話號碼設定為您的驗證方法](security-info-setup-phone-number.md)。

    - **安全性金鑰。** 選擇使用 Microsoft 相容的安全性金鑰。 如需詳細資訊，請參閱[將安全性金鑰設定為您的驗證方法](security-info-setup-security-key.md)。

    - **電子郵件地址。** 選擇使用可以使用的替代電子郵件地址，而不需要您忘記密碼或遺失密碼。 這僅適用于密碼重設，而不是安全性驗證方法。 如需設定電子郵件地址的詳細資訊，請參閱[設定電子郵件地址做為您的驗證方法](security-info-setup-email.md)。

    - **安全性問題。** 選擇設定並回答系統管理員所設定的預先定義安全性問題。 這僅適用于密碼重設，而不是安全性驗證方法。 如需安全性問題的詳細資訊，請參閱[將安全性問題設定為您的驗證方法](security-info-setup-questions.md)。

3. 在您選取並設定方法之後，請選擇 **[完成]** 以完成處理常式。

    > [!Note]
    > 為您的電話號碼或電子郵件地址新增的資訊不會與貴組織的全域目錄共用。 只有您和您的系統管理員可以看到此資訊。 只有您可以查看您安全性問題的答案。

## <a name="common-problems-and-their-solutions"></a>常見問題及其解決方案

 以下提供一些常見的錯誤案例及其解決方案：

| 錯誤訊息 |  可能的解決方法 |
| --- | --- | --- |
| 請連絡您的系統管理員。<br>我們偵測到您的使用者帳戶密碼未受到 Microsoft 管理。 因此，我們無法自動重設您的密碼。<br>請連絡 IT 人員以尋求進一步的協助。| 如果您在輸入使用者識別碼之後收到此錯誤訊息，這表示您的組織會在內部管理您的密碼，而不會想要從 [**無法存取您的帳戶**] 連結重設您的密碼。 若要在這種情況下重設密碼，您必須洽詢貴組織的技術支援人員或系統管理員尋求協助。 |
| 您的帳戶未啟用密碼重設功能。<br>很抱歉，IT 人員還沒將您的帳戶設定用於此服務。<br>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 如果您在輸入使用者識別碼之後收到此錯誤訊息，表示您的組織未開啟密碼重設功能，或您不允許使用它。 若要在這種情況下重設密碼，您必須選取 [**聯絡系統管理員**] 連結。 按一下連結後，系統會傳送一封電子郵件給貴組織的技術支援人員或系統管理員，讓他們知道您想要重設密碼。 |
| 我們無法驗證您的帳戶。<br>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 如果您在輸入使用者識別碼之後收到此錯誤訊息，表示您的組織已開啟密碼重設，而且您可以使用它，但尚未註冊服務。 在此情況下，您必須洽詢貴組織的技術支援人員或系統管理員以重設您的密碼。 如需在您回到裝置之後註冊密碼重設的詳細資訊，請參閱本文中的處理常式。 |

## <a name="next-steps"></a>後續步驟

- [使用自助式密碼重設來變更您的密碼](active-directory-passwords-update-your-own-password.md)

- [[安全性資訊] 頁面](https://mysignins.microsoft.com/security-info)

- [密碼重設入口網站](https://passwordreset.microsoftonline.com/)

- [當您無法登入 Microsoft 帳戶時](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
