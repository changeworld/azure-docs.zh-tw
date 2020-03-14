---
title: 建立私用 Azure Kubernetes Service 叢集
description: 瞭解如何建立私用 Azure Kubernetes Service （AKS）叢集
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: b8b4f8062d9f60648e22ab4eb0be78eb47159834
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79205176"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>建立私用 Azure Kubernetes Service 叢集

在私人叢集中，控制平面或 API 伺服器具有在[私人網際網路的 RFC1918 位址配置](https://tools.ietf.org/html/rfc1918)檔中所定義的內部 IP 位址。 藉由使用私人叢集，您可以確保您的 API 伺服器與節點集區之間的網路流量只會保留在私人網路上。

控制平面或 API 伺服器位於 Azure Kubernetes Service （AKS）管理的 Azure 訂用帳戶中。 客戶的叢集或節點集區位於客戶的訂用帳戶中。 伺服器和叢集或節點集區可以透過 API 伺服器虛擬網路中的[Azure 私人連結服務][private-link-service]，以及在客戶 AKS 叢集的子網中公開的私用端點，彼此通訊。

## <a name="prerequisites"></a>Prerequisites

* Azure CLI 版2.2.0 或更新版本

## <a name="create-a-private-aks-cluster"></a>建立私用 AKS 叢集

### <a name="create-a-resource-group"></a>建立資源群組

建立資源群組，或為您的 AKS 叢集使用現有的資源群組。

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

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

## <a name="options-for-connecting-to-the-private-cluster"></a>用來連接到私人叢集的選項

API 伺服器端點沒有公用 IP 位址。 若要管理 API 伺服器，您必須使用可存取 AKS 叢集之 Azure 虛擬網路（VNet）的 VM。 有數個選項可讓您建立與私人叢集的網路連線。

* 在與 AKS 叢集相同的 Azure 虛擬網路（VNet）中建立 VM。
* 在不同的網路中使用 VM，並設定[虛擬網路對等互連][virtual-network-peering]。  如需此選項的詳細資訊，請參閱下一節。
* 使用[Express Route 或 VPN][express-route-or-VPN]連線。

在與 AKS 叢集相同的 VNET 中建立 VM 是最簡單的選項。  Express Route 和 Vpn 增加了成本，而且需要額外的網路複雜性。  虛擬網路對等互連會要求您規劃網路 CIDR 範圍，以確保沒有重迭的範圍。

## <a name="virtual-network-peering"></a>虛擬網路對等互連

如前所述，VNet 對等互連是存取私人叢集的一種方式。 若要使用 VNet 對等互連，您需要設定虛擬網路與私人 DNS 區域之間的連結。
    
1. 移至 Azure 入口網站中的 MC_ * 資源群組。  
2. 選取私人 DNS 區域。   
3. 在左窗格中，選取 [**虛擬網路**] 連結。  
4. 建立新的連結，將 VM 的虛擬網路新增至私人 DNS 區域。 需要幾分鐘的時間，DNS 區域連結才會變成可用。  
5. 回到 Azure 入口網站中的 MC_ * 資源群組。  
6. 在右窗格中，選取 [虛擬網路]。 虛擬網路名稱的格式為*aks-vnet-\** 。  
7. 在左窗格中，選取 [**對等互連**]。  
8. 選取 [**新增**]，新增 VM 的虛擬網路，然後建立對等互連。  
9. 移至您擁有 VM 的虛擬網路，選取 [**對等互連**]，選取 [AKS] 虛擬網路，然後建立對等互連。 如果 AKS 虛擬網路上的位址範圍和 VM 的虛擬網路衝突，對等互連會失敗。 如需詳細資訊，請參閱[虛擬網路對等互連][virtual-network-peering]。

## <a name="dependencies"></a>相依性  

* 僅標準 Azure Load Balancer 支援私用連結服務。 不支援基本 Azure Load Balancer。  
* 若要使用自訂 DNS 伺服器，請使用 DNS 部署 AD 伺服器以轉寄至此 IP 168.63.129.16

## <a name="limitations"></a>限制 
* IP 授權範圍無法套用至私用 api 伺服器端點，它們只適用于公用 API 伺服器
* 可用性區域目前支援特定地區，請參閱本檔的開頭 
* [Azure 私人連結服務限制][private-link-service]適用于私人叢集、azure 私人端點和虛擬網路服務端點，但目前在相同的虛擬網路中並不支援。
* 不支援私人叢集中的虛擬節點來微調私人 Azure 虛擬網路中的私人 Azure 容器實例（ACI）
* 不支援使用私人叢集的現成 Azure DevOps 整合
* 對於需要啟用 Azure Container Registry 以使用私用 AKS 的客戶，必須使用代理程式叢集虛擬網路來對等互連 Container Registry 虛擬網路。
* 目前不支援 Azure Dev Spaces
* 不支援將現有的 AKS 叢集轉換成私人叢集
* 刪除或修改客戶子網中的私用端點，會導致叢集停止運作。 
* 目前不支援容器即時資料的 Azure 監視器。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

