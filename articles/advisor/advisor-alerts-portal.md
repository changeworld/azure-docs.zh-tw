---
title: 使用 Azure 入口網站建立新建議的 Azure Advisor 警示
description: 針對新的建議建立 Azure Advisor 警示
ms.topic: article
ms.date: 09/09/2019
ms.openlocfilehash: 837f12a12e532902ee76e345afa430f758f79c10
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973655"
---
# <a name="create-azure-advisor-alerts-on-new-recommendations-using-the-azure-portal"></a>使用 Azure 入口網站建立新建議的 Azure Advisor 警示 

本文說明如何使用 Azure 入口網站為 Azure Advisor 設定新建議的警示。 

每當 Azure Advisor 偵測到您的某個資源有新的建議時，就會在 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中儲存一個事件。 您可以從 Azure Advisor 使用建議特定的警示建立體驗來設定這些事件的警示。 您可以選取訂用帳戶和選擇性的資源群組，以指定您想要收到警示的資源。 

您也可以使用下列屬性來判斷建議的類型：

* 類別
* 影響等級
* 建議類型

您也可以透過下列方式，設定警示觸發時所要進行的動作：  

* 選取現有的動作群組
* 建立新的動作群組

若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

> [!NOTE] 
> Advisor 警示目前僅適用於高可用性建議、效能建議和成本建議。 不支援安全性建議。 

## <a name="create-alert-rule"></a>建立警示規則
1. 在 **入口網站**中，選取 [ **Azure Advisor**]。

    ![入口網站中的 Azure Advisor](./media/advisor-alerts/create1.png)

2. 在左側功能表的 [ **監視** ] 區段中，選取 [ **警示**]。 

    ![Advisor 中的警示](./media/advisor-alerts/create2.png)

3. 選取 [ **新增 Advisor 警示**]。

    ![新增 Advisor 警示](./media/advisor-alerts/create3.png)

4. 在 [ **範圍** ] 區段中，選取訂用帳戶，並選擇性地選取您想要收到警示的資源群組。 

    ![Advisor 警示範圍](./media/advisor-alerts/create4.png)

5. 在 [ **條件** ] 區段中，選取您要用來設定警示的方法。 如果您想要針對特定類別及/或影響等級的所有建議發出警示，請選取 [ **類別和影響等級**]。 如果您想要針對特定類型的所有建議發出警示，請選取 [ **建議類型**]。

    ![Azure Advisor 警示條件](./media/advisor-alerts/create5.png)

6. 視您選取的 [設定依據] 選項而定，您將能夠指定準則。 如果您想要所有建議，請將其餘欄位保留空白。 

    ![Advisor 警示動作群組](./media/advisor-alerts/create6.png)

7. 在 [ **動作群組** ] 區段中，選取 [ **新增現有** 的] 以使用您已建立的動作群組，或選取 [ **建立新** 的] 來設定新的 [動作群組](../azure-monitor/platform/action-groups.md)。 

    ![Advisor 警示加入現有的](./media/advisor-alerts/create7.png)

8. 在 [警示詳細資料] 區段中，為您的警示提供名稱和簡短描述。 如果您想要啟用警示，請將 [ **建立時啟用規則** ] 選項設為 **[是]**。 然後選取要儲存警示的資源群組。 這不會影響建議的目標範圍。 

    :::image type="content" source="./media/advisor-alerts/create8.png" alt-text="[警示詳細資料] 區段的螢幕擷取畫面。":::


## <a name="configure-recommendation-alerts-to-use-a-webhook"></a>設定建議警示以使用 webhook
本節說明如何設定 Azure Advisor 警示，以透過 webhook 將建議資料傳送至您現有的系統。 

您可以設定警示，以便在您的其中一個資源有新的 Advisor 建議時收到通知。 這些警示可以透過電子郵件或文字訊息通知您，但它們也可以用來透過 webhook 與您現有的系統整合。 


### <a name="using-the-advisor-recommendation-alert-payload"></a>使用 Advisor 建議的警示承載
如果您想要使用 webhook 將 Advisor 警示整合到您自己的系統，您必須剖析從通知傳送的 JSON 承載。 

當您設定此警示的動作群組時，您可以選取是否要使用常見的警示架構。 如果您選取一般警示架構，您的承載看起來會像這樣： 

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

如果您未使用一般架構，您的承載看起來如下所示： 

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

在任一架構中，您都可以藉由尋找 **eventSource** 來識別 Advisor 建議事件， `Recommendation` 而 **operationName**  為 `Microsoft.Advisor/recommendations/available/action` 。

您可能會想要使用的一些其他重要欄位包括： 

* 一般架構中的*alertTargetIDs* () 或*resourceId* (舊版架構) 
* *recommendationType*
* *recommendationName*
* *recommendationCategory*
* *recommendationImpact*
* *recommendationResourceLink*


## <a name="manage-your-alerts"></a>管理警示 

您可以從 Azure Advisor 編輯、刪除或停用並啟用建議警示。 

1. 在 **入口網站**中，選取 [ **Azure Advisor**]。

    :::image type="content" source="./media/advisor-alerts/create1.png" alt-text="Azure 入口網站功能表的螢幕擷取畫面，其中顯示已選取 Azure Advisor。":::

2. 在左側功能表的 [ **監視** ] 區段中，選取 [ **警示**]。

    :::image type="content" source="./media/advisor-alerts/create2.png" alt-text="Azure 入口網站功能表的螢幕擷取畫面，其中顯示已選取的警示。":::

3. 若要編輯警示，請按一下警示名稱以開啟警示，並編輯您要編輯的欄位。

4. 若要刪除、啟用或停用警示，請按一下資料列結尾的省略號，然後選取您想要採取的動作。
 

## <a name="next-steps"></a>後續步驟
- 取得[活動記錄警示的概觀](../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。
- 深入了解[動作群組](../azure-monitor/platform/action-groups.md)。
