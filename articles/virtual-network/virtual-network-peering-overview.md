---
title: Azure 虛擬網路對等互連
titlesuffix: Azure Virtual Network
description: 了解 Azure 中的虛擬網路對等互連。
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279542"
---
# <a name="virtual-network-peering"></a>虛擬網路對等互連

虛擬網路對等互連使您能夠無縫連接[Azure 虛擬網路中](virtual-networks-overview.md)的網路。 出於連接目的，虛擬網路顯示為一個虛擬網路。 虛擬機器之間的流量使用 Microsoft 骨幹基礎結構。 與同一網路中虛擬機器之間的流量一樣，流量只能通過 Microsoft 的*專用*網路路由。

Azure 支援以下類型的對等互連：

* 虛擬網路對等互連：連接同一 Azure 區域內的虛擬網路。
* 全域虛擬網路對等互連：跨 Azure 區域連接虛擬網路。

使用虛擬網路對等互連 (不論是本機還是全球) 的優點包括︰

* 不同虛擬網路的資源之間具有低延遲、高頻寬連線。
* 一個虛擬網路中的資源與不同虛擬網路中的資源進行通信的能力。
* 跨 Azure 訂閱、Azure 活動目錄租戶、部署模型和 Azure 區域在虛擬網路之間傳輸資料的功能。
* 對通過 Azure 資源管理器創建的虛擬網路進行對等的能力。
* 能夠對通過資源管理器創建的虛擬網路與通過經典部署模型創建的虛擬網路對等。 若要深入了解 Azure 部署模型，請參閱[了解 Azure 部署模型](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 建立對等互連時或建立對等互連之後，虛擬網路中的資源沒有停機時間。

對等互連虛擬網路之間的網路流量為私用。 虛擬網路之間的流量會保留在 Microsoft 骨幹網路上。 虛擬網路之間的通訊不需要公用網際網路、閘道或加密。

## <a name="connectivity"></a>連線能力

對於對等虛擬網路，任一虛擬網路中的資源可以直接與對等虛擬網路中的資源連接。

在相同區域的對等互連虛擬網路中，虛擬機器之間的網路延遲與單一虛擬網路中的網路延遲相同。 網路輸送量為依照虛擬機器大小，按比例允許的頻寬。 對等互連內的頻寬沒有其他額外限制。

對等互連之虛擬網路中的虛擬機器之間的流量，會透過 Microsoft 骨幹基礎結構直接路由傳送，而不會透過閘道或透過公用網際網路來傳送。

您可以在虛擬網路中應用網路安全性群組來阻止對其他虛擬網路或子網的訪問。
配置虛擬網路對等互連時，打開或關閉虛擬網路之間的網路安全性群組規則。 如果打開對等虛擬網路之間的完全連接，則可以應用網路安全性群組來阻止或拒絕特定訪問。 完全連接是預設選項。 要瞭解有關網路安全性群組的更多資訊，請參閱[安全性群組](security-overview.md)。

## <a name="service-chaining"></a>服務鏈結

通過服務連結，您可以通過使用者定義的路由將流量從一個虛擬網路定向到對等網路中的虛擬裝置或閘道。

要啟用服務連結，請將指向對等虛擬網路中的虛擬機器的使用者定義路由配置為*下一躍點*IP 位址。 使用者定義的路由還可以指向虛擬網路閘道，以啟用服務連結。

您可以部署中心與分支網路，其中中心虛擬網路承載基礎設施元件，如網路虛擬裝置或 VPN 閘道。 所有輪輻虛擬網路可以接著與中樞虛擬網路對等互連。 流量通過集線器虛擬網路中的網路虛擬裝置或 VPN 閘道流動。

虛擬網路對等互連可讓使用者定義路由中的下一個躍點成為對等互連虛擬網路中虛擬機器的 IP 位址或 VPN 閘道。 不能使用使用者定義的路由在虛擬網路之間路由，該路由指定 Azure ExpressRoute 閘道為下一個躍點類型。 若要深入了解使用者定義的路由，請參閱[使用者定義的路由概觀](virtual-networks-udr-overview.md#user-defined)。 要瞭解如何創建集線器和分支網路拓撲，請參閱[Azure 中的中心分支網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="gateways-and-on-premises-connectivity"></a>閘道及內部部署連線能力

每個虛擬網路（包括對等虛擬網路）都可以有自己的閘道。 虛擬網路可以使用其閘道連接到本地網路。 您還可以使用閘道配置[虛擬網路到虛擬網路連接](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)，即使對於對等虛擬網路也是如此。

為虛擬網路互連配置兩個選項時，虛擬網路之間的流量會流經對等配置。 流量使用 Azure 主幹。

您還可以將對等虛擬網路中的閘道配置為本地網路的中轉站。 在這種情況下，使用遠端閘道的虛擬網路不能有自己的閘道。 虛擬網路只有一個閘道。 閘道是對等虛擬網路中的本地閘道或遠端閘道，如下圖所示：

![虛擬網路對等互連傳輸](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

虛擬網路對等互連和全球虛擬網路對等互連都支援閘道傳輸。

支援通過不同部署模型創建的虛擬網路之間的閘道傳輸。 閘道必須位於資源管理器模型中的虛擬網路中。 若要深入了解如何使用閘道來進行傳輸，請參閱[設定 VPN 閘道以在虛擬網路對等互連中進行傳輸](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。

當您對共用單個 Azure ExpressRoute 連接的虛擬網路進行對等時，它們之間的流量將經歷對等關係。 該流量使用 Azure 骨幹網。 您依然可以在每個虛擬網路中使用本機閘道來連線內部部署線路。 否則，您可以使用共用閘道並配置本地連接的傳輸。

## <a name="troubleshoot"></a>疑難排解

要確認虛擬網路是對等的，可以檢查有效的路由。 檢查虛擬網路中任何子網中的網路介面路由。 如果虛擬網路對等互連存在，則虛擬網路內的所有子網路都具有下一個躍點類型為「VNet 對等互連」** 的路由 (對每個對等互連的虛擬網路中的每個位址空間而言)。 有關詳細資訊，請參閱[診斷虛擬機器路由問題](diagnose-network-routing-problem.md)。

您還可以使用 Azure 網路觀察程式對對等虛擬網路中的虛擬機器的連接進行故障排除。 通過連接檢查，您可以查看流量如何從源虛擬機器的網路介面路由到目標虛擬機器的網路介面。 有關詳細資訊，請參閱使用[Azure 門戶與 Azure 網路觀察程式建立連接的疑難排解](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)。

您還可以嘗試[疑難排解虛擬網路對等問題](virtual-network-troubleshoot-peering-issues.md)。

## <a name="constraints-for-peered-virtual-networks"></a>對等虛擬網路的約束<a name="requirements-and-constraints"></a>

只有在為虛擬網路建立全域的對等互連時，會受到下列限制：

* 一個虛擬網路中的資源無法與全域對等虛擬網路中基本內部負載等化器 （ILB） 的前端 IP 位址進行通信。
* 某些使用基本負載等化器的服務不能通過全域虛擬網路對等互連工作。 有關詳細資訊，請參閱[與全域 VNet 對等互連和負載等化器相關的約束是什麼？](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

如需詳細資訊，請參閱[需求和限制](virtual-network-manage-peering.md#requirements-and-constraints)。 要瞭解有關支援的對等互連數（請參閱）的更多資訊，請參閱[網路限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。

## <a name="permissions"></a>權限

要瞭解創建虛擬網路對等互連所需的許可權，請參閱[許可權](virtual-network-manage-peering.md#permissions)。

## <a name="pricing"></a>定價

使用虛擬網路對等互連連接的入口和出口流量需要支付象徵性的費用。 有關詳細資訊，請參閱[虛擬網路定價](https://azure.microsoft.com/pricing/details/virtual-network)。

閘道傳輸是一種對等屬性，它使虛擬網路能夠在對等虛擬網路中使用 VPN/ExpressRoute 閘道。 閘道傳輸適用于跨本地和網到網路連接。 對等式虛擬網路中閘道（入口或出口）的流量在分支 VNet（或非閘道 VNet）上產生虛擬網路對等互連費用。 有關詳細資訊，請參閱 VPN 閘道費用的[VPN 閘道定價](https://azure.microsoft.com/pricing/details/vpn-gateway/)和 ExpressRoute 閘道費用的 ExpressRoute 閘道費用的 ExpressRoute 閘道定價。

>[!NOTE]
> 本文檔的早期版本指出，虛擬網路對等互連費用不適用於具有閘道傳輸的分支 VNet（或非閘道 VNet）。 它現在反映了每個定價頁的準確定價。

## <a name="next-steps"></a>後續步驟

* 您可以在兩個虛擬網路之間創建對等互連。 網路可以屬於同一訂閱、同一訂閱中的不同部署模型或不同的訂閱。 完成下列其中一個案例的教學課程：

    |Azure 部署模型             | 訂用帳戶  |
    |---------                          |---------|
    |兩者皆使用 Resource Manager              |[相同](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[不同](create-peering-different-subscriptions.md)|
    |一個使用 Resource Manager、一個使用傳統部署模型  |[相同](create-peering-different-deployment-models.md)|
    |                                   |[不同](create-peering-different-deployment-models-subscriptions.md)|

* 要瞭解如何創建集線器和分支網路拓撲，請參閱[Azure 中的中心分支網路拓撲](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。
* 要瞭解所有虛擬網路對等互連設置，請參閱[創建、更改或刪除虛擬網路對等互連](virtual-network-manage-peering.md)。
* 有關常見虛擬網路對等互連和全球虛擬網路對等問題的答案，請參閱[VNet 對等互連](virtual-networks-faq.md#vnet-peering)。
