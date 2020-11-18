---
title: '啟用適用於 VM 的 Azure 監視器來賓健全狀況 (preview) '
description: 說明如何在您的訂用帳戶中啟用適用於 VM 的 Azure 監視器來賓健康狀態，以及如何將 Vm 上架。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.openlocfilehash: 6033ca5b0eaf6845d14407832c776dd8e006226b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686689"
---
# <a name="enable-azure-monitor-for-vms-guest-health-preview"></a>啟用適用於 VM 的 Azure 監視器來賓健全狀況 (preview) 
適用於 VM 的 Azure 監視器來賓健康狀態可讓您依定期取樣的一組效能測量所定義，來查看虛擬機器的健康情況。 本文說明如何在您的訂用帳戶中啟用這項功能，以及如何啟用每部虛擬機器的來賓監視。

## <a name="current-limitations"></a>目前的限制
適用於 VM 的 Azure 監視器來賓健康情況在公開預覽中有下列限制：

- 目前僅支援 Azure 虛擬機器。 目前不支援適用於伺服器的 Azure Arc。
- 虛擬機器必須執行下列其中一個作業系統： 
  - Ubuntu 16.04 LTS、Ubuntu 18.04 LTS
  - Windows Server 2012 或更新版本
- 虛擬機器必須位於下列其中一個區域：
  - 澳大利亞東南部
  - 美國中部
  - 美國東部
  - 美國東部 2
  - 美國東部 2 EUAP
  - 北歐
  - 東南亞
  - 英國南部
  - 西歐
  - 美國西部
  - 美國西部 2
- Log Analytics 工作區必須位於下列其中一個區域：
  - 美國東部
  - 美國東部 2 EUAP
  - 西歐區域

## <a name="prerequisites"></a>先決條件

- 虛擬機器必須上線至適用於 VM 的 Azure 監視器。
- 執行上線步驟的使用者必須擁有虛擬機器和資料收集規則所在訂用帳戶的最小參與者層級存取權。
- 必須註冊必要的 Azure 資源提供者，如下一節所述。


## <a name="register-required-azure-resource-providers"></a>註冊所需的 Azure 資源提供者
下列 Azure 資源提供者必須為您的訂用帳戶註冊，才能啟用適用於 VM 的 Azure 監視器來賓健康情況。 

- Microsoft.WorkloadMonitor
- Microsoft.Insights

您可以使用任何可用的方法來註冊資源提供者 [，如 Azure 資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)中所述。 您也可以使用下列使用 armclient、postman 或另一個方法的範例命令，對 Azure Resource Manager 進行驗證的呼叫：

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>使用 Azure 入口網站啟用虛擬機器
當您為 Azure 入口網站中的虛擬機器啟用來賓健全狀況時，系統會為您執行所有必要的設定。 這包括建立需要資料收集規則、在虛擬機器上安裝來賓健全狀況延伸模組，以及建立與資料集合規則的關聯。

從適用於 VM 的 Azure 監視器的 [ **開始** ] 中，按一下虛擬機器的升級訊息旁的連結，然後按一下 [ **升級** ] 按鈕。 您也可以選取多個虛擬機器，將它們一起升級。

![啟用虛擬機器上的健康情況功能](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>使用 Resource Manager 範本啟用虛擬機器
使用 Azure Resource Manager 啟用虛擬機器需要執行三個步驟。

- 建立資料集合規則。
- 在每部虛擬機器上安裝來賓健全狀況延伸模組
- 建立虛擬機器與資料集合規則之間的關聯。

### <a name="create-data-collection-rule-dcr"></a>建立資料集合規則 (DCR) 

> [!NOTE]
> 如果您使用 Azure 入口網站啟用虛擬機器，則會為您建立此處所述的資料收集規則。 在此情況下，您不需要執行此步驟。

適用於 VM 的 Azure 監視器來賓健全狀況中的監視設定會儲存在 [資料集合規則 (DCR) ](../platform/data-collection-rule-overview.md)中。 安裝下列 Resource Manager 範本中定義的資料收集規則，以啟用具有來賓健康情況擴充功能的虛擬機器的所有監視。 具有來賓健康情況擴充功能的每部虛擬機器都需要與此規則相關聯。

> [!NOTE]
> 您可以建立其他資料收集規則來修改預設的監視設定，如 [設定監視適用於 VM 的 Azure 監視器來賓健康情況 (預覽) ](vminsights-health-configure.md)中所述。

範本需要下列參數的值：

- **defaultHealthDataCollectionRuleName**：保留範本中定義的預設名稱。
- **destinationWorkspaceResourceId**：用於虛擬機器資料收集之 Log Analytics 工作區的資源識別碼。
- **dataCollectionRuleLocation**：資料收集規則的區域。 這必須符合 Log Analytics 工作區的區域。


使用 [Resource Manager 範本的任何部署方法來](../../azure-resource-manager/templates/deploy-powershell.md)部署範本。 下列命令會使用 PowerShell 或 Azure CLI 來部署範本和參數檔案。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---



```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data colleciton rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              }
            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>範例參數檔案

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



## <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>安裝來賓健全狀況延伸模組並與資料收集規則產生關聯
使用下列 Resource Manager 範本來啟用虛擬機器的來賓健全狀況。 這會安裝 guest 健康情況擴充功能，並建立與資料集合規則的關聯。 您可以使用 [Resource Manager 範本的任何部署方法](../../azure-resource-manager/templates/deploy-powershell.md)來部署此範本。


例如，使用下列命令，透過 PowerShell 或 Azure CLI 將範本和參數檔案部署至資源群組。


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="cli"></a>[CLI](#tab/cli)

```cli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>範本檔案

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>範例參數檔案

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      },
      "healthExtensionVersion": {
        "value": "private-preview"
      }
  }
}
```

## <a name="next-steps"></a>後續步驟

- [自訂適用於 VM 的 Azure 監視器所啟用的監視](vminsights-health-configure.md)