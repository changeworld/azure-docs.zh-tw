---
title: 獲得多個資源的同意 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 .NET 的 Microsoft 身份驗證庫 （MSAL.NET） 獲得多個資源的預先同意。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085832"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>使用者使用MSAL.NET獲取多個資源的同意
Microsoft 標識平臺終結點不允許一次獲取多個資源的權杖。 將 Microsoft 身份驗證庫用於 .NET （MSAL.NET）時，獲取權杖方法中的作用域參數應僅包含單個資源的範圍。 但是，您可以通過使用`.WithExtraScopeToConsent`builder 方法指定其他作用域，預先同意多個資源。

> [!NOTE]
> 獲得多個資源的同意適用于 Microsoft 標識平臺，但對於 Azure AD B2C 則不有效。 Azure AD B2C 僅支援管理員同意，不支援使用者同意。

例如，如果您有兩個資源，每個資源有兩個作用域：

- HTTPs：\//mytenant.onmicrosoft.com/customerapi （帶`customer.read`2`customer.write`個作用域和 ）
- HTTPs：\//mytenant.onmicrosoft.com/vendorapi （帶`vendor.read`2`vendor.write`個作用域和 ）

您應該使用具有`.WithExtraScopeToConsent`*額外ScopetoConsent參數*的修改器，如以下示例所示：

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

這將獲取第一個 Web API 的訪問權杖。 然後，當您需要訪問第二個 Web API 時，您可以靜默地從權杖緩存中獲取權杖：

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
