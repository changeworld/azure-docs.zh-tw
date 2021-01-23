---
title: 使用鄰近放置群組
description: 瞭解如何在 Azure 中建立和使用虛擬機器的鄰近放置群組。
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: ea50d0fdc5a772cae449951901af36eed4e93771
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737620"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>使用 Azure CLI 將 VM 部署至鄰近放置群組

若要盡可能關閉 Vm，請達到最低可能的延遲，您應該將它們部署在 [鄰近放置群組](co-location.md#proximity-placement-groups)內。

鄰近放置群組是邏輯群組，可用來確保 Azure 計算資源實際位於彼此接近的位置。 鄰近放置群組很適合用於具備低延遲需求的工作負載。


## <a name="create-the-proximity-placement-group"></a>建立鄰近位置群組
使用建立鄰近放置群組 [`az ppg create`](/cli/azure/ppg#az-ppg-create) 。 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>列出鄰近放置群組

您可以使用 [az ppg list](/cli/azure/ppg#az-ppg-list)列出所有鄰近放置群組。

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>建立 VM

使用 [新的 az VM](/cli/azure/vm#az-vm-create)在鄰近放置群組中建立 VM。

```azurecli-interactive
az vm create \
   -n myVM \
   -g myPPGGroup \
   --image UbuntuLTS \
   --ppg myPPG  \
   --generate-ssh-keys \
   --size Standard_D1_v2  \
   -l westus
```

您可以使用 [az ppg show](/cli/azure/ppg#az-ppg-show)在鄰近放置群組中看到 VM。

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>可用性設定組 (Availability Sets)
您也可以在鄰近放置群組中建立可用性設定組。 使用相同的 `--ppg` 參數搭配 [az vm availability-set create](/cli/azure/vm/availability-set#az-vm-availability-set-create) 來建立可用性設定組，同時也會在相同的鄰近放置群組中建立可用性設定組中的所有 vm。

## <a name="scale-sets"></a>擴展集

您也可以在鄰近放置群組中建立擴展集。 使用 `--ppg` 與 [az vmss create](/cli/azure/vmss#az_vmss_create) 相同的參數來建立擴展集，並在相同的鄰近放置群組中建立所有的實例。

## <a name="next-steps"></a>後續步驟

深入瞭解鄰近放置群組的 [Azure CLI](/cli/azure/ppg) 命令。
