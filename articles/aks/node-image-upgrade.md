---
title: Azure Kubernetes Service (AKS) 節點映射升級
description: 瞭解如何升級 AKS 叢集節點和節點集區上的映射。
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: e8214345bd1c328f0996f8aa8a2a8bb402a76e8d
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309591"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service (AKS) 節點映射升級

AKS 支援升級節點上的映射，讓您能夠掌握最新的 OS 和執行時間更新。 AKS 每週提供一個新的映射與最新的更新，因此請定期升級節點的映射以取得最新的功能，包括 Linux 或 Windows 修補程式。 本文說明如何升級 AKS 叢集節點映射，以及如何更新節點集區映射，而不需要升級 Kubernetes 版本。

如需 AKS 所提供最新映射的詳細資訊，請參閱 [AKS 版本](https://github.com/Azure/AKS/releases)資訊。

如需升級叢集之 Kubernetes 版本的詳細資訊，請參閱 [升級 AKS][upgrade-cluster]叢集。

> [!NOTE]
> AKS 叢集必須使用節點的虛擬機器擴展集。

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>檢查您的節點集區是否位於最新節點映射上

您可以使用下列命令來查看節點集區可用的最新節點映射版本： 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

在輸出中，您可以在 `latestNodeImageVersion` 下列範例中看到類似的內容：

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

因此， `nodepool1` 最新的節點映射可供使用 `AKSUbuntu-1604-2020.10.28` 。 您現在可以執行下列動作，將它與節點集區所使用的目前節點映射版本進行比較：

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

輸出範例如下：

```output
"AKSUbuntu-1604-2020.10.08"
```

因此，在此範例中，您可以從目前的 `AKSUbuntu-1604-2020.10.08` 映射版本升級至最新版本 `AKSUbuntu-1604-2020.10.28` 。 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>升級所有節點集區中的所有節點

您可以使用來升級節點映射 `az aks upgrade` 。 若要升級節點映射，請使用下列命令：

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

在升級期間，使用下列命令檢查節點映射的狀態， `kubectl` 以取得標籤並篩選出目前的節點映射資訊：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升級完成時，請使用 `az aks show` 來取得更新的節點集區詳細資料。 目前的節點影像會顯示在 `nodeImageVersion` 屬性中。

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>升級特定的節點集區

升級節點集區上的映射類似于升級叢集上的映射。

若要在不執行 Kubernetes 叢集升級的情況下更新節點集區的 OS 映射，請使用 `--node-image-only` 下列範例中的選項：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

在升級期間，使用下列命令檢查節點映射的狀態， `kubectl` 以取得標籤並篩選出目前的節點映射資訊：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

升級完成時，請使用 `az aks nodepool show` 來取得更新的節點集區詳細資料。 目前的節點影像會顯示在 `nodeImageVersion` 屬性中。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>使用節點浪湧升級節點映射

若要加速節點映射升級程式，您可以使用可自訂的節點激增值來升級節點映射。 根據預設，AKS 會使用一個額外的節點來設定升級。

如果您想要增加升級的速度，請使用此 `--max-surge` 值來設定要用於升級的節點數目，以便更快完成。 若要深入瞭解各項設定的取捨 `--max-surge` ，請參閱 [自訂節點激增升級][max-surge]。

下列命令會設定執行節點映射升級的最大浪湧值：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

在升級期間，使用下列命令檢查節點映射的狀態， `kubectl` 以取得標籤並篩選出目前的節點映射資訊：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

用 `az aks nodepool show` 來取得更新的節點集區詳細資料。 目前的節點影像會顯示在 `nodeImageVersion` 屬性中。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>後續步驟

- 如需最新節點映射的詳細資訊，請參閱 [AKS 版本](https://github.com/Azure/AKS/releases) 資訊。
- 瞭解如何使用 [upgrade a AKS cluster][upgrade-cluster]升級 Kubernetes 版本。
- [將安全性和核心更新套用至 Azure Kubernetes Service (AKS) 中的 Linux 節點 ][security-update]
- 深入瞭解多個節點集區，以及如何使用 [建立和管理多個節點][use-multiple-node-pools]集區來升級節點集區。

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
