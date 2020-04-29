---
title: 設定電子郵件地址做為您的驗證方法-Azure AD
description: 如何設定您的安全性資訊（預覽）頁面，以使用電子郵件地址做為您的驗證方法來驗證您的身分識別。
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
ms.openlocfilehash: 9488b96dda15b0ffb794f83826dc299a5f397b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063997"
---
# <a name="set-up-an-email-address-as-your-verification-method"></a>設定電子郵件地址做為您的驗證方法

您可以遵循下列步驟來新增密碼重設方法。 在您首次將此設定好之後，即可返回 [安全性資訊]**** 頁面來新增、更新或刪除安全性資訊。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Note]
>如果您看不到電子郵件選項，可能是您的組織不允許您將此選項用於您的密碼重設方法。 在此情況下，您必須選擇其他方法，或洽詢貴組織的技術支援人員以取得更多協助。

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

## <a name="set-up-your-email-address-from-the-security-info-page"></a>從安全性資訊頁面設定電子郵件地址

根據組織的設定，您或許可以使用電子郵件地址來作為其中一個安全性資訊方法。

>[!Note]
>建議使用不需要您的網路密碼即可存取的電子郵件地址。 如果您沒有看到電子郵件選項，很可能是您的組織不允許您使用電子郵件進行驗證。 如果發生這種情況，您將必須選擇其他方法，或連絡您的系統管理員以取得更多協助。

### <a name="to-set-up-your-email-address"></a>設定電子郵件地址

1. 登入您的公司或學校帳戶，然後前往 https://myprofile.microsoft.com/ 頁面。

    ![[我的設定檔] 頁面，顯示醒目提示的 [安全性資訊] 連結](media/security-info/securityinfo-myprofile-page.png)

2. 以左側瀏覽窗格或是 [安全性資訊]**** 區塊中的連結選取 [安全性資訊]****，然後在 [安全性資訊]**** 頁面選取 [新增方法]****。

    ![醒目提示 [新增方法] 選項的 [安全性資訊] 頁面](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在 [新增方法]**** 頁面上，從下拉式清單中選取 [電子郵件]****，然後選取 [新增]****。

    ![已選取 [電子郵件] 的 [新增方法] 方塊](media/security-info/securityinfo-myprofile-addemail.png)

4. 在 [電子郵件]**** 頁面上，輸入您的電子郵件地址 (例如 alain@gmail.com)，然後選取 [下一步]****。

    ![新增電話號碼，然後選擇電話](media/security-info/securityinfo-myprofile-emailaddress.png)

    >[!Important]
    >這個電子郵件地址不能是您的公司或學校電子郵件。

5. 輸入傳送至指定電子郵件地址的代碼，然後選取 [下一步]****。

    ![新增電話號碼，然後選擇簡訊](media/security-info/securityinfo-myprofile-emailcode.png)

    安全性資訊便會更新，並可讓您在使用密碼重設時，使用電子郵件地址來驗證您的身分識別。

## <a name="delete-your-email-address-from-your-security-info-methods"></a>從您的安全性資訊方法中刪除電子郵件地址

如果您不想再使用電子郵件地址作為安全性資訊方法，您可以從 [安全性資訊]**** 頁面中刪除應用程式。

>[!Important]
>若不小心刪除電子郵件地址，就無法再復原。 您必須依照本文中[設定電子郵件地址](#set-up-your-email-address-from-the-security-info-page)一節的步驟，再次新增該方法。

### <a name="to-delete-your-email-address"></a>刪除電子郵件地址

1. 在 [安全性資訊]**** 頁面上，選取 [電子郵件]**** 選項旁的 [刪除]**** 連結。

    ![從安全性資訊刪除電話方法的連結](media/security-info/securityinfo-myprofile-emaildelete.png)

2. 從確認方塊中選取 [是]****，以刪除 [電子郵件]**** 帳戶。 刪除電子郵件帳戶之後，該帳戶就會從您的安全性資訊中移除，並且從 [安全性資訊]**** 頁面上消失。

## <a name="additional-security-info-methods"></a>其他安全性資訊方法

根據您要嘗試執行的動作，您會有組織如何連絡您來驗證您身分識別的其他選項。 選項包括：

- **驗證器應用程式。** 下載並使用驗證器應用程式，以取得核准通知或隨機產生的核准代碼來進行雙步驟驗證或密碼重設。 如需如何設定和使用 Microsoft Authenticator 應用程式的逐步指示，請參閱[將安全性資訊設定為使用驗證器應用程式](security-info-setup-auth-app.md)。

- **行動裝置簡訊。** 輸入您的行動裝置號碼，然後取得將用來進行雙步驟驗證或密碼重設的代碼簡訊。 如需如何透過簡訊 (SMS) 驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用簡訊 (SMS)](security-info-setup-text-msg.md)。

- **行動裝置或公司電話。** 輸入您的行動裝置號碼，然後等候來電以進行雙步驟驗證或密碼重設。 如需如何透過電話號碼驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用電話](security-info-setup-phone-number.md)。

- **安全性金鑰。** 註冊與 Microsoft 相容的安全性金鑰，並使用它搭配 PIN 進行雙步驟驗證或密碼重設。 如需如何使用安全性金鑰驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用安全性金鑰](security-info-setup-security-key.md)。

- **安全性問題。** 回答由您的系統管理員為組織所建立的一些安全性問題。 此選項僅適用於密碼重設，不適用雙步驟驗證。 如需如何設定安全性問題的逐步指示，請參閱[將安全性資訊設定為使用安全性問題](security-info-setup-questions.md)一文。

    >[!Note]
    >如果這其中一些選項遺失，很可能是因為您的組織不允許那些方法。 如果發生這種情況，您將必須選擇可用的方法，或連絡您的系統管理員以取得更多協助。

## <a name="next-steps"></a>後續步驟

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)或遵循[重設公司或學校密碼](active-directory-passwords-update-your-own-password.md)一文中的步驟來重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
