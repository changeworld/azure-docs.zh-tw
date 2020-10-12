---
title: 計量服務 API - Microsoft 商業市集
description: 使用方式事件 API 可讓您針對 Microsoft AppSource 和 Azure Marketplace 中的 SaaS 供應項目發出使用方式事件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ac48973653e89d43521979a5606a8a3a3c2e1346
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319978"
---
# <a name="marketplace-metered-billing-apis"></a>Marketplace 計量付費 Api

當發行者針對要在合作夥伴中心中發佈的供應專案建立自訂計量維度時，應使用計量計費 Api。 任何有一或多個方案具有自訂維度以發出使用事件的已購買供應專案，都需要與計量付費 Api 整合。

如需建立 SaaS 自訂計量維度的詳細資訊，請參閱 [saas 計量付費](saas-metered-billing.md)。

如需有關使用受管理的應用程式方案為 Azure 應用程式供應專案建立自訂計量維度的詳細資訊，請參閱 [建立新的 Azure 應用程式供應](create-new-azure-apps-offer.md#technical-configuration) 專案) 的技術設定一節。

## <a name="enforcing-tls-12-note"></a>強制執行 TLS 1.2 附注

TLS 1.2 版是以 HTTPS 通訊的最基本版本來強制執行。 請務必在您的程式碼中使用此 TLS 版本。 TLS 版本1.0 和1.1 已淘汰，且將拒絕連線嘗試。

## <a name="metered-billing-single-usage-event"></a>計量付費單一使用事件

使用事件 API 應該由發行者呼叫，以針對使用中的資源發出使用事件 (針對特定客戶購買的方案) 。 發佈供應專案時，「發行者」所定義之計畫的每個自訂維度都會分別發出「使用事件」。

每小時的行事曆日期只能發出一個使用事件。 例如，在目前的8：上午5:15 執行，您可以發出一個使用事件。 如果接受此事件，則會從今天上午9:00 開始接受下一個使用事件。 如果您今天在8:15 和8:59:59 之間傳送額外的事件，則會被拒絕為重複的事件。 您應累積在一小時內耗用的所有單位，然後在單一事件中發出。

每個資源的日曆日每小時只能發出一個使用事件。 如果一小時內耗用一個以上的單位，則會累積該小時內耗用的所有單位，然後在單一事件中發出。 使用事件只能在過去24小時內發出。 如果您在8:00 和8:59:59 之間的任何時間都發出使用事件 (並且接受) 並在8:00 和8:59:59 之間的相同日期傳送額外事件，則會被拒絕為重複專案。

**POST**：`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

查詢參數：

| 參數 | 建議          |
| ---------- | ---------------------- |
| `ApiVersion` | 使用2018-08-31。 |
| | |

要求標頭：

| Content-Type       | 使用`application/json`  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | 用於追蹤用戶端要求的唯一字串值，最好是 GUID。 如果未提供此值，則回應標頭中會產生並提供一個。 |
| `x-ms-correlationid` | 用於用戶端作業的唯一字串值。 此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`   | 識別正在進行此 API 呼叫之 ISV 的唯一存取權杖。 格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如下所述： <br> <ul> <li> 中的 SaaS [可透過 HTTP POST 取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)。 </li> <li> [驗證策略](./marketplace-metering-service-authentication.md)中的受控應用程式。 </li> </ul> |
| | |

*要求主體範例：*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId` 對於 SaaS 應用程式和發出自訂計量的受控應用程式，有不同的意義。 

針對 Azure 應用程式受控應用程式方案，`resourceId` 為可在受控應用程式中繼資料物件的 `billingDetails` 底下找到的 `resourceUsageId`。 用來加以擷取的範例指令碼，可在[使用 Azure 受控識別權杖](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到。 

針對 SaaS 供應項目，`resourceId` 為 SaaS 訂用帳戶識別碼。 如需 SaaS 訂用帳戶的詳細資料，請參閱[列出訂用帳戶](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>回應

程式碼：200<br>
正常。 會在 Microsoft 端接受並記錄使用方式，以進行進一步的處理和帳單。

回應承載範例： 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

程式碼：400 <br>
不正確的要求。

* 提供的要求資料遺失或無效。
* `effectiveStartTime` 超過24小時。 事件已過期。
* SaaS 訂用帳戶不在訂閱狀態。

回應承載範例： 

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

禁止。 未提供授權權杖、無效或已過期。  或者，要求嘗試存取的訂用帳戶，其發行的供應專案與用來建立授權權杖的帳戶不同 Azure AD App 識別碼。

程式碼：409<br>
衝突。 已成功報告指定之資源識別碼的使用事件、有效的使用日期和小時。

回應承載範例： 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>計量付費批次使用事件

批次使用事件 API 可讓您一次發出多個已購買資源的使用事件。 它也可讓您針對相同的資源發出數個使用事件，只要它們是針對不同的行事歷時間即可。 單一批次中的事件數目上限為25。

**POST：** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

查詢參數：

| 參數  | 建議     |
| ---------- | -------------------- |
| `ApiVersion` | 使用2018-08-31。 |

要求標頭：

| Content-Type       | 使用`application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | 用於追蹤用戶端要求的唯一字串值，最好是 GUID。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `x-ms-correlationid` | 用於用戶端作業的唯一字串值。 此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`      | 識別正在進行此 API 呼叫之 ISV 的唯一存取權杖。 格式是 `Bearer <access_token>` 發行者抓取權杖值的時間，如下所述： <br> <ul> <li> 中的 SaaS [可透過 HTTP POST 取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)。 </li> <li> [驗證策略](./marketplace-metering-service-authentication.md)中的受控應用程式。 </li> </ul> |
| | |


*要求主體範例：*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId` 對於 SaaS 應用程式和發出自訂計量的受控應用程式，有不同的意義。 

針對 Azure 應用程式受控應用程式方案，`resourceId` 為可在受控應用程式中繼資料物件的 `billingDetails` 底下找到的 `resourceUsageId`。 用來加以擷取的範例指令碼，可在[使用 Azure 受控識別權杖](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到。 

針對 SaaS 供應項目，`resourceId` 為 SaaS 訂用帳戶識別碼。 如需 SaaS 訂用帳戶的詳細資料，請參閱[列出訂用帳戶](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>回應

程式碼：200<br>
正常。 批次使用量會在 Microsoft 端接受並記錄，以供進一步處理和計費。 回應清單會以批次中每個個別事件的狀態傳回。 您應逐一查看回應承載，以瞭解批次事件中所傳送之每個個別使用事件的回應。

回應承載範例： 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

在 `BatchUsageEvent` API 回應中所參考之狀態碼的描述：

| 狀態碼  | 描述 |
| ---------- | -------------------- |
| `Accepted` | 已接受。 |
| `Expired` | 過期的使用方式。 |
| `Duplicate` | 已提供重複的使用方式。 |
| `Error` | 錯誤碼。 |
| `ResourceNotFound` | 提供的使用方式資源無效。 |
| `ResourceNotAuthorized` | 您未獲授權以提供此資源的使用方式。 |
| `InvalidDimension` | 傳遞使用方式的維度對此供應項目/方案無效。 |
| `InvalidQuantity` | 傳遞的數量小於或等於0。 |
| `BadArgument` | 輸入遺失或格式不正確。 |

程式碼：400<br>
不正確的要求。 批次包含超過25個使用事件。

程式碼：403<br>
禁止。 未提供授權權杖、無效或已過期。  或者，要求嘗試存取的訂用帳戶，其發行的供應專案與用來建立授權權杖的帳戶不同 Azure AD App 識別碼。

## <a name="development-and-testing-best-practices"></a>開發和測試的最佳作法

若要測試發出的自訂計量，請執行與計量 API 的整合、為已發佈的 SaaS 供應專案建立一個方案，並在其中定義的自訂維度中，每個單位為零的價格。 並將此供應專案發佈為預覽版，讓只有有限的使用者能夠存取及測試整合。

您也可以針對現有的 live 供應專案使用私用方案，在測試至有限物件的期間限制對此方案的存取。

## <a name="get-support"></a>取得支援

遵循 [合作夥伴中心中的「商業 marketplace 方案支援](./support.md) 」中的指示，瞭解發行者支援選項，並向 Microsoft 提出支援票證。

## <a name="next-steps"></a>接下來的步驟

如需有關計量服務 Api 的詳細資訊，請參閱 [Marketplace 計量服務 API 常見問題](./marketplace-metering-service-apis-faq.md)。
