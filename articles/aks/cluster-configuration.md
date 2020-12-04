---
title: Azure Kubernetes Service (AKS) 的叢集設定
description: 了解如何在 Azure Kubernetes Service (AKS) 中設定叢集
services: container-service
ms.topic: article
ms.date: 09/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: dcd9ec52992da8037bb498dc09701879c4c94667
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96572570"
---
# <a name="configure-an-aks-cluster"></a>設定 AKS 叢集

建立 AKS 叢集的過程中，可能需要自訂叢集設定來配合您的需求。 本文會介紹幾種可用來自訂 AKS 叢集的選項。

## <a name="os-configuration"></a>OS 設定

AKS 現在支援 Ubuntu 18.04 作為節點作業系統 (OS) 在高於1.18.8 的 kubernetes 版本中的叢集正式運作。 針對小於 1.18. x 的版本，AKS Ubuntu 16.04 仍是預設的基底映射。 從 kubernetes v 1.18. x 和之後，預設基底為 AKS Ubuntu 18.04。

> [!IMPORTANT]
> 在 Kubernetes v 1.18 或更高版本上建立的節點集區預設為 `AKS Ubuntu 18.04` 節點映射。 小於1.18 的支援 Kubernetes 版本上的節點集區會 `AKS Ubuntu 16.04` 以節點映射的形式接收，但 `AKS Ubuntu 18.04` 一旦節點集區 Kubernetes 版本更新為1.18 或更高版本，則會更新為。
> 
> 強烈建議您先在 AKS Ubuntu 18.04 節點集區上測試您的工作負載，再于1.18 或更高版本上使用叢集。 瞭解如何 [測試 Ubuntu 18.04 節點](#use-aks-ubuntu-1804-existing-clusters-preview)集區。

下一節將說明如何在尚未使用 kubernetes 版本 1.18. x 或更高版本的叢集上使用案例，並測試 AKS Ubuntu 18.04，或使用 OS 設定預覽版在此功能正式推出之前建立。

您必須先安裝下列資源：

- [Azure CLI][azure-cli-install]版本2.2.0 或更新版本
- aks-preview 0.4.35 擴充功能

若要安裝 aks-preview 0.4.35 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

註冊 `UseCustomizedUbuntuPreview` 功能：

```azurecli
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>在新的叢集上使用 AKS Ubuntu 18.04 (Preview) 

在叢集建立時，請將叢集設定為使用 Ubuntu 18.04。 請使用 `--aks-custom-headers` 旗標將 Ubuntu 18.04 設定為預設作業系統。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果您想要使用 AKS Ubuntu 16.04 映射建立叢集，您可以省略自訂標記來進行 `--aks-custom-headers` 。

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>使用 AKS Ubuntu 18.04 現有的叢集 (預覽) 

請將新的節點集區設定為使用 Ubuntu 18.04。 請使用 `--aks-custom-headers` 旗標，將 Ubuntu 18.04 設定為該節點集區的預設作業系統。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果您想要使用 AKS Ubuntu 16.04 映射建立節點集區，可以省略自訂標籤來進行 `--aks-custom-headers` 。

## <a name="container-runtime-configuration"></a>容器執行時間設定

容器執行時間是一種軟體，可執行容器和管理節點上的容器映射。 執行時間可協助您抽象化 sys 呼叫或作業系統 (OS) 特定功能，在 Linux 或 Windows 上執行容器。 使用 Kubernetes 1.19 版節點集區的 AKS 叢集，並將 `containerd` 其作為容器執行時間使用。 在節點集區中使用 Kubernetes 之前的 AKS 叢集使用 [Moby](https://mobyproject.org/) (上游 docker) 作為容器執行時間。

![Docker CRI 1](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/) 是一個 [OCI](https://opencontainers.org/) (開放式容器計畫) 符合規範的核心容器執行時間，可提供執行容器和管理節點上映射的最小必要功能集。 它是在2017年3月的雲端原生計算基礎 (CNCF) [捐贈](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/) 。 AKS 使用的目前 Moby 版本已充分利用，並建立在之上 `containerd` ，如上所示。

透過以架構為 `containerd` 基礎的節點和節點集區， `dockershim` kubelet 會直接透過 `containerd` CRI (容器執行時間介面) 外掛程式，在流程上移除額外的躍點，而不是與 Docker CRI 的執行方式進行交談。 因此，您會看到較佳的 pod 啟動延遲，以及較少的資源 (CPU 和記憶體) 使用量。

藉由使用 `containerd` AKS 節點，pod 啟動延遲會改善，且容器執行時間會減少節點資源耗用量。 這項新架構會啟用這些改進 `containerd` ，而 kubelet 會直接透過 CRI 外掛程式進行討論，而在 Moby/docker 架構中，kubelet 會在 `dockershim` 到達與 docker 引擎之前，在流程中使用 `containerd` 額外的躍點。

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` 適用于 AKS 中 Kubernetes 的每個 GA 版本，以及1.19 上每個上游 Kubernetes 版本中的，並支援所有 Kubernetes 和 AKS 功能。

> [!IMPORTANT]
> 在 Kubernetes v 1.19 上建立的節點集區或更高 `containerd` 的叢集，其容器執行時間的預設值為。 在支援的 Kubernetes 版本上，節點集區的叢集會在其容器執行時間小於1.19 接收 `Moby` ，但會在 `ContainerD` 節點集區 Kubernetes 版本更新為 v 1.19 或更高版本時更新為。 您仍然可以 `Moby` 在舊版支援的版本上使用節點集區和叢集，直到這些版本支援為止。
> 
> 強烈建議您在 `containerD` 使用1.19 或更高版本的叢集之前，先在 AKS 節點集區上測試您的工作負載。

下一節將說明如何使用容器執行時間設定預覽版， `containerD` 在尚未使用 Kubernetes 1.19 版或更高版本的叢集上使用和測試 AKS，或在此功能正式推出之前建立。

### <a name="use-containerd-as-your-container-runtime-preview"></a>`containerd` (預覽版使用作為容器執行時間) 

您必須具備下列先決條件：

- 已安裝[Azure CLI][azure-cli-install]2.8.0 版或更新版本
- Aks-preview 延伸模組版本0.4.53 或更新版本
- `UseCustomizedContainerRuntime`已登錄功能旗標
- `UseCustomizedUbuntuPreview`已登錄功能旗標

若要安裝 aks-preview 0.4.53 擴充功能或更新版本，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension list
```

註冊 `UseCustomizedContainerRuntime` 和 `UseCustomizedUbuntuPreview` 功能：

```azurecli
az feature register --name UseCustomizedContainerRuntime --namespace Microsoft.ContainerService
az feature register --name UseCustomizedUbuntuPreview --namespace Microsoft.ContainerService

```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>`containerd`在新叢集上使用 (預覽版) 

設定要在建立叢集時使用的叢集 `containerd` 。 使用旗標將 `--aks-custom-headers` 設定 `containerd` 為容器執行時間。

> [!NOTE]
> `containerd`只有使用 AKS Ubuntu 18.04 映射的節點和節點集區才支援執行時間。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

如果您想要使用 Moby (docker) 執行時間來建立叢集，您可以省略自訂標籤來這麼做 `--aks-custom-headers` 。

### <a name="use-containerd-on-existing-clusters-preview"></a>`containerd`在現有的叢集上使用 (預覽版) 

設定要使用的新節點集區 `containerd` 。 使用旗標將 `--aks-custom-headers` 設定 `containerd` 為該節點集區的執行時間。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

如果您想要使用 Moby (docker) 執行時間來建立節點集區，您可以省略自訂標籤來這麼做 `--aks-custom-headers` 。


### <a name="containerd-limitationsdifferences"></a>`Containerd` 限制/差異

* 若要使用 `containerd` 作為容器執行時間，您必須使用 AKS Ubuntu 18.04 作為基礎 OS 映射。
* 雖然 docker 工具組仍然存在於節點上，但 Kubernetes 會使用 `containerd` 做為容器執行時間。 因此，因為 Moby/Docker 不會管理節點上 Kubernetes 建立的容器，所以您無法使用 Docker 命令 (例如 `docker ps`) 或 DOCKER API 來查看容器或與其互動。
* 針對 `containerd` ，我們建議您 [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) 改用取代 cli 來取代 Docker cli，以針對 Kubernetes 節點上的 pod、容器和容器映射進行 **疑難排解** (例如 `crictl ps`) 。 
   * 它不會提供 docker CLI 的完整功能。 它僅供疑難排解之用。
   * `crictl` 提供更容易 kubernetes 的容器視圖，其中有像是 pod 等概念。
* `Containerd` 使用標準化記錄格式來設定記錄 `cri` (這與您目前從 docker 的 json 驅動程式) 所取得的格式不同。 您的記錄解決方案必須支援 `cri` 記錄格式 (例如 [容器的 Azure 監視器](../azure-monitor/insights/container-insights-enable-new-cluster.md)) 
* 您無法再存取 docker 引擎， `/var/run/docker.sock` 或使用 docker (DinD) 。
  * 如果您目前已從 Docker 引擎解壓縮應用程式記錄或監視資料，請改用類似于 [容器的 Azure 監視器](../azure-monitor/insights/container-insights-enable-new-cluster.md) 。 此外，AKS 不支援在可能導致不穩定的代理程式節點上執行任何頻外命令。
  * 即使在使用 Moby/docker 時，強烈建議您不要透過上述方法建立映射並直接利用 docker 引擎。 Kubernetes 並不完全知道這些耗用的資源，而這些方法會 [在這裡](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) 提供許多 [問題，例如](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)。
* 建立映射-建立映射的建議方法是使用 [ACR 工作](../container-registry/container-registry-quickstart-task-cli.md)。 替代方法是使用更安全的叢集內選項，例如 [docker buildx](https://github.com/docker/buildx)。

## <a name="generation-2-virtual-machines-preview"></a>第2代虛擬機器 (預覽) 

Azure 支援 [ (Gen2) 虛擬機器 (vm) 的第2代虛擬機器 ](../virtual-machines/generation-2.md)。 第2代 Vm 支援第1代 Vm 中不支援的主要功能 (Gen1) 。 這些功能包括記憶體增加、Intel Software Guard Extensions (Intel SGX) 和虛擬化的持續性記憶體 (vPMEM)。

第 2 代 VM 捨棄第 1 代 VM 所使用的 BIOS 架構，改用新式的 UEFI 開機架構。
只有特定的 Sku 和大小支援 Gen2 Vm。 檢查 [支援的大小清單](../virtual-machines/generation-2.md#generation-2-vm-sizes)，以查看您的 SKU 是否支援或需要 Gen2。

此外，並非所有的 VM 映射都支援 Gen2，在 AKS Gen2 Vm 上，將會使用新的 [AKS Ubuntu 18.04 映射](#os-configuration)。 此映射支援所有 Gen2 Sku 和大小。

若要在預覽期間使用 Gen2 Vm，您需要：
- `aks-preview`已安裝 CLI 擴充功能。
- `Gen2VMPreview`已登錄功能旗標。

註冊 `Gen2VMPreview` 功能：

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

若要安裝 aks-preview CLI 擴充功能，請使用下列 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
```

若要更新 aks-preview CLI 擴充功能，請使用下列 Azure CLI 命令：

```azurecli
az extension update --name aks-preview
```

### <a name="use-gen2-vms-on-new-clusters-preview"></a>在新的叢集上使用 Gen2 的 Vm (Preview) 
設定叢集，以在建立叢集時，針對選取的 SKU 使用 Gen2 Vm。 使用 `--aks-custom-headers` 旗標，在新叢集上將 Gen2 設定為 VM 產生。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

如果您想要使用第1代 (Gen1) Vm 來建立一般叢集，您可以省略自訂標籤來這麼做 `--aks-custom-headers` 。 您也可以選擇新增更多 Gen1 或 Gen2 Vm，如下所示。

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>在現有的叢集上使用 Gen2 Vm (Preview) 
將新的節點集區設定為使用 Gen2 Vm。 使用 `--aks-custom-headers` 旗標，將 Gen2 設定為該節點集區的 VM 世代。

```azurecli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

如果您想要建立一般 Gen1 節點集區，您可以省略自訂標籤來這麼做 `--aks-custom-headers` 。


## <a name="ephemeral-os"></a>暫時 OS

根據預設，Azure 會自動將虛擬機器的作業系統磁片複寫到 Azure 儲存體，以避免 VM 需要重新放置至另一部主機時遺失資料。 不過，因為容器不是設計成要保存本機狀態，所以此行為可提供有限的價值，並提供一些缺點，包括較慢的節點布建和更高的讀取/寫入延遲。

相反地，暫時作業系統磁片只會儲存在主機電腦上，就像暫存磁片一樣。 這可提供較低的讀取/寫入延遲，以及更快速的節點擴充和叢集升級。

如同暫存磁片，虛擬機器的價格包含暫時性的 OS 磁片，因此不會產生額外的儲存體成本。

> [!IMPORTANT]
>當使用者未明確要求 OS 的受控磁片時，如果指定的 nodepool 設定可能的話，AKS 會預設為暫時 OS。

使用暫時性 OS 時，OS 磁片必須符合 VM 快取的大小。 VM 快取的大小可在 [Azure 檔](../virtual-machines/dv3-dsv3-series.md) 中以括弧括住，在 IO 輸送量 ( 「GiB 中的快取大小」 ) 。

使用 AKS 預設 VM 大小 Standard_DS2_v2 預設 OS 磁片大小為100GB 作為範例，此 VM 大小支援暫時性 OS，但只有86GB 快取大小。 如果使用者未明確指定，此設定會預設為受控磁片。 如果使用者明確要求暫時 OS，則會收到驗證錯誤。

如果使用者向 60GB OS 磁片要求相同的 Standard_DS2_v2，此設定會預設為暫時 OS：要求的60GB 大小小於86GB 的快取大小上限。

使用 Standard_D8s_v3 搭配100GB 的 OS 磁片時，此 VM 大小可支援暫時性 OS，並200GB 快取空間。 如果使用者未指定 OS 磁片類型，則 nodepool 預設會收到暫時 OS。 

暫時 OS 至少需要 Azure CLI 的版本2.15.0。

### <a name="use-ephemeral-os-on-new-clusters"></a>在新叢集上使用暫時 OS

將叢集設定為在建立叢集時使用暫時作業系統磁片。 使用旗標將 `--node-osdisk-type` 暫時 OS 設定為新叢集的 os 磁片類型。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

如果您想要使用網路連接的 OS 磁片來建立一般叢集，您可以藉由指定來建立 `--node-osdisk-type=Managed` 。 您也可以選擇新增更多暫時 OS 節點集區，如下所示。

### <a name="use-ephemeral-os-on-existing-clusters"></a>在現有的叢集上使用暫時 OS
將新的節點集區設定為使用暫時 OS 磁片。 使用 `--node-osdisk-type` 旗標，將 os 磁片類型設定為該節點集區的 os 磁片類型。

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> 使用暫時性作業系統時，您可以將 VM 和實例映射部署到 VM 快取的大小。 在 AKS 案例中，預設節點 OS 磁片設定會使用128GB，這表示您需要的 VM 大小必須大於128GB 的快取。 預設 Standard_DS2_v2 的快取大小為86GB，但不夠大。 Standard_DS3_v2 的快取大小為172GB，它夠大。 您也可以使用來減少 OS 磁片的預設大小 `--node-osdisk-size` 。 AKS 影像的大小下限為30GB。 

如果您想要使用網路連接的 OS 磁片來建立節點集區，您可以指定來建立節點集區 `--node-osdisk-type Managed` 。

## <a name="custom-resource-group-name"></a>自訂資源群組名稱

當您在 Azure 中部署 Azure Kubernetes Service 叢集時，系統會建立背景工作角色節點的第二個資源群組。 根據預設，AKS 會將該節點資源群組命名為 `MC_resourcegroupname_clustername_location`，但也可以自行命名。

若要指定屬於自己的資源群組名稱，請安裝 aks-preview Azure CLI 擴充功能 0.3.2 或以後版本。 若要使用 Azure CLI，請使用 `az aks create` 命令的 `--node-resource-group` 參數指定資源群組的自訂名稱。 如果使用 Azure Resource Manager 範本部署 AKS 叢集，則可使用 `nodeResourceGroup` 屬性來定義資源群組名稱。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

您自己訂閱內的 Azure 資源提供者，會自動建立次要資源群組。 建立叢集時，您只能指定自訂資源群組的名稱。 

當使用節點資源群組時，您無法進行以下動作：

- 指定現有的資源群組做為節點資源群組。
- 為節點資源群組指定不同的訂閱。
- 在建立叢集之後，變更節點資源群組名稱。
- 指定節點資源群組內受控資源的名稱。
- 修改或刪除節點資源群組內由 Azure 建立的受控資源標籤。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [升級叢集中的節點映射](node-image-upgrade.md) 。
- 請參閱[升級 Azure Kubernetes Service (AKS) 叢集](upgrade-cluster.md)，了解如何將叢集升級至最新版本的 Kubernetes。
- 閱讀更多關於[ `containerd` 和 Kubernetes 的](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)資訊
- 關於 AKS 常見問題的解答，請參閱 [AKS 的常見問題集](faq.md)。
- 深入瞭解 [暫時作業系統磁片](../virtual-machines/ephemeral-os-disks.md)。


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
