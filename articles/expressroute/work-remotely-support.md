---
title: 使用 Azure 快速路由支援遠端使用者
description: 本頁介紹如何利用 Azure ExpressRoute 啟用由於 COVID-19 大流行而遠端工作。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336659"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>使用 Azure 快速路由創建混合連接以支援遠端使用者

本文介紹如何利用 ExpressRoute、Azure、Microsoft 網路和 Azure 合作夥伴生態系統遠端工作。

## <a name="connecting-to-azure-services-with-expressroute"></a>使用快速路由連接到 Azure 服務

>[!NOTE]
>本文介紹如何利用 ExpressRoute、Azure、Microsoft 網路和 Azure 合作夥伴生態系統遠端工作，並緩解因 COVID-19 危機而面臨的網路問題。
>

[ExpressRoute](expressroute-introduction.md)是一種 Azure 服務，可在 Microsoft 資料中心和位於您內部或主機代管設施中的基礎結構之間進行專用連線。 ExpressRoute 連線不會經過公用網際網路。 它們提供安全的連接、可靠性和速度，比 Internet 上的典型連接具有更低且一致的延遲。

## <a name="useful-links"></a>實用的連結

* [如何增加現有快速路由電路的頻寬](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [快速路由監控、指標和警報](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [通過快速路由進行路由優化](expressroute-optimize-routing.md)
* [適用于 O365 的 Azure 快速路由](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [非對稱路由注意事項](expressroute-asymmetric-routing.md)
* [如何在 Azure 門戶中打開支援請求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>疑難排解

* [驗證 ExpressRoute 連線](expressroute-troubleshooting-expressroute-overview.md)
* 獲取[資源管理器](expressroute-troubleshooting-arp-resource-manager.md)和[經典](expressroute-troubleshooting-arp-classic.md)中的 ARP 表
* [重置故障電路](reset-circuit.md)
* [排除網路性能故障](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>後續步驟

* [瞭解快速路由連接模型](expressroute-connectivity-models.md)
* 了解 ExpressRoute 連線和路由網域。 請參閱[快速路由電路和路由域](expressroute-circuit-peerings.md)
* 尋找服務提供者。 請參閱[ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
* [創建和修改快速路由電路](expressroute-howto-circuit-portal-resource-manager.md)
* [建立和修改 ExpressRoute 線路的對等互連](expressroute-howto-routing-portal-resource-manager.md)
* [將虛擬網路連線到 ExpressRoute 電路](expressroute-howto-linkvnet-portal-resource-manager.md)
