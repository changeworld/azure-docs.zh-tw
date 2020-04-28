---
title: 將桌面應用程式呼叫 web Api 移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將會呼叫 web Api 的桌面應用程式移至生產環境
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882874"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>呼叫 web Api 的桌面應用程式：移至生產環境

在本文中，您將瞭解如何將呼叫 web Api 的桌面應用程式移至生產環境。

## <a name="handle-errors-in-desktop-applications"></a>處理桌面應用程式中的錯誤

在不同的流程中，您已瞭解如何處理無訊息流程的錯誤，如程式碼片段所示。 您也會看到需要進行互動的情況，如同在增量同意和條件式存取中。

## <a name="have-the-user-consent-upfront-for-several-resources"></a>讓使用者預先同意數個資源

> [!NOTE]
> 獲得數項資源的同意，適用于 Microsoft 身分識別平臺，但不適用於 Azure Active Directory （Azure AD） B2C。 Azure AD B2C 僅支援管理員同意，而非使用者同意。

您無法使用 Microsoft 身分識別平臺（v2.0）端點一次取得數個資源的權杖。 `scopes`參數只能包含單一資源的範圍。 您可以使用`extraScopesToConsent`參數，確保使用者已預先同意至數個資源。

例如，您可能有兩個具有兩個範圍的資源：

- `https://mytenant.onmicrosoft.com/customerapi`使用範圍`customer.read`和`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`使用範圍`vendor.read`和`vendor.write`

在此範例中，請`.WithAdditionalPromptToConsent`使用具有`extraScopesToConsent`參數的修飾詞。

例如：

### <a name="in-msalnet"></a>在 MSAL.NET 中

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

### <a name="in-msal-for-ios-and-macos"></a>適用于 iOS 和 macOS 的 MSAL

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

此呼叫會取得第一個 Web API 的存取權杖。

當您需要呼叫第二個 Web API 時，請`AcquireTokenSilent`呼叫 API。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>每次執行應用程式時，Microsoft 個人帳戶都需要重新同意

針對 Microsoft 個人帳戶使用者，reprompting 在每個原生用戶端（桌面或行動應用程式）呼叫以授與授權是預期的行為。 Native client 身分識別原本就不安全，這違反了機密用戶端應用程式身分識別。 機密用戶端應用程式會與 Microsoft 身分識別平臺交換秘密，以證明其身分識別。 Microsoft 身分識別平臺選擇在每次授權應用程式時提示使用者同意，藉此減少取用者服務的安全。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
