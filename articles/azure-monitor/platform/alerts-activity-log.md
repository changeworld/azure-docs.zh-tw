---
title: 在 Azure 監視器中建立、檢視及管理活動記錄警示
description: 使用 Azure 入口網站、Azure Resource Manager 範本和 Azure PowerShell 來建立活動記錄警示。
ms.topic: conceptual
ms.subservice: alerts
ms.date: 06/25/2019
ms.openlocfilehash: 7914f3ee57d014de2f34fca301b587c6297bebe3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983093"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>使用 Azure 監視器中建立、檢視及管理活動記錄警示  

## <a name="overview"></a>概觀

活動記錄警示是發生符合警示中指定條件的新活動記錄事件時所啟動的警示。

這些警示都適用於 Azure 資源，可以使用 Azure Resource Manager 範本來建立。 也可以在 Azure 入口網站中將它們建立、更新或刪除。 一般而言，您可以建立活動記錄警示，以在 Azure 訂閱中的資源發生特定變更時接收通知。 警示的範圍通常是特定資源群組或資源。 例如，您想要在同一個資源群組 **myProductionResourceGroup** 中的任何虛擬機器遭刪除時收到通知。 或者，您想要在有任何新角色指派給訂閱中的使用者時收到通知。

> [!IMPORTANT]
> 服務健康情況通知的警示是無法透過活動記錄警示建立的介面來建立的。 若要深入了解如何建立及使用服務健康情況通知，請參閱[在服務健康情況通知上接收活動記錄警示](../../service-health/alerts-activity-log-service-notifications-portal.md)。

在建立警示規則時，請確定下列各項：

- 範圍中的訂閱和警示建立所在訂閱並無不同。
- 設定警示必須以層級、狀態、呼叫端、資源群組、資源識別碼，或資源類型事件類別目錄為準則。
- 在警示設定 JSON 中，沒有「anyOf」條件或巢狀條件。 基本上，只允許一個「allOf」條件，不允許有進一步的「allOf」或「anyOf」條件。
- 當類別為「系統管理」時，您必須在警示中指定至少一個上述準則。 您不能建立會在每次活動記錄中建立事件時即啟動的警示。
- 無法為活動記錄警示類別中的事件建立警示。

## <a name="azure-portal"></a>Azure 入口網站

您可以使用 Azure 入口網站來建立和修改活動記錄警示規則。 此體驗與 Azure 活動記錄整合，以確保能針對感興趣的特定事件順暢地建立警示。

### <a name="create-with-the-azure-portal"></a>使用 Azure 入口網站建立

請使用下列程序。

1. 在 Azure 入口網站中，選取 [監視] > [警示]。
2. 在 [警示] 視窗的左上角，選取 [新增警示規則]。

     ![新增警示規則](media/alerts-activity-log/AlertsPreviewOption.png)

     [建立規則] 視窗隨即出現。

      ![新增警示規則選項](media/alerts-activity-log/create-new-alert-rule-options.png)

3. 在 [定義警示條件] 下提供下列資訊，然後選取 [完成]：

   - **警示目標：** 若要檢視並選取新警示的目標，請使用 [依訂閱篩選] / [依資源類型篩選]。 從顯示的清單中選取資源或資源群組。

     > [!NOTE]
     > 
     > 您可以為活動記錄訊號僅選取 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 追蹤的資源、資源群組或整個訂閱。 

     **警示目標範例檢視**

     ![選取目標](media/alerts-activity-log/select-target.png)

   - 在 [目標準則] 下方選取 [新增準則]。 系統會顯示目標的所有可用訊號，其中包括來自各種**活動記錄**類別的可用訊號。 類別名稱會附加至**監視器服務**名稱。

   - 從類型 [活動記錄] 的各種可能作業顯示的清單中選取訊號。

     您可以選取此目標訊號的記錄歷程記錄時間軸和對應的警示邏輯：

     **新增準則畫面**

     ![新增準則](media/alerts-activity-log/add-criteria.png)
     
     > [!NOTE]
     > 
     >  為了擁有高品質且有效的規則，我們要求至少再將一個條件新增至具有「全部系統管理」訊號的規則。 
     > 在警示定義的過程中，您必須填入其中一個下拉式清單：「事件層級」、「狀態」或「起始者」，如此一來規則也會更加明確。

     - **歷程記錄時間**：所選作業的可用事件可以是在過去 6、12 或 24 小時或過去一週繪製的。

     - **警示邏輯**：

       - **事件層級**：事件的嚴重性層級：_詳細資訊_ _資訊_ _警告_ _錯誤_或_嚴重_。
       - **狀態**：事件的狀態：_已啟動_ _失敗_或_成功_。
       - **事件起始者**：也稱為呼叫端。 執行作業之使用者的電子郵件地址或 Azure Active Directory 識別碼。

       此範例訊號圖表已套用警示邏輯：

       ![選取的準則](media/alerts-activity-log/criteria-selected.png)

4. 在 [定義警示詳細資料] 下，提供下列詳細資料：

    - **警示規則名稱**：新警示規則的名稱。
    - **描述**：新警示規則的描述。
    - **將警示儲存到資源群組**：選取您儲存此新規則的資源群組。

5. 在 [動作群組] 下，從下拉式功能表中指定您想要指派給此新警示規則的動作群組。 或者，[建立新的動作群組](./action-groups.md)並指派給新規則。 若要建立新群組，請選取 [+ 新增群組]。

6. 若要在建立群組之後啟用規則，請選取 [在建立時啟用規則] 選項的 [是] 。
7. 選取 [建立警示規則]。

    活動記錄的新警示規則隨即建立，並會在視窗右上角顯示確認訊息。

    您可以啟用、停用、編輯或刪除規則。 深入了解如何管理活動記錄規則。


有一個簡單類比可用來了解哪些警示規則可在活動記錄上建立，就是透過 [Azure 入口網站中的活動記錄](./activity-log.md#view-the-activity-log)探索或篩選事件。 在 [Azure 監視器 - 活動記錄] 中，您可以篩選或尋找必要事件，然後透過使用 [新增活動記錄警示] 按鈕來建立警示。 然後依照前面所示的步驟 4 到 7 進行。
    
 ![從活動記錄新增警示](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>在 Azure 入口網站中檢視及管理

1. 在 Azure 入口網站中，選取 [監視] > [警示]。 在視窗的左上角，選取 [管理警示規則]。

    ![螢幕擷取畫面顯示已反白顯示搜尋方塊的活動記錄。](media/alerts-activity-log/manage-alert-rules.png)

    可用規則清單隨即出現。

2. 搜尋要修改的活動記錄規則。

    ![搜尋活動記錄警示規則](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    您可以使用可用的篩選條件 ([訂閱]、[資源群組]、[資源]、[訊號類型] 或 [狀態]) 來尋找您想要編輯的活動規則。

   > [!NOTE]
   > 
   > 您只能編輯 [描述]、[目標準則] 與 [動作群組]。

3. 選取規則，然後按兩下以編輯規則選項。 進行必要的變更，然後選取 [儲存]。

   ![管理警示規則](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. 您可以啟用、停用或刪除規則。 選取規則之後，在視窗頂端選取適當選項，如步驟 2 所示。


## <a name="azure-resource-manager-template"></a>Azure Resource Manager 範本
若要使用 Azure Resource Manager 範本建立活動記錄警示規則，您要建立 `microsoft.insights/activityLogAlerts` 類型的資源。 然後要填入所有相關的屬性。 以下是建立活動記錄警示規則的範本：

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
舉例來說，上述 JSON 範例可儲存為 sampleActivityLogAlert.json，以供本逐步解說之用，並可透過使用 [Azure 入口網站中的 Azure Resource Manager](../../azure-resource-manager/templates/deploy-portal.md) 進行部署。

  > [!NOTE]
  > 
  > 請注意，最高層級活動記錄警示可以定義為訂用帳戶。
  > 這表示沒有任何選項可定義幾個訂用帳戶的警示，因此定義應為每個訂用帳戶發出警示。

以下欄位是您可在條件欄位的 Azure Resource Manager 範本中使用的選項：請注意，「資源健康狀態」、「Advisor」和「服務健康狀態」具有其特殊欄位適用的額外屬性欄位。 
1. resourceId：在應產生警示的活動記錄事件中受影響資源的資源識別碼。
2. category：活動記錄事件中的類別。 例如：Administrative、ServiceHealth、ResourceHealth、Autoscale、Security、Recommendation、Policy。
3. caller：執行活動記錄事件作業的使用者所屬的電子郵件地址或 Azure Active Directory 識別碼。
4. level：活動記錄檔事件中應該產生警示的活動層級。 例如：重大、錯誤、警告、告知性、詳細資訊。
5. operationName：活動記錄事件中的作業名稱。 例如：Microsoft.Resources/deployments/write
6. resourceGroup：活動記錄事件中受影響資源的資源群組名稱。
7. resourceProvider：[Azure 資源提供者與類型說明](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fresource-providers-and-types&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373543634&sdata=4RjpTkO5jsdOgPdt%2F%2FDOlYjIFE2%2B%2BuoHq5%2F7lHpCwQw%3D&reserved=0)。 如需將資源提供者對應至 Azure 服務的清單，請參閱 [Azure 服務的資源提供者](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-resource-manager%2Fmanagement%2Fazure-services-resource-providers&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373553639&sdata=0ZgJPK7BYuJsRifBKFytqphMOxMrkfkEwDqgVH1g8lw%3D&reserved=0)。
8. status：字串，描述活動事件中作業的狀態。 例如：已啟動、進行中、成功、失敗、使用中、已解決
9. subStatus：通常是對應 REST 呼叫的 HTTP 狀態碼，但也可以包含其他描述子狀態的字串。   例如：正常 (HTTP 狀態碼:200)、已建立 (HTTP 狀態碼:201)、已接受 (HTTP 狀態碼:202)、無內容 (HTTP 狀態碼:204)、錯誤的要求 (HTTP 狀態碼:400)、找不到 (HTTP 狀態碼:404)、衝突 (HTTP 狀態碼:409)、內部伺服器錯誤 (HTTP 狀態碼:500)、無法使用服務 (HTTP 狀態碼:503)、閘道逾時 (HTTP 狀態碼:504)。
10. resourceType：受事件影響的資源類型。 例如：Microsoft.Resources/deployments

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
有關活動記錄欄位的詳細資訊，請參閱[這裡](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fazure-monitor%2Fplatform%2Factivity-log-schema&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C90b7c2308c0647c0347908d7c9a2918d%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637199572373563632&sdata=6QXLswwZgUHFXCuF%2FgOSowLzA8iOALVgvL3GMVhkYJY%3D&reserved=0)。



> [!NOTE]
> 新活動記錄警示規則最多可能需要 5 分鐘的時間才會運作。

## <a name="rest-api"></a>REST API 
[Azure 監視器活動記錄警示 API](/rest/api/monitor/activitylogalerts) 為 REST API。 它與 Azure Resource Manager REST API 完全相容。 可以藉由使用 Resource Manager Cmdlet 或 Azure CLI，來透過 PowerShell 使用它。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>使用 Azure PowerShell 部署 Resource Manager 範本
若要使用 PowerShell 來部署上一節 [Azure Resource Manager 範本](#azure-resource-manager-template)中所示的範例 Resource Manager 範本，請使用下列命令：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

其中 sampleActivityLogAlert.parameters.json 包含提供給警示規則建立所需參數的值。

### <a name="use-activity-log-powershell-cmdlets"></a>使用活動記錄 PowerShell Cmdlet

活動記錄警示有專用的 PowerShell Cmdlet 可供使用：

- [Set-AzActivityLogAlert](/powershell/module/az.monitor/set-azactivitylogalert)：建立新的活動記錄警示，或更新現有的活動記錄警示。
- [Get-AzActivityLogAlert](/powershell/module/az.monitor/get-azactivitylogalert)：取得一或多個活動記錄警示資源。
- [Enable-AzActivityLogAlert](/powershell/module/az.monitor/enable-azactivitylogalert)：啟用現有的活動記錄警示，並設定其標記。
- [Disable-AzActivityLogAlert](/powershell/module/az.monitor/disable-azactivitylogalert)：停用現有的活動記錄警示，並設定其標記。
- [Remove-AzActivityLogAlert](/powershell/module/az.monitor/remove-azactivitylogalert)：移除活動記錄警示。

## <a name="azure-cli"></a>Azure CLI

[az monitor activity-log alert](/cli/azure/monitor/activity-log/alert) 集合底下的專用 Azure CLI 命令可用來管理活動記錄警示規則。

若要建立新的活動記錄警示規則，請依照此順序使用下列命令：

1. [az monitor activity-log alert create](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create)：建立新的活動記錄警示規則資源。
1. [az monitor activity-log alert scope](/cli/azure/monitor/activity-log/alert/scope)：為所建立的活動記錄警示規則新增範圍。
1. [az monitor activity-log alert action-group](/cli/azure/monitor/activity-log/alert/action-group)：將動作群組新增至活動記錄警示規則。

若要擷取一個活動記錄警示規則資源，請使用 Azure CLI 命令 [az monitor activity-log alert show](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
)。 若要檢視某個資源群組中的所有活動記錄警示規則資源，請使用 [az monitor activity-log alert list](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)。
Azure CLI 命令 [az monitor activity-log alert delete](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) 則可用來移除活動記錄警示規則資源。

## <a name="next-steps"></a>後續步驟

- 了解[活動記錄的 Webhook 結構描述](./activity-log-alerts-webhook.md)。
- 參閱[活動記錄概觀](./activity-log-alerts.md)。
- 深入了解[動作群組](./action-groups.md)。  
- 深入了解[服務健康狀態通知](../../service-health/service-notifications.md)。

