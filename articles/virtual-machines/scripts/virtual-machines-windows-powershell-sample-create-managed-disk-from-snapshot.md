---
title: 從快照集建立受控磁碟 - PowerShell 範例
description: Azure PowerShell 指令碼範例 - 從快照集建立受控磁碟
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
ms.openlocfilehash: 5758e31fc24d366479cbebb139f1a8376330b397
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81459316"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>使用 PowerShell 從快照集建立受控磁碟

此指令碼會從快照集建立受控磁碟。 使用它從 OS 和資料磁碟的快照集還原虛擬機器。 從個別的快照集建立 OS 和資料受控磁碟，再連結受控磁碟以建立新的虛擬機器。 您也可以連結從快照集建立的資料磁碟，以還原現有 VM 的資料磁碟。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令從快照集建立受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | 取得快照集屬性。  |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 建立用於磁碟建立的磁碟組態。 其中包含父代快照集的資源識別碼、與父代快照集位置相同的位置和儲存體類型。  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 使用當作參數傳遞的磁碟組態、磁碟名稱和資源群組名稱來建立磁碟。 |


## <a name="next-steps"></a>後續步驟

[從受控磁碟建立虛擬機器](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure Windows VM 文件](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。
