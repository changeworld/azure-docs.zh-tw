---
title: Microsoft 商業 marketplace 中的 SaaS 履行 Api v2
description: 瞭解如何使用履行 Api 第2版來建立和管理 Microsoft AppSource 和 Azure Marketplace 上的 SaaS 供應專案。
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: 8f452349c0d636bc74a3722e2526623c955093f7
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854201"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 中的 SaaS 履行 Api 第2版

本文詳細說明履行 Api，讓合作夥伴能夠在 Microsoft AppSource 和 Azure Marketplace 中銷售其 SaaS 應用程式。 Transactable SaaS 供應專案需要這些 Api。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 訂閱生命週期

Azure SaaS 會管理 SaaS 訂閱購買的整個生命週期。 它使用履行 Api 做為驅動實際履行、方案變更，以及與夥伴刪除訂用帳戶的機制。 客戶的帳單是以 Microsoft 維護的 SaaS 訂用帳戶狀態為基礎。 下圖說明在狀態之間進行變更的狀態和作業。

![SaaS 訂用帳戶生命週期狀態](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 訂用帳戶的狀態

下表列出 SaaS 訂閱的布建狀態，包括每個訂用帳戶的描述和順序圖表（如果適用）。 

#### <a name="provisioning"></a>佈建

當客戶開始購買時，合作夥伴會在使用 URL 參數的客戶互動式網頁上，收到授權碼中的這項資訊。 其中一個範例`https://contoso.com/signup?token=..`是，而合作夥伴中心的登陸頁面 URL 是`https://contoso.com/signup`。 您可以藉由呼叫解析 API 來驗證和交換授權碼，以取得布建服務的詳細資料。  當 SaaS 服務完成布建時，它會傳送「啟動」呼叫來通知履行已完成，而且客戶可以計費。 

下圖顯示布建案例的 API 呼叫順序。  

![布建 SaaS 服務的 API 呼叫](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已佈建

此狀態是已布建服務的穩定狀態。

##### <a name="provisioning-for-update"></a>布建以進行更新 

此狀態表示現有服務的更新已暫止。 客戶可以從 marketplace 或 SaaS 服務（僅適用于直接對客戶交易）來起始這類更新。

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>布建更新（從 marketplace 起始時）

下圖顯示從 marketplace 起始更新時的動作順序。

![從 marketplace 起始更新時的 API 呼叫](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>布建以進行更新（從 SaaS 服務起始時）

下圖顯示從 SaaS 服務起始更新時的動作。 （Webhook 呼叫是由 SaaS 服務起始的訂用帳戶更新所取代）。 

![從 SaaS 服務起始更新時的 API 呼叫](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>暫止

此狀態表示尚未收到客戶的付款。 根據原則，我們會在取消訂用帳戶之前，為客戶提供寬限期。 當訂用帳戶處於此狀態時： 

- 身為合作夥伴，您可以選擇降低或封鎖使用者對服務的存取。
- 訂用帳戶必須保持在可復原的狀態，而不會遺失任何資料或設定。 
- 預期會在寬限期結束時，透過履行 Api 或解除布建要求，取得此訂用帳戶的恢復要求。 

#### <a name="unsubscribed"></a>訂閱 

訂閱會達到此狀態，以回應明確的客戶要求或因為未付款。 合作夥伴預期的是，客戶的資料會保留在要求的特定天數內進行復原，然後予以刪除。 


## <a name="api-reference"></a>API 參考資料

本節記載 SaaS 訂用帳戶*api*和*作業 api*。  第2版 Api `api-version`的參數值為`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>參數和實體定義

下表列出履行 Api 所使用的一般參數和實體的定義。

|     實體/參數     |     定義                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 資源的 GUID 識別碼。  |
| `name`                   | 客戶為此資源提供的易記名稱。 |
| `publisherId`            | 每一個發行者的唯一字串識別碼（例如： "contoso"）。 |
| `offerId`                | 每個供應專案的唯一字串識別碼（例如： "offer1"）。  |
| `planId`                 | 每個方案/SKU 的唯一字串識別碼（例如：「銀級」）。 |
| `operationId`            | 特定作業的 GUID 識別碼。  |
|  `action`                | 在資源上執行的動作，可能是`Unsubscribe`、 `Suspend`、 `Reinstate`或`ChangePlan`、 `ChangeQuantity`、 `Transfer`。 |
|   |   |

全域唯一識別碼（[guid](https://en.wikipedia.org/wiki/Universally_unique_identifier)）是通常會自動產生的128位（32-十六進位）數位。 

#### <a name="resolve-a-subscription"></a>解析訂閱 

解析端點可讓發行者將 marketplace 權杖解析為持續性資源識別碼。 資源識別碼是 SaaS 訂用帳戶的唯一識別碼。 當使用者重新導向至合作夥伴的網站時，URL 會在查詢參數中包含一個權杖。 合作夥伴預期會使用此權杖，並提出要求來解決此問題。 回應包含唯一的 SaaS 訂用帳戶識別碼、名稱、供應專案識別碼，以及資源的方案。 此權杖僅限一小時的有效時間。 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>張貼<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。 |
|  x-ms-marketplace-token  |  當使用者從 Azure 重新導向至 SaaS 合作夥伴的網站時，URL 中的權杖查詢參數（例如： `https://contoso.com/signup?token=..`）。 *注意：* URL 會先從瀏覽器解碼 token 值，再加以使用。  |

*回應碼：*

代碼：200<br>
將不透明的 token 解析為 SaaS 訂用帳戶。 回應主體：
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

代碼：400<br>
不正確的要求。 x-ms-marketplace-權杖遺失、格式不正確或已過期。

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。

代碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>訂用帳戶 API

訂用帳戶 API 支援下列 HTTPS 作業： **Get**、 **Post**、 **Patch**和**Delete**。


#### <a name="list-subscriptions"></a>列出訂閱

列出發行者的所有 SaaS 訂閱。

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*查詢參數：*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
| x-ms-correlationid |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
| 授權      |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。  |

*回應碼：*

代碼：200 <br/>
根據驗證 token，取得所有發行者供應專案的發行者和對應訂閱。

>[!Note]
>模擬[api](#mock-apis)會在您第一次開發供應專案時使用，而實際的 api 則需要在實際發佈供應專案時使用。  實際的 Api 和模擬 Api 會因程式碼的第一行而有所不同。  在實際的 API 中，有`subscription`區段，但此區段不存在於 mock api。

Mock API 的回應承載：<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
而對於實際的 API： <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial.(optional field - default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
只有在有其他「頁面」的計畫要抓取時，才會顯示接續 token。 

代碼：403 <br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。 

代碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>取得訂用帳戶

取得指定的 SaaS 訂用帳戶。 使用此呼叫來取得授權資訊和計畫資訊。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   SaaS 訂用帳戶的唯一識別碼，會在透過解析 API 解析權杖之後取得。   |
|  ApiVersion        |   要用於此要求的作業版本。   |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。  |

*回應碼：*

代碼：200<br>
從識別碼取得 SaaS 訂用帳戶。 回應裝載：<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true - customer subscription is currently in free trial, false - customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。<br> 

代碼：500<br>
內部伺服器錯誤。<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>列出可用的方案

使用此呼叫來找出目前發行者是否有任何私用或公用供應專案。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
|  x-ms-correlationid  | 用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。 |

*回應碼：*

代碼：200<br>
取得客戶可用方案的清單。 回應主體：

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

代碼：404<br>
找不到。<br> 

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。 <br> 

代碼：500<br>
內部伺服器錯誤。<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>啟用訂用帳戶

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>張貼<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用解析 API 解析權杖之後，所取得的 SaaS 訂用帳戶唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  x-ms-correlationid  | 用於用戶端作業的特殊字串值。 這個字串會將用戶端操作的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。 |

*要求承載：*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*回應碼：*

代碼：200<br>
啟用訂用帳戶。<br>

代碼：400<br>
不正確的要求：驗證失敗。

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。

代碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>變更訂用帳戶上的方案

更新訂用帳戶上的方案。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用解析 API 解析權杖之後，所取得的 SaaS 訂用帳戶唯一識別碼。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。    |
| 授權      |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*要求承載：*

```json
Request Body:
{
    "planId": "gold"
}
```

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 用來取得作業狀態的資源連結。   |

*回應碼：*

代碼：202<br>
已接受變更計畫的要求。 合作夥伴預期會輪詢作業-位置以判斷成功或失敗。 <br>

代碼：400<br>
不正確的要求：驗證失敗。

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。

代碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>一次只能修補一個方案或數量，而不是兩者。 具有**Update**之訂用帳戶的編輯`allowedCustomerOperations`不在中。

#### <a name="change-the-quantity-on-the-subscription"></a>變更訂用帳戶的數量

更新訂用帳戶上的數量。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修補程式：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用解析 API 解析權杖之後，所取得的 SaaS 訂用帳戶唯一識別碼。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。    |
| 授權      |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*要求承載：*

```json
Request Body:
{
    "quantity": 5
}
```

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 連結至資源以取得作業的狀態。   |

*回應碼：*

代碼：202<br>
已接受變更數量的要求。 合作夥伴預期會輪詢作業-位置以判斷成功或失敗。 <br>

代碼：400<br>
不正確的要求：驗證失敗。


代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。

代碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>一次只能修補一個方案或數量，而不是兩者。 具有**Update**之訂用帳戶的編輯`allowedCustomerOperations`不在中。

#### <a name="delete-a-subscription"></a>刪除訂用帳戶

取消訂閱並刪除指定的訂用帳戶。

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>刪除<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用解析 API 解析權杖之後，所取得的 SaaS 訂用帳戶唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。   |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。   |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*回應碼：*

代碼：202<br>
夥伴起始了取消訂閱 SaaS 訂用帳戶的呼叫。<br>

代碼：400<br>
刪除不在`allowedCustomerOperations`的訂**用帳戶**上。

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。

代碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>作業 API

作業 API 支援下列修補和取得作業。

#### <a name="list-outstanding-operations"></a>列出未完成的作業 

列出目前發行者的未完成作業。 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查詢參數：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   要用於此要求的作業版本。                |
| subscriptionId     | 使用解析 API 解析權杖之後，所取得的 SaaS 訂用帳戶唯一識別碼。  |

*要求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*回應碼：*

代碼：200<br> 取得訂用帳戶的暫止作業清單。 回應裝載：

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


代碼：400<br>
不正確的要求：驗證失敗。

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。

代碼：500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>取得作業狀態

讓發行者能夠追蹤指定之觸發非同步作業的狀態（ `Subscribe`例如、 `Unsubscribe`、 `ChangePlan`或`ChangeQuantity`）。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。  |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如： "`Bearer <access_token>`"。  |

*回應碼：*<br>

代碼：200<br> 取得指定的暫止 SaaS 作業。 回應裝載：

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

代碼：400<br>
不正確的要求：驗證失敗。

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。
 
代碼：404<br>
找不到。

代碼：500<br> 內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>更新作業的狀態

以提供的值更新作業的狀態，以指示成功或失敗。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  要用於此要求的作業版本。  |
| subscriptionId     | 使用解析 API 解析權杖之後，所取得的 SaaS 訂用帳戶唯一識別碼。  |
|  operationId       | 正在完成的作業。 |

*要求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 這個參數會將用戶端作業的所有事件與伺服器端上的事件相互關聯。 如果未提供此值，則會產生一個並提供于回應標頭中。 |
|  授權     |  [取得 JSON web 權杖（JWT）持有人權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如： "`Bearer <access_token>`"。  |

*要求承載：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*回應碼：*

代碼：200<br> 呼叫，通知合作夥伴端的作業完成。 例如，此回應可能會通知基座或計畫的變更。

代碼：400<br>
不正確的要求：驗證失敗。

代碼：403<br>
未經授權。 未提供驗證 token 或其無效，或要求嘗試存取不屬於目前發行者的取得。

代碼：404<br>
找不到。

代碼：409<br>
衝突。 例如，已經滿足了較新的交易。

代碼：500<br> 內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服務上執行 webhook

發行者必須在此 SaaS 服務中執行 webhook，以主動通知使用者其服務中的變更。 在對 webhook 通知採取動作之前，SaaS 服務應呼叫作業 API 來進行驗證和授權。


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher's name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
其中，動作可以是下列其中一項： 
- `Unsubscribe`（當資源已刪除時）
- `ChangePlan`（當變更計畫作業完成時）
- `ChangeQuantity`（當變更數量作業完成時）
- `Suspend`（當資源已暫停時）
- `Reinstate`（當資源在暫停後復原時）

其中的狀態可以是下列其中一項： 
- **NotStarted** <br>
 - **InProgress** <br>
- **已成功** <br>
- **Failed** <br>
- **合併** <br>

在 webhook 通知中，可操作的狀態會是 [**成功**] 和 [**失敗**]。 作業的生命週期是從**NotStarted**到終端機狀態，例如 [**成功**]、[**失敗**] 或 [**衝突**]。 如果您收到**NotStarted**或**InProgress**，請繼續透過 GET API 要求狀態，直到作業達到結束狀態，再採取動作。 

## <a name="mock-apis"></a>Mock Api

您可以使用我們的 mock Api，協助您開始進行開發，特別是原型，以及測試專案。 

主機端點： `https://marketplaceapi.microsoft.com/api` （不需要驗證）<br/>
API 版本：`2018-09-15`<br/>
範例 URI：`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

模擬和實際 Api 中的 API 端點路徑都相同，但 API 版本不同。 此版本`2018-09-15`適用于模擬版本和`2018-08-31`生產版本。 

本文中的任何 API 呼叫都可以對 mock 主機端點進行。 一般來說，預期會以回應的形式取得模擬資料。 呼叫 mock API 上的更新訂閱者法時，一律會傳回500。 

## <a name="next-steps"></a>後續步驟

開發人員也可以使用[CLOUD PARTNER 入口網站 REST api](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)，以程式設計方式取出和操作工作負載、供應專案和發行者設定檔。
