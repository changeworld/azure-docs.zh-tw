---
title: Azure AD B2C （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 在將 Azure AD B2C 與 .NET （MSAL.NET） 的 Microsoft 身份驗證庫一起使用時，瞭解具體注意事項。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262811"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>使用MSAL.NET使用具有社交身份的使用者進行登錄

您可以使用MSAL.NET使用[Azure 活動目錄 B2C （Azure AD B2C）](https://aka.ms/aadb2c)使用具有社交標識的使用者登錄。 Azure AD B2C 是圍繞策略的概念構建的。 在MSAL.NET中，指定策略將轉換為提供許可權。

- 具現化公共用戶端應用程式時，需要指定許可權的策略。
- 如果要應用策略，需要調用包含參數`AcquireTokenInteractive``authority`的重寫。

此頁面適用于 MSAL 3.x。 如果您對 MSAL 2.x 感興趣，請參閱[MSAL 2.x 中的 Azure AD B2C 細節](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x)。

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Azure AD B2C 租戶和策略的授權

使用許可權是`https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`：

- `azureADB2CHostname`是 Azure AD B2C 租戶加上主機的名稱（例如`{your-tenant-name}.b2clogin.com`），
- `tenant`是 Azure AD B2C 租戶（例如`{your-tenant-name}.onmicrosoft.com`）或租戶的 GUID 的全名， 
- `policyName`要申請的策略或使用者流的名稱（例如註冊/登錄的"b2c_1_susi"）。

有關 Azure AD B2C 許可權的詳細資訊，請參閱[本文檔](/azure/active-directory-b2c/b2clogin)。

## <a name="instantiating-the-application"></a>具現化應用程式

構建應用程式時，您需要提供許可權。

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

## <a name="acquire-a-token-to-apply-a-policy"></a>獲取權杖以應用策略

在公共用戶端應用程式中獲取 Azure AD B2C 受保護 API 的權杖需要您將重寫與頒發機構一起使用：

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

成為：

- `policy`是前面的字串之一（例如`PolicySignUpSignIn`）。
- `ParentActivityOrWindow`Android（活動）是必需的，對於支援父 UI 的其他平臺（如 Windows 中的視窗和 iOS 中的 UIViewController））是可選的。 [請參閱 UI 對話方塊 的詳細資訊](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow)。
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`是查找給定策略的帳戶的方法。 例如：

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

應用策略或使用者流（例如，讓最終使用者編輯其設定檔或重置其密碼）當前通過調用`AcquireTokenInteractive`來完成。 在這兩個策略的情況下，不使用返回的權杖/身份驗證結果。

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>編輯設定檔和重置密碼原則的特殊情況

如果要提供最終使用者使用社交標識登錄然後編輯其設定檔的體驗，則要應用 Azure AD B2C 編輯設定檔策略。 執行此操作的方法是調用`AcquireTokenInteractive`該策略的特定許可權，並設置一個提示集，`Prompt.NoPrompt`以防止顯示帳戶選擇對話方塊（因為使用者已登錄並且具有活動 Cookie 會話）。

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>使用 Azure AD B2C 的資源擁有者密碼憑據 （ROPC）
有關 ROPC 流的更多詳細資訊，請參閱[本文檔](v2-oauth-ropc.md)。

**不建議**使用此流，因為應用程式向使用者詢問其密碼不安全。 有關此問題的詳細資訊，請參閱[本文](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)。 

通過使用使用者名/密碼，您放棄了許多內容：
- 現代身份的核心信條：密碼被魚，重播。 因為我們有一個可以截獲的共用秘密的概念。 這與無密碼不相容。
- 需要執行 MFA 的使用者將無法登錄（因為沒有交互）。
- 使用者將無法執行單一登入。

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>在 Azure AD B2C 中配置 ROPC 流
在 Azure AD B2C 租戶中，創建新使用者流，然後**選擇使用 ROPC 登錄**。 這將為租戶啟用 ROPC 策略。 有關詳細資訊[，請參閱配置資源擁有者密碼憑據流](/azure/active-directory-b2c/configure-ropc)。

`IPublicClientApplication`包含一個方法：
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

此方法用作參數：
- 要*為其*請求訪問權杖的範圍。
- *使用者名*。
- 使用者的安全字串*密碼*。

請記住使用包含 ROPC 策略的許可權。

### <a name="limitations-of-the-ropc-flow"></a>ROPC 流的限制
 - ROPC 流**僅適用于本地帳戶**（使用電子郵件或使用者名向 Azure AD B2C 註冊）。 如果與 Azure AD B2C 支援的任何標識提供程式（Facebook、Google 等）聯合，則此流不起作用。

## <a name="google-auth-and-embedded-webview"></a>谷歌Auth和嵌入式網頁視圖

如果您是使用 Google 作為身份供應商的 Azure AD B2C 開發人員，我們會重新命令您使用系統瀏覽器，因為 Google 不允許[從嵌入式網頁進行身份驗證](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)。 目前，`login.microsoftonline.com`谷歌是一個值得信賴的機構。 使用此許可權將處理嵌入式 Webview。 但是，`b2clogin.com`使用不是 Google 的可信許可權，因此使用者將無法進行身份驗證。

如果情況發生變化，我們將提供[此問題](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)的更新。

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>在MSAL.Net中使用 Azure AD B2C 進行緩存 

### <a name="known-issue-with-azure-ad-b2c"></a>Azure AD B2C 的已知問題

MSAL.Net支援[權杖緩存](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet)。 權杖緩存鍵基於標識提供程式返回的聲明。 當前MSAL.Net需要兩個聲明來生成權杖緩存金鑰：  
- `tid`這是 Azure AD 租戶 ID，以及 
- `preferred_username` 

在許多 Azure AD B2C 方案中，這兩種聲明都丟失。 

客戶影響是，在嘗試顯示使用者名欄位時，您是否得到"權杖回應中缺少"作為值？ 如果是這樣，這是因為 Azure AD B2C 不會返回 idToken 中的值preferred_username因為社交帳戶和外部標識提供程式 （IdP） 的限制。 Azure AD 返回preferred_username的值，因為它知道使用者是誰，但對於 Azure AD B2C，因為使用者可以使用本地帳戶、Facebook、Google、GitHub 等登錄，因此 Azure AD B2C 沒有用於preferred_username的一致值。 為了解除阻止 MSAL 與 ADAL 推出緩存相容性，我們決定在處理 Azure AD B2C 帳戶時，在 IdToken 返回preferred_username時，在末尾使用"從權杖回應中丟失"。 MSAL 必須返回preferred_username的值，以保持跨庫的緩存相容性。

### <a name="workarounds"></a>因應措施

#### <a name="mitigation-for-the-missing-tenant-id"></a>缺少租戶 ID 的緩解

建議的解決方法是使用[按策略進行緩存](#acquire-a-token-to-apply-a-policy)

或者，如果您使用的是`tid`[B2C 自訂策略](https://aka.ms/ief)，則可以使用 聲明，因為它提供了向應用程式返回其他聲明的功能。 瞭解有關[索賠轉換](/azure/active-directory-b2c/claims-transformation-technical-profile)的更多

#### <a name="mitigation-for-missing-from-the-token-response"></a>緩解"從權杖回應中丟失"
一個選項是使用"名稱"聲明作為首選使用者名。 此過程在此[B2C 文檔](../../active-directory-b2c/user-flow-overview.md)->"在 Return 聲明列中，選擇要在成功設定檔編輯體驗後發回應用程式的授權權杖中返回的聲明。 例如，選擇"顯示名稱"，郵遞區號。

## <a name="next-steps"></a>後續步驟 

有關與 Azure AD B2C 應用程式MSAL.NET交互獲取權杖的更多詳細資訊，請于以下示例中提供。

| 範例 | Platform | 描述|
|------ | -------- | -----------|
|[活動目錄-b2c-xamarin-本機](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | 夏馬林 iOS， 夏馬林安卓， UWP | 一個簡單的 Xamarin 表單應用，演示如何使用MSAL.NET通過 Azure AD B2C 對使用者進行身份驗證，並使用生成的權杖訪問 Web API。|
