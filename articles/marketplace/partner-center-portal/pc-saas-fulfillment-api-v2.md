---
title: Microsoft 商業 marketplace 中的 SaaS 履行 Api v2
description: 瞭解如何使用履行 Api 第2版，在 Microsoft AppSource 和 Azure Marketplace 上建立和管理 SaaS 供應專案。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 0d7259972693b9ca12e0801007cbd3c0af45ca1c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937326"
---
# <a name="saas-fulfillment-apis-version-2-in-the-commercial-marketplace"></a>商業 marketplace 中的 SaaS 履行 Api 第2版

本文詳述的 Api 可讓合作夥伴以 Microsoft AppSource 和 Azure Marketplace (SaaS) 供應專案銷售軟體即服務。 需要發行者才能與這些 Api 整合，以在合作夥伴中心中發佈可交易 SaaS 供應專案。

## <a name="managing-the-saas-subscription-life-cycle"></a>管理 SaaS 訂用帳戶生命週期

商業 marketplace 會在使用者購買 SaaS 訂用帳戶之後，管理整個生命週期。 它會使用登陸頁面、履行 Api、作業 Api 和 webhook 作為驅動實際 SaaS 訂用帳戶啟用、使用方式、更新和取消的機制。 終端使用者的帳單是以 Microsoft 維護的 SaaS 訂用帳戶狀態為基礎。 

### <a name="states-of-a-saas-subscription"></a>SaaS 訂用帳戶的狀態

下圖顯示 SaaS 訂用帳戶的狀態和適用的動作。

![此圖顯示在 marketplace 中，軟體即服務訂用帳戶的生命週期。](./media/saas-subscription-lifecycle-api-v2.png)

#### <a name="purchased-but-not-yet-activated-pendingfulfillmentstart"></a>已購買但尚未啟用 (*PendingFulfillmentStart*) 

當使用者 (或 CSP) 在商用 marketplace 中購買 SaaS 供應專案之後，發行者應該會收到購買通知。 然後，發行者可以在發行者端為終端使用者建立和設定新的 SaaS 帳戶。

若要建立帳戶，請執行下列動作：

1. 客戶會在 Microsoft AppSource 或 Azure 入口網站中的成功購買之後，選取可用於 SaaS 供應專案的 [ **設定** ] 按鈕。 或者，客戶可以使用電子郵件中的 [ **設定** ] 按鈕，在購買後不久就會收到電子郵件。
2. 接著，Microsoft 會在新的瀏覽器索引標籤中開啟新的瀏覽器索引標籤，並使用權杖參數 (來自商用 marketplace) 的購買識別碼權杖，以通知合作夥伴購買。

這類呼叫的範例是 `https://contoso.com/signup?token=<blob>` ，而合作夥伴中心中此 SaaS 供應專案的登陸頁面 URL 設定為 `https://contoso.com/signup` 。 此權杖會為發行者提供唯一識別 SaaS 購買和客戶的識別碼。

>[!NOTE]
>在客戶從 Microsoft 端起始設定程式之前，不會收到 SaaS 購買的通知。

登陸頁面 URL 必須在每天都啟動並執行，而且隨時都能接收來自 Microsoft 的新電話。 如果登陸頁面變得無法使用，客戶將無法註冊 SaaS 服務並開始使用。

接下來，發行者必須呼叫 [SaaS 解析 API](#resolve-a-purchased-subscription)，並輸入 token 作為 header 參數的值，將 *權杖* 傳回給 Microsoft `x-ms-marketplace-token header` 。 由於解析 API 呼叫的結果，會交換權杖以取得 SaaS 購買的詳細資料，例如購買的唯一識別碼、購買的供應專案識別碼，以及購買的方案識別碼。

在登陸頁面上，客戶應該透過 Azure Active Directory (Azure AD) 單一登入 (SSO) ，登入新的或現有的 SaaS 帳戶。

發行者應執行 SSO，以提供 Microsoft 在此流程中所需的使用者體驗。 請務必使用多租使用者 Azure AD 應用程式，並在設定 SSO 時同時允許公司和學校帳戶或個人 Microsoft 帳戶。 這項需求只適用于登陸頁面，適用于已在使用 Microsoft 認證登入的情況下，重新導向至 SaaS 服務的使用者。 所有登入 SaaS 服務都不需要 SSO。

> [!NOTE]
>如果 SSO 要求系統管理員必須將許可權授與應用程式，則合作夥伴中心中的供應專案描述必須公開需要系統管理員層級的存取權。 這項洩漏是為了符合 [商業 marketplace 認證原則](/legal/marketplace/certification-policies#10003-authentication-options)。

登入之後，客戶應該在發行者端完成 SaaS 設定。 然後，發行者必須呼叫 [啟用訂](#activate-a-subscription) 用帳戶 API 來傳送信號，以 Azure Marketplace SaaS 帳戶的布建已完成。
此動作會啟動客戶的計費週期。 如果「啟用訂用帳戶 API」呼叫失敗，則不會向客戶收取購買費用。


![此圖顯示在布建案例中的 A P I 通話。](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="active-subscribed"></a>Active (已 *訂閱*) 

作用中的 *(已訂閱的)* 是已布建之 SaaS 訂用帳戶的穩定狀態。 在 Microsoft 端處理 [啟用訂](#activate-a-subscription) 用帳戶 API 呼叫之後，SaaS 訂用帳戶會標示為已 *訂閱*。 客戶現在可以在發行者端使用 SaaS 服務，將會收取費用。

當 SaaS 訂用帳戶已在使用中時，客戶可以從 Azure 入口網站或 Microsoft 365 系統管理中心選取 [ **管理 saas 體驗** ]。 此動作也會讓 Microsoft 使用 *權杖* 參數來呼叫 **登陸頁面 URL** ，如同啟用流程中所發生。 發行者應該區分新的購買和現有 SaaS 帳戶的管理，並據此處理此登陸頁面 URL 呼叫。

#### <a name="being-updated-subscribed"></a> (*訂閱*) 更新

此動作表示現有作用中 SaaS 訂用帳戶的更新正在由 Microsoft 和發行者進行處理。 這類更新可透過下列方式起始：

- 商業 marketplace 中的客戶。
- 來自商業市場的 CSP。
- 來自發行者之 SaaS 網站的客戶 (，但不是針對 CSP 購買) 。

有兩種類型的更新可供 SaaS 訂用帳戶使用：

- 當客戶選擇訂用帳戶的另一個方案時，更新方案。
- 當客戶變更訂用帳戶的購買基座數量時，更新數量。

只有有效的訂用帳戶可以更新。 當訂用帳戶更新時，其狀態會在 Microsoft 端保持作用中狀態。

##### <a name="update-initiated-from-the-commercial-marketplace"></a>從商業 marketplace 起始的更新

在此流程中，客戶會變更訂用帳戶方案或 Azure 入口網站或 Microsoft 365 系統管理中心的基座數量。

1. 輸入更新之後，Microsoft 會呼叫發行者的 webhook URL，在合作夥伴中心的 [連線 **webhook** ] 欄位中設定，並提供適當的 *動作* 和其他相關參數值。 
1. 發行者端應對 SaaS 服務進行必要的變更，並在完成時透過呼叫作業 [API 的更新狀態](#update-the-status-of-an-operation)來通知 Microsoft。
1. 如果修補程式是以「 *失敗* 」狀態傳送，則更新程式將無法在 Microsoft 端完成。 SaaS 訂用帳戶將會保留現有的方案和基座數量。

> [!NOTE]
> 發行者應該叫用修補程式，以在收到 webhook 通知之後，于 *10 秒的時間範圍內*，以失敗/成功回應 [更新作業 API 的狀態](#update-the-status-of-an-operation)。 如果未在10秒內收到作業狀態的修補程式，則會自動將變更計畫 *修補為成功*。 

下圖顯示從商業 marketplace 起始之更新案例的 API 呼叫順序。

![此圖顯示對 marketplace 起始的更新所呼叫的 A P。](./media/saas-update-status-api-v2-calls-marketplace-side.png)

##### <a name="update-initiated-from-the-publisher"></a>從發行者起始的更新

在此流程中，客戶會變更訂用帳戶方案或從 SaaS 服務本身購買的基座數量。 

1. 發行者程式碼必須先呼叫 [變更計畫 api](#change-the-plan-on-the-subscription) 和/或 [變更數量 api](#change-the-quantity-of-seats-on-the-saas-subscription) ，才能在發行者端進行要求的變更。 

1. Microsoft 會將變更套用至訂用帳戶，然後透過 **連接 Webhook** 通知發行者套用相同的變更。

1. 只有在發行者對 SaaS 訂用帳戶進行必要的變更，並透過呼叫作業 [API 的更新狀態](#update-the-status-of-an-operation)來完成變更時，才會通知 Microsoft。

下圖顯示從發行者端起始之更新案例的 API 呼叫順序。

![此圖顯示在發行者端起始的更新中呼叫的 P。](./media/saas-update-status-api-v2-calls-publisher-side.png)

#### <a name="suspended-suspended"></a>擱置的 (已 *暫* 止) 

此狀態表示尚未收到客戶的 SaaS 服務付款。 Microsoft 會在 SaaS 訂用帳戶狀態中通知發行者此項變更。 通知是透過呼叫 webhook 來完成，並將 *action* 參數設為 [已 *暫停*]。

發行者可能會或可能不會在發行者端對 SaaS 服務進行變更。 我們建議發行者將此資訊提供給已被擱置的客戶使用，並限制或封鎖客戶對 SaaS 服務的存取。 永遠不會收到付款的機率。

Microsoft 會為客戶提供30天的寬限期，然後再自動取消訂用帳戶。 當訂用帳戶處於 *暫停* 狀態時：

* 夥伴或 ISV 必須將 SaaS 帳戶保持在可復原狀態，才能還原完整的功能，而不會遺失任何資料或設定。
* 夥伴或 ISV 應該會要求復原訂用帳戶、如果在寬限期內收到付款，或在寬限期結束時取消布建訂用帳戶的要求。 這兩個要求都會透過 webhook 機制傳送。

在發行者採取任何動作之前，訂用帳戶狀態會變更為在 Microsoft 端暫停。 只有作用中的訂閱可以暫停。

#### <a name="reinstated-suspended"></a>已復原 (已 *暫* 止) 

此動作表示客戶的付款條件已再次生效、已對 SaaS 訂用帳戶付款，並正在復原訂用帳戶。 在此案例中： 

1. Microsoft 會呼叫 webhook，並將 *action* 參數設定為 *恢復* 值。
1. 發行者可確保訂閱會在發行者端完全正常運作。
1. 發行者會呼叫具有成功狀態的 [Patch 作業 API](#update-the-status-of-an-operation) 。
1. 恢復程式成功，並再次向客戶收取 SaaS 訂用帳戶的費用。 

如果修補程式是以「 *失敗* 」狀態傳送，reinstatement 程式不會在 Microsoft 端完成，而且訂用帳戶將會保持 *暫停* 狀態。

只有已暫止的訂閱可以恢復。 已暫停的 SaaS 訂用帳戶會在復原時保持 *暫停* 狀態。 完成此作業之後，訂用帳戶的狀態就會變成 *作用中狀態。*

#### <a name="renewed-subscribed"></a> (*訂閱*) 更新

在一個月或一年的訂閱期限結束時，Microsoft 會自動更新 SaaS 訂用帳戶。 所有 SaaS 訂閱的自動續約設定預設值都是 *true* 。 作用中的 SaaS 訂用帳戶將繼續定期更新。 當訂用帳戶更新時，Microsoft 不會通知發行者。 客戶可以透過 Microsoft 365 系統管理員入口網站關閉 SaaS 訂用帳戶的自動續約。 在此情況下，SaaS 訂用帳戶將會在目前的帳單期限結束時自動取消。 客戶也可以隨時取消 SaaS 訂用帳戶。

只有作用中的訂閱會自動更新。 訂用帳戶會在更新過程中保持作用中狀態，而且如果自動更新成功。 更新之後，訂用帳戶期限的開始和結束日期會更新為新的字詞日期。

如果自動更新因為付款問題而失敗，訂用帳戶將會被 *擱置* ，並會通知發行者。

#### <a name="canceled-unsubscribed"></a>取消取消 *訂閱* 的 ()  

訂用帳戶會透過取消發行者網站、Azure 入口網站或 Microsoft 365 系統管理中心的訂用帳戶，來回應明確客戶或 CSP 的動作。 訂用帳戶也可以隱含地取消（因為因為未付款）在處於 *暫停* 狀態30天后遭到過了。

當發行者收到取消 webhook 呼叫之後，它們應該會保留客戶資料，以在要求時保留至少七天的資料。 只有客戶資料可以刪除。

SaaS 訂用帳戶可在其生命週期中的任何時間點取消。 取消訂用帳戶之後，就無法重新開機。

## <a name="api-reference"></a>API 參考資料

本節記載 SaaS 訂用帳戶和作業 Api。

**訂** 用帳戶 api 應該用來處理從購買到取消的 SaaS 訂用帳戶生命週期。

**作業 api** 應該用來：

* 確認並確認已處理的 webhook 呼叫。
* 取得等候發行者認可的應用程式暫止作業清單。

> [!NOTE]
> TLS 1.2 版將會在最基本版本的 HTTPS 通訊之後強制執行。 請務必在您的程式碼中使用此 TLS 版本。 TLS 1.0 和1.1 版即將淘汰。

### <a name="subscription-apis"></a>訂用帳戶 Api

#### <a name="resolve-a-purchased-subscription"></a>解決購買的訂用帳戶

解析端點可讓發行者從商業 marketplace 交換購買識別碼權杖， (稱為購買的 *權杖* ， [但尚未啟用](#purchased-but-not-yet-activated-pendingfulfillmentstart)) 到持續性購買的 SAAS 訂用帳戶識別碼及其詳細資料。

當客戶重新導向至合作夥伴的登陸頁面 URL 時，會在此 URL 呼叫中將客戶識別權杖作為 *權杖* 參數傳遞。 夥伴預期會使用此權杖，並提出解析的要求。 解析 API 回應包含 SaaS 訂用帳戶識別碼，以及可唯一識別購買的其他詳細資料。 登陸頁面 URL 呼叫所提供的 *權杖* 通常會在24小時內有效。 如果您收到的 *權杖* 已過期，建議您將下列指引提供給終端使用者：

「我們無法識別此購買。 請在 Azure 入口網站或 Microsoft 365 管理中心重新開啟此 SaaS 訂用帳戶，然後再次選取 [設定帳戶] 或 [管理帳戶]。

呼叫 Resolve API 將會傳回所有支援狀態中 SaaS 訂閱的訂用帳戶詳細資料和狀態。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>發佈 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

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
|  `authorization`     |  識別發出此 API 呼叫之發行者的唯一存取權杖。 格式為 `"Bearer <accessaccess_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |
|  `x-ms-marketplace-token`  | 要解析的購買識別 *權杖* 參數。  當客戶重新導向至 SaaS 夥伴的網站時，會在登陸頁面 URL 呼叫中傳遞權杖 (例如： `https://contoso.com/signup?token=<token><authorization_token>`) 。 <br> <br>  請注意，所編碼的 *權杖* 值是登陸頁面 URL 的一部分，因此在此 API 呼叫中做為參數使用之前，必須先將其解碼。  <br> <br> 以下是 URL 中編碼字串的範例： `contoso.com/signup?token=ab%2Bcd%2Fef` ，其中 *token* 為 `ab%2Bcd%2Fef` 。  已解碼的相同權杖將會是： `Ab+cd/ef` |
| | |

*回應碼：*

程式碼：200會根據提供的來傳回唯一的 SaaS 訂用帳戶識別碼 `x-ms-marketplace-token` 。

回應主體範例：

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
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
      "startDate": "2019-05-31",
      "endDate": "2019-06-29"
    },
    "isTest": true,
    "isFreeTrial": false,
    "allowedCustomerOperations": ["Delete", "Update", "Read"],
    "sandboxType": "None",
    "sessionMode": "None"
  }
}

```

程式碼：400不正確的要求。 `x-ms-marketplace-token` 遺漏、格式不正確、無效或已過期。

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求嘗試存取供應專案的 SaaS 訂用帳戶，但該供應專案是使用不同的 Azure AD 應用程式識別碼所發佈，並與用來建立授權權杖的識別碼不同。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="activate-a-subscription"></a>啟用訂用帳戶

為終端使用者設定 SaaS 帳戶之後，發行者必須在 Microsoft 端呼叫「啟用訂閱 API」。  除非此 API 呼叫成功，否則不會向客戶收費。

##### <a name="post-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>發佈 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用2018-08-31。   |
| `subscriptionId` | 已購買之 SaaS 訂用帳戶的唯一識別碼。  使用 [解析 API](#resolve-a-purchased-subscription)解析商用 marketplace 授權權杖之後，就會取得此識別碼。
 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  這個字串會將用戶端作業的所有事件與伺服器端上的事件相互關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`      |  識別發出此 API 呼叫之發行者的唯一存取權杖。 格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*要求承載範例：*

```json
{  // needed for validation of the activation request
  "planId": "gold", // purchased plan, cannot be empty
  "quantity": "" // purchased number of seats, can be empty if plan is not per seat
}
```

*回應碼：*

代碼：200訂用帳戶已在 Microsoft 端標示為已訂閱。

此呼叫沒有回應主體。

程式碼：400不正確的要求：驗證失敗。

* `planId` 不存在於要求裝載中。
* `planId` 在要求承載中，與所購買的內容不符。
* `quantity` 在要求承載中，與已購買的內容不符
* SaaS 訂用帳戶處於已 *訂閱* 或已 *暫停* 狀態。

代碼：403禁止。 授權權杖無效、已過期或未提供。 要求嘗試存取供應專案的 SaaS 訂用帳戶，但該供應專案是使用不同的 Azure AD 應用程式識別碼所發佈，並與用來建立授權權杖的識別碼不同。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。

代碼：找不到404。 SaaS 訂用帳戶處於取消 *訂閱* 的狀態。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="get-list-of-all-subscriptions"></a>取得所有訂用帳戶的清單

此 API 會針對發行者在商業 marketplace 中發佈的所有供應專案，取得所有已購買 SaaS 訂用帳戶的清單。  系統會傳回所有可能狀態的 SaaS 訂用帳戶。 系統也會傳回取消訂閱的 SaaS 訂用帳戶，因為這項資訊不會在 Microsoft 端刪除。

API 會傳回每個頁面100的分頁結果。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>獲取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  --------   |  ---------------  |
| `ApiVersion`  |  使用2018-08-31。  |
| `continuationToken`  | 選擇性參數。 若要取得結果的第一頁，請保留空白。  使用參數中所傳回的值 `@nextLink` 來取出下一頁。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
| `content-type`       |  `application/json`  |
| `x-ms-requestid`     |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
| `authorization`      |  識別正在進行此 API 呼叫之發行者的唯一存取權杖。  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*回應碼：*

程式碼：200會根據發行者的授權權杖，傳回這個發行者所做之所有供應專案的所有現有訂閱清單。

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
      "allowedCustomerOperations": ["Read", "Update", "Delete"], // Indicates operations allowed on the SaaS subscription for beneficiary. For CSP-initiated purchases, this will always be "Read" because the customer cannot update or delete subscription in this flow.  Purchaser can perform all operations on the subscription.
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
      "allowedCustomerOperations": ["Read"],
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

如果找不到此發行者的購買 SaaS 訂用帳戶，則會傳回空的回應主體。

代碼：403禁止。 授權權杖無法使用、無效或已過期。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。 

代碼：500內部伺服器錯誤。 請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="get-subscription"></a>取得訂用帳戶

此 API 會針對發行者在商業 marketplace 中發佈的 SaaS 供應專案，抓取指定的已購買 SaaS 訂用帳戶。 使用此呼叫來依識別碼取得特定 SaaS 訂用帳戶的所有可用資訊，而不是呼叫用來取得所有訂用帳戶清單的 API。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>獲取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
| `ApiVersion`        |   使用2018-08-31。 |
| `subscriptionId`     |  已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`  |
|  `x-ms-requestid`    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `authorization`     | 識別正在進行此 API 呼叫之發行者的唯一存取權杖。 格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

程式碼：200根據提供的，傳回 SaaS 訂用帳戶的詳細資料 `subscriptionId` 。

*回應主體範例：*

```json
{
  "id": "<guid>", // purchased SaaS subscription ID
  "name": "Contoso Cloud Solution", // SaaS subscription name
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
  "saasSubscriptionStatus": " Subscribed ", // Indicates the status of the operation: PendingFulfillmentStart, Subscribed, Suspended or Unsubscribed.
  "term": { // the period for which the subscription was purchased
    "startDate": "2019-05-31", //format: YYYY-MM-DD. This is the date when the subscription was activated by the ISV and the billing started. This field is relevant only for Active and Suspended subscriptions.
    "endDate": "2019-06-29", // This is the last day the subscription is valid. Unless stated otherwise, the automatic renew will happen the next day. This field is relevant only for Active and Suspended subscriptions.
    "termUnit": "P1M" //where P1M is monthly and P1Y is yearly. Also reflected in the startDate and endDate values.
  }
}
```

代碼：403禁止。 授權權杖無效、已過期或未提供。 要求正在嘗試存取供應專案的 SaaS 訂用帳戶，該供應專案是使用不同于用來建立授權權杖的 Azure AD 應用程式識別碼所發行。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。 

代碼：找不到404。  找不到具有指定的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="list-available-plans"></a>列出可用的方案

此 API 會抓取由 `subscriptionId` 特定購買此供應專案所識別之 SaaS 供應專案的所有方案。  使用此呼叫來取得 SaaS 訂用帳戶的受益人可針對訂用帳戶更新的所有私用和公用方案清單。  傳回的方案將可在與已購買方案相同的地理位置中使用。

除了已購買的方案之外，此呼叫會傳回該客戶可用的方案清單。  清單可以呈現給發行者網站上的終端使用者。  終端使用者可以將訂用帳戶方案變更為傳回清單中的任何一個方案。  將方案變更為不在清單中的方案將會失敗。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>獲取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`    |  已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`     |  `application/json` |
|   `x-ms-requestid`   |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `x-ms-correlationid`  |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `authorization`     |  識別正在進行此 API 呼叫之發行者的唯一存取權杖。  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

程式碼：200會傳回現有 SaaS 訂用帳戶的所有可用方案清單，包括已購買的方案。

回應主體範例：

```json
{
  "plans": [
    {
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

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求可能會嘗試存取使用不同的 Azure AD 應用程式識別碼發行之供應專案的 SaaS 訂用帳戶，而該供應專案是用來建立授權權杖。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。 

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="change-the-plan-on-the-subscription"></a>變更訂用帳戶的方案

您可以使用此 API，將針對 SaaS 訂用帳戶購買的現有方案更新為 (公用或私用) 的新方案。  發行者必須在發行者端變更方案，以取得在商業 marketplace 中購買的 SaaS 訂用帳戶。

此 API *只能針對使用* 中的訂閱呼叫。  任何方案都可以變更為任何其他現有的方案 (公用或私用) ，而不是本身。  針對私人方案，客戶的租使用者必須定義為合作夥伴中心中方案物件的一部分。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>補丁 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
| `subscriptionId`     | 已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。 |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。 如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid`  | 用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  識別正在進行此 API 呼叫之發行者的唯一存取權杖。  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*要求承載範例：*

```json
{
  "planId": "gold" // the ID of the new plan to be purchased
}
```

*回應碼：*

代碼：202已接受變更計畫的要求，並以非同步方式處理。  夥伴預期會輪詢作業 **位置 URL** ，以判斷變更計畫要求成功或失敗。  每隔幾秒鐘就會進行輪詢，直到收到作業的最終狀態 *失敗*、 *成功* 或 *衝突* 為止。  最終作業狀態應該會快速傳回，但在某些情況下可能需要幾分鐘的時間。

當動作準備好在商業 marketplace 端順利完成時，合作夥伴也會收到 webhook 通知。  只有在發行者在發行者端進行計畫變更時才應該如此。

*回應標頭：*

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  取得作業狀態的 URL。  例如： `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 。 |

程式碼：400不正確的要求：驗證失敗。

* 新的計畫不存在或不適用於此特定 SaaS 訂用帳戶。
* 新的計畫與目前的計畫相同。
* 未 *訂閱* SaaS 訂用帳戶狀態。
* SaaS 訂用帳戶的更新作業不包含在中 `allowedCustomerOperations` 。

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求正在嘗試存取供應專案的 SaaS 訂用帳戶，該供應專案是使用不同于用來建立授權權杖的 Azure AD 應用程式識別碼所發行。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。

代碼：找不到404。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

>[!NOTE]
>您可以一次變更一個或多個基座的數量，而不是同時變更。

>[!Note]
>只有在明確核准使用者的變更之後，才能呼叫此 API。

#### <a name="change-the-quantity-of-seats-on-the-saas-subscription"></a>變更 SaaS 訂用帳戶上的基座數量

使用此 API 來更新 (增加或減少) 為 SaaS 訂用帳戶購買的基座數量。  當自發行者端的基座數目變更為在商業 marketplace 中建立的 SaaS 訂用帳戶時，發行者必須呼叫此 API。

基座數量不能超過目前方案中允許的數量。  在此情況下，發行者應該在變更基座數量之前變更方案。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>補丁 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`     | 已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。  |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid`  | 用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     | 識別正在進行此 API 呼叫之發行者的唯一存取權杖。  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*要求承載範例：*

```json
{
  "quantity": 5 // the new amount of seats to be purchased
}
```

*回應碼：*

代碼：202已接受變更數量的要求，並以非同步方式處理。 夥伴預期會輪詢作業 **位置 URL** ，以判斷變更數量要求是否成功或失敗。  每隔幾秒鐘就會進行輪詢，直到收到作業的最終狀態 *失敗*、 *成功* 或 *衝突* 為止。  最終的作業狀態應該會快速傳回，但在某些情況下可能需要幾分鐘的時間。

當動作準備好在商業 marketplace 端順利完成時，合作夥伴也會收到 webhook 通知。  只有在發行者在發行者端進行數量變更時才應該如此。

*回應標頭：*

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  連結至資源以取得作業的狀態。  例如： `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 。  |

程式碼：400不正確的要求：驗證失敗。

* 新數量大於或小於目前的方案限制。
* 缺少新的數量。
* 新數量與目前數量相同。
* SaaS 訂用帳戶狀態為 [未訂閱]。
* SaaS 訂用帳戶的更新作業不包含在中 `allowedCustomerOperations` 。

代碼：403禁止。  授權權杖無效、已過期或未提供。  要求嘗試存取的訂用帳戶不屬於目前的發行者。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。 

代碼：找不到404。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

>[!Note]
>一次只能變更一個方案或數量，不能同時變更兩者。

>[!Note]
>只有在使用者明確核准變更之後，才能呼叫此 API。

#### <a name="cancel-a-subscription"></a>取消訂用帳戶

使用此 API 來取消訂閱指定的 SaaS 訂用帳戶。  發行者不需要使用此 API，我們建議將客戶導向商用 marketplace 以取消 SaaS 訂閱。

如果發行者決定要針對發行者端的商業 marketplace 中所購買的 SaaS 訂用帳戶執行取消作業，則必須呼叫此 API。  完成此呼叫後，訂用帳戶的狀態將 *會在 Microsoft 端取消訂閱。*

如果訂用帳戶在下列寬限期內取消，就不會向客戶收費：

* 啟用之後，每月訂閱會有二十個小時。
* 啟用之後的每年訂用帳戶14天。

如果在先前的寬限期之後取消訂用帳戶，則會向客戶收費。  客戶將無法在取消之後立即失去 Microsoft 端 SaaS 訂用帳戶的存取權。 

##### <a name="delete-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>刪除 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`     | 已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。  |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      | `application/json`  |
|  `x-ms-requestid`    | 用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid`  | 用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  識別發出此 API 呼叫之發行者的唯一存取權杖。  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*回應碼：*

代碼：202已接受取消訂閱的要求，並以非同步方式處理。  夥伴預期會輪詢作業 **位置 URL** ，以判斷此要求是否成功或失敗。  每隔幾秒鐘就會進行輪詢，直到收到作業的最終狀態 *失敗*、 *成功* 或 *衝突* 為止。  最終的作業狀態應該會快速傳回，但在某些情況下可能需要幾分鐘的時間。

當商務 marketplace 上的動作順利完成時，合作夥伴也會收到 webhook 通知。  只有在發行者端取消訂閱者端的訂閱。

*回應標頭：*

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `Operation-Location`        |  連結至資源以取得作業的狀態。  例如： `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=2018-08-31` 。 |

程式碼：400不正確的要求。  此 SaaS 訂用帳戶的刪除不在 `allowedCustomerOperations` 清單中。

代碼：403禁止。  授權權杖無效、已過期或無法使用。 要求正在嘗試存取供應專案的 SaaS 訂用帳戶，該供應專案是使用不同于用來建立授權權杖的 Azure AD 應用程式識別碼所發行。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。

代碼：找不到404。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。 請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

### <a name="operations-apis"></a>作業 Api

#### <a name="list-outstanding-operations"></a>列出未完成的作業 

取得所指定 SaaS 訂用帳戶的暫止作業清單。  發行者應該藉由呼叫作業 [修補程式 API](#update-the-status-of-an-operation)來確認傳回的作業。

目前只會將 **恢復作業** 作為此 API 呼叫的回應傳回。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>獲取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|    `ApiVersion`    |  使用2018-08-31。         |
|    `subscriptionId` | 已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。  |

要求標頭：
 
|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`     |  `application/json` |
|  `x-ms-requestid`    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

程式碼：200傳回指定之 SaaS 訂用帳戶上的暫止恢復操作。

*回應承載範例：*

```json
{
  "operations": [
    {
      "id": "<guid>", //Operation ID, should be provided in the operations patch API call
      "activityId": "<guid>", //not relevant
      "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription that is being reinstated
      "offerId": "offer1", // purchased offer ID
      "publisherId": "contoso",
      "planId": "silver", // purchased plan ID
      "quantity": "20", // purchased amount of seats, will be empty is not relevant
      "action": "Reinstate",
      "timeStamp": "2018-12-01T00:00:00", // UTC
      "status": "InProgress" // the only status that can be returned in this case
    }
  ]
}
```

如果沒有任何恢復作業暫止，則會傳回空的 json。

程式碼：400不正確的要求：驗證失敗。

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求正在嘗試存取供應專案的 SaaS 訂用帳戶，該供應專案是使用不同于用來建立授權權杖的 Azure AD 應用程式識別碼所發行。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。 

代碼：找不到404。  找不到具有的 SaaS 訂用帳戶 `subscriptionId` 。

代碼：500內部伺服器錯誤。 請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="get-operation-status"></a>取得作業狀態

此 API 可讓發行者追蹤指定之非同步作業的狀態：  **取消訂閱**、 **ChangePlan** 或 **ChangeQuantity**。

您 `operationId` 可以從作業 **位置**、取得暫止作業 API 呼叫所傳回的值，或 `<id>` 在 webhook 呼叫中收到的參數值，抓取此 API 呼叫的。

##### <a name="get-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>獲取 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `ApiVersion`        |  使用2018-08-31。  |
|  `subscriptionId`    |  已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。 |
|  `operationId`       |  要抓取之作業的唯一識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|  `content-type`      |  `application/json`   |
|  `x-ms-requestid`    |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。  |
|  `authorization`     |  識別發出此 API 呼叫之發行者的唯一存取權杖。  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。  |

*回應碼：*

代碼：200取得所指定 SaaS 作業的詳細資料。 

*回應承載範例：*

```json
Response body:
{
  "id  ": "<guid>", //Operation ID, should be provided in the patch operation API call
  "activityId": "<guid>", //not relevant
  "subscriptionId": "<guid>", // subscriptionId of the SaaS subscription for which this operation is relevant
  "offerId": "offer1", // purchased offer ID
  "publisherId": "contoso",
  "planId": "silver", // purchased plan ID
  "quantity": "20", // purchased amount of seats
  "action": "ChangePlan", // Can be ChangePlan, ChangeQuantity or Reinstate
  "timeStamp": "2018-12-01T00:00:00", // UTC
  "status": "InProgress", // Possible values: NotStarted, InProgress, Failed, Succeeded, Conflict (new quantity / plan is the same as existing)
  "errorStatusCode": "",
  "errorMessage": ""
}
```

代碼：403禁止。 授權權杖無效、已過期或未提供。  要求正在嘗試存取供應專案的 SaaS 訂用帳戶，該供應專案是使用不同于用來建立授權權杖的 Azure AD 應用程式識別碼所發行。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。 

代碼：找不到404。  

* 找不到具有的訂用帳戶 `subscriptionId` 。
* `operationId`找不到具有的作業。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

#### <a name="update-the-status-of-an-operation"></a>更新作業的狀態

您可以使用此 API 來更新暫止作業的狀態，以指出作業在發行者端的成功或失敗。

您 `operationId` 可以從作業 **位置**、取得暫止作業 API 呼叫所傳回的值，或 `<id>` 在 webhook 呼叫中收到的參數值，抓取此 API 呼叫的。

##### <a name="patch-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>補丁 `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

查詢參數：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|   `ApiVersion`       |  使用2018-08-31。  |
|   `subscriptionId`   |  已購買之 SaaS 訂用帳戶的唯一識別碼。  使用解析 API 解析商用 marketplace 授權權杖之後，就會取得此識別碼。  |
|   `operationId`      |  正在完成之作業的唯一識別碼。 |

要求標頭：

|  參數         | 值             |
|  ---------------   |  ---------------  |
|   `content-type`   | `application/json`   |
|   `x-ms-requestid`   |  用於追蹤用戶端要求的特殊字串值，最好是全域唯一識別碼 (GUID)。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|   `x-ms-correlationid` |  用於用戶端作業的特殊字串值。  此參數會將來自用戶端作業的所有事件與伺服器端上的事件相關聯。  如果未提供此值，則系統會產生一個，並在回應標頭中提供。 |
|  `authorization`     |  識別正在進行此 API 呼叫之發行者的唯一存取權杖。  格式為 `"Bearer <access_token>"` ：發行者抓取權杖值的時間，如 [取得以 Azure AD 應用程式為基礎的權杖](./pc-saas-registration.md#get-the-token-with-an-http-post)中所述。 |

*要求承載範例：*

```json
{
  "status": "Success" // Allowed Values: Success/Failure. Indicates the status of the operation on ISV side.
}
```

*回應碼：*

程式碼：200呼叫以通知夥伴端的作業已完成。  例如，此回應可能表示在發行者端上的基座或方案變更已完成。

程式碼：403
- 禁止。  授權權杖無法使用、無效或已過期。 要求可能嘗試存取不屬於目前發行者的訂用帳戶。
- 禁止。  授權權杖無效、已過期或未提供。  要求正在嘗試存取供應專案的 SaaS 訂用帳戶，該供應專案是使用不同于用來建立授權權杖的 Azure AD 應用程式識別碼所發行。

此錯誤通常是無法正確執行 [SaaS 註冊](pc-saas-registration.md) 的徵兆。

代碼：找不到404。

* 找不到具有的訂用帳戶 `subscriptionId` 。
* `operationId`找不到具有的作業。

代碼：409衝突。  例如，已滿足較新的更新。

代碼：500內部伺服器錯誤。  請重試 API 呼叫。  如果錯誤持續發生，請洽詢 [Microsoft 支援服務](https://partner.microsoft.com/support/v2/?stage=1)。

## <a name="implementing-a-webhook-on-the-saas-service"></a>在 SaaS 服務上執行 webhook

在合作夥伴中心中建立可交易 SaaS 供應專案時，夥伴會提供要作為 HTTP 端點使用的連線 **Webhook** URL。  Microsoft 會使用 POST HTTP 呼叫來呼叫此 webhook，以通知發行者端發生在 Microsoft 端的下列事件：

* 當 SaaS 訂用帳戶處於已 *訂閱* 狀態時：
    * ChangePlan 
    * ChangeQuantity
    * 暫止
    * 取消訂閱
* 當 SaaS 訂用帳戶處於 *暫停* 狀態時：
    * 恢復
    * 取消訂閱

發行者必須在 SaaS 服務中執行 webhook，以保持 SaaS 訂用帳戶狀態與 Microsoft 端一致。  需要有 SaaS 服務，才能在根據 webhook 通知採取動作之前，呼叫取得作業 API 來驗證和授權 webhook 呼叫和承載資料。  一旦處理 webhook 呼叫，發行者就應該會傳回 HTTP 200 給 Microsoft。  此值會確認發行者已成功接收 webhook 呼叫。

>[!Note]
>Webhook URL 服務必須全天候啟動並執行，並準備好隨時從 Microsoft time 接收新的呼叫。  Microsoft 對於 webhook 呼叫會有重試原則， (500) 次重試，但如果發行者不接受呼叫並傳迴響應，則 webhook 通知的作業最後會在 Microsoft 端失敗。

*Webhook 承載範例：*

```json
// end user changed a quantity of purchased seats for a plan on Microsoft side
{
  "id": "<guid>", // this is the operation ID to call with get operation API
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
// end user's payment instrument became valid again, after being suspended, and the SaaS subscription is being reinstated
{
  "id": "<guid>",
  "activityId": "<guid>",
  "subscriptionId": "<guid>",
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

若要開始開發程式，建議您在發行者端建立虛擬 API 回應。  這些回應可以根據本文中提供的範例回應。

當發行者準備好進行端對端測試時：

* 將 SaaS 供應專案發佈至受限的預覽物件，並將其保留在預覽階段。
* 將方案價格設定為0，以避免在測試時觸發實際的計費費用。  另一個選項是設定非零的價格，並在24小時內取消所有測試購買。
* 確定所有流程都會以端對端叫用，以模擬實際的客戶案例。
* 如果夥伴想要測試完整的購買和計費流程，請使用價格高於 $0 的供應專案。  購買將會計費，而且會產生發票。

您可以根據供應專案發佈的位置，從 Azure 入口網站或 Microsoft AppSource 的網站觸發購買流程。

*變更計畫*、 *變更數量* 和 *取消訂閱* 動作都會從發行者端進行測試。  從 Microsoft 端 *開始，您* 可以從 Azure 入口網站和系統管理中心 (入口網站中的 Microsoft AppSource 購買) 進行管理。  您只能從系統管理中心觸發 *變更數量和方案*。

## <a name="get-support"></a>取得支援

如需發行者支援選項，請參閱 [合作夥伴中心中的商業 marketplace 方案支援](../support.md) 。

## <a name="next-steps"></a>後續步驟

請參閱 [商業 marketplace 計量服務 api](marketplace-metering-service-apis.md) ，以取得商用市集中 SaaS 供應專案的更多選項。

請參閱本檔中所述之 Api 之上建的 [SAAS SDK](https://github.com/Azure/Microsoft-commercial-marketplace-transactable-SaaS-offer-SDK) 。
