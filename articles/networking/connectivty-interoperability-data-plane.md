---
title: Azure 後端連線功能的互通性：資料平面分析 | Microsoft Docs
description: 本文提供測試設定的資料平面分析，您可使用該設定分析 Azure 中 ExpressRoute、站對站 VPN 及虛擬網路對等互連之間的互通性。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 815976c672272270e465610e17fef3aea79387f6
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526632"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Azure 後端連線功能的互通性：資料平面分析

本文說明[測試設定][Setup]的資料平面分析。 您也可以查看測試設定的[測試設定][Configuration]和[控制平面分析][Control-Analysis]。

資料平面分析會檢查從一個本機網路 (LAN 或虛擬網路) 周遊至拓撲中其他網路的封包所走的路徑。 兩個本機網路之間的資料路徑不一定要對稱。 因此，我們在本文中分析從本機網路至其他網路的轉送路徑與反向路徑是分離的案例。

## <a name="data-path-from-the-hub-vnet"></a>從中樞 VNet 來的資料路徑

### <a name="path-to-the-spoke-vnet"></a>往輪幅 VNet 的路徑

虛擬網路 (VNet) 對等互連可模擬兩個對等 VNet 之間的網路橋接器功能。 以下是從中樞 VNet 輸出至輪輻 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

下圖顯示 Azure 網路監看員視角下的中樞 VNet 和輪幅 VNet 的圖形連線檢視：


![1][1]

### <a name="path-to-the-branch-vnet"></a>往分支 VNet 的路徑

以下是從中樞 VNet 輸出至分支 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

在此追蹤路由，第一個躍點是中樞 VNet 的 Azure VPN 閘道中的 VPN 閘道。 第二個躍點是分支 VNet 的 VPN 閘道。 分支 VNet 的 VPN 閘道的 IP 位址沒有在中樞 VNet 中公告。 第三個躍點是分支 VNet 上的 VM。

下圖顯示網路監看員視角下的中樞 VNet 和分支 VNet 的圖形連線檢視：

![2][2]

下圖顯示同一個連線在網路監看員中的格線檢視：

![3][3]

### <a name="path-to-on-premises-location-1"></a>往內部部署位置 1 的路徑

以下是從中樞 VNet 輸出至內部部署位置 1 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

在此追蹤路由中，第一個躍點是對 Microsoft Enterprise Edge Router (MSEE) 的 Azure ExpressRoute 閘道通道端點。 第二個和第三個躍點是，客戶 Edge (CE) 路由器和內部部署位置 1 LAN IP。 這些 IP 位址沒有在中樞 VNet 中公告。 第四個躍點是內部部署位置 1 中的 VM。


### <a name="path-to-on-premises-location-2"></a>往內部部署位置 2 的路徑

以下是從中樞 VNet 輸出至內部部署位置 2 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

在此追蹤路由中，第一個躍點是對 MSEE 的 ExpressRoute 閘道通道端點。 第二個和第三個躍點是 CE 路由器和內部部署位置 2 LAN IP。 這些 IP 位址沒有在中樞 VNet 中公告。 第四個躍點是內部部署位置 2 上的 VM。

### <a name="path-to-the-remote-vnet"></a>往遠端 VNet 的路徑

以下是從中樞 VNet 輸出至遠端 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

在此追蹤路由中，第一個躍點是對 MSEE 的 ExpressRoute 閘道通道端點。 第二個躍點是遠端 VNet 的閘道 IP。 第二個躍點 IP 範圍沒有在中樞 VNet 中公告。 第三個躍點是遠端 VNet 上的 VM。

## <a name="data-path-from-the-spoke-vnet"></a>從輪幅 VNet 來的資料路徑

輪幅 VNet 的網路檢視和中樞 VNet 的一樣。 輪輻 VNet 可以透過 VNet 對等互連來使用中樞 VNet 的遠端閘道連線，就像直接連線到輪輻 VNet。

### <a name="path-to-the-hub-vnet"></a>往中樞 VNet 的路徑

以下是從輪幅 VNet 輸出至中樞 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>往分支 VNet 的路徑

以下是從輪幅 VNet 輸出至分支 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

在此追蹤路由中，第一個躍點是中樞 VNet 的 VPN 閘道。 第二個躍點是分支 VNet 的 VPN 閘道。 分支 VNet 的 VPN 閘道的 IP 位址沒有在中樞/輪幅 VNet 中公告。 第三個躍點是分支 VNet 上的 VM。

### <a name="path-to-on-premises-location-1"></a>往內部部署位置 1 的路徑

以下是從輪幅 VNet 輸出至內部部署位置 1 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

在此追蹤路由中，第一個躍點是中樞 VNet 對 MSEE 的 ExpressRoute 閘道通道端點。 第二個和第三個躍點是 CE 路由器和內部部署位置 1 LAN IP。 這些 IP 位址沒有在中樞/輪幅 VNet 中公告。 第四個躍點是內部部署位置 1 中的 VM。

### <a name="path-to-on-premises-location-2"></a>往內部部署位置 2 的路徑

以下是從輪幅 VNet 輸出至內部部署位置 2 中 VM 的追蹤路由：


    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    76 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

在此追蹤路由中，第一個躍點是中樞 VNet 對 MSEE 的 ExpressRoute 閘道通道端點。 第二個和第三個躍點是 CE 路由器和內部部署位置 2 LAN IP。 這些 IP 位址沒有在中樞/輪幅 VNet 中公告。 第四個躍點是內部部署位置 2 中的 VM。

### <a name="path-to-the-remote-vnet"></a>往遠端 VNet 的路徑

以下是從輪幅 VNet 輸出至遠端 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

在此追蹤路由中，第一個躍點是中樞 VNet 對 MSEE 的 ExpressRoute 閘道通道端點。 第二個躍點是遠端 VNet 的閘道 IP。 第二個躍點 IP 範圍沒有在中樞/輪幅 VNet 中公告。 第三個躍點是遠端 VNet 上的 VM。

## <a name="data-path-from-the-branch-vnet"></a>從分支 VNet 來的資料路徑

### <a name="path-to-the-hub-vnet"></a>往中樞 VNet 的路徑

以下是從分支 VNet 輸出至中樞 VNet 中 VM 的追蹤路由：

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

在此追蹤路由中，第一個躍點是分支 VNet 的VPN 閘道。 第二個躍點是中樞 VNet 的 VPN 閘道。 中樞 VNet 的 VPN 閘道的 IP 位址沒有在遠端 VNet 中公告。 第三個躍點是中樞 VNet 上的 VM。

### <a name="path-to-the-spoke-vnet"></a>往輪幅 VNet 的路徑

以下是從分支 VNet 輸出至輪幅 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

在此追蹤路由中，第一個躍點是分支 VNet 的VPN 閘道。 第二個躍點是中樞 VNet 的 VPN 閘道。 中樞 VNet 的 VPN 閘道的 IP 位址沒有在遠端 VNet 中公告。 第三個躍點是在輪輻 VNet 上的 VM。

### <a name="path-to-on-premises-location-1"></a>往內部部署位置 1 的路徑

以下是從分支 VNet 輸出至內部部署位置 1 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

在此追蹤路由中，第一個躍點是分支 VNet 的VPN 閘道。 第二個躍點是中樞 VNet 的 VPN 閘道。 中樞 VNet 的 VPN 閘道的 IP 位址沒有在遠端 VNet 中公告。 第三個躍點是主要 CE 路由器的 VPN 通道終止點。 第四個躍點是內部部署位置 1 的內部 IP 位址。 此 LAN IP 位址沒有在 CE 路由器外公告。 第五個躍點是內部部署位置 1 中的目的地 VM。

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>往內部部署位置 2 和遠端 VNet 的路徑

如我們在控制平面分析中所討論，由於網路設定的緣故，內部部署位置 2 或遠端 VNet 對於分支 VNet 都是不可見的。 下列 Ping 結果可證實： 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>從內部部署位置 1 來的資料路徑

### <a name="path-to-the-hub-vnet"></a>往中樞 VNet 的路徑

以下是從內部部署位置 1 輸出至中樞 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

在此追蹤路由中，前兩個躍點屬於內部部署網路。 第三個躍點是面對 CE 路由器的主要 MSEE 介面。 第四個躍點是中樞 VNet 的 ExpressRoute 閘道。 中樞 VNet 的 ExpressRoute 閘道的 IP 範圍沒有對內部部署網路公告。 第五個躍點是目的地 VM。

網路監看員僅提供以 Azure 為中心的檢視。 針對內部部署視角，我們使用 Azure 網路效能監控。 網路效能監控提供用於資料路徑分析的代理程式，您可以在 Azure 外網路中的伺服器上安裝。

下圖顯示內部部署位置 1 VM 透過 ExpressRoute 連線至中樞 VNet 上的 VM 之拓撲檢視：

![4][4]

如稍早所述，測試安裝程式使用對站 VPN 作為內部部署位置 1 和中樞 VNet 之間 ExpressRoute 的備份連線。 為了測試備份資料路徑，讓我們引發內部部署位置 1 主要 CE 路由器和相對應 MSEE 之間的 ExpressRoute 連結失敗。 為了引發 ExpressRoute 連結失敗，請關閉面對 MSEE 的 CE 介面：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

下圖顯示當 ExpressRoute 連線中斷時，內部部署位置 1 VM 透過站對站 VPN 連線至中樞 VNet 上的 VM 之拓撲檢視：

![5][5]

### <a name="path-to-the-spoke-vnet"></a>往輪幅 VNet 的路徑

以下是從內部部署位置 1 輸出至輪幅 VNet 中 VM 的追蹤路由：

讓我們重新建立 ExpressRoute 主要連線，以執行朝向輪輻 VNet 的資料路徑分析：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

重新建立主要 ExpressRoute 1 連線，以進行其餘的資料路徑分析。

### <a name="path-to-the-branch-vnet"></a>往分支 VNet 的路徑

以下是從內部部署位置 1 輸出至分支 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>往內部部署位置 2 的路徑

如我們在[控制平面分析][Control-Analysis]中所討論，內部部署位置1對於每個網路設定都沒有內部部署位置2的可見度。 下列 Ping 結果可證實： 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>往遠端 VNet 的路徑

以下是從內部部署位置 1 輸出至遠端 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>從內部部署位置 2 來的資料路徑

### <a name="path-to-the-hub-vnet"></a>往中樞 VNet 的路徑

以下是從內部部署位置 2 輸出至中樞 VNet 中 VM 的追蹤路由：

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>往輪幅 VNet 的路徑

以下是從內部部署位置 2 輸出至輪幅 VNet 中 VM 的追蹤路由：

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>往分支 VNet、內部部署位置 1 和遠端 VNet 的路徑

如我們在[控制平面分析][Control-Analysis]中所討論，內部部署位置1無法看到分支 VNet、內部部署位置1，或每個網路設定的遠端 VNet。 

## <a name="data-path-from-the-remote-vnet"></a>從遠端 VNet 來的資料路徑

### <a name="path-to-the-hub-vnet"></a>往中樞 VNet 的路徑

以下是從遠端 VNet 輸出至中樞 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>往輪幅 VNet 的路徑

以下是從遠端 VNet 輸出至輪幅 VNet 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>往分支 VNet 和內部部署位置 2 的路徑

如我們在[控制平面分析][Control-Analysis]中所討論，遠端 VNet 對於每個網路設定都沒有分支 VNet 或內部部署位置2的可見度。 

### <a name="path-to-on-premises-location-1"></a>往內部部署位置 1 的路徑

以下是從遠端 VNet 輸出至內部部署位置 1 中 VM 的追蹤路由：

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute 和站對站 VPN 連線串聯

###  <a name="site-to-site-vpn-over-expressroute"></a>透過 ExpressRoute 設定站對站 VPN

您可透過 ExpressRoute Microsoft 對等互連設定站對站 VPN，私下交換您內部部署網路和 Azure VNet 之間的資料。 使用此組態時，您可以交換具有機密性、真實性和完整性的資料。 資料交換也會禁止重新播放。 如需如何使用 ExpressRoute Microsoft 對等互連，在通道模式中設定站對站 IPsec VPN 的詳細資訊，請參閱透過[Expressroute microsoft 對等互連的站對站 vpn][S2S-Over-ExR]。 

設定使用 Microsoft 對等互連的站對站 VPN 的主要限制是輸送量。 IPSec 通道的輸送量受限於 VPN 閘道容量。 VPN 閘道輸送量低於 ExpressRoute 輸送量。 在此情況下，針對高安全流量使用 IPSec 通道，並針對所有其他流量使用私人對等互連，可協助將 ExpressRoute 頻寬使用量最佳化。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>站對站 VPN 作為 ExpressRoute 的安全容錯移轉路徑

ExpressRoute 作為備援線路組可確保高可用性。 您可以在不同的 Azure 區域中設定異地備援 ExpressRoute 連線。 如果您想在 Azure 區域中為 ExpressRoute 連線建立容錯移轉路徑，可以透過與測試設定中示範的相同方法使用站對站 VPN。 當 ExpressRoute 和站對站 VPN 顯示相同的首碼時，Azure 將優先選擇 ExpressRoute。 為了避免 ExpressRoute 和站對站 VPN 擁有不對稱的路由，內部部署網路組態也應該在使用站對站 VPN 連線之前使用 ExpressRoute 連線進行回應。

如需有關如何為 ExpressRoute 和站對站 VPN 設定並存連線的詳細資訊，請參閱[expressroute 和站對站共存][ExR-S2S-CoEx]。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>將後端連線擴充至輪輻 VNet 和分支位置

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>使用 VNet 對等互連的輪輻 VNet 連線

中樞和輪輻 VNet 架構為普遍採用的選項。 中樞是 Azure 中的 VNet，可在您的輪輻 VNet 和內部部署網站之間作為連線中心點。 輪輻是與中樞對等互連的 VNet，可用於隔離工作負載。 流量會透過 ExpressRoute 或 VPN 連線，在內部部署資料中心和中樞之間流動。 如需架構的詳細資訊，請參閱[在 Azure 中執行中樞輪輻網路拓撲][Hub-n-Spoke]。

區域中的 VNet 對等互連可讓輪輻 VNet 使用中樞 VNet 閘道 (VPN 和 ExpressRoute 閘道) 與遠端網路通訊。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>使用站對站 VPN 的分支 VNet 連線

您可能希望分支 VNet (位於不同區域) 和內部部署網路透過中樞 VNet 與彼此通訊。 此設定的原生 Azure 解決方案是使用 VPN 的站對站 VPN 連線。 替代方式是使用網路虛擬設備 (NVA) 在中樞中進行路由。

如需詳細資訊，請參閱[什麼是 VPN 閘道？][VPN]和[部署高可用性 NVA][Deploy-NVA]。


## <a name="next-steps"></a>後續步驟

請參閱[EXPRESSROUTE 常見問題][ExR-FAQ]：
-   了解可以連線到 ExpressRoute 閘道的 ExpressRoute 線路數量。
-   了解可以連接到 ExpressRoute 線路的 ExpressRoute 閘道數量。
-   了解 ExpressRoute 的其他規模限制。


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "從中樞 VNet 到輪輻 VNet 的連線網路監看員觀點"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "從中樞 VNet 到分支 VNet 的連線網路監看員觀點"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "從中樞 VNet 到分支 VNet 的連線網路監看員方格視圖"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "透過 ExpressRoute 1 從位置 1 VM 至中樞 VNet 的連線網路效能監控觀點"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "透過站對站 VPN 從位置 1 VM 至中樞 VNet 的連線網路效能監控觀點"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


