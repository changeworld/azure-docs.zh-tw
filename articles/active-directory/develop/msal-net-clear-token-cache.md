---
title: 清除權杖緩存 （MSAL.NET） |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 .NET （MSAL.NET）的 Microsoft 身份驗證庫清除權杖緩存。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084777"
---
# <a name="clear-the-token-cache-using-msalnet"></a>使用MSAL.NET清除權杖緩存

當您使用 Microsoft 身份驗證庫為 .NET（MSAL.NET）[獲取訪問權杖](msal-acquire-cache-tokens.md)時，將緩存該權杖。 當應用程式需要權杖時，它應首先調用`AcquireTokenSilent`方法以驗證緩存中是否包含可接受的權杖。 

清除緩存是通過從緩存中刪除帳戶來實現的。 但這不會移除瀏覽器中的工作階段 Cookie。  下面的示例具現化公共用戶端應用程式，獲取應用程式的帳戶，並刪除帳戶。

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

要瞭解有關獲取和緩存權杖的更多內容，請閱讀[獲取訪問權杖](msal-acquire-cache-tokens.md)。
