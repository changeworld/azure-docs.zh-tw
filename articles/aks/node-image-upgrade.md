---
title: 升級 Azure Kubernetes Service (AKS) 節點映射
description: 瞭解如何升級 AKS 叢集節點和節點集區上的映射。
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 040f4378e01c3696b9a74bfcc27230503828f19a
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562782"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>預覽-Azure Kubernetes Service (AKS) 節點映射升級

AKS 支援升級節點上的映射，因此您可以使用最新的 OS 和執行時間更新。 AKS 以最新的更新提供每週新的映射，因此最好定期升級節點的映射，以取得最新功能，包括 Linux 或 Windows 修補程式。 本文說明如何升級 AKS 叢集節點映射，以及如何更新節點集區映射，而不需要升級 Kubernetes 版本。

如果您想要瞭解 AKS 所提供的最新映射，請參閱[AKS 版本](https://github.com/Azure/AKS/releases)資訊，以取得詳細資料。

如需升級叢集之 Kubernetes 版本的詳細資訊，請參閱[Upgrade a AKS cluster][upgrade-cluster]。

## <a name="register-the-node-image-upgrade-preview-feature"></a>註冊節點映射升級預覽功能

若要在預覽期間使用「節點映射升級」功能，您必須註冊該功能。

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

註冊需要幾分鐘的時間才能完成。 使用下列命令來確認已註冊功能：

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

在預覽期間，您需要*aks-preview* CLI 擴充功能，才能使用節點映射升級。 使用[az extension add][az-extension-add]命令，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

## <a name="upgrade-all-nodes-in-all-node-pools"></a>升級所有節點集區中的所有節點

升級節點映射是使用完成 `az aks upgrade` 。 若要升級節點映射，請使用下列命令：

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

在升級期間，使用下列命令來檢查節點映射的狀態， `kubectl` 以取得標籤並篩選出目前的節點影像資訊：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

當升級完成時，請使用 `az aks show` 來取得更新的節點集區詳細資料。 目前的節點映射會顯示在 `nodeImageVersion` 屬性中。

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>升級特定的節點集區

升級節點集區上的映射類似于升級叢集上的映射。

若要更新節點集區的 OS 映射而不執行 Kubernetes 叢集升級，請使用 `--node-image-only` 下列範例中的選項：

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

在升級期間，使用下列命令來檢查節點映射的狀態， `kubectl` 以取得標籤並篩選出目前的節點影像資訊：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

當升級完成時，請使用 `az aks nodepool show` 來取得更新的節點集區詳細資料。 目前的節點映射會顯示在 `nodeImageVersion` 屬性中。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>升級具有節點浪湧的節點映射

若要加速節點映射升級程式，您可以使用可自訂的節點激增值來升級節點映射。 根據預設，AKS 會使用一個額外的節點來設定升級。

如果您想要增加升級的速度，請使用 `--max-surge` 值來設定要用於升級的節點數目，讓它們的完成速度更快。 若要深入瞭解各種設定的取捨，請 `--max-surge` 參閱[自訂節點激增升級][max-surge]。

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

在升級期間，使用下列命令來檢查節點映射的狀態， `kubectl` 以取得標籤並篩選出目前的節點影像資訊：

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

使用 `az aks nodepool show` 來取得更新的節點集區詳細資料。 目前的節點映射會顯示在 `nodeImageVersion` 屬性中。

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>後續步驟

- 如需最新節點映射的詳細資訊，請參閱[AKS 版本](https://github.com/Azure/AKS/releases)資訊。
- 瞭解如何升級 Kubernetes 版本與[升級 AKS][upgrade-cluster]叢集。
- [將安全性和核心更新套用至 Azure Kubernetes Service (AKS 中的 Linux 節點) ][security-update]
- 深入瞭解多個節點集區，以及如何使用[建立及管理多個節點][use-multiple-node-pools]集區來升級節點集區。

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
