---
title: 調整 Azure Kubernetes Service (AKS) 叢集
description: 了解如何在 Azure Kubernetes Service (AKS) 叢集中調整節點數目。
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79368412"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>在 Azure Kubernetes Service (AKS) 叢集中調整節點計數

如果資源需要應用程式變更，您可以手動調整 AKS 叢集，以執行不同數目的節點。 縮減規模時，會將節點仔細地[隔離並清空][kubernetes-drain] \(英文\)，以將對執行中應用程式造成的中斷情況降到最低。 當您相應增加時，AKS 會等待 Kubernetes 叢集`Ready`標記節點，然後才在其上排程 pod。

## <a name="scale-the-cluster-nodes"></a>調整叢集節點

首先，使用[az aks show][az-aks-show]命令來取得節點集區的*名稱*。 下列範例會取得*myResourceGroup*資源群組中名為*myAKSCluster*之叢集的節點集區名稱：

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

使用[az aks scale][az-aks-scale]命令來調整叢集節點。 下列範例會將名為 *myAKSCluster* 的叢集調整成單一節點。 提供來自前一個命令的您自身 *--nodepool-name*，例如 *nodepool1*：

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

在本文中，您會以手動方式調整 AKS 叢集，以增加或減少節點數目。 您也可以使用 [叢集[自動調整程式][cluster-autoscaler]] 自動調整您的叢集。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
