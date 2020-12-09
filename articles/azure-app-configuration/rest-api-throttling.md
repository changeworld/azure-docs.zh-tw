---
title: Azure 應用程式組態 REST API-節流
description: 使用 Azure 應用程式組態時瞭解節流的參考頁面 REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b4472fdcacfe5b62c0cded089224c6d41e32f47d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932433"
---
# <a name="throttling"></a>節流

設定存放區對其可能提供的要求有限制。 任何超過設定存放區之配置配額的要求都會收到 HTTP 429 (太多要求) 回應。

節流會分成不同的配額原則：

- **總要求** 數-要求總數
- **總頻寬** -輸出資料（位元組）
- **儲存體** -使用者資料的儲存體大小總計（以位元組為單位）

## <a name="handling-throttled-responses"></a>處理節流回應

當達到指定配額的速率限制時，伺服器會以 _429_ 狀態碼回應該類型的進一步要求。 _429_ 回應會包含以) 毫秒為單位的 _重試_ 標頭，為用戶端提供建議的等候 (時間，以毫秒為單位，以允許要求配額進行補充。

```http
HTTP/1.1 429 (Too Many Requests)
retry-after-ms: 10
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/too-many-requests",
  "title": "Resource utilization has surpassed the assigned quota",
  "policy": "Total Requests",
  "status": 429
}
```

在上述範例中，用戶端已超過其允許的配額，建議您在嘗試任何進一步的要求之前，先減慢並等候10毫秒。 用戶端也應該考慮漸進式輪詢。

## <a name="other-retry"></a>其他重試

服務可能會找出需要用戶端重試的節流以外的情況 (例如：503服務無法使用) 。 在這類情況下， `retry-after-ms` 將會提供回應標頭。 為了提高穩定性，建議用戶端遵循建議的間隔，然後執行重試。

```http
HTTP/1.1 503 Service Unavailable
retry-after-ms: 787
```
