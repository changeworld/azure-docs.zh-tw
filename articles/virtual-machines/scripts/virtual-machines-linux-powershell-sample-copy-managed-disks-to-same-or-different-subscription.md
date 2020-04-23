---
title: 將受控磁碟複製到訂用帳戶 - PowerShell 範例
description: Azure PowerShell 指令碼範例 - 將受控磁碟複製 (或移動) 到相同或不同的訂用帳戶
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
ms.date: 06/06/2017
ms.author: ramankum
ms.openlocfilehash: 35890d7b19451aa8051618871c9ca485aabca2c9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458517"
---
# <a name="copy-managed-disks-in-the-same-subscription-or-different-subscription-with-powershell"></a>使用 PowerShell 複製相同訂用帳戶或不同訂用帳戶中的受控磁碟

此指令碼會在相同訂用帳戶或不同訂用帳戶中建立現有受控磁碟的複本。 新磁碟會建立在與父代受控磁碟相同的區域中。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>範例指令碼

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.ps1 "Copy managed disk")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，在使用來源受控磁碟識別碼的目標訂用帳戶中，建立新的受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | 建立用於磁碟建立的磁碟組態。 其中包含父代磁碟的資源識別碼以及與父代磁碟位置相同的位置。  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | 使用當作參數傳遞的磁碟組態、磁碟名稱和資源群組名稱來建立磁碟。 |

## <a name="next-steps"></a>後續步驟

如需有關 Azure PowerShell 模組的詳細資訊，請參閱 [Azure PowerShell 文件](/powershell/azure/overview)。

您可以在 [Azure Linux VM 文件](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 PowerShell 指令碼範例。