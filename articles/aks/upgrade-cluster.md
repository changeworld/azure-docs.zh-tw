---
title: 升級 Azure Kubernetes Service (AKS) 叢集
description: 瞭解如何升級 Azure Kubernetes Service （AKS）叢集，以取得最新的功能和安全性更新。
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: ea9f0154c221fe99d683cc58d5f6dccfce8d948c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800489"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>升級 Azure Kubernetes Service (AKS) 叢集

作為 AKS 叢集生命週期的一部分，您通常需要升級至最新的 Kubernetes 版本。 套用最新的 Kubernetes 安全性版本，或升級以取得最新的功能非常重要。 本文說明如何在 AKS 叢集中升級主要元件或單一預設節點集區。

對於使用多個節點集區或 Windows Server 節點的 AKS 叢集（目前在 AKS 中為預覽狀態），請參閱[升級 AKS 中的節點集][nodepool-upgrade]區。

## <a name="before-you-begin"></a>開始之前

本文會要求您執行 Azure CLI 版本2.0.65 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

> [!WARNING]
> AKS 叢集升級會觸發 cordon 並清空您的節點。 如果您有可用的計算配額不足，升級可能會失敗。 如需詳細資訊，請參閱[增加配額](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

## <a name="check-for-available-aks-cluster-upgrades"></a>檢查可用的 AKS 叢集升級

若要檢查哪些 Kubernetes 版本可用於您的叢集，請使用 [az aks get-upgrades][az-aks-get-upgrades] 命令。 下列範例會在名為 *myResourceGroup* 的資源群組中，查看名為 *myAKSCluster* 的叢集的可用升級：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> 當您升級 AKS 叢集時，無法略過 Kubernetes 次要版本。 例如，允許在*1.12.* x  ->  *1.13. x*或*1.13. x*  ->  *1.14*之間進行升級，但*1.12. x*  ->  *1.14*不是。
>
> 若要升級，請從*1.12. x*  ->  *1.14*，先從*1.12.* x 1.13. x 升級  ->  * *，然後從*1.13. x*  ->  *1.14*升級。

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

## <a name="customize-node-surge-upgrade-preview"></a>自訂節點浪湧升級（預覽）

> [!Important]
> 節點浪湧需要針對每個升級作業要求的最大浪湧計數的訂用帳戶配額。 例如，具有5個節點集區的叢集（每個都有4個節點的計數）總共有20個節點。 如果每個節點集區的最大浪湧值為50%，則需要10個節點（2個節點 * 5 集區）的額外計算和 IP 配額，才能完成升級。
>
> 如果使用 Azure CNI，請驗證子網中是否有可用的 ip，以及[滿足 AZURE CNI 的 IP 需求](configure-azure-cni.md)。

根據預設，AKS 會使用一個額外的節點來設定激增的升級。 最大浪湧設定的預設值，可讓 AKS 在 cordon/清空現有應用程式以取代舊版的節點之前，先建立額外的節點，以將工作負載中斷降至最低。 您可以針對每個節點集區自訂最大的浪湧值，以便在升級速度和升級中斷之間進行取捨。 藉由增加最大的浪湧值，升級程式的完成速度會更快，但為最大激增設定大的值可能會在升級過程中造成中斷。 

例如，最大浪湧值100% 提供最快的可能升級程式（節點計數加倍），同時也會導致節點集區中的所有節點同時清空。 您可能想要使用較高的值（例如，用於測試環境）。 針對生產節點集區，我們建議使用33% 的 max_surge 設定。

AKS 接受整數值和最大激增的百分比值。 整數（例如 "5"）表示五個額外的節點要進行激增。 值為 "50%" 表示集區中目前節點計數一半的電湧值。 最大激增百分比值最小為1%，最大可達100%。 百分比值會無條件進位到最接近的節點計數。 如果最大浪湧值低於升級時的目前節點計數，則會使用目前的節點計數做為最大的浪湧值。

在升級期間，最大電湧值最小為1，而最大值等於節點集區中的節點數目。 您可以設定較大的值，但最大激增使用的節點數目上限不會高於升級時集區中的節點數目。

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>設定自訂節點浪湧升級的預覽功能

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

註冊需要幾分鐘的時間。 使用下列命令來確認已註冊功能：

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

在預覽期間，您需要*aks-preview* CLI 擴充功能，才能使用最大激增。 使用[az extension add][az-extension-add]命令，然後使用[az extension update][az-extension-update]命令檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> 節點集區上的最大浪湧設定是永久的。  後續的 Kubernetes 升級或節點版本升級會使用此設定。 您可以隨時變更節點集區的最大浪湧值。 針對生產節點集區，我們建議使用最大衝擊設定33%。

使用下列命令，為新的或現有的節點集區設定最大的浪湧值。

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>升級 AKS 叢集

透過適用於您的 AKS 叢集的可用版本清單，使用 [az aks upgrade][az-aks-upgrade] 命令進行升級。 在升級過程中，AKS 會將新節點新增至執行指定之 Kubernetes 版本的叢集，然後小心[cordon 並][kubernetes-drain]清空其中一個舊節點，以最小化執行應用程式的中斷。 當新節點確認為執行中的應用程式 pod 時，就會刪除舊的節點。 此程式會重複執行，直到叢集中的所有節點都已升級為止。

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

叢集升級需要幾分鐘的時間，具體取決於您擁有多少節點。

> [!NOTE]
> 完成叢集升級的允許時間總計。 這次是藉由取得的產品來計算 `10 minutes * total number of nodes in the cluster` 。 例如，在20個節點的叢集中，升級作業必須在200分鐘內成功，否則 AKS 將無法執行操作，以避免叢集狀態無法復原。 若要在升級失敗時復原，請在達到超時時間之後重試升級操作。

若要確認升級是否成功，請使用 [az aks show][az-aks-show] 命令：

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
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
