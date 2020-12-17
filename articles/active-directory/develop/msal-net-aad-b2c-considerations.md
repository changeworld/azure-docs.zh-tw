---
title: Azure AD B2C 和 MSAL.NET
titleSuffix: Microsoft identity platform
description: 搭配適用于 .NET 的 Microsoft 驗證程式庫使用 Azure AD B2C (MSAL.NET) 的考慮。
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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 15f65da83f14f43a7892d52c6a2ed4e08580d367
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614909"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 來登入具有社交身分識別的使用者

您可以使用 [Azure Active Directory B2C (Azure AD B2C) ](../../active-directory-b2c/overview.md)，使用 MSAL.NET 以社交身分識別登入使用者。 Azure AD B2C 是根據原則的概念來建立的。 在 MSAL.NET 中，指定原則會轉譯為提供授權。

- 當您具現化公用用戶端應用程式時，您必須將原則指定為授權單位的一部分。
- 當您想要套用原則時，請呼叫 `AcquireTokenInteractive` 可接受參數的覆寫 `authority` 。

本文適用于 MSAL.NET 3.x。 MSAL.NET 2.x 的詳細資訊，請參閱 GitHub 上 MSAL.NET Wiki 中 MSAL 2.x 的 [Azure AD B2C 細節](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) 。

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租使用者和原則的授權單位

Azure AD B2C 的授權單位格式為： `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` -Azure AD B2C 租使用者加上主機的名稱。 例如， *contosob2c.b2clogin.com*。
- `tenant` -功能變數名稱或 (租使用者) 識別碼 Azure AD B2C 租使用者的目錄。 例如，分別是 *contosob2c.onmicrosoft.com* 或 GUID。
- `policyName` -要套用的使用者流程或自訂原則的名稱。 例如，如 *b2c_1_susi* 的註冊/登入原則。

如需 Azure AD B2C 授權單位的詳細資訊，請參閱 [將重新導向 Url 設為 b2clogin.com](../../active-directory-b2c/b2clogin.md)。

## <a name="instantiating-the-application"></a>具現化應用程式

`WithB2CAuthority()`當您建立應用程式物件時，請呼叫以提供授權單位：

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

若要在公用用戶端應用程式中取得受 Azure AD B2C 保護之 API 的權杖，您必須搭配授權單位使用覆寫：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

在上述程式碼片段中：

- `policy` 這是包含您的 Azure AD B2C 使用者流程或自訂原則名稱的字串 (例如 `PolicySignUpSignIn`) 。
- `ParentActivityOrWindow` Android (活動) 是必要的，而且對於支援父 UI 的其他平臺（例如 Microsoft Windows 上的 windows 和 iOS 中的 UIViewController）而言，是選擇性的。 如需 UI 對話方塊的詳細資訊，請參閱 MSAL Wiki 上的 [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) 。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` 是尋找指定原則之帳戶的方法。 例如：

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

套用使用者流程或自訂原則 (例如，讓使用者編輯其設定檔或重設其密碼) 目前是藉由呼叫來完成 `AcquireTokenInteractive` 。 針對這兩個原則，您不會使用傳回的權杖/驗證結果。

## <a name="profile-edit-policies"></a>設定檔編輯原則

若要讓您的使用者使用社交身分識別登入，然後編輯其設定檔，請套用 Azure AD B2C 編輯設定檔原則。

若要這麼做，請 `AcquireTokenInteractive` 使用該原則的授權單位進行呼叫。 因為使用者已登入且有作用中的 cookie 會話，所以請使用 `Prompt.NoPrompt` 防止顯示帳戶選取對話方塊。

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

## <a name="resource-owner-password-credentials-ropc"></a>資源擁有者密碼認證 (ROPC) 

如需 ROPC 流程的詳細資訊，請參閱 [使用資源擁有者密碼認證授與登入](v2-oauth-ropc.md)。

**不建議** 使用 ROPC 流程，因為在您的應用程式中詢問使用者的密碼並不安全。 如需有關此問題的詳細資訊，請參閱 [不斷成長的密碼問題解決方案](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。

藉由在 ROPC 流程中使用使用者名稱/密碼，您會犧牲幾件事：

- 新式身分識別的核心原則：密碼可以被 fished 或重新執行，因為共用的密碼可以遭到攔截。 根據定義，ROPC 與無密碼流程不相容。
- 需要進行 MFA 的使用者將無法登入 (，因為沒有任何互動) 。
- 使用者將無法使用單一登入 (SSO) 。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 中設定 ROPC 流程

在您的 Azure AD B2C 租使用者中，建立新的使用者流程，然後選取 [ **使用 ROPC 登入** ] 以啟用使用者流程的 ROPC。 如需詳細資訊，請參閱 [設定資源擁有者密碼認證流程](../../active-directory-b2c/add-ropc-policy.md)。

`IPublicClientApplication` 包含 `AcquireTokenByUsernamePassword` 方法：

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

這個 `AcquireTokenByUsernamePassword` 方法會採用下列參數：

- 要取得存取權杖的 *範圍* 。
- 使用者 *名稱*。
- 使用者的 SecureString *密碼* 。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流程的限制

ROPC 流程 **僅適用于本機帳戶**，您的使用者已使用電子郵件地址或使用者名稱向 Azure AD B2C 註冊。 當同盟至 Azure AD B2C (Facebook、Google 等 ) 所支援的外部識別提供者時，此流程無法運作。

## <a name="google-auth-and-embedded-webview"></a>Google 驗證和內嵌的 web 視圖

如果您使用 Google 做為身分識別提供者，建議您使用系統瀏覽器，因為 Google 不允許 [來自 embedded web 檢視的驗證](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前， `login.microsoftonline.com` 是 Google 的受信任授權單位，可與內嵌的 web 程式搭配使用。 但是，不是 `b2clogin.com` Google 的受信任授權單位，因此使用者將無法進行驗證。

如果有變更，我們將會提供此 [問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) 的更新。

## <a name="token-caching-in-msalnet"></a>MSAL.NET 中的權杖快取

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知問題

MSAL.NET 支援 [權杖](/dotnet/api/microsoft.identity.client.tokencache)快取。 權杖快取金鑰是根據身分識別提供者傳回的宣告 (IdP) 。

目前，MSAL.NET 需要兩個宣告來建立權杖快取金鑰：

- `tid` (Azure AD 租使用者識別碼) 
- `preferred_username`

這兩個宣告在 Azure AD B2C 的情況下可能會遺失，因為並非所有的社交識別提供者 (Facebook、Google 和其他提供者) 將它們傳回至 Azure AD B2C 的權杖中。

這種情況的徵兆是 `Missing from the token response` 當您存取 `preferred_username` Azure AD B2C 所簽發權杖中的宣告值時，MSAL.NET 會傳回。 MSAL 會使用的 `Missing from the token response` 值 `preferred_username` 來維護程式庫之間的快取交叉相容性。

### <a name="workarounds"></a>因應措施

#### <a name="mitigation-for-missing-tenant-id"></a>緩和缺少的租使用者識別碼

建議的解決方法是使用稍早所述 [原則的](#acquire-a-token-to-apply-a-policy) 快取。

或者， `tid` 如果您要在 Azure AD B2C 中使用 [自訂原則](../../active-directory-b2c/custom-policy-get-started.md) ，則可以使用宣告。 自訂原則可以使用 [宣告轉換](../../active-directory-b2c/claims-transformation-technical-profile.md)，將其他宣告傳回給您的應用程式。

#### <a name="mitigation-for-missing-from-the-token-response"></a>「權杖回應遺失」的風險降低

其中一個選項是使用宣告， `name` 而不是 `preferred_username` 。 若要在 `name` Azure AD B2C 所簽發的識別碼權杖中包含宣告，請在設定使用者流程時選取 [ **顯示名稱** ]。

如需指定使用者流程傳回哪些宣告的詳細資訊，請參閱 [教學課程：在 Azure AD B2C 中建立使用者流程](../../active-directory-b2c/tutorial-create-user-flows.md)。

## <a name="next-steps"></a>後續步驟

下列範例提供更多有關使用 MSAL.NET 以互動方式取得權杖的詳細資料 Azure AD B2C 應用程式。

| 範例 | 平台 | 描述|
|------ | -------- | -----------|
|[active directory-b2c-xamarin-原生](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | Xamarin Forms 應用程式，它會使用 MSAL.NET 透過 Azure AD B2C 來驗證使用者，然後使用傳回的權杖來存取 web API。|
