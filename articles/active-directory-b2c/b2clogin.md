---
title: 將應用程式和 Api 遷移至 b2clogin.com
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 的重新導向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: aa1e6d8705cf4aed975ed0940087f243a06a9019
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/17/2020
ms.locfileid: "77372687"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>將 Azure Active Directory B2C 的重新導向 URL 設定為 b2clogin.com

當您在 Azure Active Directory B2C （Azure AD B2C）應用程式中設定註冊和登入的身分識別提供者時，您需要指定重新導向 URL。 您不應該再于應用程式和 Api 中參考*login.microsoftonline.com* 。 相反地，請將*b2clogin.com*用於所有新的應用程式，並將現有的應用程式從*login.microsoftonline.com*遷移至*b2clogin.com*。

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>取代 login.microsoftonline.com

在2019年12月04日，我們已于**2020 年12月**的 Azure AD B2C 中宣佈排程淘汰 login.microsoftonline.com 支援：

[Azure Active Directory B2C 是淘汰 login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Login.microsoftonline.com 的淘汰會在2020年12月4日 Azure AD B2C 生效，讓現有的租使用者一（1）年遷移至 b2clogin.com。 2019年12月4日之後建立的新租使用者將不會接受來自 login.microsoftonline.com 的要求。 B2clogin.com 端點上的所有功能都保持不變。

取代 login.microsoftonline.com 並不會影響租使用者 Azure Active Directory。 只有 Azure Active Directory B2C 的租使用者會受到這項變更的影響。

## <a name="benefits-of-b2clogincom"></a>B2clogin.com 的優點

當您使用*b2clogin.com*作為重新導向 URL 時：

* Microsoft 服務在 Cookie 標頭中所耗用的空間會縮小。
* 重新導向 Url 不再需要包含 Microsoft 的參考。
* 自訂頁面支援 JavaScript 用戶端程式代碼（目前處於[預覽](user-flow-javascript-overview.md)狀態）。 基於安全性限制，如果您使用*login.microsoftonline.com*，就會從自訂頁面中移除 JavaScript 程式碼和 HTML 表單元素。

## <a name="overview-of-required-changes"></a>必要變更的總覽

將您的應用程式遷移至*b2clogin.com*時，您可能需要進行幾項修改：

* 將身分識別提供者應用程式中的 [重新導向 URL] 變更為參考*b2clogin.com*。
* 更新您的 Azure AD B2C 應用程式，以在其使用者流程和權杖端點參考中使用*b2clogin.com* 。
* 更新您在 CORS 設定中為[使用者介面自訂](custom-policy-ui-customization.md)所定義的任何**允許原始來源**。

## <a name="change-identity-provider-redirect-urls"></a>變更識別提供者重新導向 Url

在您已建立應用程式的每個識別提供者網站上，變更所有受信任的 Url 以重新導向至 `your-tenant-name.b2clogin.com`，而不是*login.microsoftonline.com*。

您可以使用兩種格式來 b2clogin.com 重新導向 Url。 第一種方式是使用租使用者識別碼（GUID）取代您的租使用者功能變數名稱，讓「Microsoft」不會出現在 URL 中的任何位置：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

第二個選項會以 `your-tenant-name.onmicrosoft.com`的形式使用您的租使用者功能變數名稱。 例如：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

針對這兩種格式：

* 將 `{your-tenant-name}` 取代為您的 Azure AD B2C 租用戶名稱。
* 如果 URL 中存在 `/te`，請將其移除。

## <a name="update-your-applications-and-apis"></a>更新您的應用程式和 Api

已啟用 Azure AD B2C 的應用程式和 Api 中的程式碼可能會在數個地方參考 `login.microsoftonline.com`。 例如，您的程式碼可能會參考使用者流程和權杖端點。 更新下列內容，改為參考 `your-tenant-name.b2clogin.com`：

* 授權端點
* 權杖端點
* 權杖簽發者

例如，Contoso 的註冊/登入原則的授權單位端點現在會是：

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

如需將以 OWIN 為基礎的 web 應用程式遷移至 b2clogin.com 的詳細資訊，請參閱將[OWIN 型 Web API 遷移至 b2clogin.com](multiple-token-endpoints.md)。

若要遷移受 Azure AD B2C 保護的 Azure API 管理 Api，請參閱[使用 Azure AD B2C 保護 AZURE Api 管理 api](secure-api-management.md)的「[遷移至 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) 」一節。

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>ValidateAuthority 屬性

如果您使用的是[MSAL.NET][msal-dotnet] v2 或更早版本，請將用戶端具現化上的**ValidateAuthority**屬性設定為 `false`，以允許重新導向至*b2clogin.com*。 MSAL.NET v3 和更新版本不需要此設定。

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

如果您是使用[JavaScript 的 MSAL][msal-js]：

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>後續步驟

如需將以 OWIN 為基礎的 web 應用程式遷移至 b2clogin.com 的詳細資訊，請參閱將[OWIN 型 Web API 遷移至 b2clogin.com](multiple-token-endpoints.md)。

若要遷移受 Azure AD B2C 保護的 Azure API 管理 Api，請參閱[使用 Azure AD B2C 保護 AZURE Api 管理 api](secure-api-management.md)的「[遷移至 b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) 」一節。

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
