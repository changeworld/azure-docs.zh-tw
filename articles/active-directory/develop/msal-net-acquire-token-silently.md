---
title: '從快取中取得 (MSAL.NET 的權杖) '
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET (MSAL.NET) 的 Microsoft 驗證程式庫，以無訊息方式從權杖快取) 取得存取權杖 (。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 95cfb937ce236d0acd1a3369068afc6f3b505aed
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88166241"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>使用 MSAL.NET 從權杖快取取得權杖

當您使用適用于 .NET (MSAL.NET) 的 Microsoft 驗證程式庫取得存取權杖時，會快取權杖。 當應用程式需要權杖時，它應該會先呼叫 `AcquireTokenSilent` 方法，以確認快取中是否有可接受的 token。 在許多情況下，您可以根據快取中的權杖，取得具有更多範圍的另一個權杖。 您也可以在權杖接近到期 (時重新整理權杖，因為權杖快取也會包含) 的重新整理權杖。

建議的模式是 `AcquireTokenSilent` 先呼叫方法。  如果 `AcquireTokenSilent` 失敗，則使用其他方法取得權杖。

在下列範例中，應用程式會先嘗試從權杖快取取得權杖。  如果擲回 `MsalUiRequiredException` 例外狀況，應用程式會以互動方式取得權杖。 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
