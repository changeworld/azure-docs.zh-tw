---
title: 案例： any 對 any
titleSuffix: Azure Virtual WAN
description: 路由的案例-任何對任何
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: ecc2b3cf236cb2a78fd595189649e7f6b176d709
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567314"
---
# <a name="scenario-any-to-any"></a>案例： Any 對 any

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在任何對任何案例中，任何輪輻都可以到達另一個輪輻。 當有多個中樞存在時，預設會在標準虛擬 WAN 中啟用中樞對中樞路由（也稱為集線器間）。 

在此案例中，VPN、ExpressRoute 和使用者 VPN 連線會與相同的路由表建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會將路由傳播到相同的一組路由表。 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-architecture"></a><a name="architecture"></a>案例架構

在 [**圖 1**] 中，所有 Vnet 和分支（VPN、EXPRESSROUTE、P2S）都可以彼此接觸。 在虛擬中樞中，連線的操作方式如下：

* Vpn 連線會將 VPN 網站連接到 VPN 閘道。
* 虛擬網路連線會將虛擬網路連接到虛擬中樞。 虛擬中樞的路由器提供 Vnet 之間的傳輸功能。
* ExpressRoute 連線會將 ExpressRoute 線路連接至 ExpressRoute 閘道。

這些連線（依預設在建立時）會與預設路由表相關聯，除非您將連接的路由設定設為 [**無**] 或自訂路由表。 根據預設，這些連接也會將路由傳播到預設路由表。 這可讓任何輪輻（VNet、VPN、ER、P2S）彼此接觸的任何一種情況。

**圖1**

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="圖1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

預設會針對標準虛擬 WAN 啟用此案例。 如果 [WAN 設定] 中的 [分支對分支] 設定已停用，則不允許分支輪輻之間的連線。 VPN/ExpressRoute/使用者 VPN 會被視為虛擬 WAN 中的分支輪輻

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
