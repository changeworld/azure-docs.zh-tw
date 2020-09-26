---
title: 案例：任何對任意
titleSuffix: Azure Virtual WAN
description: 路由的案例-任何對任意
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6115ca375c3e5bf2be3335fe2231628ec7bf309f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267732"
---
# <a name="scenario-any-to-any"></a>案例：任何對任意

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在任何對任何情況下，任何輪輻都可以觸及另一個輪輻。 如果有多個中樞，則在標準虛擬 WAN 中預設會啟用中樞對中樞路由 (也稱為「中樞間) 」。 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

為了瞭解虛擬 WAN 案例中需要多少個路由表，您可以建立一個連接矩陣，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行) 進行通訊。 此案例中的連線矩陣很簡單，但我們已將其加入，以便與其他案例保持一致。

| 從 |   收件者 |  *Vnet* | *分支* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|      X     |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中的每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料表中的資料列標頭) 學習目的地前置詞 (流程的「到」端、在資料表中為斜體的資料行標頭) 特定的流量，其中「X」表示連線是由虛擬 WAN 提供。

因為 Vnet 和分支 (VPN、ExpressRoute 和使用者 VPN) 的所有連線都有相同的連線需求，所以需要單一路由表。 如此一來，所有連接都會相關聯並傳播至相同的路由表，也就是預設的路由表：

* 虛擬網路：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **預設**
* 分支：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **預設**

如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>架構

在 [ **圖 1**] 中，所有 Vnet 和分支 (VPN、EXPRESSROUTE、P2S) 都可以彼此聯繫。 在虛擬中樞內，連接的運作方式如下：

* Vpn 連接會將 VPN 網站連線到 VPN 閘道。
* 虛擬網路連線會將虛擬網路連接到虛擬中樞。 虛擬中樞的路由器提供 Vnet 之間的傳輸功能。
* Expressroute 連線會將 ExpressRoute 線路連接到 ExpressRoute 閘道。

除非您將連接的路由設定設定為 [ **無**] 或自訂路由表，否則這些 (連接預設會在建立) 與預設路由表建立關聯。 這些連線預設也會將路由傳播到預設路由表。 這項功能可讓您在任何輪輻 (VNet、VPN、ER、P2S) 都可以彼此聯繫的情況下進行。

**圖1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="圖1":::

## <a name="workflow"></a><a name="workflow"></a>工作流程

標準虛擬 WAN 預設會啟用此案例。 如果在 WAN 設定中停用分支對分支的設定，則不允許分支輪輻之間的連接。 VPN/ExpressRoute/使用者 VPN 被視為虛擬 WAN 中的分支輪輻

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
