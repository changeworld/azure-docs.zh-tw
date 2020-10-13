---
title: Azure ExpressRoute：連線能力模型
description: 檢查客戶網路、Microsoft Azure 和 Microsoft 365 服務之間的連線能力。 客戶可以使用 MPLS 提供者、雲端交換和乙太網路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: duau
ms.openlocfilehash: fb35a03b5dd8780445eb27f485966e3c3452feea
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978744"
---
# <a name="expressroute-connectivity-models"></a>ExpressRoute 連線模型
您可以使用四種不同的方式，在內部部署網路與 Microsoft 雲端之間建立連線， [CloudExchange 共](#CloudExchange)置、 [點對點乙太](#Ethernet)網路連線、 [任何對任何 (IPVPN) ](#IPVPN)連線和 [ExpressRoute Direct](#Direct)。 連線提供者可能會提供一或多個連接模型。 您可以洽詢連線提供者來選擇最適合您的模型。
<br><br>

:::image type="content" source="./media/expressroute-connectivity-models/expressroute-connectivity-models-diagram.png" alt-text="ExpressRoute 連線模型圖表":::

## <a name="co-located-at-a-cloud-exchange"></a><a name="CloudExchange"></a>共置於雲端交換
如果您在具有雲端交換的設施中共置，您可以透過共同位置提供者的乙太網路交換，訂購與 Microsoft 雲端的虛擬交叉連線。 共置提供者可以在您於共置設施中的基礎結構與 Microsoft 雲端之間，提供第 2 層交叉連接或受控第 3 層交叉連接。

## <a name="point-to-point-ethernet-connections"></a><a name="Ethernet"></a>點對點乙太網路連線
您可以透過點對點乙太網路連結，將內部部署資料中心/辦公室連接到 Microsoft 雲端。 點對點乙太網路提供者可以在您的網路與 Microsoft 雲端之間，提供第 2 層連線或受控第 3 層連線。

## <a name="any-to-any-ipvpn-networks"></a><a name="IPVPN"></a>任意點對任意點 (IPVPN) 網路
您可以整合 WAN 與 Microsoft 雲端。 IPVPN 提供者 (通常是 MPLS VPN) 在您的分公司與資料中心之間提供任意點對任意點連線。 Microsoft 雲端可以相互連接到您的 WAN，看起來就像任何其他分公司一樣。 WAN 提供者通常會提供受控第 3 層連線能力。 在上述所有連線模型中，ExpressRoute 功能與特性完全相同。

## <a name="direct-from-expressroute-sites"></a><a name="Direct"></a>直接從 ExpressRoute 網站
您可以在策略性分散于世界各地的對等互連位置，直接連接到 Microsoft 的全球網路。 [ExpressRoute Direct](expressroute-erdirect-about.md) 提供雙 100 Gbps 或 10 gbps 的連線能力，可支援大規模主動/主動連線。

## <a name="next-steps"></a>後續步驟
* 了解 ExpressRoute 連線和路由網域。 請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md)。
* 了解 ExpressRoute 功能。 請參閱 [ExpressRoute 技術概觀](expressroute-introduction.md)
* 尋找服務提供者。 請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
* 請參閱[路由](expressroute-routing.md)、[NAT](expressroute-nat.md) 和 [QoS](expressroute-qos.md) 的需求。
* 設定 ExpressRoute 連線。
  * [建立 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)
  * [設定路由](expressroute-howto-routing-portal-resource-manager.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-portal-resource-manager.md)