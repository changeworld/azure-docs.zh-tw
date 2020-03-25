---
title: 從訂用帳戶中儲存體帳戶的 VHD 檔案建立受控磁碟 - PowerShell 範例
description: Azure PowerShell 指令碼範例 - 從相同或不同訂用帳戶的儲存體帳戶的 VHD 檔案建立受控磁碟
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 3fe13751d75d1b4cf549b4921934922ddccfb211
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75368618"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>使用 PowerShell 從相同或不同訂用帳戶的儲存體帳戶的 VHD 檔案建立受控磁碟

此指令碼會從相同或不同訂用帳戶的儲存體帳戶中的 VHD 檔案，建立受控磁碟。 使用此指令碼可將專用 (非一般化/已執行過 Sysprep) 的 VHD 匯入至受控 OS 磁碟，以建立虛擬機器。 同時，用它將資料 VHD 匯入到受控資料磁碟。 

請勿在短時間內從 VHD 檔案建立多個相同的受控磁碟。 若要從 VHD 檔案建立受控磁碟，會建立 VHD 檔案的 Blob 快照集，然後將它用於建立受控磁碟。 一分鐘內只能建立一個 Blob 快照集，這會導致磁碟建立因節流而失敗。 若要避免進行此節流，請[從 VHD 檔案建立受控快照集](virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)，然後使用受控快照集在短時間內建立多個受控磁碟。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]


## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令從不同訂用帳戶的 VHD 建立受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 建立用於磁碟建立的磁碟組態。 它包含儲存體類型、位置、儲存父 VHD 所在儲存體帳戶的資源識別碼、父 VHD 的 VHD URI。 |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 使用當作參數傳遞的磁碟組態、磁碟名稱和資源群組名稱來建立磁碟。 |

## <a name="next-steps"></a>後續步驟

[將受控磁碟連結為 OS 磁碟以建立虛擬機器](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
