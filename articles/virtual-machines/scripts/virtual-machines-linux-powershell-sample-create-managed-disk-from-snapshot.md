---
title: 從快照集建立受控磁碟 (Linux) - PowerShell 範例
description: Azure PowerShell 指令碼範例 - 從快照集建立受控磁碟
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 844bff9a363fb9734159e892006dd61440132a34
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079956"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-linux"></a>使用 PowerShell 從快照集建立受控磁碟 (Linux)

此指令碼會從快照集建立受控磁碟。 使用它從 OS 和資料磁碟的快照集還原虛擬機器。 從個別的快照集建立 OS 和資料受控磁碟，再連結受控磁碟以建立新的虛擬機器。 您也可以連結從快照集建立的資料磁碟，以還原現有 VM 的資料磁碟。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令從快照集建立受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | 取得快照集屬性。  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | 建立用於磁碟建立的磁碟組態。 其中包含父代快照集的資源識別碼、與父代快照集位置相同的位置和儲存體類型。  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | 使用當作參數傳遞的磁碟組態、磁碟名稱和資源群組名稱來建立磁碟。 |

## <a name="next-steps"></a>後續步驟


[從受控磁碟建立虛擬機器](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Linux VM 文件](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
