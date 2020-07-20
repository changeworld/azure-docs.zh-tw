---
title: Azure AD B2C 和 MSAL.NET
titleSuffix: Microsoft identity platform
description: 使用 Azure AD B2C 搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時的考慮。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 3aac63369dffa5b8ba0b9e55b5063ad8136c95cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82883221"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 以社交身分識別登入使用者

您可以使用 MSAL.NET，透過[Azure Active Directory B2C （Azure AD B2C）](https://aka.ms/aadb2c)，透過社交身分識別登入使用者。 Azure AD B2C 是根據原則的概念來建立的。 在 MSAL.NET 中，指定原則會轉譯為提供授權單位。

- 當您具現化公用用戶端應用程式時，您必須將原則指定為授權單位的一部分。
- 當您想要套用原則時，請呼叫 `AcquireTokenInteractive` 可接受參數的覆寫 `authority` 。

本文適用于 MSAL.NET 3.x。 針對 MSAL.NET 2.x，請參閱 GitHub 上 MSAL.NET Wiki 中 MSAL 2.x 的[Azure AD B2C 詳細資訊](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租使用者和原則的授權單位

Azure AD B2C 的授權格式為：`https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname`-Azure AD B2C 租使用者的名稱加上主機。 例如， *contosob2c.b2clogin.com*。
- `tenant`-Azure AD B2C 租使用者的功能變數名稱或目錄（租使用者）識別碼。 例如，分別是*contosob2c.onmicrosoft.com*或 GUID。
- `policyName`-要套用的使用者流程或自訂原則的名稱。 例如， *b2c_1_susi*之類的註冊/登入原則。

如需 Azure AD B2C 授權單位的詳細資訊，請參閱[將重新導向 Url 設定為 b2clogin.com](../../active-directory-b2c/b2clogin.md)。

## <a name="instantiating-the-application"></a>將應用程式具現化

`WithB2CAuthority()`當您建立應用程式物件時，藉由呼叫來提供授權單位：

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>取得權杖以套用原則

若要在公用用戶端應用程式中取得受 Azure AD B2C 保護之 API 的權杖，您必須使用具有授權的覆寫：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

在上述程式碼片段中：

- `policy`這是一個字串，其中包含您 Azure AD B2C 的使用者流程名稱或自訂原則（例如 `PolicySignUpSignIn` ）。
- `ParentActivityOrWindow`是 Android （活動）的必要項，而且對於支援上層 UI （例如 Microsoft Windows 上的 windows 和 iOS 中的 UIViewController）的其他平臺而言是選擇性的。 如需 UI 對話方塊的詳細資訊，請參閱 MSAL Wiki 上的[WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) 。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`是一種方法，可尋找給定原則的帳戶。 例如：

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
      foreach (var account in accounts)
      {
          string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
          if (userIdentifier.EndsWith(policy.ToLower()))
              return account;
      }
      return null;
  }
  ```

目前藉由呼叫來套用使用者流程或自訂原則（例如，讓使用者編輯其設定檔或重設其密碼） `AcquireTokenInteractive` 。 針對這兩個原則，您不會使用傳回的權杖/驗證結果。

## <a name="profile-edit-policies"></a>設定檔編輯原則

若要讓您的使用者使用社交身分識別登入，然後編輯其設定檔，請套用 Azure AD B2C 編輯設定檔原則。

若要這麼做，請呼叫 `AcquireTokenInteractive` 具有該原則的授權單位。 因為使用者已經登入，而且有作用中的 cookie 會話，所以請使用 `Prompt.NoPrompt` 防止顯示帳戶選取對話方塊。

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
    try
    {
        var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                            .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                            .WithPrompt(Prompt.NoPrompt),
                            .WithB2CAuthority(App.AuthorityEditProfile)
                            .ExecuteAsync();
        DisplayBasicTokenInfo(authResult);
    }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>資源擁有者密碼認證（ROPC）

如需 ROPC 流程的詳細資訊，請參閱[使用資源擁有者密碼認證授與登入](v2-oauth-ropc.md)。

**不建議**使用 ROPC 流程，因為在您的應用程式中要求使用者提供其密碼並不安全。 如需有關此問題的詳細資訊，請參閱[密碼成長問題的解決方案為何？](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。

藉由在 ROPC 流程中使用使用者名稱/密碼，您會犧牲幾件事：

- 新式身分識別的核心原則：可以 fished 或重新執行密碼，因為共用密碼可以被攔截。 根據定義，ROPC 與無密碼流程不相容。
- 需要執行 MFA 的使用者將無法登入（因為沒有互動）。
- 使用者將無法使用單一登入（SSO）。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 中設定 ROPC 流程

在您的 Azure AD B2C 租使用者中，建立新的使用者流程，然後選取 [**使用 ROPC 登入**] 來啟用使用者流程的 ROPC。 如需詳細資訊，請參閱[設定資源擁有者密碼認證流程](/azure/active-directory-b2c/configure-ropc)。

`IPublicClientApplication`包含 `AcquireTokenByUsernamePassword` 方法：

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

這個 `AcquireTokenByUsernamePassword` 方法會採用下列參數：

- 要為其取得存取權杖的*範圍*。
- 使用者*名稱*。
- 使用者的 SecureString*密碼*。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流程的限制

ROPC 流程**僅適用于本機帳戶**，您的使用者已使用電子郵件地址或使用者名稱向 Azure AD B2C 註冊。 當同盟到 Azure AD B2C （Facebook、Google 等）支援的外部身分識別提供者時，此流程無法正常執行。

## <a name="google-auth-and-embedded-webview"></a>Google auth 和 embedded web 視圖

如果您使用 Google 做為身分識別提供者，建議您使用系統瀏覽器，因為 Google 不允許[來自 embedded 網站的驗證](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前， `login.microsoftonline.com` 是 Google 的受信任授權單位，並可與內嵌的 web 操作人員搭配使用。 不過，不是 `b2clogin.com` Google 的信任授權單位，因此使用者將無法進行驗證。

如果發生變更，我們將提供此[問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)的更新。

## <a name="token-caching-in-msalnet"></a>MSAL.NET 中的權杖快取

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知問題

MSAL.NET 支援[權杖](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)快取。 權杖快取金鑰是以身分識別提供者（IdP）傳回的宣告為基礎。

目前，MSAL.NET 需要兩個宣告來建立權杖快取金鑰：

- `tid`（Azure AD 的租使用者識別碼）
- `preferred_username`

這兩個宣告在 Azure AD B2C 案例中可能會遺失，因為並非所有的社交識別提供者（Facebook、Google 和其他人）都會在其傳回 Azure AD B2C 的權杖中傳回它們。

這類案例的徵兆是 `Missing from the token response` 當您存取 Azure AD B2C 所簽發的權杖中的宣告值時，MSAL.NET 會傳回 `preferred_username` 。 MSAL 會使用的 `Missing from the token response` 值 `preferred_username` 來維護程式庫之間的快取交互相容性。

### <a name="workarounds"></a>因應措施

#### <a name="mitigation-for-missing-tenant-id"></a>降低遺失的租使用者識別碼

建議的解決方法是使用稍早所述[原則的](#acquire-a-token-to-apply-a-policy)快取。

或者， `tid` 如果您在 Azure AD B2C 中使用[自訂原則](../../active-directory-b2c/custom-policy-get-started.md)，則可以使用宣告。 自訂原則可以使用[宣告轉換](/azure/active-directory-b2c/claims-transformation-technical-profile)，將額外的宣告傳回給您的應用程式。

#### <a name="mitigation-for-missing-from-the-token-response"></a>「權杖回應遺失」的緩和措施

其中一個選項是使用宣告， `name` 而不是 `preferred_username` 。 若要在 `name` Azure AD B2C 所簽發的識別碼權杖中包含宣告，請在設定使用者流程時選取 [**顯示名稱**]。

如需指定使用者流程所傳回之宣告的詳細資訊，請參閱[教學課程：在 Azure AD B2C 中建立使用者流程](../../active-directory-b2c/tutorial-create-user-flows.md)。

## <a name="next-steps"></a>後續步驟

下列範例會提供更多有關使用 Azure AD B2C 應用程式的 MSAL.NET 以互動方式取得權杖的詳細資料。

| 範例 | 平台 | 說明|
|------ | -------- | -----------|
|[active directory-b2c-xamarin-原生](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | Xamarin Forms 應用程式，它會使用 MSAL.NET 透過 Azure AD B2C 來驗證使用者，然後存取具有所傳回權杖的 Web API。|
