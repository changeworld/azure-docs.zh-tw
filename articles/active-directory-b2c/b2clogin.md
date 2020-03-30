---
title: 將應用程式和 API 遷移到b2clogin.com
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 的重新導向 URL 中使用 b2clogin.com。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189985"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>將 Azure Active Directory B2C 的重新導向 URL 設定為 b2clogin.com

在 Azure 活動目錄 B2C （Azure AD B2C） 應用程式中為註冊和登錄設置標識提供程式時，需要指定重定向 URL。 不應再在應用程式和 API 中引用*login.microsoftonline.com。* 相反，對所有新應用程式使用*b2clogin.com，* 並將現有應用程式從*login.microsoftonline.com*遷移到*b2clogin.com*。

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>棄用login.microsoftonline.com

2019年12月4日，我們宣佈定于**2020年12月4日在**Azure AD B2C 中停用login.microsoftonline.com支援：

[Azure 活動目錄 B2C 正在棄用login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

login.microsoftonline.com的棄用將在 2020 年 12 月 4 日對所有 Azure AD B2C 租戶生效，為現有租戶提供一 （1） 年才能遷移到b2clogin.com。 2019 年 12 月 4 日之後創建的新租戶將不接受login.microsoftonline.com的請求。 b2clogin.com終結點上的所有功能都保持不變。

login.microsoftonline.com的棄用不會影響 Azure 活動目錄租戶。 只有 Azure 活動目錄 B2C 租戶受此更改的影響。

## <a name="benefits-of-b2clogincom"></a>b2clogin.com的好處

當您使用*b2clogin.com*作為重定向 URL 時：

* Microsoft 服務在 Cookie 標頭中所耗用的空間會縮小。
* 重定向 URL 不再需要包含對 Microsoft 的引用。
* 在自訂頁面中支援 JavaScript 用戶端代碼（[當前預覽版](user-flow-javascript-overview.md)）。 由於安全限制，如果使用login.microsoftonline.com，JavaScript 代碼和 HTML 表單元素將從自訂頁面*login.microsoftonline.com*中刪除。

## <a name="overview-of-required-changes"></a>所需更改概述

可能需要進行一些修改才能將應用程式遷移到*b2clogin.com：*

* 將標識提供程式應用程式中的重定向 URL 更改為引用*b2clogin.com*。
* 更新 Azure AD B2C 應用程式，以在其使用者流和權杖終結點引用中使用*b2clogin.com。*
* 更新您在使用者介面[自訂](custom-policy-ui-customization.md)的 CORS 設置中定義的任何**允許源**。

## <a name="change-identity-provider-redirect-urls"></a>更改標識提供程式重定向 URL

在創建應用程式的每個標識提供程式的網站上，將所有受信任的 URL 更改為重定向到`your-tenant-name.b2clogin.com`，而不是*login.microsoftonline.com*。

有兩種格式可用於b2clogin.com重定向 URL。 第一種好處是，通過使用租戶 ID （GUID） 代替租戶功能變數名稱，使 URL 中的任意位置沒有"Microsoft"出現：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

第二個選項以 的`your-tenant-name.onmicrosoft.com`租戶功能變數名稱的形式使用。 例如：

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

對於這兩種格式：

* 將 `{your-tenant-name}` 取代為您的 Azure AD B2C 租用戶名稱。
* 如果`/te`URL 中存在，請將其刪除。

## <a name="update-your-applications-and-apis"></a>更新應用程式和 API

啟用 Azure AD B2C 的應用程式和 API 中的代碼可能`login.microsoftonline.com`在幾個位置引用。 例如，您的代碼可能具有對使用者流和權杖終結點的引用。 將以下內容更新為引用`your-tenant-name.b2clogin.com`：

* 授權端點
* 權杖端點
* 權杖頒發者

例如，Contoso 的註冊/登錄策略的授權終結點現在為：

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

有關將基於 OWIN 的 Web 應用程式遷移到b2clogin.com的資訊，請參閱[將基於 OWIN 的 Web API 遷移到b2clogin.com](multiple-token-endpoints.md)。

有關遷移受 Azure AD B2C 保護的 Azure API 管理 API，請參閱[使用 Azure AD B2C](secure-api-management.md)[遷移到b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="validateauthority-property"></a>驗證授權屬性

如果您使用的[是MSAL.NET][msal-dotnet] v2 或更早版本，請將**驗證授權**屬性`false`設置為用戶端具現化以允許重定向到*b2clogin.com*。 MSAL.NET v3 及以上不需要此設置。

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

如果您使用的[是 JAVAScript 的 MSAL][msal-js]：

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

有關將基於 OWIN 的 Web 應用程式遷移到b2clogin.com的資訊，請參閱[將基於 OWIN 的 Web API 遷移到b2clogin.com](multiple-token-endpoints.md)。

有關遷移受 Azure AD B2C 保護的 Azure API 管理 API，請參閱[使用 Azure AD B2C](secure-api-management.md)[遷移到b2clogin.com](secure-api-management.md#migrate-to-b2clogincom)部分。

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
