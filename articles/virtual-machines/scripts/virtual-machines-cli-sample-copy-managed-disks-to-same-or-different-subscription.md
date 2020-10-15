---
title: 將受控磁碟複製到訂用帳戶 - CLI 範例
description: Azure CLI 指令碼範例 - 將受控磁碟複製 (或移動) 到相同或不同的訂用帳戶
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ed593b37de556d5b62cfec6b726322d12f4d8104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89051796"
---
# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>使用 CLI將受控磁碟複製到相同或不同的訂用帳戶

此指令碼會將受控磁碟複製到相同的訂用帳戶，或同區域的不同訂用帳戶。 此複本僅在訂用帳戶屬於相同 Azure AD 租用戶時適用。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，在使用來源受控磁碟識別碼的目標訂用帳戶中，建立新的受控磁碟。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az disk show](/cli/azure/disk) | 使用受控磁碟的名稱和資源群組屬性，取得受控磁碟的所有屬性。 使用 Id 屬性將受控磁碟複製到不同的訂用帳戶。  |
| [az disk create](/cli/azure/disk) | 使用父受控磁碟的識別碼和名稱，在不同的訂閱中建立一個新的受控磁碟管，來複製受控磁碟。  |

## <a name="next-steps"></a>後續步驟

[從受控磁碟建立虛擬機器](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Linux VM 文件](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器和受控磁碟 CLI 指令碼範例。
