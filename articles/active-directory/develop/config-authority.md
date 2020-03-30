---
title: 配置標識提供程式 （MSAL iOS/macOS） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 iOS 和 macOS 的 MSAL 使用不同的權威機構（如 B2C、主權雲和來賓使用者）。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 4810de772e44be22ee5bd4a9fb6ef0ef756e62f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085219"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>如何：為 iOS 和 macOS 配置 MSAL 以使用不同的標識提供程式

本文將介紹如何為不同的機構（如 Azure 活動目錄 （Azure AD）、企業對消費者 （B2C）、主權雲和來賓使用者）配置適用于 iOS 和 macOS （MSAL） 的 Microsoft 身份驗證庫應用。  在本文中，通常可以將機構視為標識提供程式。

## <a name="default-authority-configuration"></a>預設許可權配置

`MSALPublicClientApplication`配置的預設許可權 URL，該`https://login.microsoftonline.com/common`URL 適用于大多數 Azure 活動目錄 （AAD） 方案。 除非您正在實現高級方案（如國家雲）或使用 B2C，否則無需更改它。

> [!NOTE]
> 不支援使用活動目錄聯合服務作為標識提供程式 （ADFS） 進行現代身份驗證（有關詳細資訊，請參閱[開發人員 ADFS）。](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) 通過聯合支援 ADFS。

## <a name="change-the-default-authority"></a>更改預設許可權

在某些情況下，例如企業對消費者 （B2C），您可能需要更改預設許可權。

### <a name="b2c"></a>B2C

要使用 B2C，Microsoft[身份驗證庫 （MSAL）](reference-v2-libraries.md)需要不同的許可權配置。 MSAL 將一種權威 URL 格式本身識別為 B2C。 認可的 B2C 許可權格式`https://<host>/tfp/<tenant>/<policy>`是 ，`https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`例如 。 但是，您也可以通過顯式聲明許可權為 B2C 許可權來使用任何其他受支援的 B2C 許可權 URL。

要支援 B2C 的任意 URL`MSALB2CAuthority`格式，可以使用任意 URL 進行設置，如下所示：

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

所有不使用預設 B2C 許可權格式的 B2C 頒發機構都必須聲明為已知頒發機構。

將每個不同的 B2C 許可權添加到已知許可權清單中，即使許可權僅在政策上有所不同。

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

當應用請求新策略時，需要更改許可權 URL，因為每個策略的許可權 URL 不同。 

要配置 B2C 應用程式，`@property MSALAuthority *authority`在創建`MSALPublicClientApplication`之前`MSALB2CAuthority`使用`MSALPublicClientApplicationConfig`中的實例設置，如下所示：

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>主權雲

如果應用在主權雲中運行，則可能需要更改 中的`MSALPublicClientApplication`許可權 URL。 下面的示例設置用於使用德國 AAD 雲的許可權 URL：

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

您可能需要將不同的作用域傳遞給每個主權雲。 要發送的作用域取決於您正在使用的資源。 例如，您可以在`"https://graph.microsoft.com/user.read"`全球雲和德國`"https://graph.microsoft.de/user.read"`雲中使用。

### <a name="signing-a-user-into-a-specific-tenant"></a>將使用者登錄到特定租戶

當權限 URL 設置為`"login.microsoftonline.com/common"`時，使用者將登錄到其主租戶。 但是，某些應用可能需要將使用者登錄到其他租戶，而某些應用僅與單個租戶一起工作。

要將使用者登錄到特定租戶，請使用特定許可權`MSALPublicClientApplication`進行配置。 例如：

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

下面演示如何將使用者登錄到特定租戶：

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>支援當局

### <a name="msalauthority"></a>MSAL授權

類`MSALAuthority`是 MSAL 許可權類的基礎抽象類別。 不要嘗試使用`alloc`或`new`創建它的實例。 相反，要麼直接創建其子類之一`MSALAADAuthority`（， `MSALB2CAuthority`， 或使用工廠`authorityWithURL:error:`方法使用許可權 URL 創建子類）。

使用`url`屬性獲取正常化許可權 URL。 不屬於許可權的額外參數和路徑元件或片段不在返回的正常化許可權 URL 中。

以下是您可以具現化的子`MSALAuthority`類，具體取決於要使用的許可權。

### <a name="msalaadauthority"></a>姆薩拉德管理局

`MSALAADAuthority`表示 AAD 許可權。 許可權 URL 應採用以下格式，其中`<port>`可選：`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2C授權

`MSALB2CAuthority`表示 B2C 許可權。 預設情況下，B2C 許可權 URL 應採用以下格式，其中`<port>`可選： `https://<host>:<port>/tfp/<tenant>/<policy>`。 但是，MSAL 還支援其他任意 B2C 許可權格式。

## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
