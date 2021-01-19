---
title: 設定 Azure AD B2C 本機帳戶身分識別提供者
titleSuffix: Azure AD B2C
description: 定義可用來在 Azure Active Directory B2C 租使用者中，用來註冊或登入 (電子郵件、使用者名稱、電話號碼) 的身分識別類型。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e6b520a32755379fce5326e25b90f135b0d67755
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98574306"
---
# <a name="set-up-the-local-account-identity-provider"></a>設定本機帳戶身分識別提供者

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C 提供各種使用者可在其中驗證使用者的方式。 使用者可以登入本機帳戶，方法是使用使用者名稱和密碼、電話驗證 (也稱為密碼較少驗證) 或社交識別提供者。 預設會在您的本機帳戶身分識別提供者設定中啟用電子郵件註冊。 

本文說明使用者如何建立此 Azure AD B2C 租使用者的本機帳戶。 針對社交或企業身分識別，其中使用者的身分識別是由 Facebook 和 Google 等同盟身分識別提供者所管理，請參閱 [新增身分識別提供者](add-identity-provider.md)。

## <a name="email-sign-in"></a>電子郵件登入

使用 [電子郵件] 選項時，使用者可以使用其電子郵件地址和密碼登入/登出：

- 登 **入**，系統會提示使用者提供其電子郵件和密碼。
- **註冊** 時，系統會提示使用者輸入電子郵件地址，這會在註冊 (選擇性) 時驗證，並成為其登入識別碼。 使用者接著會在註冊頁面上輸入任何其他要求的資訊，例如顯示名稱、名字和姓氏。 然後選取 [繼續] 以建立帳戶。
- **密碼重設**，使用者必須輸入並驗證其電子郵件，之後使用者才能重設密碼

![電子郵件註冊或登入體驗](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>使用者名稱登入

使用者可利用使用者選項，以使用者名稱和密碼登入/登入：

- 登 **入**：系統會提示使用者提供其使用者名稱和密碼。
- **註冊**：系統將會提示使用者輸入使用者名稱，這會成為其登入識別碼。 系統也會提示使用者輸入電子郵件地址，這會在註冊時進行驗證。 將在密碼重設流程期間使用電子郵件地址。 使用者會在註冊頁面上輸入任何其他要求的資訊，例如顯示名稱、名字和姓氏。 然後，使用者會選取 [繼續] 以建立帳戶。
- **密碼重設**：使用者必須輸入其使用者名稱和相關聯的電子郵件地址。 電子郵件地址必須經過驗證之後，使用者才能重設密碼。

![使用者名稱註冊或登入體驗](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>手機登入 (預覽) 

無密碼 authentication 是一種驗證類型，使用者不需要使用其密碼登入。 使用電話註冊和登入，使用者就可以使用電話號碼作為主要登入識別碼來註冊應用程式。 在註冊和登入期間，使用者將會有下列體驗：

- 登 **入**：如果使用者有一個具有電話號碼的現有帳戶做為其識別碼，使用者會輸入他們的電話號碼，然後選取 [登 *入*]。 他們會選取 [ *繼續*] 來確認國家/地區和電話號碼，並將一次性驗證碼傳送到其電話。 使用者輸入驗證碼，然後選取 [ *繼續* 登入]。
- **註冊**：如果使用者還沒有您應用程式的帳戶，可以按一下 [ *立即註冊* ] 連結建立一個帳戶。 
    1. 註冊頁面隨即出現，使用者會在其中選取其 *國家/地區*、輸入其電話號碼，然後選取 [ *傳送程式碼*]。 
    1. 一次性驗證碼會傳送至使用者的電話號碼。 使用者在註冊頁面上輸入 *驗證碼* ，然後選取 [ *驗證程式代碼*]。  (如果使用者無法取得程式碼，則可以選取 [ *傳送新程式碼*) 。 
    1. 使用者會在註冊頁面上輸入任何其他要求的資訊，例如顯示名稱、名字和姓氏。 然後選取 [繼續]  。
    1. 接下來，系統會要求使用者提供 **修復電子郵件**。 使用者輸入其電子郵件地址，然後選取 [ *傳送驗證碼*]。 系統會將程式碼傳送至使用者的電子郵件收件匣，並在 [驗證碼] 方塊中輸入。 然後，使用者選取 [驗證程式代碼]。
    1. 驗證程式代碼之後，使用者會選取 [ *建立* ] 以建立他們的帳戶。 

![電話註冊或登入體驗](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>定價

單次密碼會使用 SMS 文字訊息傳送給您的使用者。 根據您的行動網路操作員，您可能會針對每個傳送的訊息付費。 如需定價資訊，請參閱 [Azure Active Directory B2C 定價](https://azure.microsoft.com/pricing/details/active-directory-b2c/)的 **個別費用** 區段。

> [!NOTE]
> 當您使用電話註冊來設定使用者流程時，預設會停用 (MFA) 的多重要素驗證。 您可以使用電話註冊在使用者流程中啟用 MFA，但因為使用電話號碼作為主要識別碼，所以電子郵件單次密碼是第二個驗證因素唯一可用的選項。

### <a name="phone-recovery"></a>電話修復

當您啟用使用者流程的電話註冊和登入功能時，也建議您啟用復原電子郵件功能。 使用這項功能時，使用者可以提供電子郵件地址，以在他們沒有電話時用來復原其帳戶。 此電子郵件地址僅用於帳戶復原。 它無法用來登入。

- 當復原電子郵件提示 **開啟** 時，使用者第一次註冊時，系統會提示使用者確認備份電子郵件。 在下次登入時，系統會要求尚未提供復原電子郵件的使用者驗證備份電子郵件。

- 當復原電子郵件 **關閉** 時，註冊或登入的使用者不會看到復原電子郵件提示。
 
下列螢幕擷取畫面示範電話修復流程：

![電話修復使用者流程](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>手機或電子郵件登入 (預覽) 

您可以選擇結合手機登 [入](#phone-sign-in-preview)，以及 [電子郵件登入](#email-sign-in)。 在 [註冊] 或 [登入] 頁面中，使用者可以輸入電話號碼或電子郵件地址。 根據使用者輸入，Azure AD B2C 會將使用者導向至對應的流程。 

![電話或電子郵件註冊或登入體驗](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>設定本機帳戶識別提供者設定

您可以藉由啟用或停用提供者 (電子郵件、使用者名稱或電話號碼) ，設定可在使用者流程中使用的本機身分識別提供者。  您可以在租使用者層級啟用一個以上的本機身分識別提供者。

使用者流程只能設定為使用其中一個本機帳戶身分識別提供者。 如果已在租使用者層級上啟用多個使用者流程，則每個使用者流程都可以設定不同的本機帳戶身分識別提供者。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取頂端功能表中的 [ **目錄 + 訂** 用帳戶] 篩選，然後選擇包含您 Azure AD 租使用者的目錄，以確定您使用的是包含 Azure AD B2C 租使用者的目錄。
1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 在 [管理] 底下，選取 [識別提供者]。
1. 在識別提供者清單中，選取 [本機帳戶]。
1. 在 [ **設定本機 IDP** ] 頁面中，選取至少一個可允許的身分識別類型，供取用者在 Azure AD B2C 租使用者中建立其本機帳戶。
1. 選取 [儲存]  。

## <a name="configure-your-user-flow"></a>設定您的使用者流程

1. 在 Azure 入口網站的左側功能表中，選取 [ **Azure AD B2C**]。
1. 在 [原則] 底下，選取 [使用者流程 (原則)]。
1. 選取您要設定註冊和登入體驗的使用者流程。
1. 選取 **識別提供者**
1. 在 **本機帳戶** 下方，選取下列其中一項： **電子郵件註冊**、  **使用者識別碼註冊**、 **電話註冊**、 **電話/電子郵件註冊** 或 **無**。

### <a name="enable-the-recovery-email-prompt"></a>啟用修復電子郵件提示

如果您選擇 **電話註冊**、 **電話/電子郵件註冊** 選項，請啟用修復電子郵件提示。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 **目錄 + 訂用帳戶** 圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 Azure AD B2C 的 [ **原則**] 底下，選取 [ **使用者流程**]。
1. 從清單中選取使用者流程。
1. 在 [設定]  下方，選取 [屬性]  。
1. 在 [ **啟用手機號碼註冊的復原電子郵件提示字元] 和 [登入 (預覽])** 中，選取：
   - **在上** ，以顯示在註冊和登入期間的修復電子郵件提示。
   - **關閉** 以隱藏復原電子郵件提示。
1. 選取 [儲存]  。

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>取得入門套件

自訂原則是一組您上傳至 Azure AD B2C 租使用者以定義使用者旅程的 XML 檔案。 我們提供的入門套件包含數個預先建立的原則。 下載相關的入門套件： 

- [電子郵件登入](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [使用者名稱登入](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [手機登入](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 選取 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) 的信賴憑證者原則。 
- [電話或電子郵件登入](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless)。 選取 [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) 的信賴憑證者原則。

下載入門套件之後。

1. 在每個檔案中，將字串取代 `yourtenant` 為您 Azure AD B2C 租使用者的名稱。 例如，如果您的 B2C 租使用者名稱是 *contosob2c*，則的所有實例都是 `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` 。

1. 完成「[開始使用自訂](custom-policy-get-started.md)原則」 Azure Active Directory B2C 中的 [[將應用程式識別碼新增至自訂原則](custom-policy-get-started.md#add-application-ids-to-the-custom-policy)] 區段中的步驟。 例如，更新 `/phone-number-passwordless/` **`Phone_Email_Base.xml`** **應用程式 (用戶端)** 您在完成必要條件 *IdentityExperienceFramework* 和 *ProxyIdentityExperienceFramework* 時所註冊的兩個應用程式的識別碼。
1. 上傳原則檔案

::: zone-end

## <a name="next-steps"></a>後續步驟

- [新增外部識別提供者](tutorial-add-identity-providers.md)
- [建立使用者流程](tutorial-create-user-flows.md)
