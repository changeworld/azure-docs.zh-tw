---
title: 應用商店計量服務 API |Azure 應用商店
description: Azure 應用商店中 SaaS 提供的使用事件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275742"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace 計量服務 API

使用事件 API 允許您為特定採購的實體發出使用事件。 使用事件請求引用發行者在發佈產品/服務時定義的計量服務維度。

## <a name="usage-event"></a>使用事件

**帖子**：`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*查詢參數：*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 要用於此要求的作業版本。 最新 API 版本為 2018-08-31。 |

*請求標頭：*

| Content-Type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用於跟蹤來自用戶端的請求的唯一字串值，最好是 GUID。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| `x-ms-correlationid` | 用於用戶端操作的唯一字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。 |
| `authorization`   | [獲取 JSON Web 權杖 （JWT） 無記名權杖。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：在發出 HTTP 要求時`Bearer`，首碼從引用的連結獲取的權杖。 |

*請求：*

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

代碼： 200<br>
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

代碼： 400 <br>
請求錯誤、提供或不正確資料或過期

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

代碼： 403<br>
請求錯誤、提供或不正確資料或過期

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

代碼： 409<br>
衝突，當我們收到使用資源識別碼 的使用調用時，以及已經存在的有效使用方式。 回應將包含`additionalInfo`包含有關接受郵件的資訊的欄位。

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

## <a name="batch-usage-event"></a>批次處理使用事件

批次處理使用事件 API 允許您一次為多個採購實體發出使用事件。 批次處理使用方式事件請求引用發行者在發佈產品/服務時定義的計量服務維度。

>[!Note]
>您可以在 Microsoft 的商業市場註冊多個 SaaS 產品/ 每個已註冊的 SaaS 產品/服務都有一個為身份驗證和授權目的註冊的唯一 Azure AD 應用程式。 在註冊產品/服務時，批量發出的事件應屬於具有相同 Azure AD 應用程式的優惠。

**帖子：**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*查詢參數：*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 要用於此要求的作業版本。 最新 API 版本為 2018-08-31。 |

*請求標頭：*

| Content-Type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用於跟蹤來自用戶端的請求的唯一字串值，最好是 GUID。 如果未提供此值，將生成一個值，並在回應標頭中提供。 |
| `x-ms-correlationid` | 用於用戶端操作的唯一字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將生成一個值，並在回應標頭中提供。 |
| `authorization`      | [獲取 JSON Web 權杖 （JWT） 無記名權杖。](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) 注意：在發出 HTTP 要求時`Bearer`，首碼從引用的連結獲取的權杖。  |

*請求：*
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

代碼： 200<br>
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

API 回應中`BatchUsageEvent`引用的狀態碼的說明：

| 狀態碼  | 描述 |
| ---------- | -------------------- |
| `Accepted` | 已接受代碼。 |
| `Expired` | 過期使用。 |
| `Duplicate` | 提供重複用法。 |
| `Error` | 錯誤碼。 |
| `ResourceNotFound` | 提供的使用資源無效。 |
| `ResourceNotAuthorized` | 您無權提供此資源的使用方式。 |
| `InvalidDimension` | 傳遞使用量的維度對於此產品/計畫無效。 |
| `InvalidQuantity` | 通過的數量為 0 <。 |
| `BadArgument` | 輸入丟失或格式不正確。 |

代碼： 400<br>
錯誤請求、提供的資料丟失或無效或已過期

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
代碼： 403<br>
使用者未經授權進行此呼叫

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>後續步驟

有關詳細資訊，請參閱[SaaS 計量計費](./saas-metered-billing.md)。
