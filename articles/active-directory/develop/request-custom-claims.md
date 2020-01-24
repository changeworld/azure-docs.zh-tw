---
title: 要求自訂宣告（MSAL iOS/macOS） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何要求自訂宣告。
services: active-directory
documentationcenter: ''
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 0aa4648d3d5e76c2d0ebd7524ad8dfa52da3fb30
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702482"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>如何：使用適用于 iOS 和 macOS 的 MSAL 要求自訂宣告

OpenID Connect 可讓您選擇性地要求從使用者資訊端點和/或在識別碼權杖中傳回個別宣告。 宣告要求會以 JSON 物件表示，其中包含要求的宣告清單。 如需詳細資訊，請參閱[OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) 。

適用于 iOS 和 macOS 的 Microsoft 驗證程式庫（MSAL）可在互動式和無訊息的權杖取得案例中，要求特定的宣告。 它會透過 `claimsRequest` 參數來執行此動作。

有多個案例需要這麼做。 例如：

- 在您應用程式的標準集外部要求宣告。
- 要求不能使用應用程式的範圍指定的標準宣告的特定組合。 例如，如果因為遺漏宣告而拒絕存取權杖，應用程式可以使用 MSAL 來要求遺漏的宣告。

> [!NOTE]
> 每當指定了宣告要求時，MSAL 就會略過存取權杖快取。 請務必在需要額外的宣告時才提供 `claimsRequest` 參數（而不是一律在每個 MSAL API 呼叫中提供相同的 `claimsRequest` 參數）。

可以在 `MSALSilentTokenParameters` 和 `MSALInteractiveTokenParameters`中指定 `claimsRequest`：

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` 可以從 JSON 宣告要求的 NSString 標記法來進行。 

Objective-C：

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift：

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



也可以藉由要求其他特定的宣告來進行修改：

Objective-C：

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift：

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



`MSALClaimsRequest` 應該在權杖參數中設定，並提供給其中一個 MSAL 權杖取得 Api：

Objective-C：

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift：

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
