---
title: 資料收集規則的 Resource Manager 範本範例
description: 範例 Azure Resource Manager 範本，可用來在 Azure 監視器中建立資料收集規則與虛擬機器之間的關聯。
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 11/17/2020
ms.openlocfilehash: 12e6aa85f7d8c2c4aebe80efc92f771917e6a1d5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746007"
---
# <a name="resource-manager-template-samples-for-data-collection-rules-in-azure-monitor"></a>Azure 監視器中資料收集規則的 Resource Manager 範本範例
本文說明為 Azure 監視器中的虛擬機器部署和設定 [Log Analytics 代理程式](../platform/log-analytics-agent.md)與[診斷擴充功能](../platform/diagnostics-extension-overview.md)時適用的範例 [Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)。 每個範例都包含範本檔案和參數檔案，且附有要提供給範本的範例值。

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-association-with-azure-vm"></a>建立與 Azure VM 間的關聯

下列範例會在 Windows Azure 虛擬機器上安裝 Azure 監視器代理程式。 Azure 虛擬機器與資料集合規則之間會建立關聯。

### <a name="template-file"></a>範本檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('vmName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this virtual machine.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>參數檔案

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```

## <a name="create-association-ith-azure-arc"></a>建立與 Azure Arc 間的關聯

下列範例會在 Windows Azure 虛擬機器上安裝 Azure 監視器代理程式。 已啟用 Azure Arc 的伺服器機器與資料集合規則之間會建立關聯。

### <a name="template-file"></a>範本檔案

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the virtual machine."
            }
        },
        "associationName": {
            "type": "string",
            "metadata": {
                "description": "Name of the association."
            }
        },
        "dataCollectionRuleId": {
            "type": "string",
            "metadata": {
                "description": "Resource ID of the data collection rule."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.HybridCompute/machines/providers/dataCollectionRuleAssociations",
            "name": "[concat(parameters('machineName'),'/microsoft.insights/', parameters('associationName'))]",
            "apiVersion": "2019-11-01-preview",
            "properties": {
                "description": "Association of data collection rule. Deleting this association will break the data collection for this Arc server.",
                "dataCollectionRuleId": "[parameters('dataCollectionRuleId')]"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>參數檔案

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
        "value": "my-windows-vm"
      },
      "location": {
        "value": "eastus"
      }
  }
}
```


## <a name="next-steps"></a>後續步驟

* [取得 Azure 監視器的其他範例範本](resource-manager-samples.md)。
* [深入了解 Log Analytics 代理程式](../platform/log-analytics-agent.md)。
* [深入了解診斷擴充功能](../platform/diagnostics-extension-overview.md)。
