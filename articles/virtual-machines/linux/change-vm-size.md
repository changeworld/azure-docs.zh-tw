---
title: 如何使用 Azure CLI 來調整 Linux VM 的大小
description: 如何藉由變更 VM 的大小來相應增加或相應減少 Linux 虛擬機器。
author: DavidCBerry13
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 02/10/2017
ms.author: daberry
ms.openlocfilehash: a93b1dcfe803fdead0d741553c45894f1675372b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372258"
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

## <a name="next-steps"></a>接下來的步驟
如需更多的擴充性，請執行多個 VM 實例和相應放大。如需詳細資訊，請參閱 [在虛擬機器擴展集中自動調整 Linux 機器][scale-set]。 

<!-- links -->
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../../virtual-machine-scale-sets/tutorial-autoscale-cli.md 
[vm-sizes]:sizes.md
