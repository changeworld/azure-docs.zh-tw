---
title: 設定受保護的 Web API 應用程式 |Azure
titleSuffix: Microsoft identity platform
description: 了解如何建立受保護的 Web API，並設定應用程式的程式碼。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 992c29cb8380cf6acbe970b2fd5e958b6b2b33dc
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026708"
---
# <a name="protected-web-api-code-configuration"></a>受保護的 Web API：程式碼設定

若要設定受保護 Web API 的程式碼，您必須瞭解：

- 哪些內容會將 Api 定義為受保護。
- 如何設定持有人權杖。
- 如何驗證權杖。

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>哪些專案會將 ASP.NET 和 ASP.NET Core Api 定義為受保護？

就像 web 應用程式一樣，ASP.NET 和 ASP.NET Core web Api 會受到保護，因為其控制器動作前面會加上 **[授權]** 屬性。 只有在使用授權的身分識別呼叫 API 時，才能呼叫控制器動作。

請考量下列問題：

- 只有應用程式可以呼叫 Web API。 API 如何得知呼叫它的應用程式的身分識別？
- 如果應用程式代表使用者呼叫 API，使用者的身分識別會是什麼？

## <a name="bearer-token"></a>持有人權杖

呼叫應用程式時，標頭中所設定的持有人權杖會保存應用程式身分識別的相關資訊。 它也會保存使用者的相關資訊，除非 web 應用程式接受來自 daemon 應用程式的服務對服務呼叫。

以下是一個 c # 程式碼範例，它會顯示用戶端在取得權杖並搭配適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）後呼叫 API：

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> 用戶端應用程式會向 Microsoft 身分識別平臺端點要求*Web API*的持有人權杖。 Web API 是唯一應驗證權杖並查看其所包含之宣告的應用程式。 用戶端應用程式應該永遠不會嘗試檢查權杖中的宣告。
>
> 未來，Web API 可能需要加密權杖。 這項需求會防止可查看存取權杖的用戶端應用程式存取。

## <a name="jwtbearer-configuration"></a>Microsoft.aspnetcore.authentication.jwtbearer 設定

本節說明如何設定持有人權杖。

### <a name="config-file"></a>組態檔

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>您在 Web API 使用自訂應用程式識別碼 URI 的情況

如果您已接受應用程式註冊入口網站所提議的應用程式識別碼 URI，則不需要指定物件（請參閱[應用程式識別碼 uri 和範圍](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)）。 否則，您應該加入 `Audience` 屬性，其值為您 Web API 的應用程式識別碼 URI。

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>程式碼初始化

在保存 **[授權]** 屬性的控制器動作上呼叫應用程式時，ASP.NET 和 ASP.NET Core 會從授權標頭的持有人權杖中解壓縮存取權杖。 存取權杖接著會轉送到 Microsoft.aspnetcore.authentication.jwtbearer 中介軟體，這會呼叫適用于 .NET 的 Microsoft Microsoft.identitymodel 延伸模組。

#### <a name="using-microsoftidentityweb-templates"></a>使用 Microsoft. Identity. Web 範本

您可以使用 Microsoft. Identity. Web 專案範本從頭建立 Web API。 如需詳細資訊，請參閱[Microsoft. Identity. web-WEB API 專案範本](https://aka.ms/ms-id-web/webapi-project-templates)

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>從現有的 ASP.NET Core 3.1 應用程式開始

今天，ASP.NET Core 3.1 會使用 AspNetCore. AzureAD. UI 程式庫。 中介軟體會在 Startup.cs 檔案中初始化。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

此指示會將中介軟體新增至 Web API：

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 目前，ASP.NET Core 範本會建立 Azure Active Directory （Azure AD） web Api，以便在您的組織或任何組織內登入使用者。 他們不會以個人帳戶登入使用者。 不過，您可以使用[microsoft 身分識別](https://www.nuget.org/packages/Microsoft.Identity.Web)，將範本變更為使用 microsoft 身分識別平臺端點，其可做為 NuGet 套件，取代*Startup.cs*中的程式碼：

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

> [!NOTE]
> 如果您使用的是 Microsoft. Identity. Web，但未在 `Audience` *appsettings.js*中設定，則會使用下列內容：
> -  `$"{ClientId}"`如果您已將「[存取權杖已接受」版本](scenario-protected-web-api-app-registration.md#accepted-token-version)設定為 `2` ，或 Azure AD B2C web api。
> - `$"api://{ClientId}`在所有其他情況下（適用于 v1.0[存取權杖](access-tokens.md)）。
> 如需詳細資訊，請參閱 Microsoft web.config[原始程式碼](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)。

上述程式碼片段會從[ASP.NET Core Web API 累加式教學](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28)課程中解壓縮。 **AddMicrosoftWebApiAuthentication**的詳細資料可在[Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiServiceCollectionExtensions.cs#L27)中取得。 這個方法會呼叫[AddMicrosoftWebAPI](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiAuthenticationBuilderExtensions.cs#L58)，它本身會指示中介軟體如何驗證權杖。 如需詳細資訊，請參閱其[原始程式碼](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/WebApiExtensions/WebApiAuthenticationBuilderExtensions.cs#L104-L122)。

## <a name="token-validation"></a>權杖驗證

在先前的程式碼片段中，Microsoft.aspnetcore.authentication.jwtbearer 中介軟體（例如 web 應用程式中的 OpenID Connect 中介軟體）會根據的值來驗證權杖 `TokenValidationParameters` 。 權杖會視需要解密，宣告會解壓縮，並驗證簽章。 然後中介軟體會藉由檢查此資料來驗證權杖：

- 物件：權杖是以 Web API 為目標。
- Sub：它是針對允許呼叫 Web API 的應用程式發出的。
- 簽發者：它是由信任的 Security Token Service （STS）所發行。
- 到期：其存留期在範圍內。
- 簽章：不會遭到篡改。

也可以進行特殊驗證。 例如，當內嵌在權杖中的簽章金鑰是受信任的，而且該權杖並未重新執行時，就可以進行驗證。 最後，某些通訊協定需要特定驗證。

### <a name="validators"></a>驗證程式

驗證步驟會在[Microsoft Microsoft.identitymodel Extensions for .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)開放原始碼程式庫所提供的驗證程式中加以捕捉。 驗證程式是在程式庫原始程式檔[（microsoft.identitymodel](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)）中定義。

下表描述驗證程式：

| 驗證程式 | 描述 |
|---------|---------|
| **ValidateAudience** | 確保權杖適用于為您驗證權杖的應用程式。 |
| **ValidateIssuer** | 確保權杖是由信任的 STS 所發行，這表示它是來自您信任的人。 |
| **ValidateIssuerSigningKey** | 確保驗證權杖的應用程式信任用來簽署權杖的金鑰。 有一個特殊的案例，即金鑰內嵌在權杖中。 但這種情況通常不會發生。 |
| **ValidateLifetime** | 確保權杖仍然有效或已生效。 驗證程式會檢查權杖的存留期是否在**notbefore**和**expires**宣告所指定的範圍內。 |
| **ValidateSignature** | 確保 token 尚未遭到篡改。 |
| **ValidateTokenReplay** | 確保不會重新執行 token。 有些 onetime 使用的通訊協定有特殊的案例。 |

#### <a name="customizing-token-validation"></a>自訂權杖驗證

驗證程式與**TokenValidationParameters**類別的屬性相關聯。 這些屬性會從 ASP.NET 初始化，並 ASP.NET Core 設定。

在大部分的情況下，您不需要變更參數。 不是單一租使用者的應用程式是例外狀況。 這些 web 應用程式會接受來自任何組織或個人 Microsoft 帳戶的使用者。 在此情況下，必須驗證簽發者。 Microsoft Identity. Web 也會負責簽發者驗證。 如需詳細資訊，請參閱 Microsoft [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)。

在 ASP.NET Core 中，如果您想要自訂權杖驗證參數，請在*Startup.cs*中使用下列程式碼片段：

```c#
services.AddMicrosoftWebApiAuthentication(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  }
});
```

針對 ASP.NET MVC，下列程式碼範例示範如何執行自訂權杖驗證：

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Azure Functions 中的權杖驗證

您也可以在 Azure Functions 中驗證傳入的存取權杖。 您可以在 GitHub 上的下列程式碼範例中找到這類驗證的範例：

- .NET： [Azure-範例/ms-身分識別-dotnet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js： [Azure-範例/ms-身分識別-nodejs-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python： [Azure-範例/ms-身分識別-python-webapi-azurefunctions）](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [在程式碼中驗證範圍和應用程式角色](scenario-protected-web-api-verification-scope-app-roles.md)
