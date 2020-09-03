---
title: Azure PowerShell 指令碼範例 - IIS 搭配 DSC
description: Azure PowerShell 指令碼範例 - IIS 搭配 DSC
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 63efacf13070822286d4ed15b90a05135883fbc8
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078141"
---
# <a name="use-an-azure-powershell-sample-script-to-create-an-iis-vm"></a>使用 Azure PowerShell 範例指令碼建立 IIS VM

此指令碼會建立執行 Windows Server 2016 的 Azure 虛擬機器，然後使用 Azure 虛擬機器 DSC 擴充功能來安裝 IIS。 執行指令碼之後，您可以在虛擬機器的公用 IP 位址上存取預設的 IIS 網站。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-dsc/create-windows-vm-iis-dsc.ps1 "Create VM IIS DSC")]

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
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 建立虛擬機器，並將它連線到網路卡、虛擬網路、子網路及網路安全性群組。 此命令也會開啟連接埠 80 並設定系統管理認證。 |
| [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) | 將 VM 擴充功能新增至虛擬機器。 此範例使用 DSC 擴充功能來安裝 IIS。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
