---
title: 設定受保護的 Web API 應用 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何建構受保護的 Web API 並設定應用程式的代碼。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882466"
---
# <a name="protected-web-api-code-configuration"></a>受保護的 Web API:代碼設定

要設定受保護 Web API 的代碼,您需要瞭解:

- 什麼定義 API 作為受保護。
- 如何配置無記名令牌。
- 如何驗證權杖。

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>哪些定義ASP.NET並將核心 API ASP.NET為受保護?

與 Web 應用一樣,ASP.NET 和 ASP.NET 核心 Web API 受到保護,因為它們的控制器操作是使用 **[授權]** 屬性預固定的。 僅當使用授權標識調用 API 時,才能呼叫控制器操作。

請考慮下列問題：

- 只有應用才能調用 Web API。 API 如何知道調用它的應用的標識?
- 如果應用代表使用者調用 API,則使用者的身份是什麼?

## <a name="bearer-token"></a>持有人權杖

調用應用時在標頭中設置的承載令牌包含有關應用標識的資訊。 它還保存有關使用者的資訊,除非 Web 應用接受來自守護程序應用的服務到服務調用。

下面是一個 C# 代碼範例,該範例顯示客戶端在取得了用於 .NET 的 Microsoft 身份驗證函式庫的權杖後呼叫 API (MSAL.NET):

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
> 用戶端應用程式將承載令牌請求到*Web API 的*Microsoft 識別平台終結點。 Web API 是唯一應驗證權杖並查看它包含的聲明的應用程式。 用戶端應用絕不應嘗試在權杖中檢查聲明。
>
> 將來,Web API 可能要求對權杖進行加密。 此要求將阻止訪問可以查看訪問權杖的用戶端應用。

## <a name="jwtbearer-configuration"></a>JwtBearer 設定

本節介紹如何配置無記名令牌。

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

### <a name="code-initialization"></a>代碼初始化

當在包含 **[授權]** 屬性的控制器操作上調用應用時,ASP.NET和 ASP.NET酷睿從授權標頭的承載令牌中提取訪問權杖。 然後,訪問令牌將轉發到 JwtBearer 中間件,後者調用 .NET 的 Microsoft 標識模型擴展。

在ASP.NET核心中,此中間件在Startup.cs檔中初始化。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

此指令將中間件新增到 Web API 中:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 目前,ASP.NET核心範本創建 Azure 活動目錄 (Azure AD) Web API,這些 API 登錄到組織或任何組織內的使用者。 他們不使用個人帳戶登錄使用者。 但是,您可以通過將此代碼添加到Startup.cs來更改範本以使用 Microsoft 識別平台終結點:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

前面的代碼段是從 Microsoft 中 ASP.NET 核心 Web API 增量教學中提取的[。](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63) **AddProtectedWebApi**方法比代碼段顯示的多,是從Startup.cs調用的。

## <a name="token-validation"></a>權杖驗證

在前面的代碼段中,JwtBearer 中間件(如 Web 應用中的 OpenID Connect 中間件)根據`TokenValidationParameters`的值驗證權杖。 根據需要解密權杖,提取聲明,並驗證簽名。 然後,中間件通過檢查此數據來驗證權杖:

- 訪問群體:權杖是 Web API 的目標。
- Sub:它是為允許調用 Web API 的應用發布的。
- 頒發者:它由受信任的安全權杖服務 (STS) 頒發。
- 過期:其存留期在範圍內。
- 簽名:沒有被篡改。

也可以有特殊的驗證。 例如,可以驗證簽名金鑰(嵌入權杖時)是否受信任,並且令牌未重播。 最後,某些協定需要特定的驗證。

### <a name="validators"></a>驗證程式

驗證步驟在驗證器中捕獲,驗證器由 .NET 開源庫[的 Microsoft 標識模型擴展](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)提供。 認證器在函式庫源檔案 Microsoft 中定義[。](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

下表描述了驗證程式:

| 驗證器 | 描述 |
|---------|---------|
| **驗證受眾** | 確保權杖適用於為您驗證權杖的應用程式。 |
| **驗證發行者** | 確保權杖由受信任的 STS 頒發,這意味著它來自您信任的人。 |
| **驗證簽章者簽署金鑰** | 確保驗證權杖的應用程式信任用於對權杖進行簽名的金鑰。 有一個特殊情況,其中密鑰嵌入在令牌中。 但這種情況通常不會出現。 |
| **驗證存留期** | 確保權杖仍然或已有效。 驗證器檢查權杖的存留期是否位於**之前**指定的範圍內,並且**過期**聲明。 |
| **驗證簽章** | 確保權杖未被篡改。 |
| **驗證權碼重播** | 確保不重播權杖。 一些一次性使用協定有一個特例。 |

驗證器與**令牌驗證參數**類的屬性相關聯。 屬性從ASP.NET和ASP.NET核心配置初始化。

在大多數情況下,您不需要更改參數。 不是單個租戶的應用是例外。 這些 Web 應用接受來自任何組織或個人 Microsoft 帳戶的使用者。 在這種情況下,必須驗證頒發者。

## <a name="token-validation-in-azure-functions"></a>Azure 函數中的權杖驗證

您還可以在 Azure 函數中驗證傳入訪問權杖。 您可以在[Microsoft .NET、NodeJS](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)和[NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)[Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)中尋找此類驗證的範例。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [驗證代碼中的範圍和應用程式角色](scenario-protected-web-api-verification-scope-app-roles.md)
