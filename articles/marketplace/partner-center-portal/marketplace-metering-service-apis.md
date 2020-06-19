---
title: 計量服務 API - Microsoft 商業市集
description: 使用方式事件 API 可讓您針對 Microsoft AppSource 和 Azure Marketplace 中的 SaaS 供應項目發出使用方式事件。
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725460"
---
# <a name="marketplace-metering-service-apis"></a>Marketplace 計量服務 API

使用方式事件 API 可讓您針對特定的已購買實體發出使用方式事件。 使用方式事件要求會參考發行者在發佈供應項目時所定義的計量服務維度。

## <a name="usage-event"></a>使用方式事件

**POST**：`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

查詢參數：

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | 要用於此要求的作業版本。 最新的 API 版本為 2018-08-31。 |

要求標頭：

| Content-Type       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用於追蹤用戶端要求的唯一字串值，最好是 GUID。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| `x-ms-correlationid` | 用於用戶端作業的唯一字串值。 此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`   | [取得 JSON Web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) \(部分機器翻譯\) 注意:進行 HTTP 要求時，請將 `Bearer` 作為從參考連結取得之權杖的前置詞。 |

>[!Note]
>針對 Azure 應用程式受控應用程式方案，`resourceId` 為可在受控應用程式中繼資料物件的 `billingDetails` 底下找到的 `resourceUsageId`。  用來加以擷取的範例指令碼，可在[使用 Azure 受控識別權杖](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到。  針對 SaaS 供應項目，`resourceId` 為 SaaS 訂用帳戶識別碼。  如需 SaaS 訂用帳戶的詳細資料，請參閱[列出訂用帳戶](./pc-saas-fulfillment-api-v2.md#list-subscriptions)。

*要求：*

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

程式碼：200<br>
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

程式碼：400 <br>
不正確的要求、所提供的資料遺失或無效或是已經過期

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

程式碼：403<br>
不正確的要求、所提供的資料遺失或無效或是已經過期

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

程式碼：409<br>
在我們接收到針對使用方式資源識別碼及有效使用方式 (其已經存在) 的使用方式呼叫時，發生衝突。 回應將會包含 `additionalInfo` 欄位，其中包含關於已接受訊息的資訊。

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

## <a name="batch-usage-event"></a>批次使用方式事件

批次使用方式事件 API 可讓您針對一或多個已購買實體同時發出使用方式事件。 批次使用方式事件要求會參考發行者在發佈供應項目時所定義的計量服務維度。

>[!Note]
>您可以在 Microsoft 的商業市集中註冊多個 SaaS 供應項目。 每個已註冊的 SaaS 供應項目都具有唯一的 Azure AD 應用程式，其已針對驗證和授權目的進行註冊。 以批次方式發出的事件應該要屬於在註冊供應項目期間具有相同 Azure AD 應用程式的供應項目。

**POST：** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

查詢參數：

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | 要用於此要求的作業版本。 最新的 API 版本為 2018-08-31。 |

要求標頭：

| Content-Type       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用於追蹤用戶端要求的唯一字串值，最好是 GUID。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `x-ms-correlationid` | 用於用戶端作業的唯一字串值。 此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`      | [取得 JSON Web 權杖 (JWT) 持有人權杖](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) \(部分機器翻譯\) 注意:進行 HTTP 要求時，請將 `Bearer` 作為從參考連結取得之權杖的前置詞。  |

*要求：*
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

程式碼：200<br>
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

在 `BatchUsageEvent` API 回應中所參考之狀態碼的描述：

| 狀態碼  | 描述 |
| ---------- | -------------------- |
| `Accepted` | 已接受程式碼。 |
| `Expired` | 過期的使用方式。 |
| `Duplicate` | 已提供重複的使用方式。 |
| `Error` | 錯誤碼。 |
| `ResourceNotFound` | 提供的使用方式資源無效。 |
| `ResourceNotAuthorized` | 您未獲授權以提供此資源的使用方式。 |
| `InvalidDimension` | 傳遞使用方式的維度對此供應項目/方案無效。 |
| `InvalidQuantity` | 傳遞的數量為 < 0。 |
| `BadArgument` | 輸入遺失或格式不正確。 |

程式碼：400<br>
不正確的要求、所提供的資料遺失或無效或是已經過期

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
程式碼：403<br>
使用者未獲授權以進行此呼叫

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱 [SaaS 計量帳單](./saas-metered-billing.md)。
