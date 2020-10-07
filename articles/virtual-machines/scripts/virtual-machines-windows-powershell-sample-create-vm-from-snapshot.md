---
title: 從快照集建立 VM (Windows) - PowerShell 範例
description: Azure PowerShell 指令碼範例 - 使用 Windows 範例從快照集建立 VM。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: be8af12d1154216386737d653b231a81868eb4ed
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320110"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-windows"></a>使用 PowerShell 從快照集建立虛擬機器 (Windows)

此指令碼會從 OS 磁碟的快照集建立虛擬機器。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>清除部署 

執行下列命令來移除資源群組、VM 和所有相關資源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來取得快照集屬性、從快照集建立受控磁碟，以及建立 VM。 下表中的每個項目都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | 使用快照集名稱來取得快照集。 |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | 建立磁碟組態。 此組態可使用於磁碟建立程序。 |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | 建立受控磁碟。 |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | 建立 VM 組態。 此組態包括 VM 名稱、作業系統和系統管理認證等資訊。 建立 VM 時會使用此組態。 |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | 將受控磁碟當作 OS 磁碟連結至虛擬機器 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 建立公用 IP 位址。 |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 建立網路介面。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 建立虛擬機器。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 移除資源群組及其內含的所有資源。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
