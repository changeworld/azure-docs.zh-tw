---
title: 註冊身份驗證資訊以重置自己的密碼 - Azure AD
description: 註冊 Azure AD 自助服務密碼重設的驗證方法資訊，以便無需管理員説明即可重置自己的密碼。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062637"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>註冊驗證方法資訊以重置自己的密碼

如果您忘記公司或學校密碼、組織不曾提供密碼給您，或您的帳戶遭鎖定，您可以使用安全性資訊和行動裝置來重設公司或學校密碼。

管理員必須打開此功能，才能註冊資訊並重置自己的密碼。 如果您沒有看到"**忘記我的密碼**"選項，則意味著管理員尚未打開組織的功能。 如果您認為情況有誤，請連絡技術支援中心以尋求協助。

>[!Important]
>本文適用于嘗試使用註冊進行自助服務密碼重設的使用者。 這意味著您將能夠重置自己的工作或學校密碼（例如），alain@contoso.com而無需管理員的説明。 如果您是管理員，正在查找有關如何為員工或其他使用者打開自助服務密碼重設的資訊，請參閱[部署 Azure AD 自助服務密碼重設和其他文章](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)。

## <a name="set-up-your-password-reset-verification-method"></a>設置密碼重設驗證方法

1. 打開設備上的 Web 瀏覽器，然後轉到[安全資訊頁面](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)。

2. 根據管理員如何設置組織，以下一個或多個選項將可供您設置為安全驗證方法。 如果有多個選項可用，我們強烈建議您使用多個選項作為安全驗證方法，以防其中一種方法不可用。

    - **身份驗證應用。** 選擇使用 Microsoft 身份驗證器應用或其他身份驗證器應用作為安全驗證方法。 有關設置應用的詳細資訊，請參閱將 Microsoft[身份驗證器應用設置為驗證方法](security-info-setup-auth-app.md)。

    - **短信。** 選擇將自己傳送簡訊到行動裝置。 有關設置文本消息的詳細資訊，請參閱[將文本消息設置為驗證方法](security-info-setup-text-msg.md)。

    - **電話。** 選擇撥打您註冊的電話號碼。 有關設置撥打電話的詳細資訊，請參閱[設置電話號碼作為驗證方法](security-info-setup-phone-number.md)。

    - **安全金鑰。** 選擇使用與 Microsoft 相容的安全金鑰。 有關詳細資訊，請參閱[將安全金鑰設置為驗證方法](security-info-setup-security-key.md)。

    - **電子郵件地址。** 選擇使用備用電子郵件地址，無需忘記或缺少密碼即可使用。 這僅適用于密碼重設，而不是作為安全驗證方法。 有關設置電子郵件地址的詳細資訊，請參閱[設置電子郵件地址作為驗證方法](security-info-setup-email.md)。

    - **安全問題。** 選擇設置和回答管理員設置的預定義的安全問題。 這僅適用于密碼重設，而不是作為安全驗證方法。 有關安全問題的詳細資訊，請參閱[將安全問題設置為驗證方法](security-info-setup-questions.md)。

3. 選擇和設置方法後，選擇 **"完成"** 以完成該過程。

    > [!Note]
    > 為電話號碼或電子郵件地址添加的資訊不會與組織的全域目錄共用。 唯一能看到此資訊的人是您和您的管理員。 只有您可以查看您安全性問題的答案。

## <a name="common-problems-and-their-solutions"></a>常見問題及其解決方案

 以下提供一些常見的錯誤案例及其解決方案：

| 錯誤訊息 |  可能的解決方法 |
| --- | --- | --- |
| 請連絡您的系統管理員。<br>我們偵測到您的使用者帳戶密碼未受到 Microsoft 管理。 因此，我們無法自動重設您的密碼。<br>請連絡 IT 人員以尋求進一步的協助。| 如果您在鍵入使用者 ID 後收到此錯誤訊息，則意味著您的組織在內部管理您的密碼，並且不希望您從 **"無法訪問您的帳戶**"連結重置密碼。 要在此情況下重置密碼，您必須聯繫組織的説明台或管理員尋求説明。 |
| 您的帳戶未啟用密碼重設功能。<br>很抱歉，IT 人員還沒將您的帳戶設定用於此服務。<br>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 如果在鍵入使用者 ID 後收到此錯誤訊息，則表示您的組織未打開密碼重設功能，或者不允許使用該功能。 要在此情況下重置密碼，必須選擇 **"聯繫管理員**"連結。 按一下該連結後，將向組織的説明台或管理員發送電子郵件，讓他們知道要重置密碼。 |
| 我們無法驗證您的帳戶。<br>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 如果在鍵入使用者 ID 後收到此錯誤訊息，則表示您的組織已打開密碼重設，您可以使用它，但尚未註冊該服務。 在此情況下，您必須與組織的説明台或管理員聯繫以重置密碼。 有關在設備上重新註冊密碼重設的資訊，請參閱本文中的上述過程。 |

## <a name="next-steps"></a>後續步驟

- [使用自助式密碼重設來變更您的密碼](active-directory-passwords-update-your-own-password.md)

- [安全資訊頁面](https://mysignins.microsoft.com/security-info)

- [密碼重設門戶](https://passwordreset.microsoftonline.com/)

- [當您無法登入 Microsoft 帳戶時](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
