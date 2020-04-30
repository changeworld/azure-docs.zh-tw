---
title: 從 VHD 建立快照集以建立多個相同的受控磁碟 - PowerShell 範例
description: Azure PowerShell 指令碼範例 - 從 VHD 建立快照集以在短時間內建立多個相同的受控磁碟
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
ms.openlocfilehash: b163b5a1aad343382b9b2d22a91c1b07078c14df
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459707"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>使用 PowerShell 從 VHD 建立快照集以在短時間內建立多個相同的受控磁碟

此指令碼會從相同或不同訂用帳戶的儲存體帳戶中的 VHD 檔案，建立快照集。 使用此指令碼將特製化 (非一般化/已執行過 sysprep) VHD 匯入到快照集，然後使用快照集在短時間內建立多個相同的受控磁碟。 此外，使用它將資料 VHD 匯入到快照集，然後使用快照集在短時間內建立多個受控磁碟。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令從不同訂用帳戶的 VHD 建立受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 建立用於磁碟建立的磁碟組態。 其中包含儲存體類型、位置、儲存父代 VHD 之儲存體帳戶的資源識別碼，以及父代 VHD 的 VHD URI。 |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 使用當作參數傳遞的磁碟組態、磁碟名稱和資源群組名稱來建立磁碟。 |

## <a name="next-steps"></a>後續步驟

[從快照集建立受控磁碟](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure Linux VM 文件](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
