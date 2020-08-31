---
title: Azure Kubernetes Service (AKS) 節點映射升級
description: 瞭解如何升級 AKS 叢集節點和節點集區上的映射。
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 744e62f8a2207cff400a96069fc6ea82866f6e2d
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055680"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service (AKS) 節點映射升級

AKS 支援升級節點上的映射，讓您能夠掌握最新的 OS 和執行時間更新。 AKS 每週提供一個新的映射與最新的更新，因此請定期升級節點的映射以取得最新的功能，包括 Linux 或 Windows 修補程式。 本文說明如何升級 AKS 叢集節點映射，以及如何更新節點集區映射，而不需要升級 Kubernetes 版本。

如果您有興趣瞭解 AKS 提供的最新映射，請參閱 [AKS 版本](https://github.com/Azure/AKS/releases) 資訊以取得詳細資料。

如需升級叢集之 Kubernetes 版本的詳細資訊，請參閱 [升級 AKS][upgrade-cluster]叢集。

## <a name="limitations"></a>限制

* AKS 叢集必須使用節點的虛擬機器擴展集。

## <a name="install-the-aks-cli-extension"></a>安裝 AKS CLI 擴充功能

在下一個核心 CLI 版本發行之前，您需要 *aks-preview* cli 擴充功能才能使用節點映射升級。 使用 [az extension add][az-extension-add] 命令，然後使用 [az extension update][az-extension-update] 命令檢查是否有任何可用的更新：

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

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
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
