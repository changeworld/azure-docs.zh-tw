---
title: Azure CLI 指令碼範例 - 掛接作業系統磁碟
description: Azure CLI 指令碼範例 - 掛接作業系統磁碟
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 735b16ad054edfea7cf0ad2e1e5b9c76b664e6c3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479609"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>針對 VM 作業系統磁碟進行疑難排解

此指令碼會將已失敗或有問題之虛擬機器的作業系統磁碟當做資料磁碟，掛接到第二部虛擬機器。 在進行磁碟問題或復原資料的疑難排解時，這非常有用。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>指令碼說明

此指令碼使用下列命令來建立資源群組、虛擬機器和所有相關資源。 下表中的每個命令都會連結至命令特定的文件。

| Command | 注意 |
|---|---|
| [az vm show](/cli/azure/vm) | 傳回虛擬機器清單。 此案例使用查詢選項來傳回虛擬機器的作業系統磁碟。 接著將此值新增至 'uri' 變數名稱。 |
| [az vm delete](/cli/azure/vm) | 刪除虛擬機器。 |
| [az vm create](/cli/azure/vm) | 建立虛擬機器。  |
| [az vm disk attach](/cli/azure/vm/disk) | 將磁碟連接至虛擬機器。 |
| [az vm list-ip-addresses](/cli/azure/vm) | 傳回虛擬機器的 IP 位址。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。

您可以在 [Azure Linux VM 文件](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中找到其他的虛擬機器 CLI 指令碼範例。
