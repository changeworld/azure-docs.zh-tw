---
title: 在 Azure 監視器中創建、查看和管理活動日誌警報
description: 使用 Azure 門戶、Azure 資源管理器範本和 Azure PowerShell 創建活動日誌警報。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: bfbe2bc3ae3edf9285d3ec006ab0451f070cabd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132397"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>使用 Azure 監視器創建、查看和管理活動日誌警報  

## <a name="overview"></a>總覽

活動記錄警示是發生符合警示中指定條件的新活動記錄事件時所啟動的警示。

這些警報適用于 Azure 資源，可以使用 Azure 資源管理器範本創建。 也可以在 Azure 入口網站中將它們建立、更新或刪除。 通常，創建活動日誌警報以在 Azure 訂閱中資源發生特定更改時接收通知。 警報通常範圍為特定資源組或資源。 例如，當示例資源組 **"我的生產資源組**"中的任何虛擬機器被刪除時，您可能希望收到通知。 或者，如果向訂閱中的使用者分配了任何新角色，則可能需要收到通知。

> [!IMPORTANT]
> 無法通過創建活動日誌警報的介面創建服務運行狀況通知警報。 要瞭解有關如何創建和使用服務運行狀況通知的更多內容，請參閱[在服務運行狀況通知上接收活動日誌警報](alerts-activity-log-service-notifications.md)。

創建警報規則時，請確保：

- 作用域中的訂閱與創建警報的訂閱沒有區別。
- 條件必須是配置警報的級別、狀態、調用方、資源組、資源識別碼 或資源類型事件類別。
- 警報配置 JSON 中沒有"任何"條件或嵌套條件。 基本上，只允許一個"所有"條件，沒有進一步的"所有"或"任何"條件。
- 當類別為"管理"時，必須在警報中至少指定上述條件之一。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。

## <a name="azure-portal"></a>Azure 入口網站

可以使用 Azure 門戶創建和修改活動日誌警報規則。 體驗與 Azure 活動日誌集成，以確保針對感興趣的特定事件無縫創建警報。

### <a name="create-with-the-azure-portal"></a>使用 Azure 門戶創建

請使用下列程序。

1. 在 Azure 門戶中，選擇 **"監視** > **警報**"。
2. 在 **"警報"** 視窗的左上角選擇 **"新建警報規則**"。

     ![新增警示規則](media/alerts-activity-log/AlertsPreviewOption.png)

     [建立規則]**** 視窗隨即出現。

      ![新的警報規則選項](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 在**定義警示準則**下，提供以下資訊，然後選擇 **"完成**" ：

   - **警報目標：** 要查看和選擇新警報的目標，請使用 / **按訂閱篩選器（按資源類型**）**篩選**。 從顯示的清單中選擇資源或資源組。

     > [!NOTE]
     > 
     > 只能選擇[Azure 資源管理器](../../azure-resource-manager/management/overview.md)跟蹤的資源、資源組或活動日誌信號的整個訂閱。 

     **警示目標範例檢視**

     ![選取目標](media/alerts-activity-log/select-target.png)

   - 在 **"目標"條件**下，選擇 **"添加條件**"。 將顯示目標的所有可用信號，其中包括來自各種類別**的活動日誌**的信號。 類別名稱將追加到**監視器服務**名稱中。

   - 從類型 [活動記錄]**** 的各種可能作業顯示的清單中選取訊號。

     您可以選取此目標訊號的記錄歷程記錄時間軸和對應的警示邏輯：

     **新增準則畫面**

     ![新增準則](media/alerts-activity-log/add-criteria.png)

     - **歷史記錄時間**：可用於所選操作的事件可以繪製在過去 6、12 或 24 小時或過去一周。

     - **警報邏輯**：

       - **事件級別**：事件的嚴重性級別：_詳細_、_資訊_、_警告_、_錯誤_或_嚴重_。
       - **狀態**：事件的狀態：_已啟動_、_失敗_或_成功_。
       - **由 啟動的事件**： 也稱為調用方。 執行作業之使用者的電子郵件地址或 Azure Active Directory 識別碼。

       此示例信號圖應用了警報邏輯：

       ![所選條件](media/alerts-activity-log/criteria-selected.png)

4. 在**定義警報詳細資訊**下，提供以下詳細資訊：

    - **警報規則名稱**：新警報規則的名稱。
    - **說明**：新警報規則的說明。
    - **將警報保存到資源組**：選擇要保存此新規則的資源組。

5. 在 [動作群組]**** 下，從下拉式功能表中指定您想要指派給此新警示規則的動作群組。 或者，[創建新的操作組](../../azure-monitor/platform/action-groups.md)並將其分配給新規則。 要創建新組，請選擇 **"新群組**"。

6. 要在創建規則後啟用規則，請在**創建時為"啟用規則"** 選項選擇 **"是**"。
7. 選取 [建立警示規則]****。

    將創建活動日誌的新警報規則，並在視窗的右上角顯示一條確認訊息。

    您可以啟用、停用、編輯或刪除規則。 詳細瞭解如何管理活動日誌規則。


瞭解可在活動日誌中創建警報規則的條件的一個簡單類比是通過[Azure 門戶中的活動日誌](activity-log-view.md#azure-portal)來流覽或篩選事件。 在**Azure 監視器 - 活動日誌**螢幕中，可以篩選或查找必要的事件，然後使用 **"添加活動日誌警報**"按鈕創建警報。 然後按照前面所示的步驟 4 到 7 操作。
    
 ![從活動日誌添加警報](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>在 Azure 門戶中查看和管理

1. 在 Azure 門戶中，選擇 **"監視** > **警報**"。 選擇"在視窗左上角**管理警報規則**"。

    ![管理警示規則](media/alerts-activity-log/manage-alert-rules.png)

    可用規則清單隨即出現。

2. 搜索要修改的活動日誌規則。

    ![搜索活動日誌警報規則](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    您可以使用可用的篩選器、_訂閱_、_資源組_、_資源_、_信號類型_或_狀態_來查找要編輯的活動規則。

   > [!NOTE]
   > 
   > 只能編輯 **"描述**"、"**目標條件****"和"操作組**"。

3. 選擇規則，然後按兩下以編輯規則選項。 進行所需的更改，然後選擇 **"保存**"。

   ![管理警示規則](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 您可以啟用、禁用或刪除規則。 選擇步驟 2 中所述的規則後，在視窗頂部選擇適當的選項。


## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
要使用 Azure 資源管理器範本創建活動日誌警報規則，請創建類型 的資源`microsoft.insights/activityLogAlerts`。 然後要填入所有相關的屬性。 下面是創建活動日誌警報規則的範本：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
為了演練的目的，可以將前面的示例 JSON 另存為示例 ActivityLogAlert.json，並且可以通過[在 Azure 門戶中使用 Azure 資源管理器](../../azure-resource-manager/templates/deploy-portal.md)進行部署。

以下欄位是可用於條件欄位的 Azure 資源管理器範本中的選項：請注意，"資源運行狀況"、"顧問"和"服務運行狀況"具有其特殊欄位的額外屬性欄位。 
1. 資源 Id：應在生成警報的活動日誌事件中受影響的資源的資源識別碼。
2. 類別：活動日誌事件中 的類別。 例如：管理、服務運行狀況、資源運行狀況、自動縮放、安全性、建議、策略。
3. 調用方：執行活動日誌事件操作的使用者的電子郵件地址或 Azure 活動目錄識別碼。
4. 級別：應在生成警報的活動日誌事件中的活動級別。 例如：嚴重、錯誤、警告、資訊、詳細。
5. 操作名稱：活動日誌事件中的操作的名稱。 例如：微軟.資源/部署/寫入
6. 資源組：活動日誌事件中受影響資源的資源組的名稱。
7. 資來源提供者[：Azure 資來源提供者和類型說明](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)。 有關將資來源提供者映射到 Azure 服務的清單，請參閱[Azure 服務的資來源提供者](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)。
8. 狀態：描述活動事件中操作狀態的字串。 例如：已啟動、正在進行、成功、失敗、活動、已解決
9. 子狀態：通常對應 REST 調用的 HTTP 狀態碼，但也可以包括描述子狀態的其他字串。   例如：確定（HTTP 狀態碼： 200）、已創建 （HTTP 狀態碼： 201）、已接受（HTTP 狀態碼：202）、無內容（HTTP 狀態碼：204）、錯誤請求（HTTP 狀態碼：400）、未找到（HTTP 狀態碼：404）、衝突（HTTP 狀態碼：409）、內部伺服器錯誤（HTTP 狀態碼： 500），服務不可用（HTTP 狀態碼：503），閘道超時（HTTP 狀態碼：504）。
10. 資源類型：受事件影響的資源的類型。 例如：微軟.資源/部署

例如：

```json
"condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        }

```
有關活動日誌欄位的更多詳細資訊，請[在此處](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)找到 。



> [!NOTE]
> 新的活動日誌警報規則最多可能需要 5 分鐘才能變為活動狀態。

## <a name="rest-api"></a>REST API 
[Azure 監視器活動日誌警報 API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts)是 REST API。 它與 Azure 資源管理器 REST API 完全相容。 它可以通過 PowerShell 使用資源管理器 Cmdlet 或 Azure CLI 使用。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>使用 PowerShell 部署資源管理器範本
要使用 PowerShell 部署上一個[Azure 資源管理器範本](#azure-resource-manager-template)部分中顯示的示例資源管理器範本，請使用以下命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中示例 ActivityLogAlert.parameter.json 包含為創建警報規則所需的參數提供的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活動日誌 PowerShell Cmdlet

活動記錄警示有專用的 PowerShell Cmdlet 可供使用：

- [設置-AzActivityLogAlert：](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert)創建新的活動日誌警報或更新現有活動日誌警報。
- [獲取"AzActivityLogAlert"：](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert)獲取一個或多個活動日誌警報資源。
- [啟用-AzActivityLogAlert：](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert)啟用現有活動日誌警報並設置其標記。
- [禁用 AzActivityLogAlert：](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert)禁用現有活動日誌警報並設置其標記。
- [刪除"刪除活動日誌警報](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert)"：刪除活動日誌警報。

## <a name="azure-cli"></a>Azure CLI

[az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) 集合底下的專用 Azure CLI 命令可用來管理活動記錄警示規則。

要創建新的活動日誌警報規則，請按以下順序使用以下命令：

1. [az 監視器活動日誌警報創建](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：創建新的活動日誌警報規則資源。
1. [az 監視器活動日誌警報範圍](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope)：為創建的活動日誌警報規則添加作用域。
1. [az 監視器活動日誌警報操作組](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group)：向活動日誌警報規則添加操作組。

若要檢索一個活動日誌警報規則資源，請使用 Azure CLI 命令[az 監視器活動日誌警報顯示](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 要查看資源組中的所有活動日誌警報規則資源，請使用 az[監視器活動日誌警報清單](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
可以使用 Azure CLI 命令[az 監視器活動日誌警報刪除](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete)刪除活動日誌警報規則資源。

## <a name="next-steps"></a>後續步驟

- 瞭解[活動日誌的 Webhook 架構](../../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 閱讀[活動日誌的概述](../../azure-monitor/platform/activity-log-alerts.md)。
- 瞭解有關[操作組](../../azure-monitor/platform/action-groups.md)的更多。  
- 瞭解[服務運行狀況通知](../../azure-monitor/platform/service-notifications.md)。
