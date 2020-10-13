---
title: Azure ExpressRoute 概欟：透過私人連線來連結
description: 此「ExpressRoute 技術概觀」說明 ExpressRoute 連線如何透過私人連線，將內部部署網路延伸至 Azure。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: ee690a73907eca3bcd577cf2d983c8abc5409925
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743058"
---
# <a name="what-is-azure-expressroute"></a>什麼是 Azure ExpressRoute？
ExpressRoute 可讓您藉由連線提供者的協助，透過私人連線將內部部署網路延伸至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure 和 Microsoft 365。

從任意點對任意點 (IP VPN) 網路、點對點乙太網路，或在共置設施上透過連線提供者的虛擬交叉連接，都可以進行連線。 ExpressRoute 連線不會越過公用網際網路。 相較於一般網際網路連線，這可讓 ExpressRoute 連線提供更高的可靠性、更快的速度、一致的延遲和更高的安全性。 如需如何使用 ExpressRoute 將您的網路連接至 Microsoft 的詳細資訊，請參閱 [ExpressRoute 連線模型](expressroute-connectivity-models.md)。

![ExpressRoute 連線概觀](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>主要權益

* 內部部署網路與 Microsoft Cloud 之間透過連線提供者的第 3 層連線能力。 從任意點對任意點 (IP VPN) 網路、點對點乙太網路，或透過乙太網路交換經由虛擬交叉連接，都可以進行連線。
* 跨地理政治區域中的所有區域連接到 Microsoft 雲端服務。
* 透過 ExpressRoute Premium 附加元件從全球連線到所有區域的 Microsoft 服務。
* 透過 BGP 在網路與 Microsoft 之間進行動態路由。
* 每個對等位置有內建的備援性，可靠性更高。
* 連線執行時間 [SLA](https://azure.microsoft.com/support/legal/sla/)。
* 商務用 Skype 的 QoS 支援。

如需詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

## <a name="features"></a>特性

### <a name="layer-3-connectivity"></a>第 3 層連線能力
Microsoft 使用 BGP，這是一種業界標準動態路由通訊協定。 用來在內部部署網路、Azure 中的執行個體與 Microsoft 公用位址之間交換路由。 我們會針對不同的流量設定檔，與您的網路建立多個 BGP 工作階段。 如需詳細資訊，請參閱 [ExpressRoute 線路和路由網域](expressroute-circuit-peerings.md) 一文。

### <a name="redundancy"></a>備援性
每個 ExpressRoute 線路有兩條連線，會從連線提供者/您的網路邊緣連線至兩個位於 [ExpressRoute 位置](https://docs.microsoft.com/azure/expressroute/expressroute-locations#expressroute-locations)的 Microsoft Enterprise 邊緣路由器 (MSEE)。 Microsoft 需要有來自連線提供者 / 您網路邊緣的雙重 BGP 連線 – 每個連線皆各自連線至每個 MSEE。 您可以選擇不要在您這端部署備援裝置 / 乙太網路線路。 不過，連線提供者會使用備援裝置，確保以備援方式將您的連線交給 Microsoft。 備援第 3 層連線組態是我們的 [SLA](https://azure.microsoft.com/support/legal/sla/) 生效的條件。

### <a name="connectivity-to-microsoft-cloud-services"></a>連線到 Microsoft 雲端服務
透過 ExpressRoute 連線可存取下列服務：
* Microsoft Azure 服務
* Microsoft 365 服務

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

如需透過 ExpressRoute 所支援的詳細服務清單，請瀏覽 [ExpressRoute 常見問題集](expressroute-faqs.md)頁面。

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>連線到地理政治區域內的所有區域
您可以在我們的其中一個[對等互連位置](expressroute-locations.md)中連線到 Microsoft，並存取地理政治區域內的區域。

例如，如果您在阿姆斯特丹透過 ExpressRoute 連線至 Microsoft， 您就能夠存取在北歐和西歐中託管的所有 Microsoft 雲端服務。 如需地理政治地區、相關聯的 Microsoft 雲端區域以及對應的 ExpressRoute 對等互連位置之概觀，請參閱 [ExpressRoute 合作夥伴和對等互連位置](expressroute-locations.md)一文。

### <a name="global-connectivity-with-expressroute-premium"></a>搭配 ExpressRoute Premium 的全球連線能力
您可以啟用 [ExpressRoute Premium](expressroute-faqs.md) 來將連線能力延伸到跨越地理政治的界限。 例如，如果您在阿姆斯特丹透過 ExpressRoute 連線至 Microsoft，您就能夠存取在全球各地託管的所有 Microsoft 雲端服務。 就像存取北歐和西歐區域一樣，您也可以存取部署在南美洲或澳大利亞的服務。 國家 (地區) 雲端排除在外。

### <a name="local-connectivity-with-expressroute-local"></a>搭配 ExpressRoute Local 的本地連線能力
您可以啟用[本機 SKU](expressroute-faqs.md)，以符合成本效益的方式傳輸資料。 使用本機 SKU，您可以將資料帶至您想要的 Azure 區域附近的 ExpressRoute 位置。 透過使用 Local，資料轉送將會被包含在 ExpressRoute 連接埠費用中。 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>使用 ExpressRoute Global Reach 跨內部部署連線
您可以透過連線 ExpressRoute 線路，以啟用 ExpressRoute Global Reach 在內部部署站台之間交換資料。 例如，如果您在加州有一個連線到矽谷 ExpressRoute 線路的私人資料中心，而在德克薩斯州有另一個連線到達拉斯 ExpressRoute 線路的私人資料中心。 使用 ExpressRoute Global Reach，您就可以透過這兩個 ExpressRoute 線路將您的私人資料中心連線在一起。 您跨資料中心的流量會透過 Microsoft 的網路周遊。

如需詳細資訊，請參閱 [ExpressRoute Global Reach](expressroute-global-reach.md)。
### <a name="rich-connectivity-partner-ecosystem"></a>豐富的連線合作夥伴生態系統
ExpressRoute 的連線提供者和系統整合者合作夥伴生態系統持續成長茁壯。 如需最新資訊，請參閱 [ExpressRoute 合作夥伴和對等互連位置](expressroute-locations.md)。

### <a name="connectivity-to-national-clouds"></a>連線到國家雲端
Microsoft 為特殊的地理政治地區和客戶群提供隔離的雲端環境。 如需國家雲端和提供者的清單，請參閱 [ExpressRoute 合作夥伴和對等互連位置](expressroute-locations.md)頁面。

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct 讓客戶有機會在策略性分散於世界各地的對等互連位置上，直接連線至 Microsoft 全球網路。 ExpressRoute Direct 提供雙向 100-Gbps 連線，且支援大規模主動/主動連線。

ExpressRoute Direct 提供的主要功能包括但不限於：

* 將資料大量擷取至儲存體與 Cosmos DB 等服務
* 實體隔離適用於受規範且需要專用隔離連線的產業，例如：銀行、政府機構及零售業
* 根據業務單位決定的細微線路分散控制

如需詳細資訊，請參閱[關於 ExpressRoute Direct](https://go.microsoft.com/fwlink/?linkid=2022973)。

### <a name="bandwidth-options"></a>頻寬選項
您可以購買各種頻寬的 ExpressRoute 線路。 支援的頻寬列示如下。 請務必洽詢您的連線提供者，以判斷他們支援的頻寬。

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>動態調整頻寬
您可以在不中斷連線的情況下﹐增加 ExpressRoute 線路頻寬 (盡最大努力)。 如需詳細資訊，請參閱[修改 ExpressRoute 線路](expressroute-howto-circuit-portal-resource-manager.md#modify)。

### <a name="flexible-billing-models"></a>彈性的計費模型
您可以挑選最適合的計費模型。 請從如下所列的計費模型中選擇。 如需詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

* **無限制資料**。 帳單為按月計費；所有輸入和輸出資料傳輸已免費包括在內。
* **已計量資料**。 帳單為按月計費；所有輸入資料傳輸皆為免費。 輸出資料傳輸依每 GB 資料傳輸計費。 資料傳輸費率依區域而不同。
* **ExpressRoute Premium 附加元件**。 ExpressRoute Premium 是 ExpressRoute 線路的附加元件。 ExpressRoute Premium 附加元件提供下列功能： 
  * Azure 公用和 Azure 私用對等的路由限制從 4,000 個路由提高到 10,000 個路由。
  * 從全球連線到服務。 任何區域中建立的 ExpressRoute 線路 (不包括國家雲端) 能夠存取全球其他各地的資源。 例如，透過在美國矽谷佈建的 ExpressRoute 線路，可存取在西歐建立的虛擬網路。
  * 每個 ExpressRoute 線路的 VNet 連結數目已從原本 10 個放寬限制，視線路的頻寬而定。

## <a name="faq"></a>常見問題集
如需有關 ExpressRoute 的常見問題，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。

## <a name="whats-new"></a><a name="new"></a>新功能

訂閱 RSS 摘要，並在 [Azure 更新](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute) 頁面上查看最新的 ExpressRoute 功能更新。

## <a name="next-steps"></a>後續步驟
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。
* 深入了解 [ExpressRoute 連線模型](expressroute-connectivity-models.md)。
* 尋找服務提供者。 請參閱 [ExpressRoute 合作夥伴和對等位置](expressroute-locations.md)。
