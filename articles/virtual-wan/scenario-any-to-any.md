---
title: 案例： any 對 any
titleSuffix: Azure Virtual WAN
description: 路由的案例-任何對任何
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 95fa7a8c6abd0ad65b367cacef15b8faa16da640
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553422"
---
# <a name="scenario-any-to-any"></a>案例： Any 對 any

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在任何對任何案例中，任何輪輻都可以到達另一個輪輻。 當有多個中樞存在時，中樞對中樞路由 (也稱為「中樞間) 」，預設會在標準虛擬 WAN 中啟用。 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

為了找出虛擬 WAN 案例中需要多少個路由表，您可以建立一個連接矩陣，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行) 進行通訊。 此案例中的連線矩陣很簡單，但我們已包含它，以便與其他案例一致。

| 寄件者 |   收件者 |  *Vnet* | *分支* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|      X     |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中的每個儲存格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料表中的資料列標頭) 學習到目的地前置詞 (流程的「到」端，而資料表中的資料行標頭則) 特定的流量流程。

由於 Vnet 和分支 (VPN、ExpressRoute 和使用者 VPN) 的所有連接都有相同的連線需求，因此需要單一路由表。 如此一來，所有連接都會關聯並傳播至相同的路由表，也就是預設路由表：

* 虛擬網路：
  * 關聯的路由表：**預設值**
  * 傳播至路由表：**預設值**
* 網點
  * 關聯的路由表：**預設值**
  * 傳播至路由表：**預設值**

如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>架構

在 [**圖 1**] 中，所有 Vnet 和分支 (VPN、EXPRESSROUTE、P2S) 都可以彼此聯繫。 在虛擬中樞中，連線的操作方式如下：

* Vpn 連線會將 VPN 網站連接到 VPN 閘道。
* 虛擬網路連線會將虛擬網路連接到虛擬中樞。 虛擬中樞的路由器提供 Vnet 之間的傳輸功能。
* ExpressRoute 連線會將 ExpressRoute 線路連接至 ExpressRoute 閘道。

這些 (連接預設會在建立) 與預設路由表相關聯，除非您將連接的路由設定設為 [**無**] 或自訂路由表。 根據預設，這些連接也會將路由傳播到預設路由表。 這項功能可讓您在任何輪輻 (VNet、VPN、ER、P2S) 都能彼此連線的情況下，達到任何目標。

**圖1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="圖1":::

## <a name="workflow"></a><a name="workflow"></a>工作流程

預設會針對標準虛擬 WAN 啟用此案例。 如果 [WAN 設定] 中的 [分支對分支] 設定已停用，則不允許分支輪輻之間的連線。 VPN/ExpressRoute/使用者 VPN 會被視為虛擬 WAN 中的分支輪輻

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
