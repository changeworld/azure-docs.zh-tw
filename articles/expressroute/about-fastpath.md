---
title: 關於 Azure ExpressRoute FastPath
description: 瞭解 Azure ExpressRoute FastPath 如何藉由略過閘道來傳送網路流量
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: duau
ms.openlocfilehash: bf600d835e177ce51870ed2a8894adcd667e3901
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89398047"
---
# <a name="about-expressroute-fastpath"></a>關於 ExpressRoute FastPath

ExpressRoute 虛擬網路閘道的設計目的是要切換式網路路由及路由網路流量。 FastPath 的設計目的是要改善您的內部部署網路與虛擬網路之間的資料路徑效能。 啟用時，FastPath 會將網路流量直接傳送到虛擬網路中的虛擬機器，略過閘道。

## <a name="requirements"></a>需求

### <a name="circuits"></a>電路

FastPath 適用于所有 ExpressRoute 線路。

### <a name="gateways"></a>閘道

FastPath 仍然需要建立虛擬網路閘道，以在虛擬網路與內部部署網路之間交換路由。 如需虛擬網路閘道和 ExpressRoute （包括效能資訊和閘道 Sku）的詳細資訊，請參閱 [ExpressRoute 虛擬網路閘道](expressroute-about-virtual-network-gateways.md)。

若要設定 FastPath，虛擬網路閘道必須是：

* Ultra 效能
* ErGw3AZ

## <a name="supported-features"></a>支援的功能

雖然 FastPath 支援大部分的設定，但不支援下列功能：

* 閘道子網上的 UDR：如果您將 UDR 套用至虛擬網路的閘道子網，來自內部部署網路的網路流量會繼續傳送到虛擬網路閘道。

* VNet 對等互連：如果您有其他虛擬網路與連線到 ExpressRoute 的虛擬網路對等互連，則從內部部署網路到其他虛擬網路的網路流量 (也就是所謂的「輪輻」 Vnet) 將繼續傳送到虛擬網路閘道。 解決方法是直接將所有虛擬網路連線到 ExpressRoute 線路。

* 基本 Load Balancer：如果您在虛擬網路中部署基本內部負載平衡器，或您在虛擬網路中部署的 Azure PaaS 服務使用基本的內部負載平衡器，則從內部部署網路到裝載于基本負載平衡器之虛擬 Ip 的網路流量都會傳送至虛擬網路閘道。 解決方法是將基本負載平衡器升級到 [標準負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

* Private Link：如果您從內部部署網路連線到虛擬網路中的 [私人端點](../private-link/private-link-overview.md) ，連線會通過虛擬網路閘道。
 
## <a name="next-steps"></a>後續步驟

若要啟用 FastPath，請參閱將 [虛擬網路連結至 ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。
