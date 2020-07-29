---
title: Microsoft 商業 marketplace 中的 SaaS 履行 Api v2
description: 瞭解如何使用履行 Api 第2版來建立和管理 Microsoft AppSource 和 Azure Marketplace 上的 SaaS 供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: f40da30ff0d702078861367dea810cc8ca1ab91b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87305137"
---
# <a name="saas-fulfillment-apis-version-2-in-microsoft-commercial-marketplace"></a>Microsoft 商業 marketplace 中的 SaaS 履行 Api 第2版

本文詳細說明可讓合作夥伴在 Microsoft AppSource 和 Azure Marketplace 中銷售其 SaaS 供應專案的 Api。 必須要有發行者才能與這些 Api 整合，以在合作夥伴中心發佈 transactable SaaS 供應專案。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 訂閱生命週期

Azure Marketplace 會管理 SaaS 訂用帳戶在終端客戶購買後的整個生命週期。  它會使用登陸頁面、履行 Api、作業 Api 和 webhook 來驅動實際的 SaaS 訂用帳戶啟用和使用方式、更新和訂用帳戶的取消。  終端客戶的帳單是以 Microsoft 維護的 SaaS 訂用帳戶狀態為基礎。 

### <a name="states-of-a-saas-subscription"></a>SaaS 訂用帳戶的狀態

SaaS 訂用帳戶和適用動作的狀態會顯示出來。

![Marketplace 中 SaaS 訂用帳戶的生命週期](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>已購買但尚未啟用（*PendingFulfillmentStart*）

當終端客戶（或 CSP）在 marketplace 中購買 SaaS 供應專案之後，發行者應該會收到購買通知，以便在發行者端為終端客戶建立和設定新的 SaaS 帳戶。

進行帳戶建立的情況：

1. 客戶在 Microsoft AppSource 或 Azure 入口網站成功購買後，必須按一下可用於 SaaS 供應專案的 [**設定**] 按鈕。 或在客戶于購買後不久就會收到的電子郵件中。
2. 然後 Microsoft 會在新的瀏覽器索引標籤中開啟具有權杖參數（marketplace 採購識別權杖）的登陸頁面 URL，以通知合作夥伴關於購買。

這類呼叫的範例是 `https://contoso.com/signup?token=<blob>` ，而合作夥伴中心內此 SaaS 供應專案的登陸頁面 URL 設定為 `https://contoso.com/signup` 。 此權杖會提供發行者識別碼，以唯一識別 SaaS 購買和客戶。

>[!NOTE]
>在客戶從 Microsoft 端起始設定程式之前，發行者不會收到 SaaS 購買的通知。

登陸頁面 url 必須全天候啟動並執行，而且隨時都可從 Microsoft 接收新的來電。 如果登陸頁面變得無法使用，客戶將無法註冊 SaaS 服務並開始使用。

然後，您必須呼叫[SaaS 解析 API](#resolve-a-purchased-subscription)做為標頭參數的值，從發行者將*權杖*傳回給 Microsoft `x-ms-marketplace-token header` 。  作為解析 API 呼叫的結果，會交換權杖以取得 SaaS 購買的詳細資料，例如購買的唯一識別碼、購買的供應專案識別碼、購買的方案識別碼等等。

在登陸頁面上，客戶應透過 Azure Active Directory （AAD）單一登入（SSO）登入新的或現有的 SaaS 帳戶。

發行者應執行 SSO 登入，以提供 Microsoft 在此流程中所需的使用者體驗。  設定 SSO 時，請務必使用多租使用者 Azure AD 應用程式，同時允許公司和學校帳戶或個人 Microsoft 帳戶。  這項需求只適用于登陸頁面，以及已使用 Microsoft 認證登入時，會重新導向至 SaaS 服務的使用者。 它不適用於 SaaS 服務的所有登入。

> [!NOTE]
>如果 SSO 登入需要系統管理員授與應用程式的許可權，合作夥伴中心的供應專案描述必須公開需要系統管理員層級的存取權。 這是為了符合[Marketplace 認證原則](https://docs.microsoft.com/legal/marketplace/certification-policies#10003-authentication-options)。

登入之後，客戶應該在發行者端完成 SaaS 設定。 然後，發行者必須呼叫「[啟用訂閱 API](#activate-a-subscription) 」，將 SaaS 帳戶布建完成的通知傳送至 Marketplace。
這會啟動客戶的計費週期。 如果「啟用訂閱 API」呼叫不成功，則不會向客戶收取購買費用。


![布建案例的 API 呼叫](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>作用中（已訂閱）

此狀態是已布建的 SaaS 訂用帳戶的穩定狀態。 在 Microsoft 端處理「[啟用訂](#activate-a-subscription)用帳戶 API」呼叫之後，SaaS 訂用帳戶會標示為已訂閱。 SaaS 服務現已準備好供客戶在發行者端使用，且客戶會向您收取費用。

當 SaaS 訂用帳戶已在使用中，且客戶選擇從 Azure 入口網站或 M365 系統管理中心啟動 [**管理**SaaS 體驗] 時，Microsoft 會再次使用 [*權杖*] 參數來呼叫**登陸頁面 URL** ，如同在啟用流程中一樣。  發行者應該區別現有 SaaS 帳戶的新購買和管理，並據此處理此登陸頁面 URL 呼叫。

#### <a name="being-updated-subscribed"></a>正在更新（已訂閱）

此動作表示現有作用中 SaaS 訂用帳戶的更新已由 Microsoft 和發行者處理。 這類更新可由起始

* 來自 marketplace 的客戶
* 來自 marketplace 的 CSP
* 來自發行者 SaaS 網站的客戶（不適用於購買的 CSP）

有兩種類型的更新可供 SaaS 訂用帳戶使用：

1. 當客戶選擇另一個訂用帳戶方案時的更新計畫。
1. 當客戶變更訂用帳戶的已購買基座數目時，更新數量

只有有效的訂閱可以更新。 當訂用帳戶正在更新時，其狀態會在 Microsoft 端保持作用中。

##### <a name="update-initiated-from-the-marketplace"></a>從 marketplace 起始的更新

在此流程中，客戶會從 M365 系統管理中心變更訂用帳戶方案或基座數量。  

1. 一旦輸入更新之後，Microsoft 會呼叫「發行者」的 webhook URL （在合作夥伴中心的 [連線**webhook** ] 欄位中設定），並提供適當的*動作*和其他相關參數值。  
1. 發行者端應對 SaaS 服務進行必要的變更，並藉由呼叫作業[API 的更新狀態](#update-the-status-of-an-operation)，在變更完成時通知 Microsoft。
1. 如果以 [失敗] 狀態傳送修補程式，則不會在 Microsoft 端完成更新流程。  SaaS 訂用帳戶將會保留現有的方案和基座數量。

Marketplace 起始的更新案例的 API 呼叫順序如下所示。

![Marketplace 起始更新的 API 呼叫](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>從發行者起始的更新

在此流程中，客戶會變更訂用帳戶方案或從 SaaS 服務本身購買的基座數量。 

1. 發行者程式碼必須先呼叫[變更計畫 api](#change-the-plan-on-the-subscription)和/或[變更數量 API](#change-the-quantity-of-seats-on-the-saas-subscription) ，才能在發行者端進行要求的變更。 

1. Microsoft 會將變更套用至訂用帳戶，然後透過**連接 Webhook**通知發行者以套用相同的變更。  

1. 只有發行者才應該對 SaaS 訂用帳戶進行必要的變更，並藉由呼叫作業[API 的更新狀態](#update-the-status-of-an-operation)，在變更完成時通知 Microsoft。

發行者端起始之更新案例的 API 呼叫順序。

![發行者端起始更新的 API 呼叫](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>已暫停（已*暫停*）

此狀態表示尚未收到 SaaS 服務的客戶付款。 Microsoft 將會通知發行者此變更在 SaaS 訂閱狀態。 通知是透過呼叫將*action*參數設定為已*暫停*的 webhook 來完成。

發行者不一定會對發行者端上的 SaaS 服務進行變更。 我們建議發行者將此資訊提供給已暫停的客戶，並限制或封鎖客戶對 SaaS 服務的存取。  永遠不會收到付款的機率。

Microsoft 會在自動取消訂用帳戶之前，為客戶提供30天的寬限期。 當訂用帳戶處於暫停狀態時：

* SaaS 帳戶必須由 ISV 保持在可復原的狀態。 可以還原完整的功能，而不會遺失任何資料或設定。
* 如果在寬限期期間收到款項，或在寬限期結束時（透過 webhook 機制），則預期會取得此訂用帳戶的恢復要求。

在發行者採取任何動作之前，訂用帳戶狀態會變更為 [已暫停于 Microsoft]。 只有使用中的訂閱可以暫停。

#### <a name="reinstated-suspended"></a>已復原（已*暫停*）

正在復原訂用帳戶。

此動作表示客戶的付款工具再次生效，並會針對 SaaS 訂用帳戶進行付款。  正在復原訂用帳戶。 在此案例中： 

1. Microsoft 會呼叫 webhook，並將*action*參數設為*恢復*值。  
1. 發行者會確保此訂用帳戶在發行者端上可完全正常運作。
1. 發行者會呼叫具有成功狀態的[修補作業 API](#update-the-status-of-an-operation) 。  
1. 然後，恢復將會成功，且會再次向客戶收取 SaaS 訂用帳戶的費用。 
1. 如果以 [失敗] 狀態傳送修補程式，reinstatement 流程將不會在 Microsoft 端完成。 訂用帳戶將會保持暫停。

如果以 [失敗] 狀態傳送修補程式，reinstatement 流程將不會在 Microsoft 端完成。  訂用帳戶將會保持暫停。

只有暫止的訂閱可以復原。  當 SaaS 訂用帳戶正在復原時，其狀態會保持暫停。  此作業完成後，訂用帳戶的狀態將會變成 [作用中]。

#### <a name="renewed-subscribed"></a>已更新（已*訂閱*）

在訂用帳戶期限結束（一個月或一年後）時，Microsoft 會自動更新 SaaS 訂用帳戶。  所有 SaaS 訂閱的 [自動續約] 設定預設為*true* 。 有效的 SaaS 訂用帳戶將繼續以一般步調進行更新。 當訂閱正在更新時，Microsoft 不會通知發行者。 客戶可以透過 M365 管理員入口網站或 Azure 入口網站，關閉 SaaS 訂用帳戶的自動續約。  在此情況下，SaaS 訂用帳戶會在目前計費期限結束時自動取消。  客戶也可以在任何時間點取消 SaaS 訂用帳戶。

只有使用中的訂閱會自動更新。  訂閱會在更新程式期間保持作用中，如果自動更新成功，則為。  更新之後，訂用帳戶期限的開始和結束日期將會更新為新詞彙的日期。

如果因為付款問題而自動更新失敗，訂用帳戶將會被擱置。  發行者將會收到通知。

#### <a name="canceled-unsubscribed"></a>已取消（未*訂閱*） 

訂用帳戶會達到此狀態，藉由取消發行者網站、Azure 入口網站或 M365 系統管理中心的訂閱來回應明確的客戶或 CSP 動作。  您也可以隱含地取消訂用帳戶，因為因為未付款的逾期狀態為30天。

收到取消 webhook 呼叫之後，發行者應保留客戶資料，以便在要求時至少7天。 只有之後，才可以刪除客戶資料。

SaaS 訂用帳戶可以在其生命週期中的任何時間點取消。 一旦取消，就無法重新開機訂用帳戶。

## <a name="api-reference"></a>API 參考資料

本節記載 SaaS 訂用帳戶和作業 Api。

**訂**用帳戶 api 應該用來處理從購買到取消的 SaaS 訂用帳戶生命週期。

**作業 api**應該用來：

* 確認並確認已處理的 webhook 呼叫
* 取得等待發行者認可之待決作業的應用程式清單

### <a name="enforcing-tls-12-note"></a>強制執行 TLS 1.2 注意事項

TLS 版本1.2 版本將會立即強制執行，做為 HTTPS 通訊的最低版本。 請確定您在程式碼中使用此 TLS 版本。  TLS 版本1.0 和1.1 即將淘汰。

### <a name="subscription-apis"></a>訂用帳戶 Api

#### <a name="resolve-a-purchased-subscription"></a>解決已購買的訂用帳戶

解析端點可讓發行者將 marketplace 購買識別權杖（稱為已[購買但尚未啟用](#purchased-but-not-yet-activated-pendingfulfillmentstart)的*權杖*）交換至持續購買的 SAAS 訂用帳戶識別碼及其詳細資料。

當客戶重新導向至合作夥伴的登陸頁面 URL 時，客戶識別權杖會以*權杖*參數的形式在此 URL 呼叫中傳遞。 合作夥伴預期會使用此權杖，並提出要求來解決此問題。 解析 API 回應包含 SaaS 訂用帳戶識別碼，以及可唯一識別購買的其他詳細資料。 登陸頁面 URL 呼叫所提供的*權杖*通常會在24小時內有效。 如果您收到的*權杖*已過期，建議您將下列指引提供給終端客戶：

「無法識別此購買，請在 Azure 入口網站或 M365 系統管理中心重新開啟此 SaaS 訂閱，然後再按一下 [設定帳戶] 或 [管理帳戶] 按鈕。

呼叫解析 API 將會傳回訂用帳戶的詳細資料，以及所有支援狀態中 SaaS 訂閱的狀態。

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>發佈`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值            |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。   |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json` |
|  `x-ms-requestid`    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `x-ms-correlationid` |  用於用戶端作業的特殊字串值。 此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  唯一的存取權杖，可識別發出此 API 呼叫的發行者。 格式是 `"Bearer <accessaccess_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |
|  `x-ms-marketplace-token`  | 要解析的 marketplace 購買識別*權杖*參數。  當客戶重新導向至 SaaS 合作夥伴的網站時，會在登陸頁面 URL 呼叫中傳遞權杖（例如： `https://contoso.com/signup?token=<token><authorization_token>` ）。 <br> <br>  *注意：* 所編碼的*token*值屬於登陸頁面 url 的一部分，因此必須先將其解碼，才能在此 API 呼叫中作為參數使用。  <br> <br> Url 中編碼字串的範例看起來像： `contoso.com/signup?token=ab%2Bcd%2Fef` ，其中 token 是 `ab%2Bcd%2Fef` 。  已解碼的相同權杖將會是：`Ab+cd/ef` |
| | |

*回應碼：*

程式碼：200會根據提供的傳回唯一的 SaaS 訂用帳戶識別碼 `x-ms-marketplace-token` 。

回應主體範例：

```json
{
    "id": "<guid>",  // purchased SaaS subscription ID 
    "subscriptionName": "Contoso Cloud Solution", // SaaS subscription name 
    "offerId": "offer1", // purchased offer ID
    "planId": "silver", // purchased offer's plan ID
    "quantity": "20", // number of purchased seats, might be empty if the plan is not per seat
    "subscription": { // full SaaS subscription details, see Get Subscription APIs response body for full description
    "id": "<guid>",
    "publisherId": "contoso",
    "offerId": "offer1",
    "name": "Contoso Cloud Solution",
    "saasSubscriptionStatus": " PendingFulfillmentStart ",
    "beneficiary": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "purchaser": {
      "emailId": "test@test.com",
      "objectId": "<guid>",
      "tenantId": "<guid>",
"pid": "<ID of the user>"
    },
    "planId": "silver",
    "term": {
      "termUnit": "P1M",
                   startDate": "2019-05-31", 
   "endDate": "2019-06-29",
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": [
      "Delete",
      "Update",
      "Read"
    ],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

代碼：400不正確的要求。 `x-ms-marketplace-token`遺漏、格式不正確、無效或已過期。

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求嘗試存取的供應專案的 SaaS 訂用帳戶，已使用與用來建立授權權杖的不同 Azure AD App 識別碼來發行。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="activate-a-subscription"></a>啟用訂用帳戶

設定終端客戶的 SaaS 帳戶後，發行者必須在 Microsoft 端呼叫「啟用訂閱 API」。  除非此 API 呼叫成功，否則不會向客戶收取費用。

##### <a name="posthttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>發佈`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用2018-08-31。   |
| `subscriptionId` | 已購買的 SaaS 訂用帳戶的唯一識別碼。  使用[解析 API](#resolve-a-purchased-subscription)解析 marketplace 授權權杖之後，就會取得此識別碼。
 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  這個字串會將用戶端作業的所有事件與伺服器端上的事件相互關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`      |  唯一的存取權杖，可識別發出此 API 呼叫的發行者。 格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*要求承載範例：*

```json
{ // needed for validation of the activation request
    "planId": "gold", // purchased plan, cannot be empty
    "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*回應碼：*

代碼：200訂用帳戶已在 Microsoft 端標示為已訂閱。

沒有此呼叫的回應主體。

代碼：400不正確的要求：驗證失敗。

* `planId`不存在於要求裝載中。
* `planId`在中，要求裝載不符合所購買的內容。
* `quantity`在要求承載中，不符合已購買的內容
* SaaS 訂用帳戶處於「已訂閱」或「已暫停」狀態。

代碼：403禁止。 授權權杖無效、已過期或未提供。 要求嘗試存取的供應專案的 SaaS 訂用帳戶，已使用與用來建立授權權杖的不同 Azure AD App 識別碼來發行。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。

代碼：404找不到。 SaaS 訂用帳戶處於取消訂閱的狀態。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="get-list-of-all-subscriptions"></a>取得所有訂用帳戶的清單

針對 marketplace 中發行者發佈的所有供應專案，抓取所有已購買的 SaaS 訂閱清單。  系統會傳回所有可能狀態的 SaaS 訂用帳戶。 也會傳回取消訂閱的 SaaS 訂用帳戶，因為 Microsoft 端不會刪除這則資訊。

此 API 會傳回分頁的結果。 頁面大小為100。

##### <a name="gethttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用2018-08-31。  |
| `continuationToken`  | 選擇性參數。 若要取出結果的第一頁，請保留空白。  使用在參數中傳回的值 `@nextLink` ，來抓取下一個頁面。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`      |  唯一的存取權杖，可識別正在進行此 API 呼叫的發行者。  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*回應碼：*

程式碼：200會根據發行者的授權 token，傳回此發行者所有供應專案的所有現有訂閱清單。

*回應主體範例：*

```json
{
  "subscriptions": [
      {
          "id": "<guid>", // purchased SaaS subscription ID
          "name": "Contoso Cloud Solution", // SaaS subscription name
          "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
          "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription was purchased.
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address, user ID and tenant ID that purchased the SaaS subscription. These could be different from beneficiary information for reseller (CSP) purchase
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
            "term": { // The period for which the subscription was purchased. 
                "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
                "endDate": "2019-06-30", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
                "termUnit": "P1M" // where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values
          },
          "allowedCustomerOperations": [
              "Read", "Update", "Delete" 
          ], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
          "sessionMode": "None", // not relevant
          "isFreeTrial": true, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. (Optional field -– if not returned, the value is false.)
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation. Can be one of the following: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
      },
// next SaaS subscription details, might be a different offer
{
          "id": "<guid1>", 
          "name": "Contoso Cloud Solution1", 
          "publisherId": "contoso", 
          "offerId": "offer2", 
          "planId": "gold", 
          "quantity": "", 
          "beneficiary": {
              "emailId": " test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { 
              "emailId": "purchase@csp.com ",
              "objectId": "<guid>",
              "tenantId": "<guid>",
               "pid": "<ID of the user>"
          },
            "term": { 
                "startDate": "2019-05-31",
                "endDate": "2020-04-30",
                "termUnit": "P1Y"
          },
          "allowedCustomerOperations": [
              "Read" 
          ], 
          "sessionMode": "None",
          "isFreeTrial": false,
          "isTest": false,
          "sandboxType": "None",
          "saasSubscriptionStatus": "Suspended"
      }
  ],
  "@nextLink": "https:// https://marketplaceapi.microsoft.com/api/saas/subscriptions/?continuationToken=%5b%7b%22token%22%3a%22%2bRID%3a%7eYeUDAIahsn22AAAAAAAAAA%3d%3d%23RT%3a1%23TRC%3a2%23ISV%3a1%23FPC%3aAgEAAAAQALEAwP8zQP9%2fFwD%2b%2f2FC%2fwc%3d%22%2c%22range%22%3a%7b%22min%22%3a%22%22%2c%22max%22%3a%2205C1C9CD673398%22%7d%7d%5d&api-version=2018-08-31" // url that contains continuation token to retrieve next page of the SaaS subscriptions list, if empty or absent, this is the last page. ISV can use this url as is to retrieve the next page or extract the value of continuation token from this url.
}
```

如果找不到此發行者購買的 SaaS 訂用帳戶，則會傳回空的回應主體。

代碼：403禁止。 授權權杖無法使用、無效或已過期。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。 

代碼：500內部伺服器錯誤。 請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="get-subscription"></a>取得訂用帳戶

針對發行者在 marketplace 中發佈的 SaaS 供應專案，抓取指定的已購買 SaaS 訂用帳戶。 使用此呼叫來依識別碼取得特定 SaaS 訂用帳戶的所有可用資訊，而不是呼叫 API 以取得所有訂用帳戶的清單。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>獲取`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   使用2018-08-31。 |
| `subscriptionId`     |  已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `authorization`     | 唯一的存取權杖，可識別正在進行此 API 呼叫的發行者。 格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

程式碼：200會根據提供的來傳回 SaaS 訂閱的詳細資料 `subscriptionId` 。

*回應主體範例：*

```json
{
        "id":<guid>, // purchased SaaS subscription ID
        "name":"Contoso Cloud Solution", // SaaS subscription name
         "publisherId": "contoso", // publisher ID
          "offerId": "offer1", // purchased offer ID
          "planId": "silver", // purchased plan ID
          "quantity": "10", // purchased amount of seats, will be empty if plan is not per seat
         "beneficiary": { // email address, user ID and tenant ID for which SaaS subscription is purchased.
              "emailId": "test@contoso.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
          "purchaser": { // email address ,user ID and tenant ID that purchased the SaaS subscription.  These could be different from beneficiary information for reseller (CSP) scenario
              "emailId": "test@test.com",
              "objectId": "<guid>",
              "tenantId": "<guid>",
              "pid": "<ID of the user>"
          },
        "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary.  For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
        "sessionMode": "None", // not relevant
        "isFreeTrial": false, // true - the customer subscription is currently in free trial, false - the customer subscription is not currently in free trial. Optional field – if not returned the value is false.
          "isTest": false, // not relevant
          "sandboxType": "None", // not relevant
          "saasSubscriptionStatus": " Subscribed " // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
          "term": { // the period for which the subscription was purchased 
            "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
            "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
            "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
        }
}
```

代碼：403禁止。 授權權杖無效、已過期，且未提供。 要求嘗試存取的 SaaS 訂用帳戶，與用來建立授權權杖的供應專案不同 Azure AD App 識別碼。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。 

代碼：404找不到。  找不到具有指定之的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="list-available-plans"></a>列出可用的方案

抓取由 `subscriptionId` 特定購買此供應專案所識別的 SaaS 供應專案的所有計劃。  使用此呼叫來取得 SaaS 訂閱的受益人可以為訂用帳戶更新之所有私用和公用方案的清單。  傳回的方案將會在與已購買方案相同的地理位置中提供。

此呼叫會傳回該客戶除了已購買的方案以外的計畫清單。  此清單可以向「發行者」網站上的終端客戶呈現。  終端客戶可以將訂用帳戶方案變更為傳回清單中的任何一個方案。  將計畫變更為未列于清單中的方案將會失敗。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>獲取`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`    |  已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `x-ms-correlationid`  |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `authorization`     |  唯一的存取權杖，可識別正在進行此 API 呼叫的發行者。  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

程式碼：200會傳回現有 SaaS 訂閱的所有可用方案清單，包括已購買的訂用帳戶。

回應主體範例：

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso", // display name of the plan as it appears in the marketplace
        "isPrivate": true //true or false
    },
{ 
        "planId": "gold",
        "displayName": "Gold plan for Contoso", 
        "isPrivate": false //true or false
    }
]
}
```

如果 `subscriptionId` 找不到，則會傳回空的回應主體。

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求可能會嘗試存取使用不同的 Azure AD App 識別碼發佈之供應專案的 SaaS 訂用帳戶，而這些專案是用來建立授權權杖。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。 

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="change-the-plan-on-the-subscription"></a>變更訂用帳戶上的方案

將針對 SaaS 訂閱購買的現有方案更新為新方案（公用或私用）。  在 marketplace 中購買的 SaaS 訂閱的發行者端變更計畫時，發行者必須呼叫此 API。

只有使用中的訂用帳戶才可呼叫此 API。  任何計畫都可以變更為任何其他現有的方案（公用或私用），而不是本身。  針對私人方案，客戶的租使用者必須在合作夥伴中心內定義為方案物件的一部分。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>跳`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
| `subscriptionId`     | 已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。 |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid`  | 用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  唯一的存取權杖，可識別正在進行此 API 呼叫的發行者。  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*要求承載範例：*

```json
{
    "planId": "gold" // the ID of the new plan to be purchased
}
```

*回應碼：*

代碼：202已接受變更計畫的要求，並以非同步方式處理。  夥伴應輪詢作業 **-位置 URL** ，以判斷變更計畫要求成功或失敗。  輪詢應每隔數秒執行一次，直到收到作業的最終狀態為 [失敗]、[成功] 或 [衝突] 為止。  最終作業狀態應該會快速傳回，但在某些情況下可能需要幾分鐘的時間。

當動作準備好在 Marketplace 端成功完成時，合作夥伴也會收到 webhook 通知。  只有在發行者端才進行計畫變更。

*回應標頭：*

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  用來取得作業狀態的 URL。  例如： `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 。 |

代碼：400不正確的要求：驗證失敗。

* 新的方案不存在，或不適用於此特定的 SaaS 訂用帳戶。
* 嘗試變更為相同的方案。
* SaaS 訂用帳戶狀態為「未訂閱」。
* SaaS 訂用帳戶的更新作業不包含在中 `allowedCustomerOperations` 。

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求嘗試存取的 SaaS 訂用帳戶，與用來建立授權權杖的供應專案不同 Azure AD App 識別碼。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。

代碼：404找不到。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

>[!NOTE]
>方案或基座數量可以一次變更，而不是兩者。

>[!Note]
>只有在使用者明確核准變更之後，才能呼叫此 API。

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>變更 SaaS 訂用帳戶的基座數量

更新（增加或減少）針對 SaaS 訂用帳戶購買的基座數量。  在 marketplace 中建立的 SaaS 訂用帳戶的「發行者」端變更基座數目時，「發行者」必須呼叫此 API。

基座數量不得超過目前方案中允許的數目。  在此情況下，您應該在變更數量之前變更計畫。

##### <a name="patchhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>跳`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`     | 已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。  |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid`  | 用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     | 唯一的存取權杖，可識別正在進行此 API 呼叫的發行者。  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*要求承載範例：*

```json
{
    "quantity": 5 // the new amount of seats to be purchased
}
```

*回應碼：*

代碼：202已接受變更數量的要求，並以非同步方式處理。 合作夥伴預期會輪詢作業 **-位置 URL** ，以判斷變更數量要求成功或失敗。  輪詢應每隔數秒執行一次，直到收到作業的最終狀態為 [失敗]、[成功] 或 [衝突] 為止。  最後的作業狀態應該會快速傳回，但在某些情況下可能需要幾分鐘的時間。

當動作準備好在 Marketplace 端成功完成時，合作夥伴也會收到 webhook 通知。  只有在發行者端才會進行數量變更。

*回應標頭：*

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  連結至資源以取得作業的狀態。  例如： `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 。  |

代碼：400不正確的要求：驗證失敗。

* 新的數量大於或小於目前的方案限制。
* 缺少新的數量。
* 嘗試變更為相同的數量。
* SaaS 訂用帳戶狀態為「未訂閱」。
* SaaS 訂用帳戶的更新作業不包含在中 `allowedCustomerOperations` 。

代碼：403禁止。  授權權杖無效、已過期或未提供。  要求嘗試存取的訂用帳戶不屬於目前的發行者。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。 

代碼：404找不到。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

>[!Note]
>一次只能變更一個方案或數量，而非兩者。

>[!Note]
>只有在使用者明確核准變更之後，才能呼叫此 API。

#### <a name="cancel-a-subscription"></a>取消訂用帳戶

取消訂閱指定的 SaaS 訂用帳戶。  發行者不需要使用此 API，因此建議將客戶導向至 marketplace，以取消 SaaS 訂閱。

如果發行者決定在發行者端執行 marketplace 中購買的 SaaS 訂用帳戶取消，他必須呼叫此 API。  完成此呼叫之後，訂用帳戶的狀態將*會在 Microsoft 端取消訂閱。*

如果在下列寬限期內取消訂用帳戶，將不會向客戶收費：

* 啟用後的每月訂閱24小時。
* 啟用後的每年訂閱14天。

如果在上述寬限期後取消訂用帳戶，將會向客戶收取費用。  一旦取消成功，客戶就會立即失去 Microsoft 端 SaaS 訂用帳戶的存取權。

##### <a name="deletehttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>刪除`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`     | 已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。  |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid`  | 用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  唯一的存取權杖，可識別發出此 API 呼叫的發行者。  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*回應碼：*

代碼：202已接受取消訂閱的要求，並以非同步方式處理。  夥伴應輪詢作業 **-位置 URL** ，以判斷此要求是否成功或失敗。  輪詢應每隔數秒執行一次，直到收到作業的最終狀態為 [失敗]、[成功] 或 [衝突] 為止。  最後的作業狀態應該會快速傳回，但在某些情況下可能需要幾分鐘的時間。

合作夥伴也會在 Marketplace 端成功完成動作時，取得 webhook 通知。  而且，只有發行者才應該取消發行者端的訂閱。

*回應標頭：*

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  連結至資源以取得作業的狀態。  例如： `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 。 |

代碼：400不正確的要求。  此 SaaS 訂用帳戶的刪除不在 `allowedCustomerOperations` 清單中。

代碼：403禁止。  授權權杖無效、已過期或無法使用。 要求嘗試存取的 SaaS 訂用帳戶，與用來建立授權權杖的供應專案不同 Azure AD App 識別碼。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。

代碼：404找不到。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。 請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

### <a name="operations-apis"></a>作業 Api

#### <a name="list-outstanding-operations"></a>列出未完成的作業 

取得指定 SaaS 訂閱的暫止作業清單。  發行者必須藉由呼叫作業[修補程式 API](#update-the-status-of-an-operation)來確認傳回的作業。

目前只有**復原作業**會當做此 API 呼叫的回應傳回。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>獲取`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  使用2018-08-31。         |
|    `subscriptionId` | 已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。  |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

程式碼：200會在指定的 SaaS 訂用帳戶上傳回暫止的恢復作業。

*回應承載範例：*

```json
{"operations": [{
    "id": "<guid>",  //Operation ID, should be provided in the operations patch API call
    "activityId": "<guid>", //not relevant
    "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
    "offerId": "offer1",  // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver",  // purchased plan ID
    "quantity": "20", // purchased amount of seats, will be empty is not relevant
    "action": "Reinstate", 
    "timeStamp": "2018-12-01T00:00:00",  // UTC
    "status": "InProgress" // the only status that can be returned in this case
}]
}
```

如果沒有暫止的復原作業，則會傳回空的 json。

代碼：400不正確的要求：驗證失敗。

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求嘗試存取的 SaaS 訂用帳戶，與用來建立授權權杖的供應專案不同 Azure AD App 識別碼。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。 

代碼：404找不到。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。 請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="get-operation-status"></a>取得作業狀態

可讓發行者追蹤指定之非同步作業的狀態： [**取消訂閱**]、[ **ChangePlan**] 或 [ **ChangeQuantity**]。

您 `operationId` 可以從作業 **-位置**、取得擱置作業 API 呼叫所傳回的值，或 `<id>` 在 webhook 呼叫中收到的參數值，抓取此 API 呼叫的。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>獲取`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`    |  已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。 |
|  `operationId`       |  正在抓取之作業的唯一識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  唯一的存取權杖，可識別發出此 API 呼叫的發行者。  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

代碼：200取得指定 SaaS 作業的詳細資料。 

*回應承載範例：*

```json
Response body:
{
    "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
    "activityId": "<guid>", //not relevant
    "subscriptionId":"<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
    "offerId": "offer1", // purchased offer ID
    "publisherId": "contoso",  
    "planId": "silver", // purchased plan ID
    "quantity": "20", // purchased amount of seats 
    "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
    "timeStamp": "2018-12-01T00:00:00", // UTC
    "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeed, Conflict (new quantity / plan is the same as existing)

"errorStatusCode": "",
"errorMessage": ""
}
```

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求嘗試存取的 SaaS 訂用帳戶，與用來建立授權權杖的供應專案不同 Azure AD App 識別碼。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。 

代碼：404找不到。  

* 找不到具有的訂用帳戶 `subscriptionId` 。
* `operationId`找不到具有的作業。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="update-the-status-of-an-operation"></a>更新作業的狀態

更新暫止作業的狀態，以指出發行者端的作業成功或失敗。

您 `operationId` 可以從作業 **-位置**所傳回的值、取得暫止的作業 API 呼叫或 `<id>` 在 webhook 呼叫中收到的參數值，抓取此 API 呼叫的。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>跳`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  使用2018-08-31。  |
|   `subscriptionId`   |  已購買的 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析 marketplace 授權權杖之後，就會取得此識別碼。  |
|   `operationId`      |  正在完成之作業的唯一識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|   `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `authorization`     |  唯一的存取權杖，可識別正在進行此 API 呼叫的發行者。  格式是 `"Bearer <access_token>"` 發行者抓取權杖值的時間，如根據[Azure AD 應用程式取得權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*要求承載範例：*

```json
{ 
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*回應碼：*

Code：200呼叫以通知合作夥伴端的作業完成。  例如，此回應可能表示在發行者端的基座或計畫變更完成。

代碼：403禁止。  授權權杖無法使用、無效或已過期。 要求可能正嘗試存取不屬於目前發行者的訂閱。
禁止。  授權權杖無效、已過期或未提供。  要求嘗試存取的 SaaS 訂用帳戶，與用來建立授權權杖的供應專案不同 Azure AD App 識別碼。

此錯誤通常是未正確執行[SaaS 註冊](pc-saas-registration.md)的徵兆。

代碼：404找不到。

* 找不到具有的訂用帳戶 `subscriptionId` 。
* `operationId`找不到具有的作業。

代碼：409衝突。  例如，已經滿足了較新的更新。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  若錯誤持續發生，請聯絡[Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服務上執行 webhook

在合作夥伴中心建立 transactable SaaS 供應專案時，合作夥伴會提供要當做 HTTP 端點使用的連線**Webhook** URL。  Microsoft 會使用 POST HTTP 呼叫來呼叫此 webhook，以通知發行者端發生在 Microsoft 端的下列事件：

* 當 SaaS 訂用帳戶處於「已訂閱」狀態時：
    * ChangePlan 
    * ChangeQuantity
    * 暫止
    * 取消訂閱
* 當 SaaS 訂用帳戶處於暫停狀態時：
    * 恢復
    * 取消訂閱

發行者必須在 SaaS 服務中實作為 webhook，讓 SaaS 訂用帳戶狀態與 Microsoft 端保持一致。  在根據 webhook 通知採取動作之前，必須先呼叫「取得作業 API」來驗證和授權 webhook 呼叫和內容資料。  一旦處理 webhook 呼叫，發行者應該會將 HTTP 200 傳回給 Microsoft。  此值會確認發行者已成功接收 webhook 呼叫。

>[!Note]
>Webhook url 服務必須全天候啟動並執行，而且隨時都可從 Microsoft 時間接收新的呼叫。  Microsoft 具有 webhook 呼叫的重試原則（500重試超過8小時），但如果發行者不接受呼叫並傳迴響應，webhook 通知的作業最後會在 Microsoft 端失敗。

*Webhook 承載範例：*

```json
// end customer changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": <guid>, // this is the operation ID to call with get operation API
  "activityId": "<guid>", // do not use
  "subscriptionId": "guid", // The GUID identifier for the SaaS resource which status changes
  "publisherId": "contoso", // A unique string identifier for each publisher
  "offerId": "offer1", // A unique string identifier for each offer
  "planId": "silver", // the most up-to-date plan ID
  "quantity": " 25", // the most up-to-date number of seats, can be empty if not relevant
  "timeStamp": "2019-04-15T20:17:31.7350641Z", // UTC time when the webhook was called
  "action": "ChangeQuantity", // the operation the webhook notifies about
  "status": "Success" // Can be either InProgress or Success  
}
```

```json
// end customer's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": <guid>, 
  "activityId": <guid>, 
  "subscriptionId": "guid", 
  "publisherId": "contoso",
  "offerId": "offer2 ",
  "planId": "gold", 
  "quantity": " 20", 
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Reinstate",
  "status": "In Progress" 
} 
```

## <a name="development-and-testing"></a>開發與測試

若要開始開發程式，建議您在發行者端建立虛擬 API 回應。  這些回應可以根據本檔中提供的範例回應。

當「發行者」準備好進行端對端測試時： 

* 將 SaaS 供應專案發佈至有限的預覽物件，並將其保留在預覽階段。
* 此供應專案應具有0價格的方案，因此不會在測試時觸發實際的計費費用。  另一個選項是設定非零的價格，並在24小時內取消所有測試購買。 
* 請確定所有流程都是以端對端的方式叫用，就像客戶會購買供應專案一樣。 
* 如果合作夥伴想要測試完整的採購和計費流程，請使用高於 $0 的供應專案來執行此動作。  購買將會計費，並會產生發票。

購買流程可以從 Azure 入口網站或 Microsoft AppSource 網站觸發，視供應專案的發佈位置而定。

「*變更計畫*」、「*變更數量*」和「*取消訂閱*」動作都是從「發行者」端進行測試。  從 Microsoft 端，您可以從 Azure 入口網站和系統管理中心（管理 Microsoft AppSource 購買的入口網站）觸發*取消訂閱*。  *變更數量和方案*只能從系統管理中心觸發。

## <a name="get-support"></a>取得支援

如需發行者支援選項，請參閱[合作夥伴中心的商業 marketplace 方案支援](support.md)。


## <a name="next-steps"></a>後續步驟

如需 marketplace 中 SaaS 供應專案的更多選項，請參閱 Marketplace[計量服務 api](marketplace-metering-service-apis.md) 。

檢查並使用以本檔所述之 Api 為基礎的[SAAS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) 。
