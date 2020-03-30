---
title: SaaS 履行 API v2 |Azure 應用商店
description: 本文介紹如何使用關聯的履行 v2 API 在 AppSource 和 Azure 應用商店上創建和管理 SaaS 產品/
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 10/18/2019
ms.author: dsindona
ms.openlocfilehash: ca49418013357ecaae62ea5e91374eaa1cbde59d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275759"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS 履行 API 第 2 版 

本文詳細介紹了使合作夥伴能夠在 AppSource 市場和 Azure 應用商店中銷售其 SaaS 應用程式的 API。 這些 API 是 AppSource 和 Azure 應用商店上可交易 SaaS 產品的要求。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 訂閱生命週期

Azure SaaS 管理 SaaS 訂閱購買的整個生命週期。 它使用履行 API 作為一種機制，以推動與合作夥伴的實際履行、計畫更改和刪除訂閱。 客戶的帳單基於 Microsoft 維護的 SaaS 訂閱的狀態。 下圖描述了驅動狀態之間更改狀態和操作。

![SaaS 訂閱生命週期狀態](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>SaaS 訂閱的狀態

下表列出了 SaaS 訂閱的預配狀態，包括每個訂閱的說明和序列圖（如果適用）。 

#### <a name="provisioning"></a>佈建

當客戶發起購買時，合作夥伴將在使用 URL 參數的客戶互動式網頁上的授權代碼中接收此資訊。 例如`https://contoso.com/signup?token=..`，合作夥伴中心的著陸頁 URL 為`https://contoso.com/signup`。 可以通過調用 Resolve API 來驗證和交換授權代碼，以瞭解預配服務的詳細資訊。  當 SaaS 服務完成預配時，它會發送啟動呼叫，以指示履行已完成，並且可以向客戶收費。 

下圖顯示了預配方案的 API 呼叫順序。  

![API 呼叫預配 SaaS 服務](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>已佈建

此狀態是預配服務的穩定狀態。

##### <a name="provisioning-for-update"></a>為更新預配 

此狀態表示對現有服務的更新處於掛起狀態。 此類更新可以由客戶從市場或 SaaS 服務（僅適用于直接到客戶交易）啟動。

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>更新的預配（從市場啟動時）

下圖顯示了從市場啟動更新時的操作順序。

![從市場啟動更新時調用 API](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>更新的預配（從 SaaS 服務啟動時）

下圖顯示了從 SaaS 服務啟動更新時的操作。 （Webhook 調用替換為 SaaS 服務啟動的訂閱的更新。 

![從 SaaS 服務啟動更新時調用 API](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>暫止

此狀態表示尚未收到客戶的付款。 根據策略，我們將在取消訂閱之前為客戶提供寬限期。 當訂閱處於此狀態時： 

- 作為合作夥伴，您可以選擇降級或阻止使用者對服務的訪問。
- 訂閱必須保持可恢復狀態，可以恢復完整功能，而不會丟失任何資料或設置。 
- 期望通過履行 API 或寬限期結束時的取消預配請求獲得此訂閱的恢復請求。 

#### <a name="unsubscribed"></a>取消訂閱 

訂閱達到此狀態，以回應明確的客戶請求或不支付會費。 合作夥伴的期望是，客戶的資料將保留一定天數，然後被刪除。 


## <a name="api-reference"></a>應用程式開發介面參考

本節記錄 SaaS*訂閱 API* *和操作 API*。  版本 2 `api-version` API 的參數值為`2018-08-31`。  


### <a name="parameter-and-entity-definitions"></a>參數和實體定義

下表列出了履行 API 使用的常見參數和實體的定義。

|     實體/參數     |     定義                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | SaaS 資源的 GUID 識別碼。  |
| `name`                   | 客戶為此資源提供的易記名稱。 |
| `publisherId`            | 每個發行者的唯一字串識別碼（例如："contoso"）。 |
| `offerId`                | 每個產品/服務的唯一字串識別碼（例如："offer1"）。  |
| `planId`                 | 每個計畫/SKU 的唯一字串識別碼（例如："銀色"）。 |
| `operationId`            | 特定操作的 GUID 識別碼。  |
|  `action`                | 對資源執行的操作`Unsubscribe`，、、`Suspend``Reinstate``ChangePlan``ChangeQuantity`或 。 `Transfer` |
|   |   |

全域唯一識別碼 （[GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)） 是 128 位（32-十進位）的數位，通常自動生成。 

#### <a name="resolve-a-subscription"></a>解析訂閱 

解析終結點使發行者能夠將市場權杖解析為持久資源識別碼。 資源識別碼 是 SaaS 訂閱的唯一識別碼。 當使用者重定向到合作夥伴的網站時，URL 在查詢參數中包含權杖。 合作夥伴應使用此權杖並請求解析它。 回應包含資源的唯一 SaaS 訂閱 ID、名稱、優惠 ID 和計畫。 此權杖僅有效一小時。 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>張貼<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |

*請求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>` |
|  x-ms-marketplace-token  |  當使用者從 Azure 重定向到 SaaS 合作夥伴的網站時，URL 中的權杖查詢參數（例如： `https://contoso.com/signup?token=..` *注：* 在使用權杖值之前，URL 會從瀏覽器中解碼權杖值。  |

*回應代碼：*

代碼： 200<br>
將不透明權杖解析為 SaaS 訂閱。 回應機構：
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

代碼： 400<br>
不正確的要求。 x-ms-市場權杖丟失、格式錯誤或過期。

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。

代碼： 500<br>
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

訂閱 API 支援以下 HTTPS 操作：**獲取**、**發佈**、**修補**和**刪除**。


#### <a name="list-subscriptions"></a>清單訂閱

列出發行者的所有 SaaS 訂閱。

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*查詢參數：*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  要用於此要求的作業版本。  |

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。 |
| x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。  |
| 授權      |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>`  |

*回應代碼：*

代碼： 200 <br/>
根據身份驗證權杖獲取所有發行者優惠的發行者和相應訂閱。

>[!Note]
>當您首次開發產品/服務時，將使用[類比 API，](#mock-apis)而實際發佈產品/服務時需要使用真正的 API。  實際 API 和類比 API 因代碼的第一行而異。  在實際 API 中有`subscription`該部分，而此部分不存在類比 API。

類比 API 的回應負載：<br>

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
對於真正的 API： <br>

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
僅當有要檢索的計畫的其他"頁面"時，延續權杖才會存在。 

代碼： 403 <br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。 

代碼： 500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>獲取訂閱

獲取指定的 SaaS 訂閱。 使用此呼叫獲取許可證資訊和計畫資訊。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   通過 Resolve API 解析權杖後獲取的 SaaS 訂閱的唯一識別碼。   |
|  ApiVersion        |   要用於此要求的作業版本。   |

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>`  |

*回應代碼：*

代碼： 200<br>
從識別碼獲取 SaaS 訂閱。 回應有效負載：<br>

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

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。<br> 

代碼： 500<br>
內部伺服器錯誤。<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>列出可用計畫

使用此調用可瞭解當前發行者是否有任何私人或公開優惠。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   要用於此要求的作業版本。  |

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。 |
|  x-ms-correlationid  | 用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。 |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>` |

*回應代碼：*

代碼： 200<br>
獲取客戶的可用計畫清單。 回應機構：

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

代碼： 404<br>
找不到。<br> 

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。 <br> 

代碼： 500<br>
內部伺服器錯誤。<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>啟動訂閱

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>張貼<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用 Resolve API 解析權杖後獲取的 SaaS 訂閱的唯一識別碼。  |

*請求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  x-ms-correlationid  | 用於用戶端作業的特殊字串值。 此字串將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>` |

*請求有效負載：*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*回應代碼：*

代碼： 200<br>
啟動訂閱。<br>

代碼： 400<br>
錯誤請求：驗證失敗。

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。

代碼： 500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>更改訂閱上的計畫

更新訂閱上的計畫。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用 Resolve API 解析權杖後獲取的 SaaS 訂閱的唯一識別碼。  |

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。    |
| 授權      |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*請求有效負載：*

```json
Request Body:
{
    "planId": "gold"
}
```

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 指向資源的連結，以獲取操作的狀態。   |

*回應代碼：*

代碼： 202<br>
已接受更改計畫的請求。 合作夥伴應輪詢操作位置以確定成功或失敗。 <br>

代碼： 400<br>
錯誤請求：驗證失敗。

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。

代碼： 500<br>
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
>一次只能修補計畫或數量，不能同時修補兩者。 使用 Update 的訂閱進行**編輯不在**`allowedCustomerOperations`中。

#### <a name="change-the-quantity-on-the-subscription"></a>更改訂閱上的數量

更新訂閱上的數量。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>修補程式：<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用 Resolve API 解析權杖後獲取的 SaaS 訂閱的唯一識別碼。  |

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。    |
| 授權      |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*請求有效負載：*

```json
Request Body:
{
    "quantity": 5
}
```

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | 連結到資源以獲取操作的狀態。   |

*回應代碼：*

代碼： 202<br>
已接受更改數量的請求。 合作夥伴應輪詢操作位置以確定成功或失敗。 <br>

代碼： 400<br>
錯誤請求：驗證失敗。


代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。

代碼： 500<br>
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
>一次只能修補計畫或數量，不能同時修補兩者。 使用 Update 的訂閱進行**編輯不在**`allowedCustomerOperations`中。

#### <a name="delete-a-subscription"></a>刪除訂用帳戶

取消訂閱並刪除指定的訂閱。

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>刪除<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |
| subscriptionId     | 使用 Resolve API 解析權杖後獲取的 SaaS 訂閱的唯一識別碼。  |

*請求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。   |
|  x-ms-correlationid  |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。   |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*回應代碼：*

代碼： 202<br>
合作夥伴發起取消訂閱 SaaS 訂閱的呼叫。<br>

代碼： 400<br>
刪除不在 中的`allowedCustomerOperations`**刪除**訂閱。

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。

代碼： 500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>操作 API

操作 API 支援以下修補程式和獲取操作。

#### <a name="list-outstanding-operations"></a>列出未完成的操作 

列出當前發行者未完成的操作。 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*查詢參數：*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   要用於此要求的作業版本。                |
| subscriptionId     | 使用 Resolve API 解析權杖後獲取的 SaaS 訂閱的唯一識別碼。  |

*請求標頭：*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*回應代碼：*

代碼： 200<br> 獲取訂閱上的掛起操作的清單。 回應有效負載：

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


代碼： 400<br>
錯誤請求：驗證失敗。

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。

代碼： 500<br>
內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>獲取操作狀態

使發佈`Subscribe`者能夠跟蹤指定觸發的非同步作業（如 、`Unsubscribe``ChangePlan`或`ChangeQuantity`） 的狀態。

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  要用於此要求的作業版本。  |

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。  |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。 例如：""`Bearer <access_token>`  |

*回應代碼：*<br>

代碼： 200<br> 獲取指定的掛起 SaaS 操作。 回應有效負載：

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

代碼： 400<br>
錯誤請求：驗證失敗。

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。
 
代碼： 404<br>
找不到。

代碼： 500<br> 內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>更新操作的狀態

更新操作的狀態以指示使用提供的值成功或失敗。

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*查詢參數：*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  要用於此要求的作業版本。  |
| subscriptionId     | 使用 Resolve API 解析權杖後獲取的 SaaS 訂閱的唯一識別碼。  |
|  operationId       | 正在完成的操作。 |

*請求標頭：*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，將在回應標頭中生成並提供此值。 |
|  x-ms-correlationid |  用於用戶端作業的特殊字串值。 此參數將用戶端操作中的所有事件與伺服器端的事件相關聯。 如果未提供此值，將在回應標頭中生成並提供此值。 |
|  授權     |  [獲取 JSON Web 權杖 （JWT） 無記名權杖](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app)。  例如：""`Bearer <access_token>`  |

*請求有效負載：*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*回應代碼：*

代碼： 200<br> 通知合作夥伴方完成操作的呼叫。 例如，此回應可能表示座位或計畫發生變化。

代碼： 400<br>
錯誤請求：驗證失敗。

代碼： 403<br>
未經授權。 身份驗證權杖未提供或無效，或者請求正在嘗試訪問不屬於當前發行者的獲取。

代碼： 404<br>
找不到。

代碼： 409<br>
衝突。 例如，已完成較新的事務。

代碼： 500<br> 內部伺服器錯誤。

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服務上實現網路鉤子

發行者必須在此 SaaS 服務中實現 Webhook，以便主動通知使用者其服務中的更改。 SaaS 服務應叫用作業 API 以驗證和授權，然後再對 Webhook 通知執行操作。


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
其中操作可以是以下操作之一： 
- `Unsubscribe`（刪除資源時）
- `ChangePlan`（更改計畫操作完成後）
- `ChangeQuantity`（更改數量操作完成後）
- `Suspend`（資源已掛起時）
- `Reinstate`（暫停後恢復資源時）

狀態可以是以下狀態之一： 
- **NotStarted** <br>
 - **在進行中** <br>
- **成功** <br>
- **失敗** <br>
- **衝突** <br>

在 Webhook 通知中，可操作狀態為 **"成功"** 和 **"失敗**"。 操作的生命週期是從 **"未啟動"** 到終端狀態，如 **"成功**"、"**失敗**"或 **"衝突**"。 如果收到 **"未啟動**"或 **"未前進"，** 請繼續通過 GET API 請求狀態，直到操作達到終端狀態，然後再執行操作。 

## <a name="mock-apis"></a>類比 API

您可以使用我們的類比 API 説明您開始開發，特別是原型設計以及測試專案。 

主機終結點：（`https://marketplaceapi.microsoft.com/api`無需身份驗證）<br/>
API 版本：`2018-09-15`<br/>
示例 URI：`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

在類比和實際 API 中，API 終結點路徑相同，但 API 版本不同。 該版本適用于`2018-09-15`類比版本和生產`2018-08-31`版本。 

本文中的任何 API 呼叫都可以對類比主機終結點進行。 通常，期望將類比資料作為回應返回。 對類比 API 上的更新訂閱者法的調用始終返回 500。 

## <a name="next-steps"></a>後續步驟

開發人員還可以使用[雲合作夥伴門戶 REST API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)以程式設計方式檢索和操作工作負載、產品/服務以及發行者設定檔。
