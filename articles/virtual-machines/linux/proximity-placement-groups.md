---
title: 使用鄰近放置群組
description: 瞭解如何在 Azure 中創建和使用虛擬機器的鄰近放置群組。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: f89b28e7a3c29e45efa2796788e27325c01d7098
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759244"
---
# <a name="deploy-vms-to-proximity-placement-groups-using-azure-cli"></a>使用 Azure CLI 將 VM 部署到鄰近放置群組

要盡可能接近 VM,實現盡可能低的延遲,應將它們部署在[接近放置群組中](co-location.md#proximity-placement-groups)。

鄰近放置組是一種邏輯分組,用於確保 Azure 計算資源物理上彼此靠近。 接近放置組對於需要低延遲的工作負載非常有用。


## <a name="create-the-proximity-placement-group"></a>建立鄰近放置群組
使用[`az ppg create`](/cli/azure/ppg#az-ppg-create)創建接近放置組。 

```azurecli-interactive
az group create --name myPPGGroup --location westus
az ppg create \
   -n myPPG \
   -g myPPGGroup \
   -l westus \
   -t standard 
```

## <a name="list-proximity-placement-groups"></a>列出鄰近放置群組

您可以使用[az ppg 清單](/cli/azure/ppg#az-ppg-list)列出所有鄰近放置群組。

```azurecli-interactive
az ppg list -o table
```

## <a name="create-a-vm"></a>建立 VM

使用新的 az vm 在接近放置群組中創建[VM。](/cli/azure/vm#az-vm-create)

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

您可以使用[az ppg 顯示](/cli/azure/ppg#az-ppg-show)在接近放置群組中看到 VM。

```azurecli-interactive
az ppg show --name myppg --resource-group myppggroup --query "virtualMachines"
```

## <a name="availability-sets"></a>可用性設定組 (Availability Sets)
您還可以在鄰近放置組中創建可用性集。 使用同一`--ppg`參數創建[az vm 可用性集](/cli/azure/vm/availability-set#az-vm-availability-set-create)以建立可用性集,並且可用性集中的所有 VM 也將在同一接近放置組中創建。

## <a name="scale-sets"></a>擴展集

您還可以在鄰近放置組中創建比例集。 使用同一`--ppg`參數創建[az vmss](/cli/azure/vmss?view=azure-cli-latest#az-vmss-create)以建立比例集,並將在同一鄰近放置組中創建所有實例。

## <a name="next-steps"></a>後續步驟

詳細瞭解接近放置組的[Azure CLI](/cli/azure/ppg)命令。
