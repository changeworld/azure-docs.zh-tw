---
title: 調整 Azure Kubernetes Service (AKS) 叢集
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中調整節點數目。
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368412"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes Service (AKS) 叢集中調整節點計數

如果資源需要應用程式變更，您可以手動調整 AKS 叢集，以執行不同數目的節點。 縮減規模時，會將節點仔細地[隔離並清空][kubernetes-drain] \(英文\)，以將對執行中應用程式造成的中斷情況降到最低。 向上擴展時，AKS 會等待節點由 Kubernetes 群集標記`Ready`，然後再將 pod 安排在它們上。

## <a name="scale-the-cluster-nodes"></a>調整叢集節點

首先，使用[az aks show][az-aks-show]命令獲取節點池*的名稱*。 以下示例獲取*myResourceGroup*資源組中名為*myAKSCluster*的群集的節點池名稱：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

下列範例輸出顯示 *name* 是 *nodepool1*：

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

使用[az aks 縮放][az-aks-scale]命令縮放叢集節點。 下列範例會將名為 *myAKSCluster* 的叢集調整成單一節點。 提供來自前一個命令的您自身 *--nodepool-name*，例如 *nodepool1*：

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

下列範例輸出顯示叢集已成功調整為一個節點，如 *agentPoolProfiles* 區段中所示：

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>後續步驟

在本文中，您可以手動縮放 AKS 群集以增加或減少節點數。 您還可以使用[群集自動縮放器][cluster-autoscaler]自動縮放群集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
