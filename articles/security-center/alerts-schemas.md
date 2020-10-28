---
title: Azure 資訊安全中心警示的架構
description: 本文說明 Azure 資訊安全中心用於安全性警示的不同架構。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: f9b3be69ab57c0abf7523169303def899f325229
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789211"
---
# <a name="security-alerts-schemas"></a>安全性警示架構

如果您的訂用帳戶已啟用 Azure Defender，當安全性中心偵測到對其資源的威脅時，您將會收到安全性警示。

您可以在 Azure 資訊安全中心的 **威脅防護** 頁面中查看這些安全性警示，或透過外部工具（例如：

- [Azure Sentinel](../sentinel/index.yml) -Microsoft 的雲端原生 SIEM。 Sentinel 連接器會從 Azure 資訊安全中心取得警示，並將其傳送至 [Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md) 以進行 Azure Sentinel。
- 協力廠商 Siem-將資料傳送至 [Azure 事件中樞](../event-hubs/index.yml)。 然後將您的事件中樞資料與協力廠商 SIEM 整合。 深入瞭解 [串流警示至 SIEM、SOAR 或 IT 服務管理解決方案](export-to-siem.md)。
- [REST API](/rest/api/securitycenter/) -如果您要使用 REST API 來存取警示，請參閱 [線上警示 API 檔](/rest/api/securitycenter/alerts)。

如果您使用任何程式設計方法來取用警示，您需要正確的架構，才能找到與您相關的欄位。 此外，如果您要匯出至事件中樞，或嘗試使用泛型 HTTP 連接器來觸發工作流程自動化，請使用架構來適當地剖析 JSON 物件。

>[!IMPORTANT]
> 每個案例的架構稍有不同，因此請務必選取下方的相關索引標籤。


## <a name="the-schemas"></a>架構 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[工作流程自動化和連續匯出至事件中樞](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>傳送給 Logic Apps、事件中樞和協力廠商 Siem 之警示的 JSON 範例

您可以在下面找到傳遞給的警示事件的架構：

- 在安全中心的工作流程自動化中設定的 Azure 邏輯應用程式實例
- 使用「安全性中心」連續匯出功能的 Azure 事件中樞

如需工作流程自動化功能的詳細資訊，請參閱 [自動化資訊安全中心觸發](workflow-automation.md)程式的回應。

如需連續匯出的詳細資訊，請參閱 [持續匯出資訊安全中心資料](continuous-export.md)。

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure Sentinel 與 Log Analytics 工作區](#tab/schema-sentinel)

Sentinel 連接器會從 Azure 資訊安全中心取得警示，並將其傳送至 Log Analytics 工作區以進行 Azure Sentinel。 

若要使用「安全性中心」警示來建立 Sentinel 案例或事件，您將需要這些警示的架構，如下所示。 

如需 Azure Sentinel 的詳細資訊，請參閱 [檔](../sentinel/index.yml)。

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure 活動記錄](#tab/schema-activitylog)

Azure 資訊安全中心會在 Azure 活動記錄中以事件形式來審核產生的安全性警示。

您可以藉由搜尋啟動警示事件來查看活動記錄中的安全性警示事件，如下所示：

[![搜尋活動記錄檔中的啟動警示事件](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>傳送至 Azure 活動記錄的警示範例 JSON

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>架構的資料模型

|欄位|描述|
|----|----|
|**管道**|常數，"Operation"|
|**correlationId**|Azure 資訊安全中心警示識別碼|
|**description**|警示的描述|
|**eventDataId**|請參閱 correlationId|
|**名稱**|值和 localizedValue 子欄位包含警示顯示名稱|
|**類別**|值和 localizedValue 子欄位都是常數-"Security"|
|**eventTimestamp**|產生警示時的 UTC 時間戳記|
|**id**|完整的警示識別碼|
|**level**|常數，"資訊"|
|**operationId**|請參閱 correlationId|
|**operationName**|值欄位是常數-"Microsoft. Security/位置/alerts/activate/action"，而當地語系化值會是「啟動警示」 (可能會當地語系化為使用者地區設定) |
|**resourceGroupName**|將包含資源組名|
|**resourceProviderName**|值和 localizedValue 子欄位都是常數-"Microsoft. Security"|
|**resourceType**|值和 localizedValue 子欄位都是常數-"Microsoft. Security/位置/警示"|
|**resourceId**|完整的 Azure 資源識別碼|
|**status**|值和 localizedValue 子欄位都是常數-"Active"|
|**狀態值**|值和 localizedValue 子欄位是空的|
|**submissionTimestamp**|事件提交至活動記錄的 UTC 時間戳記|
|**subscriptionId**|遭盜用之資源的訂用帳戶識別碼|
|**properties**|與警示相關之其他屬性的 JSON 包。 這些可能會從一個警示變更為另一個警示，不過，下欄欄位將會出現在所有警示中：<br>-嚴重性：攻擊的嚴重性<br>-compromisedEntity：遭盜用的資源名稱<br>-remediationSteps：要採取的補救步驟陣列<br>-意圖：警示的終止鏈意圖。 [意圖資料表](alerts-reference.md#intentions)中記載了可能的意圖|
|**relatedEvents**|常數-空陣列|
|||





### <a name="ms-graph-api"></a>[MS 圖形 API](#tab/schema-graphapi)

Microsoft Graph 是 Microsoft 365 中資料和智慧的閘道。 它提供統一的可程式性模型，可讓您用來存取 Microsoft 365、Windows 10 和 Enterprise Mobility + Security 的大量資料。 在 Microsoft Graph 中使用豐富的資料來建立與數百萬個使用者互動之組織和取用者的應用程式。

針對傳送至 MS Graph 的安全性警示，架構和 JSON 標記法可在 [Microsoft Graph 檔](/graph/api/resources/alert?preserve-view=true&view=graph-rest-1.0)中取得。

---


## <a name="next-steps"></a>後續步驟

本文說明在傳送安全性警示資訊時，Azure 資訊安全中心威脅防護工具所使用的架構。

如需有關從資訊安全中心存取安全性警示方式的詳細資訊，請參閱下列頁面：

- [Azure Sentinel](../sentinel/index.yml) -Microsoft 的雲端原生 SIEM
- [Azure 事件中樞](../event-hubs/index.yml) -Microsoft 完全受控的即時資料內嵌服務
- [持續匯出安全中心資料](continuous-export.md)
- [Log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md) -Azure 監視器將記錄資料儲存在 log Analytics 工作區中，包含資料和設定資訊的容器