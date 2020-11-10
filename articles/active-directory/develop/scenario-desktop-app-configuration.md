---
title: 設定呼叫 web Api 的桌面應用程式-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何設定呼叫 web Api 的傳統型應用程式的程式碼
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 27c9f2ab8b02c96d016fea66acd2b3840d79d09d
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444112"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>呼叫 web Api 的桌面應用程式：程式碼設定

現在您已建立應用程式，您將瞭解如何使用應用程式的座標來設定程式碼。

## <a name="microsoft-authentication-libraries"></a>Microsoft 驗證程式庫

下列 Microsoft 驗證程式庫 (MSALs) 支援桌面應用程式。

  Microsoft 驗證程式庫 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援在多個平臺（例如 Linux、Windows 和 macOS）中建立桌面應用程式。
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 支援在多個平臺中建立桌面應用程式。
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 支援在多個平臺中建立桌面應用程式。
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | 僅支援在 macOS 上執行的桌面應用程式。

## <a name="public-client-application"></a>公用用戶端應用程式

從程式碼的觀點來看，桌面應用程式是公用用戶端應用程式。 根據您是否使用互動式驗證，設定會有一點不同。

# <a name="net"></a>[.NET](#tab/dotnet)

您必須建立和操作 MSAL.NET `IPublicClientApplication` 。

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>專用的程式碼

下列程式碼會具現化公用用戶端應用程式，並使用工作或學校帳戶或個人 Microsoft 帳戶登入 Microsoft Azure 公用雲端中的使用者。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果您想要使用互動式驗證或裝置程式碼流程（如先前所示），請使用 `.WithRedirectUri` 修飾詞。

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>使用組態檔

下列程式碼會從設定物件具現化公用用戶端應用程式，該物件可能會以程式設計方式填入或從設定檔讀取。

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更詳細的設定

您可以藉由新增一些修飾詞來詳細地建立應用程式。 比方說，如果您想要讓應用程式成為全國雲端中的多租使用者應用程式，例如此處顯示的美國政府，您可以撰寫：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET 也包含 Active Directory 同盟服務2019的修飾詞：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果您想要取得 Azure Active Directory (Azure AD) B2C 租使用者的權杖，請指定您的租使用者，如下列程式碼片段所示：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>深入了解

若要深入瞭解如何設定 MSAL.NET 桌面應用程式：

- 如需所有可用的修飾詞清單 `PublicClientApplicationBuilder` ，請參閱參考檔 [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。
- 如需中所公開之所有選項的說明 `PublicClientApplicationOptions` ，請參閱參考檔中的 [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) 。

### <a name="complete-example-with-configuration-options"></a>具有設定選項的完整範例

想像一下具有下列設定檔的 .NET Core 主控台應用程式 `appsettings.json` ：

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

您有少量的程式碼可以使用來讀取此檔案。NET 提供的設定架構：

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

現在，若要建立您的應用程式，請撰寫下列程式碼：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在呼叫 `.Build()` 方法之前，您可以使用方法的呼叫來覆寫您的設定 `.WithXXX` ，如先前所示。

# <a name="java"></a>[Java](#tab/java)

以下是在 MSAL JAVA 開發範例中用來設定範例的類別： [>testdata](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)。

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

下列程式碼會具現化公用用戶端應用程式，並使用工作或學校帳戶或個人 Microsoft 帳戶登入 Microsoft Azure 公用雲端中的使用者。

### <a name="quick-configuration"></a>快速設定

Objective-C：

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift：
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>更詳細的設定

您可以藉由新增一些修飾詞來詳細地建立應用程式。 比方說，如果您想要讓應用程式成為全國雲端中的多租使用者應用程式，例如此處顯示的美國政府，您可以撰寫：

Objective-C：

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift：

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>後續步驟

移至本案例的下一篇文章，取得傳統型 [應用程式的權杖](scenario-desktop-acquire-token.md)。