---
title: 將受控磁碟的 VHD 匯出/複製到另一個區域的帳戶 (Windows) - PowerShell
description: Azure PowerShell 指令碼範例 - 將受控磁碟的 VHD 匯出/複製到相同或不同區域的儲存體帳戶
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
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: a02b55adf4ac1838e9fcb98b9dffcfbd2b4b52d4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91969877"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell-windows"></a>使用 PowerShell 將受控磁碟的 VHD 匯出/複製到不同區域的儲存體帳戶 (Windows)

此指令碼會將受控磁碟的 VHD 匯出到不同區域的儲存體帳戶。 它會先產生受控磁碟的 SAS URI，然後用它來將基礎 VHD 複製到不同區域的儲存體帳戶。 使用此指令碼將受控磁碟複製到其他區域以進行區域擴充。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來產生受控磁碟的 SAS URI，並使用 SAS URI 將基礎 VHD 複製到儲存體帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Grant-AzDiskAccess](/powershell/module/az.compute/grant-azdiskaccess) | 產生受控磁碟的 SAS URI，用來將基礎 VHD 複製到儲存體帳戶。 |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | 使用帳戶名稱與金鑰建立儲存體帳戶內容。 此內容可用來對儲存體帳戶執行讀取/寫入作業。 |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | 將快照集的底層 VHD 複製到儲存體帳戶 |

## <a name="next-steps"></a>後續步驟

[從 VHD 建立受控磁碟](./virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%252fpowershell%252fmodule%252ftoc.json)

[從受控磁碟建立虛擬機器](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。