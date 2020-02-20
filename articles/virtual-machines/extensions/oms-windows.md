---
title: 適用於 Windows 的 Log Analytics 虛擬機器擴充功能
description: 使用虛擬機器擴充功能在 Windows 虛擬機器上部署 Log Analytics 代理程式。
services: virtual-machines-windows
documentationcenter: ''
author: MicahMcKittrick-MSFT
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
ms.openlocfilehash: 85b97f31e77736603bd0dc7003d4dbfb91a694dc
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470695"
---
# <a name="log-analytics-virtual-machine-extension-for-windows"></a>適用於 Windows 的 Log Analytics 虛擬機器擴充功能

Azure 監視器記錄可提供跨雲端和內部部署資產的監視功能。 Microsoft 已發佈和支援適用於 Windows 的 Log Analytics 代理程式虛擬機器擴充功能。 擴充功能會在 Azure 虛擬機器上安裝 Log Analytics 代理程式，並且在現有的 Log Analytics 工作區中註冊虛擬機器。 本文件詳述適用於 Windows 的 Log Analytics 虛擬機器擴充功能所支援的平台、組態和部署選項。

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

如需有關支援的 Windows 作業系統的詳細資訊，請參閱[Log Analytics 代理程式總覽](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)一文。

### <a name="agent-and-vm-extension-version"></a>代理程式和 VM 擴充功能版本
下表提供每個版本之 Windows Log Analytics VM 擴充功能和 Log Analytics 代理程式套件組合的版本對應。 

| Log Analytics Windows 代理程式套件組合版本 | Log Analytics Windows VM 延伸模組版本 | 發行日期 | 版本資訊 |
|--------------------------------|--------------------------|--------------------------|--------------------------|
| 10.20.18018 | 1.0.18018 | 2019 年 10 月 | <ul><li> 次要錯誤修正和穩定改良功能 </li></ul> |
| 10.20.18011 | 1.0.18011 | 2019 年 7 月 | <ul><li> 次要錯誤修正和穩定改良功能 </li><li> 將 MaxExpressionDepth 增加至10000 </li></ul> |
| 10.20.18001 | 1.0.18001 | 2019 年 6 月 | <ul><li> 次要錯誤修正和穩定改良功能 </li><li> 已新增在進行 proxy 連線時停用預設認證的功能（WINHTTP_AUTOLOGON_SECURITY_LEVEL_HIGH 的支援） </li></ul>|
| 10.19.13515 | 1.0.13515 | 2019 年 3 月 | <ul><li>次要穩定修正 </li></ul> |
| 10.19.10006 | n/a | 12月2018 | <ul><li> 次要穩定修正 </li></ul> | 
| 8.0.11136 | n/a | 2018年9月 |  <ul><li> 已新增偵測 VM 移動上資源識別碼變更的支援 </li><li> 已新增使用非延伸模組安裝時的報告資源識別碼支援 </li></ul>| 
| 8.0.11103 | n/a |  2018 年 4 月 | |
| 8.0.11081 | 1.0.11081 | 2017年11月 | | 
| 8.0.11072 | 1.0.11072 | 2017年9月 | |
| 8.0.11049 | 1.0.11049 | 2017年2月 | |

### <a name="azure-security-center"></a>Azure 資訊安全中心

Azure 資訊安全中心會自動布建 Log Analytics 代理程式，並將它與 Azure 訂用帳戶的預設 Log Analytics 工作區連接。 如果您使用的是 Azure 資訊安全中心，請不要執行此文件中的步驟。 這樣做會覆寫已設定的工作區，並中斷與 Azure 資訊安全中心的連線。

### <a name="internet-connectivity"></a>網際網路連線
適用於 Windows 的 Log Analytics 代理程式擴充功能會要求目標虛擬機器連線到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

下列 JSON 顯示 Log Analytics 代理程式擴充功能的結構描述。 此擴充功能需要來自目標 Log Analytics 工作區的工作區識別碼和工作區金鑰。 在 Azure 入口網站中工作區的設定中可找到這些項目。 由於工作區金鑰應視為敏感性資料，因此應儲存在受保護的設定組態中。 Azure VM 擴充功能保護的設定資料會經過加密，只會在目標虛擬機器上解密。 請注意，**workspaceId** 和 **workspaceKey** 區分大小寫。

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
> 如需其他屬性，請參閱 Azure [Connect Windows 電腦以 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows)。

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節中詳述的 JSON 結構描述可在部署 Azure Resource Manager 範本期間，在 Azure Resource Manager 範本中用來執行 Log Analytics 代理程式擴充功能。 在 [Azure 快速入門資源庫](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm) \(英文\) 上可以找到包含 Log Analytics 代理程式 VM 擴充功能的範例範本。 

>[!NOTE]
>當您想要將代理程式設定為向多個工作區報告時，此範本不支援指定多個工作區識別碼和工作區金鑰。 若要將代理程式設定為向多個工作區報告，請參閱[新增或移除工作區](../../azure-monitor/platform/agent-manage.md#adding-or-removing-a-workspace)。  

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

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。
