---
title: 建立私人 Azure Kubernetes Service 叢集
description: 了解如何建立私人 Azure Kubernetes Service (AKS) 叢集
services: container-service
ms.topic: article
ms.date: 7/17/2020
ms.openlocfilehash: 696ba785abb317a29de38160440dc06487ff5bca
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673880"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>建立私人 Azure Kubernetes Service 叢集

在私人叢集中，控制平面或 API 伺服器具有在 [私人網際網路檔的 RFC1918 位址配置](https://tools.ietf.org/html/rfc1918) 中定義的內部 IP 位址。 您可以使用私人叢集，確保您的 API 伺服器與節點集區之間的網路流量，只會保留在私人網路中。

控制平面或 API 伺服器會位於 Azure Kubernetes Service (AKS) 管理的 Azure 訂用帳戶中。 客戶的叢集或節點集區會位於客戶的訂用帳戶中。 伺服器和叢集或節點集區可以透過 API 伺服器虛擬網路中的 [Azure Private Link 服務][private-link-service]，以及在客戶 AKS 叢集子網路中公開的私人端點，彼此通訊。

## <a name="region-availability"></a>區域可用性

私人叢集可在 [支援 AKS](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)的公用區域、Azure Government 和 Azure 中國世紀區域中使用。

> [!NOTE]
> 支援 Azure Government 網站，但因為缺少 Private Link 支援，所以目前不支援 US Gov 德克薩斯州。

## <a name="prerequisites"></a>Prerequisites

* Azure CLI 2.2.0 版或更新版本
* 僅 Standard Azure Load Balancer 支援 Private Link 服務。 不支援 Basic Azure Load Balancer。  
* 若要使用自訂 DNS 伺服器，請在自訂 DNS 伺服器中新增 Azure DNS IP 168.63.129.16 作為上游 DNS 伺服器。

## <a name="create-a-private-aks-cluster"></a>建立私人 AKS 叢集

### <a name="create-a-resource-group"></a>建立資源群組

為您的 AKS 叢集建立資源群組，或使用現有的資源群組。

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>預設的基本網路 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
其中 `--enable-private-cluster` 是私人叢集的強制旗標。 

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
其中 `--enable-private-cluster` 是私人叢集的強制旗標。 

> [!NOTE]
> 如果 Docker 橋接器位址 CIDR (172.17.0.1/16) 與子網路 CIDR 衝突，請適當地變更 Docker 橋接器位址。

### <a name="configure-private-dns-zone"></a>設定私人 DNS 區域

如果省略--private-dns-zone 引數，則預設值為 "system"。 AKS 會在節點資源群組中建立私人 DNS 區域。 傳遞 "none" 參數表示 AKS 將不會建立私人 DNS 區域。  這取決於攜帶您自己的 DNS 伺服器，以及設定私人 FQDN 的 DNS 解析。  如果您未設定 DNS 解析，則只能在代理程式節點內解析 DNS，並且在部署後會導致叢集問題。

## <a name="no-private-dns-zone-prerequisites"></a>沒有私人 DNS 區域必要條件
無 PrivateDNSZone
* Azure CLI 0.4.67 版或更新版本
* Api 版本2020-11-01 或更新版本

## <a name="create-a-private-aks-cluster-with-private-dns-zone"></a>使用私人 DNS 區域建立私人 AKS 叢集

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster --private-dns-zone [none|system]
```
## <a name="options-for-connecting-to-the-private-cluster"></a>用來連線到私人叢集的選項

API 伺服器端點沒有公用 IP 位址。 若要管理 API 伺服器，您必須使用可存取 AKS 叢集的 Azure 虛擬網路 (VNet) 的 VM。 有數個選項可讓您與私人叢集建立網路連線。

* 在與 AKS 叢集相同的 Azure 虛擬網路 (VNet) 中建立 VM。
* 在不同的網路中使用 VM，並設定[虛擬網路對等互連][virtual-network-peering]。  如需此選項的詳細資訊，請參閱下一節。
* 使用 [Express Route 或 VPN][express-route-or-VPN] 連線。

在與 AKS 叢集相同的 VNET 中建立 VM 是最簡單的選項。  Express Route 和 VPN 會增加成本，而且需要額外的網路複雜性。  虛擬網路對等互連會要求您規劃網路 CIDR 範圍，以確保沒有重疊的範圍。

## <a name="virtual-network-peering"></a>虛擬網路對等互連

如前所述，虛擬網路對等互連是存取私人叢集的一種方式。 若要使用虛擬網路對等互連，您必須設定虛擬網路與私人 DNS 區域之間的連結。
    
1. 移至 Azure 入口網站中的 node 資源群組。  
2. 選取私人 DNS 區域。   
3. 在左側窗格中，選取 [虛擬網路連結]。  
4. 建立新的連結，將 VM 的虛擬網路新增至私人 DNS 區域。 需要幾分鐘的時間，DNS 區域連結才會變成可供使用。  
5. 在 Azure 入口網站中，流覽至包含叢集虛擬網路的資源群組。  
6. 在右側窗格中，選取虛擬網路。 虛擬網路名稱的格式為 aks-vnet-\*。  
7. 在左側窗格中選取 [對等互連]。  
8. 選取 [新增]、新增 VM 的虛擬網路，然後建立對等互連。  
9. 移至您擁有 VM 的虛擬網路，選取 [對等互連]、選取 AKS 虛擬網路，然後建立對等互連。 如果 AKS 虛擬網路上的位址範圍和 VM 的虛擬網路衝突，對等互連將會失敗。 如需詳細資訊，請參閱[虛擬網路對等互連][virtual-network-peering]。

## <a name="hub-and-spoke-with-custom-dns"></a>具有自訂 DNS 的中樞和輪輻

[中樞和輪輻架構](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)通常用來在 Azure 中部署網路。 在其中許多部署中，輪輻 VNet 中的 DNS 設定會設定為參考中央 DNS 轉寄站，以允許用於內部部署和以 Azure 為基礎的 DNS 解析。 將 AKS 叢集部署到這類的網路環境時，有一些特殊的考量必須納入考慮。

![私人叢集中樞和輪輻](media/private-clusters/aks-private-hub-spoke.png)

1. 根據預設，布建私人叢集時，會在叢集管理的資源群組中建立私人端點 (1) 和私人 DNS 區域 (2) 。 叢集會使用私人區域中的 A 記錄來解析私人端點的 IP，以便與 API 伺服器通訊。

2. 私人 DNS 區域只會連結至叢集節點所連結的 VNet (3)。 這表示私人端點只能由該連結 VNet 中的主機解析。 在 VNet 上未設定任何自訂 DNS (預設) 的情況下，這項作業不會發生問題，因為 168.63.129.16 for DNS 的主機點可解決私人 DNS 區域中的記錄，因為連結。

3. 在包含叢集的 VNet 有自訂 DNS 設定 (4) 的情況下，除非私人 DNS 區域已連結至包含自訂 DNS 解析程式 (5) 的 VNet，否則叢集部署將會失敗。 在叢集布建期間建立私人區域，或在使用事件型部署 (機制（例如，Azure 事件方格和 Azure Functions) ）偵測到建立區域時，可以手動建立此連結。

> [!NOTE]
> 如果您使用「 [攜帶您自己的路由表與 kubenet](https://docs.microsoft.com/azure/aks/configure-kubenet#bring-your-own-subnet-and-route-table-with-kubenet) ，並將您自己的 DNS 與私人叢集整合，叢集建立將會失敗。 在叢集建立失敗之後，您必須將節點資源群組中的 [RouteTable](https://docs.microsoft.com/azure/aks/configure-kubenet#bring-your-own-subnet-and-route-table-with-kubenet) 與子網產生關聯，才能成功建立。

## <a name="limitations"></a>限制 
* IP 授權範圍無法套用至私人 api 伺服器端點，只適用于公用 API 伺服器
* [Azure Private Link 服務限制][private-link-service]適用於私人叢集。
* 不支援搭配私人叢集使用 Azure DevOps Microsoft 裝載的代理程式。 請考慮使用[自我裝載代理程式](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser&preserve-view=true)。 
* 對於需要啟用 Azure Container Registry 以使用私人 AKS 的客戶，必須以代理程式叢集虛擬網路來與 Container Registry 虛擬網路對等互連。
* 不支援將現有的 AKS 叢集轉換成私人叢集
* 刪除或修改客戶子網路中的私人端點，會導致叢集停止運作。 
* 目前不支援適用於容器的 Azure 監視器即時資料。
* 當客戶在自己的 DNS 伺服器上更新 A 記錄之後，這些 pod 仍會在遷移後將 apiserver FQDN 解析為較舊的 IP，直到重新開機為止。 客戶必須在控制平面遷移之後，重新開機 hostNetwork pod 和預設 DNSPolicy pod。
* 在控制平面上進行維護的情況下，您的 [AKS IP](https://docs.microsoft.com/azure/aks/limit-egress-traffic#:~:text=By%20default%2C%20AKS%20clusters%20have%20unrestricted%20outbound%20%28egress%29,be%20accessible%20to%20maintain%20healthy%20cluster%20maintenance%20tasks.) 可能會變更。 在此情況下，您必須在自訂 DNS 伺服器上將指向 API 伺服器私人 IP 的 A 記錄更新為，然後使用 hostNetwork 重新開機任何自訂 pod 或部署。

<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: ../private-link/private-link-service-overview.md#limitations
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md
[devops-agents]: /azure/devops/pipelines/agents/agents?view=azure-devops
[availability-zones]: availability-zones.md