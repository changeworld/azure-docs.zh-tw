---
title: 提供 HttpClient & proxy (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 .NET 的 Microsoft 驗證程式庫 (MSAL.NET) ，以提供您自己的 HttpClient 和 proxy 來連線至 Azure AD。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6139fd1b081c69f037ec9cd3313e4a6499c39543
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064618"
---
# <a name="providing-your-own-httpclient-and-proxy-using-msalnet"></a>使用 MSAL.NET 提供您自己的 HttpClient 和 Proxy
[將公用用戶端應用程式初始化](msal-net-initializing-client-applications.md)時，您可以使用 `.WithHttpClientFactory method` 來提供自己的 HttpClient。  提供您自己的 HttpClient 會啟用如下的進階案例：精確控制 HTTP Proxy、自訂使用者代理程式標頭，或強制 MSAL 使用特定的 HttpClient (例如，在 ASP.NET Core Web 應用程式/API 中)。

## <a name="initialize-with-httpclientfactory"></a>使用 HttpClientFactory 初始化
下列範例示範如何建立 `HttpClientFactory`，然後使用它來將公用用戶端應用程式初始化：

```csharp
IMsalHttpClientFactory httpClientFactory = new MyHttpClientFactory();

var pca = PublicClientApplicationBuilder.Create(MsalTestConstants.ClientId) 
                                        .WithHttpClientFactory(httpClientFactory)
                                        .Build();
```

## <a name="httpclient-and-xamarin-ios"></a>HttpClient 和 Xamarin iOS
使用 Xamarin iOS 時，建議建立 `HttpClient`，針對 iOS 7 及更新版本明確地使用以 `NSURLSession` 為基礎的處理常式。 MSAL.NET 會自動建立 `HttpClient`，以針對 iOS 7 及更新版本使用 `NSURLSessionHandler`。 如需詳細資訊，請參閱[適用於 HttpClient 的 Xamarin iOS 文件](/xamarin/cross-platform/macios/http-stack)。