---
title: Azure ExpressRoute：使用全球接觸連線到 Microsoft Cloud
description: 瞭解 Azure ExpressRoute 全球接觸如何將 ExpressRoute 線路連結在一起，以在您的內部部署網路之間建立私人網路。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: duau
ms.openlocfilehash: 8c6ed19a585bc1ebae65045cd1cc4c442f113597
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651494"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute 是將內部部署網路連線到 Microsoft 雲端的私用和具彈性的方式。 您可以從您的私人資料中心或公司網路存取許多 Microsoft 雲端服務（例如 Azure 和 Microsoft 365）。 例如，您在舊金山分公司的 ExpressRoute 線路可能在美國矽谷，而另一家倫敦分公司的 ExpressRoute 線路則位於同一座城市。 這兩個分公司都具有對美國西部和英國南部的 Azure 資源的高速連線能力。 不過，分公司無法彼此直接連接及傳送資料。 換句話說，10.0.1.0/24 可以將資料傳送至 10.0.3.0/24 和 10.0.4.0/24 網路，但不能傳送至 10.0.2.0/24 網路。

![顯示未與「Express Route 全球接觸」連結的電路的圖表。][1]

透過 **ExpressRoute Global Reach**，您可以將 ExpressRoute 線路連結在一起，以在內部部署網路之間產生私人網路。 在上述範例中，透過新增 ExpressRoute Global Reach，舊金山分公司 (10.0.1.0/24) 就能透過現有的 ExpressRoute 線路及透過 Microsoft 的全球網路，直接與倫敦分公司 (10.0.2.0/24) 交換資料。 

![顯示與 Express Route 全球接觸的電路連結的圖表。][2]

## <a name="use-case"></a>使用案例
ExpressRoute Global Reach 是設計來補充您服務提供者的 WAN 實作，以及連線您位於世界各地的分公司。 例如，如果您服務提供者的營運範圍主要是在美國境內，並且已連結您在美國的所有分公司，但該服務提供者並未在日本和香港特別行政區中營運，透過 ExpressRoute Global Reach，您就能與當地服務提供者合作，而 Microsoft 會使用 ExpressRoute 和全球網路，將您位於該處的分公司連線到美國的分公司。

![顯示 Express Route 全球接觸使用案例的圖表。][3]

## <a name="availability"></a>可用性 
目前支援 ExpressRoute 的大部分區域都支援 ExpressRoute Global 觸及。 您可以參考目前支援區域的 [ExpressRoute 連線提供者](expressroute-locations-providers.md#partners) 。 

> [!NOTE] 
> 若要在 [不同的地緣政治區域](expressroute-locations-providers.md#locations)之間啟用 ExpressRoute 全球存取範圍，您的線路必須是 **Premium SKU**。

## <a name="next-steps"></a>接下來的步驟
- 觀看 [全球接觸的常見問題](expressroute-faqs.md#globalreach)。
- 瞭解如何 [實現全球接觸](expressroute-howto-set-global-reach.md)。
- 瞭解如何將 [ExpressRoute 線路連結至您的虛擬網路](expressroute-howto-linkvnet-arm.md)。

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "不含觸角擴及全球的圖表"
[2]: ./media/expressroute-global-reach/2.png "包含觸角擴及全球的圖表"
[3]: ./media/expressroute-global-reach/3.png "Global Reach 的使用案例"
