---
title: 查看虛擬中心的有效路由：Azure 虛擬 WAN |微軟文檔
description: 在 Azure 虛擬 WAN 中為虛擬中心提供有效路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: cherylmc
ms.openlocfilehash: 1173da81736661048d1e4e12d9919bc2aadf73ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515846"
---
# <a name="view-effective-routes-of-a-virtual-hub"></a>查看虛擬中心的有效路由

您可以在 Azure 門戶中查看虛擬 WAN 中心的所有路由。 要查看路由，請導航到虛擬中心，然後選擇路由 **->查看有效路由**。

## <a name="understanding-routes"></a><a name="understand"></a>瞭解路線

以下示例可説明您更好地瞭解虛擬 WAN 路由的顯示方式。

在此示例中，我們有一個具有三個集線器的虛擬 WAN。 第一個樞紐位於美國東部地區，第二個樞紐位於西歐地區，第三個樞紐位於美國西部區域。 在虛擬廣域網路中，所有集線器都是互連的。 在此示例中，我們將假定東美國和西歐中心具有來自本地分支（分支）和 Azure 虛擬網路（分支）的連接。

具有網路虛擬裝置 （10.4.0.6） 的 Azure VNet 分支 （10.4.0.0/16） 進一步與 VNet （10.5.0.0/16） 對等。 有關中心路由表的詳細資訊，請參閱本文後面的[其他資訊](#abouthubroute)。

在此示例中，我們還假定西歐分支 1 連接到美國東部中心以及西歐樞紐。 美國東部的 ExpressRoute 電路將第 2 分支連接到美國東部樞紐。

![圖表](./media/effective-routes-virtual-hub/diagram.png)

## <a name="view-effective-routes"></a><a name="view"></a>查看有效路線

當您在門戶中選擇"查看有效路由"時，它將生成美國東部[中心集線表中](#routetable)顯示的輸出。

綜上所述，第一行意味著美國東部中心由於 VPN*下一躍點類型*連接（"下一躍點"VPN 閘道實例0 IP 10.1.0.6、實例1 IP 10.1.0.7）而瞭解了 10.20.1.0/24 （分支 1） 的路由。 *路由原點*指向資源識別碼。 *AS 路徑*指示分支 1 的 AS 路徑。

### <a name="hub-route-table"></a><a name="routetable"></a>中心路由表

使用表底部的捲軸查看"AS 路徑"。

| **首碼** |  **下一個躍點類型** | **下一跳** |  **路線原點** |**作為路徑** |
| ---        | ---                | ---          | ---               | ---         |
| 10.20.1.0/24|VPN |10.1.0.6, 10.1.0.7| /訂閱/`<sub>`資源組//`<rg>`供應商/微軟.網路/vpn閘道/343a19aa6ac74e4d81f05ccccf1536cf-Eastus-gw| 20000|
|10.21.1.0/24 |ExpressRoute|10.1.0.10, 10.1.0.11|/訂閱/`<sub>`資源組//`<rg>`供應商/微軟.網路/快速路由閘道/4444a6ac74e4d85555-eastus-gw|21000|
|10.23.1.0/24| VPN |10.1.0.6, 10.1.0.7|/訂閱/`<sub>`資源組//`<rg>`供應商/微軟.網路/vpn閘道/343a19aa6ac74e4d81f05ccccf1536cf-Eastus-gw|23000|
|10.4.0.0/16|虛擬網路連接| 鏈路上 |  |  |
|10.5.0.0/16| IP 位址| 10.4.0.6|/訂閱/`<sub>`資源組//`<rg>`供應商/微軟.網路/虛擬中心/easthub_1/路由表/table_1| |
|0.0.0.0/0| IP 位址| `<Azure Firewall IP>` |/訂閱/`<sub>`資源組//`<rg>`供應商/微軟.網路/虛擬中心/easthub_1/路由表/table_1| |
|10.22.1.0/16| 遠端中心|10.8.0.6, 10.8.0.7|/訂閱/`<sub>`資源組//`<rg>`供應商/微軟.網路/虛擬中心/westhub_| 4848-22000 |
|10.9.0.0/16| 遠端中心|  鏈路上 |/訂閱/`<sub>`資源組//`<rg>`供應商/微軟.網路/虛擬中心/westhub_1| |

>[!NOTE]
> 如果美國東部和西歐中心在示例拓撲中互不通信，則所學的路線 （10.9.0.0/16） 將不存在。 中心僅通告直接連接到它們的網路。
>

## <a name="additional-information"></a><a name="additional"></a>其他資訊

### <a name="about-the-hub-route-table"></a><a name="abouthubroute"></a>關於中心路由表

您可以建立虛擬中樞路由並將路由套用到虛擬中樞路由表。 您可以將多個路由套用到虛擬中樞路由表。 這允許您通過 IP 位址（通常是分支 VNet 中的網路虛擬裝置 （NVA））設置目標 VNet 的路由。 有關 NVA 的詳細資訊，請參閱[將流量從虛擬集線器路由到 NVA](virtual-wan-route-table-portal.md)。

### <a name="about-default-route-00000"></a><a name="aboutdefaultroute"></a>關於預設路由 （0.0.0.0/0）

如果連接上的標誌為"已啟用"，則虛擬中心能夠將學習的預設路由傳播到虛擬網路、網站到網站 VPN 和 ExpressRoute 連接。 編輯虛擬網路連接、VPN 連接或 ExpressRoute 連接時，此標誌可見。 預設情況下，在集線器 VNet、ExpressRoute 和 VPN 連接上，"啟用 Internet 安全"始終為 false。

預設路由不源自虛擬廣域網路中心。 如果虛擬 WAN 中心由於在集線器中部署防火牆而已瞭解預設路由，或者另一個連接網站已啟用強制隧道，則將傳播該路由。

## <a name="next-steps"></a>後續步驟

有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概述](virtual-wan-about.md)。