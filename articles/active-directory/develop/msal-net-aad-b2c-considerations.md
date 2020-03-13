---
title: Azure AD B2C （MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解使用 Azure AD B2C 搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）時的特定考慮。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262811"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用 MSAL.NET 以社交身分識別登入使用者

您可以使用 MSAL.NET，透過[Azure Active Directory B2C （Azure AD B2C）](https://aka.ms/aadb2c)，透過社交身分識別登入使用者。 Azure AD B2C 是根據原則的概念來建立的。 在 MSAL.NET 中，指定原則會轉譯為提供授權單位。

- 當您具現化公用用戶端應用程式時，您必須指定 [授權單位] 中的原則。
- 當您想要套用原則時，您需要呼叫包含 `authority` 參數之 `AcquireTokenInteractive` 的覆寫。

此頁面適用于 MSAL 3.x。 如果您對 MSAL 2.x 有興趣，請參閱 MSAL 2.x[中的 Azure AD B2C 詳細資訊](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租使用者和原則的授權單位

要使用的授權單位是 `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`，其中：

- `azureADB2CHostname` 是 Azure AD B2C 租使用者加上主機的名稱（例如 `{your-tenant-name}.b2clogin.com`），
- `tenant` 是 Azure AD B2C 租使用者的完整名稱（例如，`{your-tenant-name}.onmicrosoft.com`）或租使用者的 GUID， 
- `policyName` 要套用的原則或使用者流程的名稱（例如「b2c_1_susi」進行註冊/登入）。

如需 Azure AD B2C 授權單位的詳細資訊，請參閱此[檔](/azure/active-directory-b2c/b2clogin)。

## <a name="instantiating-the-application"></a>將應用程式具現化

建立應用程式時，您必須提供授權單位。

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

若要取得公用用戶端應用程式中受 Azure AD B2C 保護之 API 的權杖，您必須使用具有授權的覆寫：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

成為：

- `policy` 是先前的其中一個字串（例如 `PolicySignUpSignIn`）。
- Android （活動）需要 `ParentActivityOrWindow`，對於支援父 UI 的其他平臺（例如 Windows 中的 windows 和 iOS 中的 UIViewController）則為選擇性。 如需詳細資訊，請參閱[UI 對話方塊](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` 是一種方法，可尋找給定原則的帳戶。 例如：

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

套用原則或使用者流程（例如，讓終端使用者編輯其設定檔或重設其密碼）目前是藉由呼叫 `AcquireTokenInteractive`來完成。 在這兩個原則的案例中，您不會使用傳回的權杖/驗證結果。

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>EditProfile 和 ResetPassword 原則的特殊案例

當您想要提供經驗，讓使用者使用社交身分識別登入，然後編輯其設定檔時，您會想要套用 Azure AD B2C 編輯設定檔原則。 執行此動作的方法是呼叫具有該原則之特定授權單位的 `AcquireTokenInteractive`，並將提示設定為 [`Prompt.NoPrompt`]，以防止顯示 [帳戶選取] 對話方塊（因為使用者已經登入，而且有作用中的 cookie 會話）。

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
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>具有 Azure AD B2C 的資源擁有者密碼認證（ROPC）
如需 ROPC 流程的詳細資訊，請參閱此[檔](v2-oauth-ropc.md)。

**不建議使用**此流程，因為詢問使用者密碼的應用程式並不安全。 如需此問題的詳細資訊，請參閱[這篇文章](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

藉由使用使用者名稱/密碼，您會產生許多事項：
- 新式身分識別的核心原則：密碼會取得 fished、重新執行。 因為我們有可攔截的共用密碼概念。 這與無密碼不相容。
- 需要執行 MFA 的使用者將無法登入（因為沒有互動）。
- 使用者將無法執行單一登入。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 中設定 ROPC 流程
在您的 Azure AD B2C 租使用者中，建立新的使用者流程，然後選取 [**使用 ROPC 登入**]。 這會為您的租使用者啟用 ROPC 原則。 如需詳細資訊，請參閱[設定資源擁有者密碼認證流程](/azure/active-directory-b2c/configure-ropc)。

`IPublicClientApplication` 包含方法：
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

這個方法會使用做為參數：
- 要為其要求存取權杖的*範圍*。
- 使用者*名稱*。
- 使用者的 SecureString*密碼*。

請記得使用包含 ROPC 原則的授權單位。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流程的限制
 - ROPC 流程**僅適用于本機帳戶**（您可以使用電子郵件或使用者名稱向 Azure AD B2C 註冊）。 如果與 Azure AD B2C （Facebook、Google 等）支援的任何身分識別提供者同盟，此流程就無法運作。

## <a name="google-auth-and-embedded-webview"></a>Google Auth 和 Embedded Web 視圖

如果您是使用 Google 做為身分識別提供者的 Azure AD B2C 開發人員，我們會建議您使用系統瀏覽器，因為 Google 不允許[來自內嵌網站的驗證](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前，`login.microsoftonline.com` 是 Google 的受信任授權單位。 使用此授權單位將可使用內嵌的 web 工作。 不過，使用 `b2clogin.com` 不是 Google 的受信任授權單位，因此使用者將無法進行驗證。

如果發生變更，我們將會提供此[問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)的更新。

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>在 MSAL.Net 中使用 Azure AD B2C 進行快取 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知問題

MSAL.Net 支援[權杖](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)快取。 權杖快取金鑰是以身分識別提供者傳回的宣告為基礎。 目前 MSAL.Net 需要兩個宣告來建立權杖快取金鑰：  
- `tid`，這是 Azure AD 的租使用者識別碼，而 
- `preferred_username` 

許多 Azure AD B2C 案例中都缺少這兩個宣告。 

客戶的影響是，當嘗試顯示 [使用者名稱] 欄位時，您會收到「遺漏權杖回應」作為值嗎？ 若是如此，這是因為因為社交帳戶和外部身分識別提供者（Idp）的限制，Azure AD B2C 不會在 preferred_username 的 IdToken 中傳回值。 Azure AD 會傳回 preferred_username 的值，因為它知道使用者是誰，但對於 Azure AD B2C，因為使用者可以使用本機帳戶、Facebook、Google、GitHub 等進行登入。 preferred_username 使用的 Azure AD B2C 沒有一致的值。 若要解除封鎖 MSAL 與 ADAL 的快取相容性，我們決定在處理 Azure AD B2C 帳戶時，于結束時使用「權杖回應中遺漏」，而當 IdToken 不傳回任何 preferred_username。 MSAL 必須傳回 preferred_username 的值，以維持跨程式庫的快取相容性。

### <a name="workarounds"></a>因應措施

#### <a name="mitigation-for-the-missing-tenant-id"></a>降低遺失的租使用者識別碼

建議的解決方法是使用[依原則](#acquire-a-token-to-apply-a-policy)的快取

或者，如果您使用[B2C 自訂原則](https://aka.ms/ief)，您可以使用 `tid` 宣告，因為它提供了將額外的宣告傳回給應用程式的功能。 若要深入瞭解[宣告轉換](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>「權杖回應遺失」的緩和措施
其中一個選項是使用 "name" 宣告做為慣用的使用者名稱。 此程式會在 [傳回宣告] 欄中提及于此[B2C](../../active-directory-b2c/user-flow-overview.md)檔 > 中，選擇成功編輯設定檔後，您要在授權權杖中傳回給應用程式的宣告。 例如，選取 [顯示名稱]、[郵遞區號]。

## <a name="next-steps"></a>後續步驟 

下列範例會提供更多有關使用 Azure AD B2C 應用程式的 MSAL.NET 以互動方式取得權杖的詳細資料。

| 範例 | 平台 | 描述|
|------ | -------- | -----------|
|[active directory-b2c-xamarin-原生](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS、Xamarin Android、UWP | 簡單的 Xamarin Forms 應用程式展示如何使用 MSAL.NET 透過 Azure AD B2C 來驗證使用者，並使用產生的權杖來存取 Web API。|
