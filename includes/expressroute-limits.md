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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335108"
---
| 資源 | 限制 |
| --- | --- |
| 每個訂用帳戶的 ExpressRoute 電路 |10 |
| 每個訂閱的 ExpressRoute 電路，使用 Azure 資源管理器 |10 |
| 使用 ExpressRoute 標準通告到 Azure 專用對等互連的最大路由數 |4,000 |
| 使用 ExpressRoute 高級載入項通告到 Azure 專用對等互連的最大路由數 |10,000 |
| 從 ExpressRoute 連接的 VNet 位址空間從 Azure 專用對等互連通告的最大路由數 |200 |
| 使用 ExpressRoute 標準向 Microsoft 對等互連通告的最大路由數 |200 |
| 使用 ExpressRoute 高級載入項向 Microsoft 通告對等路由的最大路由數 |200 |
| 在同一對等位置連接到同一虛擬網路的最大 ExpressRoute 電路數 |4 |
| ExpressRoute 線路連結至相同虛擬網路中不同對等互連位置的最大數目 |4 |
| 每個 ExpressRoute 電路允許的虛擬網路連結數目 |請參閱[每個 ExpressRoute 電路表的虛擬網路數](#vnetpercircuit)。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>每個 ExpressRoute 電路的虛擬網路數
| **電路尺寸** | **標準虛擬網路連結數** | **使用高級載入項的虛擬網路連結數** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**僅限 100 Gbps 快速路由*

> [!NOTE]
> 全域覆蓋連接計入每個 ExpressRoute 電路的虛擬網路連接限制。 例如，10 Gbps 高級電路將允許 5 個全域到達連接和 95 個到 ExpressRoute 閘道的連接或 95 個全域到達連接，以及 5 個到 ExpressRoute 閘道的連接，或到 100 個連接限制的任何其他組合電路。
