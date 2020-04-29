---
title: 包含檔案
description: 包含檔案
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335108"
---
| 資源 | 限制 |
| --- | --- |
| 每個訂用帳戶的 ExpressRoute 電路 |10 |
| 每個訂用帳戶每個區域的 ExpressRoute 線路，Azure Resource Manager |10 |
| 使用 ExpressRoute Standard 向 Azure 私用對等互連通告的最大路由數目 |4,000 |
| 使用 ExpressRoute Premium 附加元件通告至 Azure 私用對等互連的最大路由數目 |10,000 |
| 從 ExpressRoute 連線的 VNet 位址空間針對 Azure 私用對等互連通告的路由數目上限 |200 |
| 使用 ExpressRoute Standard 公告至 Microsoft 對等互連的最大路由數目 |200 |
| 使用 ExpressRoute Premium 附加元件公告至 Microsoft 對等互連的最大路由數目 |200 |
| 在相同的對等互連位置中連結至相同虛擬網路的 ExpressRoute 線路數目上限 |4 |
| ExpressRoute 線路連結至相同虛擬網路中不同對等互連位置的最大數目 |4 |
| 每個 ExpressRoute 電路允許的虛擬網路連結數目 |請參閱[每個 ExpressRoute 線路的虛擬網路數目](#vnetpercircuit)。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>每個 ExpressRoute 線路的虛擬網路數目
| **線路大小** | **標準的虛擬網路連結數目** | **具有 Premium 附加元件的虛擬網路連結數目** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| 100 Gbps * |10 |100 |

**僅限 100 Gbps ExpressRoute Direct*

> [!NOTE]
> 全球觸達連線計數會與每個 ExpressRoute 線路的虛擬網路連線限制有關。 例如，10 Gbps 高階線路可允許5個全球連線和95連線至 ExpressRoute 閘道或 95 Global 連線，以及連接到 ExpressRoute 閘道的5個連線，或任何其他組合，直到線路的100連線限制為止。
