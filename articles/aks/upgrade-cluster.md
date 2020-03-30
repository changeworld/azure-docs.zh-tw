---
title: 升級 Azure Kubernetes Service (AKS) 叢集
description: 了解如何升級 Azure Kubernetes Service (AKS) 叢集
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 4520297e83f96f95b10ecafd5af52a913dc5f450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77621985"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升級 Azure Kubernetes Service (AKS) 叢集

作為 AKS 叢集生命週期的一部分，您通常需要升級至最新的 Kubernetes 版本。 套用最新的 Kubernetes 安全性版本，或升級以取得最新的功能非常重要。 本文介紹如何升級 AKS 群集中的主元件或單個預設節點池。

對於使用多個節點池或 Windows 伺服器節點（當前在 AKS 中預覽）的 AKS 群集，請參閱[在 AKS 中升級節點池][nodepool-upgrade]。

## <a name="before-you-begin"></a>開始之前

本文要求您運行 Azure CLI 版本 2.0.65 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 群集升級會觸發節點的警戒線和耗盡。 如果可用的計算配額較低，則升級可能會失敗。 有關詳細資訊，請參閱[增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。
> 如果您正在運行自己的群集自動縮放器部署，請在升級期間禁用它（您可以將其縮放為零副本），因為它可能會干擾升級過程。 託管自動縮放程式會自動處理此問題。 

## <a name="check-for-available-aks-cluster-upgrades"></a>檢查可用的 AKS 叢集升級

若要檢查哪些 Kubernetes 版本可用於您的叢集，請使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 下列範例會在名為 *myResourceGroup* 的資源群組中，查看名為 *myAKSCluster* 的叢集的可用升級：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 當您升級 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，允許在*1.12.x* -> *1.13.x*或*1.13.x* -> *1.14.x*之間進行升級，但不允許升級*1.12.x* -> *1.14.x。*
>
> 要升級，從*1.12.x* -> *1.14.x*，首先從*1.12.x* -> *1.13.x*升級，然後從*1.13.x* -> *1.14.x*升級。

以下示例輸出顯示群集可以升級到版本*1.13.9*和*1.13.10*：

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
如果沒有可用的升級，您將獲得：
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>升級 AKS 叢集

透過適用於您的 AKS 叢集的可用版本清單，使用 [az aks upgrade][az-aks-upgrade] 命令進行升級。 在升級過程中，AKS 向運行指定 Kubernetes 版本的群集添加新節點，然後仔細[設置和排空][kubernetes-drain]其中一個舊節點，以儘量減少對正在運行的應用程式的中斷。 當新節點確認為正在運行的應用程式窗格時，舊節點將被刪除。 此過程重複，直到群集中的所有節點都升級。

下面的示例將群集升級到版本*1.13.10*：

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

叢集升級需要幾分鐘的時間，具體取決於您擁有多少節點。 

> [!NOTE]
> 群集升級的總允許時間可完成。 此時間是通過獲取 的產品計算的`10 minutes * total number of nodes in the cluster`。 例如，在 20 個節點群集中，升級操作必須在 200 分鐘內成功，否則 AKS 將無法執行該操作以避免無法恢復的群集狀態。 要在升級失敗時恢復，請重試超時命中後升級操作。

若要確認升級是否成功，請使用 [az aks show][az-aks-show] 命令：

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

以下示例輸出顯示群集現在運行*1.13.10*：

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
