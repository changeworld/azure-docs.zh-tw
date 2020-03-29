---
title: 如何使用 Azure CLI 調整 Linux VM 的大小
description: 如何藉由變更 VM 的大小來相應增加或相應減少 Linux 虛擬機器。
author: mikewasson
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 02/10/2017
ms.author: mwasson
ms.openlocfilehash: 20e7db80b55347c4a4a76b7c95d4d8bec368abda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969268"
---
# <a name="resize-a-linux-virtual-machine-using-azure-cli"></a>使用 Azure CLI 調整 Linux 虛擬機器大小 

部屬虛擬機器 (VM) 之後，您可以藉由變更 [VM 大小][vm-sizes]來相應增加或減少 VM。 在某些情況下，您必須先解除配置 VM。 如果無法在裝載 VM 的硬體叢集上取得所需的大小，您可能需要解除配置 VM。 此文章詳述如何使用 Azure CLI 來調整 Linux VM 的大小。 

## <a name="resize-a-vm"></a>調整 VM 的大小
若要調整 VM 的大小，您需要安裝最新的 [Azure CLI](/cli/azure/install-az-cli2)，並使用 [az login](/cli/azure/reference-index) 來登入 Azure 帳戶。

1. 使用 [az vm list-vm-resize-options](/cli/azure/vm) 檢視裝載 VM 之硬體叢集上可用的 VM 大小清單。 下列範例會針對資源群組 `myResourceGroup` 區域中名為 `myVM` 的 VM 列出 VM 大小：
   
    ```azurecli
    az vm list-vm-resize-options --resource-group myResourceGroup --name myVM --output table
    ```

2. 如果已列出所需的大小，請使用 [az vm resize](/cli/azure/vm) 來調整 VM 的大小。 下列範例會將名為 `myVM` 的 VM 大小調整為 `Standard_DS3_v2` 大小：
   
    ```azurecli
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    ```
   
    VM 會在此程序中重新啟動。 重新啟動之後，會重新對應現有的 OS 和資料磁碟。 暫存磁碟的相關資料都會遺失。

3. 如果未列出所需的 VM 大小，您需要先使用 [az vm deallocate](/cli/azure/vm) 解除配置 VM。 此程序可讓 VM 的大小調整為區域支援的任何可用大小，然後啟動。 下列步驟會解除配置、調整大小，然後啟動 `myResourceGroup` 資源群組中名為 `myVM` 的 VM：
   
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm resize --resource-group myResourceGroup --name myVM --size Standard_DS3_v2
    az vm start --resource-group myResourceGroup --name myVM
    ```
   
   > [!WARNING]
   > 將 VM 解除配置也會釋出指派給該 VM 的任何動態 IP 位址。 不會影響作業系統和資料磁碟。

## <a name="next-steps"></a>後續步驟
對於其他可伸縮性，運行多個 VM 實例並橫向擴展。有關詳細資訊，請參閱[虛擬機器縮放集中自動縮放 Linux 電腦][scale-set]。 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]:sizes.md
