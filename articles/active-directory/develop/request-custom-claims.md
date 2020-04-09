---
title: 請求自定義聲明 (MSAL iOS/macOS) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何請求自定義聲明。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 4974fe3b387683f662d7a7b4f3ccb4935153f07e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883091"
---
# <a name="how-to-request-custom-claims-using-msal-for-ios-and-macos"></a>如何:使用 MSAL 請求針對 iOS 和 macOS 的自訂聲明

OpenID Connect 允許您選擇從 UserInfo 終結點和/或 ID 權杖中請求返回各個聲明。 聲明請求表示為 JSON 物件,其中包含請求的聲明清單。 有關詳細資訊[,請參閱 OpenID 連接核心 1.0。](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter)

適用於 iOS 和 macOS 的 Microsoft 身份驗證庫 (MSAL) 允許在互動式和靜默權杖獲取方案中請求特定聲明。 它通過`claimsRequest`參數這樣做。

需要這種情況的多種方案。 例如：

- 在應用程式的標準集之外請求聲明。
- 請求無法使用應用程式作用域指定的標準聲明的特定組合。 例如,如果訪問令牌因為缺少聲明而被拒絕,則應用程式可以使用 MSAL 請求缺少的聲明。

> [!NOTE]
> 每當指定聲明請求時,MSAL 都會繞過訪問令牌緩存。 僅當需要其他聲明時(而不是`claimsRequest`在每個 MSAL API 調用中`claimsRequest`始終提供相同的 參數)時才提供參數非常重要。

`claimsRequest`可在與`MSALInteractiveTokenParameters``MSALSilentTokenParameters`中指定 。

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
`MSALClaimsRequest`可以從 JSON 聲明請求的 NSString 表示形式構造。 

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



也可以通過請求其他特定聲明來對其進行修改:

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



`MSALClaimsRequest`然後,應在權杖參數中設置,並提供給 MSAL 權杖取得 API 之一:

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
