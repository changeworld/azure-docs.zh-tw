---
title: 設定呼叫 Web API 的桌面應用 - 微軟識別平臺 |蔚藍
description: 瞭解如何設定呼叫 Web API 的桌面應用程式的代碼
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f6cd9e17045fc400d32e9822cef870f1763179ab
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885257"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>呼叫 Web API 的桌面應用程式:程式碼設定

現在,您已經創建了應用程式,您將瞭解如何使用應用程式的座標配置代碼。

## <a name="microsoft-authentication-libraries"></a>微軟驗證庫

以下 Microsoft 身份驗證庫 (MSA) 支援桌面應用程式。

  Microsoft 驗證程式庫 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援在多個平臺(如 Linux、Windows 和 macOS)中構建桌面應用程式。
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 支援在多個平台中構建桌面應用程式。
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 支援在多個平台中構建桌面應用程式。
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | 支援僅在 macOS 上運行的桌面應用程式。

## <a name="public-client-application"></a>公開用戶端應用程式

從程式碼的角度來看,桌面應用程式是公共用戶端應用程式。 根據是否使用互動式身份驗證,配置將略有不同。

# <a name="net"></a>[.NET](#tab/dotnet)

您需要建置和操作MSAL.NET。 `IPublicClientApplication`

![IPublicClient 應用程式](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>完全由代碼

以下代碼使用工作或學校帳戶或個人 Microsoft 帳戶實例化公共用戶端應用程式並在 Microsoft Azure 公共雲中登錄使用者。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果要使用互動式身份驗證或設備代碼流(如前所述),請使用`.WithRedirectUri`修改器。

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>使用組態檔

以下代碼從配置物件實例化公共用戶端應用程式,該物件可以以程式設計方式填充或從配置檔中讀取。

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更詳細設定

您可以通過添加許多修改器來詳細說明應用程式構建。 例如,如果您希望應用程式是國家雲中的多租戶應用程式,如此處所示的美國政府,您可以編寫:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET還包含活動目錄聯合服務 2019 的修飾符:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後,如果要為 Azure 活動目錄 (Azure AD) B2C 租戶獲取權杖,請指定租戶,如以下代碼段所示:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>深入了解

要瞭解有關如何設定MSAL.NET桌面應用程式(如何配置)的更多:

- 有關`PublicClientApplicationBuilder`上 提供的所有修改器的清單,請參考文件[PublicClient 應用程式產生器](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。
- 有關`PublicClientApplicationOptions`中公開的所有選項的說明,請參考文件中[的 PublicClient 應用程式選項](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

### <a name="complete-example-with-configuration-options"></a>使用設定選項完成範例

想像一下.NET 核心主控台應用程式`appsettings.json`具有以下 設定檔:

```JSon
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

使用 時,使用此檔中幾乎沒有要讀取的代碼。NET 提供的設定架構:

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

現在,要建立應用程式,請編寫以下代碼:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在調用`.Build()`方法之前,可以使用對方法`.WithXXX`的 調用重寫配置,如前所述。

# <a name="java"></a>[Java](#tab/java)

下面是 MSAL Java 開發範例中用於設定範例的類別[:TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java)。

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

以下代碼使用工作或學校帳戶或個人 Microsoft 帳戶實例化公共用戶端應用程式並在 Microsoft Azure 公共雲中登錄使用者。

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

### <a name="more-elaborated-configuration"></a>更詳細設定

您可以通過添加許多修改器來詳細說明應用程式構建。 例如,如果您希望應用程式是國家雲中的多租戶應用程式,如此處所示的美國政府,您可以編寫:

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

> [!div class="nextstepaction"]
> [取得桌面應用程式的權杖](scenario-desktop-acquire-token.md)
