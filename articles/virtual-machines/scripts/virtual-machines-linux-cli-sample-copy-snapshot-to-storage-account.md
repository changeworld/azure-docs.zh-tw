---
title: 將快照集複製到另一個區域中的儲存體帳戶 - CLI 範例
description: Azure CLI 指令碼範例 - 將快照集匯出/複製成相同或不同區域中儲存體帳戶的 VHD。
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: 42a516f3c52d7548d08f72687e9f233620a39287
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458608"
---
# <a name="exportcopy-a-snapshot-to-a-storage-account-in-different-region-with-cli"></a>使用 CLI 將快照集匯出/複製到不同區域中的儲存體帳戶

此指令碼會將受控快照集匯出到不同區域的儲存體帳戶。 它會先產生快照集的 SAS URI，然後用它來將快照集複製到不同區域的儲存體帳戶。 使用此指令碼維護不同區域中受控磁碟的備份，以進行災害復原。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來產生受控快照集的 SAS URI，並使用 SAS URI 將快照集複製到儲存體帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot) | 產生唯讀 SAS，用來將基礎 VHD 檔案複製到儲存體帳戶，或將它下載到內部部署。  |
| [az storage blob copy start](https://docs.microsoft.com/cli/azure/storage/blob/copy) | 以非同步方式在儲存體帳戶間複製 blob |

## <a name="next-steps"></a>後續步驟

[從 VHD 建立受控磁碟](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[從受控磁碟建立虛擬機器](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Linux VM 文件](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器和受控磁碟 CLI 指令碼範例。
