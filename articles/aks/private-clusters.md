---
title: 建立私用 Azure Kubernetes Service 叢集
description: 瞭解如何建立私用 Azure Kubernetes Service （AKS）叢集
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: e59dccbcc7514f12e148bfb2f771593a53e85dc5
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594561"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>建立私人 Azure Kubernetes Service 叢集（預覽）

在私人叢集中，控制平面或 API 伺服器具有在[私人網際網路的 RFC1918 位址配置](https://tools.ietf.org/html/rfc1918)檔中所定義的內部 IP 位址。 藉由使用私人叢集，您可以確保您的 API 伺服器與節點集區之間的網路流量只會保留在私人網路上。

控制平面或 API 伺服器位於 Azure Kubernetes Service （AKS）管理的 Azure 訂用帳戶中。 客戶的叢集或節點集區位於客戶的訂用帳戶中。 伺服器和叢集或節點集區可以透過 API 伺服器虛擬網路中的[Azure 私人連結服務][private-link-service]，以及在客戶 AKS 叢集的子網中公開的私用端點，彼此通訊。

> [!IMPORTANT]
> AKS 預覽功能是自助服務，並以選擇為基礎提供。 預覽會依*原樣提供，並會*在服務等級協定（SLA *）和有限*擔保中排除。 AKS 預覽會以*最*大的方式由客戶支援部門部分涵蓋。 因此，這些功能不適用於生產用途。 如需詳細資訊，請參閱下列支援文章：
>
> * [AKS 支援原則](support-policies.md)
> * [Azure 支援常見問題集](faq.md)

## <a name="prerequisites"></a>Prerequisites

* Azure CLI 版2.0.77 或更新版本，以及 Azure CLI AKS Preview 延伸模組版本0.4.18

## <a name="currently-supported-regions"></a>目前支援的區域

* 澳大利亞東部
* 澳大利亞東南部
* 巴西南部
* 加拿大中部
* 加拿大東部
* Cenral 我們
* 東亞
* 美國東部
* 美國東部 2
* 美國東部 2 EUAP
* 法國中部
* 德國北部
* 日本東部
* 日本西部
* 南韓中部
* 南韓南部
* 美國中北部
* 北歐
* 北歐
* 美國中南部
* 英國南部
* 西歐
* 美國西部
* 美國西部 2
* 美國東部 2


## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>安裝最新的 Azure CLI AKS Preview 擴充功能

若要使用私人叢集，您需要 Azure CLI AKS Preview 延伸模組版本0.4.18 或更新版本。 使用[az extension add][az-extension-add]命令來安裝 Azure CLI AKS Preview 延伸模組，然後使用下列[az extension update][az-extension-update]命令來檢查是否有任何可用的更新：

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> 當您在訂用帳戶上註冊功能時，目前無法取消註冊該功能。 啟用一些預覽功能之後，您可以針對訂用帳戶中建立的所有 AKS 叢集使用預設設定。 請勿在生產訂用帳戶上啟用預覽功能。 使用個別的訂用帳戶來測試預覽功能並收集意見反應。

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

註冊狀態可能需要幾分鐘的時間才會顯示為*已註冊*。 您可以使用下列[az feature list][az-feature-list]命令來檢查狀態：

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

當狀態為 [已註冊] 時，請使用下列[az provider register][az-provider-register]命令重新整理*microsoft.containerservice*資源提供者的註冊：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>建立私用 AKS 叢集

### <a name="default-basic-networking"></a>預設基本網路 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
其中 *--啟用-私人-* 叢集是私人叢集的強制旗標。 

### <a name="advanced-networking"></a>進階網路  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
其中 *--啟用-私人-* 叢集是私人叢集的強制旗標。 

> [!NOTE]
> 如果 Docker 橋接器位址 CIDR （172.17.0.1/16）與子網 CIDR 衝突，請適當地變更 Docker 橋接器位址。

## <a name="connect-to-the-private-cluster"></a>連接到私人叢集
API 伺服器端點沒有公用 IP 位址。 因此，您必須在虛擬網路中建立 Azure 虛擬機器（VM），並連接到 API 伺服器。 若要這樣做，請執行下列動作：

1. 取得認證以連接到叢集。

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. 執行下列任一步驟：
   * 在與 AKS 叢集相同的虛擬網路中建立 VM。  
   * 請在不同的虛擬網路中建立 VM，並將此虛擬網路與 AKS 叢集虛擬網路對等互連。

     如果您在不同的虛擬網路中建立 VM，請設定此虛擬網路與私人 DNS 區域之間的連結。 若要這樣做：
    
     a. 移至 Azure 入口網站中的 MC_ * 資源群組。  
     b. 選取私人 DNS 區域。   
     c. 在左窗格中，選取 [**虛擬網路**] 連結。  
     d. 建立新的連結，將 VM 的虛擬網路新增至私人 DNS 區域。 需要幾分鐘的時間，DNS 區域連結才會變成可用。  
     e. 回到 Azure 入口網站中的 MC_ * 資源群組。  
     f. 在右窗格中，選取 [虛擬網路]。 虛擬網路名稱的格式為*aks-vnet-\** 。  
     g. 在左窗格中，選取 [**對等互連**]。  
     h. 選取 [**新增**]，新增 VM 的虛擬網路，然後建立對等互連。  
     i. 移至您擁有 VM 的虛擬網路，選取 [**對等互連**]，選取 [AKS] 虛擬網路，然後建立對等互連。 如果 AKS 虛擬網路上的位址範圍和 VM 的虛擬網路衝突，對等互連會失敗。 如需詳細資訊，請參閱[虛擬網路對等互連][virtual-network-peering]。

1. 透過安全殼層（SSH）存取 VM。
1. 安裝 Kubectl 工具，然後執行 Kubectl 命令。


## <a name="dependencies"></a>相依性  
* 僅標準 Azure Load Balancer 支援私用連結服務。 不支援基本 Azure Load Balancer。  
* 若要使用自訂 DNS 伺服器，請使用 DNS 部署 AD 伺服器以轉寄至此 IP 168.63.129.16

## <a name="limitations"></a>限制 
* 目前僅支援美國東部2和美國西部2區域的可用性區域
* [Azure 私人連結服務限制][private-link-service]適用于私人叢集、azure 私人端點和虛擬網路服務端點，但目前在相同的虛擬網路中並不支援。
* 不支援私人叢集中的虛擬節點來微調私人 Azure 虛擬網路中的私人 Azure 容器實例（ACI）
* 不支援使用私人叢集的現成 Azure DevOps 整合
* 對於需要啟用 Azure Container Registry 以使用私用 AKS 的客戶，必須使用代理程式叢集虛擬網路來對等互連 Container Registry 虛擬網路。
* 目前不支援 Azure Dev Spaces
* 不支援將現有的 AKS 叢集轉換成私人叢集
* 刪除或修改客戶子網中的私用端點，會導致叢集停止運作。 
* 目前不支援容器即時資料的 Azure 監視器。
* 目前不支援*攜帶您自己的 DNS* 。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

