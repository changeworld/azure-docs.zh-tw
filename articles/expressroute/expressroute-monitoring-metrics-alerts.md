---
title: Azure ExpressRoute：監視、計量和警示
description: 此頁面提供有關 ExpressRoute 監視的資訊
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75436905"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 監視、計量和警示

本文將協助您了解使用 Azure 監視器的 ExpressRoute 監視、計量和警示。 Azure 監視器可讓您一次取得所有 Azure 服務上的所有計量、警示和診斷記錄。
 
>[!NOTE]
>不建議使用**傳統計量**。
>

## <a name="expressroute-metrics"></a>ExpressRoute 計量

若要查看**計量**，請流覽至 [ *Azure 監視器*] 頁面，然後按一下 [*計量*]。 若要查看**expressroute**計量，請依資源類型的*expressroute 線路*進行篩選。 若要查看**全球**觸達計量，請依資源類型的*expressroute 線路*進行篩選，然後選取已啟用全域觸達的 ExpressRoute 線路資源。 若要查看**Expressroute Direct**計量，請依*Expressroute 埠*篩選資源類型。 

選取度量之後，將會套用預設匯總。 （選擇性）您可以套用 [分割]，這將會顯示具有不同維度的度量。

### <a name="available-metrics"></a>可用的度量
|**計量**|**類別**|**維度**|**功能**|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ui><li>對等（主要/次要 ExpressRoute 路由器）</ui></li><ui><li> 對等互連類型（私用/公用/Microsoft）</ui></li>|ExpressRoute|
|Bgp 可用性|可用性|<ui><li> 對等（主要/次要 ExpressRoute 路由器）</ui></li><ui><li> 對等互連類型</ui></li>|ExpressRoute|
|BitsInPerSecond|交通流量|<ui><li> 對等互連類型（ExpressRoute）</ui></li><ui><li>連結（ExpressRoute Direct）</ui></li>| <li> ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|交通流量| <ui><li>對等互連類型（ExpressRoute）</ui></li><ui><li> 連結（ExpressRoute Direct） | <ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|交通流量|<ui><li>對等互連線路 Skey （服務金鑰）</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|交通流量|<ui><li>對等互連線路 Skey （服務金鑰）</ui></li>|Global Reach|
|AdminState|實體連線能力|連結|ExpressRoute Direct|
|LineProtocol|實體連線能力|連結|ExpressRoute Direct|
|RxLightLevel|實體連線能力|<ui><li>連結</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|實體連線能力|<ui><li>連結</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>只有在至少建立了一個全域連線時，才會顯示使用*GlobalGlobalReachBitsInPerSecond*和*GlobalGlobalReachBitsOutPerSecond* 。
>

## <a name="circuits-metrics"></a>線路計量

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>位 In 和 Out-所有對等互連之間的計量

您可以跨給定 ExpressRoute 線路上的所有對等互連來查看計量。

![線路計量](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>位 In 和 Out-每個對等互連的計量

您可以檢視私用、公用及 Microsoft 對等互連的計量 (以位元/秒為單位)。

![每個對等互連的計量](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>BGP 可用性-依對等分割  

您可以在對等互連和對等（主要和次要 ExpressRoute 路由器）上，查看 BGP 的即時可用性。 此儀表板會顯示私人對等互連的主要 BGP 會話，以及針對私人對等互連關閉的第二個 BGP 會話。 

![每個對等的 BGP 可用性](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>ARP 可用性-依對等互連分割  

您可以在對等互連和對等（主要和次要 ExpressRoute 路由器）上查看[ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager)的即時可用性。 此儀表板會顯示在這兩個對等互連之間的私用對等互連 ARP 會話，但會在對等互連的 Microsoft 對等互連完成 在兩個對等之間使用預設匯總（平均）。  

![每個對等的 ARP 可用性](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 計量

### <a name="admin-state---split-by-link"></a>系統管理狀態-依連結分割
您可以查看每個 ExpressRoute Direct 埠配對連結的系統管理狀態。

![er 直接管理狀態](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>每秒的位-依連結分割
您可以跨兩個 ExpressRoute Direct 埠配對的連結來查看每秒的位。 

![每秒的 er 直接位數](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>每秒的位數-依連結分割
您也可以在 ExpressRoute Direct 埠配對的兩個連結中，查看每秒的位。 

![每秒的 er direct 位](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>線路通訊協定-依連結分割
您可以跨 ExpressRoute Direct 埠配對的每個連結來查看線路通訊協定。

![er direct line 通訊協定](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx Light 層級-依連結分割
您可以針對每個埠，查看 Rx 光線層級（ExpressRoute Direct 埠**接收**的光線層級）。 狀況良好的 Rx 光線層級通常落在-10 到 0 dBm 的範圍內

![er direct line Rx Light 層級](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Tx 光線層級-依連結分割
您可以針對每個埠，查看 Tx 光源層級（ExpressRoute Direct 埠正在**傳輸**的光線層級）。 狀況良好的 Tx 光線層級通常落在-10 到 0 dBm 的範圍內

![er direct line Rx Light 層級](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

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
