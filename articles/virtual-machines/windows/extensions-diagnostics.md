---
title: 適用于 Windows 的 Azure 診斷擴充功能
description: 使用 Azure 診斷延伸模組來監視 Azure Windows VM
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli
ms.openlocfilehash: b657231cb6b896479766654e5bd989468d9b73e7
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501145"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>適用於 Windows VM 的 Azure 診斷延伸模組

## <a name="overview"></a>概觀

「Azure 診斷」VM 延伸模組可讓您從 Windows VM 收集監視資料，例如效能計數器和事件記錄。 您可以細微地指定您想要收集的資料，以及要將資料移至何處，例如 Azure 儲存體帳戶或 Azure 事件中樞。 您也可以在 Azure 入口網站中使用此資料建立圖表，或建立度量警示。

## <a name="prerequisites"></a>Prerequisites

### <a name="operating-system"></a>作業系統

您可以對 Windows 10 Client、Windows Server 2008 R2、2012、2012 R2 和 2016 執行「Azure 診斷延伸模組」。

### <a name="internet-connectivity"></a>網際網路連線

「Azure 診斷延伸模組」會要求目標虛擬機器連線到網際網路。 

## <a name="extension-schema"></a>擴充功能結構描述

[本文件說明 Azure 診斷延伸模組結構描述和屬性值。](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>範本部署

也可以使用 Azure Resource Manager 範本部署 Azure VM 擴充功能。 上一節詳述的 JSON 結構描述可以用於 Azure Resource Manager 範本，以在 Azure Resource Manager 範本部署期間執行「Azure 診斷」延伸模組。 請參閱[使用 Windows VM 和 Azure Resource Manager 範本的監視和診斷](../extensions/diagnostics-template.md)。

## <a name="azure-cli-deployment"></a>Azure CLI 部署

Azure CLI 可以用來將「Azure 診斷」延伸模組部署到現有的虛擬機器。 使用上述擴充功能結構描述中的有效 JSON，取代受保護的設定和設定屬性。 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell 部署

`Set-AzVMDiagnosticsExtension` 命令可用來將「Azure 診斷」延伸模組新增到現有的虛擬機器。 另請參閱[使用 PowerShell 在執行 Windows 的虛擬機器中啟用 Azure 診斷](../extensions/diagnostics-windows.md)。

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>疑難排解與支援

### <a name="troubleshoot"></a>疑難排解

使用 Azure CLI，就可以從 Azure 入口網站擷取有關擴充功能部署狀態的資料。 若要查看指定 VM 的擴充功能部署狀態，請使用 Azure CLI 執行下列命令。

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

如需「Azure 診斷」延伸模組的更詳盡疑難排解指南，請[參閱這篇文章](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md)。

### <a name="support"></a>支援

如果您在本文中有任何需要協助的地方，您可以連絡 [MSDN Azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/forums/)上的 Azure 專家。 或者，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。 如需使用 Azure 支援的資訊，請參閱 [Microsoft Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>後續步驟
* [深入了解 Azure 診斷延伸模組](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [檢閱擴充功能結構描述和版本](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
