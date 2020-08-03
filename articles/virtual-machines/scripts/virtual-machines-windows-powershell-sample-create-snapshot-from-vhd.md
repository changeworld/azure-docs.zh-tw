---
title: 建立許多相同受控磁碟的 VHD 快照集 (Windows) - PowerShell
description: Azure PowerShell 指令碼範例 - 從 VHD 建立快照集以在短時間內建立多個相同的受控磁碟
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 0625c968a3c60d38ca2bbe2f13318ccd85d61a61
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082370"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>使用 PowerShell 從 VHD 建立快照集以在短時間內建立多個相同的受控磁碟 (Windows)

此指令碼會從相同或不同訂用帳戶的儲存體帳戶中的 VHD 檔案，建立快照集。 使用此指令碼將特製化 (非一般化/已執行過 sysprep) VHD 匯入到快照集，然後使用快照集在短時間內建立多個相同的受控磁碟。 此外，使用它將資料 VHD 匯入到快照集，然後使用快照集在短時間內建立多個受控磁碟。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>後續步驟

[從快照集建立受控磁碟](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[將受控磁碟連結為 OS 磁碟以建立虛擬機器](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
