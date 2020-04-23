---
title: 將快照集匯出/複製成不同區域的儲存體帳戶當做 VHD - PowerShell 範例
description: Azure PowerShell 指令碼範例 - 將快照集匯出/複製成不同區域的儲存體帳戶當做 VHD
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
ms.openlocfilehash: 760b6ae2d791267053acdb1e216ab5daeab7c061
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459350"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>使用 PowerShell 將受控快照集匯出/複製到不同區域的儲存體帳戶當做 VHD

此指令碼會將受控快照集匯出到不同區域的儲存體帳戶。 它會先產生快照集的 SAS URI，然後用它來將快照集複製到不同區域的儲存體帳戶。 使用此指令碼維護不同區域中受控磁碟的備份，以進行災害復原。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來產生受控快照集的 SAS URI，並使用 SAS URI 將快照集複製到儲存體帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Grant-AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 產生快照集的 SAS URI 並用它來將快照集複製到儲存體帳戶。 |
| [New-AzStorageContext](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontext) | 使用帳戶名稱與金鑰建立儲存體帳戶內容。 此內容可用來對儲存體帳戶執行讀取/寫入作業。 |
| [Start-AzStorageBlobCopy](https://docs.microsoft.com/powershell/module/az.storage/start-azstorageblobcopy) | 將快照集的底層 VHD 複製到儲存體帳戶 |

## <a name="next-steps"></a>後續步驟

[從 VHD 建立受控磁碟](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[從受控磁碟建立虛擬機器](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
