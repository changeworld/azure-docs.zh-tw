---
title: 調整 Azure Kubernetes Service (AKS) 叢集
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中調整節點數目。
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: d5686a74ffe138af51d2319c839a3a5c5887f992
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902939"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes Service (AKS) 叢集中調整節點計數

如果資源需要應用程式變更，您可以手動調整 AKS 叢集，以執行不同數目的節點。 縮減規模時，會將節點仔細地[隔離並清空][kubernetes-drain] \(英文\)，以將對執行中應用程式造成的中斷情況降到最低。 當您相應增加時，AKS 會等到節點標示為 Kubernetes 叢集， `Ready` 然後再對其進行排程。

## <a name="scale-the-cluster-nodes"></a>調整叢集節點

首先，使用[az aks show][az-aks-show]命令取得節點集區的*名稱*。 下列範例會取得*myResourceGroup*資源群組中名為*myAKSCluster*之叢集的節點集區名稱：

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

使用 [az aks scale][az-aks-scale] 命令來調整叢集節點。 下列範例會將名為 *myAKSCluster* 的叢集調整成單一節點。 提供您自己 `--nodepool-name` 的先前命令，例如 *nodepool1*：

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


## <a name="scale-user-node-pools-to-0"></a>將節點集區調整 `User` 為0

不同于 `System` 一律需要執行節點的節點集區，節點集區可 `User` 讓您調整為0。 若要深入瞭解系統和使用者節點集區之間的差異，請參閱 [系統和使用者節點](use-system-pools.md)集區。

若要將使用者集區擴充至0，您可以使用上述命令替代的 [az aks nodepool scale][az-aks-nodepool-scale] `az aks scale` ，並將0設為您的節點計數。


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

您也可以 `User` 將叢集自動調整程式的參數設定為0，將節點集區自動調整為0個節點 `--min-count` 。 [Cluster Autoscaler](cluster-autoscaler.md)

## <a name="next-steps"></a>下一步

在本文中，您會手動調整 AKS 叢集，以增加或減少節點數目。 您也可以使用叢集 [自動調整程式][cluster-autoscaler] 來自動調整您的叢集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale&preserve-view=true