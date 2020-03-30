---
title: 創建專用 Azure 庫伯奈斯服務群集
description: 瞭解如何創建專用 Azure 庫伯奈斯服務 （AKS） 群集
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: cdefcfe460a97f647afa05947e92fae0c4d07001
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499297"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>創建專用 Azure 庫伯奈斯服務群集

在專用群集中，控制平面或 API 伺服器具有[在 RFC1918 - 專用 Internet](https://tools.ietf.org/html/rfc1918)位址分配文檔中定義的內部 IP 位址。 通過使用專用群集，可以確保 API 伺服器和節點池之間的網路流量僅保留在私人網路絡上。

控制平面或 API 伺服器位於 Azure 庫伯奈斯服務 （AKS） 管理的 Azure 訂閱中。 客戶的群集或節點池位於客戶的訂閱中。 伺服器和群集或節點池可以通過 API 伺服器虛擬網路中的[Azure 專用連結服務][private-link-service]以及在客戶 AKS 群集的子網中公開的專用終結點進行通信。

## <a name="prerequisites"></a>Prerequisites

* Azure CLI 版本 2.2.0 或更高版本

## <a name="create-a-private-aks-cluster"></a>創建專用 AKS 群集

### <a name="create-a-resource-group"></a>建立資源群組

為 AKS 群集創建資源組或使用現有資源組。

```azurecli-interactive
az group create -l westus -n MyResourceGroup
```

### <a name="default-basic-networking"></a>預設基本網路 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
*其中 --啟用-私有群集*是專用群集的必填標誌。 

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
*其中 --啟用-私有群集*是專用群集的必填標誌。 

> [!NOTE]
> 如果 Docker 橋接器位址 CIDR （172.17.0.1/16） 與子網 CIDR 衝突，則相應地更改 Docker 橋接器位址。

## <a name="options-for-connecting-to-the-private-cluster"></a>連接到專用群集的選項

API 伺服器終結點沒有公共 IP 位址。 要管理 API 伺服器，您需要使用有權訪問 AKS 群集的 Azure 虛擬網路 （VNet） 的 VM。 建立與專用群集的網路連接有多種選項。

* 在與 AKS 群集相同的 Azure 虛擬網路 （VNet） 中創建 VM。
* 在單獨的網路中使用 VM 並設置[虛擬網路對等互連][virtual-network-peering]。  有關此選項的詳細資訊，請參閱以下部分。
* 使用[快速路由或 VPN][express-route-or-VPN]連接。

在與 AKS 群集相同的 VNET 中創建 VM 是最簡單的選擇。  快速路由和 VPN 會增加成本，並需要額外的網路複雜性。  虛擬網路對等互連要求您規劃網路 CIDR 範圍，以確保沒有重疊範圍。

## <a name="virtual-network-peering"></a>虛擬網路對等互連

如前所述，VNet 對等互連是訪問專用群集的一種方式。 要使用 VNet 對等互連，您需要在虛擬網路和專用 DNS 區域之間設置連結。
    
1. 轉到 Azure 門戶中的MC_* 資源組。  
2. 選擇專用 DNS 區域。   
3. 在左側窗格中，選擇**虛擬網路**連結。  
4. 創建新連結以將 VM 的虛擬網路添加到專用 DNS 區域。 DNS 區域連結需要幾分鐘才能可用。  
5. 返回 Azure 門戶中的MC_* 資源組。  
6. 在右側窗格中，選擇虛擬網路。 虛擬網路名稱以*aks-vnet- 的形式\**。  
7. 在左側窗格中，選擇**對等互連**。  
8. 選擇 **"添加**"，添加 VM 的虛擬網路，然後創建對等互連。  
9. 轉到具有 VM 的虛擬網路，選擇**對等互連**，選擇 AKS 虛擬網路，然後創建對等互連。 如果 AKS 虛擬網路上的位址範圍和 VM 的虛擬網路衝突，對等互連將失敗。 有關詳細資訊，請參閱[虛擬網路對等互連][virtual-network-peering]。

## <a name="dependencies"></a>相依性  

* 專用鏈路服務僅在標準 Azure 負載等化器上支援。 不支援基本 Azure 負載等化器。  
* 要使用自訂 DNS 伺服器，請使用 Azure DNS IP 168.63.129.16 作為自訂 DNS 伺服器中的上游 DNS 伺服器。

## <a name="limitations"></a>限制 
* IP 授權範圍不能應用於專用 api 伺服器終結點，它們僅適用于公共 API 伺服器
* 當前某些區域支援可用性區域，請參閱本文檔的開頭 
* [Azure 專用連結服務限制][private-link-service]適用于專用群集、Azure 專用終結點和虛擬網路服務終結點，這些終結點當前不受同一虛擬網路的支援。
* 不支援專用群集中的虛擬節點在專用 Azure 虛擬網路中旋轉專用 Azure 容器實例 （ACI）
* 不支援與專用群集開箱即用的 Azure DevOps 集成
* 對於需要啟用 Azure 容器註冊表以使用專用 AKS 的客戶，容器註冊表虛擬網路必須與代理群集虛擬網路對等。
* 當前不支援 Azure 開發空間
* 不支援將現有 AKS 群集轉換為專用群集
* 刪除或修改客戶子網中的專用終結點將導致群集停止運行。 
* 當前不支援容器即時資料的 Azure 監視器。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

