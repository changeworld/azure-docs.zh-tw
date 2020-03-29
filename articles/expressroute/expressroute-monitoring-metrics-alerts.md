---
title: Azure 快速路由：監視、指標和警報
description: 此頁面提供有關 ExpressRoute 監視的資訊
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436905"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 監視、計量和警示

本文將協助您了解使用 Azure 監視器的 ExpressRoute 監視、計量和警示。 Azure 監視器可讓您一次取得所有 Azure 服務上的所有計量、警示和診斷記錄。
 
>[!NOTE]
>不建議使用**傳統計量**。
>

## <a name="expressroute-metrics"></a>快速路由指標

要查看**指標**，請導航到*Azure 監視器*頁面，然後按一下 *"指標*"。 要查看**快速路由**指標，按資源類型*快速路由電路進行*篩選。 要查看**全域覆蓋**指標，請按資源類型*ExpressRoute 電路*進行篩選，並選擇啟用全域覆蓋的 ExpressRoute 電路資源。 要查看**快速路由直接**指標，按*快速路由埠*篩選資源類型。 

選擇指標後，將應用預設聚合。 或者，您可以應用拆分，這將顯示具有不同維度的指標。

### <a name="available-metrics"></a>可用的度量
|**度量**|**類別目錄**|**尺寸**|**功能**|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ui><li>對等機（主/輔助快速路由路由器）</ui></li><ui><li> 對等類型（私有/公共/微軟）</ui></li>|ExpressRoute|
|Bgp 可用性|可用性|<ui><li> 對等機（主/輔助快速路由路由器）</ui></li><ui><li> 對等類型</ui></li>|ExpressRoute|
|BitsInPerSecond|交通流量|<ui><li> 對等類型（快速路由）</ui></li><ui><li>連結（快速路由直接）</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|交通流量| <ui><li>對等類型（快速路由）</ui></li><ui><li> 連結（快速路由直接） | <ui><li>快速路線<ui><li>ExpressRoute Direct</ui></li> |
|全域覆蓋位秒|交通流量|<ui><li>對等電路金鑰（維修金鑰）</ui></li>|Global Reach|
|全域覆蓋位出每秒|交通流量|<ui><li>對等電路金鑰（維修金鑰）</ui></li>|Global Reach|
|管理狀態|物理連接|連結|ExpressRoute Direct|
|線協定|物理連接|連結|ExpressRoute Direct|
|RxLight 級別|物理連接|<ui><li>連結</ui></li><ui><li>車道</ui></li>|ExpressRoute Direct|
|TxLight 級別|物理連接|<ui><li>連結</ui></li><ui><li>車道</ui></li>|ExpressRoute Direct|
>[!NOTE]
>僅當建立至少一個全域覆蓋連接時，才能使用*全域覆蓋點InPer秒*和全球*全球覆蓋點出點數才會*可見。
>

## <a name="circuits-metrics"></a>電路指標

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>位到和出 - 跨所有對等互連的指標

您可以在給定的 ExpressRoute 電路上查看所有對等互連的指標。

![線路計量](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>位到和出 - 每個對等的指標

您可以檢視私用、公用及 Microsoft 對等互連的計量 (以位元/秒為單位)。

![每個對等互連的計量](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP 可用性 - 按對等體拆分  

您可以通過對等互連和對等體（主和輔助快速路由路由器）查看 BGP 的近即時可用性。 此儀表板顯示用於私有對等互連的主 BGP 會話，向下顯示用於私有對等互連的第二個 BGP 會話。 

![每個對等體的 BGP 可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP 可用性 - 按對等拆分  

您可以在對等互連和對等體（主和輔助快速路由路由器）之間查看接近即時[ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager)的可用性。 此儀表板顯示跨兩個對等體的專用對等互連 ARP 會話，但為跨對等互連的 Microsoft 對等互連完成。 預設聚合（平均值）在兩個對等體之間使用。  

![每個對等體的 ARP 可用性](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>快速路由直接指標

### <a name="admin-state---split-by-link"></a>管理狀態 - 按連結拆分
您可以查看 ExpressRoute Direct 埠對的每個連結的管理員狀態。

![呃 直接管理狀態](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>位以每秒 - 按連結拆分
您可以在 ExpressRoute Direct 埠對的兩個鏈路上每秒查看位。 

![每秒直接位](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>每秒出位 - 按連結拆分
您還可以在 ExpressRoute Direct 埠對的兩個鏈路上每秒查看位。 

![呃 每秒直接出位](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>行協定 - 按連結拆分
您可以在 ExpressRoute Direct 埠對的每個鏈路上查看線路協定。

![呃直接線路協定](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx 光級別 - 按連結拆分
您可以查看每個埠的 Rx 光級別（ExpressRoute Direct 埠**接收**的光電平）。 健康的 Rx 光水準通常在 -10 到 0 dBm 的範圍內

![呃 直線 Rx 光級別](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx 光級 - 按連結拆分
您可以查看每個埠的 Tx 光級別（ExpressRoute Direct 埠**傳輸**的光電平）。 健康的 Tx 光水準通常在 -10 到 0 dBm 的範圍內

![呃 直線 Rx 光級別](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 閘道連線 (以位元/秒為單位)

![閘道連線](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 閘道連線的警示

1. 若要設定警示，請瀏覽至 [Azure 監視器]****，然後按一下 [警示]****。

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. 按一下 [+ 選取目標]****，然後選取 ExpressRoute 閘道連線資源。

   ![目標]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. 定義警示詳細資料。

   ![動作群組](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. 定義和新增動作群組。

   ![新增動作群組](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>以每個對等互連為基礎的警示

 ![什麼](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>在線路上設定活動記錄的警示

在 [警示準則]**** 中，您可以選取 [活動記錄]**** 作為訊號類型，並選取訊號。

  ![另一個](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>後續步驟

設定 ExpressRoute 連線。
  
  * [建立及修改電路](expressroute-howto-circuit-arm.md)
  * [建立和修改對等互連組態](expressroute-howto-routing-arm.md)
  * [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)
