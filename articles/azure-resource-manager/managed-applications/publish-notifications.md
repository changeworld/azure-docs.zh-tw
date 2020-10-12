---
title: 具有通知的受控應用程式
description: 使用 webhook 端點設定受控應用程式，以接收有關受控應用程式實例上建立、更新、刪除和錯誤的通知。
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: 3632a34678c7a0f0e6fa93e5ce8000b07bb413a6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86054520"
---
# <a name="azure-managed-applications-with-notifications"></a>具有通知的 Azure 受控應用程式

Azure 受控代理程式更新可讓發行者根據受控應用程式實例的生命週期事件，將動作自動化。 發行者可以指定自訂通知 webhook 端點，以接收關於新的和現有受控應用程式實例的事件通知。 發行者可以在應用程式布建、更新和刪除時設定自訂工作流程。

## <a name="getting-started"></a>開始使用
若要開始接收受控應用程式，請在發佈服務類別目錄應用程式定義或 Azure Marketplace 供應專案時，啟動公用 HTTPS 端點並指定它。

以下是快速入門的建議步驟：
1. 啟動可記錄傳入 POST 要求並傳回的公用 HTTPS 端點 `200 OK` 。
2. 將端點新增至服務類別目錄應用程式定義或 Azure Marketplace 供應專案，如本文稍後所述。
3. 建立參考應用程式定義或 Azure Marketplace 供應專案的受控應用程式實例。
4. 驗證收到的通知。
5. 如本文的「 **端點驗證** 」一節所述，啟用授權。
6. 遵循本文的「 **通知架構** 」一節中的指示，來剖析通知要求，並根據通知來執行您的商務邏輯。

## <a name="add-service-catalog-application-definition-notifications"></a>新增服務類別目錄應用程式定義通知
#### <a name="azure-portal"></a>Azure 入口網站
若要開始使用，請參閱 [透過 Azure 入口網站發佈服務類別目錄應用程式](./publish-portal.md)。

![Azure 入口網站中的服務類別目錄應用程式定義通知](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> 目前，您只能在 `notificationEndpoints` 中的應用程式定義屬性中提供一個端點。

``` JSON
    {
      "properties": {
        "isEnabled": true,
        "lockLevel": "ReadOnly",
        "displayName": "Sample Application Definition",
        "description": "Notification-enabled application definition.",
        "notificationPolicy": {
            "notificationEndpoints": [
                {
                    "uri": "https://isv.azurewebsites.net:1214?sig=unique_token"
                }
            ]
        },
        "authorizations": [
          {
            "principalId": "d6b7fbd3-4d99-43fe-8a7a-f13aef11dc18",
            "roleDefinitionId": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635"
          },
        ...

```
## <a name="add-azure-marketplace-managed-application-notifications"></a>新增 Azure Marketplace 受控代理程式更新
如需詳細資訊，請參閱 [建立 Azure 應用程式供應](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md)專案。

![在 Azure 入口網站中 Azure Marketplace 受控代理程式更新](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>事件觸發程序
下表描述所有可能的事件組合和 ProvisioningState 組合及其觸發程式：

EventType | ProvisioningState | 通知的觸發程式
---|---|---
PUT | 已接受 | 受控資源群組已在應用程式 PUT (之後成功建立並投射，) 開始進行受控資源群組中的部署。
PUT | 成功 | 在 PUT 之後，受控應用程式的完整布建成功。
PUT | Failed | 在任何時間點都無法布建應用程式實例。
PATCH | 成功 | 在受控應用程式實例上成功修補之後，更新標記、JIT 存取原則或受控識別。
刪除 | 刪除 | 一旦使用者開始刪除受管理的應用程式實例。
刪除 | 已刪除 | 完整和成功刪除受控應用程式之後。
刪除 | Failed | 在解除布建程式中封鎖刪除的任何錯誤之後。
## <a name="notification-schema"></a>通知架構
當您啟動 webhook 端點來處理通知時，您將需要剖析承載以取得重要屬性，然後在通知上採取動作。 服務類別目錄和 Azure Marketplace 受控代理程式更新提供許多相同的屬性。 以下是範例中的表格所述的兩個小差異。

#### <a name="service-catalog-application-notification-schema"></a>服務類別目錄代理程式更新架構
以下是成功布建受控應用程式實例之後的範例服務類別目錄通知：
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>"    
}

```

如果布建失敗，會將包含錯誤詳細資料的通知傳送至指定的端點。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "applicationDefinitionId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applicationDefinitions/<appDefName>",
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

#### <a name="azure-marketplace-application-notification-schema"></a>Azure Marketplace 代理程式更新架構

以下是成功布建受控應用程式實例之後的範例服務類別目錄通知：
``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Succeeded",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    }
}

```

如果布建失敗，會將包含錯誤詳細資料的通知傳送至指定的端點。

``` HTTP
POST https://{your_endpoint_URI}/resource?{optional_parameter}={optional_parameter_value} HTTP/1.1

{
    "eventType": "PUT",
    "applicationId": "subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.Solutions/applications/<applicationName>",
    "eventTime": "2019-08-14T19:20:08.1707163Z",
    "provisioningState": "Failed",
    "billingDetails": {
        "resourceUsageId":"<resourceUsageId>"
    },
    "plan": {
        "publisher": "publisherId",
        "product": "offer",
        "name": "skuName",
        "version": "1.0.1"
    },
    "error": {
        "code": "ErrorCode",
        "message": "error message",
        "details": [
            {
                "code": "DetailedErrorCode",
                "message": "error message"
            }
        ]
    }
}

```

參數 | 說明
---|---
eventType | 觸發通知的事件種類。  (例如 PUT、PATCH、DELETE。 ) 
applicationId | 觸發通知之受控應用程式的完整資源識別碼。
eventTime | 觸發通知的事件時間戳記。  (UTC ISO 8601 格式的日期和時間。 ) 
provisioningState | 受控應用程式實例的布建狀態。 例如，成功、失敗、刪除、刪除 (。 ) 
error | *只有在 ProvisioningState 失敗時才指定*。 包含造成失敗之問題的錯誤碼、訊息和詳細資料。
applicationDefinitionId | *僅針對服務類別目錄受控應用程式指定*。 表示已布建 managed 應用程式實例之應用程式定義的完整資源識別碼。
計劃 | *僅針對 Azure Marketplace 受控應用程式指定*。 代表受控應用程式實例的發行者、供應專案、SKU 和版本。
billingDetails | *僅針對 Azure Marketplace 受控應用程式指定。* 受控應用程式實例的計費詳細資料。 包含可用來查詢 Azure Marketplace 以取得使用方式詳細資料的 resourceUsageId。

## <a name="endpoint-authentication"></a>端點驗證
若要保護 webhook 端點的安全，並確保通知的真實性：
1. 在 webhook URI 上方提供查詢參數，如下所示： HTTPs \: //your-endpoint.com？ sig = Guid。 在每個通知中，檢查查詢參數 `sig` 是否具有預期的值 `Guid` 。
2. 使用 applicationId 在受控應用程式實例上發出 GET。 驗證 provisioningState 是否符合通知的 provisioningState，以確保一致性。

## <a name="notification-retries"></a>通知重試

受控代理程式更新服務預期會有 `200 OK` 來自 webhook 端點的回應到通知。 如果 webhook 端點傳回的 HTTP 錯誤碼大於或等於500（如果傳回錯誤碼429），或如果暫時無法連線到端點，通知服務將會重試。 如果 webhook 端點未在10小時內變成可用，則會捨棄通知訊息，而重試將會停止。
