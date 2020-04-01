---
title: 建立專用 Azure 庫伯奈斯服務群集
description: 瞭解如何建立專用 Azure 庫伯奈斯服務 (AKS) 叢集
services: container-service
ms.topic: article
ms.date: 2/21/2020
ms.openlocfilehash: 87f52c5a749b531e5b0656e0b30ff0fe9c1a57bf
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398060"
---
# <a name="create-a-private-azure-kubernetes-service-cluster"></a>建立專用 Azure 庫伯奈斯服務群集

在專用群集中,控制平面或 API 伺服器具有[在 RFC1918 - 專用 Internet](https://tools.ietf.org/html/rfc1918)位址分配文檔中定義的內部 IP 位址。 通過使用專用群集,可以確保 API 伺服器和節點池之間的網路流量僅保留在專用網路上。

控制平面或 API 伺服器位於 Azure 庫伯奈斯服務 (AKS) 管理的 Azure 訂閱中。 客戶的群集或節點池位於客戶的訂閱中。 伺服器和群集或節點池可以通過 API 伺服器虛擬網路中的[Azure 專用連結服務][private-link-service]以及在客戶 AKS 叢集的子網中公開的專用終結點進行通信。

## <a name="prerequisites"></a>Prerequisites

* Azure CLI 版本 2.2.0 或更高版本

## <a name="create-a-private-aks-cluster"></a>建立專用 AKS 叢集

### <a name="create-a-resource-group"></a>建立資源群組

為 AKS 群集建立資源組或使用現有資源組。

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
> 如果 Docker 閘橋位址 CIDR (172.17.0.1/16) 與子網 CIDR 衝突,則相應地更改 Docker 網橋位址。

## <a name="options-for-connecting-to-the-private-cluster"></a>連接到專用群組的選項

API 伺服器終結點沒有公共 IP 位址。 要管理 API 伺服器,您需要使用有權存取 AKS 群集的 Azure 虛擬網路 (VNet) 的 VM。 建立與專用群集的網路連接有多種選項。

* 在與 AKS 群集相同的 Azure 虛擬網路 (VNet) 中創建 VM。
* 在單獨的網路中使用 VM 並設定[虛擬網路對等互連][virtual-network-peering]。  有關此選項的詳細資訊,請參閱以下部分。
* 使用[快速路由或 VPN][express-route-or-VPN]連接。

在與 AKS 群集相同的 VNET 中創建 VM 是最簡單的選擇。  快速路由和 VPN 會增加成本,並需要額外的網路複雜性。  虛擬網路對等互連要求您規劃網路 CIDR 範圍,以確保沒有重疊範圍。

## <a name="virtual-network-peering"></a>虛擬網路對等互連

如前所述,VNet 對等互連是訪問專用群集的一種方式。 要使用 VNet 對等互連,您需要在虛擬網路和專用 DNS 區域之間設置連結。
    
1. 轉到 Azure 門戶中的MC_* 資源組。  
2. 選擇專用 DNS 區域。   
3. 在左側窗格中,選擇**虛擬網路**連結。  
4. 創建新連結以將 VM 的虛擬網路添加到專用 DNS 區域。 DNS 區域連結需要幾分鐘才能可用。  
5. 返回 Azure 門戶中的MC_* 資源組。  
6. 在右側窗格中,選擇虛擬網路。 虛擬網路名稱以*aks-vnet-\*的形式*。  
7. 在左邊窗格中, 選擇**對等互連**。  
8. 選擇 **'添加**",添加 VM 的虛擬網路,然後創建對等互連。  
9. 轉到具有 VM 的虛擬網路,選擇**對等互連**,選擇 AKS 虛擬網路,然後創建對等互連。 如果 AKS 虛擬網路上的位址範圍和 VM 的虛擬網路衝突,對等互連將失敗。 有關詳細資訊,請參閱[虛擬網路對等互連][virtual-network-peering]。

## <a name="hub-and-spoke-with-custom-dns"></a>中心,並與自定義 DNS 交談

[集線器和分支體系結構](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)通常用於在 Azure 中部署網路。 在其中許多部署中,分支 VNet 中的 DNS 設置配置為引用中央 DNS 轉寄站,以允許本地和基於 Azure 的 DNS 解析。 將 AKS 群集部署到此類網路環境中時,必須考慮一些特殊注意事項。

![專用叢集集線器和分支](media/private-clusters/aks-private-hub-spoke.png)

1. 默認情況下,在預配專用群集時,在群集託管資源組中創建專用終結點 (1) 和專用 DNS 區域 (2)。 叢集使用專用區域中的 A 記錄解析專用終結點的 IP 以與 API 伺服器通訊。

2. 專用 DNS 區域僅連結到群集節點附加到 (3) 的 VNet。 這意味著專用終結點只能由該連結 VNet 中的主機解析。 在 VNet 上未配置自定義 DNS(預設)的情況下,對於 DNS 的主機點 168.63.129.16 時,這一點沒有問題,因為 DNS 可以解析由於連結而導致的專用 DNS 區域中的記錄。

3. 包含群組的 VNet 具有自訂 DNS 設定 (4) 的情況下,叢集部署將失敗,除非專用 DNS 區域連結到包含自訂 DNS 解析器 (5) 的 VNet。 此連結可以在群集預配期間創建專用區域後手動創建,或者在使用 Azure 策略或其他基於事件的部署機制(例如,Azure 事件網格和 Azure 函數)檢測到區域創建時透過自動化創建此連結。

## <a name="dependencies"></a>相依性  

* 專用鏈路服務僅在標準 Azure 負載均衡器上支援。 不支援基本 Azure 負載均衡器。  
* 要使用自訂 DNS 伺服器,請使用 Azure DNS IP 168.63.129.16 作為自訂 DNS 伺服器中的上游 DNS 伺服器。

## <a name="limitations"></a>限制 
* IP 授權範圍不能套用於私人 api 伺服器的終結點,它們僅適用於公共 API 伺服器
* 目前某些區域支援可用性區域,請參閱本文件的開頭 
* [Azure 專用連結服務限制][private-link-service]適用於專用群集、Azure 專用終結點和虛擬網路服務終結點,這些終結點當前不受同一虛擬網路的支援。
* 不支援專用群組集中的虛擬節點在專用 Azure 虛擬網路中旋轉專用 Azure 容器實例 (ACI)
* 不支援與專用群集開箱即用的 Azure DevOps 整合
* 對於需要啟用 Azure 容器註冊表以使用專用 AKS 的客戶,容器註冊表虛擬網路必須與代理群集虛擬網路對等。
* 目前不支援 Azure 開發空間
* 不支援將現有 AKS 叢集轉換為專用叢集
* 刪除或修改客戶子網中的專用終結點將導致群集停止運行。 
* 當前不支援容器即時數據的 Azure 監視器。


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: /azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[express-route-or-vpn]: ../expressroute/expressroute-about-virtual-network-gateways.md

