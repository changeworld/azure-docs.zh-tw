---
title: 包含檔案
description: 包含檔案
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 54306b8bd8718a0a69f5b04ceaab15dff46d21a8
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978844"
---
| 資源 | 限制 |
| --- | --- |
| 每個訂用帳戶的 ExpressRoute 線路 |10 |
| 每個訂用帳戶每個區域的 ExpressRoute 線路 (具有 Azure Resource Manager) |10 |
| 具有 ExpressRoute Standard 的 Azure 私人對等互連的公告路由數目上限 |4,000 |
| 具有 ExpressRoute Premium 附加元件的 Azure 私人對等互連的公告路由數目上限 |10,000 |
| 從 ExpressRoute 連線的 VNet 位址空間的 Azure 私人對等互連的公告路由數目上限 |1000 |
| 具有 ExpressRoute Standard 的 Microsoft 對等互連的公告路由數目上限 |200 |
| 具有 ExpressRoute Premium 附加元件的 Microsoft 對等互連的公告路由數目上限 |200 |
| 連結至相同對等互連位置的相同虛擬網路的 ExpressRoute 線路數目上限 |4 |
| ExpressRoute 線路連結至相同虛擬網路中不同對等互連位置的最大數目 |4 |
| 每個 ExpressRoute 線路允許的虛擬網路連結數目 |請參閱[每個 ExpressRoute 線路的虛擬網路數目](#vnetpercircuit)資料表。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>每個 ExpressRoute 線路的虛擬網路數目
| **線路大小** | **Standard 的虛擬網路連結數目** | **具有 Premium 附加元件的虛擬網路連結數目** |
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

**僅限 100 Gbps ExpressRoute Direct*

> [!NOTE]
> Global Reach 連線數計入每個 ExpressRoute 線路的虛擬網路連線數限制。 例如，10 Gbps Premium 線路可允許 5 個 Global Reach 連線和連線至 ExpressRoute 閘道的 95 個連線，或 95 個 Global Reach 連線和連線至 ExpressRoute 閘道的 5 個連線，或任何其他組合，直到線路的 100 個連線數上限為止。
