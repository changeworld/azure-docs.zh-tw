---
title: ADAL & MSAL 應用程式 (iOS/macOS) 之間的 SSO - 微軟身份平臺 |蔚藍
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880745"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>如何:在 macOS 和 iOS 上的 ADAL 和 MSAL 應用程式之間的 SSO

適用於 iOS 的 Microsoft 驗證函式庫 (MSAL) 可以在應用程式之間與[ADAL 目標 C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)共用 SSO 狀態。 您可以按照自己的節奏將應用遷移到 MSAL,確保使用者仍將受益於跨應用 SSO,即使混合使用基於 ADAL 和 MSAL 的應用也是如此。

如果要查找使用 MSAL SDK 在應用之間設置 SSO 的指導,請參閱[多個應用之間的靜默 SSO。](single-sign-on-macos-ios.md#silent-sso-between-apps) 本文重點介紹 ADAL 和 MSAL 之間的 SSO。

實現 SSO 的細節取決於您使用的 ADAL 版本。

## <a name="adal-27x"></a>ADAL 2.7.x

本節介紹 MSAL 和 ADAL 2.7.x 之間的 SSO 差異

### <a name="cache-format"></a>快取格式

ADAL 2.7.x 可以讀取 MSAL 快取格式。 您無需為具有 ADAL 2.7.x 版本的跨應用 SSO 執行任何特殊操作。 但是,您需要瞭解這兩個庫支援的帳戶標識符的差異。

### <a name="account-identifier-differences"></a>帳號識別碼差異

MSAL 和 ADAL 使用不同的帳戶識別碼。 ADAL 使用 UPN 作為其主帳戶識別碼。 MSAL 使用基於 AAD 帳戶的物件 ID 和租戶 ID 的不可顯示帳戶標識符,以及`sub`其他類型的帳戶的聲明。

當您在 MSAL 結果中收到`MSALAccount`物件時 ,它`identifier`包含屬性中的 帳戶識別碼。 應用程式應為此標識碼用於後續靜默請求。

除了之外`identifier``MSALAccount`, 物件還包含一`username`個稱為 的可顯示標識符。 這轉化為`userId`ADAL。 `username`不被視為唯一標識碼,可以隨時更改,因此應僅用於與 ADAL 的向後相容性方案。 MSAL 支援`username`使用`identifier`或的緩存查詢,`identifier`其中建議查詢。

下表總結了 ADAL 和 MSAL 之間的帳戶識別碼差異:

| 帳號識別碼                | MSAL                                                         | ADAL 2.7.x      | 較舊的 ADAL(ADAL 2.7.x 之前) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| 可顯示識別碼            | `username`                                                   | `userId`        | `userId`                       |
| 唯一不可顯示識別碼 | `identifier`                                                 | `homeAccountId` | N/A                            |
| 不知道帳戶識別碼               | 透過`allAccounts:`API 查詢所有帳戶`MSALPublicClientApplication` | N/A             | N/A                            |

這是提供這些`MSALAccount`識別碼的介面:

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

如果您有 MSAL 應用和 ADAL 應用,並且使用者首先登入基於 MSAL 的`username``MSALAccount`應用,則可以透過將從物件中保存並將其轉換`userId`為基於 ADAL 的應用, 從而在 ADAL 應用中獲取 SSO。 然後,ADAL 可以`acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:`使用 API 靜默地查找帳戶資訊。

### <a name="sso-from-adal-to-msal"></a>從 ADAL 到 MSAL 的 SSO

如果您有 MSAL 應用和 ADAL 應用,並且使用者首先登錄到基於 ADAL 的應用,則可以使用 ADAL 使用者識別碼在 MSAL 中尋找帳戶。 這也適用於從 ADAL 遷移到 MSAL 時。

#### <a name="adals-homeaccountid"></a>ADAL 的家帳戶 Id

ADAL 2.7.x`homeAccountId`透過`ADUserInformation`此屬性傳回結果的物件中的物件中的 :

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`在ADAL的相當於`identifier`在MSAL。 您可以將此識別碼儲存為在 MSAL 中使用,`accountForIdentifier:error:`以便使用 API 進行帳戶查找。

#### <a name="adals-userid"></a>阿達爾的`userId`

如果`homeAccountId`不可用,或者您只有可顯示識別碼,則可以使用`userId`ADAL 在 MSAL 中尋找帳戶。

在 MSAL 中,`username`首先`identifier`按 或查找帳戶。 始終用於`identifier`查詢(如果有),並且僅用作`username`回退。 如果找到該帳戶,請使用調用中的`acquireTokenSilent`帳戶。

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



MSAL 支援的帳戶尋找 API:

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

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

本節介紹 MSAL 和 ADAL 2.x-2.6.6 之間的 SSO 差異。

較舊的 ADAL 版本在本機上不支援 MSAL 快取格式。 但是,為了確保從 ADAL 順利遷移到 MSAL,MSAL 可以讀取較舊的 ADAL 緩存格式,而無需再次提示使用者認證。

由於`homeAccountId`舊版 ADAL 版本無法使用,因此您需要`username`使用 搜尋帳號:

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



或者,您也可以讀取所有帳戶,這些帳戶還將從 ADAL 讀取帳戶資訊:

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
