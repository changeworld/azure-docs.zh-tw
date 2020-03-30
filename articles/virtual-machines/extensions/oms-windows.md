---
title: 適用於 Windows 的 Log Analytics 虛擬機器擴充功能
description: 使用虛擬機器擴充功能在 Windows 虛擬機器上部署 Log Analytics 代理程式。
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/30/2020
ms.author: akjosh
ms.openlocfilehash: 85977819d30ddc8745eb9231242eb1990222676c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530983"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>適用於 Windows 的 Log Analytics 虛擬機器擴充功能

Azure 監視器日誌提供跨雲和本地資產的監視功能。 Microsoft 已發佈和支援適用於 Windows 的 Log Analytics 代理程式虛擬機器擴充功能。 擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並且在現有的 Log Analytics 工作區中註冊虛擬機器。 本文件詳述適用於 Windows 的 Log Analytics 虛擬機器擴充功能所支援的平台、組態和部署選項。

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

有關受支援的 Windows 作業系統的詳細資訊，請參閱[日誌分析代理概述](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)一文。

### <a name="agent-and-vm-extension-version"></a>代理程式和 VM 擴充功能版本
下表提供了每個版本的 Windows 日誌分析 VM 擴展和日誌分析代理包版本的映射。 

| 日誌分析 Windows 代理包包版本 | 日誌分析 Windows VM 擴展版本 | 發行日期 | 版本資訊 |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18029 | 1.0.18029 | 2020 年 3 月   | <ul><li>添加 SHA-2 代碼簽名支援</li><li>改進 VM 擴展安裝和管理</li><li>解決 Azure Arc 中用於伺服器集成的 Bug</li><li>為客戶支援添加內置故障排除工具</li><li>添加對其他 Azure 政府區域的支援</li> |
| 10.20.18018 | 1.0.18018 | 2019 年 10 月 | <ul><li> 次要錯誤修復和穩定改進 </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019 年 7 月 | <ul><li> 次要錯誤修復和穩定改進 </li><li> 將最大表達深度提高到 10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019 年 6 月 | <ul><li> 次要錯誤修復和穩定改進 </li><li> 在建立代理連接時禁用預設憑據（支援WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH） </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019 年 3 月 | <ul><li>次要穩定修復 </li></ul> |
| 10.19.10006 | n/a | 2018 年 12 月 | <ul><li> 次要穩定修復 </li></ul> | 
| 8.0.11136 | n/a | 2018 年 9 月 |  <ul><li> 添加了對在 VM 移動時檢測資源識別碼 更改的支援 </li><li> 添加了使用非擴展安裝時對報告資源識別碼 的支援 </li></ul>| 
| 8.0.11103 | n/a |  2018 年 4 月 | |
| 8.0.11081 | 1.0.11081 | 2017 年 11 月 | | 
| 8.0.11072 | 1.0.11072 | 2017 年 9 月 | |
| 8.0.11049 | 1.0.11049 | 2017 年 2 月 | |


### <a name="azure-security-center"></a>Azure 資訊安全中心

Azure 安全中心自動提供日誌分析代理，並將其與 Azure 訂閱的預設日誌分析工作區連接。 如果您使用的是 Azure 資訊安全中心，請不要執行此文件中的步驟。 這樣做會覆寫已設定的工作區，並中斷與 Azure 資訊安全中心的連線。

### <a name="internet-connectivity"></a>網際網路連線
適用於 Windows 的 Log Analytics 代理程式擴充功能會要求目標虛擬機器連線到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Log Analytics 代理程式擴充功能的結構描述。 擴展需要目標日誌分析工作區中的工作區 ID 和工作區金鑰。 在 Azure 入口網站中工作區的設定中可找到這些項目。 由於工作區金鑰應視為敏感性資料，因此應儲存在受保護的設定組態中。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。 請注意，**workspaceId** 和 **workspaceKey** 區分大小寫。

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>屬性值

| 名稱 | 值 / 範例 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (例如)* | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (例如) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

\* workspaceId 在 Log Analytics API 中稱為 consumerId。

> [!NOTE]
> 有關其他屬性，請參閱 Azure[將 Windows 電腦連接到 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)。

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節中詳述的 JSON 結構描述可在部署 Azure Resource Manager 範本期間，在 Azure Resource Manager 範本中用來執行 Log Analytics 代理程式擴充功能。 在[Azure 快速入門庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm)上可以找到包含日誌分析代理 VM 擴展的示例範本。 

>[!NOTE]
>當您要將代理配置為向多個工作區報告時，該範本不支援指定多個工作區 ID 和工作區金鑰。 要將代理配置為向多個工作區報告，請參閱[添加或刪除工作區](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)。  

虛擬機器擴充功能的 JSON 可以巢狀方式置於虛擬機器資源內部，或放在 Resource Manager JSON 範本的根目錄或最上層。 JSON 的放置會影響資源名稱和類型的值。 如需詳細資訊，請參閱[設定子資源的名稱和類型](../../azure-resource-manager/templates/child-resource-name-type.md)。 

下列範例假設 Log Analytics 擴充功能以巢狀方式置於虛擬機器資源內部。 在巢狀處理擴充資源時，JSON 會放在虛擬機器的 `"resources": []` 物件中。


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

將擴充 JSON 置於範本的根目錄時，資源名稱包含父系虛擬機器的參考，而類型可反映巢狀的組態。 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzVMExtension` 命令可以用來將 Log Analytics 代理程式虛擬機器擴充功能部署到現有的虛擬機器。 執行命令之前，必須將公用和私人組態儲存在 PowerShell 雜湊表中。 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzVMExtension -ExtensionName "MicrosoftMonitoringAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure PowerShell 模組，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看所指定 VM 的擴充功能部署狀態，請使用 Azure PowerShell 模組來執行下列命令。

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

擴充功能執行輸出會記錄至下列目錄中的檔案︰

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>支援

如果本文中的任何一點都需要更多説明，則可以在[MSDN Azure 和堆疊溢位論壇](https://azure.microsoft.com/support/forums/)上聯繫 Azure 專家。 或者，您可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇"獲取支援"。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
