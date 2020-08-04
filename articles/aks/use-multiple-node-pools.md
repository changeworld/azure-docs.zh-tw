---
title: 在 Azure Kubernetes Service 中使用多個節點集區（AKS）
description: 瞭解如何在 Azure Kubernetes Service （AKS）中建立及管理叢集的多個節點集區
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: d007ec18a982d5327aa2ea0871bbe88f64786fce
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542020"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中建立和管理叢集的多個節點集區 \(部分機器翻譯\)

在 Azure Kubernetes Service （AKS）中，相同設定的節點會群組在一起成為*節點*集區。 這些節點集區包含執行應用程式的基礎 Vm。 當您建立 AKS 叢集時，會定義初始節點數目及其大小（SKU），以建立[系統節點集][use-system-pool]區。 若要支援具有不同計算或儲存體需求的應用程式，您可以建立額外的*使用者節點*集區。 系統節點集區提供裝載重要系統 pod （例如 CoreDNS 和 tunnelfront）的主要目的。 使用者節點集區提供裝載應用程式 pod 的主要目的。 不過，如果您想要在 AKS 叢集中只有一個集區，可以在系統節點集區上排程應用程式 pod。 使用者節點集區是您放置應用程式特定 pod 的位置。 例如，您可以使用這些額外的使用者節點集區，為計算密集型應用程式提供 Gpu，或存取高效能 SSD 儲存體。

> [!NOTE]
> 這項功能可讓您更進一步控制如何建立和管理多個節點集區。 因此，建立/更新/刪除需要個別的命令。 先前的叢集作業會透過 `az aks create` 或 `az aks update` 使用 managedCluster API，而且是變更您的控制平面和單一節點集區的唯一選項。 這項功能會透過 agentPool API 公開代理程式組件區的個別作業集，並要求使用 `az aks nodepool` 命令集來執行個別節點集區上的作業。

本文說明如何在 AKS 叢集中建立和管理多個節點集區。

## <a name="before-you-begin"></a>開始之前

您需要安裝並設定 Azure CLI 版本2.2.0 或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

在建立和管理支援多個節點集區的 AKS 叢集時，需遵守下列限制：

* 請參閱[配額、虛擬機器大小限制，以及 Azure Kubernetes Service 中的區域可用性（AKS）][quotas-skus-regions]。
* 您可以刪除系統節點集區，但前提是您有另一個系統節點集區，以將其放在 AKS 叢集中。
* 系統集區必須包含至少一個節點，且使用者節點集區可能包含零個或多個節點。
* AKS 叢集必須使用標準 SKU 負載平衡器來使用多個節點集區，但基本 SKU 負載平衡器不支援此功能。
* AKS 叢集必須使用節點的虛擬機器擴展集。
* 節點集區的名稱只可包含小寫英數位元，且必須以小寫字母開頭。 針對 Linux 節點集區，長度必須介於1到12個字元之間，而 Windows 節點集區的長度必須介於1到6個字元之間。
* 所有節點集區都必須位於相同的虛擬網路中。
* 在叢集建立時建立多個節點集區時，節點集區所使用的所有 Kubernetes 版本都必須符合控制平面的版本。 使用每個節點集區作業布建叢集之後，即可更新此項。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

> [!Important]
> 如果您在生產環境中針對 AKS 叢集執行單一系統節點集區，建議您針對節點集區使用至少三個節點。

若要開始使用，請建立具有單一節點集區的 AKS 叢集。 下列範例會使用[az group create][az-group-create]命令，在*eastus*區域中建立名為*myResourceGroup*的資源群組。 然後使用[az AKS create][az-aks-create]命令來建立名為*myAKSCluster*的 AKS 叢集。

> [!NOTE]
> 使用多個節點集區時，**不支援***基本*負載平衡器 SKU。 根據預設，系統會使用 Azure CLI 和 Azure 入口網站中的*標準*負載平衡器 SKU 來建立 AKS 叢集。

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

建立叢集需要幾分鐘的時間。

> [!NOTE]
> 若要確保您的叢集能夠可靠地運作，您應該在預設節點集區中至少執行2（兩個）節點，因為基本的系統服務在此節點集區上執行。

當叢集準備就緒時，請使用[az aks get-認證][az-aks-get-credentials]命令來取得要搭配使用的叢集認證 `kubectl` ：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>新增節點集區

在上一個步驟中建立的叢集具有單一節點集區。 讓我們使用[az aks nodepool add][az-aks-nodepool-add]命令來新增第二個節點集區。 下列範例會建立名為*mynodepool*的節點集區，其會執行*3*個節點：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> 節點集區的名稱必須以小寫字母開頭，而且只能包含英數位元。 針對 Linux 節點集區，長度必須介於1到12個字元之間，而 Windows 節點集區的長度必須介於1到6個字元之間。

若要查看節點集區的狀態，請使用[az aks node pool list][az-aks-nodepool-list]命令，並指定您的資源群組和叢集名稱：

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

下列範例輸出顯示已成功建立節點集區中有三個節點的*mynodepool* 。 在上一個步驟中建立 AKS 叢集時，會使用節點計數*2*來建立預設*nodepool1* 。

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> 當您新增節點集區時，如果未指定*VmSize* ，則 Windows 節點集區的預設大小為*Standard_D2s_v3* ，而 Linux 節點集區*Standard_DS2_v2*則為。 如果未指定*OrchestratorVersion* ，則會預設為與控制平面相同的版本。

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>新增具有唯一子網的節點集區（預覽）

工作負載可能需要將叢集的節點分割成不同的集區，以進行邏輯隔離。 在叢集中每個節點集區專用的個別子網，都可支援此隔離。 這可解決需求，例如在節點集區中分割非連續的虛擬網路位址空間。

#### <a name="limitations"></a>限制

* 所有指派給 nodepools 的子網都必須屬於相同的虛擬網路。
* 系統 pod 必須具有叢集中所有節點的存取權，才能透過 coreDNS 提供重要的功能，例如 DNS 解析。
* 在預覽期間，每個節點集區的唯一子網指派限制為 Azure CNI。
* 在預覽期間，不支援在每個節點集區中使用具有唯一子網的網路原則。

若要建立具有專用子網的節點集區，請在建立節點集區時，傳遞子網資源識別碼做為額外的參數。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>升級節點集區

> [!NOTE]
> 在叢集或節點集區上的升級和調整作業不能同時發生，如果嘗試了錯誤就會傳回。 相反地，每個作業類型必須在目標資源上完成，然後才在該相同資源上進行下一個要求。 請在我們的[疑難排解指南](https://aka.ms/aks-pending-upgrade)中閱讀更多相關資訊。

本節中的命令說明如何升級單一特定的節點集區。 [下一節](#upgrade-a-cluster-control-plane-with-multiple-node-pools)將說明升級控制平面和節點集區的 Kubernetes 版本之間的關聯性。

> [!NOTE]
> 節點集區 OS 映射版本會系結至叢集的 Kubernetes 版本。 在叢集升級之後，您只會取得 OS 映射升級。

因為在此範例中有兩個節點集區，所以我們必須使用[az aks nodepool upgrade][az-aks-nodepool-upgrade]來升級節點集區。 若要查看可用的升級，請使用[az aks get-升級][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

讓我們來升級*mynodepool*。 使用[az aks nodepool upgrade][az-aks-nodepool-upgrade]命令升級節點集區，如下列範例所示：

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出節點集區的狀態。 下列範例顯示*mynodepool*處於*升級*狀態， *KUBERNETES_VERSION*：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

將節點升級為指定的版本需要幾分鐘的時間。

最佳做法是將 AKS 叢集中的所有節點集區升級至相同的 Kubernetes 版本。 的預設行為 `az aks upgrade` 是將所有節點集區與控制平面同時升級，以達成這種對齊方式。 升級個別節點集區的功能可讓您執行輪流升級，並在節點集區之間排程 pod，以維護上述條件約束內的應用程式執行時間。

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>升級具有多個節點集區的叢集控制平面

> [!NOTE]
> Kubernetes 使用標準的[語義版本](https://semver.org/)控制版本設定配置。 版本號碼會以*x-y*表示，其中*x*是主要版本， *y*是次要版本，而*z*是修補程式版本。 例如，在版本*1.12.6*中，1是主要版本，12是次要版本，而6是修補程式版本。 在叢集建立期間，會設定控制平面和初始節點集區的 Kubernetes 版本。 當所有其他節點集區新增至叢集時，會設定其 Kubernetes 版本。 節點集區以及節點集區與控制平面之間的 Kubernetes 版本可能不同。

AKS 叢集有兩個叢集資源物件與 Kubernetes 版本相關聯。

1. 叢集控制平面 Kubernetes 版本。
2. 具有 Kubernetes 版本的節點集區。

控制平面會對應至一或多個節點集區。 升級作業的行為取決於所使用的 Azure CLI 命令。

升級 AKS 控制平面需要使用 `az aks upgrade` 。 此命令會升級控制平面版本以及叢集中的所有節點集區。

`az aks upgrade`以旗標發出命令 `--control-plane-only` 只會升級叢集控制平面。 叢集中沒有任何相關聯的節點集區變更。

升級個別節點集區需要使用 `az aks nodepool upgrade` 。 此命令只會以指定的 Kubernetes 版本升級目標節點集區

### <a name="validation-rules-for-upgrades"></a>升級的驗證規則

叢集的控制平面和節點集區的有效 Kubernetes 升級會由下列一組規則來驗證。

* 升級節點集區的有效版本規則：
   * 節點集區版本必須與控制平面具有相同的*主要*版本。
   * 節點集區*次要*版本必須在控制平面版本的兩個*次要*版本內。
   * 節點集區版本不能大於控制項 `major.minor.patch` 版本。

* 提交升級作業的規則：
   * 您無法降級控制平面或節點集區 Kubernetes 版本。
   * 如果未指定節點集區 Kubernetes 版本，則行為取決於所使用的用戶端。 Resource Manager 範本中的宣告會回復至針對節點集區定義的現有版本（如果使用的話），如果未設定，則會使用控制平面版本來切換回來。
   * 您可以在指定的時間升級或調整控制平面或節點集區，而無法同時在單一控制平面或節點集區資源上提交多項作業。

## <a name="scale-a-node-pool-manually"></a>手動調整節點集區

當您的應用程式工作負載需求變更時，您可能需要調整節點集區中的節點數目。 節點數目可以向上或向下調整。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

若要調整節點集區中的節點數目，請使用[az aks node pool scale][az-aks-nodepool-scale]命令。 下列範例會將*mynodepool*中的節點數目調整為*5*：

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出節點集區的狀態。 下列範例顯示*mynodepool*處於*調整*狀態，而且有*5*個節點的新計數：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

調整作業需要幾分鐘的時間才能完成。

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>藉由啟用叢集自動調整程式，自動調整特定節點集區

AKS 提供不同的功能，可使用稱為叢集[自動調整程式](cluster-autoscaler.md)的功能自動調整節點集區。 針對每個節點集區具有唯一最小和最大規模計數的節點集區，可以啟用這項功能。 瞭解如何[使用每個節點集區的叢集自動調整程式](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。

## <a name="delete-a-node-pool"></a>刪除節點集區

如果您不再需要集區，您可以將它刪除並移除基礎 VM 節點。 若要刪除節點集區，請使用[az aks node pool delete][az-aks-nodepool-delete]命令並指定節點集區名稱。 下列範例會刪除在先前步驟中建立的*mynoodepool* ：

> [!CAUTION]
> 當您刪除節點集區時，不會發生資料遺失的復原選項。 如果無法在其他節點集區上排程 pod，則無法使用這些應用程式。 當使用中的應用程式沒有資料備份或在叢集中的其他節點集區上執行的能力時，請確定您不會刪除節點集區。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

下列來自[az aks node pool list][az-aks-nodepool-list]命令的範例輸出顯示*mynodepool*處於*刪除*狀態：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

刪除節點和節點集區需要幾分鐘的時間。

## <a name="specify-a-vm-size-for-a-node-pool"></a>指定節點集區的 VM 大小

在先前的範例中，若要建立節點集區，則會針對在叢集中建立的節點使用預設的 VM 大小。 較常見的案例是使用不同的 VM 大小和功能來建立節點集區。 例如，您可以建立節點集區，其中包含具有大量 CPU 或記憶體的節點，或可提供 GPU 支援的節點集區。 在下一個步驟中，您會[使用污點和容差](#schedule-pods-using-taints-and-tolerations)，告訴 Kubernetes 排程器如何限制可以在這些節點上執行的 pod 存取。

在下列範例中，建立以 GPU 為基礎的節點集區，以使用*Standard_NC6* VM 大小。 這些 Vm 是由 NVIDIA Tesla K80 插卡提供技術支援。 如需可用 VM 大小的詳細資訊，請參閱[Azure 中 Linux 虛擬機器的大小][vm-sizes]。

再次使用[az aks node pool add][az-aks-nodepool-add]命令來建立節點集區。 這次請指定名稱*gpunodepool*，並使用 `--node-vm-size` 參數來指定*Standard_NC6*大小：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

下列來自[az aks node pool list][az-aks-nodepool-list]命令的範例輸出顯示*gpunodepool*正在*建立*具有指定*VmSize*的節點：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

成功建立*gpunodepool*需要幾分鐘的時間。

## <a name="schedule-pods-using-taints-and-tolerations"></a>使用污點和容差排程 pod

您現在的叢集中有兩個節點集區-一開始建立預設節點集區，以及以 GPU 為基礎的節點集區。 使用 [ [kubectl] [取得節點][kubectl-get]] 命令來查看您叢集中的節點。 下列範例輸出顯示節點：

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Kubernetes 排程器可以使用污點和容差來限制可以在節點上執行的工作負載。

* **污點**會套用至節點，該節點指示僅可以在其上排程特定的 pod。
* 然後**容差**會套用至容器，允許它們*容許*節點的污點。

如需如何使用 advanced Kubernetes 排程功能的詳細資訊，請參閱[AKS 中 advanced 排程器功能的最佳做法][taints-tolerations]

在此範例中，使用--node-污點命令，將污點套用至 GPU 節點。 從上一個命令的輸出中指定 GPU 型節點的名稱 `kubectl get nodes` 。 污點會套用為索引*鍵 = 值*組，然後是排程選項。 下列範例會使用*sku = gpu*配對，並定義 pod，否則會具有*NoSchedule*功能：

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

下列基本範例 YAML 資訊清單會使用 toleration，以允許 Kubernetes 排程器在 GPU 型節點上執行 NGINX pod。 如需針對 MNIST 資料集執行 Tensorflow 作業的更適當但需要大量時間的範例，請參閱[在 AKS 上針對計算密集型工作負載使用 gpu][gpu-cluster]。

建立名為 `gpu-toleration.yaml` 的檔案，然後將下列範例 YAML 複製進來：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

使用命令來排程 pod `kubectl apply -f gpu-toleration.yaml` ：

```console
kubectl apply -f gpu-toleration.yaml
```

需要幾秒鐘的時間來排程 pod，並提取 NGINX 的映射。 使用 [ [kubectl 描述 pod][kubectl-describe] ] 命令來查看 pod 狀態。 下列精簡的範例輸出顯示已套用*sku = gpu： NoSchedule* toleration。 在 [事件] 區段中，排程器已將 pod 指派給*aks-gpunodepool-28993262-vmss000000* GPU 型節點：

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

只有套用此 toleration 的 pod 可以在*gpunodepool*的節點上排程。 任何其他 pod 都會在*nodepool1*節點集區中排程。 如果您建立其他節點集區，您可以使用其他污點和容差來限制可在這些節點資源上排程的 pod。

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>指定節點集區的污點、標籤或標記

### <a name="setting-nodepool-taints"></a>設定 nodepool 污點

建立節點集區時，您可以將污點、標籤或標記新增至該節點集區。 當您新增污點、標籤或標記時，該節點集區中的所有節點也會取得該污點、標籤或標記。

若要建立具有污點的節點集區，請使用[az aks nodepool add][az-aks-nodepool-add]。 指定名稱*taintnp* ，並使用 `--node-taints` 參數指定污點的*Sku = gpu： NoSchedule* 。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> 只有在建立節點集區時，才可以針對節點集區設定污點。

下列來自[az aks nodepool list][az-aks-nodepool-list]命令的範例輸出顯示*taintnp*正在*建立*具有指定*nodeTaints*的節點：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

在處理節點排程規則的 Kubernetes 中，會顯示污點資訊。

### <a name="setting-nodepool-labels"></a>設定 nodepool 標籤

您也可以在建立節點集區時，將標籤新增至節點集區。 在節點集區設定的標籤會新增至節點集區中的每個節點。 這些[標籤會顯示在 Kubernetes 中][kubernetes-labels]，以處理節點的排程規則。

若要建立具有標籤的節點集區，請使用[az aks nodepool add][az-aks-nodepool-add]。 指定名稱*labelnp* ，並使用 `--labels` 參數來指定適用于標籤的*部門 = IT*和*costcenter = 9999* 。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> 標籤只能在節點集區建立期間，針對節點集區設定。 標籤也必須是索引鍵/值組，而且具有[有效的語法][kubernetes-label-syntax]。

下列來自[az aks nodepool list][az-aks-nodepool-list]命令的範例輸出顯示*labelnp*正在*建立*具有指定*nodeLabels*的節點：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>設定 nodepool Azure 標記

您可以將 Azure 標記套用至 AKS 叢集中的節點集區。 套用至節點集區的標記會套用至節點集區中的每個節點，並透過升級來保存。 標籤也會套用至向外延展作業期間新增至節點集區的新節點。 新增標籤可協助進行原則追蹤或成本預估之類的工作。

Azure 標記具有不區分大小寫作業的索引鍵，例如藉由搜尋金鑰來抓取標記時。 在此情況下，不論大小寫為何，將會更新或抓取具有指定之索引鍵的標記。 標記值會區分大小寫。

在 AKS 中，如果使用相同的索引鍵來設定多個標記，但大小寫不同，則使用的標記是以字母順序排列的第一個。 例如，會 `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` 導致 `Key1` 和 `val1` 設定。

使用[az aks nodepool add][az-aks-nodepool-add]建立節點集區。 指定名稱*tagnodepool* ，並使用 `--tag` 參數來指定適用于標記的*部門 = IT*和*costcenter = 9999* 。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> `--tags`使用[az aks nodepool update][az-aks-nodepool-update]命令以及在叢集建立期間，您也可以使用參數。 在叢集建立期間，參數會將標籤 `--tags` 套用至使用叢集建立的初始節點集區。 所有標記名稱都必須遵守[使用標記來組織您的 Azure 資源][tag-limitation]的限制。 以參數更新節點集區時，會 `--tags` 更新任何現有的標記值，並附加任何新的標記。 例如，如果您的節點集區具有*部門 = it*並*costcenter = 9999*的標籤，且您已使用*team = dev*和*costcenter = 111*來更新標記，則 nodepool 會有*部門 = it*、 *costcenter = 111*和*team = dev* for tags。

下列來自[az aks nodepool list][az-aks-nodepool-list]命令的範例輸出顯示*tagnodepool*正在*建立*具有指定*標記*的節點：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>使用 Resource Manager 範本管理節點集區

當您使用 Azure Resource Manager 範本來建立和管理資源時，您通常可以更新範本中的設定，並重新部署以更新資源。 在 AKS 中使用節點集區時，一旦建立 AKS 叢集之後，就無法更新初始節點集區設定檔。 這個行為表示您無法更新現有的 Resource Manager 範本、對節點集區進行變更，然後重新部署。 相反地，您必須建立個別的 Resource Manager 範本，只更新現有 AKS 叢集的節點集區。

建立範本（例如） `aks-agentpools.json` ，並貼上下列範例資訊清單。 這個範例範本會設定下列設定：

* 更新名為*myagentpool*的*Linux*節點集區，以執行三個節點。
* 設定節點集區中的節點，以執行 Kubernetes 版本*1.15.7*。
* 將節點大小定義為*Standard_DS2_v2*。

視需要編輯這些值，視需要更新、新增或刪除節點集區：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

使用[az group deployment create][az-group-deployment-create]命令來部署此範本，如下列範例所示。 系統會提示您輸入現有的 AKS 叢集名稱和位置：

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 您可以藉由在範本中新增*tag*屬性，將標記新增至您的節點集區，如下列範例所示。
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

視您在 Resource Manager 範本中定義的節點集區設定和作業而定，可能需要幾分鐘的時間來更新 AKS 叢集。

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>為節點集區指派每個節點的公用 IP （預覽）

> [!WARNING]
> 您必須安裝 CLI preview 延伸模組0.4.43 或更新版本，才能使用每個節點的公用 IP 功能。

AKS 節點不需要自己的公用 IP 位址進行通訊。 不過，案例可能會要求節點集區中的節點接收其專屬的公用 IP 位址。 常見的案例是針對遊戲工作負載，其中主控台需要直接連線到雲端虛擬機器，以將躍點降到最低。 藉由註冊預覽功能、節點公用 IP （預覽），即可在 AKS 上達成此案例。

若要安裝並更新最新的 aks-preview 延伸模組，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

使用下列 Azure CLI 命令來註冊 Node 公用 IP 功能：

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
註冊功能可能需要幾分鐘的時間。  您可以使用下列命令來檢查狀態：

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

成功註冊之後，請建立新的資源群組。

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

建立新的 AKS 叢集，並為您的節點附加公用 IP。 節點集區中的每個節點都會收到唯一的公用 IP。 您可以查看虛擬機器擴展集實例來確認這一點。

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

針對現有的 AKS 叢集，您也可以新增節點集區，並為您的節點附加公用 IP。

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> 在預覽期間，Azure Instance Metadata Service 目前不支援標準層 VM SKU 的公用 IP 位址抓取。 由於這項限制，您無法使用 kubectl 命令來顯示指派給節點的公用 Ip。 不過，系統會將 Ip 指派給並依預期運作。 您節點的公用 Ip 會附加至虛擬機器擴展集中的實例。

您可以透過各種方式找出節點的公用 Ip：

* 使用 Azure CLI 命令[az vmss list-instance-public-ip][az-list-ips]
* 使用[PowerShell 或 Bash 命令][vmss-commands]。 
* 您也可以查看虛擬機器擴展集中的實例，以在 Azure 入口網站中查看公用 Ip。

> [!Important]
> [節點資源群組][node-resource-group]包含節點和其公用 ip。 執行命令時，請使用 node 資源群組來尋找節點的公用 Ip。

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>清除資源

在本文中，您已建立 AKS 叢集，其中包含以 GPU 為基礎的節點。 若要減少不必要的成本，您可能會想要刪除*gpunodepool*或整個 AKS 叢集。

若要刪除以 GPU 為基礎的節點集區，請使用[az aks nodepool delete][az-aks-nodepool-delete]命令，如下列範例所示：

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

若要刪除叢集本身，請使用[az group delete][az-group-delete]命令來刪除 AKS 資源群組：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

您也可以刪除您為 [節點集區的公用 IP] 案例所建立的其他叢集。

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

深入瞭解[系統節點][use-system-pool]集區。

在本文中，您已瞭解如何在 AKS 叢集中建立和管理多個節點集區。 如需如何跨節點集區控制 pod 的詳細資訊，請參閱[AKS 中先進排程器功能的最佳做法][operator-best-practices-advanced-scheduler]。

若要建立及使用 Windows Server 容器節點集區，請參閱[在 AKS 中建立 Windows server 容器][aks-windows]。

使用[鄰近放置群組][reduce-latency-ppg]來減少 AKS 應用程式的延遲。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest.md#az-vmss-list-instance-public-ips
[reduce-latency-ppg]: reduce-latency-ppg.md
