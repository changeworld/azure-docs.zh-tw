---
title: 將機密用戶端應用程式具現化 (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET (MSAL.NET) 的 Microsoft 驗證程式庫，以設定選項具現化機密用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 5bccc39144186b23cc7f9fedf02e5b9d84ea2ee4
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063547"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>使用 MSAL.NET 以設定選項具現化機密用戶端應用程式

本文說明如何使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) 來具現化 [機密用戶端應用程式](msal-client-applications.md) 。  應用程式會使用設定檔中定義的設定選項來具現化。

在初始化應用程式之前，您必須先 [註冊](quickstart-register-app.md) 該應用程式，讓您的應用程式可以與 Microsoft 身分識別平臺整合。 註冊之後，您可能需要下列資訊 (可在 Azure 入口網站) 中找到：

- 用戶端識別碼 (代表 GUID 的字串) 
- 身分識別提供者 URL (名為實例) ，以及您應用程式的登入物件。 這兩個參數統稱為授權單位。
- 租使用者識別碼（如果您只是針對您的組織撰寫企業營運應用程式） (也命名為單一租使用者應用程式) 。
- 應用程式密碼 (用戶端密碼字串) 或類型為 X509Certificate2) 的憑證 (（如果它是機密用戶端應用程式的話）。
- 針對 web 應用程式，有時適用于公用用戶端應用程式 (特別是當您的應用程式需要使用訊息代理程式) 時，您也會設定 redirectUri，讓身分識別提供者使用安全性權杖來與您的應用程式連線。

## <a name="configure-the-application-from-the-config-file"></a>從設定檔設定應用程式
MSAL.NET 中選項的屬性名稱符合 ASP.NET Core 中的屬性名稱 `AzureADOptions` ，因此您不需要撰寫任何的粘附程式碼。

檔案的 *appsettings.js* 中有描述 ASP.NET Core 的應用程式設定：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

從 MSAL.NET v3 開始，您可以從設定檔設定您的機密用戶端應用程式。

在您要設定並具現化應用程式的類別中，您需要宣告 `ConfidentialClientApplicationOptions` 物件。  使用 `IConfigurationRoot.Bind()`Microsoft.Extensions.Configuration 中的方法，將讀取自來源的設定（包括檔案) 上的 appconfig.js）系結至應用程式選項的實例 ([ 。](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)系結器 nuget 套件：

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

這可讓 *appsettings.json* 檔案的 "AzureAD" 區段內容系結至物件的對應屬性 `ConfidentialClientApplicationOptions` 。  接著，建立一個 `ConfidentialClientApplication` 物件：

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>新增執行時間設定
在機密用戶端應用程式中，您通常會有每位使用者的快取。 因此，您將需要取得與使用者相關聯的快取，並通知應用程式產生器您要使用它。 以同樣的方式，您可能會有動態計算的重新導向 URI。 在此情況下，程式碼如下所示：

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

