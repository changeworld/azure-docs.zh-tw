---
title: 使用 Azure CLI 建立 VHD 的快照集
description: 了解如何在 Azure 中建立 VHD 的複本，以作為備份，或用來針對問題進行疑難排解。
author: roygara
manager: twooley
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/11/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6374108247b9bfb950c42495b13b501ded8a02d2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015956"
---
# <a name="create-a-snapshot-using-the-portal-or-azure-cli"></a>使用入口網站或 Azure CLI 建立快照集

製作作業系統或資料磁碟的快照集作為備份，或是用來針對 VM 問題進行疑難排解。 快照集是完整的 VHD 唯讀複本。 

## <a name="use-azure-cli"></a>使用 Azure CLI 

下列範例會要求您使用 [Cloud Shell](https://shell.azure.com/bash) 或已安裝 Azure CLI。

下列步驟說明如何使用 **az snapshot create** 命令搭配 **--source-disk** 參數製作快照集。 下列範例假設 myResourceGroup 資源群組中有一個名為 myVM 的 VM。

使用 [az vm show](/cli/azure/vm#az-vm-show) 取得磁碟識別碼。

```azurecli-interactive
osDiskId=$(az vm show \
   -g myResourceGroup \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

使用 [az snapshot create](/cli/azure/snapshot#az-snapshot-create) 建立名為 osDisk-backup 的快照集。

```azurecli-interactive
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDisk-backup
```

> [!NOTE]
> 如果您想要將快照集儲存於區域復原的儲存體中，則需要在支援 [可用性區域](../../availability-zones/az-overview.md)且包含 **--sku Standard_ZRS** 參數的區域中建立它。

您可以使用 [az snapshot list](/cli/azure/snapshot#az-snapshot-list) 查看快照集清單。

```azurecli-interactive
az snapshot list \
   -g myResourceGroup \
   - table
```

## <a name="use-azure-portal"></a>使用 Azure 入口網站 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左上方開始，按一下 [建立資源] 並搜尋 **快照集**。 從搜尋結果中選取 [快照集]。
3. 在 [快照集] 刀鋒視窗中，按一下 [建立]。
4. 輸入快照集的 [名稱]。
5. 選取現有的資源群組，或輸入新群組的名稱。 
7. 在 [來源磁碟] 中，選取要建立快照集的受控磁碟。
8. 選取用來儲存快照集的 [帳戶類型]。 除非需要存放在高效能的 SSD 上，否則請使用 [標準 HDD]。
9. 按一下 [建立]。


## <a name="next-steps"></a>後續步驟

 從快照集建立受控磁碟，然後連結新的受控磁碟作為作業系統磁碟，以從快照集建立虛擬機器。 如需詳細資訊，請參閱[從快照集建立 VM](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) 指令碼。

