---
title: 計量服務 Api-Microsoft 商業 marketplace
description: 使用事件 API 可讓您在 Microsoft AppSource 和 Azure Marketplace 中發出 SaaS 供應專案的使用事件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 159d2c60fc1fc5ad1f21f2b948208eaae0d06208
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857862"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace 計量服務 API

使用事件 API 可讓您發出特定已購買實體的使用事件。 當發佈供應專案時，usage 事件要求會參考發行者所定義的計量服務維度。

## <a name="usage-event"></a>使用事件

**POST**：`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*查詢參數：*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 要用於此要求的作業版本。 最新的 API 版本為2018-08-31。 |

*要求標頭：*

| Content-Type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用於追蹤用戶端要求的唯一字串值，最好是 GUID。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| `x-ms-correlationid` | 用戶端運算的唯一字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
| `authorization`   | [取得 JSON web 權杖（JWT）持有人權杖。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：提出 HTTP 要求時，請在`Bearer`從參考的連結取得的權杖前置詞。 |

*邀請*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>回應

代碼：200<br>
[確定] 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

代碼：400 <br>
不正確的要求、遺漏或不正確資料已提供或過期

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

代碼：403<br>
不正確的要求、遺漏或不正確資料已提供或過期

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

代碼：409<br>
當我們收到使用量資源識別碼的使用量呼叫，以及已存在的有效使用方式時，就會發生衝突。 回應會包含`additionalInfo`欄位，其中包含已接受訊息的相關資訊。

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>批次使用事件

Batch 使用量事件 API 可讓您一次發出一個以上已購買實體的使用事件。 當發佈供應專案時，批次使用事件要求會參考發行者所定義的計量服務維度。

>[!Note]
>您可以在 Microsoft 的商業 marketplace 中註冊多個 SaaS 供應專案。 每個已註冊的 SaaS 供應專案都有唯一的 Azure AD 應用程式，並已針對驗證和授權目的進行註冊。 在註冊供應專案時，批次中發出的事件應該屬於具有相同 Azure AD 應用程式的供應專案。

**POST：**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*查詢參數：*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 要用於此要求的作業版本。 最新的 API 版本為2018-08-31。 |

*要求標頭：*

| Content-Type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用於追蹤用戶端要求的唯一字串值，最好是 GUID。 如果未提供此值，則會產生一個，並在回應標頭中提供。 |
| `x-ms-correlationid` | 用戶端運算的唯一字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個，並在回應標頭中提供。 |
| `authorization`      | [取得 JSON web 權杖（JWT）持有人權杖。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：提出 HTTP 要求時，請在`Bearer`從參考的連結取得的權杖前置詞。  |

*邀請*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>回應

代碼：200<br>
[確定]

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

API 回應中`BatchUsageEvent`所參考之狀態碼的描述：

| 狀態碼  | Description |
| ---------- | -------------------- |
| `Accepted` | 接受的程式碼。 |
| `Expired` | 過期的使用量。 |
| `Duplicate` | 提供重複的使用方式。 |
| `Error` | 錯誤碼。 |
| `ResourceNotFound` | 提供的使用資源無效。 |
| `ResourceNotAuthorized` | 您未獲授權，無法為此資源提供使用方式。 |
| `InvalidDimension` | 傳遞使用量的維度對此供應專案/方案無效。 |
| `InvalidQuantity` | 傳遞的數量為 < 0。 |
| `BadArgument` | 輸入遺漏或格式不正確。 |

代碼：400<br>
不正確的要求、遺漏或不正確資料已提供或過期

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
代碼：403<br>
使用者未獲授權，無法進行此呼叫

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱[SaaS 計量付費計費](./saas-metered-billing.md)。
