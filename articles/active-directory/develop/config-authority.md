---
title: 設定身分識別提供者（MSAL iOS/macOS） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何透過 MSAL for iOS 和 macOS 使用不同的授權單位（例如 B2C、主權雲端和來賓使用者）。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77085219"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>如何：將 MSAL 用於 iOS 和 macOS，以使用不同的身分識別提供者

本文將說明如何針對不同的授權單位（例如 Azure Active Directory （Azure AD）、企業對消費者（B2C）、主權雲端和來賓使用者），設定適用于 iOS 和 macOS （MSAL）的 Microsoft 驗證程式庫應用程式。  在本文中，您通常可以將授權視為身分識別提供者。

## <a name="default-authority-configuration"></a>預設授權設定

`MSALPublicClientApplication`是使用的預設授權 URL 來設定`https://login.microsoftonline.com/common`，這適用于大部分的 AZURE ACTIVE DIRECTORY （AAD）案例。 除非您要執行像是國家雲端的 advanced 案例，或使用 B2C，否則您不需要變更它。

> [!NOTE]
> 不支援以 Active Directory 同盟服務身分識別提供者（ADFS）進行新式驗證（如需詳細資訊，請參閱[適用于開發人員的 adfs](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) ）。 ADFS 受到同盟的支援。

## <a name="change-the-default-authority"></a>變更預設授權單位

在某些情況下，例如企業對消費者（B2C），您可能需要變更預設的授權單位。

### <a name="b2c"></a>B2C

若要使用 B2C， [Microsoft 驗證程式庫（MSAL）](reference-v2-libraries.md)需要不同的授權設定。 MSAL 會將一個授權單位 URL 格式視為 B2C 本身。 例如`https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`，可辨識的 B2C `https://<host>/tfp/<tenant>/<policy>`授權單位格式為。 不過，您也可以明確地將授權單位宣告為 B2C 授權單位，藉以使用任何其他支援的 B2C 授權 Url。

若要支援 B2C 的任意 URL 格式， `MSALB2CAuthority`可以使用任意 url 來設定，如下所示：

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

所有不使用預設 B2C 授權單位格式的 B2C 授權單位，都必須宣告為已知的授權單位。

將每個不同的 B2C 授權單位新增至已知的授權清單，即使原則中的授權不同也一樣。

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

當您的應用程式要求新的原則時，必須變更授權單位 URL，因為每個原則的授權單位 URL 都不同。 

若要設定 B2C 應用程式， `@property MSALAuthority *authority`請`MSALB2CAuthority`在建立`MSALPublicClientApplicationConfig` `MSALPublicClientApplication`之前，使用中的實例進行設定，如下所示：

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

### <a name="sovereign-clouds"></a>主權雲端

如果您的應用程式在主權雲端中執行，您可能需要變更中的授權單位`MSALPublicClientApplication`URL。 下列範例會設定授權單位 URL 以與德國 AAD 雲端搭配使用：

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

您可能需要將不同的範圍傳遞給每個主權雲端。 要傳送的範圍取決於您所使用的資源。 例如，您可以在全球`"https://graph.microsoft.com/user.read"`雲端和`"https://graph.microsoft.de/user.read"`德文雲端中使用。

### <a name="signing-a-user-into-a-specific-tenant"></a>將使用者登入特定租使用者

當授權單位 URL 設定為`"login.microsoftonline.com/common"`時，使用者將會登入其主租使用者。 不過，某些應用程式可能需要將使用者登入不同的租使用者，而某些應用程式只能與單一租使用者搭配使用。

若要將使用者登入特定租使用者， `MSALPublicClientApplication`請使用特定授權單位進行設定。 例如：

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

以下顯示如何將使用者登入特定租使用者：

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

## <a name="supported-authorities"></a>支援的授權單位

### <a name="msalauthority"></a>MSALAuthority

`MSALAuthority`類別是 MSAL 授權單位類別的基底抽象類別。 請不要嘗試使用`alloc`或`new`來建立其實例。 相反地，請直接建立其中一個子類別`MSALAADAuthority`（ `MSALB2CAuthority`、），或使用 factory `authorityWithURL:error:`方法來建立使用授權單位 URL 的子類別。

使用`url`屬性，即可取得正規化的授權單位 URL。 不屬於授權單位的額外參數和路徑元件或片段，將不會出現在傳回的正規化授權 URL 中。

以下是您可以根據`MSALAuthority`要使用的授權單位來具現化的子類別。

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority`代表 AAD 授權單位。 授權單位 url 應採用下列格式，其中`<port>`是選擇性的：`https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority`代表 B2C 授權單位。 根據預設，B2C 授權單位 url 應為下列格式，其中`<port>`是選擇性的：。 `https://<host>:<port>/tfp/<tenant>/<policy>` 不過，MSAL 也支援其他任意 B2C 授權單位格式。

## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
