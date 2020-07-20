---
title: 關於 Azure ExpressRoute FastPath
description: 瞭解 Azure ExpressRoute FastPath，藉由略過閘道來傳送網路流量
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80282846"
---
# <a name="about-expressroute-fastpath"></a>關於 ExpressRoute FastPath

ExpressRoute 虛擬網路閘道的設計是用來切換式網路路由和路由傳送網路流量。 FastPath 的設計目的是要改善內部部署網路與虛擬網路之間的資料路徑效能。 啟用時，FastPath 會直接將網路流量傳送到虛擬網路中的虛擬機器，並略過閘道。

## <a name="requirements"></a>需求

### <a name="circuits"></a>獲得

FastPath 適用于所有 ExpressRoute 線路。

### <a name="gateways"></a>閘道

FastPath 仍然需要建立虛擬網路閘道，才能在虛擬網路和內部部署網路之間交換路由。 如需虛擬網路閘道和 ExpressRoute 的詳細資訊，包括效能資訊和閘道 Sku，請參閱[ExpressRoute 虛擬網路閘道](expressroute-about-virtual-network-gateways.md)。

若要設定 FastPath，虛擬網路閘道必須是：

* Ultra 效能
* ErGw3AZ

## <a name="supported-features"></a>支援的功能

雖然 FastPath 支援大部分的設定，但它並不支援下列功能：

* 閘道子網上的 UDR：如果您將 UDR 套用至虛擬網路的閘道子網，來自內部部署網路的網路流量將會繼續傳送到虛擬網路閘道。

* VNet 對等互連：如果您有其他虛擬網路與連線到 ExpressRoute 的對等互連，則從內部部署網路到其他虛擬網路的網路流量（也就是所謂的「輪輻」 Vnet）將會繼續傳送到虛擬網路閘道。 因應措施是將所有虛擬網路直接連接到 ExpressRoute 線路。

* 基本 Load Balancer：如果您在虛擬網路中部署基本內部負載平衡器，或您在虛擬網路中部署的 Azure PaaS 服務使用基本內部負載平衡器，則從內部部署網路到裝載于基本負載平衡器的虛擬 Ip 的網路流量將會傳送到虛擬網路閘道。 解決方案是將基本負載平衡器升級為[標準負載平衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

* 私用連結：如果您從內部部署網路連線到虛擬網路中的[私人端點](../private-link/private-link-overview.md)，則該連線將會通過虛擬網路閘道。
 
## <a name="next-steps"></a>後續步驟

若要啟用 FastPath，請參閱將[虛擬網路連結到 ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。
