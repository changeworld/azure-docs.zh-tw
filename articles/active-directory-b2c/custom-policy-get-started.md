---
title: 開始使用自訂原則
titleSuffix: Azure AD B2C
description: 瞭解如何在 Azure 活動目錄 B2C 中開始自訂策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc87628d8b47435012c3d20ec2e72ac186983555
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189322"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中開始使用自訂原則

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自訂策略](custom-policy-overview.md)是定義 Azure 活動目錄 B2C （Azure AD B2C） 租戶行為的設定檔。 在此文章中，您會建立自訂原則，使用電子郵件地址與密碼來支援本機帳戶註冊或登入。 您也會準備環境以新增識別提供者。

## <a name="prerequisites"></a>Prerequisites

- 如果還沒有，請創建連結到 Azure 訂閱的[Azure AD B2C 租戶](tutorial-create-tenant.md)。
- 在創建的租戶中[註冊應用程式](tutorial-register-applications.md)，以便它可以與 Azure AD B2C 通信。
- 使用 Facebook[帳戶完成設置註冊和登錄](identity-provider-facebook.md)中的步驟，以配置 Facebook 應用程式。 儘管使用自訂策略不需要 Facebook 應用程式，但本演練中使用它來演示自訂策略中的啟用社交登錄。

## <a name="add-signing-and-encryption-keys"></a>新增簽署與加密金鑰

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在門戶工具列中選擇**目錄 + 訂閱**圖示，然後選擇包含 Azure AD B2C 租戶的目錄。
1. 在 Azure 門戶中，搜索並選擇**Azure AD B2C**。
1. 在概述頁上，在 **"策略"** 下，選擇 **"身份體驗框架**"。

### <a name="create-the-signing-key"></a>建立簽署金鑰

1. 選取 [原則金鑰]****，然後選取 [新增]****。
1. 針對 [選項]**** 選擇 `Generate`。
1. 在 [名稱]**** 中輸入 `TokenSigningKeyContainer`。 可能會自動加入前置詞 `B2C_1A_`。
1. 針對 [金鑰類型]**** 選取 [RSA]****。
1. 針對 [金鑰使用方法]**** 選取 [簽章]****。
1. 選取 [建立]****。

### <a name="create-the-encryption-key"></a>建立加密金鑰

1. 選取 [原則金鑰]****，然後選取 [新增]****。
1. 針對 [選項]**** 選擇 `Generate`。
1. 在 [名稱]**** 中輸入 `TokenEncryptionKeyContainer`。 可能會自動加入前置詞 `B2C_1A`_。
1. 針對 [金鑰類型]**** 選取 [RSA]****。
1. 針對 [金鑰使用方法]**** 選取 [加密]****。
1. 選取 [建立]****。

### <a name="create-the-facebook-key"></a>建立 Facebook 金鑰

將 Facebook 應用程式[的應用金鑰](identity-provider-facebook.md)添加為策略金鑰。 您可以使用作為本文先決條件一部分創建的應用程式的應用機密。

1. 選取 [原則金鑰]****，然後選取 [新增]****。
1. 針對 [選項]**** 選擇 `Manual`。
1. 針對 [名稱]**** 輸入 `FacebookSecret`。 可能會自動加入前置詞 `B2C_1A_`。
1. 在 **"秘密**"中，從developers.facebook.com輸入 Facebook*應用程式的應用程式機密*。 此值是機密，而不是應用程式 ID。
1. 針對 [金鑰使用方法]**** 選取 [簽章]****。
1. 選取 [建立]****。

## <a name="register-identity-experience-framework-applications"></a>註冊身分識別體驗架構應用程式

Azure AD B2C 要求您註冊兩個應用程式，用於使用本地帳戶註冊和登錄使用者：*身份體驗框架*、Web API 和*代理身份體驗框架*，一個具有委派到身份體驗框架應用的本機應用。 您的使用者可以使用電子郵件地址或使用者名和密碼註冊以訪問租戶註冊的應用程式，這將創建一個"本地帳戶"。 本地帳戶僅存在於 Azure AD B2C 租戶中。

只需在 Azure AD B2C 租戶中註冊這兩個應用程式一次。

### <a name="register-the-identityexperienceframework-application"></a>註冊 IdentityExperienceFramework 應用程式

要在 Azure AD B2C 租戶中註冊應用程式，可以使用**應用註冊（舊版）** 體驗或我們新的統一**應用註冊（預覽）** 體驗。 [深入了解新的體驗](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在 Azure 入口網站中，搜尋並選取 [Azure Active Directory]****。
1. 在**Azure 活動目錄**概述功能表中，在 **"管理**"下，選擇**應用註冊（舊版）。**
1. 選取 [新增應用程式註冊]****。
1. 針對 [名稱]**** 輸入 `IdentityExperienceFramework`。
1. 針對 [應用程式類型]**** 選擇 [Web 應用程式/API]****。
1. 針對 [登入 URL]**** 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶網域名稱。 所有 URL 現在都應會使用 [b2clogin.com](b2clogin.md)。
1. 選取 [建立]****。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 選取 [應用程式註冊 (預覽)]****，然後選取 [新增註冊]****。
1. 針對 [名稱]**** 輸入 `IdentityExperienceFramework`。
1. 在 **"支援帳戶類型**"下，**選擇"僅在此組織目錄中的帳戶**"。
1. 在**重定向 URI**下，選擇**Web** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，然後`your-tenant-name`輸入 Azure AD B2C 租戶功能變數名稱所在的 位置。
1. 在 [權限]**** 下，選取 [對 openid 與 offline_access 權限授與管理員同意]** 核取方塊。
1. 選取 [註冊]****。
1. 記錄 [應用程式 (用戶端) 識別碼]****，以便在稍後的步驟中使用。

接下來，通過添加作用域來公開 API：

1. 在 [管理]**** 底下，選取 [公開 API]****。
1. 選擇 **"添加作用域**"，然後選擇 **"保存並繼續**接受預設應用程式 ID URI"。
1. 輸入以下值以創建允許在 Azure AD B2C 租戶中執行自訂策略的範圍：
    * **範圍名稱**：`user_impersonation`
    * **管理員同意顯示名稱**：`Access IdentityExperienceFramework`
    * **管理員同意說明**：`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. 選取 [新增範圍]****

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>註冊 ProxyIdentityExperienceFramework 應用程式

#### <a name="applications"></a>[應用程式](#tab/applications/)

1. 在**應用註冊（舊版）** 中，選擇 **"新建應用程式註冊**"。
1. 針對 [名稱]**** 輸入 `ProxyIdentityExperienceFramework`。
1. 針對 [應用程式類型]**** 選擇 [原生]****。
1. 針對 [重新導向 URI]**** 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶。
1. 選取 [建立]****。 建立之後，複製應用程式識別碼，並儲存它以供日後使用。
1. 選擇 **"設置**"，然後選擇 **"必需許可權**"，然後選擇"**添加**"。
1. 選擇 **"選擇 API"，** 搜索並選擇 **"身份體驗框架**"，然後按一下"**選擇**"。
1. 選取 [存取 IdentityExperienceFramework]**** 旁的核取方塊、按一下 [選取]****，然後按一下 [完成]****。
1. 選擇 **"授予許可權**"，然後選擇"**是**"進行確認。

#### <a name="app-registrations-preview"></a>[應用程式註冊 (預覽)](#tab/app-reg-preview/)

1. 選取 [應用程式註冊 (預覽)]****，然後選取 [新增註冊]****。
1. 針對 [名稱]**** 輸入 `ProxyIdentityExperienceFramework`。
1. 在 **"支援帳戶類型**"下，**選擇"僅在此組織目錄中的帳戶**"。
1. 在 [重新導向 URI]**** 下，使用下拉式清單來選取 [公用用戶端/原生 (行動裝置及桌上型電腦)]****。
1. 針對 [重新導向 URI]**** 輸入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是您的 Azure AD B2C 租用戶。
1. 在 [權限]**** 下，選取 [對 openid 與 offline_access 權限授與管理員同意]** 核取方塊。
1. 選取 [註冊]****。
1. 記錄 [應用程式 (用戶端) 識別碼]****，以便在稍後的步驟中使用。

接下來，指定應用程式應視為公共用戶端：

1. 在 [管理]**** 底下，選取 [驗證]****。
1. 選取 [試用全新體驗]**** (若顯示的話)。
1. 在 **"高級設置**"下，啟用**將應用程式視為公共用戶端**（選擇 **"是**" 。
1. 選取 [儲存]****。

現在，向*在身份體驗框架*註冊中前面公開的 API 作用域授予許可權：

1. 在 [管理]**** 之下選取 [API 權限]****。
1. 在 [已設定的權限]**** 底下，選取 [新增權限]****。
1. 選擇"**我的 API"** 選項卡，然後選擇 **"身份體驗框架"** 應用程式。
1. 在 **"許可權**"下，選擇之前定義的**user_impersonation**作用域。
1. 選取 [新增權限]****。 依照指示，稍等幾分鐘，然後再繼續進行下一個步驟。
1. 選取 [授與管理員同意 (您的租用戶名稱)]****。
1. 選取您目前登入的系統管理員帳戶，或使用 Azure AD B2C 租用戶中的帳戶 (該租用戶至少已指派「雲端應用程式管理員」** 角色) 來登入。
1. 選取 [接受]****。
1. 選擇 **"刷新**"，然後驗證"已授予..."顯示在兩個作用域**的狀態**下。 權限可能需要幾分鐘的時間來傳播。

* * *

## <a name="custom-policy-starter-pack"></a>自訂策略啟動包

自訂策略是一組 XML 檔，用於定義技術設定檔和使用者旅程。 我們提供入門包與幾個預先構建的策略，讓您快速前進。 每個初學者包都包含實現上述方案所需的最少數量的技術設定檔和使用者旅程：

- **本地帳戶**- 僅啟用本地帳戶。
- **社交帳戶**- 僅啟用社交（或聯合）帳戶。
- **社交和本地帳戶**- 支援使用本地帳戶和社會帳戶。
- **社交和本地帳戶與MFA** - 啟用社交、本地和多重要素驗證選項。

每個入門套件均包含：

- **基本檔**- 基本檔不需要修改。 示例：*信任框架基礎.xml*
- **擴展檔**- 此檔是進行大多數配置更改的地方。 示例：*信任框架擴展.xml*
- **依賴方檔**- 應用程式調用的任務特定檔。 示例：*註冊或Signin.xml*，*設定檔編輯.xml*，*密碼重設.xml*

在本文中，您可以在**社交和本地帳戶**初學者包中編輯 XML 自訂策略檔。 如果您需要 XML 編輯器，請嘗試[視覺化工作室代碼](https://code.visualstudio.com/download)，一個羽量級的跨平臺編輯器。

### <a name="get-the-starter-pack"></a>獲取入門包

從 GitHub 獲取自訂策略初學者包，然後使用 Azure AD B2C 租戶名稱更新社交和本地帳戶初學者包中的 XML 檔。

1. [下載 .ZIP 檔案](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或克隆存儲庫：

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 在**社交和本地帳戶**目錄中的所有檔中，將字串`yourtenant`替換為 Azure AD B2C 租戶的名稱。

    例如，如果 B2C 租戶的名稱是*contoso 租戶*，則 的所有`yourtenant.onmicrosoft.com`實例都`contosotenant.onmicrosoft.com`變為 。

### <a name="add-application-ids-to-the-custom-policy"></a>將應用程式識別碼新增至自訂原則

將應用程式識別碼新增至擴充檔案 *TrustFrameworkExtensions.xml*。

1. 打開`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 並查找元素`<TechnicalProfile Id="login-NonInteractive">`。
1. 將 中的`IdentityExperienceFrameworkAppId`兩個實例替換為您之前創建的標識體驗框架應用程式的應用程式 ID。
1. 將 中的`ProxyIdentityExperienceFrameworkAppId`兩個實例替換為之前創建的代理身份體驗框架應用程式的應用程式 ID。
1. 儲存檔案。

## <a name="upload-the-policies"></a>上傳原則

1. 在 Azure 門戶中的 B2C 租戶中選擇 **"身份體驗框架"** 功能表項目。
1. 選擇 **"上載自訂策略**"。
1. 按此順序上載策略檔：
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *註冊或Signin.xml*
    1. *設定檔編輯.xml*
    1. *密碼重設.xml*

上載檔時，Azure 會將首碼`B2C_1A_`添加到每個檔。

> [!TIP]
> 如果 XML 編輯器支援驗證，請根據位於初學者`TrustFrameworkPolicy_0.3.0.0.xsd`包根目錄中的 XML 架構驗證檔。 在上載之前，XML 結構描述驗證會識別錯誤。

## <a name="test-the-custom-policy"></a>測試自訂原則

1. 在**自訂策略**下，選擇**B2C_1A_signup_signin。**
1. 有關在自訂策略的概述頁上**選擇應用程式**，請選擇以前註冊的名為*Webapp1*的 Web 應用程式。
1. 確保**回復 URL**為`https://jwt.ms`。
1. 選取 [立即執行]****。
1. 使用電子郵件地址註冊。
1. 再次選擇 **"立即運行**"。
1. 使用相同的帳戶登入，以確認您的設定正確。

## <a name="add-facebook-as-an-identity-provider"></a>將 Facebook 新增為識別提供者

如[先決條件](#prerequisites)中所述，Facebook*不需要*使用自訂策略，但在此處用於演示如何在自訂策略中啟用聯合社交登錄。

1. 在`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 檔中，將 的值`client_id`替換為 Facebook 應用程式 ID：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 將 *TrustFrameworkExtensions.xml* 檔案上傳至您的租用戶。
1. 在**自訂策略**下，選擇**B2C_1A_signup_signin。**
1. 選擇 **"立即運行**"，選擇 Facebook 以使用 Facebook 登錄並測試自訂策略。

## <a name="next-steps"></a>後續步驟

接下來，嘗試添加 Azure 活動目錄 （Azure AD） 作為標識提供程式。 此入門指南中使用的基本檔已包含添加其他標識提供程式（如 Azure AD）所需的一些內容。

有關將 Azure AD 設置為 和標識提供程式的資訊，請參閱[使用活動目錄 B2C 自訂策略使用 Azure 活動目錄帳戶設置註冊和登錄](identity-provider-azure-ad-single-tenant-custom.md)。
