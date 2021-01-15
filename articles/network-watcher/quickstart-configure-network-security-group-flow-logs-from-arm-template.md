---
title: 快速入門：使用 Azure Resource Manager 範本 (ARM 範本) 設定網路安全性群組流量記錄
description: 了解如何使用 Azure Resource Manager 範本 (ARM 範本) 和 Azure PowerShell，以程式設計方式啟用網路安全性群組 (NSG) 流量記錄。
services: network-watcher
author: damendo
Customer intent: I need to enable the network security group flow logs by using an Azure Resource Manager template.
ms.service: network-watcher
ms.topic: quickstart
ms.date: 01/07/2021
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: ded7b24461fdcdbc3d020a487cafc20620633097
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019715"
---
# <a name="quickstart-configure-network-security-group-flow-logs-by-using-an-arm-template"></a>快速入門：使用 ARM 範本設定網路安全性群組流量記錄

在本快速入門中，您將了解如何使用 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 範本 (ARM 範本) 和 Azure PowerShell 啟用[網路安全性群組 (NSG) 流量記錄](network-watcher-nsg-flow-logging-overview.md)。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

首先我們會概述 NSG flow 記錄物件的屬性。 我們將提供範例範本。 然後，我們會使用本機 Azure PowerShell 執行個體來部署範本。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>檢閱範本

我們在本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)。

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

範本中定義了下列資源：

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>NSG 流量記錄物件

下列程式碼顯示 NSG flow 記錄物件及其參數。 若要建立 `Microsoft.Network/networkWatchers/flowLogs` 資源，請將此程式碼新增至範本的資源區段：

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

如需 NSG 記錄物件屬性的完整概觀，請參閱 [networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs)。

## <a name="create-your-template"></a>建立您自己的範本

如果您是第一次使用 ARM 範本，請參閱下列文章以深入了解 ARM 範本：

- [使用 ARM 範本與 Azure PowerShell 來部署資源](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [教學課程：建立及部署您的第一個 ARM 範本](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

下列範例是完整的範本。 這也是最簡單的範本版本。 此範例包含要設定 NSG 流量記錄所需傳遞的基本參數。 如需更多範例，請參閱概觀文章[從 Azure Resource Manager 範本設定 NSG 流程記錄](network-watcher-nsg-flow-logging-azure-resource-manager.md)。

### <a name="example"></a>範例

下列範本會啟用 NSG 的流量記錄，然後將記錄儲存在特定的儲存體帳戶中：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - 資源名稱會使用 _ParentResource_ChildResource_ 的格式。 在我們的範例中，父資源是區域 Azure 網路監看員執行個體：
>    - **Format**︰NetworkWatcher_RegionName
>    - **範例**：NetworkWatcher_centraluseuap
> - `targetResourceId` 是目標 NSG 的資源識別碼。
> - `storageId` 是目的地儲存體帳戶的資源識別碼。

## <a name="deploy-the-template"></a>部署範本

本教學課程假設您具有現有的資源群組，以及可啟用流量記錄的 NSG。

您可以將本文中顯示的任何範例範本以 *azuredeploy.json* 的形式儲存在本機。 更新屬性值，使其指向您訂用帳戶中的有效資源。

若要部署範本，請在 Azure PowerShell 中執行下列命令：

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 這些命令會將資源部署至範例 NetworkWatcherRG 資源群組，而不是包含 NSG 的資源群組。

## <a name="validate-the-deployment"></a>驗證部署

您有兩個選項可檢視部署是否成功：

- 您的 PowerShell 主控台會將 `ProvisioningState` 顯示為 `Succeeded`。
- 移至 [NSG 流量記錄入口網站頁面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)以確認您的變更。 

如果部署發生問題，請查看[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](../azure-resource-manager/templates/common-deployment-errors.md)。

## <a name="clean-up-resources"></a>清除資源

您可以使用完整的部署模式來刪除 Azure 資源。 若要刪除流量記錄資源，請在「完整」模式中指定部署，而不包含您想要刪除的資源。 深入了解[完整部署模式](../azure-resource-manager/templates/deployment-modes.md#complete-mode)。

您也可以在 Azure 入口網站中停用 NSG 流量記錄：

1. 登入 Azure 入口網站。
1. 選取 [所有服務]。 在 [篩選] 方塊中，輸入 **網路監看員**。 在搜尋結果中，選取 [網路監看員]。
1. 在 [記錄] 底下，選取 [NSG 流量記錄]。
1. 在 NSG 清單中，選取您要停用流量記錄的 NSG。
1. 在 [流量記錄設定] 底下，選取 [關閉]。
1. 選取 [儲存]。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用 ARM 範本來啟用 NSG 流量記錄。 接下來，請了解如何使用下列其中一個選項將 NSG 流量資料視覺化：

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [開放原始碼工具](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Azure 流量分析](traffic-analytics.md)
