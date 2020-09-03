---
title: Azure PowerShell 指令碼範例 - 建立 Linux VM
description: Azure PowerShell 指令碼範例 - 建立 Linux VM
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 329129417ddf6303d0ae493f640701eeea3e0de2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078817"
---
# <a name="create-a-fully-configured-virtual-machine-with-powershell"></a>使用 PowerShell 建立完整設定的虛擬機器

此指令碼會建立具有 Ubuntu 作業系統的 Azure 虛擬機器。 執行指令碼之後，您可以透過 SSH 存取虛擬機器。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-detailed/create-vm-detailed.ps1 "Create VM detailed")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來建立部署。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 建立用來存放所有資源的資源群組。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 建立子網路組態。 此組態可使用於虛擬網路建立程序。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 建立虛擬網路。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。 |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | 建立網路安全性群組規則組態。 建立 NSG 時，此組態用來建立 NSG 規則。 |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 建立網路安全性群組。 |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 取得子網路資訊。 建立網路介面時會使用此資訊。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 建立網路介面。 |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | 建立 VM 組態。 此組態包括 VM 名稱、作業系統和系統管理認證等資訊。 建立 VM 時會使用此組態。 |
| [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) | 設定虛擬機器的作業系統屬性。 |
| [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) | 指定虛擬機器的映像。 |
| [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) | 將網路介面新增至虛擬機器。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 建立虛擬機器。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Linux VM 文件](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
