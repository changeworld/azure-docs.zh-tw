---
title: 自訂權杖快取序列化（MSAL4j）
titleSuffix: Microsoft identity platform
description: 瞭解如何序列化適用于 JAVA 的 MSAL 的權杖快取
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696158"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>MSAL for JAVA 中的自訂權杖快取序列化

若要保存應用程式實例之間的權杖快取，您將需要自訂序列化。 與權杖快取序列化相關的 JAVA 類別和介面如下所示：

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html)：代表安全性權杖快取的介面。
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html)：代表在存取之前和之後執行程式碼之作業的介面。 您會使用負責序列化和還原序列化快取的邏輯，來 @Override *beforeCacheAccess*和*afterCacheAccess* 。
- [ITokenCacheCoNtext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html)：代表存取權杖快取之內容的介面。 

以下是權杖快取序列化/還原序列化的自訂序列化的簡單實作為方式。 請勿將此複本複製並貼到生產環境中。

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

## <a name="learn-more"></a>了解更多

瞭解如何[使用適用于 JAVA 的 MSAL，從權杖快取取得和移除帳戶](msal-java-get-remove-accounts-token-cache.md)。
