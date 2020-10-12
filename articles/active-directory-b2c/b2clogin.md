---
title: 將應用程式和 Api 遷移至 b2clogin.com
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 的重新導向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 53d41b5024b29a8c6c394d65a3ce36f8bb878fc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90524975"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>將 Azure Active Directory B2C 的重新導向 URL 設定為 b2clogin.com

當您在 Azure Active Directory B2C (Azure AD B2C) 應用程式中設定註冊和登入的身分識別提供者時，您需要指定重新導向 URL。 您應該不會再參考應用程式和 Api 中的 *login.microsoftonline.com* ，以使用 Azure AD B2C 來驗證使用者。 相反地，請將 *b2clogin.com* 用於所有新的應用程式，並將現有的應用程式從 *login.microsoftonline.com* 遷移至 *b2clogin.com*。

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>淘汰 login.microsoftonline.com

在2019年12月12日，我們已在 Azure AD B2C 于 **2020 年12月 12**日宣佈排程淘汰 login.microsoftonline.com 支援：

[Azure Active Directory B2C 為淘汰 login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Login.microsoftonline.com 的淘汰將于2020年12月的所有 Azure AD B2C 租使用者生效，提供現有的租使用者一 (1) 年來遷移至 b2clogin.com。 在2019年12月4日之後建立的新租使用者將不會接受來自 login.microsoftonline.com 的要求。 B2clogin.com 端點上的所有功能都保持不變。

Login.microsoftonline.com 的取代不會影響租使用者 Azure Active Directory。 只有 Azure Active Directory B2C 租使用者會受到這項變更的影響。

## <a name="what-endpoints-does-this-apply-to"></a>適用的端點
轉換至 b2clogin.com 僅適用于使用 Azure AD B2C 原則 (使用者流程或自訂原則) 驗證使用者的驗證端點。 這些端點都有 `<policy-name>` 參數，可指定 Azure AD B2C 應使用的原則。 [深入瞭解 Azure AD B2C 原則](technical-overview.md#identity-experiences-user-flows-or-custom-policies)。 

這些端點看起來可能像這樣：
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

或者， `<policy-name>` 可能會以查詢參數的形式傳遞：
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> 使用 ' policy ' 參數的端點必須更新，以及身分 [識別提供者重新導向 url](#change-identity-provider-redirect-urls)。

有些 Azure AD B2C 客戶使用 Azure AD 企業租使用者的共用功能，例如 OAuth 2.0 用戶端認證授與流程。 這些功能是使用 Azure AD 的 login.microsoftonline.com 端點來存取， *其中不包含原則參數*。 __這些端點不會受到影響__。

## <a name="benefits-of-b2clogincom"></a>B2clogin.com 的優點

當您使用 *b2clogin.com* 做為重新導向 URL 時：

* Microsoft 服務在 Cookie 標頭中所耗用的空間會縮小。
* 重新導向 Url 不再需要包含 Microsoft 的參考。
* 支援 JavaScript 用戶端程式代碼 (目前在自訂頁面的 [預覽](user-flow-javascript-overview.md)) 。 由於安全性限制，如果您使用 *login.microsoftonline.com*，則會從自訂頁面移除 JavaScript 程式碼和 HTML 表單元素。

## <a name="overview-of-required-changes"></a>必要變更的總覽

將您的應用程式遷移至 *b2clogin.com*時，您可能需要進行幾項修改：

* 將身分識別提供者應用程式中的重新導向 URL 變更為參考 *b2clogin.com*。
* 更新您的 Azure AD B2C 應用程式，以在其使用者流程和權杖端點參考中使用 *b2clogin.com* 。 這可能包括更新您使用的驗證程式庫，例如 Microsoft 驗證程式庫 (MSAL) 。
* 更新您在 CORS 設定中為[使用者介面自訂](custom-policy-ui-customization.md)所定義的任何**允許來源**。

舊的端點看起來可能像這樣：
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

對應的更新端點看起來會像這樣：
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>變更身分識別提供者重新導向 Url

在您已建立應用程式的每個身分識別提供者網站上，將所有信任的 Url 變更為重新導向， `your-tenant-name.b2clogin.com` 而不是 *login.microsoftonline.com*。

您可以使用兩種格式來 b2clogin.com 重新導向 Url。 第一個方法是使用租使用者識別碼 (GUID) 來取代您的租使用者功能變數名稱，以提供不具有 "Microsoft" 的優點：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

第二個選項是以的形式使用您的租使用者功能變數名稱 `your-tenant-name.onmicrosoft.com` 。 例如：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

適用于這兩種格式：

* 將 `{your-tenant-name}` 取代為您的 Azure AD B2C 租用戶名稱。
* `/te`如果 URL 中有的話，請移除。

## <a name="update-your-applications-and-apis"></a>更新您的應用程式和 Api

您 Azure AD B2C 啟用的應用程式和 Api 中的程式碼可能會 `login.microsoftonline.com` 在數個地方參考。 例如，您的程式碼可能會參考使用者流程和權杖端點。 更新下列內容以改為參考 `your-tenant-name.b2clogin.com` ：

* 授權端點
* 權杖端點
* 權杖簽發者

例如，Contoso 的註冊/登入原則的授權單位端點現在會是：

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

如需將以 OWIN 為基礎的 web 應用程式遷移至 b2clogin.com 的詳細資訊，請參閱 [將 OWIN 為基礎的 WEB API 遷移至 b2clogin.com](multiple-token-endpoints.md)。

如需遷移受 Azure AD B2C 保護的 Azure API 管理 Api，請參閱[使用 Azure AD B2C 保護 AZURE api 管理 API](secure-api-management.md)的「[遷移至 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) 」一節。

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET ValidateAuthority 屬性

如果您使用的是 [MSAL.NET][msal-dotnet] v2 或更早版本，請在用戶端具現化上將 **ValidateAuthority** 屬性設定為， `false` 以允許重新導向至 *b2clogin.com*。 MSAL.NET v3 和更新版本不需要將此值設定為 `false` 。

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL for JavaScript validateAuthority 屬性

如果您是使用 [MSAL For JavaScript][msal-js] v 1.2.2 或更早版本，請將 **validateAuthority** 屬性設定為 `false` 。

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

如果您將設定為 `validateAuthority: true` MSAL.js 1.3.0 + (預設) ，您也必須使用下列內容指定有效的權杖簽發者 `knownAuthorities` ：

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>後續步驟

如需將以 OWIN 為基礎的 web 應用程式遷移至 b2clogin.com 的詳細資訊，請參閱 [將 OWIN 為基礎的 WEB API 遷移至 b2clogin.com](multiple-token-endpoints.md)。

如需遷移受 Azure AD B2C 保護的 Azure API 管理 Api，請參閱[使用 Azure AD B2C 保護 AZURE api 管理 API](secure-api-management.md)的「[遷移至 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) 」一節。

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
