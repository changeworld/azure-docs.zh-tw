---
title: 提供 HttpClient & proxy (MSAL.NET) |蔚藍
titleSuffix: Microsoft identity platform
description: 了解如何使用 Microsoft Authentication Library for .NET (MSAL.NET)，來提供您自己的 HttpClient 和 Proxy 以連線到 Azure AD。
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
ms.openlocfilehash: 6099bfe339ae398e4683f17a4397656a4507b6ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166003"
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