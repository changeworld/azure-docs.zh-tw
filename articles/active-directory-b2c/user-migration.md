---
title: 使用者遷移方法
titleSuffix: Azure AD B2C
description: 討論使用 Azure AD 圖形 API 進行使用者遷移的核心和先進概念，並選擇性地使用 Azure AD B2C 的自訂原則。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 6c70ab718235a67d9f039df638f8ba90b21ab15b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847832"
---
# <a name="migrate-users-to-azure-active-directory-b2c"></a>將使用者遷移至 Azure Active Directory B2C

當您將身分識別提供者遷移至 Azure Active Directory B2C （Azure AD B2C）時，您可能也需要遷移使用者帳戶。 本文說明如何將任何識別提供者的現有使用者帳戶移轉至 Azure AD B2C。 本文並不是為了規範性，而是要說明幾個案例。 每種方法是否適合則屬開發人員的責任。

## <a name="user-migration-flows"></a>使用者移轉流程

有了 Azure AD B2C，您就可以透過[Azure AD 圖形 API][B2C-GraphQuickStart]來遷移使用者。 使用者移轉程序分為兩個流程：

- **移轉前**：當您能夠完全存取使用者認證 (使用者名稱和密碼) 時，或是認證已加密，但您可以將認證解密時，便適用此流程。 此移轉前程序包括從舊識別提供者讀取使用者，然後在 Azure AD B2C 目錄中建立新的帳戶。

- **移轉前和密碼重設**：當您無法存取使用者的密碼時，便適用此流程。 例如：
  - 密碼以 HASH 格式儲存。
  - 密碼儲存在您無法存取的識別提供者中。 舊識別提供者會呼叫 Web 服務來驗證使用者認證。

在這兩個流程中，您要先執行移轉前程序、從舊識別提供者讀取使用者，然後在 Azure AD B2C 目錄中建立新帳戶。 如果您沒有密碼，則會使用隨機產生的密碼來建立帳戶。 然後您要要求使用者變更密碼，或當使用者第一次登入時，Azure AD B2C 會要求使用者重設密碼。

## <a name="password-policy"></a>密碼原則

Azure AD B2C 的本機帳戶密碼原則是以 Azure AD 原則為基礎的。 Azure AD B2C 註冊或登入和密碼重設原則會使用「強式」密碼強度，而且不會讓任何密碼過期。 如需詳細資訊，請參閱 [Azure AD 中的密碼原則][AD-PasswordPolicies]。

如果您想要移轉的帳戶所使用的密碼強度比[由 Azure AD B2C 強制執行的強式密碼強度][AD-PasswordPolicies]還弱，您可以停用強式密碼需求。 若要變更預設密碼原則，請將 `passwordPolicies` 屬性設定為 `DisableStrongPassword`。 例如，您可以修改「建立使用者要求」，如下所示：

```JSON
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="step-1-use-azure-ad-graph-api-to-migrate-users"></a>步驟 1：使用 Azure AD Graph API 來移轉使用者

您可以透過圖形 API (使用密碼或隨機密碼) 建立 Azure AD B2C 使用者帳戶。 本節說明在 Azure AD B2C 目錄中使用圖形 API 建立使用者帳戶的程序。

### <a name="step-11-register-your-application-in-your-tenant"></a>步驟 1.1：在您的租用戶中註冊您的應用程式

若要與圖形 API 通訊，您必須先獲得含有系統管理權限的服務帳戶。 在 Azure AD 中，您會註冊應用程式，並啟用該目錄的寫入權限。 應用程式認證是**應用程式識別碼**和**應用程式密碼**。 應用程式會以本身 (而非使用者的身分) 呼叫圖形 API。

首先，註冊您可用於管理工作的應用程式，例如使用者遷移。

[!INCLUDE [active-directory-b2c-appreg-mgmt](../../includes/active-directory-b2c-appreg-mgmt.md)]

### <a name="step-12-grant-administrative-permission-to-your-application"></a>步驟1.2：授與系統管理許可權給您的應用程式

接下來，將寫入目錄所需的 Azure AD 圖形 API 許可權授與應用程式。

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]

### <a name="step-13-create-the-application-secret"></a>步驟1.3：建立應用程式密碼

建立用戶端秘密（金鑰），供您在稍後步驟中設定的使用者遷移應用程式使用。

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

現在您有一個應用程式，具有在 Azure AD B2C 租使用者中建立、讀取和更新使用者的許可權。

### <a name="step-14-optional-environment-cleanup"></a>步驟 1.4：(選用) 環境清除

*讀取和寫入目錄資料*許可權*不*包含刪除使用者的許可權。 若要讓您的應用程式能夠刪除使用者 (清除您的環境)，您必須執行額外的步驟，這牽涉到執行 PowerShell 設定使用者帳戶系統管理員權限。 否則，您可以跳到下一節。

> [!IMPORTANT]
> 您必須使用 B2C 租用戶*本機*的 B2C 租用戶系統管理員帳戶。 帳戶名稱語法是*admin\@contosob2c.onmicrosoft.com*。

在需要[Azure AD PowerShell V2 模組][AD-Powershell]的此 powershell 腳本中，請執行下列動作：

1. 連線到您的線上服務。 若要這麼做，請在 Windows PowerShell 命令提示字元執行 `Connect-AzureAD` Cmdlet，並提供您的認證。

1. 使用**應用程式識別碼**來為應用程式指派使用者帳戶管理員角色。 這些角色都有已知的識別碼，您只需要在指令碼中輸入您的**應用程式識別碼**即可。

```powershell
# NOTE: This script REQUIRES the Azure AD PowerShell V2 module
#       https://docs.microsoft.com/powershell/azure/active-directory/install-adv2

Connect-AzureAD

$AppId = "<Your application ID>"

# Fetch Azure AD application to assign to role
$roleMember = Get-AzureADServicePrincipal -Filter "AppId eq '$AppId'"

# Fetch User Account Administrator role instance
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}

# If role instance does not exist, instantiate it based on the role template
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq 'User Account Administrator'}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId

    # Fetch User Account Administrator role instance again
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq 'User Account Administrator'}
}

# Add application to role
Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId $roleMember.ObjectId

# Fetch role membership for role to confirm
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
```

使用您的 Azure AD **應用程式識別碼**變更 `$AppId` 值。

## <a name="step-2-pre-migration-application-sample"></a>步驟 2：移轉前應用程式範例

您可以在 GitHub `azure-ad-b2c/user-migration` GitHub 存放庫中找到預先遷移程式碼範例：

[azure-ad-b2c/使用者-遷移/預先遷移][UserMigrationSample-code]（GitHub）

### <a name="step-21-edit-the-migration-data-file"></a>步驟 2.1：編輯移轉資料檔案

應用程式範例使用包含虛擬使用者資料的 JSON 檔案。 成功執行範例後，您可以將程式碼變更為取用您自己資料庫中的資料。 或者您可以將使用者設定檔匯出至 JSON 檔案，然後將應用程式設定為使用該檔案。

若要編輯 JSON 檔案，請開啟 `AADB2C.UserMigration.sln` Visual Studio 方案。 在 `AADB2C.UserMigration` 專案中，開啟 `UsersData.json` 檔案。

![UsersData 的部分，其中顯示兩個使用者的 JSON 區塊](./media/user-migration/pre-migration-data-file.png)

如您所見，此檔案包含使用者實體的清單。 每個使用者實體都有下列屬性：

- 電子郵件
- displayName
- firstName
- lastName
- 密碼 (可空白)

> [!NOTE]
> 編譯時，Visual Studio 會將檔案複製到 `bin` 目錄。

### <a name="step-22-configure-the-application-settings"></a>步驟 2.2：設定應用程式設定

在 `AADB2C.UserMigration` 專案底下，開啟 *App.config* 檔案。 使用您自己的值取代下列應用程式設定：

```XML
<appSettings>
    <add key="b2c:Tenant" value="{Your Tenant Name}" />
    <add key="b2c:ClientId" value="{The ApplicationID from above}" />
    <add key="b2c:ClientSecret" value="{The Client Secret Key from above}" />
    <add key="MigrationFile" value="{Name of a JSON file containing the users' data; for example, UsersData.json}" />
    <add key="BlobStorageConnectionString" value="{Your connection Azure table string}" />
</appSettings>
```

> [!NOTE]
> - 後面幾節會說明如何使用 Azure 資料表的連接字串。
> - B2C 租用戶名稱是您在租用戶建立期間輸入的網域，並且會顯示在 Azure 入口網站中。 租用戶名稱通常以尾碼 *.onmicrosoft.com* 結尾 (例如， *.contosob2c.onmicrosoft.com*)。

### <a name="step-23-run-the-pre-migration-process"></a>步驟 2.3：執行移轉前程序

以滑鼠右鍵按一下 `AADB2C.UserMigration` 方案，然後重建範例。 如果成功，您現在應該有一個 `UserMigration.exe` 可執行檔位於 `AADB2C.UserMigration\bin\Debug\net461`。 若要執行移轉程序，請使用下列其中一個命令列參數：

- 若要**使用密碼移轉使用者**，請使用 `UserMigration.exe 1` 命令。

- 若要**使用隨機密碼移轉使用者**，則請使用 `UserMigration.exe 2` 命令。 這項作業也會建立 Azure 資料表實體。 稍後您可以設定呼叫 REST API 服務的原則。 此服務會使用 Azure 資料表來追蹤和管理移轉程序。

![顯示 UserMigration 命令輸出的命令提示字元視窗](./media/user-migration/pre-migration-demo.png)

### <a name="step-24-check-the-pre-migration-process"></a>步驟 2.4：檢查移轉前程序

若要驗證移轉，請使用下列兩種方法之一：

- 若要依顯示名稱搜尋使用者，請使用 Azure 入口網站：

   1. 開啟**Azure AD B2C**，然後選取 [**使用者**]。
   1. 在搜尋方塊中輸入使用者的顯示名稱，然後檢視使用者的設定檔。

- 若要以登入電子郵件地址抓取使用者，請使用範例應用程式：

   1. 執行以下命令：

      ```Console
          UserMigration.exe 3 {email address} > UserProfile.json
      ```

      > [!TIP]
      > 您也可以使用下列命令，依顯示名稱擷取使用者：`UserMigration.exe 4 "<Display name>"`。

   1. 在 JSON 編輯器中開啟 UserProfile.json 檔案，查看使用者資訊。

      ![UserProfile. json 檔案在 Visual Studio Code 編輯器中開啟](./media/user-migration/pre-migration-get-by-email2.png)

### <a name="step-25-optional-environment-cleanup"></a>步驟 2.5：(選用) 環境清除

如果您想要清除 Azure AD 租用戶並從 Azure AD 目錄移除使用者，請執行 `UserMigration.exe 5` 命令。

> [!NOTE]
> * 若要清除租用戶，請為應用程式設定「使用者帳戶管理員」權限。
> * 移轉應用程式範例會清除 JSON 檔案中列出的所有使用者。

### <a name="step-26-sign-in-with-migrated-users-with-password"></a>步驟 2.6：使用已移轉的使用者登入 (使用密碼)

利用使用者的密碼執行移轉前程序之後，帳戶便可供使用，使用者就能使用 Azure AD B2C 登入您的應用程式。 如果您無法存取使用者的密碼，請繼續瀏覽下一節。

## <a name="step-3-help-users-reset-their-password"></a>步驟 3：幫助使用者重設密碼

如果您使用隨機密碼移轉使用者，使用者就必須重設密碼。 若要協助他們重設密碼，請傳送有連結的歡迎電子郵件以重設密碼。

若要取得密碼重設原則的連結，請遵循下列步驟。 此程式假設您先前已建立密碼重設[自訂原則](custom-policy-get-started.md)。

1. 使用[Azure 入口網站](https://portal.azure.com)右上方區段中的 [**目錄 + 訂**用帳戶] 篩選，選取包含您 Azure AD B2C 租使用者的目錄。
1. 在左側功能表（或從 [**所有服務**] 內）選取 [ **Azure AD B2C** ]。
1. 在 [**原則**] 底下，選取 [ **Identity Experience Framework**]。
1. 選取您的密碼重設原則。 例如， *B2C_1A_PasswordReset*。
1. 在 [**選取應用程式**] 下拉式選單中選取您的應用程式。

    > [!NOTE]
    > [**立即執行**] 需要在您的租使用者中註冊至少一個應用程式。 若要瞭解如何註冊應用程式，請參閱[教學課程：在 Azure Active Directory B2C 中註冊應用程式][B2C-AppRegister]。

1. 複製 [**立即執行端點**] 文字方塊中所顯示的 URL，然後將它傳送給您的使用者。

    ![已反白顯示 [立即執行] 端點的 [密碼重設原則] 頁面](./media/user-migration/pre-migration-policy-uri.png)

## <a name="step-4-optional-change-your-policy-to-check-and-set-the-user-migration-status"></a>步驟 4：(選用) 變更原則以檢查並設定使用者移轉狀態

> [!NOTE]
> 若要檢查並變更使用者移轉狀態，您必須使用自訂原則。 您必須完成[開始使用自訂原則][B2C-GetStartedCustom]中的設定指示。

當使用者不先重設密碼就嘗試登入時，您的原則應該會傳回易懂的錯誤訊息。 例如：

> *您的密碼已過期。若要重設，請選取 [重設密碼] 連結。*

此選用步驟需要使用 Azure AD B2C 自訂原則，如[開始使用自訂原則][B2C-GetStartedCustom]一文所述。

在本節中，您會變更原則以在登入時檢查使用者移轉狀態。 如果使用者未變更密碼，則會傳回 HTTP 409 錯誤訊息，要求使用者選取**忘記密碼？** 連結。

若要追蹤密碼的變更，您可以使用 Azure 資料表。 當您使用命令列參數 `2` 執行移轉前程序時，就是在 Azure 資料表中建立使用者實體。 您的服務會執行下列動作：

- 在登入時，Azure AD B2C 原則會叫用您的移轉 RESTful 服務，傳送電子郵件訊息作為輸入宣告。 此服務會在 Azure 資料表中搜尋電子郵件地址。 如果已有地址，服務會擲回錯誤訊息：*您必須變更密碼*。

- 使用者成功變更密碼之後，請從 Azure 資料表移除實體。

> [!NOTE]
> 我們使用 Azure 資料表來簡化範例。 您可以將移轉狀態儲存在任何資料庫，或儲存為 Azure AD B2C 帳戶中的自訂屬性。

### <a name="41-update-your-application-setting"></a>4.1：更新應用程式設定

1. 若要測試 RESTful API 示範，請在 Visual Studio 中開啟 `AADB2C.UserMigration.sln`。
1. 在 `AADB2C.UserMigration.API` 專案中，*開啟 web.config 檔案*。 使用[步驟 2.2](#step-22-configure-the-application-settings) 中的設定取代此設定：

    ```json
    {
        "BlobStorageConnectionString": "{The Azure Blob storage connection string}",
        ...
    }
    ```

### <a name="step-42-deploy-your-web-application-to-azure-app-service"></a>步驟 4.2：將 Web 應用程式部署至 Azure App Service

在 [方案總管] 中，以滑鼠右鍵按一下 `AADB2C.UserMigration.API`，並選取 [發佈...]。 依照指示來發佈至 Azure App Service。 如需詳細資訊，請參閱[將您的應用程式部署至 Azure App Service][AppService-Deploy]。

### <a name="step-43-add-a-technical-profile-and-technical-profile-validation-to-your-policy"></a>步驟 4.3：新增技術設定檔和技術設定檔驗證到您的原則中

1. 在 [方案總管] 中，展開 [方案項目]，並開啟 *TrustFrameworkExtensions.xml* 原則檔。
1. 從 `yourtenant.onmicrosoft.com` 將 `TenantId`、`PublicPolicyUri` 和 `<TenantId>` 欄位變更成您的租用戶名稱。
1. 在 `<TechnicalProfile Id="login-NonInteractive">` 元素底下，以[開始使用自訂原則][B2C-GetStartedCustom]中設定的應用程式識別碼取代 `ProxyIdentityExperienceFrameworkAppId` 和 `IdentityExperienceFrameworkAppId` 的所有實例。
1. 在 `<ClaimsProviders>` 節點下，尋找下列 XML 程式碼片段。 變更 `ServiceUrl` 的值，使其指向您的 Azure App Service URL。

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="LocalAccountSignIn">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/LoalAccountSignIn</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>

        <TechnicalProfile Id="LocalAccountPasswordReset">
          <DisplayName>Local account just in time migration</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ServiceUrl">http://{your-app}.azurewebsites.net/api/PrePasswordReset/PasswordUpdated</Item>
            <Item Key="AuthenticationType">None</Item>
            <Item Key="SendClaimsIn">Body</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          </InputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

上述技術設定檔會定義一個輸入宣告：`signInName` (以電子郵件傳送)。 在登入時，宣告就會傳送至 RESTful 端點。

在您為 RESTful API 定義技術設定檔之後，請將現有的 `SelfAsserted-LocalAccountSignin-Email` 技術設定檔設定為在*TrustFrameworkExtensions*中覆寫，以額外呼叫您的 REST API 技術設定檔：

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="LocalAccountUserMigration" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

然後，將 `LocalAccountSignIn` 技術設定檔的 `Id` 變更為 [`LocalAccountUserMigration`]。

### <a name="step-44-upload-the-policy-to-your-tenant"></a>步驟 4.4：將原則上傳至您的租用戶

1. 在 [Azure 入口網站][Portal]中，切換至[您的 Azure AD B2C 租用戶環境][B2C-NavContext]，然後選取 [Azure AD B2C]。
1. 選取 [識別體驗架構]。
1. 選取 [所有原則]。
1. 選取 [上傳原則]。
1. 選取 [覆寫已存在的原則] 核取方塊。
1. 上傳 TrustFrameworkExtensions.xml 檔案，確定它通過驗證。

### <a name="step-45-test-the-custom-policy-by-using-run-now"></a>步驟 4.5：使用 [立即執行] 測試自訂原則

1. 選取 [ **Azure AD B2C**]，然後選取 [ **Identity Experience Framework**]。
1. 開啟 *B2C_1A_signup_signin* (此為您上傳的信賴憑證者 (RP) 自訂原則)，然後選取 [立即執行]。
1. 輸入其中一個已遷移使用者的認證，然後選取 [登**入**]。 您的 REST API 應該會擲回下列錯誤訊息：

    ![顯示 [變更密碼] 錯誤訊息的登入註冊頁面](./media/user-migration/pre-migration-error-message.png)

### <a name="step-46-optional-troubleshoot-your-rest-api"></a>步驟 4.6：(選用) 針對 REST API 進行疑難排解

您可以近乎即時地檢視和監視記錄資訊。

1. 在 RESTful 應用程式 [設定] 功能表的 [監視] 底下，選取 [診斷記錄]。
1. 將 [應用程式記錄 (檔案系統)] 設定為 [開啟]。
1. 將 [層級] 設定為 [詳細資訊]。
1. 選取 [儲存]。

    ![Azure 入口網站中的診斷記錄設定頁面](./media/user-migration/pre-migration-diagnostic-logs.png)

1. 在 [設定] 功能表上，選取 [記錄資料流]。
1. 檢查 RESTful API 的輸出。

> [!IMPORTANT]
> 請只在開發與測試期間使用診斷記錄。 RESTful API 輸出可能包含不應該在生產環境中公開的機密資訊。

## <a name="optional-download-the-complete-policy-files"></a>(選用) 下載完整的原則檔案

當您完成[開始使用自訂原則][B2C-GetStartedCustom]逐步解說之後，建議您使用自己的自訂原則檔案來建立您的案例。 我們已提供[範例原則][UserMigrationSample-policy]檔案，供您參考。

[AD-PasswordPolicies]: https://docs.microsoft.com/azure/active-directory/active-directory-passwords-policy
[AD-Powershell]: https://docs.microsoft.com/powershell/azure/active-directory/install-adv2
[AppService-Deploy]: https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vs
[B2C-AppRegister]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration
[B2C-GetStarted]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started
[B2C-GetStartedCustom]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom
[B2C-GraphQuickStart]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet
[B2C-NavContext]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-navigate-to-b2c-context
[Portal]: https://portal.azure.com/
[UserMigrationSample-code]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/source-code
[UserMigrationSample-policy]: https://github.com/azure-ad-b2c/user-migration/tree/master/pre-migration/policy
