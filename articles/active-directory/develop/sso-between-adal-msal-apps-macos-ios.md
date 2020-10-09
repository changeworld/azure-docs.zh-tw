---
title: ADAL & MSAL apps 之間的 SSO (iOS/macOS) -Microsoft 身分識別平臺 |蔚藍
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80880745"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>如何：在 macOS 和 iOS 上的 ADAL 與 MSAL 應用程式之間的 SSO

適用于 iOS 的 Microsoft 驗證程式庫 (MSAL) 可與應用程式之間的 [ADAL 目標](https://github.com/AzureAD/azure-activedirectory-library-for-objc) 共用 SSO 狀態。 您可以依自己的步調將應用程式遷移至 MSAL，以確保您的使用者仍可受益于跨應用程式的 SSO--即使是混用 ADAL 和 MSAL 應用程式。

如果您要尋找使用 MSAL SDK 在應用程式之間設定 SSO 的指引，請參閱 [多個應用程式之間的無訊息 sso](single-sign-on-macos-ios.md#silent-sso-between-apps)。 本文著重于 ADAL 與 MSAL 之間的 SSO。

執行 SSO 的細節取決於您所使用的 ADAL 版本。

## <a name="adal-27x"></a>ADAL 2.7. x

本節涵蓋 MSAL 與 ADAL 2.7. x 之間的 SSO 差異

### <a name="cache-format"></a>快取格式

ADAL 2.7. x 可以讀取 MSAL 快取格式。 您不需要針對具有 ADAL 2.7. x 版的跨應用程式 SSO 進行任何特殊動作。 不過，您必須知道這兩個程式庫支援的帳戶識別碼差異。

### <a name="account-identifier-differences"></a>帳戶識別碼差異

MSAL 和 ADAL 使用不同的帳戶識別碼。 ADAL 會使用 UPN 作為其主要帳戶識別碼。 MSAL 會使用以物件識別碼為基礎的不可顯示帳戶識別碼和 AAD 帳戶的租使用者識別碼，以及 `sub` 其他帳戶類型的宣告。

當您 `MSALAccount` 在 MSAL 結果中收到物件時，它會在屬性中包含帳戶識別碼 `identifier` 。 應用程式應該針對後續的無訊息要求使用此識別碼。

除了之外 `identifier` ， `MSALAccount` 物件還包含名為的可顯示識別碼 `username` 。 `userId`在 ADAL 中轉譯為。 `username` 不會被視為唯一的識別碼，而且可以隨時變更，因此只能用於與 ADAL 的回溯相容性案例。 MSAL 支援使用或的快取查詢 `username` `identifier` ， `identifier` 建議您在其中進行查詢。

下表摘要說明 ADAL 與 MSAL 之間的帳戶識別碼差異：

| 帳戶識別碼                | MSAL                                                         | ADAL 2.7. x      | ADAL 2.7. x 之前的舊版 ADAL ()  |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 可顯示的識別碼            | `username`                                                   | `userId`        | `userId`                       |
| 唯一的不可顯示識別碼 | `identifier`                                                 | `homeAccountId` | N/A                            |
| 沒有已知的帳戶識別碼               | 透過 `allAccounts:` 中的 API 查詢所有帳戶 `MSALPublicClientApplication` | N/A             | N/A                            |

這是 `MSALAccount` 提供這些識別碼的介面：

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>從 MSAL 到 ADAL 的 SSO

如果您有 MSAL 應用程式和 ADAL 應用程式，且使用者第一次登入 MSAL 應用程式，您可以從物件中儲存， `username` `MSALAccount` 並將其傳遞給 adal 應用程式，以在 adal 應用程式中取得 SSO `userId` 。 ADAL 接著可以使用 API 以無訊息方式尋找帳戶資訊 `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` 。

### <a name="sso-from-adal-to-msal"></a>從 ADAL 至 MSAL 的 SSO

如果您有 MSAL 應用程式和 ADAL 應用程式，且使用者第一次登入 ADAL 架構的應用程式，您可以在 MSAL 中使用 ADAL 使用者識別碼進行帳戶查閱。 這也適用于從 ADAL 遷移至 MSAL 時。

#### <a name="adals-homeaccountid"></a>ADAL 的 homeAccountId

ADAL 2.7. x 會透過 `homeAccountId` `ADUserInformation` 這個屬性，傳回結果中物件的。

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` ADAL 在 MSAL 中相當於 `identifier` 。 您可以將此識別碼儲存在 MSAL 中，以使用 API 進行帳戶查閱 `accountForIdentifier:error:` 。

#### <a name="adals-userid"></a>ADAL `userId`

如果 `homeAccountId` 無法使用，或您只有可顯示的識別碼，您可以使用 ADAL `userId` 來查閱 MSAL 中的帳戶。

在 MSAL 中，請先查閱或的 `username` 帳戶 `identifier` 。 `identifier`如果有的話，請一律使用來查詢，而且只會使用 `username` 做為回復。 如果找到帳戶，請在呼叫中使用該帳戶 `acquireTokenSilent` 。

Objective-C：

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift：

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL 支援的帳戶查閱 Api：

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6。6

本節涵蓋 MSAL 與 ADAL 2.x-2.6.6 之間的 SSO 差異。

較舊的 ADAL 版本原本就不支援 MSAL 快取格式。 不過，為了確保從 ADAL 順利遷移至 MSAL，MSAL 可以讀取較舊的 ADAL 快取格式，而不會再次提示使用者認證。

因為 `homeAccountId` 舊版 ADAL 中無法使用，所以您必須使用下列內容來查閱帳戶 `username` ：

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

例如：

Objective-C：


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift：

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



或者，您也可以讀取所有帳戶，這也會讀取 ADAL 的帳戶資訊：

Objective-C：

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift：

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>後續步驟

深入了解[驗證流程和應用程式案例](authentication-flows-app-scenarios.md)
