---
title: 從快取取得權杖（MSAL.NET）
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET），以無訊息方式（從權杖快取）取得存取權杖。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: bb17ebf5afdc20eaa9db8b86a5643ad18a112eab
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695648"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>使用 MSAL.NET 從權杖快取取得權杖

當您使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）取得存取權杖時，會快取權杖。 當應用程式需要權杖時，應該先呼叫 `AcquireTokenSilent` 方法，以確認快取中是否有可接受的 token。 在許多情況下，您可以根據快取中的權杖，取得具有更多範圍的另一個權杖。 當令牌接近到期時，您也可以重新整理權杖（因為權杖快取也包含重新整理權杖）。

建議的模式是先呼叫 `AcquireTokenSilent` 方法。  如果 `AcquireTokenSilent` 失敗，則使用其他方法取得權杖。

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
