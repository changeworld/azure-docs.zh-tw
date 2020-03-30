---
title: 包含檔案
description: 包含檔案
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1f6c65d6d8da275d6c52ac2802092dbb5e4e507d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73182182"
---
### <a name="what-is-expressroute-direct"></a>什麼是 ExpressRoute Direct？

ExpressRoute Direct 為客戶提供能夠將策略性分散於世界各地的對等互連位置，都直接連線至 Microsoft 全球網路的能力。 ExpressRoute Direct 提供雙 100 或 10 Gbps 連接，支援大規模主動/主動連接。 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>客戶如何連線至 ExpressRoute Direct？ 

客戶需要與當地的電訊廠商和共置提供者合作，才能連線到 ExpressRoute 路由器以充分利用 ExpressRoute Direct。

### <a name="what-locations-currently-support-expressroute-direct"></a>當前哪些位置支援快速路由直接？ 

請檢查[位置頁面上](../articles/expressroute/expressroute-locations-providers.md)的可用性。 

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct 的 SLA 為何？

ExpressRoute Direct 會利用相同的[企業級 ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)。 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>客戶應針對哪些案例考慮使用 ExpressRoute Direct？  

ExpressRoute Direct 為客戶提供直接 100 或 10 Gbps 埠對到 Microsoft 全球主幹。 能為客戶提供絕佳好處的案例包括：大規模資料擷取、針對受嚴格規範市場的物理隔離和高載案例 (例如轉譯) 的專用容量。 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct 的計費模型為何？ 

ExpressRoute Direct 會針對連接埠配對收取固定的金額。 標準線路將包含在內，不會計算額外時數，而進階線路將會有些許附加元件費用。 輸出將會根據對等位置的區域，按每個線路計費。

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>快速路由直接埠對何時開始計費？

ExpressRoute Direct 的連接埠配對在建立 ExpressRoute Direct 資源 45 天後或在啟用其中一個或同時兩個連結 (視何者先) 時收費。 在 45 天寬限期內會允許客戶與共置提供者完成跨連線程序。
