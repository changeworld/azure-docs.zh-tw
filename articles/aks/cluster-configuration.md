---
title: Azure Kubernetes Service (AKS) 的叢集設定
description: 了解如何在 Azure Kubernetes Service (AKS) 中設定叢集
services: container-service
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: c3123d22d2a13be9b9e5360e82990ba3a6320b1a
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008792"
---
# <a name="configure-an-aks-cluster"></a>設定 AKS 叢集

建立 AKS 叢集的過程中，可能需要自訂叢集設定來配合您的需求。 本文會介紹幾種可用來自訂 AKS 叢集的選項。

## <a name="os-configuration-preview"></a>作業系統設定 (預覽)

AKS 現可支援 Ubuntu 18.04 做為預覽的節點作業系統 (OS)。 在預覽期間，Ubuntu 16.04 和 Ubuntu 18.04 皆可供使用。

> [!IMPORTANT]
> 在 Kubernetes v 1.18 或更高版本上建立的節點集區預設為必要的 `AKS Ubuntu 18.04` 節點映射。 低於1.18 的支援 Kubernetes 版本上的節點集區會 `AKS Ubuntu 16.04` 以節點映射的形式接收，但 `AKS Ubuntu 18.04` 當節點集區 Kubernetes 版本更新為1.18 或更大時，就會更新為。
> 
> 強烈建議您先在 AKS Ubuntu 18.04 節點集區上測試您的工作負載，再使用1.18 或更高版本的叢集。 瞭解如何[測試 Ubuntu 18.04 節點](#use-aks-ubuntu-1804-existing-clusters-preview)集區。

您必須先安裝下列資源：

- [Azure CLI][azure-cli-install]，2.2.0 或更新版本
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

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="use-aks-ubuntu-1804-on-new-clusters-preview"></a>在 (預覽) 的新叢集上使用 AKS Ubuntu 18.04

在叢集建立時，請將叢集設定為使用 Ubuntu 18.04。 請使用 `--aks-custom-headers` 旗標將 Ubuntu 18.04 設定為預設作業系統。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果您想要使用 AKS Ubuntu 16.04 映射來建立叢集，您可以省略自訂標記來執行此動作 `--aks-custom-headers` 。

### <a name="use-aks-ubuntu-1804-existing-clusters-preview"></a>使用 AKS Ubuntu 18.04 現有的叢集 (預覽) 

請將新的節點集區設定為使用 Ubuntu 18.04。 請使用 `--aks-custom-headers` 旗標，將 Ubuntu 18.04 設定為該節點集區的預設作業系統。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804
```

如果您想要使用 AKS Ubuntu 16.04 映射建立節點集區，您可以省略自訂標記來執行此動作 `--aks-custom-headers` 。


## <a name="container-runtime-configuration-preview"></a>容器執行時間設定 (預覽) 

容器執行時間是一種軟體，可執行容器並管理節點上的容器映射。 執行時間有助於抽象地脫離 sys 呼叫或作業系統 (OS) 特定功能，在 Linux 或 Windows 上執行容器。 今日 AKS 會使用[Moby](https://mobyproject.org/) (上游 docker) 作為其容器執行時間。 
    
![Docker CRI](media/cluster-configuration/docker-cri.png)

[`Containerd`](https://containerd.io/)是一個[OCI](https://opencontainers.org/) (開放容器計畫) 相容的核心容器執行時間，可提供執行容器和管理節點上之映射所需的最小功能集。 它是在2017年3月 (由 CNCF) 的雲端原生計算基礎中[捐贈](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/)。 AKS 目前使用的目前 Moby 版本已利用，並建立在之上，如上 `containerd` 所示。 

使用以 containerd 為基礎的節點和節點集區，而不是與交談時， `dockershim` kubelet 會直接透過 `containerd` CRI (容器執行時間介面) 外掛程式來交談，並在與 Docker CRI 執行相較之下，移除流程上的額外躍點。 因此，您會看到較佳的 pod 啟動延遲和較少的資源 (CPU 和記憶體) 使用量。

藉由使用 `containerd` AKS 節點，pod 啟動延遲會改善，而容器執行時間的節點資源耗用量也會減少。 這項新架構會啟用這些改良功能，其中 kubelet 會直接與 `containerd` CRI 外掛程式交談，而在 Moby/docker 架構中 kubelet 會在 `dockershim` 到達之前與 docker 引擎通訊 `containerd` ，因此在流程上有額外的躍點。

![Docker CRI](media/cluster-configuration/containerd-cri.png)

`Containerd`適用于 AKS 中每個 GA 版本的 kubernetes，以及在每個在每個上游 kubernetes 版本的1.10 中，並支援所有 kubernetes 和 AKS 功能。

> [!IMPORTANT]
> 在 `containerd` AKS 正式推出之後，它將會是新叢集上容器執行時間的預設且唯一可用的選項。 您仍然可以在舊版支援的版本上使用 Moby nodepools 和叢集，直到這些叢集支援為止。 
> 
> 我們建議您先在節點集區上測試工作負載， `containerd` 再升級或使用此容器執行時間建立新的叢集。

### <a name="use-containerd-as-your-container-runtime-preview"></a>使用 `containerd` 做為您的容器執行時間 (預覽) 

您必須具備下列必要條件：

- 已安裝[Azure CLI][azure-cli-install]2.8.0 或更新版本
- Aks-preview 延伸模組版本0.4.53 或更新版本
- `UseCustomizedContainerRuntime`已註冊的功能旗標
- `UseCustomizedUbuntuPreview`已註冊的功能旗標

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

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedContainerRuntime')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedUbuntuPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

```azurecli
az provider register --namespace Microsoft.ContainerService
```  

### <a name="use-containerd-on-new-clusters-preview"></a>`containerd`在新叢集上使用 (預覽) 

設定叢集，以在 `containerd` 建立叢集時使用。 使用旗標將 `--aks-custom-headers` 設定 `containerd` 為容器執行時間。

> [!NOTE]
> `containerd`只有在使用 AKS Ubuntu 18.04 映射的節點和節點集區上，才支援執行時間。

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

如果您想要使用 Moby (docker) 執行時間建立叢集，您可以省略自訂標記來執行此動作 `--aks-custom-headers` 。

### <a name="use-containerd-on-existing-clusters-preview"></a>`containerd`在現有的叢集上使用 (預覽) 

設定要使用的新節點集區 `containerd` 。 使用旗標， `--aks-custom-headers` 將設定 `containerd` 為該節點集區的執行時間。

```azurecli
az aks nodepool add --name ubuntu1804 --cluster-name myAKSCluster --resource-group myResourceGroup --aks-custom-headers CustomizedUbuntu=aks-ubuntu-1804,ContainerRuntime=containerd
```

如果您想要使用 Moby (docker) 執行時間建立節點集區，您可以省略自訂標記來執行此動作 `--aks-custom-headers` 。


### <a name="containerd-limitationsdifferences"></a>`Containerd`限制/差異

* 若要使用 `containerd` 做為容器執行時間，您必須使用 AKS Ubuntu 18.04 做為基本 OS 映射。
* 雖然 docker 工具組仍然存在於節點上，但 Kubernetes 會使用 `containerd` 做為容器執行時間。 因此，由於 Moby/Docker 不會管理節點上已 Kubernetes 建立的容器，因此您無法使用 Docker 命令來查看或與容器互動， (例如 `docker ps`) 或 DOCKER API。
* 針對 `containerd` ，建議您使用 [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) 作為取代 cli，而不是使用 Docker cli 來針對 Kubernetes 節點上的 pod、容器和容器映射**進行疑難排解** (例如 `crictl ps`) 。 
   * 它不會提供 docker CLI 的完整功能。 僅供疑難排解之用。
   * `crictl`提供更 kubernetes 易懂的容器，其中包含 pod 之類的概念等等。
* `Containerd`使用標準化記錄格式設定記錄 `cri` (這與您目前從 docker 的 json 驅動程式) 所取得的不同。 您的記錄解決方案需要支援 `cri` 記錄格式 (例如[容器的 Azure 監視器](../azure-monitor/insights/container-insights-enable-new-cluster.md)) 
* 您無法再存取 docker 引擎， `/var/run/docker.sock` 或使用 docker (DinD) 。
  * 如果您目前從 Docker 引擎解壓縮應用程式記錄檔或監視資料，請改用類似于[容器的 Azure 監視器](../azure-monitor/insights/container-insights-enable-new-cluster.md)。 此外，AKS 不支援在代理程式節點上執行任何可能造成不穩定的頻外命令。
  * 即使在使用 Moby/docker 時，強烈建議您不要透過上述方法來建立映射並直接利用 docker 引擎。 Kubernetes 並不完全察覺那些已耗用的資源，而這些方法會[在這裡](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)和[這裡](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/)詳述許多問題，例如。
* 建立映射-建立影像的建議方法是使用[ACR 工作](../container-registry/container-registry-quickstart-task-cli.md)。 另一種方法是使用更安全的叢集中選項，例如[docker buildx](https://github.com/docker/buildx)。

## <a name="generation-2-virtual-machines-preview"></a>第2代虛擬機器 (預覽) 

Azure 支援[第2代 (Gen2) 虛擬機器 (vm) ](../virtual-machines/windows/generation-2.md)。 第2代 Vm 支援第1代 Vm 中不支援的主要功能 (Gen1) 。 這些功能包括記憶體增加、Intel Software Guard Extensions (Intel SGX) 和虛擬化的持續性記憶體 (vPMEM)。

第 2 代 VM 捨棄第 1 代 VM 所使用的 BIOS 架構，改用新式的 UEFI 開機架構。
只有特定的 Sku 和大小支援 Gen2 Vm。 檢查[支援的大小清單](../virtual-machines/windows/generation-2.md#generation-2-vm-sizes)，以查看您的 SKU 是否支援或需要 Gen2。

此外，並非所有 VM 映射都支援 Gen2，而在 AKS Gen2 Vm 上，則會使用新的[AKS Ubuntu 18.04 映射](#os-configuration-preview)。 此映射支援所有的 Gen2 Sku 和大小。

若要在預覽期間使用 Gen2 Vm，您需要：
- `aks-preview`已安裝 CLI 擴充功能。
- `Gen2VMPreview`已註冊的功能旗標。

註冊 `Gen2VMPreview` 功能：

```azurecli
az feature register --name Gen2VMPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/Gen2VMPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

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

### <a name="use-gen2-vms-on-new-clusters-preview"></a>在 (預覽) 的新叢集上使用 Gen2 Vm
將叢集設定為在建立叢集時，針對選取的 SKU 使用 Gen2 Vm。 使用 `--aks-custom-headers` 旗標，將 Gen2 設定為新叢集上的 VM 世代。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

如果您想要使用第1代 (Gen1) Vm 來建立一般叢集，您可以省略自訂標記來執行此動作 `--aks-custom-headers` 。 您也可以選擇新增更多 Gen1 或 Gen2 Vm，如下所示。

### <a name="use-gen2-vms-on-existing-clusters-preview"></a>在現有的叢集上使用 Gen2 Vm (Preview) 
設定新的節點集區以使用 Gen2 Vm。 使用 `--aks-custom-headers` 旗標，將 Gen2 設定為該節點集區的 VM 世代。

```azure-cli
az aks nodepool add --name gen2 --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_D2s_v3 --aks-custom-headers usegen2vm=true
```

如果您想要建立一般 Gen1 節點集區，您可以省略自訂標記來執行此動作 `--aks-custom-headers` 。


## <a name="ephemeral-os-preview"></a>暫時 OS (預覽) 

根據預設，Azure 虛擬機器的作業系統磁片會自動複寫到 Azure 儲存體，以避免在 VM 需要重新放置到另一部主機時遺失資料。 不過，由於容器的設計不是要保存本機狀態，因此此行為會提供有限的價值，並提供一些缺點，包括較慢的節點布建和較低的讀取/寫入延遲。

相反地，暫時 OS 磁片只會儲存在主機電腦上，就像暫存磁片一樣。 這可提供較低的讀取/寫入延遲，以及更快的節點縮放和叢集升級。

就像暫存磁片一樣，暫時的 OS 磁片會包含在虛擬機器的價格中，因此您不會產生額外的儲存體成本。

註冊 `EnableEphemeralOSDiskPreview` 功能：

```azurecli
az feature register --name EnableEphemeralOSDiskPreview --namespace Microsoft.ContainerService
```

可能需要幾分鐘的時間，狀態才會顯示為 [已註冊]。 您可以使用 [az feature list](/cli/azure/feature?view=azure-cli-latest#az-feature-list) 命令檢查註冊狀態：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableEphemeralOSDiskPreview')].{Name:name,State:properties.state}"
```

當狀態顯示為已註冊時，請使用 [az provider register](/cli/azure/provider?view=azure-cli-latest#az-provider-register) 命令重新整理 `Microsoft.ContainerService` 資源提供者的註冊：

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

### <a name="use-ephemeral-os-on-new-clusters-preview"></a>在新叢集上使用暫時 OS (預覽) 

將叢集設定為在建立叢集時使用暫時的 OS 磁片。 使用 `--aks-custom-headers` 旗標，將暫時 OS 設定為新叢集的 os 磁片類型。

```azure-cli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

如果您想要使用網路連接的 OS 磁片建立一般叢集，可以省略自訂標記來執行此動作 `--aks-custom-headers` 。 您也可以選擇新增更多暫時 OS 節點集區，如下所示。

### <a name="use-ephemeral-os-on-existing-clusters-preview"></a>在現有叢集上使用暫時 OS (預覽) 
設定新的節點集區以使用暫時的 OS 磁片。 使用旗標將 `--aks-custom-headers` 設定為 os 磁片類型，做為該節點集區的 os 磁片類型。

```azure-cli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --aks-custom-headers EnableEphemeralOSDisk=true
```

> [!IMPORTANT]
> 有了暫時的 OS，您就可以將 VM 和實例映射部署到 VM 快取的大小。 在 AKS 案例中，預設節點 OS 磁片設定會使用100GiB，這表示您需要的 VM 大小必須大於 100 GiB 的快取。 預設 Standard_DS2_v2 的快取大小為 86 GiB，但不夠大。 Standard_DS3_v2 的快取大小為 172 GiB，這夠大。 您也可以使用來減少 OS 磁片的預設大小 `--node-osdisk-size` 。 AKS 映射的大小下限為30GiB。 

如果您想要使用網路連接的 OS 磁片來建立節點集區，您可以省略自訂標記來執行此動作 `--aks-custom-headers` 。

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

- 了解如何使用 `Kured`，在您的叢集中[將安全性和核心更新套用至 Linux 節點](node-updates-kured.md)。
- 請參閱[升級 Azure Kubernetes Service (AKS) 叢集](upgrade-cluster.md)，了解如何將叢集升級至最新版本的 Kubernetes。
- 閱讀更多有關[ `containerd` 和 Kubernetes 的](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)資訊
- 關於 AKS 常見問題的解答，請參閱 [AKS 的常見問題集](faq.md)。
- 深入瞭解[暫時 OS 磁片](../virtual-machines/ephemeral-os-disks.md)。


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
