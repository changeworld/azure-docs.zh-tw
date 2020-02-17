---
title: Azure 虛擬 WAN 概觀 | Microsoft Docs
description: 了解虛擬 WAN 自動化可調整的分支對分支連線、可用的區域和夥伴。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 02/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 9ac70252ce7c818ccbdecfd996b9970f011aa967
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056337"
---
# <a name="about-azure-virtual-wan"></a>關於 Azure 虛擬 WAN

Azure 虛擬 WAN 是一種網路服務，可將最佳且自動化的分支連線提供給 Azure，或透過 Azure 提供最佳且自動化的分支連線。 Azure 區域作為中樞，您可以選擇將您的分支連線到該中樞。 您也可以利用 Azure 骨幹來連接分支，並享受分支對 VNet 的連線能力。 我們有一份夥伴清單，這些夥伴都支援透過 Azure 虛擬 WAN VPN 的自動連線。 如需詳細資訊，請參閱[虛擬 WAN 夥伴與位置](virtual-wan-locations-partners.md)一文。

Azure 虛擬 WAN 將許多 Azure 雲端連線服務 (例如站對站 VPN、使用者 VPN (點對站) 和 ExpressRoute) 結合到單一操作介面。 使用虛擬網路連線可建立與 Azure Vnet 的連線。 其會根據傳統的中樞和輪輻連線模型來實現[全域傳輸網路架構](virtual-wan-global-transit-network-architecture.md)，讓雲端裝載的網路「中樞」能夠在可能分散到不同「輪輻」類型的端點之間實現傳輸連線。

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwan1.png)

本文可讓您快速檢視 Azure 虛擬 WAN 中的網路連線。 虛擬 WAN 提供下列優點：

* **中樞和輪幅中的整合式連線解決方案：** 自動化站對站設定，以及內部部署網站與 Azure 中樞之間的連線。
* **自動化輪輻設定和組態：** 將您的虛擬網路和工作負載順暢地連線至 Azure 中樞。
* **直覺式疑難排解：** 您可以看到 Azure 內的端對端流程，然後使用此資訊來採取必要的動作。

## <a name="basicstandard"></a>基本和標準的虛擬 WAN

虛擬 WAN 有兩種類型：基本和標準。 下表顯示每種類型的可用設定。

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

如需如何升級虛擬 WAN 的步驟，請參閱[將虛擬 WAN 從基本升級至標準](upgrade-virtual-wan.md)。

## <a name="architecture"></a>架構

如需有關虛擬 WAN 架構以及如何遷移至虛擬 WAN 的詳細資訊，請參閱下列文章：

* [虛擬 WAN 架構](migrate-from-hub-spoke-topology.md)
* [全域傳輸網路架構](virtual-wan-global-transit-network-architecture.md)

## <a name="resources"></a>虛擬 WAN 資源

若要設定端對端虛擬 WAN，您可以建立下列資源：

* **virtualWAN：** 虛擬 WAN 資源代表您 Azure 網路的虛擬覆疊，而且是多個資源的集合。 它包含所有您想要包含在虛擬 WAN 內虛擬中樞的連結。 虛擬 WAN 資源會互相隔離，且不能包含一般的中樞。 跨虛擬 WAN 的虛擬中樞不會互相通訊。

* **中樞：** 虛擬中樞是受 Microsoft 管理的虛擬網路。 中樞包含各種可實現連線的服務端點。 從內部部署網路 (vpnsite)，您可以連線到虛擬中樞內的 VPN 閘道、將 ExpressRoute 線路連線到虛擬中樞，或甚至將行動使用者連線到虛擬中樞內的點對站閘道。 中樞是您在區域中的網路核心。 每個 Azure 區域只能有一個中樞。

  中樞閘道與您用於 ExpressRoute 與 VPN 閘道的虛擬網路閘道不同。 例如，當您使用虛擬 WAN 時，不用從您的內部部署網站直接建立與您 VNet 的站對站連線。 但是，您要建立與中樞的站對站連線。 流量一律會傳送到中樞閘道。 這表示您的 VNet 不需要自己的虛擬網路閘道。 虛擬 WAN 可透過虛擬中樞和虛擬中樞閘道，讓您的 VNet 輕鬆地利用調整功能。

* **中樞虛擬網路連線：** 您可以使用中樞虛擬網路連線資源，順暢地將中樞連線到您的虛擬網路。

* **(預覽) 中樞對中樞連線** - 中樞會在虛擬 WAN 中彼此連線。 這表示連線到本機中樞的分支、使用者或 VNet，可以使用已連線中樞的完整網格架構來與另一個分支或 VNet 通訊。 您也可以在透過虛擬中樞來傳輸的中樞內與其中的 VNet 連線，以及使用中樞對中樞連線架構來跨中樞與 VNet 連線。

* **中樞路由表：** 您可以建立虛擬中樞路由並將路由套用到虛擬中樞路由表。 您可以將多個路由套用到虛擬中樞路由表。

**其他虛擬 WAN 資源**

  * **站台：** 此資源僅用於站對站連線。 站台資源為 **vpnsite**。 它代表您的內部部署 VPN 裝置及其設定。 使用虛擬 WAN 合作夥伴，您將擁有內建的解決方案，會將這項資訊自動匯出至 Azure。

## <a name="connectivity"></a>連線類型

虛擬 WAN 可實現下列類型的連線：站對站 VPN、使用者 VPN (點對站) 和 ExpressRoute。

### <a name="s2s"></a>站對站 VPN 連線

![虛擬 WAN 的圖表](./media/virtual-wan-about/virtualwan.png)

當您建立虛擬 WAN 站對站連線時，可以使用可用的夥伴。 如果您不想使用夥伴，可以手動設定連線。 如需詳細資訊，請參閱[使用虛擬 WAN 建立站對站連線](virtual-wan-site-to-site-portal.md)。

#### <a name="s2spartner"></a>虛擬 WAN 夥伴工作流程

當您使用虛擬 WAN 夥伴時，工作流程是：

1. 分支裝置 (VPN/SDWAN) 控制器會進行驗證，使用 [Azure 服務主體](../active-directory/develop/howto-create-service-principal-portal.md)，將以網站為中心的資訊匯出至 Azure。
2. 分支裝置 (VPN/SDWAN) 控制器會取得 Azure 連線設定，並更新本機裝置。 這會將設定下載、編輯和更新內部部署 VPN 裝置等動作自動化。
3. 一旦裝置有正確的 Azure 設定後，隨即建立與 Azure WAN 的站對站連線 (兩個使用中的通道)。 Azure 同時支援 IKEv1 與 IKEv2。 BGP 為選擇性。

#### <a name="partners"></a>站對站虛擬 WAN 連線的夥伴

如需可用夥伴和位置的清單，請參閱[虛擬 WAN 夥伴與位置](virtual-wan-locations-partners.md)一文。

### <a name="uservpn"></a>使用者 VPN (點對站) 連線

您可以透過 IPsec/IKE (IKEv2) 或 OpenVPN 連線，來連線到您在 Azure 中的資源。 此類型的連線需要在用戶端電腦上設定 VPN 用戶端。 如需詳細資訊，請參閱[建立點對站連線](virtual-wan-point-to-site-portal.md)。

### <a name="er"></a>ExpressRoute 連線
ExpressRoute 可讓您透過私人連線將內部部署網路連線至 Azure。 若要建立連線，請參閱[使用虛擬 WAN 建立 ExpressRoute 連線](virtual-wan-expressroute-portal.md)。

## <a name="locations"></a>位置

如需位置資訊，請參閱[虛擬 WAN 夥伴與位置](virtual-wan-locations-partners.md)一文。

## <a name="faq"></a>常見問題集

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>後續步驟

[使用虛擬 WAN 建立站對站連線](virtual-wan-site-to-site-portal.md)
