---
title: 自訂權杖緩存序列化 （MSAL4j）
titleSuffix: Microsoft identity platform
description: 瞭解如何序列化 JAVA 的 MSAL 權杖緩存
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696158"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>JAVA 的 MSAL 中的自訂權杖緩存序列化

要在應用程式的實例之間保留權杖緩存，您需要自訂序列化。 權杖緩存序列化中涉及的 JAVA 類和介面如下：

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html)：表示安全權杖緩存的介面。
- [ITokenCacheAccessAspect：](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html)表示訪問前後執行代碼的操作的介面。 在@Override*CacheAccess*和*CacheAccess之後*，您將使用負責序列化和反序列化緩存的邏輯。
- [ITokenCacheCoNtext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html)：表示訪問權杖緩存的上下文的介面。 

下面是權杖緩存序列化/反序列化自訂序列化的天真實現。 不要複製並將其粘貼到生產環境中。

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>深入了解

瞭解如何[使用 JAVA 的 MSAL 從權杖緩存獲取和刪除帳戶](msal-java-get-remove-accounts-token-cache.md)。
