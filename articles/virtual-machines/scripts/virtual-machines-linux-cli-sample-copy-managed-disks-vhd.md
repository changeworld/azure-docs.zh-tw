---
title: 將受控磁碟複製到儲存體帳戶 - Linux CLI 範例
description: Azure CLI 範例 - 將受控磁碟匯出或複製到儲存體帳戶。
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
ms.date: 05/09/2019
ms.author: ramankum
ms.custom: mvc,seodec18
ms.openlocfilehash: e0939e452627cc9f624f7d96da1059b539fa5546
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509829"
---
# <a name="exportcopy-a-managed-disk-to-a-storage-account-using-the-azure-cli"></a>使用 Azure CLI 將受控磁碟匯出/複製到儲存體帳戶

此指令碼會將受控磁碟的基礎 VHD 匯出到相同或不同區域的儲存體帳戶。 它會先產生受控磁碟的 SAS URI，然後用它來將 VHD 複製到儲存體帳戶。 使用此指令碼將受控磁碟複製到其他區域以進行區域擴充。 如果想要在 Azure Marketplace 中發行受控磁碟的 VHD 檔案，可以使用此指令碼將 VHD 檔案複製到儲存體帳戶，然後產生所複製 VHD 的 SAS URI，以在 Marketplace 中發佈它。   


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.sh "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令來產生受控磁碟的 SAS URI，並使用 SAS URI 將基礎 VHD 複製到儲存體帳戶。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az disk grant-access](/cli/azure/disk?view=azure-cli-latest#az-disk-grant-access) | 產生唯讀 SAS，用來將基礎 VHD 檔案複製到儲存體帳戶，或將它下載到內部部署  |
| [az storage blob copy start](/cli/azure/storage/blob/copy) | 以非同步方式在儲存體帳戶間複製 blob |

## <a name="next-steps"></a>後續步驟

[從 VHD 建立受控磁碟](virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[從受控磁碟建立虛擬機器](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Linux VM 文件](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器和受控磁碟 CLI 指令碼範例。
