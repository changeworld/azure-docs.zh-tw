---
title: 網路監看員-使用 Azure Resource Manager 範本建立 NSG 流量記錄
description: 使用 Azure Resource Manager 範本和 PowerShell 輕鬆地設定 NSG 流量記錄。
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 104311904b99cadbbc8c0267a98f2709443608ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80891453"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>從 Azure Resource Manager 範本設定 NSG 流量記錄

> [!div class="op_single_selector"]
> - [Azure 入口網站](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)是 Azure 的原生且強大的方式，可管理您的[基礎結構即程式碼](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)。

本文說明如何使用 Azure Resource Manager 範本和 Azure PowerShell 以程式設計方式啟用[NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 我們一開始先提供 NSG Flow 記錄物件的屬性總覽，再加上幾個範例範本。 然後，我們會使用本機 PowerShell 實例來部署範本。


## <a name="nsg-flow-logs-object"></a>NSG 流量記錄物件

具有所有參數的 NSG Flow 記錄物件如下所示。
如需屬性的完整總覽，您可以閱讀[NSG 流量記錄範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)。

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
若要建立 networkWatchers/flowLogs 資源，請將上述 JSON 新增至範本的 resources 區段。


## <a name="creating-your-template"></a>建立您的範本

如果您是第一次使用 Azure Resource Manager 範本，您可以使用下列連結深入瞭解它們。

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [教學課程：建立及部署您的第一個 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


以下是設定 NSG 流量記錄的兩個完整範本範例。

**範例 1**：上述最簡單的版本，傳遞最少的參數。 下列範本會在目標 NSG 上啟用 NSG 流量記錄，並將它們儲存在指定的儲存體帳戶中。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * 資源名稱的格式為「父資源>/Child 資源」。 在這裡，父資源是區域網路監看員實例（格式： NetworkWatcher_<RegionName>。 範例： NetworkWatcher_centraluseuap）
> * targetResourceId 是目標 NSG 的資源識別碼
> * storageId 是目的地儲存體帳戶的資源識別碼

**範例 2**：下列範本可啟用 NSG 流量記錄（第2版），保留期為5天。 以10分鐘的處理間隔啟用流量分析。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>部署您的 Azure Resource Manager 範本

本教學課程假設您有現有的資源群組，以及可啟用流量記錄的 NSG。
您可以在本機將上述範例範本儲存為`azuredeploy.json`。 更新屬性值，使其指向您的訂用帳戶中的有效資源。

若要部署範本，請在 PowerShell 中執行下列命令。
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>正在驗證您的部署

有幾種方式可以檢查您的部署是否成功。 您的 PowerShell 主控台應該會將 "ProvisioningState" 顯示為「成功」。 此外，您可以流覽[NSG Flow 記錄入口網站頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)，以確認您的變更。 如果部署有問題，請參閱[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)。

## <a name="deleting-your-resource"></a>刪除您的資源
Azure 可透過「完成」部署模式來刪除資源。 若要刪除流量記錄資源，請在 [完整] 模式中指定部署，而不包含您想要刪除的資源。 深入瞭解[完整部署模式](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

## <a name="next-steps"></a>後續步驟

瞭解如何使用來視覺化您的 NSG 流量資料：
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [開放原始碼工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
