---
title: 為新建議創建 Azure 顧問警報
description: 為新建議創建 Azure 顧問警報
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 07cbc57ef718b6cac104d2b5238ff4e3196f197a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443162"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations"></a>創建新建議的 Azure 顧問警報 

本文演示如何使用 Azure 門戶和 Azure 資源管理器範本為 Azure 顧問的新建議設置警報。 

每當 Azure Advisor 檢測到對其中一個資源的新建議時，事件都會存儲在[Azure 活動日誌](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview)中。 您可以使用特定于建議的警報創建體驗從 Azure Advisor 設置這些事件的警報。 您可以選擇訂閱，也可以選擇資源組來指定要接收警報的資源。 

您還可以使用以下屬性確定建議的類型：

* 類別
* 影響級別
* 建議類型

您還可以配置警報觸發時將執行的操作：  

* 選擇現有操作組
* 創建新的操作組

要瞭解有關操作組的更多詳細資訊，請參閱[創建和管理操作組](../azure-monitor/platform/action-groups.md)。

> [!NOTE] 
> 顧問警報當前僅適用于高可用性、性能和成本建議。 不支援安全建議。 

## <a name="in-the-azure-portal"></a>在 Azure 入口網站
1. 在**門戶**中，選擇**Azure 顧問**。

    ![門戶中的 Azure 顧問](./media/advisor-alerts/create1.png)

2. 在左側功能表的 **"監視"** 部分中，選擇 **"警報**"。 

    ![顧問中的警報](./media/advisor-alerts/create2.png)

3. 選擇**新的顧問警報**。

    ![新顧問警報](./media/advisor-alerts/create3.png)

4. 在 **"範圍"** 部分中，選擇訂閱，並選擇要收到警報的資源組。 

    ![顧問警報範圍](./media/advisor-alerts/create4.png)

5. 在 **"條件"** 部分中，選擇要用於配置警報的方法。 如果要警惕特定類別和/或影響級別的所有建議，請選擇 **"類別"和"影響級別**"。 如果要警惕特定類型的所有建議，請選擇 **"建議"類型**。

    ![Azure 顧問警示準則](./media/advisor-alerts/create5.png)

6. 根據您選擇的"按配置"選項，您將能夠指定條件。 如果想要所有建議，只需將其餘欄位留空。 

    ![顧問警報操作組](./media/advisor-alerts/create6.png)

7. 在**操作組**部分中，選擇 **"添加現有**"以使用已創建的操作組，或選擇 **"新建"** 以設置新的[操作組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)。 

    ![顧問警報添加現有](./media/advisor-alerts/create7.png)

8. 在"警報詳細資訊"部分中，為警報指定名稱和簡短說明。 如果希望啟用警報，在創建選擇時將**啟用規則**保留為 **"是**"。 然後選擇要將警報保存到的資源組。 這不會影響建議的確定範圍。 

    ![Azure 顧問橫幅](./media/advisor-alerts/create8.png)


## <a name="with-an-azure-resource-manager-template"></a>使用 Azure 資源管理器範本

此資源管理器範本創建建議警報和新的操作組。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest2",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "groupShortName": "[parameters('actionGroups_name')]",
        "enabled": true,
        "emailReceivers": [
          {
            "name": "[parameters('actionGroups_name')]",
            "emailAddress": "[parameters('emailAddress')]"
          }
        ],
        "smsReceivers": [],
        "webhookReceivers": []
      },
      "dependsOn": []
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "name": "[parameters('activityLogAlerts_name')]",
      "apiVersion": "2017-04-01",
      "location": "Global",
      "tags": {},
      "scale": null,
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
            }
          ]
        },
        "actions": {
          "actionGroups": [
            {
              "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
              "webhookProperties": {}
            }
          ]
        },
        "enabled": true,
        "description": ""
      },
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ]
    }
  ]
}
  ```

## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>將建議警報配置為使用 Webhook
本節介紹如何配置 Azure Advisor 警報，以便通過 Webhook 將建議資料發送到現有系統。 

您可以設置警報，以便當您對其中一個資源有新的 Advisor 建議時收到通知。 這些警報可以通過電子郵件或短信通知您，但它們也可用於通過 Webhook 與現有系統集成。 


### <a name="using-the-advisor-recommendation-alert-payload"></a>使用顧問建議警報負載
如果要使用 Webhook 將 Advisor 警報集成到您自己的系統中，則需要分析從通知發送的 JSON 負載。 

為此警報設置操作組時，請選擇是否要使用公共警報架構。 如果選擇通用警報架構，則有效負載將如下所示： 

```json
{  
   "schemaId":"azureMonitorCommonAlertSchema",
   "data":{  
      "essentials":{  
         "alertId":"/subscriptions/<subid>/providers/Microsoft.AlertsManagement/alerts/<alerted>",
         "alertRule":"Webhhook-test",
         "severity":"Sev4",
         "signalType":"Activity Log",
         "monitorCondition":"Fired",
         "monitoringService":"Activity Log - Recommendation",
         "alertTargetIDs":[  
            "/subscriptions/<subid>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>"
         ],
         "originAlertId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "firedDateTime":"2019-07-17T23:00:57.3858656Z",
         "description":"A new recommendation is available.",
         "essentialsVersion":"1.0",
         "alertContextVersion":"1.0"
      },
      "alertContext":{  
         "channels":"Operation",
         "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
         "caller":"Microsoft.Advisor",
         "correlationId":"8554b847-2a72-48ef-9776-600aca3c3aab",
         "eventSource":"Recommendation",
         "eventTimestamp":"2019-07-17T22:28:54.1566942+00:00",
         "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
         "eventDataId":"001d8b40-5d41-4310-afd7-d65c9d4428ed",
         "level":"Informational",
         "operationName":"Microsoft.Advisor/recommendations/available/action",
         "properties":{  
            "recommendationSchemaVersion":"1.0",
            "recommendationCategory":"Performance",
            "recommendationImpact":"Medium",
            "recommendationName":"Increase the MariaDB server vCores",
            "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%<subscription id>%2FresourceGroups%2<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
            "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
         },
         "status":"Active",
         "subStatus":"",
         "submissionTimestamp":"2019-07-17T22:28:54.1566942+00:00"
      }
   }
}
  ```

如果不使用通用架構，則有效負載如下所示： 

```json
{  
   "schemaId":"Microsoft.Insights/activityLogs",
   "data":{  
      "status":"Activated",
      "context":{  
         "activityLog":{  
            "channels":"Operation",
            "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
            "caller":"Microsoft.Advisor",
            "correlationId":"3ea7320f-c002-4062-adb8-96d3bd92a5f4",
            "description":"A new recommendation is available.",
            "eventSource":"Recommendation",
            "eventTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
            "eventDataId":"a12b8e59-0b1d-4003-bfdc-3d8152922e59",
            "level":"Informational",
            "operationName":"Microsoft.Advisor/recommendations/available/action",
            "properties":{  
               "recommendationSchemaVersion":"1.0",
               "recommendationCategory":"Performance",
               "recommendationImpact":"Medium",
               "recommendationName":"Increase the MariaDB server vCores",
               "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/source/ActivityLog/recommendationTypeId/a5f888e3-8cf4-4491-b2ba-b120e14eb7ce/resourceId/%2Fsubscriptions%2F<subscription id>%2FresourceGroups%2F<resource group name>%2Fproviders%2FMicrosoft.DBforMariaDB%2Fservers%2F<resource name>",
               "recommendationType":"a5f888e3-8cf4-4491-b2ba-b120e14eb7ce"
            },
            "resourceId":"/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/microsoft.dbformariadb/servers/<resource name>",
            "resourceGroupName":"<resource group name>",
            "resourceProviderName":"MICROSOFT.DBFORMARIADB",
            "status":"Active",
            "subStatus":"",
            "subscriptionId":"<subscription id>",
            "submissionTimestamp":"2019-07-17T20:36:39.3966926+00:00",
            "resourceType":"MICROSOFT.DBFORMARIADB/SERVERS"
         }
      },
      "properties":{  
 
      }
   }
}
```

在任一架構中，您可以通過查找**事件來源**和`Recommendation`**操作名稱**為`Microsoft.Advisor/recommendations/available/action`來標識顧問建議事件。

您可能想要使用的其他一些重要欄位包括： 

* *警報目標ID（* 在通用架構中）或資源*Id（* 舊架構）
* *建議類型*
* *建議名稱*
* *建議類別*
* *建議影響*
* *建議資源連結*


## <a name="manage-your-alerts"></a>管理警示 

從 Azure 顧問中，可以編輯、刪除或禁用建議警報。 

1. 在**門戶**中，選擇**Azure 顧問**。

    ![Azure 顧問橫幅](./media/advisor-alerts/create1.png)

2. 在左側功能表的 **"監視"** 部分中，選擇 **"警報**"。

    ![Azure 顧問橫幅](./media/advisor-alerts/create2.png)

3. 要編輯警報，請按一下"警報"名稱以打開警報並編輯要編輯的欄位。

4. 要刪除、啟用或禁用警報，請按一下行末尾的橢圓，然後選擇要執行的操作。
 

