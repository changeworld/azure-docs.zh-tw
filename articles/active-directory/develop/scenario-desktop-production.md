---
title: 將呼叫 web Api 的傳統型應用程式移至生產-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何將呼叫 web Api 的桌面應用程式移至生產環境
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80882874"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>呼叫 web Api 的桌面應用程式：移至生產環境

在本文中，您將瞭解如何將呼叫 web Api 的桌面應用程式移至生產環境。

## <a name="handle-errors-in-desktop-applications"></a>在桌面應用程式中處理錯誤

在不同的流程中，您已瞭解如何處理無訊息流程的錯誤，如程式碼片段所示。 您也已經看到需要進行互動的情況，如同累加式同意和條件式存取。

## <a name="have-the-user-consent-upfront-for-several-resources"></a>讓使用者同意數個資源

> [!NOTE]
> 針對 Microsoft 身分識別平臺，獲得數個資源的同意，但不適合 Azure Active Directory (Azure AD) B2C。 Azure AD B2C 僅支援系統管理員同意，而不支援使用者同意。

您無法使用 Microsoft 身分識別平臺 (v2.0) 端點，同時取得數個資源的權杖。 `scopes`參數只能包含單一資源的範圍。 您可以使用參數，確定使用者已預先同意至數個資源 `extraScopesToConsent` 。

比方說，您可能有兩個具有兩個範圍的資源：

- `https://mytenant.onmicrosoft.com/customerapi` 使用範圍 `customer.read` 和 `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` 使用範圍 `vendor.read` 和 `vendor.write`

在此範例中，請使用 `.WithAdditionalPromptToConsent` 具有參數的修飾詞 `extraScopesToConsent` 。

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

此呼叫會取得第一個 web API 的存取權杖。

當您需要呼叫第二個 web API 時，請呼叫 `AcquireTokenSilent` API。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>每次執行應用程式時，Microsoft 個人帳戶都需要 reconsent

針對 Microsoft 個人帳戶使用者， (桌面或行動裝置應用程式的每個原生用戶端上的同意 reprompting) 的授權是預期的行為。 原生用戶端身分識別本質上不安全，這與機密用戶端應用程式身分識別相反。 機密用戶端應用程式會與 Microsoft 身分識別平臺交換秘密，以證明其身分識別。 Microsoft 身分識別平臺會在每次授權應用程式時提示使用者同意，藉此為取用者服務選擇降低此安全。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
