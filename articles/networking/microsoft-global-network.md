---
title: 微軟全球網路 - Azure
description: 描述微軟如何建立快速可靠的全球網路
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453062"
---
# <a name="microsoft-global-network"></a>Microsoft 全域網路

微軟擁有並運營著世界上最大的骨幹網路之一。 這種全球和複雜的架構跨越 100，000 英里，連接我們的資料中心和客戶。 
 
每天，世界各地的客戶都會將數萬億個請求連接到 Microsoft Azure、必應、Dynamics 365、Office 365、XBox 等。 無論類型如何，客戶都期望我們的服務能夠即時可靠性和回應能力。 
 
[微軟全球網路](https://azure.microsoft.com/global-infrastructure/global-network/)（WAN） 是提供巨大雲體驗的核心部分。 我們的全球網路將 Microsoft[資料中心](https://azure.microsoft.com/global-infrastructure/)連接到全球 54 個 Azure 區域和大型邊緣節點網格，提供可用性、容量和靈活性，以滿足任何需求。

![Microsoft 全域網路](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>獲取優質雲網路
 
當您使用 Microsoft 雲時，選擇[最佳體驗](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/)非常簡單。 從客戶流量通過我們戰略性邊緣節點進入我們的全球網路的那一刻起，您的資料就以接近光速的速度通過優化的路線傳輸。 這可確保最佳延遲，實現最佳性能。 這些邊緣節點通過超過 145 個位置的數千個連接與 3500 多個獨特的 Internet 合作夥伴（對等方）互連，為我們的互連戰略奠定了基礎。 
 
無論是從倫敦到東京，還是從華盛頓特區到洛杉磯，網路性能都會被量化，並受延遲、抖動、資料包丟失和輸送量等因素的影響。  在 Microsoft，我們更喜歡並使用直接互連而不是傳輸鏈路，這樣可以保持回應流量對稱，並有助於盡可能短和簡單地保持躍點、對等方和路徑。 

例如，如果倫敦的使用者嘗試訪問東京的服務，那麼 Internet 流量就會進入我們在倫敦的一個邊緣，通過法國、我們在歐洲和印度之間的跨阿拉伯路徑，然後到達託管該服務的日本。 回應流量是對稱的。 這有時稱為[冷土豆路由](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing)，這意味著流量在微軟網路上停留的時間盡可能長，然後再將其傳遞。  
  
那麼，在使用 Microsoft 服務時，這是否意味著任何和所有流量？ 是的，資料中心之間、Microsoft Azure 內或 Microsoft 服務（如虛擬機器、Office 365、XBox、SQL DB、存儲和虛擬網路）之間的任何流量都在我們的全球網路中路由，並且永遠不會通過公共 Internet 路由，以確保最佳性能和完整性。  
 
在地鐵、陸地和海底路徑的光纖容量和多樣性方面進行大量投資，對於我們保持一致和高水準的服務等級至關重要，同時推動我們的雲和線上服務的極端增長。 我們全球網路最近增加了我們的[MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea)海底電纜，這是業界首款穿越海底的開通線系統（OLS），位於西班牙畢爾巴鄂和美國弗吉尼亞州佛吉尼亞海灘之間，以及位於美國紐約和都柏林之間的[AEC，](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1)以及日本東京和美國俄勒岡州波特蘭之間的[新太平洋（NCP）。](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) 
 

## <a name="our-network-is-your-network"></a>我們的網路就是您的網路

我們投入了 20 年的經驗，以及對網路的大量投資，以確保始終獲得最佳性能。 企業可以充分利用我們的網路資產，並在頂部構建高級疊加架構。 
 
Microsoft Azure 提供了最豐富的服務和功能組合，使客戶能夠隨時隨地快速輕鬆地構建、擴展和滿足網路要求。 我們的連接服務系列跨越區域、混合和雲中點對點和網站到網站架構以及全球 IP 傳輸方案之間的虛擬網路對等互連。  對於希望將自己的資料中心或網路連接到 Azure 的企業，或具有海量資料引入或傳輸需求的客戶[，ExpressRoute](../expressroute/expressroute-introduction.md)和[ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md)提供高達 100 Gbps 的頻寬選項，直接連接到 Microsoft 全球網路的全球網路，位於世界各地的對等位置。  
 
[ExpressRoute 全球覆蓋](../expressroute/expressroute-global-reach.md)旨在補充服務提供者的 WAN 實現，並連接世界各地的本地網站。 如果運行全域操作，則可以使用 ExpressRoute 全球覆蓋與您首選的本地服務提供者結合使用 Microsoft 全球網路連接所有全球網站。 通過 Azure 虛擬 WAN 擴展雲中的新網路以涵蓋大量分支網站，從而能夠借助 SDWAN & VPN 設備（即客戶場所設備或 CPE）將分支機搆無縫連接到 Microsoft 全球網路，並內置易用性和自動化連接和建構管理。 
 
[全域 VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)使客戶能夠跨區域無縫連接兩個或多個 Azure 虛擬網路。 對等後，虛擬網路將顯示為一個。 對等虛擬網路中虛擬機器之間的流量通過 Microsoft 骨幹基礎結構路由，就像流量在同一虛擬網路中的虛擬機器之間路由一樣 - 只能通過私人 IP 位址。 
 

## <a name="well-managed-using-software-defined-innovation"></a>使用軟體定義的創新進行良好管理

Microsoft 運行世界領先的雲之一，在構建和管理高性能全球基礎架構方面獲得了許多洞察力和經驗。  
 
我們堅持一套強有力的運營原則： 
 
- 跨網路的各個層使用同類最佳的交換硬體。  
- 部署對最終使用者沒有影響的新功能。  
- 盡可能快地跨車隊安全可靠地推出更新。 小時而不是幾周。  
- 利用雲級深度遙測和全自動故障緩解。  
- 使用統一和軟體定義的網路技術來控制網路中的所有硬體元素。  消除重複並減少故障。 
 
這些原則適用于網路的所有層：從主機網路介面、交換平臺、資料中心的網路功能（如負載等化器）一直使用我們的交通工程平臺和光纖網路，一直連接到 WAN。  
 
Azure 及其網路的指數級增長已經達到我們最終意識到人類直覺不再依賴來管理全球網路操作的點。 為了滿足驗證網路上長期、中期和短期變化的需要，我們開發了一個平臺來綜合鏡像和類比我們的生產網路。 創建鏡像環境和運行數百萬次類比的能力使我們能夠測試軟體和硬體更改及其影響，然後再將其提交到我們的生產平臺和網路。 

## <a name="next-steps"></a>後續步驟
- [瞭解有關 Azure 中提供的網路服務](https://azure.microsoft.com/product-categories/networking/)
