---
title: 將受控磁碟的快照集複製到訂用帳戶 - CLl 範例
description: Azure CLI 指令碼範例 - 使用 CLI 將受控磁碟快照集複製 (移動) 到相同或不同的訂用帳戶
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: ddcf585af86a265410f9c2d424c85acf5abbc8fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "75375911"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>使用 CLI將受控磁碟快照集複製到相同或不同的訂用帳戶

此指令碼會將受控磁碟的快照集複製到相同或不同的訂用帳戶。 請對下列案例使用此指令碼：

1. 將進階儲存體 (Premium_LRS) 中的快照集遷移到標準儲存體 (Standard_LRS 或 Standard_ZRS)，以降低成本。
1. 將快照集從本地備援儲存體 (Premium_LRS、Standard_LRS) 遷移到區域備援儲存體 (Standard_ZRS)，以受惠於 ZRS 儲存體的高可靠性。
1. 將快照集移至相同區域中的不同訂用帳戶，以便保留更長時間。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令，使用來源快照集的識別碼在目標訂用帳戶中建立快照集。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot) | 使用快照集的名稱和資源群組屬性，取得快照集的所有屬性。 使用 Id 屬性將快照集複製到不同的訂用帳戶。  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot) | 在使用父快照集識別碼和名稱的不同訂用帳戶中建立快照集，以複製快照集。  |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](https://docs.microsoft.com/cli/azure)。

您可以在 [Azure Windows VM 文件](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中找到其他的虛擬機器和受控磁碟 CLI 指令碼範例。
