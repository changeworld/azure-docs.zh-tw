---
title: 網路觀察程式 - 使用 Azure 資源管理員樣本建立 NSG 串流紀錄
description: 使用 Azure 資源管理器範本和 PowerShell 輕鬆設定 NSG 串流日誌。
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
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891453"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>從 Azure 資源管理員樣本設定 NSG 串流紀錄

> [!div class="op_single_selector"]
> - [Azure 門戶](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure 資源管理員](https://azure.microsoft.com/features/resource-manager/)是 Azure 作為[代碼管理基礎結構](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)的本機和強大方法。

本文展示如何使用 Azure 資源管理員樣本和 Azure PowerShell 以程式設計模式啟用[NSG 串流紀錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 我們首先提供 NSG 串流日誌物件屬性的概述,然後是幾個範例範本。 然後,我們使用本地 PowerShell 實例部署範本。


## <a name="nsg-flow-logs-object"></a>NSG 串流記錄物件

具有所有參數的 NSG 流日誌物件如下所示。
有關屬性的完整概述,請閱讀[NSG 串流紀錄樣本參考](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)。

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
要創建 Microsoft.Network/網路觀察程式/流日誌資源,請將上述 JSON 添加到範本的資源部分。


## <a name="creating-your-template"></a>建立樣本

如果首次使用 Azure 資源管理器範本,則可以使用以下連結瞭解有關這些範本的更多內容。

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [教學:建立並部署第一個 Azure 資源管理員範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


下面是兩個用於設置 NSG 流日誌的完整範本範例。

**範例 1**: 上述最簡單的版本,傳遞最小參數。 以下範本支援目標 NSG 上的 NSG 串流日誌,並將它們儲存在給定的儲存帳戶中。

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
> * 資源的名稱具有"父資源>/子資源"的格式。 此處,父資源是區域網路觀察程式實例(格式:NetworkWatcher_<RegionName>。 範例:NetworkWatcher_centraluseuap)
> * 目標資源代碼是目標 NSG 的資源識別碼
> * 儲存 Id 是目標儲存帳戶的資源識別碼

**範例 2**: 以下樣本啟用 NSG 串流紀錄(版本 2),保留 5 天。 啟用處理間隔為 10 分鐘的流量分析。

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

## <a name="deploying-your-azure-resource-manager-template"></a>部署 Azure 資源管理員樣本

本教程假定您具有現有資源組和可以啟用流登錄的 NSG。
您可以儲存此選項的樣本 。`azuredeploy.json` 更新屬性值,以便它們指向訂閱中的有效資源。

要部署範本,在 PowerShell 中執行以下命令。
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>驗證部署

有幾種方法可以檢查部署是否成功。 您的 PowerShell 主控台應將「預配狀態」顯示為」。成功」。 此外,您還可以訪問[NSG 串流日誌門戶頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)以確認您的更改。 如果部署出現問題,請檢視[Azure 資源管理器中常見的 Azure 部署錯誤故障排除](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)。

## <a name="deleting-your-resource"></a>刪除資源
Azure 通過"完成"部署模式啟用資源刪除。 要刪除串流紀錄資源,請在「完成」模式下指定部署,而不包括要刪除的資源。 閱讀有關[「完成部署模式」的更多內容](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

## <a name="next-steps"></a>後續步驟

瞭解如何使用:
* [微軟電源 BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [開源工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
