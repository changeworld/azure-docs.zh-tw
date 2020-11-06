---
title: Azure 應用程式組態 REST API 一致性
description: 使用 Azure 應用程式組態 REST API 確保即時一致性的參考頁面
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424102"
---
# <a name="real-time-consistency"></a>即時一致性

由於某些分散式系統的本質，要求之間的即時一致性很難隱含地強制執行。 解決方案是以多個 **同步處理權杖** 的形式允許通訊協定支援。 同步處理權杖是選擇性的。

## <a name="initial-request"></a>初始要求

為了保證不同用戶端實例和要求之間的即時一致性，請使用選擇性的 `Sync-Token` 要求/回應標頭。

語法：

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|參數|描述|
|--|--|
| `<id>` | 標記識別項 (不透明)  |
| `<value>` | 標記值 (不透明) 。 允許 base64 編碼字串 |
| `<sn>` |  (版本) 的 Token 序號。 較高表示相同權杖的較新版本。 可提供更佳的平行存取和用戶端快取。 用戶端可能會選擇只使用權杖的最新版本，因為權杖版本是內含的。 要求不需要。 |

## <a name="response"></a>回應

服務會提供 `Sync-Token` 每個回應的標頭。

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>後續要求

任何後續的要求都可保證與提供的相關的 **即時** 一致回應 `Sync-Token` 。

```http
Sync-Token: <id>=<value>
```

如果 `Sync-Token` 要求中省略標頭，則服務可能會在短時間內回應快取的資料，在這段期間內，會在內部進行 (的時間) 。 如果在讀取前立即發生變更，此行為可能會造成不一致的讀取。

## <a name="multiple-sync-tokens"></a>多個同步處理-權杖

伺服器可能會以單一要求的多個同步權杖回應。 為了保持下一個要求的 **即時** 一致性，用戶端必須回應所有收到的同步權杖。 根據 RFC，多個標頭值必須以逗號分隔。

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
