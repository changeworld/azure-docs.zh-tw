---
title: 配置調用 Web API 的桌面應用 - 微軟識別平臺 |蔚藍
description: 瞭解如何配置調用 Web API 的桌面應用的代碼
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 2ba69e6447c686230412c33e74196c4bb594e0de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77611822"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>調用 Web API 的桌面應用：代碼配置

現在，您已經創建了應用程式，您將瞭解如何使用應用程式的座標配置代碼。

## <a name="microsoft-authentication-libraries"></a>微軟身份驗證庫

以下 Microsoft 身份驗證庫 （MSA） 支援桌面應用程式。

  Microsoft 驗證程式庫 | 描述
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | 支援在多個平臺（如 Linux、Windows 和 macOS）中構建桌面應用程式。
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | 支援在多個平臺中構建桌面應用程式。
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | 支援在多個平臺中構建桌面應用程式。
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | 支援僅在 macOS 上運行的桌面應用程式。

## <a name="public-client-application"></a>公共用戶端應用程式

從代碼的角度來看，桌面應用程式是公共用戶端應用程式。 根據是否使用互動式身份驗證，配置將略有不同。

# <a name="net"></a>[.NET](#tab/dotnet)

您需要構建和操作MSAL.NET。 `IPublicClientApplication`

![IPublicClient應用程式](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>完全由代碼

以下代碼使用工作或學校帳戶或個人 Microsoft 帳戶具現化公共用戶端應用程式並在 Microsoft Azure 公共雲中登錄使用者。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

如果要使用互動式身份驗證或設備代碼流（如前所述），請使用`.WithRedirectUri`修改器。

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>使用組態檔

以下代碼從設定物件具現化公共用戶端應用程式，該物件可以以程式設計方式填充或從設定檔中讀取。

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>更詳細配置

您可以通過添加許多修改器來詳細說明應用程式構建。 例如，如果您希望應用程式是國家雲中的多租戶應用程式，如此處所示的美國政府，您可以編寫：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET還包含活動目錄聯合服務 2019 的修飾符：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果要為 Azure 活動目錄 （Azure AD） B2C 租戶獲取權杖，請指定租戶，如以下程式碼片段所示：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>深入了解

要瞭解有關如何配置MSAL.NET桌面應用程式（如何配置）的更多：

- 有關 上`PublicClientApplicationBuilder`提供的所有修改器的清單，請參閱參考文檔[PublicClient應用程式產生器](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)。
- 有關 中`PublicClientApplicationOptions`公開的所有選項的說明，請參閱參考文檔中[的 PublicClient 應用程式選項](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)。

### <a name="complete-example-with-configuration-options"></a>使用配置選項完成示例

想像一下.NET 核心主控台應用程式具有以下`appsettings.json`設定檔：

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

使用 時，使用此檔中幾乎沒有要讀取的代碼。NET 提供的組態架構：

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

現在，要創建應用程式，請編寫以下代碼：

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

在調用`.Build()`方法之前，可以使用對方法的`.WithXXX`調用重寫配置，如前所述。

# <a name="java"></a>[JAVA](#tab/java)

下面是 MSAL JAVA 開發示例中用於配置示例的類[：TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java)。

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

以下代碼使用工作或學校帳戶或個人 Microsoft 帳戶具現化公共用戶端應用程式並在 Microsoft Azure 公共雲中登錄使用者。

### <a name="quick-configuration"></a>快速配置

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

### <a name="more-elaborated-configuration"></a>更詳細配置

您可以通過添加許多修改器來詳細說明應用程式構建。 例如，如果您希望應用程式是國家雲中的多租戶應用程式，如此處所示的美國政府，您可以編寫：

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
> [獲取桌面應用的權杖](scenario-desktop-acquire-token.md)
