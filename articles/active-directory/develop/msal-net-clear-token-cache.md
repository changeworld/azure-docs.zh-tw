---
title: 清除權杖快取（MSAL.NET） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）清除權杖快取。
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 51407c4c157fa171bc5fd18e8db1f97f677ed973
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695580"
---
# <a name="clear-the-token-cache-using-msalnet"></a>使用 MSAL.NET 清除權杖快取

當您使用適用于 .NET 的 Microsoft 驗證程式庫（MSAL.NET）[取得存取權杖](msal-acquire-cache-tokens.md)時，會快取權杖。 當應用程式需要權杖時，應該先呼叫 `AcquireTokenSilent` 方法，以確認快取中是否有可接受的 token。 

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
