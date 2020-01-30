---
title: 開始使用自訂原則-Azure Active Directory B2C
description: 瞭解如何開始使用 Azure Active Directory B2C 中的自訂原則。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cd1b5f120952752e956c957df9daa124a06d7b63
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847624"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中開始使用自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自訂原則](custom-policy-overview.md)是定義 Azure Active Directory B2C （Azure AD B2C）租使用者行為的設定檔。 在此文章中，您會建立自訂原則，使用電子郵件地址與密碼來支援本機帳戶註冊或登入。 您也會準備環境以新增識別提供者。

## <a name="prerequisites"></a>必要條件

- 如果您還沒有帳戶，請建立連結到您 Azure 訂用帳戶的[Azure AD B2C 租](tutorial-create-tenant.md)使用者。
- 在您建立的租使用者中[註冊您的應用程式](tutorial-register-applications.md)，讓它可以與 Azure AD B2C 進行通訊。
- 完成[設定註冊和以 Facebook 帳戶登入](identity-provider-facebook.md)中的步驟，以設定 facebook 應用程式。

## <a name="add-signing-and-encryption-keys"></a>新增簽署與加密金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取 [**目錄 + 訂**用帳戶] 圖示，然後選取包含您 Azure AD B2C 租使用者的目錄。
1. 在 Azure 入口網站中，搜尋並選取  **Azure AD B2C**。
1. 在 [總覽] 頁面的 [**原則**] 底下，選取 [ **Identity Experience Framework**]。

### <a name="create-the-signing-key"></a>建立簽署金鑰

1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項] 選擇 `Generate`。
1. 在 [名稱] 中輸入 `TokenSigningKeyContainer`。 可能會自動加入前置詞 `B2C_1A_`。
1. 針對 [金鑰類型] 選取 [RSA]。
1. 針對 [金鑰使用方法] 選取 [簽章]。
1. 選取 [建立]。

### <a name="create-the-encryption-key"></a>建立加密金鑰

1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項] 選擇 `Generate`。
1. 在 [名稱] 中輸入 `TokenEncryptionKeyContainer`。 可能會自動加入前置詞 `B2C_1A`_。
1. 針對 [金鑰類型] 選取 [RSA]。
1. 針對 [金鑰使用方法] 選取 [加密]。
1. 選取 [建立]。

### <a name="create-the-facebook-key"></a>建立 Facebook 金鑰

新增您的 Facebook 應用程式的[應用程式密碼](identity-provider-facebook.md)作為原則金鑰。 您可以使用您在本文必要條件中所建立之應用程式的應用程式密碼。

1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項] 選擇 `Manual`。
1. 針對 [名稱] 輸入 `FacebookSecret`。 可能會自動加入前置詞 `B2C_1A_`。
1. 在 [**秘密**] 中，從 developers.facebook.com 輸入您的 Facebook 應用程式的*應用程式密碼*。 這個值是秘密，而不是應用程式識別碼。
1. 針對 [金鑰使用方法] 選取 [簽章]。
1. 選取 [建立]。

## <a name="register-identity-experience-framework-applications"></a>註冊身分識別體驗架構應用程式

Azure AD B2C 要求您註冊兩個應用程式，以用來註冊和登入具有本機帳戶的使用者： *IdentityExperienceFramework*、Web API 和*ProxyIdentityExperienceFramework*，這是具有 IdentityExperienceFramework 應用程式委派許可權的原生應用程式。 您的使用者可以使用電子郵件地址或使用者名稱和密碼來註冊，以存取租使用者註冊的應用程式，以建立「本機帳戶」。 本機帳戶只存在於您的 Azure AD B2C 租使用者中。

您只需要在 Azure AD B2C 租使用者中註冊這兩個應用程式一次。

### <a name="register-the-identityexperienceframework-application"></a>註冊 IdentityExperienceFramework 應用程式

若要在您的 Azure AD B2C 租用戶中註冊應用程式，您可以使用目前的**應用程式**體驗，或使用新整合的**應用程式註冊 (預覽)** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[應用程式](#tab/applications/)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]。
1. 在 [ **Azure Active Directory**總覽] 功能表的 [**管理**] 底下，選取 **[應用程式註冊（舊版）** ]。
1. 選取 [新增應用程式註冊]。
1. 針對 [名稱] 輸入 `IdentityExperienceFramework`。
1. 針對 [應用程式類型] 選擇 [Web 應用程式/API]。
1. 針對 [登入 URL] 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶網域名稱。 所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。
1. 選取 [建立]。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 選取 [應用程式註冊 (預覽)]，然後選取 [新增註冊]。
1. 針對 [名稱] 輸入 `IdentityExperienceFramework`。
1. 在 [**支援的帳戶類型**] 底下，選取 [**僅此組織目錄中的帳戶**]。
1. 在 [重新**導向 URI**] 底下，選取 [ **Web**]，然後輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租使用者功能變數名稱。
1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。
1. 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。

接下來，藉由新增範圍來公開 API：

1. 在 [管理] 底下，選取 [公開 API]。
1. 選取 [**新增領域**]，然後選取 [**儲存並繼續**] 以接受預設的應用程式識別碼 URI。
1. 輸入下列值，以建立可在您的 Azure AD B2C 租使用者中執行自訂原則的範圍：
    * **範圍名稱**：`user_impersonation`
    * **管理員同意顯示名稱**：`Access IdentityExperienceFramework`
    * **管理員同意描述**：`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. 選取 [新增範圍]

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>註冊 ProxyIdentityExperienceFramework 應用程式

#### <a name="applicationstabapplications"></a>[應用程式](#tab/applications/)

1. 在 **[應用程式註冊（舊版）** ] 中，選取 [**新增應用程式註冊**]。
1. 針對 [名稱] 輸入 `ProxyIdentityExperienceFramework`。
1. 針對 [應用程式類型] 選擇 [原生]。
1. 針對 [重新導向 URI] 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶。
1. 選取 [建立]。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。
1. 選取 [**設定**]，然後選取 [**必要許可權**]，再選取 [**新增**]。
1. 選擇 [**選取 API**]，搜尋並選取 [ **IdentityExperienceFramework**]，然後按一下 [**選取**]。
1. 選取 [存取 IdentityExperienceFramework] 旁的核取方塊、按一下 [選取]，然後按一下 [完成]。
1. 選取 **[授與許可權**]，然後選取 **[是]** 進行確認。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 選取 [應用程式註冊 (預覽)]，然後選取 [新增註冊]。
1. 針對 [名稱] 輸入 `ProxyIdentityExperienceFramework`。
1. 在 [**支援的帳戶類型**] 底下，選取 [**僅此組織目錄中的帳戶**]。
1. 在 [重新導向 URI] 下，使用下拉式清單來選取 [公用用戶端/原生 (行動裝置及桌上型電腦)]。
1. 針對 [重新導向 URI] 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶。
1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。
1. 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。

接下來，指定應將應用程式視為公用用戶端：

1. 在 [管理] 底下，選取 [驗證]。
1. 選取 [試用全新體驗] (若顯示的話)。
1. 在 [**高級設定**] 下，啟用 [**將應用程式視為公用用戶端**] （選取 **[是]** ）。
1. 選取 [儲存]。

現在，將許可權授與您稍早在*IdentityExperienceFramework*註冊中公開的 API 範圍：

1. 在 [管理] 之下選取 [API 權限]。
1. 在 [已設定的權限] 底下，選取 [新增權限]。
1. 選取 [**我的 api** ] 索引標籤，然後選取 [ **IdentityExperienceFramework** ] 應用程式。
1. 在 [**許可權**] 底下，選取您稍早定義的**user_impersonation**範圍。
1. 選取 [新增權限]。 依照指示，稍等幾分鐘，然後再繼續進行下一個步驟。
1. 選取 [授與管理員同意 (您的租用戶名稱)]。
1. 選取您目前登入的系統管理員帳戶，或使用 Azure AD B2C 租用戶中的帳戶 (該租用戶至少已指派「雲端應用程式管理員」角色) 來登入。
1. 選取 [接受]。
1. 選取 [重新整理]，然後確認 [授與...] 出現在這個範圍的 [狀態] 下方。 權限可能需要幾分鐘的時間來傳播。

* * *

## <a name="custom-policy-starter-pack"></a>自訂原則入門套件

自訂原則是一組您上傳至 Azure AD B2C 租使用者以定義技術設定檔和使用者旅程的 XML 檔案。 我們會提供入門套件，其中包含數個預先建立的原則，可讓您快速進行。 這些入門套件中的每一個都包含最小數目的技術設定檔，以及完成所述案例所需的使用者旅程：

- **LocalAccounts** -僅允許使用本機帳戶。
- **Socialaccounts 只能**-只允許使用社交（或同盟）帳戶。
- **SocialAndLocalAccounts** -啟用本機和社交帳戶的使用。
- **SocialAndLocalAccountsWithMFA** -啟用社交、本機和多重要素驗證選項。

每個入門套件均包含：

- **基底**檔案-基底需要一些修改。 範例： *trustframeworkbase.xml .xml*
- **擴充**檔案-此檔案是大部分設定變更的位置。 範例： *TrustFrameworkExtensions .xml*
- **信賴**憑證者檔案-應用程式所呼叫的工作特定檔案。 範例： *signuporsignin.xml*， *Profileedit.xml*.xml， *passwordreset.xml .xml*

在本文中，您會編輯**SocialAndLocalAccounts**入門套件中的 XML 自訂原則檔案。 如果您需要 XML 編輯器，請嘗試[Visual Studio Code](https://code.visualstudio.com/download)，這是一個輕巧的跨平臺編輯器。

### <a name="get-the-starter-pack"></a>取得入門套件

從 GitHub 取得自訂原則入門套件，然後使用您的 Azure AD B2C 租使用者名稱更新 SocialAndLocalAccounts 入門套件中的 XML 檔案。

1. [下載 .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)檔案或複製存放庫：

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 在**SocialAndLocalAccounts**目錄中的所有檔案中，將字串 `yourtenant` 取代為 Azure AD B2C 租使用者的名稱。

    例如，如果您的 B2C 租使用者名稱是*contosotenant*，則 `yourtenant.onmicrosoft.com` 的所有實例都會 `contosotenant.onmicrosoft.com`。

### <a name="add-application-ids-to-the-custom-policy"></a>將應用程式識別碼新增至自訂原則

將應用程式識別碼新增至擴充檔案 *TrustFrameworkExtensions.xml*。

1. 開啟 `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** 並尋找 `<TechnicalProfile Id="login-NonInteractive">`的元素。
1. 以您稍早建立之 IdentityExperienceFramework 應用程式的應用程式識別碼取代 `IdentityExperienceFrameworkAppId` 的兩個實例。
1. 以您稍早建立之 ProxyIdentityExperienceFramework 應用程式的應用程式識別碼取代 `ProxyIdentityExperienceFrameworkAppId` 的兩個實例。
1. 儲存檔案。

## <a name="upload-the-policies"></a>上傳原則

1. 在 Azure 入口網站的 B2C 租使用者中，選取 [ **Identity Experience Framework** ] 功能表項目。
1. 選取 **[上傳自訂原則**]。
1. 在此順序中，上傳原則檔案：
    1. *Trustframeworkbase.xml .xml*
    1. *TrustFrameworkExtensions .xml*
    1. *Signuporsignin.xml .xml*
    1. *Profileedit.xml .xml*
    1. *Passwordreset.xml .xml*

當您上傳檔案時，Azure 會將前置詞 `B2C_1A_` 新增至每個檔案。

> [!TIP]
> 如果您的 XML 編輯器支援驗證，請根據位於入門套件根目錄中的 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 架構來驗證檔案。 在上載之前，XML 結構描述驗證會識別錯誤。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在 [**自訂原則**] 底下，選取 [ **B2C_1A_signup_signin**]。
1. 針對 [**選取應用程式**]，在自訂原則的 [總覽] 頁面上，選取您先前註冊之名為*webapp1*的 web 應用程式。
1. 請確定 [**回復 URL** ] 是 `https://jwt.ms`。
1. 選取 [立即執行]。
1. 使用電子郵件地址註冊。
1. 再次選取 [**立即執行**]。
1. 使用相同的帳戶登入，以確認您的設定正確。

## <a name="add-facebook-as-an-identity-provider"></a>將 Facebook 新增為識別提供者

1. 在 `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** 檔案中，將 `client_id` 的值取代為 Facebook 應用程式識別碼：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 將 *TrustFrameworkExtensions.xml* 檔案上傳至您的租用戶。
1. 在 [**自訂原則**] 底下，選取 [ **B2C_1A_signup_signin**]。
1. 選取 [**立即執行**]，然後選取 [facebook] 以使用 Facebook 登入並測試自訂原則。

## <a name="next-steps"></a>後續步驟

接下來，請嘗試新增 Azure Active Directory （Azure AD）做為身分識別提供者。 本快速入門手冊中所使用的基底檔案，已包含新增其他身分識別提供者（例如 Azure AD）所需的部分內容。

如需將 Azure AD 設定為和識別提供者的詳細資訊，請參閱[使用 Active Directory B2C 自訂原則，設定使用 Azure Active Directory 帳戶的註冊和登入](identity-provider-azure-ad-single-tenant-custom.md)。
