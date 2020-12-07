---
title: 設定本機帳戶身分識別提供者
titleSuffix: Azure AD B2C
description: 當您在 Azure Active Directory B2C 租使用者中設定使用者流程時，請定義可用於本機帳戶驗證 (電子郵件、使用者名稱、電話號碼) 的身分識別類型。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ce9b60ead9aff792bf2e4e98841469d58620ccd
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754585"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>為使用者流程設定電話註冊和登入 (預覽) 

> [!NOTE]
> 使用者流程的電話註冊與登入和復原電子郵件功能處於公開預覽狀態。

除了電子郵件和使用者名稱之外，您還可以將電話號碼新增至本機帳戶身分識別提供者，將電話號碼當作全租使用者的註冊選項來啟用。 啟用電話註冊並登入本機帳戶之後，您可以將電話註冊新增至您的使用者流程。

在使用者流程中設定電話註冊和登入牽涉到下列步驟：

- 在您的本機帳戶身分識別提供者中[設定電話註冊和登入全租](#configure-phone-sign-up-and-sign-in-tenant-wide)使用者，以接受以使用者身分識別的電話號碼。 

- [將電話註冊新增至您的使用者流程](#add-phone-sign-up-to-a-user-flow) ，讓使用者能夠使用他們的電話號碼來註冊您的應用程式。

- [啟用 [復原電子郵件提示 (預覽]) ](#enable-the-recovery-email-prompt-preview) 可讓使用者指定當他們沒有電話時，可用來復原其帳戶的電子郵件。

當您使用電話註冊來設定使用者流程時，預設會停用 (MFA) 的多重要素驗證。 您可以使用電話註冊在使用者流程中啟用 MFA，但因為使用電話號碼作為主要識別碼，所以電子郵件單次密碼是第二個驗證因素唯一可用的選項。

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>設定電話註冊和登入全租使用者

預設會在您的本機帳戶身分識別提供者設定中啟用電子郵件註冊。 您可以選取或取消選取 [電子郵件註冊]、[使用者名稱] 或 [電話號碼]，以變更您在租使用者中將支援的身分識別類型。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。

3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。

4. 在 [ **管理**] 底下，選取 [ **識別提供者**]。

5. 在 [識別提供者] 清單中，選取 [ **本機帳戶**]。

   ![身分識別提供者選取本機帳戶](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. 在 [ **設定本機 IDP** ] 頁面中，確定已選取 [ **電話** ]，作為取用者可在 Azure AD B2C 租使用者中用來建立其本機帳戶的其中一個允許的身分識別類型。

   ![選取允許的身分識別類型](media/phone-authentication-user-flows/configure-local-idp.png)

1. 選取 [儲存]。

## <a name="add-phone-sign-up-to-a-user-flow"></a>將電話註冊新增至使用者流程

將電話註冊作為本機帳戶的身分識別選項之後，您可以將其新增至使用者流程，只要它們是最新的 **建議** 使用者流程版本即可。 以下範例顯示如何將電話註冊新增至新的使用者流程。 但是，您也可以將電話註冊新增至現有的建議版本使用者流程 (選取 **消費者流程**  >  *使用者流程名稱*  >  **識別提供者**  >  **本機帳戶電話註冊**) 。 

以下範例顯示如何將電話註冊新增至新的使用者流程。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。

    ![B2C 租用戶、[目錄和訂用帳戶] 窗格、Azure 入口網站](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
4. 在 [原則] 底下，選取 [使用者流程]，然後選取 [新使用者流程]。

    ![入口網站中的 [使用者流程] 頁面，已醒目提示 [新增使用者流程] 按鈕](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. 在 [建立使用者流程] 頁面上，選取 [註冊和登入] 使用者流程。

    ![選取已反白顯示註冊和登入流程的使用者流程頁面](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. 在 [選取版本] 底下，選取 [建議]，然後選取 [建立]。 ([深入了解](user-flow-versions.md)使用者流程版本。)

    ![建立使用者流程按鈕](./media/phone-authentication-user-flows/select-version.png)

7. 輸入使用者流程的 [名稱]。 例如，*signupsignin1*。
8. 在 [ **識別提供者** ] 區段的 [ **本機帳戶**] 底下，選取 [ **電話註冊**]。

   ![已選取使用者流程電話註冊選項](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. 在 [ **社交識別提供者**] 下，選取您要允許此使用者流程使用的任何其他身分識別提供者。

   > [!NOTE]
   > 預設會停用 MFA) 的多重要素驗證 (。 您可以為手機註冊使用者流程啟用 MFA，但因為使用電話號碼作為主要識別碼，所以電子郵件單次密碼是第二個驗證因素唯一可用的選項。

1. 在 [ **使用者屬性和權杖宣告** ] 區段中，選擇您想要在註冊期間從使用者收集和傳送的宣告和屬性。 例如，選取 [顯示更多]，然後選擇 [國家/地區]、[顯示名稱]，及 [郵遞區號] 的屬性和宣告。 選取 [確定]。

1. 選取 [ **建立** ] 以新增使用者流程。 名稱前面會自動加上前置詞 *B2C_1*。

## <a name="enable-the-recovery-email-prompt-preview"></a>啟用復原電子郵件提示 (預覽) 

當您啟用使用者流程的電話註冊和登入功能時，也建議您啟用復原電子郵件功能。 使用這項功能時，使用者可以提供電子郵件地址，以在他們沒有電話時用來復原其帳戶。 此電子郵件地址僅用於帳戶復原。 它無法用來登入。

- 當復原電子郵件提示開啟時，系統會要求使用者 **在** 第一次註冊時驗證備份電子郵件。 在下次登入時，系統會要求尚未提供復原電子郵件的使用者驗證備份電子郵件。

- 當復原電子郵件 **關閉** 時，註冊或登入的使用者不會看到復原電子郵件提示。
 
您可以在使用者流程屬性中啟用復原電子郵件提示。

> [!NOTE]
> 開始之前，請確定您已將 [電話註冊新增至您的使用者流程](#add-phone-sign-up-to-a-user-flow) （如上所述）。

### <a name="to-enable-the-recovery-email-prompt"></a>啟用修復電子郵件提示

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
3. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
4. 在 Azure AD B2C 的 [ **原則**] 底下，選取 [ **使用者流程**]。
5. 從清單中選取使用者流程。
6. 在 [設定]  下方，選取 [屬性]  。
7. 在 [ **啟用手機號碼註冊的復原電子郵件提示字元] 和 [登入 (預覽])** 中，選取：

   - **在上** ，以顯示在註冊和登入期間的修復電子郵件提示。
   - **關閉** 以隱藏復原電子郵件提示。

    ![啟用復原電子郵件的使用者流程屬性](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. 選取 [儲存]。

### <a name="to-test-the-recovery-email-prompt"></a>測試復原電子郵件提示

在您的使用者流程中啟用電話註冊和登入和修復電子郵件提示之後，您可以使用 [ **執行使用者流程** ] 來測試使用者體驗。

1. 選取 [**原則**  >  **使用者流程**]，然後選取您所建立的使用者流程。 在 [使用者流程] 總覽頁面上，選取 [ **執行使用者流程**]。

2. 針對 [ **應用程式**]，選取您在步驟1中註冊的 web 應用程式。 **Reply URL** 應顯示 `https://jwt.ms`。

3. 選取 [ **執行使用者流程** ] 並確認下列行為：

   - 第一次註冊的使用者會被要求提供修復電子郵件。 
   - 已註冊但尚未提供復原電子郵件的使用者，會要求您在登入時提供一封。

4. 輸入電子郵件地址，然後選取 [ **傳送驗證碼**]。 確認程式碼已傳送至您提供的電子郵件收件匣。 取出程式碼，並在 [ **驗證碼** ] 方塊中輸入。 然後選取 [ **驗證程式代碼**]。

## <a name="next-steps"></a>後續步驟

- [新增外部識別提供者](tutorial-add-identity-providers.md)
- [建立使用者流程](tutorial-create-user-flows.md)