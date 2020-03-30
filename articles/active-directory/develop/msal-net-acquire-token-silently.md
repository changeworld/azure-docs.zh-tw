---
title: 從緩存獲取權杖 （MSAL.NET）
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 Microsoft 身份驗證庫 （MSAL.NET） 靜默地（從權杖緩存）獲取訪問權杖。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084844"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>使用MSAL.NET從權杖緩存獲取權杖

當您使用 Microsoft 身份驗證庫獲取 .NET （MSAL.NET）的訪問權杖時，將緩存該權杖。 當應用程式需要權杖時，它應首先調用`AcquireTokenSilent`方法以驗證緩存中是否包含可接受的權杖。 在許多情況下，可以基於緩存中的權杖獲取具有更多作用域的另一個權杖。 當令牌接近過期時，也可以刷新權杖（因為權杖緩存還包含刷新權杖）。

建議的模式是首先調用`AcquireTokenSilent`方法。  如果`AcquireTokenSilent`失敗，則使用其他方法獲取權杖。

在下面的示例中，應用程式首先嘗試從權杖緩存中獲取權杖。  如果引發`MsalUiRequiredException`異常，應用程式將交互獲取權杖。 

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
