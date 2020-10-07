---
title: 使用 Resource Manager 範本針對新的建議建立 Azure Advisor 警示
description: 了解如何使用 Azure Resource Manager 範本 (ARM 範本) 從 Azure Advisor 設定新建議的警示。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: f03bf6eaf4f3045e00fc67efe6faa9f53d962089
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91629891"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>快速入門：使用 ARM 範本針對新的建議建立 Azure Advisor 警示

本文說明如何使用 Azure Resource Manager 範本 (ARM 範本) 從 Azure Advisor 設定新建議的警示。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

每當 Azure Advisor 偵測到您的某個資源有新的建議時，就會在 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中儲存一個事件。 您可以從 Azure Advisor 使用建議特定的警示建立體驗來設定這些事件的警示。 您可以選取訂用帳戶和選擇性的資源群組，以指定您想要收到警示的資源。

您也可以使用下列屬性來判斷建議的類型：

- 類別
- 影響等級
- 建議類型

您也可以透過下列方式，設定警示觸發時所要進行的動作：

- 選取現有的動作群組
- 建立新的動作群組

若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

> [!NOTE]
> Advisor 警示目前僅適用於高可用性建議、效能建議和成本建議。 不支援安全性建議。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 若要從您的本機電腦執行命令，請安裝 Azure CLI 或 Azure PowerShell 模組。 如需詳細資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)和[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="review-the-template"></a>檢閱範本

以下範本會建立具有電子郵件目標的動作群組，並啟用目標訂用帳戶的所有服務健康情況通知。 將此範本儲存為 *CreateAdvisorAlert.json*。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
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
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [],
      "tags": {},
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
      }
    },
    {
      "comments": "Azure Advisor Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "tags": {},
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

此範本會定義兩個資源：

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>部署範本

使用[部署 ARM 範本](../azure-resource-manager/templates/deploy-portal.md)來，例如使用 CLI 和 PowerShell 的下列範例。 以適用於您環境的適當值取代 **Resource Group** 和 **emailAddress** 的範例值。 工作區名稱在您的 Azure 訂用帳戶中必須是唯一的。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>驗證部署

使用下列其中一個命令驗證已建立的工作區。 以您在上面使用的值取代 **Resource Group** 的範例值。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
```

---

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續進行後續的快速入門和教學課程，您可以讓這些資源留在原處。 如果不再需要，請刪除資源群組，這會刪除警示規則和相關資源。 若要使用 Azure CLI 或 Azure PowerShell 刪除資源群組

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>後續步驟

- 取得[活動記錄警示的概觀](../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。
- 深入了解[動作群組](../azure-monitor/platform/action-groups.md)。
