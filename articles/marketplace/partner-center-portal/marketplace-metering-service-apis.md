---
title: 計量服務 API - Microsoft 商業市集
description: 使用方式事件 API 可讓您針對 Microsoft AppSource 和 Azure Marketplace 中的 SaaS 供應項目發出使用方式事件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 8a6636b0fc6c3e67ec171d738efb3fd8a93de30c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120767"
---
# <a name="marketplace-metered-billing-apis"></a>Marketplace 計量付費計費 Api

當發行者建立要在合作夥伴中心發佈之供應專案的自訂計量維度時，應該使用計量付費計費 Api。 任何已購買的供應專案都需要與計量付費 Api 整合，其中有一或多個具有自訂維度的方案可發出使用事件。

如需建立 SaaS 之自訂計量維度的詳細資訊，請參閱[saas 計量付費計費](saas-metered-billing.md)。

如需有關使用受管理的應用程式方案為 Azure 應用程式供應專案建立自訂計量維度的詳細資訊，請參閱[建立新的 Azure 應用程式供應專案的技術設定一節](create-new-azure-apps-offer.md#technical-configuration)。

## <a name="enforcing-tls-12-note"></a>強制執行 TLS 1.2 注意事項

TLS 版本1.2 版本會強制做為 HTTPS 通訊的最低版本。 請確定您在程式碼中使用此 TLS 版本。 TLS 版本1.0 和1.1 已被取代，且將拒絕連接嘗試。

## <a name="metered-billing-single-usage-event"></a>計量付費單一使用事件

使用事件 API 應由發行者呼叫，以便針對特定客戶所購買的計畫，針對使用中的資源（訂閱）發出使用量事件。 發行供應專案時，會針對發行者所定義之計畫的每個自訂維度，分別發出 usage 事件。

每個日曆天的每小時只能發出一個使用事件。 例如，在今天的8：上午5:15 執行，您可以發出一個使用事件。 如果接受此事件，則會從今天上午9:00 接受下一個使用事件。 如果您在今天的8:15 和8:59:59 之間傳送額外的事件，它將會被視為重複而遭到拒絕。 您應該累計一小時內耗用的所有單位，然後在單一事件中發出。

每個資源的行事曆日每小時只能發出一個使用事件。 如果一個小時內耗用一個以上的單位，則會累積在該小時內取用的所有單位，然後在單一事件中發出。 使用事件只能在過去24小時內發出。 如果您在8:00 和8:59:59 之間的任何時間發出使用事件（並接受它），並在8:00 和8:59:59 之間的同一天傳送額外的事件，則會將其視為重複而遭到拒絕。

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
| `authorization`   | 唯一的存取權杖，可識別進行此 API 呼叫的 ISV。 格式是發行者抓取權杖值的時機， `"Bearer <access_token>"` 如下所述： <br> <ul> <li> 中的 SaaS 會[取得具有 HTTP POST 的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)。 </li> <li> [驗證策略](./marketplace-metering-service-authentication.md)中的受控應用程式。 </li> </ul> |
| | |

*要求本文範例：*

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
>`resourceId`對 SaaS 應用程式和發出自訂計量的受控應用程式有不同的意義。 

針對 Azure 應用程式受控應用程式方案，`resourceId` 為可在受控應用程式中繼資料物件的 `billingDetails` 底下找到的 `resourceUsageId`。 用來加以擷取的範例指令碼，可在[使用 Azure 受控識別權杖](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到。 

針對 SaaS 供應項目，`resourceId` 為 SaaS 訂用帳戶識別碼。 如需 SaaS 訂用帳戶的詳細資料，請參閱[列出訂用帳戶](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>回應

程式碼：200<br>
正常。 Microsoft 端已接受並記錄使用方式，以供進一步處理和計費。

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
* `effectiveStartTime`過去24小時以上。 事件已過期。
* SaaS 訂用帳戶不是處於已訂閱狀態。

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

禁止。 未提供授權權杖、無效或已過期。  或者，要求嘗試存取的訂用帳戶，是使用與用來建立授權權杖的不同 Azure AD App 識別碼所發行的供應專案。

程式碼：409<br>
衝突。 已針對指定的資源識別碼、有效的使用日期和小時，成功回報使用事件。

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

Batch 使用量事件 API 可讓您一次發出一個以上已購買資源的使用事件。 它也可讓您針對相同的資源發出數個使用事件，前提是它們是針對不同的行事歷時數。 單一批次中的最大事件數目是25。

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
| `authorization`      | 唯一的存取權杖，可識別進行此 API 呼叫的 ISV。 格式是發行者抓取權杖值的時機， `Bearer <access_token>` 如下所述： <br> <ul> <li> 中的 SaaS 會[取得具有 HTTP POST 的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)。 </li> <li> [驗證策略](./marketplace-metering-service-authentication.md)中的受控應用程式。 </li> </ul> |
| | |


*要求本文範例：*

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
>`resourceId`對 SaaS 應用程式和發出自訂計量的受控應用程式有不同的意義。 

針對 Azure 應用程式受控應用程式方案，`resourceId` 為可在受控應用程式中繼資料物件的 `billingDetails` 底下找到的 `resourceUsageId`。 用來加以擷取的範例指令碼，可在[使用 Azure 受控識別權杖](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token)中找到。 

針對 SaaS 供應項目，`resourceId` 為 SaaS 訂用帳戶識別碼。 如需 SaaS 訂用帳戶的詳細資料，請參閱[列出訂用帳戶](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions)。

### <a name="responses"></a>回應

程式碼：200<br>
正常。 已接受並記錄在 Microsoft 端的批次使用方式，以供進一步處理和計費。 傳回的回應清單會包含批次中每個個別事件的狀態。 您應該逐一查看回應裝載，以瞭解批次事件中所傳送之每個個別使用事件的回應。

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
禁止。 未提供授權權杖、無效或已過期。  或者，要求嘗試存取的訂用帳戶，是使用與用來建立授權權杖的不同 Azure AD App 識別碼所發行的供應專案。

## <a name="development-and-testing-best-practices"></a>開發和測試的最佳作法

若要測試所發出的自訂計量，請執行與計量 API 的整合、為已發佈的 SaaS 供應專案建立一個方案，並以其定義的自訂維度（每單位零價格）。 並將此供應專案發佈為預覽，讓只有有限的使用者能夠存取和測試整合。

您也可以使用現有 live 供應專案的私用方案，在測試期間限制對此方案的存取權。

## <a name="get-support"></a>取得支援

請遵循[合作夥伴中心的商業 marketplace 方案支援](./support.md)中的指示，以瞭解發行者支援選項，並向 Microsoft 提出支援票證。

## <a name="next-steps"></a>後續步驟

如需計量服務 Api 的詳細資訊，請參閱[Marketplace 計量服務 API 常見問題](./marketplace-metering-service-apis-faq.md)。
