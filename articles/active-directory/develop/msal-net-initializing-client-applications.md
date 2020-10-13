---
title: 初始化 MSAL.NET 用戶端應用程式 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) ，初始化公用用戶端和機密用戶端應用程式。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 5ec419be5c7549553788d009f09fa3e0fb8655e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91258280"
---
# <a name="initialize-client-applications-using-msalnet"></a>使用 MSAL.NET 初始化用戶端應用程式
本文說明如何使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) ，初始化公用用戶端和機密用戶端應用程式。  若要深入瞭解用戶端應用程式類型和應用程式設定選項，請參閱 [總覽](msal-client-applications.md)。

使用 MSAL.NET 3.x 時，將應用程式具現化的建議方法是使用應用程式構建者： `PublicClientApplicationBuilder` 和 `ConfidentialClientApplicationBuilder` 。 它們提供了一種強大的機制，可從程式碼或設定檔來設定應用程式，甚至是混合兩種方法。

## <a name="prerequisites"></a>必要條件
在初始化應用程式之前，您必須先 [註冊該](quickstart-register-app.md) 應用程式，讓您的應用程式可以與 Microsoft 身分識別平臺整合。  註冊之後，您可能需要下列資訊 (可在 Azure 入口網站) 中找到：

- 用戶端識別碼 (代表 GUID 的字串) 
- 身分識別提供者 URL (名為實例) ，以及您應用程式的登入物件。 這兩個參數統稱為授權單位。
- 租使用者識別碼（如果您只是針對您的組織撰寫企業營運應用程式） (也命名為單一租使用者應用程式) 。
- 應用程式密碼 (用戶端密碼字串) 或類型為 X509Certificate2) 的憑證 (（如果它是機密用戶端應用程式的話）。
- 針對 web 應用程式，有時適用于公用用戶端應用程式 (特別是當您的應用程式需要使用訊息代理程式) 時，您也會設定 redirectUri，讓身分識別提供者使用安全性權杖來與您的應用程式連線。

## <a name="ways-to-initialize-applications"></a>初始化應用程式的方式
有許多不同的方式可具現化用戶端應用程式。

### <a name="initializing-a-public-client-application-from-code"></a>從程式碼初始化公用用戶端應用程式

下列程式碼會具現化公用用戶端應用程式、使用其公司和學校帳戶登入 Microsoft Azure 公用雲端中的使用者，或其個人的 Microsoft 帳戶。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>從程式碼初始化機密用戶端應用程式

同樣地，下列程式碼會將機密應用程式具現化 (Web 應用程式，該應用程式位於 `https://myapp.azurewebsites.net`) 處理 Microsoft Azure 公用雲端中使用者的權杖，以及其公司和學校帳戶，或其個人的 Microsoft 帳戶。 應用程式會藉由共用用戶端密碼來識別身分識別提供者：

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

您可能知道，在生產環境中，您可能會想要與憑證 Azure AD 共用，而不是使用用戶端密碼。 程式碼如下所示：

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>從設定選項初始化公用用戶端應用程式

下列程式碼會從設定物件具現化公用用戶端應用程式，該設定物件可以透過程式設計方式或從設定檔案讀取來填入：

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>從設定選項初始化機密用戶端應用程式

相同類型的模式會套用至機密用戶端應用程式。 您也可以使用 `.WithXXX` (在憑證) 的修飾詞來新增其他參數。

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Builder 修飾詞

在使用應用程式產生器的程式碼片段中，有數種 `.With` 方法可以套用為修飾詞 (例如 `.WithCertificate` 和 `.WithRedirectUri`) 。 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>公用和機密用戶端應用程式通用的修飾詞

您可以在公用用戶端或機密用戶端應用程式產生器上設定的修飾詞為：

|修飾詞 | 描述|
|--------- | --------- |
|`.WithAuthority()` 7個覆寫 | 將應用程式預設授權單位設定為 Azure AD 授權單位，而且可能會選擇 Azure 雲端、物件、租使用者 (租使用者識別碼或功能變數名稱) ，或直接提供授權單位 URI。|
|`.WithAdfsAuthority(string)` | 將應用程式預設授權單位設定為 ADFS 授權單位。|
|`.WithB2CAuthority(string)` | 將應用程式預設授權單位設定為 Azure AD B2C 授權單位。|
|`.WithClientId(string)` | 覆寫用戶端識別碼。|
|`.WithComponent(string)` | 使用 MSAL.NET (為遙測原因設定程式庫的名稱) 。 |
|`.WithDebugLoggingCallback()` | 如果呼叫此應用程式，應用程式將會呼叫， `Debug.Write` 只會啟用偵錯工具追蹤。 如需詳細資訊，請參閱[記錄](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | 設定將在所有驗證要求中傳送的應用層級額外查詢參數。 在具有相同) 的每個權杖取得方法層級 (可覆寫 `.WithExtraQueryParameters pattern` 。|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | 啟用 advanced 案例（例如設定 HTTP proxy），或強制 MSAL 在 ASP.NET Core web apps/Api) 中使用特定的 HttpClient (。|
|`.WithLogging()` | 如果呼叫此應用程式，應用程式將會使用偵錯工具呼叫回呼。 如需詳細資訊，請參閱[記錄](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)。|
|`.WithRedirectUri(string redirectUri)` | 覆寫預設的重新導向 URI。 在公用用戶端應用程式的案例中，這對包含 broker 的案例很有用。|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | 設定用來傳送遙測的委派。|
|`.WithTenantId(string tenantId)` | 覆寫租使用者識別碼或租使用者描述。|

### <a name="modifiers-specific-to-xamarinios-applications"></a>適用于 Xamarin iOS 應用程式的修飾詞

您可以在 Xamarin 的公用用戶端應用程式產生器上設定的修飾詞為：

|修飾詞 | 描述|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **僅限 Xamarin**：設定適用于快取持續性) 的 ios 金鑰鏈安全性群組 (。|

### <a name="modifiers-specific-to-confidential-client-applications"></a>機密用戶端應用程式專用的修飾詞

您可以在機密用戶端應用程式產生器上設定的修飾詞為：

|修飾詞 | 描述|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | 使用 Azure AD 設定識別應用程式的憑證。|
|`.WithClientSecret(string clientSecret)` | 設定用戶端密碼 (應用程式密碼) 使用 Azure AD 識別應用程式。|

這些修飾詞互相排斥。 如果您同時提供這兩個 MSAL，則會擲回有意義的例外狀況。

### <a name="example-of-usage-of-modifiers"></a>使用修飾詞的範例

讓我們假設您的應用程式是企業營運應用程式，這只適用于您的組織。  然後您可以撰寫：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

有趣的是，國家雲端的程式設計現在已簡化。 如果您想要讓應用程式成為全國雲端中的多租使用者應用程式，您可以撰寫，例如：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

目前並不支援 adfs (ADFS 2019 的覆寫) ：
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後，如果您是 Azure AD B2C 開發人員，您可以指定您的租使用者，如下所示：

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
