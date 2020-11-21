---
title: Azure 對等互連服務總覽
description: 深入瞭解 Azure 對等互連服務總覽
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 198ba23920179e71e095e498ee2173d7f0111d42
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026724"
---
# <a name="azure-peering-service-overview"></a>Azure 對等互連服務總覽

Azure 對等互連服務是一項網路服務，可增強客戶對 Microsoft 雲端服務，例如 Microsoft 365、Dynamics 365、軟體即服務 (SaaS) 服務、Azure，或可透過公用網際網路存取的任何 Microsoft 服務的連線能力。 Microsoft 已與網際網路服務提供者合作 (Isp) 、網際網路 exchange 合作夥伴 (IXPs) ，以及軟體定義的雲端互連 (SDCI) 提供者，提供可靠且高效能的公用連線，並提供客戶最佳的路由傳送至 Microsoft 網路。

使用對等互連服務時，客戶可以在指定的區域中選取妥善連線的夥伴服務提供者。 公用連線已針對高可靠性以及從雲端服務到使用者位置的最短延遲進行優化。

![Microsoft cloud 的分散式連接](./media/peering-service-about/peering-service-what.png)

客戶也可以在 Azure 入口網站中註冊對等互連服務連線，以選擇對等互連服務遙測，例如 Microsoft 網路的使用者延遲量值、BGP 路由監視，以及洩漏和劫持的警示。 

若要使用對等互連服務，客戶不需要向 Microsoft 註冊。 唯一的需求是與對 [等互連服務夥伴](location-partners.md) 聯繫以取得服務。 若要加入對等互連服務遙測，客戶必須在 Azure 入口網站中註冊。

如需如何註冊對等互連服務的指示，請參閱 [使用 Azure 入口網站註冊對等互連服務](azure-portal.md)。 

> [!NOTE]
> 本文適用于負責企業連線至雲端和網際網路的網路架構設計人員。


## <a name="what-is-peering-service"></a>什麼是對等互連服務？

對等互連服務是：

- 使用公用網際網路的 IP 服務。 
- 使用服務提供者和加值型服務的共同作業平臺，其目的是透過服務提供者合作夥伴，透過公用網路向 Microsoft 雲端提供最佳且可靠的路由傳送給客戶。

對等互連服務不是私人連線產品，例如 Azure ExpressRoute 或 VPN 產品。

> [!NOTE]
> 如需 ExpressRoute 的詳細資訊，請參閱 [expressroute 檔](../expressroute/index.yml)。
>

## <a name="background"></a>背景

Microsoft 365、Dynamics 365 和任何其他 Microsoft SaaS 服務都裝載于多個 Microsoft 資料中心，而且可以從任何地理位置存取。 Microsoft 全球網路在世界各地都有 Microsoft Edge 的存在點 (PoP) 位置，可透過其服務提供者連接到使用者。 

Microsoft 與合作夥伴服務提供者可確保以對等互連服務連線所註冊之首碼的流量，會進入並結束 Microsoft 全球網路上最接近的 Microsoft Edge PoP 位置。 Microsoft 可確保從以對等互連服務連線所註冊的首碼零輸出的網路流量，會採用 Microsoft 全球網路上最接近的 Microsoft Edge PoP 位置。

![Microsoft 網路和公用連線能力](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> 如需 Microsoft 全球網路的詳細資訊，請參閱 [microsoft 全球網路](../networking/microsoft-global-network.md)。
>

## <a name="why-use-peering-service"></a>為何要使用對等互連服務？

企業需要以網際網路優先的方式存取雲端或考慮使用 SD WAN 架構，或使用 Microsoft SaaS 服務的高用量，需要強大且高效能的網際網路連線能力。 客戶可以使用對等互連服務來進行這項轉換。 Microsoft 與服務提供者合作，為 Microsoft 雲端提供可靠且以效能為中心的公用連線能力。 以下列出一些主要的客戶功能：

- 網際網路上的最佳公用路由，可 Microsoft Azure 雲端服務以獲得最佳效能和可靠性。
- 能夠選取慣用的服務提供者以連接到 Microsoft cloud。
- 流量見解，例如延遲報告和前置詞監視。
- 從 Microsoft 雲端) 的躍點 (的最佳網路躍點。
- 路由分析和統計資料： ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) 路由異常的事件 (洩漏或劫持偵測) 和次佳路由。

### <a name="robust-reliable-peering"></a>穩固可靠的對等互連

對等互連服務會使用兩種類型的冗余：

- **本機冗余**

   Microsoft 與服務提供者可跨多個 Microsoft Edge PoP 位置互連，以提供對等互連服務。 在每個位置中，互相連線必須支援在兩個路由器之間進行容錯移轉。

   每個對等互連位置都會布建重複且多樣化的對等互連連結。

- **異地備援**

   Microsoft 已在多個 metro 位置與服務提供者互連，因此，如果其中一個邊緣節點的效能降低，則流量會透過替代網站路由傳送至 Microsoft。 Microsoft 會使用 SDN 型路由原則來路由傳送其全球網路中的流量，以獲得最佳效能。

    這種類型的冗余會使用最短的路由路徑，方法是一律選擇最接近的 Microsoft Edge PoP 給終端使用者，並確保客戶是一個網路躍點 (作為躍點) 離開 Microsoft。

   ![異地備援](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>最佳路由

慣用的路由技術如下：

-  **冷硬式路由**

   軟體定義的冷硬式路由技術可讓您控制源自 Microsoft 雲端的網路流量。 它可確保流量會一直留在高容量、低延遲且高度可靠的 Microsoft 全球網路，直到接近目的地為止。
   
   未使用冷刷技術的路由稱為熱刷式路由。 透過熱刷的路由，來自 Microsoft 雲端的流量接著會通過網際網路。

   ![冷硬式路由](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>監視平臺

   提供服務監視來分析客戶流量和路由，並提供下列功能： 

-  **網際網路 BGP 路由異常偵測**
          
   這項服務可用來偵測任何路由異常事件（例如路由劫持至客戶首碼）併發出警示。

-  **客戶延遲**

   此服務會監視客戶位置與 Microsoft 之間的路由效能。 
   
   路由效能的測量方式是驗證從用戶端到 Microsoft Edge PoP 所採取的來回行程時間。 客戶可以查看不同地理位置的延遲報表。

   監視會在任何服務降低時捕捉事件。

   ![對等互連服務的監視平臺](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>流量保護

路由只會透過向對等互連服務註冊客戶時定義的慣用路徑進行。

Microsoft 保證即使偵測到惡意活動，也能透過慣用路徑路由傳送流量。

在 Azure 入口網站中報告 BGP 路由異常（如果有的話）。

## <a name="next-steps"></a>下一步

- 若要深入瞭解對等互連服務連接，請參閱對 [等互連服務](connection.md)連線。
- 若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。
- 若要尋找服務提供者合作夥伴，請參閱對 [等互連服務夥伴和位置](location-partners.md)。
- 若要讓對等互連服務連線上線，請參閱上 [架對等互連服務模型](onboarding-model.md)。
- 若要使用 Azure 入口網站註冊連接，請參閱 [使用 Azure 入口網站註冊對等互連服務](azure-portal.md)連線。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。