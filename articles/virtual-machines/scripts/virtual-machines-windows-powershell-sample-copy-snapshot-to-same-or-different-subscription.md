---
title: 訂用帳戶受控磁碟的快照集 (Windows) - PowerShell
description: Azure PowerShell 指令碼範例 - 將受控磁碟快照集複製 (移動) 到相同或不同的訂用帳戶
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
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: b5ac47c8074a3a7bec86a35075a98a141ea0ccd1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082421"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>使用 PowerShell 複製相同訂用帳戶或不同訂用帳戶中的受控磁碟快照集 (Windows)

此指令碼會將受控磁碟的快照集複製到相同或不同的訂用帳戶。 請對下列案例使用此指令碼：

1. 將進階儲存體 (Premium_LRS) 中的快照集遷移到標準儲存體 (Standard_LRS 或 Standard_ZRS)，以降低成本。
1. 將快照集從本地備援儲存體 (Premium_LRS、Standard_LRS) 遷移到區域備援儲存體 (Standard_ZRS)，以受惠於 ZRS 儲存體的高可靠性。
1. 將快照集移至相同區域中的不同訂用帳戶，以便保留更長時間。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，使用來源快照集的識別碼在目標訂用帳戶中建立快照集。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | 建立用於建立快照集的快照集組態。 其中包含父代快照集的資源識別碼以及與父代快照集位置相同的位置。  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | 使用當作參數傳遞的快照集組態、快照集名稱和資源群組名稱來建立快照集。 |

## <a name="next-steps"></a>後續步驟

[從快照集建立虛擬機器](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
