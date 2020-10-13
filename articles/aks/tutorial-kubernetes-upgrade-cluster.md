---
title: Azure 上的 Kubernetes 教學課程 - 升級叢集
description: 在本 Azure Kubernetes Service (AKS) 教學課程中，您將了解如何將現有的 AKS 叢集升級至最新的可用 Kubernetes 版本。
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: c41f6dbd3b85125ef290539040819ffa1833ef6f
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91629637"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>教學課程：在 Azure Kubernetes Service (AKS) 中升級 Kubernetes

依循應用程式和叢集的生命週期，您可能會想要升級至 Kubernetes 的最新可用版本，並使用新功能。 使用 Azure CLI 可以升級 Azure Kubernetes Service (AKS) 叢集。

在本教學課程 (七個章節的第七部分) 中升級了 Kubernetes 叢集。 您會了解如何：

> [!div class="checklist"]
> * 識別目前和可用的 Kubernetes 版本
> * 升級 Kubernetes 節點
> * 驗證升級成功

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝為容器映像。 此映像已上傳至 Azure Container Registry，而您已建立 AKS 叢集。 接著已將應用程式部署至 AKS 叢集。 如果您尚未完成這些步驟，而且想要跟著做，請從[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]開始。

在本教學課程中，您必須執行 Azure CLI 2.0.53 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="get-available-cluster-versions"></a>取得可用的叢集版本

在升級叢集之前，請使用 [az aks get-upgrades][] 命令檢查哪些 Kubernetes 版本可進行升級：

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

在下列範例中，目前版本是 1.15.11**，可用版本則顯示在 [升級]** 之下。

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.15.11",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.8"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.9"
      }
    ]
  },
  ...
}
```

## <a name="upgrade-a-cluster"></a>升級叢集

為了將中斷執行中應用程式的情況降到最低，系統會仔細地隔離並清空 AKS 節點。 在此流程中，會執行下列步驟：

1. Kubernetes 排程器會防止在要升級的節點上排程其他 Pod。
1. 節點上的執行中 Pod 會排程於叢集中的其他節點上。
1. 系統會建立一個節點來執行最新的 Kubernetes 元件。
1. 當新節點準備好並加入叢集時，Kubernetes 排程器會開始在其上執行 Pod。
1. 舊節點會遭到刪除，而叢集中的下一個節點會開始隔離和清空流程。

使用 [az aks upgrade][] 命令升級 AKS 叢集。

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> 您一次只能升級一個次要版本。 例如，您可以從 1.14.x** 升級至 1.15.x**，但無法直接從 1.14.x** 升級至 1.16.x**。 若要從 1.14.x** 升級至 1.16.x**，必須先從 1.14.x** 升級至 1.15.x**，然後再執行從 1.15.x** 到 1.16.x** 的升級。

下列扼要的範例輸出顯示升級至 *1.16.8*的結果。 請注意，*kubernetesVersion* 現在回報 *1.16.8*：

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.16.8",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>驗證升級

使用 [az aks show][] 命令確認升級是否成功，如下所示：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

下列範例輸出顯示 AKS 叢集執行的是 KubernetesVersion 1.16.8**：

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.16.8               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>選取叢集

由於本教學課程是整個系列的最後一個部分，因此，您可以刪除 AKS 叢集。 Kubernetes 節點會在 Azure 虛擬機器 (VM) 上執行，所以即使您不使用叢集，這些節點仍會繼續產生費用。 請使用 [az group delete][az-group-delete] 命令來移除資源群組、容器服務以及所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除][sp-delete]。 如果您使用受控識別，則身分識別會由平台負責管理，因此您不需要佈建或輪替任何密碼。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 AKS 叢集中升級 Kubernetes。 您已了解如何︰

> [!div class="checklist"]
> * 識別目前和可用的 Kubernetes 版本
> * 升級 Kubernetes 節點
> * 驗證升級成功

如需 AKS 的詳細資訊，請參閱 [AKS 概觀][aks-intro]。 如需使用 AKS 建立完整解決方案的指導，請參閱 [AKS 解決方案指導][aks-solution-guidance]。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[aks-solution-guidance]: /azure/architecture/reference-architectures/containers/aks-start-here
