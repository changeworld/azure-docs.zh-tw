---
title: Azure 活動記錄
description: 查看 Azure 活動記錄檔，並將它傳送至 Azure 監視器記錄、Azure 事件中樞和 Azure 儲存體。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: e6fb2f09200e42f7ad7781716bb83ab418134509
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86516136"
---
# <a name="azure-activity-log"></a>Azure 活動記錄
活動記錄是 Azure 中的[平臺記錄](platform-logs-overview.md)，可讓您深入瞭解訂用帳戶層級事件。 這包括修改資源或啟動虛擬機器時的資訊。 您可以在 Azure 入口網站中檢視活動記錄，或使用 PowerShell 和 CLI 來取出項目。 如需其他功能，您應該建立診斷設定，以將活動記錄傳送至[Azure 監視器記錄](data-platform-logs.md)，以 Azure 事件中樞在 Azure 外部轉送，或 Azure 儲存體進行封存。 本文詳細說明如何查看活動記錄，並將其傳送至不同的目的地。

如需建立診斷設定的詳細資訊，請參閱[建立診斷設定以將平臺記錄和計量傳送至不同的目的地](diagnostic-settings.md)。

> [!NOTE]
> 活動記錄中的專案是系統產生的，無法變更或刪除。

## <a name="view-the-activity-log"></a>檢視活動記錄
您可以從 Azure 入口網站中的大部分功能表存取活動記錄。 從中開啟它的功能表會決定其初始篩選準則。 如果您從 [**監視**] 功能表開啟它，則訂用帳戶上會有唯一的篩選準則。 如果您從資源的功能表中開啟它，篩選準則將會設定為該資源。 您隨時都可以變更篩選，以查看所有其他專案。 按一下 [**新增篩選**]，將其他屬性新增至篩選準則。

![檢視活動記錄](./media/activity-logs-overview/view-activity-log.png)

如需活動記錄類別的說明，請參閱[Azure 活動記錄事件架構](activity-log-schema.md#categories)。

### <a name="view-change-history"></a>檢視變更歷程記錄

針對某些事件，您可以查看變更歷程記錄，其中顯示在該事件期間發生的變更。 從您想要更深入了解的活動記錄中選取事件。 選取 [變更歷程記錄 (預覽)] 索引標籤，以檢視與該事件相關聯的任何變更。

![變更事件的歷程記錄清單](media/activity-logs-overview/change-history-event.png)

如果事件有任何相關聯的變更，您將會看到一份可選取的變更清單。 這可開啟 [變更歷程記錄 (預覽)] 頁面。 在此頁面上，您會看到資源的變更。 在下列範例中，您不只可以看到 VM 的大小變更，而是先前的 VM 大小是在變更之前，以及已變更的內容。 若要深入瞭解變更歷程記錄，請參閱[取得資源變更](../../governance/resource-graph/how-to/get-resource-changes.md)。

![顯示差異的變更歷程記錄頁面](media/activity-logs-overview/change-history-event-details.png)


### <a name="other-methods-to-retrieve-activity-log-events"></a>取得活動記錄事件的其他方法
您也可以使用下列方法來存取活動記錄事件。

- 使用 [Get-AzLog](/powershell/module/az.monitor/get-azlog) Cmdlet，從 PowerShell 擷取活動記錄。 請參閱[Azure 監視器 PowerShell 範例](../samples/powershell-samples.md#retrieve-activity-log)。
- 使用 [az monitor activity-log](/cli/azure/monitor/activity-log) 從 CLI 擷取活動記錄。  請參閱 [Azure 監視器 CLI 範例](../samples/cli-samples.md#view-activity-log)。
- 使用 [Azure 監視器 REST API](/rest/api/monitor/)，從 REST 用戶端擷取活動記錄。 


## <a name="send-to-log-analytics-workspace"></a>傳送至 Log Analytics 工作區
 將活動記錄傳送至 Log Analytics 工作區，以啟用[Azure 監視器記錄](data-platform-logs.md)的功能，包括下列各項：

- 將活動記錄資料與 Azure 監視器所收集的其他監視資料相互關聯。
- 將多個 Azure 訂用帳戶和租使用者中的記錄專案合併成一個位置，以供分析一起進行。
- 使用記錄查詢來執行複雜的分析，並取得活動記錄專案的深入見解。
- 使用記錄警示搭配活動專案，以允許更複雜的警示邏輯。
- 儲存活動記錄專案超過90天。
- 儲存在 Log Analytics 工作區中的活動記錄資料不會有任何資料內嵌或資料保留費用。

[建立診斷設定](diagnostic-settings.md)，以將活動記錄傳送至 log Analytics 工作區。 您可以將任何單一訂用帳戶的活動記錄傳送到最多五個工作區。 跨租使用者收集記錄需要[Azure 燈塔](../../lighthouse/index.yml)。

Log Analytics 工作區中的活動記錄資料會儲存在名為*AzureActivity*的資料表中，您可以使用[log Analytics](../log-query/get-started-portal.md)中的[記錄查詢](../log-query/log-query-overview.md)來加以抓取。 這個資料表的結構會根據[記錄專案的類別](activity-log-schema.md)而有所不同。 如需資料表屬性的描述，請參閱[Azure 監視器資料參考](/azure/azure-monitor/reference/tables/azureactivity)。

例如，若要查看每個類別目錄的活動記錄計數，請使用下列查詢。

```kusto
AzureActivity
| summarize count() by Category
```

若要取出系統管理類別中的所有記錄，請使用下列查詢。

```kusto
AzureActivity
| where Category == "Administrative"
```


## <a name="send-to-azure-event-hubs"></a>傳送至 Azure 事件中樞
將活動記錄檔傳送至 Azure 事件中樞，以在 Azure 外部傳送專案，例如 SIEM 至協力廠商或其他 Log analytics 解決方案。 事件中樞的活動記錄事件會以 JSON 格式取用，其中 `records` 包含每個承載中含有記錄的元素。 架構相依于類別目錄，並在[儲存體帳戶和事件中樞的架構](activity-log-schema.md)中說明。

以下是來自活動記錄事件中樞的範例輸出資料：

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```


## <a name="send-to--azure-storage"></a>傳送至 Azure 儲存體
如果您想要保留記錄資料超過90天的時間，以便進行審核、靜態分析或備份，請將活動記錄傳送至 Azure 儲存體帳戶。 如果您只需要保留您的事件90天（或更少），則不需要設定封存至儲存體帳戶，因為活動記錄事件會在 Azure 平臺中保留90天。

當您將活動記錄傳送至 Azure 時，系統會在事件發生時立即在儲存體帳戶中建立儲存體容器。 容器中的 blob 會使用下列命名慣例：

```
insights-activity-logs/resourceId=/SUBSCRIPTIONS/{subscription ID}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，特定的 blob 可能會有類似以下的名稱：

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/y=2020/m=06/d=08/h=18/m=00/PT1H.json
```

每個 PT1H.json blob 有一個 JSON blob，包含在 blob URL 指定時數內 (例如 h = 12) 發生的事件。 在目前這一小時，事件一發生就會附加到 PT1H.json 檔案。 分鐘值（m = 00）一定是00，因為資源記錄事件會分成每小時的個別 blob。

每個事件都會以下列格式儲存在 PT1H.js的檔案中，其使用通用的最上層架構，但對於[活動記錄架構](activity-log-schema.md)中所述的每個類別而言是唯一的。

``` JSON
{ "time": "2020-06-12T13:07:46.766Z", "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MV-VM-01", "correlationId": "0f0cb6b4-804b-4129-b893-70aeeb63997e", "operationName": "Microsoft.Resourcehealth/healthevent/Updated/action", "level": "Information", "resultType": "Updated", "category": "ResourceHealth", "properties": {"eventCategory":"ResourceHealth","eventProperties":{"title":"This virtual machine is starting as requested by an authorized user or process. It will be online shortly.","details":"VirtualMachineStartInitiatedByControlPlane","currentHealthStatus":"Unknown","previousHealthStatus":"Unknown","type":"Downtime","cause":"UserInitiated"}}}
```


## <a name="legacy-collection-methods"></a>舊版收集方法
本節說明用來收集診斷設定之前所使用之活動記錄檔的舊版方法。 如果您使用這些方法，您應該考慮轉換成診斷設定，以提供更好的功能並與資源記錄檔一致。

### <a name="log-profiles"></a>記錄檔的設定檔
記錄檔設定檔是將活動記錄傳送至 Azure 儲存體或事件中樞的舊版方法。 使用下列程式繼續使用記錄檔設定檔，或在準備遷移至診斷設定時加以停用。

1. 從 Azure 入口網站的 [ **Azure 監視器**] 功能表中，選取 [**活動記錄**]。
3. 按一下 [診斷設定]****。

   ![診斷設定](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. 按一下 [紫色] 橫幅以取得舊版體驗。

    ![舊版體驗](media/diagnostic-settings-subscription/legacy-experience.png)



### <a name="configure-log-profile-using-powershell"></a>使用 PowerShell 設定記錄檔設定檔

如果記錄檔設定檔已經存在，您必須先移除現有的記錄設定檔，然後再建立一個新的。

1. 使用 `Get-AzLogProfile` 來識別記錄設定檔是否存在。  如果記錄檔設定檔已存在，請注意*name*屬性。

1. 使用 `Remove-AzLogProfile` 透過 name** 屬性中的值來移除記錄設定檔。

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. 使用 `Add-AzLogProfile` 來建立新的記錄設定檔：

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | 屬性 | 必要 | 描述 |
    | --- | --- | --- |
    | 名稱 |是 |記錄檔設定檔的名稱。 |
    | StorageAccountId |否 |應儲存活動記錄之儲存體帳戶的資源識別碼。 |
    | serviceBusRuleId |否 |服務匯流排規則識別碼，您想要在其中建立事件中樞的服務匯流排命名空間。 這是格式為的字串： `{service bus resource ID}/authorizationrules/{key name}` 。 |
    | 位置 |是 |以逗號分隔的區域清單，其中列出您要收集的活動記錄檔事件的區域。 |
    | RetentionInDays |是 |在儲存體帳戶中應保留事件的天數，介於1到365之間。 值為 0 會無限期地儲存記錄。 |
    | 類別 |否 |以逗號分隔的類別清單，其中列出應該收集的事件類別。 可能的值為_Write_、 _Delete_和_Action_。 |

### <a name="example-script"></a>範例指令碼
以下是範例 PowerShell 腳本，用來建立記錄檔設定檔，以將活動記錄寫入至儲存體帳戶和事件中樞。

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>使用 Azure CLI 設定記錄檔設定檔

如果記錄設定檔已經存在，您必須先移除現有的記錄設定檔，然後再建立新的記錄設定檔。

1. 使用 `az monitor log-profiles list` 來識別記錄設定檔是否存在。
2. 使用 `az monitor log-profiles delete --name "<log profile name>` 透過 name** 屬性中的值來移除記錄設定檔。
3. 使用 `az monitor log-profiles create` 來建立新的記錄設定檔：

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | 屬性 | 必要 | 描述 |
    | --- | --- | --- |
    | NAME |是 |記錄檔設定檔的名稱。 |
    | storage-account-id |是 |資源識別碼，活動記錄應該要儲存至此儲存體帳戶。 |
    | 位置 |是 |以空格分隔的區域清單，其中列出您要收集的活動記錄事件的區域。 您可以使用 `az account list-locations --query [].name` 來檢視您訂用帳戶的所有區域清單。 |
    | days |是 |應保留事件的天數，介於1到365之間。 值為 0 會無限期地 (永遠) 儲存記錄。  如果為零，則已啟用的參數應該設定為 false。 |
    |已啟用 | 是 |是非題。  用來啟用或停用保留原則。  如果為 True，則 days 參數必須是大於 0 的值。
    | categories |是 |以空格分隔的類別清單，其中列出應收集的事件類別。 可能的值有 Write、Delete、Action。 |


### <a name="log-analytics-workspace"></a>Log Analytics 工作區
將活動記錄傳送到 Log Analytics 工作區的舊版方法，是連接工作區設定中的記錄。 

1. 從 [Azure 入口網站中的 [ **Log Analytics 工作區**] 功能表，選取要收集活動記錄的工作區。
1. 在工作區功能表的 [**工作區資料來源**] 區段中，選取 [ **Azure 活動記錄**]。
1. 按一下您要連接的訂用帳戶。

    ![工作區](media/activity-log-collect/workspaces.png)

1. 按一下 [連線 **]** ，將訂用帳戶中的活動記錄連接到選取的工作區。 如果訂用帳戶已連接到另一個工作區，請按一下 [先**中斷**連線] 將其中斷連接。

    ![連接工作區](media/activity-log-collect/connect-workspace.png)


若要停用此設定，請執行相同的程式，然後按一下 **[中斷連線]** ，從工作區移除訂用帳戶。

### <a name="data-structure-changes"></a>資料結構變更
診斷設定會傳送與用來傳送活動記錄檔的舊版方法相同的資料，其中包含*AzureActivity*資料表結構的某些變更。

下表中的資料行已在更新的架構中被取代。 它們仍然存在於*AzureActivity*中，但不會有任何資料。 這些資料行的取代並不是新的，但它們包含與已被取代資料行相同的資料。 它們的格式不同，因此您可能需要修改使用它們的記錄查詢。 

| 已取代的資料行 | 取代資料行 |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> 在某些情況下，這些資料行中的值可能全部大寫。 如果您有包含這些資料行的查詢，您應該使用[= ~ 運算子](/azure/kusto/query/datatypes-string-operators)來執行不區分大小寫的比較。

下列資料行已新增至已更新之架構中的*AzureActivity* ：

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-analytics-monitoring-solution"></a>活動記錄分析監視解決方案
Azure Log Analytics 監視解決方案即將淘汰，並由使用 Log Analytics 工作區中更新架構的活頁簿所取代。 如果您已經啟用解決方案，您仍然可以使用它，但只有在使用舊版設定收集活動記錄檔時，才可以使用它。 



### <a name="use-the-solution"></a>使用解決方案
監視解決方案可從 Azure 入口網站中的 [**監視**] 功能表存取。 在 [**深入**解析] 區段中選取 [**更多**]，以開啟具有方案磚的 [**總覽**] 頁面。 [ **Azure 活動記錄**] 圖格會顯示工作區中**AzureActivity**記錄的數目。

![Azure 活動記錄圖格](media/collect-activity-logs/azure-activity-logs-tile.png)


按一下 [ **Azure 活動記錄**] 圖格，以開啟 [ **azure 活動記錄**] 視圖。 此視圖包含下表中的視覺效果元件。 每個部分會列出最多10個專案，符合所指定時間範圍內的部分準則。 您可以按一下元件底部的 [**查看全部**]，來執行記錄查詢，以傳回所有相符的記錄。

![Azure 活動記錄儀表板](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>為新訂閱啟用解決方案
您很快就無法再使用 Azure 入口網站將活動記錄分析解決方案新增至您的訂用帳戶。 您可以使用下列程式搭配 Resource Manager 範本來新增它。 

1. 將下列 json 複製到名為*ActivityLogTemplate*的檔案中。

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. 使用下列 PowerShell 命令來部署範本：

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```



## <a name="next-steps"></a>後續步驟

* [閱讀平台記錄的概觀](platform-logs-overview.md)
* [建立診斷設定以將活動記錄傳送至其他目的地](diagnostic-settings.md)
