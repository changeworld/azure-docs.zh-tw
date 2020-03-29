---
title: Azure 安全中心警報的架構
description: 本文介紹了 Azure 安全中心用於安全警報的不同架構。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 19ca17f66f6818ed4c3ef532e2030cc03f0e73ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062957"
---
# <a name="security-alerts-schemas"></a>安全警報架構

當安全中心檢測到對其資源的威脅時，Azure 安全中心的標準層的使用者將收到安全警報。

您可以在 Azure 安全中心**的威脅保護**頁面或通過外部工具（如：

- [Azure 哨兵](https://docs.microsoft.com/azure/sentinel/)- 微軟的雲原生 SIEM。 Sentinel 連接器從 Azure 安全中心獲取警報，並將它們發送到 Azure Sentinel 的[日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。
- 協力廠商 SIEM - 使用安全中心的[連續匯出](continuous-export.md)工具將資料發送到[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)。 然後，將事件中心資料與協力廠商 SIEM 集成。
- [REST API](https://docs.microsoft.com/rest/api/securitycenter/) - 如果您使用 REST API 訪問警報，請參閱[連線警報 API 文檔](https://docs.microsoft.com/rest/api/securitycenter/alerts)。

如果您使用任何程式設計方法使用警報，則需要正確的架構來查找與您相關的欄位。 此外，如果要匯出到事件中心或嘗試使用通用 HTTP 連接器觸發工作流自動化，請使用架構正確解析 JSON 物件。

>[!IMPORTANT]
> 對於每種方案，架構略有不同，因此請確保選擇下面的相關選項卡。


## <a name="the-schemas"></a>架構 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[工作流自動化和連續匯出到事件中心](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>針對發送到邏輯應用、事件中心和協力廠商 SIEM 的警報的 JSON 示例

下面您將找到傳遞給的警報事件的架構：

- 在安全中心的工作流自動化中配置的 Azure 邏輯應用實例
- 使用安全中心的連續匯出功能的 Azure 事件中心

有關工作流自動化功能的詳細資訊，請參閱[自動回應警報和建議](workflow-automation.md)。
有關連續匯出的詳細資訊，請參閱[匯出警報和建議](continuous-export.md)。

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Azure 哨兵和日誌分析工作區](#tab/schema-sentinel)

Sentinel 連接器從 Azure 安全中心獲取警報，並將其發送到 Azure 哨兵的日誌分析工作區。 

要使用安全中心警報創建 Sentinel 案例或事件，您需要下面顯示的這些警報的架構。 

有關 Azure 哨兵的詳細資訊，請參閱[文檔](https://docs.microsoft.com/azure/sentinel/)。

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Azure 活動記錄檔](#tab/schema-activitylog)

Azure 安全中心審核生成安全警報作為 Azure 活動日誌中的事件。

您可以通過搜索啟動警報事件來查看活動日誌中的安全警報事件，如下所示：

[![搜索活動日誌以查找啟動警報事件](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>發送到 Azure 活動日誌的警報的示例 JSON

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
|**管道**|常量，"操作"|
|**相關性Id**|Azure 安全中心警報 ID|
|**描述**|警報說明|
|**eventDataId**|請參閱關聯 Id|
|**事件名稱**|值和當地語系化價值子欄位包含警報顯示名稱|
|**類別**|值和當地語系化價值子欄位是常量 - "安全"|
|**eventTimestamp**|生成警報時的 UTC 時間戳記|
|**id**|完全合格的警報 ID|
|**水準**|常量，"資訊"|
|**操作 Id**|請參閱關聯 Id|
|**操作名稱**|值欄位是常量 - "Microsoft.安全/位置/警報/啟動/操作"，當地語系化值將為"啟動警報"（可能是使用者地區設定的當地語系化標準）|
|**資源組名稱**|將包括資源組名稱|
|**resourceProviderName**|值和當地語系化價值子欄位是常數 - "Microsoft.Security"|
|**資源類型**|值和當地語系化價值子欄位是常量 - "Microsoft.安全/位置/警報"|
|**資源 Id**|完全限定的 Azure 資源識別碼|
|**狀態**|值和當地語系化價值子欄位是常量 - "活動"|
|**子狀態**|值和當地語系化價值子欄位為空|
|**submissionTimestamp**|事件提交到活動日誌的 UTC 時間戳記|
|**訂閱 Id**|已入侵資源的訂閱 ID|
|**性能**|與警報相關的其他屬性的 JSON 包。 這些欄位可能會從一個警報更改為另一個警報，但是，以下欄位將顯示在所有警報中：<br>- 嚴重性：攻擊的嚴重程度<br>- 洩露的實體：被入侵資源的名稱<br>- 補救步驟：要採取的補救步驟陣列<br>- 意圖：警報的殺傷鏈意圖。 可能的意圖記錄在["意圖"表中](alerts-reference.md#intentions)|
|**relatedEvents**|常量 - 空陣列|
|||





### <a name="ms-graph-api"></a>[MS 圖形 API](#tab/schema-graphapi)

微軟圖形是微軟365資料和智慧的閘道。 它提供了一個統一的可程式設計模型，可用於訪問 Office 365、Windows 10 和企業移動性 + 安全性中的大量資料。 使用 Microsoft Graph 中豐富的資料為與數百萬使用者交互的組織和消費者構建應用。

發送到 MS Graph 的安全警報的架構和 JSON 表示形式在[Microsoft 圖形文檔中](https://docs.microsoft.com/graph/api/resources/alert?view=graph-rest-1.0)可用。

---


## <a name="next-steps"></a>後續步驟

本文介紹了 Azure 安全中心的威脅保護工具在發送安全警報資訊時使用的架構。

有關從安全中心外部訪問安全警報的方法的詳細資訊，請參閱以下頁面：

- [Azure 哨兵](https://docs.microsoft.com/azure/sentinel/)- 微軟的雲原生 SIEM
- [Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/)- 微軟完全託管的即時資料引入服務
- 安全中心的[連續匯出功能](continuous-export.md)
- [日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)- Azure 監視器將日誌資料存儲在日誌分析工作區中，該工作區包含資料和配置資訊
