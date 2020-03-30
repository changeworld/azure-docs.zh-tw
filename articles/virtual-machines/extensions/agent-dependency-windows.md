---
title: Azure 監視器依賴項虛擬機器擴展視窗
description: 使用虛擬機器擴展在 Windows 虛擬機器上部署 Azure 監視器依賴項代理。
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 27d43af2d5860d287d8b5914379747ae528db34b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250669"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-windows"></a>Azure 監視器依賴項虛擬機器擴展視窗

適用於 VM 的 Azure 監視器對應功能會從 Microsoft Dependency Agent 取得其資料。 Microsoft 發佈並支援 Windows 的 Azure VM 依賴項代理虛擬機器擴展。 擴展在 Azure 虛擬機器上安裝依賴項代理。 本文檔詳細介紹了適用于 Windows 的 Azure VM 依賴項代理虛擬機器擴展的支援平臺、配置和部署選項。

## <a name="operating-system"></a>作業系統

可以針對 Azure 監視器 VM 部署文章中列出的受支援的作業系統運行 Windows[Supported operating systems](../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)的 Azure VM 依賴項代理擴展。

## <a name="extension-schema"></a>擴充功能結構描述

以下 JSON 顯示了 Azure Windows VM 上的 Azure VM 依賴項代理代理擴展的架構。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Azure VM. Supported Windows Server versions:  2008 R2 and above (x64)."
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
        "type": "DependencyAgentWindows",
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

| 名稱 | 值/示例 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | 微軟.Azure.監視.依賴代理 |
| type | 依賴項代理視窗 |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>範本部署

您可以使用 Azure 資源管理器範本部署 Azure VM 擴展。 可以使用 Azure 資源管理器範本中上一節中詳述的 JSON 架構在 Azure 資源管理器範本部署期間運行 Azure VM 依賴項代理擴展。

虛擬機器擴展的 JSON 可以嵌套在虛擬機器資源中。 或者，您可以將它放置在資源管理器 JSON 範本的根級或頂層。 JSON 的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/templates/child-resource-name-type.md)。

下面的示例假定依賴項代理擴展嵌套在虛擬機器資源中。 嵌套擴展資源時，JSON 將放置在虛擬機器`"resources": []`的物件中。


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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

將擴展 JSON 放在範本的根目錄時，資源名稱包含對父虛擬機器的引用。 該類型反映嵌套配置。

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
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 部署

可以使用 命令`Set-AzVMExtension`將依賴項代理虛擬機器擴展部署到現有虛擬機器。 在運行該命令之前，公共配置和私有配置需要存儲在 PowerShell 雜湊表中。

```powershell

Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

有關擴展部署狀態的資料可以從 Azure 門戶和使用 Azure PowerShell 模組檢索。 要查看給定 VM 的擴展的部署狀態，請使用 Azure PowerShell 模組運行以下命令：

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

擴充功能執行輸出會記錄至下列目錄中的檔案︰

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitoring.DependencyAgent\
```

### <a name="support"></a>支援

如果本文中的任何一點都需要更多説明，則可以在[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上聯繫 Azure 專家。 或者，您可以提交 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]****。 有關如何使用 Azure 支援的資訊，請閱讀[Microsoft Azure 支援常見問題解答](https://azure.microsoft.com/support/faq/)。
