---
title: 使用 Resource Manager 範本接收 Azure 服務通知的活動記錄警示
description: 在 Azure 服務發生時透過 SMS、電子郵件或 Webhook 獲得通知。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 688314a2057964c66baeacbbc49736ea436f5ec5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91630214"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>快速入門：使用 ARM 範本，建立服務通知的活動記錄警示

本文說明如何使用 Azure Resource Manager 範本 (ARM 範本) 為服務健康情況通知設定活動記錄警示。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

服務健康情況通知儲存在 [ Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中。 由於活動記錄中可能儲存大量的資訊，因此有個別的使用者介面，可讓您更輕鬆地檢視及設定服務健康情況通知的警示。

您可以在 Azure 傳送服務健康狀態通知到您的 Azure 訂用帳戶時接收警示。 您可以針對下列設定警示：

- 服務健康情況通知的類別 (服務問題、計劃性維護或健康情況摘要報告)。
- 受影響的訂閱。
- 受影響的服務。
- 受影響的區域。

> [!NOTE]
> 服務健康情況通知不會傳送關於資源健康情況事件的警示。

您也可以設定應傳送警示的對象：

- 選取現有的動作群組。
- 建立新動作群組 (可用於未來的警示)。

若要深入了解動作群組，請參閱[建立及管理動作群組](../azure-monitor/platform/action-groups.md)。

## <a name="prerequisites"></a>必要條件

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 若要從您的本機電腦執行命令，請安裝 Azure CLI 或 Azure PowerShell 模組。 如需詳細資訊，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)和[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="review-the-template"></a>檢閱範本

以下範本會建立具有電子郵件目標的動作群組，並啟用目標訂用帳戶的所有服務健康情況通知。 將此範本儲存為 *CreateServiceHealthAlert.json*。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "type": "String",
      "defaultValue": "SubHealth"
    },
    "activityLogAlerts_name": {
      "type": "String",
      "defaultValue": "ServiceHealthActivityLogAlert"
    },
    "emailAddress": {
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
      "comments": "Service Health Activity Log Alert",
      "type": "microsoft.insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
      "location": "Global",
      "scale": null,
      "dependsOn": [
        "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
      ],
      "tags": {},
      "properties": {
        "scopes": [
          "[variables('alertScope')]"
        ],
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ServiceHealth"
            },
            {
              "field": "properties.incidentType",
              "equals": "Incident"
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
      }
    }
  ]
}
```

此範本會定義兩個資源：

- [Microsoft.Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Microsoft.Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>部署範本

使用[部署 ARM 範本](../azure-resource-manager/templates/deploy-portal.md)來，例如使用 CLI 和 PowerShell 的下列範例。 以適用於您環境的適當值取代 **Resource Group** 和 **emailAddress** 的範例值。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>驗證部署

使用下列其中一個命令驗證已建立的工作區。 以您在上面使用的值取代 **Resource Group** 的範例值。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
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

- 深入了解[設定 Azure 服務健康狀態警示的最佳做法](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)。
- 了解如何[設定 Azure 服務健康狀態的行動推播通知](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)。
- 了解如何[設定現有問題管理系統的 Webhook 通知](service-health-alert-webhook-guide.md)。
- 深入了解[服務健康狀態通知](service-notifications.md)。
- 深入了解[通知速率限制](../azure-monitor/platform/alerts-rate-limiting.md)。
- 檢閱[活動記錄警示 Webhook 結構描述](../azure-monitor/platform/activity-log-alerts-webhook.md)。
- 取得[活動記錄警示的概觀](../azure-monitor/platform/alerts-overview.md)，並了解如何收到警示。
- 深入了解[動作群組](../azure-monitor/platform/action-groups.md)。
