---
title: 將呼叫 Web API 的桌面應用移動到生產 - 微軟識別平臺 |蔚藍
description: 瞭解如何將呼叫 Web API 的桌面應用移至生產
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
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882874"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>呼叫 Web API 的桌面應用:移動到生產

在本文中,您將瞭解如何將調用 Web API 的桌面應用移動到生產。

## <a name="handle-errors-in-desktop-applications"></a>處理桌面應用程式中的錯誤

在不同的流中,您已經學會了如何處理靜默流的錯誤,如代碼段所示。 您還看到,在某些情況下需要交互,如增量同意和條件訪問。

## <a name="have-the-user-consent-upfront-for-several-resources"></a>提前取得多個資源的使用者同意

> [!NOTE]
> 獲得多個資源的同意適用於 Microsoft 標識平臺,但對於 Azure 活動目錄 (Azure AD) B2C 則不有效。 Azure AD B2C 僅支援管理員同意,不支援使用者同意。

使用 Microsoft 標識平臺 (v2.0) 終結點,不能一次獲取多個資源的權杖。 參數`scopes`只能包含單個資源的範圍。 您可以使用`extraScopesToConsent`參數確保使用者預先同意多個資源。

例如,您可能有兩個資源,每個資源有兩個作用域:

- `https://mytenant.onmicrosoft.com/customerapi`與範圍`customer.read`與`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`與範圍`vendor.read`與`vendor.write`

此範例中,`.WithAdditionalPromptToConsent`使用具有參數`extraScopesToConsent`的修飾符。

例如：

### <a name="in-msalnet"></a>在MSAL.NET

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

### <a name="in-msal-for-ios-and-macos"></a>在適用於 iOS 和 macOS 的 MSAL 中

Objective-C：

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift：

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

此調用獲取第一個 Web API 的訪問權杖。

當您需要調用第二個 Web API 時`AcquireTokenSilent`,呼叫 API。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>每次應用運行時,Microsoft 個人帳戶都需要重新批准

對於 Microsoft 個人帳戶使用者,在授權的每個本機用戶端(桌面或行動應用)調用上重新提示同意是預期行為。 本機客戶端標識本質上是不安全的,這與機密用戶端應用程式標識相悖。 機密用戶端應用程式與Microsoft識別平台交換機密,以證明其身份。 Microsoft 標識平台選擇在每次授權應用程式時提示使用者同意,以減輕消費者服務的這種不安全。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
