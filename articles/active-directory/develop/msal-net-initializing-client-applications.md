---
title: 初始化MSAL.NET用戶端應用程式 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 Microsoft 身份驗證庫為 .NET （MSAL.NET） 初始化公共用戶端和機密用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083979"
---
# <a name="initialize-client-applications-using-msalnet"></a>使用MSAL.NET初始化用戶端應用程式
本文介紹使用 Microsoft 身份驗證庫為 .NET （MSAL.NET） 初始化公共用戶端和機密用戶端應用程式。  要瞭解有關用戶端應用程式類型和應用程式佈建選項的詳細資訊，請閱讀[概述](msal-client-applications.md)。

對於 MSAL.NET 3.x，建議具現化應用程式的方法是使用應用程式產生器：`PublicClientApplicationBuilder`和`ConfidentialClientApplicationBuilder`。 它們提供了一個強大的機制，可以從代碼或設定檔配置應用程式，甚至通過混合這兩種方法。

## <a name="prerequisites"></a>Prerequisites
在初始化應用程式之前，首先需要[註冊它](quickstart-register-app.md)，以便應用可以與 Microsoft 標識平臺集成。  註冊後，您可能需要以下資訊（可在 Azure 門戶中找到）：

- 用戶端 ID（表示 GUID 的字串）
- 標識提供程式 URL（名為實例）和應用程式的登錄訪問群體。 這兩個參數統稱為許可權。
- 如果您只為您的組織（也稱為單租戶應用程式）編寫業務線應用程式，則租戶 ID。
- 應用程式金鑰（用戶端機密字串）或證書（X509證書2類型）（如果是機密用戶端應用）。
- 對於 Web 應用，有時對於公共用戶端應用（尤其是在應用需要使用代理時），您還將設置重定向 Uri，標識提供程式將在其中使用安全權杖聯繫回應用程式。

## <a name="ways-to-initialize-applications"></a>初始化應用程式的方法
具現化用戶端應用程式的方法有很多種。

### <a name="initializing-a-public-client-application-from-code"></a>從代碼初始化公共用戶端應用程式

以下代碼具現化公共用戶端應用程式、在 Microsoft Azure 公共雲中登錄的使用者及其工作帳戶和學校帳戶或其個人 Microsoft 帳戶。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>從代碼初始化機密用戶端應用程式

同樣，以下代碼會具現化一個機密應用程式（位於`https://myapp.azurewebsites.net`的 Web 應用），以處理 Microsoft Azure 公共雲中的使用者及其工作帳戶和學校帳戶或其個人 Microsoft 帳戶中的權杖。 通過共用用戶端金鑰，使用標識提供程式標識應用程式：

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

您可能知道，在生產中，您可能希望與 Azure AD 共用證書，而不是使用用戶端機密。 然後，代碼如下：

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>從配置選項初始化公共用戶端應用程式

以下代碼從設定物件具現化公共用戶端應用程式，該物件可以以程式設計方式填充或從設定檔中讀取：

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>從配置選項初始化機密用戶端應用程式

相同的模式適用于機密用戶端應用程式。 您還可以使用`.WithXXX`修改器添加其他參數（此處為證書）。

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>產生器修改器

在使用應用程式產生器的代碼程式碼片段中，許多`.With`方法可以作為修飾符應用（例如，`.WithCertificate`和`.WithRedirectUri`。 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>公共和機密用戶端應用程式共有的修改器

您可以在公共用戶端或機密用戶端應用程式產生器上設置的修改器包括：

|修飾詞 | 描述|
|--------- | --------- |
|`.WithAuthority()`7 次覆蓋 | 將應用程式預設許可權設置為 Azure AD 許可權，可以選擇 Azure 雲、訪問群體、租戶（租戶 ID 或功能變數名稱）或直接提供許可權 URI。|
|`.WithAdfsAuthority(string)` | 將應用程式預設許可權設為 ADFS 許可權。|
|`.WithB2CAuthority(string)` | 將應用程式預設許可權設為 Azure AD B2C 許可權。|
|`.WithClientId(string)` | 覆蓋用戶端 ID。|
|`.WithComponent(string)` | 使用MSAL.NET（出於遙測原因）設置庫的名稱。 |
|`.WithDebugLoggingCallback()` | 如果調用，應用程式將調用`Debug.Write`只是啟用調試跟蹤。 如需詳細資訊，請參閱[記錄](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 設置將在所有身份驗證請求中發送的應用程式級別額外查詢參數。 這在每個權杖採集方法級別（具有相同`.WithExtraQueryParameters pattern`）是可重寫的。|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | 啟用高級方案，如為 HTTP 代理配置，或強制 MSAL 使用特定的 HttpClient（例如，在 ASP.NET核心 Web 應用/API中）。|
|`.WithLogging()` | 如果調用，應用程式將調用具有調試跟蹤的回檔。 如需詳細資訊，請參閱[記錄](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithRedirectUri(string redirectUri)` | 覆蓋預設重定向 URI。 對於公共用戶端應用程式，這對於涉及代理的方案非常有用。|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 設置用於發送遙測的委託。|
|`.WithTenantId(string tenantId)` | 覆蓋租戶 ID 或租戶說明。|

### <a name="modifiers-specific-to-xamarinios-applications"></a>特定于 Xamarin.iOS 應用程式的修飾符

您可以在 Xamarin.iOS 上的公共用戶端應用程式產生器上設置的修飾符是：

|修飾詞 | 描述|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **僅限 Xamarin.iOS：** 設置 iOS 金鑰鏈安全性群組（用於緩存持久性）。|

### <a name="modifiers-specific-to-confidential-client-applications"></a>特定于機密用戶端應用程式的修飾符

您可以在機密用戶端應用程式產生器上設置的修改器包括：

|修飾詞 | 描述|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | 使用 Azure AD 設置標識應用程式的證書。|
|`.WithClientSecret(string clientSecret)` | 使用 Azure AD 設置標識應用程式的用戶端金鑰（應用密碼）。|

這些修飾符是互斥的。 如果同時提供這兩個，MSAL 將引發一個有意義的異常。

### <a name="example-of-usage-of-modifiers"></a>修改器的使用示例

假設應用程式是業務線應用程式，僅適用于您的組織。  然後，您可以編寫：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

有趣的是，國家雲的程式設計現在已經簡化。 如果希望應用程式是國家雲中的多租戶應用程式，則可以編寫，例如：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS 也有覆蓋（ADFS 2019 目前不受支援）：
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果您是 Azure AD B2C 開發人員，則可以像這樣指定租戶：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
