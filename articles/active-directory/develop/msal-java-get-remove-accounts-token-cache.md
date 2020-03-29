---
title: 獲取&從權杖緩存 （MSAL4j） 中刪除帳戶 |蔚藍
titleSuffix: Microsoft identity platform
description: 瞭解如何使用 JAVA 的 Microsoft 身份驗證庫從權杖緩存中查看和刪除帳戶。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696192"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>使用 JAVA 的 MSAL 從權杖緩存獲取和刪除帳戶

預設情況下，JAVA 的 MSAL 提供記憶體內權杖緩存。 記憶體中權杖緩存持續應用程式實例的持續時間。

## <a name="see-which-accounts-are-in-the-cache"></a>查看緩存中哪些帳戶

可以通過調用`PublicClientApplication.getAccounts()`緩存中哪些帳戶來檢查以下示例所示：

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>從緩存中刪除帳戶

要從緩存中刪除帳戶，請查找需要刪除的帳戶，然後調用`PublicClientApplicatoin.removeAccount()`如下示例所示：

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>深入了解

如果使用 MSAL 進行 JAVA，則瞭解[JAVA 的 MSAL 中的自訂權杖緩存序列化](msal-java-token-cache-serialization.md)。
