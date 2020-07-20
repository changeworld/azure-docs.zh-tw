---
title: Azure 對等服務總覽
description: 瞭解 Azure 對等服務總覽
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 548d4f90bd9632e4807547120ac1be589668e8fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871338"
---
# <a name="azure-peering-service-overview"></a>Azure 對等服務總覽

Azure 對等互連服務是一項網路服務，可增強客戶對 Microsoft 雲端服務，例如 Office 365、Dynamics 365、軟體即服務 (SaaS) 服務、Azure，或可透過公用網際網路存取的任何 Microsoft 服務的連線能力。 Microsoft 已與網際網路服務提供者（Isp）、網際網路交換夥伴（IXPs），以及全球軟體定義的雲端互連（SDCI）提供者合作，提供可靠且高效能的公用連線，以及從客戶到 Microsoft 網路的最佳路由。

透過對等互連服務，客戶可以在指定區域中選取連線良好的夥伴服務提供者。 公用連線已針對從雲端服務到使用者位置的高可靠性和最小延遲而優化。

![Microsoft 雲端的分散式連線能力](./media/peering-service-about/peering-service-what.png)

客戶也可以在 Azure 入口網站中註冊對等互連服務連線，以選擇對等互連服務遙測，例如使用者延遲量值到 Microsoft 網路、BGP 路由監視，以及針對流失和劫持發出警示。 

若要使用對等互連服務，客戶不需要向 Microsoft 註冊。 唯一的要求是聯絡對[等服務合作夥伴](location-partners.md)以取得服務。 若要加入宣告對等互連服務遙測，客戶必須在 Azure 入口網站中註冊。

如需如何註冊對等互連服務的指示，請參閱[使用 Azure 入口網站註冊對等服務](azure-portal.md)。 

> [!NOTE]
> 本文適用于網路架構設計人員，負責對雲端和網際網路的企業連線能力。


## <a name="what-is-peering-service"></a>什麼是對等互連服務？

對等互連服務為：

- 使用公用網際網路的 IP 服務。 
- 一種共同作業平臺，提供服務提供者和增值服務，其目標是透過服務提供者合作夥伴，透過公用網路提供最佳且可靠的路由傳送至 Microsoft 雲端。

對等互連服務不是 Azure ExpressRoute 或 VPN 產品之類的私用連接產品。

> [!NOTE]
> 如需 ExpressRoute 的詳細資訊，請參閱[expressroute 檔](https://docs.microsoft.com/azure/expressroute/)。
>

## <a name="background"></a>背景

Office 365、Dynamics 365 和任何其他 Microsoft SaaS 服務都會裝載于多個 Microsoft 資料中心，並可從任何地理位置存取。 Microsoft 全球網路具有全球各地的 Microsoft Edge 存在點（PoP）位置，其可透過其服務提供者連接到使用者。 

Microsoft 和合作夥伴服務提供者可確保在對等互連服務連線中註冊的前置詞流量，會進入並結束 Microsoft 全球網路上最接近的 Microsoft Edge PoP 位置。 Microsoft 會確保從向對等服務連線註冊的首碼支付的網路流量，會在 Microsoft 全球網路上使用最接近的 Microsoft Edge PoP 位置。

![Microsoft 網路和公用連線能力](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> 如需 Microsoft 全球網路的詳細資訊，請參閱[microsoft 全球網路](https://docs.microsoft.com/azure/networking/microsoft-global-network)。
>

## <a name="why-use-peering-service"></a>為何要使用對等互連服務？

尋找網際網路優先存取雲端或考慮 SD WAN 架構或使用 Microsoft SaaS 服務高用量的企業，需要健全且高效能的網際網路連線能力。 客戶可以使用對等服務來進行轉換。 Microsoft 與服務提供者合作，為 Microsoft 雲端提供可靠且以效能為中心的公用連線能力。 以下列出一些主要的客戶功能：

- 透過網際網路的最佳公用路由來 Microsoft Azure 雲端服務，以獲得最佳效能和可靠性。
- 能夠選取慣用的服務提供者來連接到 Microsoft cloud。
- 流量深入解析，例如延遲報告和前置詞監視。
- 來自 Microsoft cloud 的最佳網路躍點（AS 躍點）。
- 路由分析和統計資料：（[BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)）路由異常（遺漏或劫持偵測）和次佳路由的事件。

### <a name="robust-reliable-peering"></a>穩固可靠的對等互連

對等互連服務會使用兩種類型的冗余：

- **本機冗余**

   Microsoft 和服務提供者會跨多個 Microsoft Edge PoP 位置互連，以提供對等服務。 在每個位置中，相互連線必須支援跨兩個路由器的容錯移轉。

   每個對等互連位置都會以多餘且多樣化的對等互連連結來布建。

- **異地冗余**

   Microsoft 與多個 metro 位置的服務提供者相互連接，因此，如果其中一個邊緣節點的效能降低，流量就會透過替代網站向 Microsoft 路由傳送。 Microsoft 會使用 SDN 型路由原則來路由傳送其全球網路中的流量，以獲得最佳效能。

    這種類型的冗余會使用最短的路由路徑，方法是一律選擇最接近的 Microsoft Edge PoP 給終端使用者，並確保客戶是遠離 Microsoft 的一個網路躍點（作為躍點）。

   ![異地備援](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>最佳路由

建議採用下列路由技術：

-  **冷刷路由**

   軟體定義的冷刷路由技術提供來自 Microsoft cloud 的網路流量控制。 它可確保流量會保持在高容量、低延遲且高度可靠的 Microsoft 全球網路上，直到盡可能接近目的地為止。
   
   未使用冷刷技術的路由稱為熱刷路由。 使用熱刷路由時，來自 Microsoft 雲端的流量會經過網際網路。

   ![冷刷路由](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>監視平臺

   提供服務監視來分析客戶流量和路由，並提供下列功能： 

-  **網際網路 BGP 路由異常偵測**
          
   此服務可用來偵測任何路由異常事件，併發出警示，例如路由劫持到客戶的首碼。

-  **客戶延遲**

   此服務會監視客戶位置和 Microsoft 之間的路由效能。 
   
   藉由驗證從用戶端到 Microsoft Edge PoP 的來回行程時間來測量路由效能。 客戶可以查看不同地理位置的延遲報表。

   監視會在任何服務降低的情況下捕捉事件。

   ![對等互連服務的監視平臺](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>流量保護

路由只會透過客戶向對等服務註冊時所定義的慣用路徑進行。

Microsoft 保證即使偵測到惡意活動，也會透過慣用路徑來路由傳送流量。

BGP 路由異常會在 Azure 入口網站中報告（如果有的話）。

## <a name="next-steps"></a>後續步驟

- 若要深入瞭解對等互連服務連線，請參閱對[等互連服務連接](connection.md)。
- 若要深入了解對等互連服務連線遙測，請參閱[對等互連服務連線遙測](connection-telemetry.md)。
- 若要尋找服務提供者合作夥伴，請參閱對[等互連服務合作夥伴和位置](location-partners.md)。
- 若要讓對等互連服務連線上線，請參閱上[架對等互連服務模型](onboarding-model.md)。
- 若要使用 Azure 入口網站註冊連接，請參閱[使用 Azure 入口網站註冊對等互連服務連接](azure-portal.md)。
- 若要測量遙測，請參閱[測量連線遙測](measure-connection-telemetry.md)。
