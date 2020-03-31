---
title: 具現化機密用戶端應用 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 .NET 的 Microsoft 身份驗證庫 （MSAL.NET） 使用配置選項具現化機密用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084727"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>使用MSAL.NET配置選項具現化機密用戶端應用程式

本文介紹如何使用 Microsoft 身份驗證庫對 .NET （MSAL.NET） 具現化[機密用戶端應用程式](msal-client-applications.md)。  應用程式使用設置檔中定義的配置選項進行具現化。

在初始化應用程式之前，首先需要[註冊](quickstart-register-app.md)它，以便應用可以與 Microsoft 標識平臺集成。 註冊後，您可能需要以下資訊（可在 Azure 門戶中找到）：

- 用戶端 ID（表示 GUID 的字串）
- 標識提供程式 URL（名為實例）和應用程式的登錄訪問群體。 這兩個參數統稱為許可權。
- 如果您只為您的組織（也稱為單租戶應用程式）編寫業務線應用程式，則租戶 ID。
- 應用程式金鑰（用戶端機密字串）或證書（X509證書2類型）（如果是機密用戶端應用）。
- 對於 Web 應用，有時對於公共用戶端應用（尤其是在應用需要使用代理時），您還將設置重定向 Uri，標識提供程式將在其中使用安全權杖聯繫回應用程式。

## <a name="configure-the-application-from-the-config-file"></a>從設定檔配置應用程式
MSAL.NET中選項的屬性的名稱與 ASP.NET 酷中的屬性`AzureADOptions`的名稱匹配，因此無需編寫任何粘附代碼。

ASP.NET核心應用程式佈建在*appsettings.json*檔中描述：

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

從MSAL.NET v3.x 開始，可以從設定檔配置機密用戶端應用程式。

在要配置和具現化應用程式的類中，需要聲明物件`ConfidentialClientApplicationOptions`。  使用`IConfigurationRoot.Bind()` [Microsoft.擴展.配置.Binder nuget 包](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)將從源讀取的配置（包括 appconfig.json 檔）綁定到應用程式選項的實例：

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

這使*appsettings.json*檔的"AzureAD"部分的內容能夠綁定到`ConfidentialClientApplicationOptions`物件的相應屬性。  接下來，生成物件`ConfidentialClientApplication`：

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>添加運行時配置
在機密用戶端應用程式中，您通常具有每個使用者的緩存。 因此，您需要獲取與使用者關聯的緩存，並通知應用程式產生器要使用它。 同樣，您可能有一個動態計算的重定向 URI。 在這種情況下，代碼如下：

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

