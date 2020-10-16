---
title: 包含檔案
description: 包含檔案
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "67173715"
---
### <a name="what-is-expressroute-global-reach"></a>什麼是 ExpressRoute Global Reach？

ExpressRoute Global Reach 是可透過 Microsoft 的全球網路經由 ExpressRoute 服務連線內部部署網路的 Azure 服務。 例如，如果您有位在加州的私人資料中心連線到位在矽谷的 ExpressRoute，以及另一個位在德州的私人資料中心連線到位在達拉斯的 ExpressRoute，使用 ExpressRoute Global Reach 您可以透過兩個 ExpressRoute 連線將私人資料中心連在一起，而您的跨資料中心流量會透過 Microsoft 的網路骨幹周遊。

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>如何啟用或停用 ExpressRoute Global Reach？

您可將 ExpressRoute 線路連在一起，以啟用 ExpressRoute Global Reach。 您可中斷線路的連線以停用此功能。 請參閱[組態](../articles/expressroute/expressroute-howto-set-global-reach.md)。

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>我需要適用於 ExpressRoute Global Reach 的 ExpressRoute Premium？

如果您的 ExpressRoute 線路位於相同的地緣政治區域中，則不需要 ExpressRoute Premium 即可將它們連在一起。 如果兩個 ExpressRoute 線路位於不同的地緣政治區域中，您則需要適用於這兩個線路的 ExpressRoute Premium，才能讓兩者之間連線。 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>ExpressRoute Global Reach 如何計費？

ExpressRoute 可從內部部署網路連線到 Microsoft 雲端服務。 ExpressRoute Global Reach 能夠利用 Microsoft 的全球網路，透過現有的 ExpressRoute 線路來連線您自己的內部部署網路。 ExpressRoute Global Reach 會與現有 ExpressRoute 服務分開計費。 在每條 ExpressRoute 線路上啟用這項功能需要附加費用。 ExpressRoute Global Reach 所啟用內部部署網路之間的流量，在來源會以輸出速率計費，而在目的地會以輸入速率計費。 費率是以線路所在的區域為基礎。

### <a name="where-is-expressroute-global-reach-supported"></a>哪裡支援 ExpressRoute Global Reach？

[選定的國家/區域或位置](../articles/expressroute/expressroute-global-reach.md)支援 ExpressRoute Global Reach。 您必須在這些國家/區域或位置的對等互連位置建立 ExpressRoute 線路。

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>我有兩個以上的內部部署網路，每個網路都會連線到 ExpressRoute 線路。 可以啟用 ExpressRoute Global Reach 將所有內部部署網路連在一起嗎？

是的，您可以，只要線路位於支援的國家/區域中。 您需要一次連接兩條 ExpressRoute 線路。 若要建立完整網狀網路，您需要列舉所有的線路配對並重複進行設定。 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>可以在位於相同對等互連位置的兩條 ExpressRoute 線路之間啟用 ExpressRoute Global Reach 嗎？

否。 這兩條線路必須來自不同的對等互連位置。 如果支援的國家/區域中的捷運有一個以上的 ExpressRoute 對等互連位置，您可以將在該條捷運不同對等互連位置建立的 ExpressRoute 線路連在一起。 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>如果在線路 X 與線路 Y 以及線路 Y 與線路 Z 之間啟用 ExpressRoute Global Reach，連線到線路 X 和線路 Z 的內部部署網路會透過 Microsoft 的網路彼此通訊嗎？

否。 若要啟用任何兩個內部部署網路之間的連線，您必須明確地連線對應的 ExpressRoute 線路。 在上述範例中，您必須連線線路 X 與線路 Z。 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>在啟用 ExpressRoute Global Reach 之後，我可以預期的內部部署網路間網路輸送量為何？

由 ExpressRoute Global Reach 所啟用的內部部署網路間網路輸送量，是以兩個 ExpressRoute 線路中的較小者為上限。 內部部署到 Azure 的流量和內部部署到內部部署的流量會共用相同的線路，並受限於相同的頻寬上限。 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>使用 ExpressRoute Global Reach 時，可通告的路由數目和我可收到的路由數目有何限制？

您可以透過 Azure 私人對等互連向 Microsoft 通告的路由數目，仍保持為標準線路 4000 個或進階線路 10000 個。 您可透過 Azure 私人對等互連從 Microsoft 接收的路由數目，將是您的 Azure 虛擬網路和您其他透過 ExpressRoute Global Reach 連線的內部部署網路兩者的路由數目總和。 請確實為您的內部部署路由器設定適當的最大前置詞限制。 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>ExpressRoute Global Reach 的 SLA 為何？

ExpressRoute Global Reach 將提供與一般 ExpressRoute 服務相同的[可用性 SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)。
