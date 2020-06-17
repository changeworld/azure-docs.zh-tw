---
title: 註冊驗證資訊以重設您自己的密碼 - Azure AD
description: 針對 Azure AD 自助式密碼重設來註冊驗證方法資訊，讓您不需要管理員協助就能重設自己的密碼。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 3deb59b04f3c0ba90e270bc064e3ece3c21229b3
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739599"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>註冊驗證方法資訊以重設您自己的密碼

如果您忘記公司或學校密碼、組織不曾提供密碼給您，或您的帳戶遭鎖定，您可以使用安全性資訊和行動裝置來重設公司或學校密碼。

管理員必須開啟此功能，您才可以註冊資訊和重設自己的密碼。 如果您沒有看到 [忘記密碼] 選項，就表示管理員尚未替組織開啟此功能。 如果您認為情況有誤，請連絡技術支援中心以尋求協助。

>[!Important]
>本文以嘗試註冊自助式密碼重設的使用者為對象。 這表示您不需要管理員協助就能重設自己的公司或學校密碼 (例如 alain@contoso.com)。 如果您是管理員，而且想知道如何為員工或其他使用者開啟自助式密碼重設，請參閱[部署 Azure AD 自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)及其他文章。

## <a name="set-up-your-password-reset-verification-method"></a>設定密碼重設驗證方法

1. 在您的裝置上開啟網頁瀏覽器，並移至[安全性資訊頁面](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup)。

2. 根據管理員如何設定組織而定，您可以將下列其中一個或多個選項設定為安全性驗證方法。 如果有多個選項可用，強烈建議您使用多個選項作為安全性驗證方法，以防萬一其中一個方法變成無法使用。

    - **驗證應用程式**。 選擇使用 Microsoft Authenticator 應用程式或其他驗證器應用程式作為安全性驗證方法。 如需設定應用程式的詳細資訊，請參閱 [將 Microsoft Authenticator 應用程式設定為驗證方法](security-info-setup-auth-app.md)。

    - **簡訊**。 選擇將簡訊傳送至您自己的行動裝置。 如需設定簡訊的詳細資訊，請參閱[將簡訊設定為驗證方法](security-info-setup-text-msg.md)。

    - **通話**。 選擇打電話到您註冊的電話號碼。 如需設定通話的詳細資訊，請參閱[將電話號碼設定為驗證方法](security-info-setup-phone-number.md)。

    - **安全性金鑰**。 選擇使用 Microsoft 相容的安全性金鑰。 如需詳細資訊，請參閱[將安全性金鑰設定為驗證方法](security-info-setup-security-key.md)。

    - **電子郵件地址。** 選擇不需要有忘記密碼或遺失密碼就可使用的替代電子郵件地址。 這僅適用於密碼重設，不作為安全性驗證方法。 如需設定電子郵件地址的詳細資訊，請參閱[將電子郵件地址設定為驗證方法](security-info-setup-email.md)。

    - **安全性問題。** 選擇設定並回答管理員所設定的預定安全性問題。 這僅適用於密碼重設，不作為安全性驗證方法。 如需安全性問題的詳細資訊，請參閱[將安全性問題設定為驗證方法](security-info-setup-questions.md)。

3. 選取並設定方法之後，請選擇 [完成] 以完成此流程。

    > [!Note]
    > 在電話號碼或電子郵件地址中新增的資訊不會與組織的全域目錄共用。 只有您和管理員可以看到此資訊。 只有您可以查看您安全性問題的答案。

## <a name="common-problems-and-their-solutions"></a>常見問題及其解決方案

 以下提供一些常見的錯誤案例及其解決方案：

| 錯誤訊息 |  可能的解決方案 |
| --- | --- | --- |
| 請連絡您的系統管理員。<br>我們偵測到您的使用者帳戶密碼未受到 Microsoft 管理。 因此，我們無法自動重設您的密碼。<br>請連絡 IT 人員以尋求進一步的協助。| 如果您在輸入使用者識別碼之後收到此錯誤訊息，則表示組織已在內部管理您的密碼，不允許您從 [無法存取您的帳戶] 連結來重設密碼。 在這種情況下，若要重設密碼，您必須連絡組織的技術支援中心或管理員來尋求協助。 |
| 您的帳戶未啟用密碼重設功能。<br>很抱歉，IT 人員還沒將您的帳戶設定用於此服務。<br>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 如果您在輸入使用者識別碼之後收到此錯誤訊息，則表示組織尚未開啟密碼重設功能，或不允許您使用此功能。 若要在這種情況下重設密碼，您必須選取 [連絡管理員] 連結。 按一下此連結後會傳送電子郵件給組織的技術支援中心或管理員，讓他們知道您想要重設密碼。 |
| 我們無法驗證您的帳戶。<br>如果您願意，我們可以連絡貴組織的系統管理員來為您重設密碼。 | 如果您在輸入使用者識別碼之後收到此錯誤訊息，則表示組織已開啟密碼重設，而且可供您使用，但您尚未註冊此服務。 在這種情況下，您必須連絡組織的技術支援中心或管理員來重設密碼。 如需您回到裝置之後如何註冊密碼重設的詳細資訊，請參閱本文中的流程。 |

## <a name="next-steps"></a>後續步驟

- [使用自助式密碼重設來變更您的密碼](active-directory-passwords-update-your-own-password.md)

- [安全性資訊頁面](https://mysignins.microsoft.com/security-info)

- [密碼重設入口網站](https://passwordreset.microsoftonline.com/)

- [當您無法登入 Microsoft 帳戶時](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)
