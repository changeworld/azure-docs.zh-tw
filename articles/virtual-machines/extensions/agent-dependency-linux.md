---
title: 適用於 Linux 的 Azure 監視器相依性虛擬機器擴充功能
description: 使用虛擬機器擴充功能在 Linux 虛擬機器上部署 Azure 監視器相依性代理程式。
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
ms.openlocfilehash: cf4653ea7e49404af9e6d46796a7b00d9373c6da
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996545"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>適用於 Linux 的 Azure 監視器相依性虛擬機器擴充功能

適用於 VM 的 Azure 監視器對應功能會從 Microsoft Dependency Agent 取得其資料。 Microsoft 已發佈並支援適用於 Linux 的 Azure VM 相依性代理程式虛擬機器擴充功能。 此擴充功能會在 Azure 虛擬機器上安裝相依性代理程式。 此文件詳細說明適用於 Linux 的 Azure VM 相依性代理程式虛擬機器擴充功能所支援的平台、組態和部署選項。

## <a name="prerequisites"></a>必要條件

### <a name="operating-system"></a>作業系統

適用於 Linux 的 Azure VM 相依性代理程式擴充功能可以針對「適用於 VM 的 Azure 監視器」部署文章的[支援的作業系統](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)一節中所列的支援的作業系統來執行。

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Azure Linux VM 上 Azure VM 相依性代理程式擴充功能的結構描述。 

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

| 名稱 | 值/範例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| type | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>範本部署

您可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 在部署 Azure Resource Manager 範本時，您可以在 Azure Resource Manager 範本中使用上一節詳述的 JSON 結構描述來執行 Azure VM 相依性代理程式擴充功能。

虛擬機器擴充功能的 JSON 能以巢狀方式置於虛擬機器資源內部。 或者，您可以將其放在 Resource Manager JSON 範本的根目錄層或最上層。 JSON 的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/templates/child-resource-name-type.md)。

下列範例假設相依性代理程式擴充功能以巢狀方式置於虛擬機器資源內部。 當您以巢狀方式安置擴充功能資源時，JSON 會放在虛擬機器的 `"resources": []` 物件中。


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

當您將擴充功能 JSON 放在範本的根目錄時，資源名稱會包含對父代虛擬機器的參考。 型別會反映以巢狀方式安置的設定。 

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

您可以使用 Azure CLI 從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令：

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

擴充功能執行輸出會記錄至下列檔案︰

```
/opt/microsoft/dependency-agent/log/install.log 
```

### <a name="support"></a>支援

如果您在此文章中有任何需要協助的地方，請連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您也可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需如何使用 Azure 支援的相關資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
