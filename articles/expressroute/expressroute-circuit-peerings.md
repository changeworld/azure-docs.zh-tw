---
title: Azure ExpressRoute：線路和對等互連
description: 此頁面提供 ExpressRoute 線路和路由網域/對等互連的概觀。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mialdrid
ms.openlocfilehash: c68ffd019937f902567c3deda8d879448dc082da
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79281349"
---
# <a name="expressroute-circuits-and-peering"></a>ExpressRoute 線路和對等互連

ExpressRoute 線路可透過連線提供者將內部部署基礎結構連線到 Microsoft。 本文將協助您了解 ExpressRoute 線路和路由網域/對等互連。 下圖所示的是 WAN 與 Microsoft 之間連線的邏輯表示法。

![](./media/expressroute-circuit-peerings/expressroute-basic.png)

> [!IMPORTANT]
> Azure 公用對等互連已被取代，不適用於新的 ExpressRoute 線路。 新線路支援 Microsoft 對等互連和私用對等互連。  
>

## <a name="circuits"></a>ExpressRoute 線路

ExpressRoute 線路代表您的內部部署基礎結構與 Microsoft 雲端服務之間，透過連線提供者的邏輯連線。 您可以訂購多條 ExpressRoute 線路。 每一條線路可以位於相同或不同區域，且可透過不同的連線提供者連線到您的內部環境。

ExpressRoute 線路不對應至任何實體裝置。 線路由一個稱為服務金鑰 (s 金鑰) 的標準 GUID 唯一識別。 服務金鑰是 Microsoft、連線提供者與您之間唯一會交換的資訊。 S 金鑰不是安全性用途的密碼。 ExpressRoute 線路與 s 金鑰之間存在 1:1 對應。

新的 ExpressRoute 線路可以包含兩個獨立的對等互連：私用對等互連和 Microsoft 對等互連。 然而，現有的 ExpressRoute 線路可能包含三個對等互連：Azure 公用、Azure 私用和 Microsoft。 每個對等互連均為一對獨立的 BGP 工作階段，其中每一個都會重複設定，以確保高可用性。 ExpressRoute 線路與路由網域之間存在 1:N (1 <= N <= 3) 對應。 每個 ExpressRoute 電路可以啟用任何一個、兩個或全部三個對等。

每個電路有固定的頻寬 (50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、10 Gbps)，而且對應至連線提供者和對等位置。 您選取的頻寬會在所有線路對等互連中共用

### <a name="quotas"></a>配額和限制

每個 ExpressRoute 線路會套用預設的配額和限制。 如需最新的配額資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md) 。

## <a name="routingdomains"></a>ExpressRoute 對等互連

ExpressRoute 線路有多個相關聯的路由網域/對等互連： Azure 公用、Azure 私用和 Microsoft。 每個對等互連在一對路由器上的設定完全相同 (使用主動-主動或載入共用設定)，以確保高可用性。 Azure 服務分成 *Azure 公用*和 *Azure 私用*兩類來代表 IP 定址配置。

![](./media/expressroute-circuit-peerings/expressroute-peerings.png)

### <a name="privatepeering"></a>Azure 私人對等互連

部署於虛擬網路內的 Azure 計算服務 (也就是虛擬機器 (IaaS) 和雲端服務 (PaaS)) 可透過私用對等網域進行連線。 私人對等互連網域會被視為核心網路至 Microsoft Azure 的受信任延伸。 您可以在核心網路與 Azure 虛擬網路 (VNet) 之間設定雙向連線。 此對等可讓您直接連線到虛擬機器和雲端服務的私人 IP 位址。  

您可以將多個虛擬網路連線到私人對等互連網域。 如需限制的相關資訊，請檢閱 [常見問題集頁面](expressroute-faqs.md) 。 如需最新的限制資訊，請參閱 [Azure 訂用帳戶和服務限制、配額及條件約束](../azure-resource-manager/management/azure-subscription-service-limits.md) 。  如需路由組態的詳細資訊，請參閱 [路由](expressroute-routing.md) 頁面。

### <a name="microsoftpeering"></a>Microsoft 對等互連

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Microsoft 線上服務（Office 365 和 Azure PaaS 服務）的連線會透過 Microsoft 對等互連進行。 我們會透過 Microsoft 對等路由網域啟用 WAN 與 Microsoft 雲端服務之間的雙向連線。 您只能透過您或連線提供者所擁有的公用 IP 位址連線到 Microsoft 雲端服務，且必須遵守所有已定義的規則。 如需詳細資訊，請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)頁面。

如需支援的服務、費用和組態的詳細資訊，請參閱 [常見問題集頁面](expressroute-faqs.md) 。 如需有關提供 Microsoft 對等支援的連線提供者清單的詳細資訊，請參閱 [ExpressRoute 位置](expressroute-locations.md) 頁面。

## <a name="peeringcompare"></a>對等互連比較

下表是這三種對等互連的比較：

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

您可能會啟用一或多個路由網域作為 ExpressRoute 線路的一部分。 如果想要將所有路由網域合併成單一路由網域，您可以選擇將所有路由網域放在相同的 VPN。 您也可以將他們放在不同的路由網域，類似上圖。 建議的設定是將私用對等直接連線到核心網路，而將公用和 Microsoft 對等連結連線到您的 DMZ。

每個對等互連都需要不同的 BGP 工作階段 (每個對等互連類型一對)。 BGP 工作階段配對提供高可用性連結。 若您透過第 2 層連線提供者來連線，則要負責設定和管理路由。 如需深入瞭解，請檢閱有關設定 ExpressRoute 的 [工作流程](expressroute-workflows.md) 。

## <a name="health"></a>ExpressRoute 健康情況

您可能會基於可用性、與 VNet 的連線能力及頻寬使用率，使用[網路效能監控](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) 來監視 ExpressRoute 線路。

NPM 會監視 Azure 私用對等互連和 Microsoft 對等互連的健康情況。 如需詳細資訊，請參閱我們的[文章](https://azure.microsoft.com/blog/monitoring-of-azure-expressroute-in-preview/) \(英文\)。

## <a name="next-steps"></a>後續步驟

* 尋找服務提供者。 請參閱 [ExpressRoute 服務提供者和位置](expressroute-locations.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
* 設定 ExpressRoute 連線。
  * [建立和管理 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md)
  * [設定 ExpressRoute 線路的路由 (對等戶連)](expressroute-howto-routing-portal-resource-manager.md)
