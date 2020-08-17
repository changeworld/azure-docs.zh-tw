---
title: 快速入門：使用 Azure Resource Manager 範本設定 NSG 流程記錄
description: 了解如何使用 Azure Resource Manager 範本和 Azure PowerShell，以程式設計方式啟用 NSG 流量記錄。
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986312"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>快速入門：從 ARM 範本設定 NSG 流量記錄

在本快速入門中，您會使用 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) 範本 (ARM 範本) 和 Azure PowerShell，以程式設計方式啟用 [NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)。 

一開始會先提供 NSG Flow 記錄物件的屬性概觀，再加上幾個範例範本。 然後會使用本機 PowerShell 執行個體來部署範本。

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="nsg-flow-logs-object"></a>NSG 流量記錄物件

具有所有參數的 NSG Flow 記錄物件如下所示。
如需屬性的完整概觀，請參閱 [NSG 流量記錄範本參考](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs)。

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
若要建立 networkWatchers/flowLogs 資源，請將上述 JSON 新增至範本的資源區段。


## <a name="creating-your-template"></a>建立範本

如果您是第一次使用 Azure Resource Manager 範本，您可以使用下列連結深入瞭解這些範本。

* [使用 Resource Manager 範本與 Azure PowerShell 來部署資源](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [教學課程：建立及部署第一個 Azure Resource Manager 範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)。

以下完整範本範例是最簡單的版本，藉由傳遞最少的參數來設定 NSG 流量記錄。 如需更多範例，請移至此[連結](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)。

**範例**：下列範本會在目標 NSG 上啟用 NSG 流量記錄，並將這些記錄儲存在指定的儲存體帳戶中。

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
> * 資源名稱的格式為「Parent Resource_Child resource」。 在這裡，父代資源是區域網路監看員執行個體 (格式：NetworkWatcher_RegionName。 範例：NetworkWatcher_centraluseuap)
> * targetResourceId 是目標 NSG 的資源識別碼
> * storageId 是目的地儲存體帳戶的資源識別碼


## <a name="deploying-your-azure-resource-manager-template"></a>部署您的 Azure Resource Manager 範本

本教學課程假設您有現有的資源群組，以及可啟用流量記錄的 NSG。
您可以在本機將上述範例範本儲存為 `azuredeploy.json`。 更新屬性值，使其指向您訂閱中的有效資源。

若要部署範本，請在 PowerShell 中執行下列命令。
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 上述命令會將資源部署至 NetworkWatcherRG 資源群組，而不是包含 NSG 的資源群組


## <a name="validate-the-deployment"></a>驗證部署

有幾種方式可以檢查您的部署是否成功。 您的 PowerShell 主控台應該會將「ProvisioningState」顯示為「成功」。 此外，您可以瀏覽 [NSG 流量記錄入口網站頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)以確認您的變更。 如果部署發生問題，請查看[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)。

## <a name="deleting-your-resource"></a>刪除資源
Azure 可透過「完成」部署模式來刪除資源。 若要刪除流量記錄資源，請在「完整」模式中指定部署，而不包含您想要刪除的資源。 深入瞭解[完整部署模式](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

或者，您可以根據下列步驟，從 Azure 入口網站停用 NSG 流量記錄：
1. 登入 Azure 入口網站
2. 在入口網站的左上角，選取 [所有服務]。 在 [篩選] 方塊中，輸入*網路監看員*。 當搜尋結果中出現**網路監看員**時，請加以選取。
3. 在 [記錄] 底下，選取 [NSG 流量記錄]
4. 從 NSG 清單中，選取您想要停用流量記錄的 NSG
5. 在 [流量記錄設定] 底下，將 [流程記錄狀態] 設定為 [關閉]
6. 向下捲動，然後選取 [儲存]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已啟用 NSG 流量記錄。 現在，您必須了解如何使用下列項目將 NSG 流量記錄視覺化： 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [開啟來源工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
