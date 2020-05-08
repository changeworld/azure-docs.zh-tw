---
title: Microsoft 全球網路-Azure
description: 說明 Microsoft 如何建立其快速且可靠的全域網路
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
ms.openlocfilehash: b78e227159065d2ae85d9b1dc989e8ea192ae26a
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982797"
---
# <a name="microsoft-global-network"></a>Microsoft 全域網路

Microsoft 擁有並操作世界上最大的骨幹網路之一。 這種全球和精密的架構，跨越超過160000英里，連接了我們的資料中心和客戶。 
 
世界各地的客戶每天都會連線並將要求數兆個給 Microsoft Azure、Bing、Dynamics 365、Office 365、XBox 等等。 不論何種類型，客戶都希望能夠立即提供服務的可靠性和回應能力。 
 
[Microsoft 全球網路](https://azure.microsoft.com/global-infrastructure/global-network/)（WAN）是提供絕佳雲端體驗的主要部分。 將 Microsoft[資料中心](https://azure.microsoft.com/global-infrastructure/)跨54個 Azure 區域，以及策略性地放在世界各地的大型邊緣節點連接，我們的全球網路提供可用性、容量和彈性，以符合任何需求。

![Microsoft 全域網路](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>取得 premium 雲端網路
 
當您使用 Microsoft cloud 時，選擇[最適合的體驗](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/)就很簡單。 從客戶流量進入我們的全球網路，透過我們策略性放置的邊緣節點，您的資料會以接近光線速度的最佳路線來傳輸。 這可確保最佳的延遲，以達到最佳效能。 這些邊緣節點，全都透過超過165個位置的上千個連線，連接到超過3500個獨特的網際網路合作夥伴（對等），以提供互連策略的基礎。 
 
無論是從倫敦連線到東京，或從華盛頓特區連接到洛杉磯，網路效能都是量化的，而且會受到延遲、抖動、封包遺失和輸送量等專案的影響。  在 Microsoft，我們偏好並使用直接互連，而不是傳輸連結，這會使回應流量保持對稱，並協助將躍點、對等互連方和路徑保持簡短且簡單。 

例如，如果倫敦的使用者嘗試存取東京中的服務，則網際網路流量會進入倫敦的一個邊緣，透過法國、歐洲和印度之間的交易-阿拉伯路徑，再到日本託管服務的國家/地區進行 Microsoft WAN。 回應流量是對稱的。 這有時稱為「[冷刷」路由](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing)，這表示流量會盡可能在 Microsoft 網路上保留。  
  
那麼，在使用 Microsoft 服務時，這是否表示所有的流量？ 是，資料中心之間的任何流量，在 Microsoft Azure 或 Microsoft 服務（例如虛擬機器、Office 365、XBox、SQL Db、儲存體和虛擬網路）之間，會在全球網路內路由傳送，並永遠不會透過公用網際網路，以確保最佳效能和完整性。  
 
大規模投資在 metro、地面和潛水艇路徑間的光纖容量和多樣性，對於我們而言，維持一致和高服務層級，同時激發雲端和線上服務的極端成長，是非常重要的。 我們的全球網路最近新增的功能是我們的[MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea)潛水艇纜線、業界的第一個開放線系統（OLS），透過海底、Bilbao、西班牙和佛吉尼亞海灘、弗吉尼亞州、美國，以及紐約、美國與都柏林、愛爾蘭和[新的亞太地區（NCP）](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system)之間的[AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) ，美國的東京、日本及上海。 
 

## <a name="our-network-is-your-network"></a>我們的網路是您的網路

我們有兩數十年的經驗，以及大量的網路投資，確保隨時都能獲得最佳效能。 企業可以充分利用我們的網路資產，並在上面建立先進的重迭架構。 
 
Microsoft Azure 提供最豐富的服務和功能組合，讓客戶能夠快速且輕鬆地在任何地方建立、擴充及滿足網路需求。 我們的聯機服務系列會跨越區域、混合式和雲端間點對站和站對站架構之間的虛擬網路對等互連，以及全域 IP 傳輸案例。  適用于想要將自己的資料中心或網路連線到 Azure 的企業，或具有大規模資料內嵌或傳輸需求、 [expressroute](../expressroute/expressroute-introduction.md)和[expressroute Direct](../expressroute/expressroute-erdirect-about.md)的客戶，可直接在全球對等互連位置的全球網路中，提供最高 100 Gbps 頻寬的選項。  
 
[ExpressRoute Global 延伸](../expressroute/expressroute-global-reach.md)的目的是要補充您的服務提供者的 WAN 實行，並將您的內部部署網站連接到全球各地。 如果您執行全域作業，您可以搭配使用 ExpressRoute Global 與慣用和本地服務提供者，以使用 Microsoft 全球網路來連接所有全球網站。 將您的雲端（WAN）中的新網路擴充成包含大量的分支網站，可以透過 Azure 虛擬 WAN 來完成，這讓您能夠順暢地將您的分支連線到 Microsoft 全球網路，SDWAN & VPN 裝置（也就是客戶部署設備或 CPE），並內建易用性和自動化連線能力和設定管理。 
 
[全域 VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)可讓客戶順暢地跨區域連接兩個或多個 Azure 虛擬網路。 一旦對等互連，虛擬網路就會顯示為一。 對等互連虛擬網路中虛擬機器之間的流量會透過 Microsoft 骨幹基礎結構路由傳送，就像流量會在同一個虛擬網路中的虛擬機器之間路由傳送（僅限透過私人 IP 位址）。 
 

## <a name="well-managed-using-software-defined-innovation"></a>使用軟體定義的創新來妥善管理

在世界上執行其中一項領先的雲端，Microsoft 在建立和管理高效能的全球基礎結構方面，取得了許多深入解析和經驗。  
 
我們會遵守一組健全的作業原則： 
 
- 跨網路各層使用最佳的切換硬體。  
- 部署不影響使用者的新功能。  
- 盡可能以最快的速度，在整個車隊中安全且可靠地推出更新。 小時，而不是周。  
- 利用雲端規模的深度遙測和完全自動化的容錯風險降低。  
- 使用統一和軟體定義的網路技術來控制網路中的所有硬體元素。  排除重複並減少失敗。 
 
這些原則適用于所有網路層：從主機網路介面、切換平臺、資料中心內的網路功能（例如負載平衡器），一直到 WAN 與我們的流量工程平臺和我們的光纖網路。  
 
Azure 及其網路的指數成長已達到我們最後發現，人類直覺無法再依賴來管理全域網路作業的重點。 為了滿足在網路上驗證長期、中型和短期變更的需求，我們開發了一個平臺來鏡像和模擬我們的生產網路合成方式。 建立鏡像環境並執行數百萬項模擬的能力，可讓我們先測試軟體和硬體變更及其影響，再將它們認可到生產平臺和網路。 

## <a name="next-steps"></a>後續步驟
- [深入瞭解 Azure 中提供的網路服務](https://azure.microsoft.com/product-categories/networking/)
