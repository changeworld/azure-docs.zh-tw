---
title: 取得 & 從權杖快取中移除帳戶（MSAL4j） |Azure
titleSuffix: Microsoft identity platform
description: 瞭解如何使用適用于 JAVA 的 Microsoft 驗證程式庫，從權杖快取中查看和移除帳戶。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76696192"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>使用適用于 JAVA 的 MSAL 從權杖快取取得和移除帳戶

MSAL for JAVA 預設會提供記憶體中的權杖快取。 記憶體中的權杖快取會持續在應用程式實例的持續時間內。

## <a name="see-which-accounts-are-in-the-cache"></a>查看快取中的帳戶

您可以藉由呼叫`PublicClientApplication.getAccounts()`來檢查快取中的帳戶，如下列範例所示：

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>從快取中移除帳戶

若要從快取中移除帳戶，請尋找需要移除的帳戶，然後呼叫`PublicClientApplicatoin.removeAccount()` ，如下列範例所示：

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>深入了解

如果您使用 MSAL for JAVA，請瞭解[MSAL For java 中的自訂權杖](msal-java-token-cache-serialization.md)快取序列化。
