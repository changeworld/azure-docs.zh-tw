---
title: 升級 Azure Kubernetes Service (AKS) 叢集
description: 了解如何升級 Azure Kubernetes Service (AKS) 叢集
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 9acf08765ee42401229bc464b4513856127e9f66
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77592980"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升級 Azure Kubernetes Service (AKS) 叢集

作為 AKS 叢集生命週期的一部分，您通常需要升級至最新的 Kubernetes 版本。 套用最新的 Kubernetes 安全性版本，或升級以取得最新的功能非常重要。 本文說明如何在 AKS 叢集中升級主要元件或單一預設節點集區。

對於使用多個節點集區或 Windows Server 節點的 AKS 叢集（目前在 AKS 中為預覽狀態），請參閱[升級 AKS 中的節點集][nodepool-upgrade]區。

## <a name="before-you-begin"></a>開始之前

本文會要求您執行 Azure CLI 版本2.0.65 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 叢集升級會觸發 cordon 並清空您的節點。 如果您有可用的計算配額不足，升級可能會失敗。  如需詳細資訊，請參閱[增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request?branch=pr-en-us-83289)。
> 如果您正在執行自己的叢集自動調整程式部署，請在升級期間將它停用（您可以將它調整為零個複本），因為它可能會干擾升級程式。 受控自動調整程式會自動處理這種情況。 

## <a name="check-for-available-aks-cluster-upgrades"></a>檢查可用的 AKS 叢集升級

若要檢查哪些 Kubernetes 版本適用于您的叢集，請使用[az aks get-升級][az-aks-get-upgrades]命令。 下列範例會在名為 *myResourceGroup* 的資源群組中，查看名為 *myAKSCluster* 的叢集的可用升級：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 當您升級 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，允許在*1.12.* x -> *1.13. x*或*1.13.* x -> *1.14*之間進行升級，但*1.12. x* -> *1.14*不是。
>
> 若要升級，請從*1.12. x* -> *1.14*，先從*1.12. x*升級 -> *1.13*. x，然後從*1.13. x*升級 -> *1.14.* x。

下列範例輸出顯示叢集可以升級至版本*1.13.9*和*1.13.10*：

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
如果沒有可用的升級，您會得到：
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>升級 AKS 叢集

如需 AKS 叢集的可用版本清單，請使用[az AKS upgrade][az-aks-upgrade]命令進行升級。 在升級過程中，AKS 會將新節點新增至執行指定之 Kubernetes 版本的叢集，然後小心[cordon 並][kubernetes-drain]清空其中一個舊節點，以最小化執行應用程式的中斷。 當新節點確認為執行中的應用程式 pod 時，就會刪除舊的節點。 此程式會重複執行，直到叢集中的所有節點都已升級為止。

下列範例會將叢集升級至版本*1.13.10*：

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

叢集升級需要幾分鐘的時間，具體取決於您擁有多少節點。 

> [!NOTE]
> 完成叢集升級的允許時間總計。 這次是藉由取得 `10 minutes * total number of nodes in the cluster`的產品來計算。 例如，在20個節點的叢集中，升級作業必須在200分鐘內成功，否則 AKS 將無法執行操作，以避免叢集狀態無法復原。 若要在升級失敗時復原，請在達到超時時間之後重試升級操作。

若要確認升級是否成功，請使用[az aks show][az-aks-show]命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

下列範例輸出顯示叢集現在會執行*1.13.10*：

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>後續步驟

本文說明如何升級現有的 AKS 叢集。 若要深入了解部署和管理 AKS 叢集，請參閱教學課程集合。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
