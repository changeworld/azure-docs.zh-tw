---
title: Azure 應用程式組態 REST API 一致性
description: 使用 Azure 應用程式組態 REST API 來確保即時一致性的參考頁面
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932620"
---
# <a name="real-time-consistency"></a>即時一致性

由於某些分散式系統的本質，要求之間的即時一致性很難隱含地強制執行。 解決方案是以多個同步處理權杖的形式允許通訊協定支援。 同步處理權杖是選擇性的。

## <a name="initial-request"></a>初始要求

若要保證不同用戶端實例與要求之間的即時一致性，請使用選擇性的 `Sync-Token` 要求和回應標頭。

語法：

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|參數|描述|
|--|--|
| `<id>` | 標記識別項 (不透明)  |
| `<value>` | 標記值 (不透明) 。 允許 base64 編碼字串。 |
| `<sn>` |  (版本) 的 Token 序號。 更高表示相同權杖的較新版本。 可提供更佳的平行存取和用戶端快取。 用戶端可以選擇只使用權杖的最新版本，因為權杖版本是內含的。 要求不需要這個參數。 |

## <a name="response"></a>回應

服務會提供 `Sync-Token` 每個回應的標頭。

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>後續要求

任何後續的要求都可保證與提供的相關的即時一致回應 `Sync-Token` 。

```http
Sync-Token: <id>=<value>
```

如果您省略要求中的 `Sync-Token` 標頭，則服務可能會在短時間內回應快取的資料，在這段期間內，會在內部進行 () 。 如果在讀取前立即發生變更，此行為可能會造成不一致的讀取。

## <a name="multiple-sync-tokens"></a>多個同步處理-權杖

伺服器可能會回應單一要求的多個同步處理權杖。 為了保持下一個要求的即時一致性，用戶端必須回應所有收到的同步處理權杖。 多個標頭值必須以逗號分隔。

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
