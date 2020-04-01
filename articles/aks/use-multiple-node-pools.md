---
title: 在 Azure 庫伯奈斯服務 (AKS) 中使用多個節點池
description: 瞭解如何在 Azure 庫伯奈斯服務 (AKS) 中為叢集建立和管理多個節點池
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 607419787bc0bab243d6cc2b8cbaa0ec22921e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422320"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>在 Azure 庫伯奈斯服務 (AKS) 中為群集建立和管理多個節點池

在 Azure 庫伯奈斯服務 (AKS) 中,相同配置的節點分組到*節點池*中。 這些節點池包含運行應用程式的基礎 VM。 建立 AKS 叢集時定義節點的初始數量及其大小 (SKU),該群集將建立*預設節點池*。 要支援具有不同計算或存儲需求的應用程式,您可以創建其他節點池。 例如,使用這些附加節點池為計算密集型應用程式提供 GPU,或存取高性能 SSD 儲存。

> [!NOTE]
> 此功能能夠對如何創建和管理多個節點池進行更高的控制。 因此,創建/更新/刪除需要單獨的命令。 以前,通過`az aks create`託管群集`az aks update`API 進行群集操作或使用,是更改控制平面和單個節點池的唯一選項。 此功能通過代理池 API 公開代理池的單獨操作集,並且`az aks nodepool`需要使用 命令集在單個節點池上執行操作。

本文介紹如何在 AKS 群集中創建和管理多個節點池。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 版本 2.2.0 或更高版本安裝和配置。 執行 `az --version` 以尋找版本。 如果需要安裝或升級,請參閱[安裝 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

建立與管理支援多個節點池的 AKS 叢集時,以下限制適用:

* 請參考[Azure 庫伯奈斯服務 (AKS) 中的配額、虛擬機器大小限制和地區可用性][quotas-skus-regions]。
* 預設情況下,無法刪除系統節點池,而無法刪除第一個節點池。
* AKS 群集必須使用標準 SKU 負載均衡器來使用多個節點池,基本 SKU 負載均衡器不支援此功能。
* AKS 群集必須對節點使用虛擬機縮放集。
* 節點池的名稱可能僅包含小寫字母數位字元,並且必須以小寫字母開頭。 對於 Linux 節點池,長度必須介於 1 到 12 個字元之間,對於 Windows 節點池,長度必須介於 1 到 6 個字元之間。
* 所有節點池必須駐留在同一虛擬網路中。
* 在群集創建時間創建多個節點池時,節點池使用的所有 Kubernetes 版本都必須與控制平面的版本集匹配。 在使用每個節點池操作預配群集后,可以更新此功能。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

要開始,請創建具有單個節點池的 AKS 群集。 下面的範例使用[az 組創建][az-group-create]命令在*東部*區域創建名為*myResourceGroup*的資源組。 然後使用[az aks 創建][az-aks-create]命令建立名為*myAKSCluster*的 AKS 群集。 *1.15.7* *的 --kubernetes 版本*用於演示如何在以下步驟中更新節點池。 您可以指定任何[支援的庫伯內斯版本][supported-versions]。

> [!NOTE]
> 使用多個節點池時 **,不支援***基本*負載均衡器 SKU。 默認情況下,AKS 群集使用 Azure CLI 和 Azure 門戶*的標準*負載均衡器 SKU 創建。

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
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

建立叢集需要幾分鐘的時間。

> [!NOTE]
> 為確保群集可靠運行,應在默認節點池中至少運行 2 個節點,因為基本系統服務在此節點池中運行。

叢集準備就緒後,使用 az [aks 取得認證的][az-aks-get-credentials]指令取得叢集認證,以便與`kubectl`中使用 。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>新增節點池

在上一步中創建的群集具有單個節點池。 讓我們使用[az aks 節點池添加][az-aks-nodepool-add]命令添加第二個節點池。 下面的範例建立一個名為*mynodepool*的節點池,該節點池運行*3 個*節點:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> 節點池的名稱必須以小寫字母開頭,並且只能包含字母數位字元。 對於 Linux 節點池,長度必須介於 1 到 12 個字元之間,對於 Windows 節點池,長度必須介於 1 到 6 個字元之間。

要檢視節點池的狀態,請使用[az aks 節點池清單][az-aks-nodepool-list]命令並指定資源群組和群集名稱:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

以下範例輸出顯示,在節點池中使用三個節點成功創建了*mynodepool。* 在上一步驟中創建 AKS 群集時,將創建一個節點數為*2*的預設*節點池 1。*

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
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
> 如果在添加節點池時未指定*VmSize,**則*Standard_DS2_v3 Windows 節點池的預設*大小,Standard_DS2_v2* Linux 節點池。 如果未指定*協調器版本*,則預設為與控制平面相同的版本。

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>新增具有唯一子網的節點池(預覽)

工作負載可能需要將群集的節點拆分為單獨的池,以便進行邏輯隔離。 此隔離可以通過專用於群集中每個節點池的單獨子網進行支援。 這可以解決諸如具有非連續虛擬網路位址空間以跨節點池拆分等要求。

#### <a name="limitations"></a>限制

* 分配給節點池的所有子網必須屬於同一虛擬網路。
* 系統 pod 必須有權訪問群集中的所有節點,才能通過 coreDNS 提供關鍵功能,如 DNS 解析。
* 在預覽期間,每個節點池的唯一子網的分配僅限於 Azure CNI。
* 預覽期間不支援將網路策略與每個節點池的唯一子網使用。

要使用專用子網創建節點池,在創建節點池時,將子網資源 ID 作為附加參數傳遞。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>升級節點池

> [!NOTE]
> 如果返回嘗試錯誤,則無法同時在群集或節點池上升級和縮放操作。 相反,在同一資源上下一個請求之前,每個操作類型都必須在目標資源上完成。 有關我們的[故障排除指南](https://aka.ms/aks-pending-upgrade),請閱讀更多有關。

首次在第一步中創建 AKS 群集`--kubernetes-version`時, 指定了*1.15.7。* 這將為控制平面和預設節點池設置 Kubernetes 版本。 本節中的命令說明如何升級單個特定節點池。

升級控制平面的 Kubernetes 版本和節點池之間的關係在[下面的部分](#upgrade-a-cluster-control-plane-with-multiple-node-pools)中解釋了。

> [!NOTE]
> 節點池 OS 映射版本綁定到群集的庫伯內斯版本。 在群集升級后,您只能獲得操作系統映射升級。

由於此示例中有兩個節點池,因此我們必須使用[az aks 節點池升級][az-aks-nodepool-upgrade]來升級節點池。 讓我們將*我的節點池*升級到庫伯內斯*1.15.7*。 使用[az aks 節點池升級][az-aks-nodepool-upgrade]命令升級節點池,如以下範例所示:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

使用[az aks 節點池清單][az-aks-nodepool-list]命令再次列出節點池的狀態。 下面的範例顯示*我的節點池*處於*升級到* *1.15.7*的狀態:

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
    "orchestratorVersion": "1.15.7",
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

將節點升級到指定版本需要幾分鐘時間。

最佳做法是,應將 AKS 群集中的所有節點池升級到相同的 Kubernetes 版本。 的`az aks upgrade`默認行為是將所有節點池與控制平面一起升級,以實現此對齊。 升級單個節點池的能力允許您執行滾動升級,並在節點池之間安排 pod,以保持上述限制範圍內的應用程式更新時間。

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>升級具有多個節點池的群集控制平面

> [!NOTE]
> 庫伯內斯使用標準的[語義版本](https://semver.org/)控制版本版本方案。 版本號表示為*x.y.z,* 其中*x*是主要版本 *,y*是次要版本 *,z*是修補程式版本。 例如,在版本*1.12.6*中,1 是主要版本,12 是次要版本,6 是修補程式版本。 在群集創建期間設置控制平面和初始節點池的 Kubernetes 版本。 所有其他節點池在添加到群集時都設置了 Kubernetes 版本。 Kubernetes 版本在節點池之間以及節點池和控制平面之間可能有所不同。

AKS 群集具有兩個群集資源物件,這些物件與庫伯內斯版本相關聯。

1. 群集控制平面庫伯內斯版本。
2. 具有庫伯奈斯版本的節點池。

控件平面映射到一個或多個節點池。 升級操作的行為取決於使用哪個 Azure CLI 命令。

升級 AKS 控制平`az aks upgrade`面需要 使用 。 此命令升級控制平面版本和群集中的所有節點池。

使用`--control-plane-only``az aks upgrade`標誌 發出命令僅升級群集控制平面。 群集中的任何關聯節點池都不會更改。

需要使用`az aks nodepool upgrade`升級 單個節點池。 此指令僅升級具有指定 Kubernetes 版本的目標節點池

### <a name="validation-rules-for-upgrades"></a>升級驗證規則

群集控制平面和節點池的有效 Kubernetes 升級由以下規則集驗證。

* 升級節點池的有效版本的規則:
   * 節點池版本必須具有與控制平面相同的*主要*版本。
   * 節點池*次要*版本必須在控件平面版本的兩*個次要*版本內。
   * 節點池版本不能大於控件`major.minor.patch`版本。

* 提交升級操作的規則:
   * 不能降級控制平面或節點池庫伯奈斯版本。
   * 如果未指定節點池 Kubernetes 版本,則行為取決於所使用的用戶端。 如果不使用,則資源管理器範本中的聲明將回退到為節點池定義的現有版本(如果未設置任何版本),則控件平面版本用於回退。
   * 您可以在給定時間升級或縮放控制平面或節點池,但不能同時在單個控制平面或節點池資源上提交多個操作。

## <a name="scale-a-node-pool-manually"></a>手動縮放節點池

隨著應用程式工作負載需求的變化,您可能需要擴展節點池中的節點數。 節點數量可以放大或縮小。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

要縮放節點池中的節點數,請使用[az aks 節點池縮放][az-aks-nodepool-scale]命令。 下面的範例將*mynodepool*中的節點數縮放為*5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

使用[az aks 節點池清單][az-aks-nodepool-list]命令再次列出節點池的狀態。 下面的範例顯示*我的節點池*處於*縮放*狀態,新計數為*5 個*節點:

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

規模操作需要幾分鐘才能完成。

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>以啟用叢集自動縮放器自動縮放特定節點池

AKS 提供了一個單獨的功能,用於使用稱為[群集自動縮放器](cluster-autoscaler.md)的功能自動縮放節點池。 此功能可以啟用每個節點池,每個節點池具有唯一的最小和最大比例計數。 瞭解如何[使用每個節點池的叢集自動縮放器](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。

## <a name="delete-a-node-pool"></a>刪除節點池

如果不再需要池,可以刪除它並刪除基礎 VM 節點。 要刪除節點池,請使用[az aks 節點池刪除][az-aks-nodepool-delete]命令並指定節點池名稱。 以下範例刪除在前面的步驟中建立的*mynoodepool:*

> [!CAUTION]
> 刪除節點池時,不存在可能發生的數據丟失的恢復選項。 如果無法在其他節點池上安排 pod,則這些應用程式不可用。 當正在使用的應用程式沒有數據備份或無法在群集中的其他節點池上運行時,請確保不會刪除節點池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

以下範例輸出來自[az aks 節點池清單][az-aks-nodepool-list]命令,顯示*我的節點池*處於*刪除*狀態:

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

刪除節點和節點池需要幾分鐘時間。

## <a name="specify-a-vm-size-for-a-node-pool"></a>為節點池指定 VM 大小

在前面創建節點池的範例中,預設 VM 大小用於群集中創建的節點。 更常見的方案是創建具有不同 VM 大小和功能的節點池。 例如,您可以創建包含具有大量 CPU 或記憶體的節點的節點池,或者創建提供 GPU 支援的節點池。 在下一步中,[您將使用污點和約束](#schedule-pods-using-taints-and-tolerations)來告訴 Kubernetes 調度程式如何限制對這些節點上運行的 pod 的訪問。

在下面的示例中,創建一個基於 GPU 的節點池,該節點池使用*Standard_NC6* VM 大小。 這些 VM 由 NVIDIA 特斯拉 K80 卡供電。 有關可用 VM 大小的資訊,請參閱[Azure 中 Linux 虛擬機器的大小][vm-sizes]。

再次使用[az aks 節點池添加][az-aks-nodepool-add]命令創建節點池。 此時,指定名稱*gpunodepool,* 並`--node-vm-size`使用 參數 指定*Standard_NC6*大小:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

以下範例輸出來自[az aks 節點池清單][az-aks-nodepool-list]指令,顯示*gpunodepool**正在建立*有*指定 VmSize 的*節點 :

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

成功創建*gpunodepool*需要幾分鐘時間。

## <a name="schedule-pods-using-taints-and-tolerations"></a>使用污漬和包腳來安排吊艙

現在,群集中有兩個節點池 - 最初創建的預設節點池和基於 GPU 的節點池。 使用[kubectl 獲取節點][kubectl-get]命令查看群集中的節點。 以下範例輸出顯示節點:

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

有關如何使用高級 Kubernets 計劃功能的詳細資訊,請參閱[AKS 中高級計畫程式功能的最佳做法][taints-tolerations]

在此範例中,使用 --node-taints 命令對基於 GPU 的節點應用污點。 從上一個`kubectl get nodes`指令的輸出中指定基於 GPU 的節點的名稱。 污點作為*鍵:值*和調度選項應用。 下面的範例使用*sku_gpu*對,並定義窗格,否則具有*No 計畫*功能:

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

以下基本示例 YAML 清單使用容忍功能允許 Kubernetes 調度程式在基於 GPU 的節點上運行 NGINX pod。 有關針對 MNIST 資料集執行 Tensorflow 作業更合適但耗時的範例,請參閱[在 AKS 上使用 GPU 進行計算密集型工作負載][gpu-cluster]。

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

使用`kubectl apply -f gpu-toleration.yaml`以下指令排程窗格:

```console
kubectl apply -f gpu-toleration.yaml
```

計劃吊艙並拉取 NGINX 圖像需要幾秒鐘的時間。 使用[kubectl 描述窗格][kubectl-describe]命令查看窗格狀態。 以下壓縮示例輸出顯示*sku_gpu:不應用計劃*容忍。 在事件部分中,計劃程式將 pod 分配給基於*aks-gpunodepool-28993262-vms00000000000*的基於 GPU 的節點:

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

只有應用了此污點的 pod 才能安排在*gpunodepool*中的節點上。 任何其他 pod 都將安排在*節點池 1*節點池中。 如果創建其他節點池,則可以使用其他污點和約束來限制可以在這些節點資源上安排哪些 pod。

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>為節點池指定污點、標籤或標記

創建節點池時,可以將污點、標籤或標記添加到該節點池。 添加污點、標籤或標記時,該節點池中的所有節點也會獲取該污點、標籤或標記。

要建立具有污點的節點池,請使用[az aks 節點池添加][az-aks-nodepool-add]。 指定*的訊號*的名稱並`--node-taints`使用 參數指定*sku_gpu:無計畫*。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

以下範例輸出來自[az aks 節點池清單][az-aks-nodepool-list]命令,顯示*taintnp*正在*建立*指定*節點的節點:*

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
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

用於處理節點調度規則的 Kubernets 中可以看到污染資訊。

您還可以在節點池創建期間向節點池添加標籤。 節點池中設置的標籤將添加到節點池中的每個節點。 [這些標籤在 Kubernetes 中可見][kubernetes-labels],用於處理節點的調度規則。

要建立帶有分頁的節點池,請使用[az aks 節點池新增][az-aks-nodepool-add]。 指定名稱*標籤,*`--labels`並使用 參數為標籤指定*dept_IT*和*costcenter_9999。*

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
> 只能在節點池創建期間為節點池設置標籤。 標籤還必須是鍵/值對,並且具有[有效的語法][kubernetes-label-syntax]。

以下範例輸出來自[az aks 節點池清單][az-aks-nodepool-list]指令,顯示*labelnp*正在*建立*在*節點分頁的節點*:

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

您可以將 Azure 標記應用於 AKS 群集中的節點池。 應用於節點池的標記將應用於節點池中的每個節點,並通過升級進行持久化。 標記還應用於橫向擴展操作期間添加到節點池的新節點。 添加標記有助於執行策略跟蹤或成本估算等任務。

使用[az aks 節點池添加][az-aks-nodepool-add]創建節點池。 指定名稱*標記節點池*`--tag`並使用 參數為標記指定*dept_IT*和*costcenter_9999。*

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
> 在使用 az `--tags` [aks 節點池更新][az-aks-nodepool-update]命令以及在群集創建期間,也可以使用 參數。 在群集建立期間,`--tags`參數將標記應用於使用群集創建的初始節點池。 所有標記名稱都必須符合[「使用」 標記中的限制來組織 Azure 資源][tag-limitation]。 使用`--tags`參數更新節點池會更新任何現有標記值並追加任何新標記。 例如,如果節點池的標記有*dept_IT*和*costcenter_9999,* 並且使用*團隊_dev*和*costcenter=111*為標記更新了它,則節點池將具有*dept_IT、costcenter=111*和為標記的*team_dev。* *costcenter=111*

以下範例輸出來自[az aks 節點池清單][az-aks-nodepool-list]命令,顯示*tagnodepool**正在 建立*有指定*標籤*的節點:

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

## <a name="manage-node-pools-using-a-resource-manager-template"></a>使用資源管理員樣本管理節點池

使用 Azure 資源管理器樣本創建和託管資源時,通常可以更新範本中的設置並重新部署以更新資源。 使用 AKS 中的節點池,創建 AKS 群集後無法更新初始節點池配置檔。 此行為意味著您無法更新現有的資源管理器範本、更改節點池並重新部署。 相反,必須創建一個單獨的資源管理器範本,該範本僅更新現有 AKS 群集的節點池。

創建範本,如`aks-agentpools.json`並粘貼以下範例清單。 此範例樣本設定以下設定:

* 更新名為*Myagentpool*的*Linux*節點池以運行三個節點。
* 將節點池中的節點設置以運行庫伯內斯版本*1.15.7*。
* 將節點大小定義為*Standard_DS2_v2*。

根據需要編輯這些值以更新、新增或刪除節點池:

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

使用[az 組部署][az-group-deployment-create]部署部署此範本建立命令,如以下範例所示。 系統會提示您提供現有 AKS 群集名稱和位置:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 通過在範本中添加*標記*屬性,可以將標記添加到節點池,如以下範例所示。
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

更新 AKS 群集可能需要幾分鐘時間,具體取決於在資源管理器範本中定義的節點池設置和操作。

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>為節點池為每個節點配置公共 IP(預覽)

> [!WARNING]
> 在為每個節點分配公共 IP 的預覽期間,由於可能與 VM 預配衝突,因此不能與*AKS 中的標準負載均衡器 SKU*一起使用。 由於此限制,此預覽功能不支援 Windows 代理池。 在預覽版中,如果需要為每個節點分配公共 IP,則必須使用*基本負載均衡器 SKU。*

AKS 節點不需要自己的公共 IP 位址進行通信。 但是,方案可能需要節點池中的節點接收其自己的專用公共 IP 位址。 常見方案適用於遊戲工作負載,其中主控台需要直接連接到雲端虛擬機,以盡量減少躍點。 此方案可以通過註冊預覽功能「節點公共 IP」(預覽)在 AKS 上實現。

通過發出以下 Azure CLI 命令註冊節點公共 IP 功能。

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

成功註冊后,按照[上述](#manage-node-pools-using-a-resource-manager-template)相同的說明部署 Azure 資源管理器範本,並將`enableNodePublicIP`布爾屬性 添加到代理池配置檔。 將該值設置為`true`默認情況下,它設置為`false`未 指定。 

此屬性僅創建時間屬性,需要最低 API 版本 2019-06-01。 這可以同時應用於 Linux 和 Windows 節點池。

## <a name="clean-up-resources"></a>清除資源

在本文中,您創建了一個 AKS 群集,該群集包含基於 GPU 的節點。 為了減少不必要的成本,您可能需要刪除*gpunodepool*或整個 AKS 群集。

要刪除基於 GPU 的節點池,請使用[az aks 節點池刪除][az-aks-nodepool-delete]命令,如以下範例所示:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

要刪除群組本身,請使用[az 群組移除][az-group-delete]指令刪除 AKS 資源群組:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>後續步驟

在本文中,您學習了如何在 AKS 群集中創建和管理多個節點池。 有關如何跨節點池控制 pod 的詳細資訊,請參閱[AKS 中進階計畫程式功能的最佳做法][operator-best-practices-advanced-scheduler]。

要建立與使用 Windows 伺服器容器的傳輸,請參閱[在 AKS 建立 Windows 伺服器容器][aks-windows]。

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
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md