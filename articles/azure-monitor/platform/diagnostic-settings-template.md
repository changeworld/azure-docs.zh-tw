---
title: 使用 Resource Manager 範本在 Azure 中建立診斷設定
description: 使用資源管理器範本創建診斷設置，將 Azure 平臺日誌轉發到 Azure 監視器日誌、Azure 存儲或 Azure 事件中心。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: a2569ca3f998030680bd7dbd872d71ccd372a25d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672424"
---
# <a name="create-diagnostic-setting-in-azure-using-a-resource-manager-template"></a>使用資源管理器範本在 Azure 中創建診斷設置
Azure 監視器中的[診斷設置](diagnostic-settings.md)指定將 Azure 資源和它們所依賴的 Azure[平臺收集的平臺日誌](platform-logs-overview.md)發送到何處。 本文提供了使用[Azure 資源管理器範本](../../azure-resource-manager/templates/template-syntax.md)創建和配置診斷設置以將平臺日誌收集到不同目標的詳細資訊和示例。

> [!NOTE]
> 由於無法使用 PowerShell 或 CLI 為 Azure 活動日誌[創建診斷設置](diagnostic-settings.md)，例如其他 Azure 資源的診斷設置，因此請使用本文中的資訊為活動日誌創建資源管理器範本，並使用 PowerShell 或 CLI 部署範本。

## <a name="deployment-methods"></a>部署方法
您可以使用任何有效方法（包括 PowerShell 和 CLI）部署資源管理器範本。 活動日誌的診斷設置必須部署到用於`az deployment create`CLI 或`New-AzDeployment`PowerShell 的訂閱。 資源日誌的診斷設置必須部署到用於`az group deployment create`CLI 或`New-AzResourceGroupDeployment`PowerShell 的資源組。

有關詳細資訊，請參閱[使用資源管理器範本和 Azure PowerShell 部署資源](../../azure-resource-manager/templates/deploy-powershell.md)，以及[使用資源管理器範本和 Azure CLI 部署資源](../../azure-resource-manager/templates/deploy-cli.md)。 





## <a name="resource-logs"></a>資源記錄
對於資源日誌，向範本添加類型的`<resource namespace>/providers/diagnosticSettings`資源。 屬性部分遵循[診斷設置 - 創建或更新](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings/createorupdate)中描述的格式。 在`logs`節中`category`為對要收集的資源有效的每個類別提供 一個。 如果`metrics`[資源支援指標](metrics-supported.md)，則添加屬性以將資源指標收集到相同的目標。

下面是一個範本，該範本將特定資源的資源日誌類別收集到日誌分析工作區、存儲帳戶和事件中心。

```json
"resources": [
  {
    "type": "/<resource namespace>/providers/diagnosticSettings",
    "name": "[concat(parameters('resourceName'),'/microsoft.insights/', parameters('settingName'))]",
    "dependsOn": [
      "[<resource Id for which resource logs will be enabled>]"
    ],
    "apiVersion": "2017-05-01-preview",
    "properties": {
      "name": "[parameters('settingName')]",
      "storageAccountId": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))]",
      "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
      "eventHubName": "[parameters('eventHubName')]",
      "workspaceId": "[parameters('workspaceId')]",
      "logs": [
        {
          "category": "<category name>",
          "enabled": true
        }
      ],
      "metrics": [
        {
          "category": "AllMetrics",
          "enabled": true
        }
      ]
    }
  }
]
```



### <a name="example"></a>範例
下面是為自動縮放設置創建診斷設置的示例，該設置允許將資源日誌資料流到事件中心、存儲帳戶和日誌分析工作區。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "autoscaleSettingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the autoscale setting"
            }
        },
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceIDl of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
    {
      "type": "microsoft.insights/autoscalesettings/providers/diagnosticSettings",
      "apiVersion": "2017-05-01-preview",
      "name": "[concat(parameters('autoscaleSettingName'),'/microsoft.insights/', parameters('settingName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/autoscalesettings', parameters('autoscaleSettingName'))]"
      ],
      "properties": {
        "workspaceId": "[parameters('workspaceId')]",
        "storageAccountId": "[parameters('storageAccountId')]",
        "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
        "eventHubName": "[parameters('eventHubName')]",
        "logs": [
          {
            "category": "AutoscaleScaleActions",
            "enabled": true
          },
          {
            "category": "AutoscaleEvaluations",
            "enabled": true
          }
        ]
      }
    }
  ]
}
```

## <a name="activity-log"></a>活動記錄檔
對於 Azure 活動日誌，添加類型 的資源`Microsoft.Insights/diagnosticSettings`。 可用類別列在[活動日誌中的類別中](activity-log-view.md#categories-in-the-activity-log)。 下面是一個範本，該範本將所有活動日誌類別收集到日誌分析工作區、存儲帳戶和事件中心。


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "settingName": {
            "type": "string",
            "metadata": {
                "description": "The name of the diagnostic setting"
            }
        },
        "workspaceId": {
            "type": "string",
            "metadata": {
                "description": "ResourceID of the Log Analytics workspace in which resource logs should be saved."
            }
        },
        "storageAccountId": {
            "type": "string",
            "metadata": {
              "description": "ResourceID of the Storage Account in which resource logs should be saved."
            }
        },
        "eventHubAuthorizationRuleId": {
            "type": "string",
            "metadata": {
              "description": "Resource ID of the event hub authorization rule for the Event Hubs namespace in which the event hub should be created or streamed to."
            }
        },
        "eventHubName": {
            "type": "string",
            "metadata": {
                "description": "Optional. Name of the event hub within the namespace to which logs are streamed. Without this, an event hub is created for each log category."
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Insights/diagnosticSettings",
            "apiVersion": "2017-05-01-preview",
            "name": "[parameters('settingName')]",
            "location": "global",
            "properties": {
                "workspaceId": "[parameters('workspaceId')]",
                "storageAccountId": "[parameters('storageAccountId')]",
                "eventHubAuthorizationRuleId": "[parameters('eventHubAuthorizationRuleId')]",
                "eventHubName": "[parameters('eventHubName')]",
                "logs": [
                    {
                        "category": "Administrative",
                        "enabled": true
                    },
                    {
                        "category": "Security",
                        "enabled": true
                    },
                    {
                        "category": "ServiceHealth",
                        "enabled": true
                    },
                    {
                        "category": "Alert",
                        "enabled": true
                    },
                    {
                        "category": "Recommendation",
                        "enabled": true
                    },
                    {
                        "category": "Policy",
                        "enabled": true
                    },
                    {
                        "category": "Autoscale",
                        "enabled": true
                    },
                    {
                        "category": "ResourceHealth",
                        "enabled": true
                    }
                ]
            }
        }
    ]
}
```


## <a name="next-steps"></a>後續步驟
* 閱讀有關[Azure 中平臺日誌](platform-logs-overview.md)的更多內容。
* 瞭解[診斷設置](diagnostic-settings.md)。
