---
title: 使用虛擬網路的案例
description: 將容器群組部署至 Azure 虛擬網路的案例、資源和限制。
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5e9c1d1606a9ad491ba7a7e623f1606717aa5b1f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89569164"
---
# <a name="virtual-network-scenarios-and-resources"></a>虛擬網路案例和資源

[Azure 虛擬網路](../virtual-network/virtual-networks-overview.md)會為 Azure 資源和內部部署資源提供安全的私人網路。 透過將容器群組部署至 Azure 虛擬網路，您的容器可在虛擬網路中安全地與其他資源通訊。 

本文提供有關虛擬網路案例、限制和資源的背景資訊。 如需使用 Azure CLI 的部署範例，請參閱 [將容器實例部署至 Azure 虛擬網路](container-instances-vnet.md)。

## <a name="scenarios"></a>案例

部署至 Azure 虛擬網路的容器群組可供您進行下列案例：

* 直接在同一個子網路中的容器群組間通訊
* 在虛擬網路中，將[以工作為基礎](container-instances-restart-policy.md)的工作負載輸出從容器執行個體傳送至資料庫
* 從虛擬網路中的[服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)為容器執行個體擷取內容
* 透過[VPN 閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md)或[ExpressRoute](../expressroute/expressroute-introduction.md)啟用與內部部署資源的容器通訊
* 與 [Azure 防火牆](../firewall/overview.md) 整合，以識別源自容器的輸出流量 
* 透過內部 Azure DNS 解析名稱，以與虛擬網路中的 Azure 資源（例如虛擬機器）進行通訊
* 使用 NSG 規則來控制容器對子網或其他網路資源的存取

## <a name="unsupported-networking-scenarios"></a>不支援的網路案例 

* **Azure Load Balancer** -不支援將 Azure Load Balancer 放置在網路容器群組中的容器實例前面
* **全域虛擬網路對等互連** -不支援跨 Azure 區域連接虛擬網路的全域對等互連 () 
* **公用 ip 或 DNS 標籤** -部署至虛擬網路的容器群組目前不支援使用公用 ip 位址或完整功能變數名稱將容器直接公開至網際網路

## <a name="other-limitations"></a>其他限制

* 目前，在部署至虛擬網路的容器群組中，僅支援 Linux 容器。
* 若要將容器群組部署至子網，子網不能包含其他資源類型。 在將容器群組部署至子網路之前，請先將所有現有資源從現有的子網路移除，或是建立新的子網路。
* 您無法在部署至虛擬網路的容器群組中使用 [受控識別](container-instances-managed-identity.md) 。
* 您無法在部署至虛擬網路的容器群組中啟用 [活動探查](container-instances-liveness-probe.md) 或 [就緒探查](container-instances-readiness-probe.md) 。
* 由於涉及額外的網路資源，部署至虛擬網路的速度通常會比部署標準容器實例慢。

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="where-to-deploy"></a>部署的位置

您可以在 Azure 虛擬網路中部署容器群組，並使用下欄區域和資源上限。

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="required-network-resources"></a>必要的網路資源

將容器群組部署至虛擬網路需要三項 Azure 虛擬網路資源：[虛擬網路](#virtual-network)本身、虛擬網路中的[委派子網路](#subnet-delegated)及[網路設定檔](#network-profile)。 

### <a name="virtual-network"></a>虛擬網路

虛擬網路可定義您建立一或多個子網路所在的位址空間。 接著，您可以將 Azure 資源 (例如容器群組) 部署到虛擬網路中的子網路。

### <a name="subnet-delegated"></a>子網路 (委派)

子網路會將虛擬網路分割成位址空間，供放在其中的 Azure 資源使用。 您可以在虛擬網路中建立一或多個子網路。

您用於容器群組的子網路只能包含容器群組。 當您首次將容器群組部署至子網路時，Azure 會將該子網路委派至 Azure 容器執行個體。 委派之後，子網路只能用於容器群組。 如果您嘗試將容器群組以外的資源部署至委派子網路，該作業會失敗。

### <a name="network-profile"></a>網路設定檔

網路設定檔是 Azure 資源的網路設定範本。 其會為資源指定特定的網路屬性，例如應該部署該資源的子網路。 當您首次使用 [az container create][az-container-create] 命令將容器群組部署至子網路 (也因此部署至虛擬網路) 時，Azure 會為您建立網路設定檔。 然後您可以在日後部署至子網路時，使用該網路設定檔。 

若要使用 Resource Manager 範本、YAML 檔案或程式設計方法將容器群組部署至子網路，您必須提供網路設定檔的完整 Resource Manager 資源識別碼。 您可以使用先前使用 [az container create][az-container-create] 建立的設定檔，或使用 Resource Manager 範本來建立設定檔 (請參閱[範本範例](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet)及[參考](/azure/templates/microsoft.network/networkprofiles))。 若要取得先前所建設定檔的識別碼，請使用 [az network profile list][az-network-profile-list] 命令。 

在下圖中，多個容器群組已部署到委派至 Azure 容器執行個體的子網路。 當您將一個容器群組部署至子網路後，就可立即指定相同的網路設定檔來部署其他容器群組。

![虛擬網路中的容器群組][aci-vnet-01]

## <a name="next-steps"></a>接下來的步驟

* 如需 Azure CLI 的部署範例，請參閱將 [容器實例部署至 Azure 虛擬網路](container-instances-vnet.md)。
* 若要使用 Resource Manager 範本部署新的虛擬網路、子網路、網路設定檔及容器群組，請參閱 [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) (使用 VNet 建立 Azure 容器群組)。
* 使用 [Azure 入口網站](container-instances-quickstart-portal.md) 建立容器實例時，您也可以在 [ **網路** ] 索引標籤上提供新的或 exsting 虛擬網路的設定。


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list
