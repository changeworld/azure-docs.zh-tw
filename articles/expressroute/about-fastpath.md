---
title: 關於 Azure 快速路由快速路徑
description: 瞭解 Azure 快速路由快速路徑，繞過閘道發送網路流量
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: cherylmc
ms.openlocfilehash: 265004b1171d1df95b3090676d5836b951c28a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282846"
---
# <a name="about-expressroute-fastpath"></a>關於 ExpressRoute FastPath

ExpressRoute虛擬網路閘道旨在切換式網路路由和路由網路流量。 FastPath 旨在提高本地網路和虛擬網路之間的資料路徑性能。 啟用後，FastPath 會繞過閘道將網路流量直接發送到虛擬網路中的虛擬機器。

## <a name="requirements"></a>需求

### <a name="circuits"></a>電路

快速路徑在所有快速路由電路上都可用。

### <a name="gateways"></a>閘道

FastPath 仍然需要創建虛擬網路閘道來交換虛擬網路和本地網路之間的路由。 有關虛擬網路閘道和 ExpressRoute 的詳細資訊（包括性能資訊和閘道 SKU），請參閱[ExpressRoute 虛擬網路閘道](expressroute-about-virtual-network-gateways.md)。

要配置 FastPath，虛擬網路閘道必須為：

* 超高性能
* ErGw3AZ

## <a name="supported-features"></a>支援的功能

雖然 FastPath 支援大多數配置，但它不支援以下功能：

* 閘道子網上的 UDR：如果將 UDR 應用於虛擬網路的閘道子網，則來自本地網路的網路流量將繼續發送到虛擬網路閘道。

* VNet 對等互連：如果其他虛擬網路與連接到 ExpressRoute 的網路對等，則從本地網路到其他虛擬網路（即所謂的"分支"VNet）的網路流量將繼續發送到虛擬網路閘道。 解決方法是將所有虛擬網路直接連接到 ExpressRoute 電路。

* 基本負載等化器：如果在虛擬網路中部署基本內部負載等化器或在虛擬網路中部署的 Azure PaaS 服務使用基本內部負載等化器，則從本地網路到託管在基本負載等化器將發送到虛擬網路閘道。 解決方案是將基本負載等化器升級到[標準負載等化器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。

* 專用鏈路：如果從本地網路連接到虛擬網路中的[專用終結點](../private-link/private-link-overview.md)，則連接將經過虛擬網路閘道。
 
## <a name="next-steps"></a>後續步驟

要啟用 FastPath，請參閱[將虛擬網路連結到 ExpressRoute](expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath)。
