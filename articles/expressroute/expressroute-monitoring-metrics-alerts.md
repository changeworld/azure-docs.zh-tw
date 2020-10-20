---
title: Azure ExpressRoute：監視、計量和警示
description: 深入瞭解使用 Azure 監視器的 Azure ExpressRoute 監視、計量和警示，這會在 Azure 中停止所有計量、警示、診斷記錄。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: d92b5685722b8a37de3945caa1305a76b3cabb8a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206232"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>ExpressRoute 監視、計量和警示

本文將協助您了解使用 Azure 監視器的 ExpressRoute 監視、計量和警示。 Azure 監視器可讓您一次取得所有 Azure 服務上的所有計量、警示和診斷記錄。
 
>[!NOTE]
>不建議使用**傳統計量**。
>

## <a name="expressroute-metrics"></a>ExpressRoute 計量

若要查看 **度量**，請流覽至 [ *Azure 監視器* ] 頁面，然後按一下 [ *計量*]。 若要查看 **expressroute** 計量，請依資源類型的 *expressroute 線路*進行篩選。 若要查看 **全球** 觸達計量，請依資源類型的 *expressroute 線路* 進行篩選，然後選取啟用全球存取範圍的 expressroute 線路資源。 若要查看 **Expressroute Direct** 計量，請依 *Expressroute 埠*篩選資源類型。 

一旦選取度量，將會套用預設匯總。 （選擇性）您可以套用分割，這會顯示具有不同維度的度量。

### <a name="available-metrics"></a>可用的度量

|**計量**|**類別**|**維度 (s) **|**功能 (s) **|
| --- | --- | --- | --- |
|ARP 可用性|可用性|<ui><li>對等 (主要/次要 ExpressRoute 路由器) </ui></li><ui><li> 對等互連類型 (私用/公用/Microsoft) </ui></li>|ExpressRoute|
|Bgp 可用性|可用性|<ui><li> 對等 (主要/次要 ExpressRoute 路由器) </ui></li><ui><li> 對等互連類型</ui></li>|ExpressRoute|
|BitsInPerSecond|交通流量|<ui><li> ExpressRoute)  (對等互連類型 </ui></li><ui><li> (ExpressRoute Direct) 連結 </ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct|
|BitsOutPerSecond|交通流量| <ui><li>ExpressRoute)  (對等互連類型 </ui></li><ui><li>  (ExpressRoute Direct) 連結 |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li> |
|CPU 使用率|效能| <ui><li>執行個體</ui></li>|ExpressRoute 虛擬網路閘道|
|每秒封包數|效能| <ui><li>執行個體</ui></li>|ExpressRoute 虛擬網路閘道|
|GlobalReachBitsInPerSecond|交通流量|<ui><li>對等互連線路 Skey (服務金鑰) </ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|交通流量|<ui><li>對等互連線路 Skey (服務金鑰) </ui></li>|Global Reach|
|AdminState|實體連線能力|連結|ExpressRoute Direct|
|LineProtocol|實體連線能力|連結|ExpressRoute Direct|
|RxLightLevel|實體連線能力|<ui><li>連結</ui></li><ui><li>車道</ui></li>|ExpressRoute Direct|
|TxLightLevel|實體連線能力|<ui><li>連結</ui></li><ui><li>車道</ui></li>|ExpressRoute Direct|
>[!NOTE]
>只有在建立至少一個全球觸達連接時，才會顯示使用 *GlobalGlobalReachBitsInPerSecond* 和 *GlobalGlobalReachBitsOutPerSecond* 。
>

## <a name="circuits-metrics"></a>電路計量

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>跨所有對等互連的位 In 和 Out 計量

您可以在特定 ExpressRoute 線路的所有對等互連上查看計量。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="電路計量":::

### <a name="bits-in-and-out---metrics-per-peering"></a>每個對等互連的位和輸出計量

您可以檢視私用、公用及 Microsoft 對等互連的計量 (以位元/秒為單位)。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="電路計量":::

### <a name="bgp-availability---split-by-peer"></a>BGP 可用性-依對等分割  

您可以跨對等互連和對等互連 (主要和次要 ExpressRoute 路由器) ，以近乎即時地查看 BGP 的可用性。 此儀表板會顯示私人對等互連的主要 BGP 會話，以及私人對等互連的第二個 BGP 會話。 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="電路計量":::

### <a name="arp-availability---split-by-peering"></a>ARP 可用性-依對等互連分割  

您可以在對等互連和對等 (主要和次要 ExpressRoute 路由器) ，全面觀看 [ARP](./expressroute-troubleshooting-arp-resource-manager.md) 的即時可用性。 此儀表板會顯示在這兩個對等之間的私用對等 ARP 會話，但會在對等互連之間完成 Microsoft 對等互連。 預設匯總 (平均) 在兩個對等之間使用。  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="電路計量":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct 計量

### <a name="admin-state---split-by-link"></a>管理狀態-依連結分割

您可以查看 ExpressRoute Direct 埠配對的每個連結的管理狀態。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="電路計量":::

### <a name="bits-in-per-second---split-by-link"></a>每秒的位數-依連結分割

您可以在 ExpressRoute Direct 埠配對的兩個連結中，每秒查看位。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="電路計量":::

### <a name="bits-out-per-second---split-by-link"></a>每秒的位數-依連結分割

您也可以在 ExpressRoute Direct 埠配對的兩個連結中，每秒查看位。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="電路計量":::

### <a name="line-protocol---split-by-link"></a>Line Protocol-依連結分割

您可以跨 ExpressRoute Direct 埠配對的每個連結來查看線路通訊協定。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="電路計量":::

### <a name="rx-light-level---split-by-link"></a>Rx 燈光層級-依連結分割

您可以針對每個 **埠) 的** ExpressRoute Direct 埠 (的燈光，查看每個埠的 Rx 光等級。 狀況良好的 Rx 燈通常落在-10 到 0 dBm 的範圍內

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="電路計量":::

### <a name="tx-light-level---split-by-link"></a>Tx Light-依連結分割

您可以在 ExpressRoute Direct 埠 (的光線層級中，查看每個埠 **) 的** Tx 燈。 狀況良好的 Tx 淺色通常落在-10 到 0 dBm 的範圍內

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="電路計量":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute 虛擬網路閘道計量

### <a name="cpu-utilization---split-instance"></a>CPU 使用率-分割實例

您可以查看閘道實例的 CPU 使用率。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="電路計量":::

### <a name="packets-per-second---split-by-instance"></a>每秒封包數-依實例分割

您可以每秒查看閘道的每秒封包。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="電路計量":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute 閘道連線 (以位元/秒為單位)

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="電路計量":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute 閘道連線的警示

1. 若要設定警示，請流覽至 **Azure 監視器**，然後選取 [ **警示**]。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="電路計量":::
2. 按一下 [+ 選取目標]****，然後選取 ExpressRoute 閘道連線資源。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="電路計量":::
3. 定義警示詳細資料。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="電路計量":::
4. 定義和新增動作群組。

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="電路計量":::

## <a name="alerts-based-on-each-peering"></a>以每個對等互連為基礎的警示

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="電路計量":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>在線路上設定活動記錄的警示

在 [警示準則]**** 中，您可以選取 [活動記錄]**** 作為訊號類型，並選取訊號。

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="電路計量":::

## <a name="additional-metrics-in-log-analytics"></a>Log Analytics 中的其他計量

您也可以流覽至 ExpressRoute 線路資源，然後選取 [ *記錄* ] 索引標籤，以查看 expressroute 計量。針對您所查詢的任何計量，輸出會包含下列資料行。

|**資料行**|**型別**|**說明**|
| --- | --- | --- |
|TimeGrain|字串|每分鐘都會推送 PT1M (度量值) |
|計數|real|通常等於 2 (每個 MSEE 會每分鐘推送一個度量值) |
|最低需求|real|這兩個 Msee 推播的兩個度量值的最小值|
|最大值|real|這兩個 Msee 所推送的兩個度量值上限|
|平均|real|等於 (最小值 + 最大值) /2|
|總計|real|這兩個 Msee 中兩個計量值的總和 (主要值，以專注于所查詢的度量) |
  
## <a name="next-steps"></a>後續步驟

設定 ExpressRoute 連線。
  
* [建立及修改電路](expressroute-howto-circuit-arm.md)
* [建立和修改對等互連組態](expressroute-howto-routing-arm.md)
* [將 VNet 連結到 ExpressRoute 線路](expressroute-howto-linkvnet-arm.md)