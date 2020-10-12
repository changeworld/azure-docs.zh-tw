---
title: 將電話號碼設定為驗證方法 - Azure AD
description: 如何設定 [安全性資訊] (預覽) 頁面，以使用電話號碼和行動裝置作為驗證方法來驗證身分識別。
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
ms.openlocfilehash: 7500b6b146a627dcdebacd9d2e2c7c61bc43c105
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83744456"
---
# <a name="set-up-a-phone-number-as-your-verification-method"></a>將電話設定為驗證方法

您可以遵循下列步驟來新增雙因素驗證和密碼重設方法。 在您首次將此設定好之後，即可返回 [安全性資訊] 頁面來新增、更新或刪除安全性資訊。

如果系統提示您要在登入公司或學校帳戶之後立即設定此資訊，請參閱[從登入頁面的提示字元中設定安全性資訊](security-info-setup-signin.md)一文中的詳細步驟。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

> [!Note]
> 安全性資訊不支援使用電話分機。 即使您新增適當格式 +1 4255551234X12345，在撥號之前仍會移除該分機號碼。
>
> 如果您未看到電話選項，可能是組織不允許您使用這個選項進行驗證。 在這種情況下，您必須選擇其他方法，或連絡組織的技術支援中心以取得更多協助。

## <a name="security-verification-versus-password-reset-authentication"></a>安全性驗證與密碼重設驗證

雙因素安全性驗證和密碼重設都會使用安全性資訊方法。 不過，並非所有方法都同時適用於這兩者。

| 方法 | 用於 |
| ------ | -------- |
| 驗證器應用程式 | 雙因素驗證與密碼重設驗證。 |
| 簡訊 | 雙因素驗證與密碼重設驗證。 |
| 電話 | 雙因素驗證與密碼重設驗證。 |
| 安全性金鑰 | 雙因素驗證與密碼重設驗證。 |
| 電子郵件帳戶 | 僅適用於密碼重設驗證。 您必須選擇另一種方法來進行雙因素驗證。 |
| 安全性問題 | 僅適用於密碼重設驗證。 您必須選擇另一種方法來進行雙因素驗證。 |

## <a name="set-up-phone-calls-from-the-security-info-page"></a>從安全性資訊頁面設定電話

根據您組織的設定，您可能可以使用電話作為其中一個安全性資訊方法。

>[!Note]
>如果您想要接收簡訊而非電話，請遵循[將安全性資訊設定為使用簡訊](security-info-setup-text-msg.md)一文中的步驟。

### <a name="to-set-up-phone-calls"></a>設定電話

1. 登入您的公司或學校帳戶，然後前往 https://myaccount.microsoft.com/ 頁面。

    ![[我的設定檔] 頁面，顯示醒目提示的 [安全性資訊] 連結](media/security-info/securityinfo-myprofile-page.png)

2. 以左側瀏覽窗格或是 [安全性資訊] 區塊中的連結選取 [安全性資訊]，然後在 [安全性資訊] 頁面選取 [新增方法]。

    ![醒目提示 [新增方法] 選項的 [安全性資訊] 頁面](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. 在 [新增方法] 頁面上，從下拉式清單中選取 [電話]，然後選取 [新增]。

    ![已選取 [電話] 的 [新增方法] 方塊](media/security-info/securityinfo-myprofile-addphonetext.png)

4. 在 [電話] 頁面上，輸入行動裝置的電話號碼，選擇 [打電話給我]，然後選取 [下一步]。

    ![新增電話號碼，然後選擇電話](media/security-info/securityinfo-myprofile-phonecall-addnumber.png)

5. 接聽驗證電話 (傳送至您所輸入的電話號碼)，並遵循指示來進行。

    該頁面會變更以顯示您已成功。

    ![成功通知、接通電話號碼、接收電話的選項，以及您的帳戶](media/security-info/securityinfo-myprofile-phonetext-success.png)

    您的安全性資訊會更新，而且可以在使用雙步驟驗證或密碼重設時，透過電話驗證您的身分識別。 如果您想要讓電話變成預設方法，請參閱本文的[變更預設的安全性資訊方法](#change-your-default-security-info-method)一節。

## <a name="delete-phone-calls-from-your-security-info-methods"></a>從安全性資訊方法中刪除電話

如果您不想再使用電話作為安全性資訊方法，您可以從 [安全性資訊] 頁面中予以刪除。

>[!Important]
>若不小心刪除電話，就無法再復原。 您必須依照本文中[設定電話](#set-up-phone-calls-from-the-security-info-page)一節的步驟，再次新增該方法。

### <a name="to-delete-phone-calls"></a>刪除電話

1. 在 [安全性資訊] 頁面上，選取 [電話] 選項旁的 [刪除] 連結。

    ![從安全性資訊刪除電話方法的連結](media/security-info/securityinfo-myprofile-phonetext-delete.png)

2. 從確認方塊中選取 [是]，以刪除 [電話] 號碼。 刪除電話號碼之後，該號碼就會從您的安全性資訊中移除，並且從 [安全性資訊] 頁面上消失。 如果 [電話] 是預設方法，則預設值將會變更為另一個可用方法。

## <a name="change-your-default-security-info-method"></a>變更預設的安全性資訊方法

如果您想要在使用雙因素驗證登入公司或學校帳戶時，或提出密碼重設要求時，使用電話作為預設方法，您可以從 [安全性資訊] 頁面進行設定。

### <a name="to-change-your-default-security-info-method"></a>變更預設的安全性資訊方法

1. 在 [安全性資訊] 頁面上，選取 [預設登入方法] 資訊旁的 [變更] 連結。

    ![變更預設登入方法的連結](media/security-info/securityinfo-myprofile-phonetext-defaultchange.png)

2. 從可用方法的下拉式清單中選取 [電話 - 撥打 (your_phone_number)]，然後選取 [確認]。

    ![選擇預設登入方法](media/security-info/securityinfo-myprofile-phonecall-changeddefault.png)

    用於登入的預設方法會變更為 [電話 - 撥打 (your_phone_number)]。

## <a name="additional-security-info-methods"></a>其他安全性資訊方法

根據您要嘗試執行的動作，您會有組織如何連絡您來驗證您身分識別的其他選項。 選項包括：

- **驗證器應用程式。** 下載並使用驗證器應用程式，以取得核准通知或隨機產生的核准代碼來進行雙步驟驗證或密碼重設。 如需如何設定和使用 Microsoft Authenticator 應用程式的逐步指示，請參閱[將安全性資訊設定為使用驗證器應用程式](security-info-setup-auth-app.md)。

- **行動裝置簡訊。** 輸入您的行動裝置號碼，然後取得將用來進行雙步驟驗證或密碼重設的代碼簡訊。 如需如何透過簡訊 (SMS) 驗證您身分識別的逐步指示，請參閱[將安全性資訊設定為使用簡訊 (SMS)](security-info-setup-text-msg.md)。

- **安全性金鑰**。 註冊 Microsoft 相容安全性金鑰，並搭配 PIN 一起使用，以進行雙步驟驗證或密碼重設。 如需如何透過安全性金鑰驗證身分識別的逐步指示，請參閱[設定安全性資訊來使用安全性金鑰](security-info-setup-security-key.md)。

- **電子郵件地址。** 輸入您的公司或學校電子郵件地址以接收用於密碼重設的電子郵件。 此選項不適用於雙步驟驗證。 如需如何設定電子郵件的逐步指示，請參閱[將安全性資訊設定為使用電子郵件](security-info-setup-email.md)。

- **安全性問題。** 回答由您的系統管理員為組織所建立的一些安全性問題。 此選項僅適用於密碼重設，不適用雙步驟驗證。 如需如何設定安全性問題的逐步指示，請參閱[將安全性資訊設定為使用安全性問題](security-info-setup-questions.md)一文。

    >[!Note]
    >如果這其中一些選項遺失，很可能是因為您的組織不允許那些方法。 如果發生這種情況，您將必須選擇可用的方法，或連絡您的系統管理員以取得更多協助。

## <a name="next-steps"></a>後續步驟

- 如果您遺失或忘記密碼，請從[密碼重設入口網站](https://passwordreset.microsoftonline.com/)或遵循[重設公司或學校密碼](active-directory-passwords-update-your-own-password.md)一文中的步驟來重設密碼。

- 在[無法登入 Microsoft 帳戶](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中取得登入問題的疑難排解秘訣和說明。
