---
title: 適用于 Linux 的 Azure 監視器相依性虛擬機器擴充功能
description: 使用虛擬機器擴充功能，在 Linux 虛擬機器上部署 Azure 監視器 Dependency agent。
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 82f9c5a67cb056752cf8310be3b7c9f0bd2501e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254036"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>適用于 Linux 的 Azure 監視器相依性虛擬機器擴充功能

適用於 VM 的 Azure 監視器對應功能會從 Microsoft Dependency Agent 取得其資料。 適用于 Linux 的 Azure VM 相依性代理程式虛擬機器擴充功能已由 Microsoft 發佈及支援。 擴充功能會在 Azure 虛擬機器上安裝 Dependency agent。 本檔詳述適用于 Linux 的 Azure VM 相依性代理程式虛擬機器擴充功能所支援的平臺、設定和部署選項。

## <a name="prerequisites"></a>先決條件

### <a name="operating-system"></a>作業系統

適用于 Linux 的 Azure VM 相依性代理程式擴充功能可以針對適用於 VM 的 Azure 監視器部署一文的[支援的作業系統](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)一節中所列的支援作業系統來執行。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Azure Linux VM 上的 Azure VM Dependency agent 擴充功能架構。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>屬性值

| Name | 值/範例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | DependencyAgent。 |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>範本部署

您可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 您可以使用 Azure Resource Manager 範本上一節中詳述的 JSON 架構，在 Azure Resource Manager 範本部署期間執行 Azure VM Dependency agent 擴充功能。

虛擬機器擴充功能的 JSON 可以嵌套在虛擬機器資源內。 或者，您可以將它放在 Resource Manager JSON 範本的根層或最上層。 JSON 的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/templates/child-resource-name-type.md)。

下列範例假設 Dependency agent 延伸模組是嵌套在虛擬機器資源內部。 當您將擴充功能資源嵌套時，JSON 會放在`"resources": []`虛擬機器的物件中。


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

當您將擴充功能 JSON 放在範本的根目錄時，資源名稱會包含父虛擬機器的參考。 型別會反映嵌套的設定。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 部署

您可以使用 Azure CLI，將相依性代理程式 VM 擴充功能部署到現有的虛擬機器。  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

您可以從 Azure 入口網站和使用 Azure CLI，抓取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令：

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，請洽詢[MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]****。 如需如何使用 Azure 支援的相關資訊，請參閱[Microsoft Azure 支援常見問題](https://azure.microsoft.com/support/faq/)。
