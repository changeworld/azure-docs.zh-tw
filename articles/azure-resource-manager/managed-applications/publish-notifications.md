---
title: 帶通知的託管應用
description: 使用 Webhook 終結點配置託管應用程式，以接收有關託管應用程式實例上的創建、更新、刪除和錯誤的通知。
ms.topic: conceptual
ms.author: ilahat
author: ilahat
ms.date: 11/01/2019
ms.openlocfilehash: ff058d7b51bd2e5efd80db69e5928d58fc5a7725
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76715680"
---
# <a name="azure-managed-applications-with-notifications"></a>具有通知的 Azure 託管應用程式

Azure 託管代理程式更新允許發行者根據託管應用程式實例的生命週期事件自動執行操作。 發行者可以指定自訂通知 Webhook 終結點來接收有關新和現有託管應用程式實例的事件通知。 發行者可以在應用程式預配、更新和刪除時設置自訂工作流。

## <a name="getting-started"></a>開始使用
要開始接收託管應用程式，請啟動公共 HTTPS 終結點並在發佈服務目錄應用程式定義或 Azure 應用商店產品/服務時指定它。

以下是快速入門的建議步驟：
1. 啟動公共 HTTPS 終結點，該終結點記錄傳入的 POST`200 OK`請求並返回 。
2. 將終結點添加到服務目錄應用程式定義或 Azure 應用商店產品/服務，如本文後面所述。
3. 創建引用應用程式定義或 Azure 應用商店產品/服務的應用程式實例。
4. 驗證是否收到通知。
5. 啟用本文的 **"終結點身份驗證**"部分中所述的授權。
6. 按照本文 **"通知架構**"部分中的說明來分析通知請求，並根據通知實現業務邏輯。

## <a name="add-service-catalog-application-definition-notifications"></a>添加服務目錄應用程式定義通知
#### <a name="azure-portal"></a>Azure 入口網站
要開始，請參閱[通過 Azure 門戶發佈服務目錄應用程式](./publish-portal.md)。

![Azure 門戶中的服務目錄應用程式定義通知](./media/publish-notifications/service-catalog-notifications.png)

#### <a name="rest-api"></a>REST API

> [!NOTE]
> 目前，只能在 應用程式定義屬性中提供一`notificationEndpoints`個終結點。

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
## <a name="add-azure-marketplace-managed-application-notifications"></a>添加 Azure 應用商店託管代理程式更新
有關詳細資訊，請參閱創建[Azure 應用程式產品/服務](../../marketplace/cloud-partner-portal/azure-applications/cpp-create-offer.md)。

![Azure 應用商店託管代理程式更新在 Azure 門戶中](./media/publish-notifications/marketplace-notifications.png)
## <a name="event-triggers"></a>事件觸發程序
下表描述了事件種類和預配狀態及其觸發器的所有可能組合：

EventType | ProvisioningState | 通知觸發器
---|---|---
PUT | 已接受 | 託管資源組已在應用程式 PUT 後成功創建和投影（在啟動託管資源組內部署之前）。
PUT | 成功 | 託管應用程式的完整預配在 PUT 後成功。
PUT | 失敗 | 應用程式實例預配的 PUT 失敗。
PATCH | 成功 | 在託管應用程式實例上成功使用 PATCH 更新標記、JIT 訪問策略或託管標識後。
刪除 | 刪除中 | 使用者啟動託管應用實例的 DELETE 後，一旦使用者立即啟動。
刪除 | Deleted | 完整且成功刪除託管應用程式後。
刪除 | 失敗 | 在取消預配過程中阻止刪除的任何錯誤之後。
## <a name="notification-schema"></a>通知架構
當您啟動 Webhook 終結點以處理通知時，您需要分析有效負載以獲取重要屬性，然後對通知執行操作。 服務目錄和 Azure 應用商店託管代理程式更新提供許多相同的屬性。 在示例後面的表中概述了兩個小差異。

#### <a name="service-catalog-application-notification-schema"></a>服務目錄代理程式更新架構
以下是成功預配託管應用程式實例後的示例服務目錄通知：
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

如果預配失敗，將發送帶有錯誤詳細資訊的通知到指定的終結點。

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

#### <a name="azure-marketplace-application-notification-schema"></a>Azure 應用商店代理程式更新架構

以下是成功預配託管應用程式實例後的示例服務目錄通知：
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

如果預配失敗，將發送帶有錯誤詳細資訊的通知到指定的終結點。

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

參數 | 描述
---|---
eventType | 觸發通知的事件種類。 （例如，PUT、PATCH、刪除。
applicationId | 觸發通知的託管應用程式的完全限定資源識別碼。
eventTime | 觸發通知的事件的時間戳記。 （UTC ISO 8601 格式的日期和時間。
provisioningState | 託管應用程式實例的預配狀態。 （例如，成功、失敗、刪除、刪除。
error | *僅當預配狀態失敗時指定*。 包含導致故障的錯誤代碼、消息和問題的詳細資訊。
應用程式定義Id | *僅為服務目錄託管應用程式指定*。 表示為其預配託管應用程式實例的應用程式定義的完全限定資源識別碼。
計劃 | *只為 Azure 應用商店託管應用程式指定*。 表示託管應用程式實例的發行者、產品/服務、SKU 和版本。
帳單詳細資訊 | *僅為 Azure 應用商店託管應用程式指定。* 託管應用程式實例的計費詳細資訊。 包含可用於查詢 Azure 應用商店的"使用 Id"資源，以瞭解使用方式詳細資訊。

## <a name="endpoint-authentication"></a>端點身份驗證
要保護 Webhook 終結點並確保通知的真實性，可以：
1. 在 Webhook URI 頂部提供查詢參數，如下所示：HTTPs\://您的終結點.com？sig_Guid。 在每次通知時，檢查查詢參數`sig`具有預期值`Guid`。
2. 使用應用程式 Id 在託管應用程式實例上發出 GET。 驗證預配狀態是否與通知的預配狀態匹配，以確保一致性。

## <a name="notification-retries"></a>通知重試

託管代理程式更新服務需要從`200 OK`Webhook 終結點對通知的回應。 如果 Webhook 終結點返回大於或等於 500 的 HTTP 錯誤代碼、返回錯誤代碼 429 或終結點暫時無法訪問，則通知服務將重試。 如果 Webhook 終結點在 10 小時內不可用，通知訊息將被刪除，重試將停止。
