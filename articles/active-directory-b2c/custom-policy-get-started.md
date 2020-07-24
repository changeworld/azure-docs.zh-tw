---
title: 開始使用自訂原則
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中開始使用自訂原則。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 65ff0ab03abdbd3b403d37700dc8df4fa5eee73a
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116596"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中開始使用自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自訂原則](custom-policy-overview.md)是定義 Azure Active Directory B2C (Azure AD B2C) 租用戶行為的設定檔。 在此文章中，您會建立自訂原則，使用電子郵件地址與密碼來支援本機帳戶註冊或登入。 您也會準備環境以新增識別提供者。

## <a name="prerequisites"></a>Prerequisites

- 如果您還沒有租用戶，請[建立 Azure AD B2C 租用戶](tutorial-create-tenant.md)並連結到 Azure 訂用帳戶。
- 在建立的租用戶中[註冊應用程式](tutorial-register-applications.md)，使其可與 Azure AD B2C 通訊。
- 完成[設定註冊，並以 Facebook 帳戶登入](identity-provider-facebook.md)中的步驟，以設定 Facebook 應用程式。 雖然使用自訂原則並不需要 Facebook 應用程式，但在本逐步解說中會用來示範如何在自訂原則中啟用社交登入。

## <a name="add-signing-and-encryption-keys"></a>新增簽署與加密金鑰

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在入口網站工具列中選取**目錄 + 訂用帳戶**圖示，然後選取包含 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [概觀] 頁面的 [原則] 下，選取 [識別體驗架構]。

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

新增 Facebook 應用程式的[應用程式祕密](identity-provider-facebook.md)作為原則金鑰。 您可使用在本文先決條件中所建立應用程式的應用程式祕密。

1. 選取 [原則金鑰]，然後選取 [新增]。
1. 針對 [選項] 選擇 `Manual`。
1. 針對 [名稱] 輸入 `FacebookSecret`。 可能會自動加入前置詞 `B2C_1A_`。
1. 在 [祕密] 中，輸入 developers.facebook.com 提供給 Facebook 應用程式「應用程式祕密」。 這個值是祕密，而非應用程式識別碼。
1. 針對 [金鑰使用方法] 選取 [簽章]。
1. 選取 [建立]。

## <a name="register-identity-experience-framework-applications"></a>註冊身分識別體驗架構應用程式

Azure AD B2C 會要求註冊兩個用來以本機帳戶註冊和登入使用者的應用程式：*IdentityExperienceFramework* (Web API) 和 *ProxyIdentityExperienceFramework* (原生應用程式，具有對 IdentityExperienceFramework 應用程式的委派權限)。 使用者可使用電子郵件地址或使用者名稱和密碼來註冊，以存取租用戶註冊的應用程式，建立「本機帳戶」。 本機帳戶只存在於 Azure AD B2C 租用戶。

您只需要在 Azure AD B2C 租用戶中註冊這兩個應用程式一次。

### <a name="register-the-identityexperienceframework-application"></a>註冊 IdentityExperienceFramework 應用程式

若要在您的 Azure AD B2C 租使用者中註冊應用程式，您可以使用**應用程式註冊**體驗。

1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 針對 [名稱] 輸入 `IdentityExperienceFramework`。
1. 在 [支援的帳戶類型] 區段下，選取 [僅限此組織目錄中的帳戶]。
1. 在 [重新導向 URI] 下，選取 [Web]，然後輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是 Azure AD B2C 租用戶網域名稱。
1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。
1. 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。

接下來，藉由新增範圍來公開 API：

1. 在 [管理] 底下，選取 [公開 API]。
1. 依序選取 [新增範圍]、[儲存並繼續]，接受預設的應用程式識別碼 URI。
1. 輸入下列值，以建立允許在 Azure AD B2C 租用戶中執行自訂原則的範圍：
    * **範圍名稱**：`user_impersonation`
    * **管理員同意顯示名稱**：`Access IdentityExperienceFramework`
    * **管理員同意描述**：`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. 選取 [新增範圍]

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>註冊 ProxyIdentityExperienceFramework 應用程式

1. 選取 [應用程式註冊]，然後選取 [新增註冊]。
1. 針對 [名稱] 輸入 `ProxyIdentityExperienceFramework`。
1. 在 [支援的帳戶類型] 區段下，選取 [僅限此組織目錄中的帳戶]。
1. 在 [重新導向 URI] 下，使用下拉式清單來選取 [公用用戶端/原生 (行動裝置及桌上型電腦)]。
1. 針對 [重新導向 URI]，輸入 `myapp://auth`。
1. 在 [權限] 下，選取 [對 openid 與 offline_access 權限授與管理員同意] 核取方塊。
1. 選取 [註冊]。
1. 記錄 [應用程式 (用戶端) 識別碼]，以便在稍後的步驟中使用。

接下來，指定應將應用程式視為公用用戶端：

1. 在 [管理] 底下，選取 [驗證]。
1. 在 [進階設定] 下，啟用 [將應用程式視為公用用戶端] (選取 [是])。 確定已在應用程式資訊清單中設定 **"allowPublicClient": true**。 
1. 選取 [儲存]。

現在，將權限授與稍早在 *IdentityExperienceFramework* 註冊中公開的 API 範圍：

1. 在 [管理] 之下選取 [API 權限]。
1. 在 [已設定的權限] 底下，選取 [新增權限]。
1. 選取 [我的 API] 索引標籤，然後選取 [IdentityExperienceFramework] 應用程式。
1. 在 [權限] 下，選取稍早定義的 [user_impersonation] 範圍。
1. 選取 [新增權限]。 依照指示，稍等幾分鐘，然後再繼續進行下一個步驟。
1. 選取 [授與管理員同意 (您的租用戶名稱)]。
1. 選取您目前登入的系統管理員帳戶，或使用 Azure AD B2C 租用戶中的帳戶 (該租用戶至少已指派「雲端應用程式管理員」角色) 來登入。
1. 選取 [接受]。
1. 選取 **[** 重新整理]，然後確認「已授與 ...」出現在 [範圍-offline_access]、[openid] 和 [user_impersonation] 的 [**狀態**] 之下。 權限可能需要幾分鐘的時間來傳播。

* * *

## <a name="custom-policy-starter-pack"></a>自訂原則入門套件

自訂原則是一組上傳至 Azure AD B2C 租用戶的 XML 檔案，可以定義技術設定檔和使用者旅程圖。 我們提供入門套件，其中包含數個預先建置的原則以供快速進行。 這當中每個入門套件，針對要完成所述的案例，均包含所需最小數目技術設定檔與使用者旅程圖：

- **LocalAccounts** - 只能使用本機帳戶。
- **SocialAccounts** - 只能使用社交 (或同盟) 帳戶。
- **SocialAndLocalAccounts** - 可使用本機帳戶與社交帳戶。
- **SocialAndLocalAccountsWithMFA** - 啟用社交、本機與多重要素驗證選項。

每個入門套件均包含：

- **基底檔案** - 需要對基底做一些修改。 範例：*TrustFrameworkBase.xml*
- **Extension file** - 大部分的設定變更都在這個檔案中完成。 範例：*TrustFrameworkExtensions.xml*
- **信賴憑證者檔案** - 工作特定檔案，由應用程式呼叫。 範例：*SignUpOrSignin.xml*、*ProfileEdit.xml*、*PasswordReset.xml*

在本文中，您會在 **SocialAndLocalAccounts** 入門套件中編輯 XML 自訂原則檔案。 如果需要 XML 編輯器，請試用 [Visual Studio Code](https://code.visualstudio.com/download)，這是一個輕巧的跨平台編輯器。

### <a name="get-the-starter-pack"></a>取得入門套件

從 GitHub 取得自訂原則入門套件，然後使用 Azure AD B2C 租用戶名稱更新 SocialAndLocalAccounts 入門套件中的 XML 檔案。

1. [下載 .zip 檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或複製存放庫：

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 在 **SocialAndLocalAccounts** 目錄中的所有檔案中，將字串 `yourtenant` 取代為 Azure AD B2C 租用戶名稱。

    例如，如果 B2C 租用戶名稱是 *contosotenant*，則 `yourtenant.onmicrosoft.com` 的所有執行個體都會變成 `contosotenant.onmicrosoft.com`。

### <a name="add-application-ids-to-the-custom-policy"></a>將應用程式識別碼新增至自訂原則

將應用程式識別碼新增至擴充檔案 *TrustFrameworkExtensions.xml*。

1. 開啟 `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 並找到 `<TechnicalProfile Id="login-NonInteractive">` 項目。
1. 以稍早所建立 IdentityExperienceFramework 應用程式識別碼來取代 `IdentityExperienceFrameworkAppId` 的兩個執行個體。
1. 以稍早所建立 ProxyIdentityExperienceFramework 應用程式識別碼來取代 `ProxyIdentityExperienceFrameworkAppId` 的兩個執行個體。
1. 儲存檔案。

## <a name="upload-the-policies"></a>上傳原則

1. 在 Azure 入口網站的 B2C 租用戶中，選取 [Identity Experience Framework] 功能表項目。
1. 選取 [上傳自訂原則]。
1. 以此順序上傳原則檔案：
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

當上傳檔案時，Azure 會將前置詞 `B2C_1A_` 新增至每個檔案。

> [!TIP]
> 如果 XML 編輯器支援驗證，請根據入門套件根目錄中的 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 結構描述來驗證檔案。 在上載之前，XML 結構描述驗證會識別錯誤。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在 [自訂原則] 下，選取 [B2C_1A_signup_signin]。
1. 針對自訂原則 [概觀] 頁面上的 [選取應用程式]，選取名為 *webapp1* 且先前註冊的 Web 應用程式。
1. 確定 [回覆 URL] 是 `https://jwt.ms`。
1. 選取 [立即執行]。
1. 使用電子郵件地址註冊。
1. 再次選取 [立即執行]。
1. 使用相同的帳戶登入，以確認您的設定正確。

## <a name="add-facebook-as-an-identity-provider"></a>將 Facebook 新增為識別提供者

如[先決條件](#prerequisites)中所述，使用自訂原則時「不需要」Facebook，但在這裡用來示範如何在自訂原則中啟用同盟社交登入。

1. 在 `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 檔案中，將 `client_id` 的值取代為 Facebook 應用程式識別碼：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 將 *TrustFrameworkExtensions.xml* 檔案上傳至您的租用戶。
1. 在 [自訂原則] 下，選取 [B2C_1A_signup_signin]。
1. 依序選取 [立即執行]、[Facebook]，以使用 Facebook 登入並測試自訂原則。

## <a name="next-steps"></a>後續步驟

接下來，嘗試新增 Azure Active Directory (Azure AD) 作為識別提供者。 此入門指南中所使用的基底檔案已經包含新增其他識別提供者 (例如 Azure AD) 時所需部分內容。

如需設定 Azure AD 作為識別提供者的資訊，請參閱[使用 Active Directory B2C 自訂原則透過 Azure Active Directory 帳戶設定註冊和登入](identity-provider-azure-ad-single-tenant-custom.md)。
