---
title: 清除權杖快取（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）清除權杖快取。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 83c1dd43235dc7bccb322a484362b08544d54d11
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85477510"
---
# <a name="clear-the-token-cache-using-msalnet"></a>使用 MSAL.NET 清除權杖快取

當您使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）[取得存取權杖](msal-acquire-cache-tokens.md)時，會快取權杖。 當應用程式需要權杖時，它應該會先呼叫 `AcquireTokenSilent` 方法，以確認快取中是否有可接受的 token。 

藉由從快取中移除帳戶來清除快取。 但這不會移除瀏覽器中的工作階段 Cookie。  下列範例會具現化公用用戶端應用程式、取得應用程式的帳戶，並移除帳戶。

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

若要深入瞭解如何取得和快取權杖，請參閱[取得存取權杖](msal-acquire-cache-tokens.md)。
