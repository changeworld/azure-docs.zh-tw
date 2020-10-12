---
title: 記錄和計量
titleSuffix: Azure Virtual WAN
description: 深入瞭解 Azure 虛擬 WAN 記錄和計量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/05/2020
ms.author: cherylmc
ms.openlocfilehash: 78165e9c14d4a83dbc20cbccd2f31dc8ac4c79ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440863"
---
# <a name="azure-virtual-wan-logs-and-metrics"></a>Azure 虛擬 WAN 記錄和計量

您可以使用 Azure 監視器來監視 Azure 虛擬 WAN。 虛擬 WAN 是一種網路服務，可將許多網路、安全性和路由功能整合在一起，以提供單一操作介面。 虛擬 WAN VPN 閘道、ExpressRoute 閘道和 Azure 防火牆具有可透過 Azure 監視器取得的記錄和計量。 如需 Azure 防火牆的資訊，請參閱 [Azure 防火牆記錄和計量](../firewall/logs-and-metrics.md)。

本文討論可透過入口網站取得的計量和診斷。 計量很輕量，可支援近乎即時的案例，讓它們對警示和快速的問題偵測很有用。

## <a name="metrics"></a>計量

Azure 監視器中的計量是數值，可描述系統在特定時間的某個方面。 計量會每分鐘收集一次，而且對於警示是很有用的，因為它們可以經常取樣。 您可以使用相對簡單的邏輯快速地引發警示。

### <a name="site-to-site-vpn-gateways"></a>站對站 VPN 閘道

下列計量適用于 Azure 站對站 VPN 閘道：

* **閘道頻寬** ：閘道的平均站對站匯總頻寬（以每秒位元組數為單位）。
* 通道**頻寬**：通道的平均頻寬（位元組/秒）。
* 通道輸出**位元組**–通道的傳出位元組。 
* 通道輸出封**包**–通道的傳出封包計數。 
* 通道輸出**TS 不相符**的封包卸載–來自流量選取器與通道不符的傳出封包計數。 
* 通道輸入**位元組**–通道的傳入位元組數。 
* 通道輸入封**包**–通道的傳入封包計數。 
* 通道輸入**TS 不相符**的封包卸載–來自流量選取器與通道不符的傳入封包下降計數。 

### <a name="point-to-site-vpn-gateways"></a>點對站 VPN 閘道

下列計量適用于 Azure 點對站 VPN 閘道：

* **閘道 P2S 頻寬** –閘道的平均點對站匯總頻寬（以每秒位元組數為單位）。
* **P2S 連接計數** –閘道的點對站連線計數。

### <a name="azure-expressroute-gateways"></a>Azure ExpressRoute 閘道

下列計量適用于 Azure ExpressRoute 閘道：

* **BitsInPerSecond** – Bits 輸入 Azure/秒。
* **BitsOutPerSecond** – Bits 零輸出 Azure/秒。

### <a name="view-gateway-metrics"></a><a name="metrics-steps"></a>查看閘道計量

下列步驟可協助您找出並查看度量：

1. 在入口網站中，流覽至具有閘道的虛擬中樞。

2. 選取 **VPN (站對站) ** 以找出站對站閘道、 **expressroute** 以找出 expressroute 閘道，或使用 **使用者 VPN (點對站) ** 以找出點對站閘道。 在頁面上，您可以查看閘道資訊。 複製此資訊。 您稍後將使用它來查看使用 Azure 監視器的診斷。

3. 選取 [計量]。

   :::image type="content" source="./media/logs-metrics/metrics.png" alt-text="螢幕擷取畫面顯示已選取 Azure 監視器中 View 的站對站 V P N 窗格。":::

4. 在 [ **度量** ] 頁面上，您可以查看您感興趣的度量。

   :::image type="content" source="./media/logs-metrics/metrics-page.png" alt-text="螢幕擷取畫面顯示已選取 Azure 監視器中 View 的站對站 V P N 窗格。":::

## <a name="diagnostic-logs"></a><a name="diagnostic"></a>診斷記錄

### <a name="site-to-site-vpn-gateways"></a>站對站 VPN 閘道

下列診斷適用于 Azure 站對站 VPN 閘道：

* **閘道診斷記錄** –閘道專屬的診斷，例如健康情況、設定、服務更新，以及額外的診斷。
* 通道**診斷記錄**–這些是與 IPsec 通道相關的記錄，例如站對站 IPsec 通道的連線和中斷線上活動、協商的 SAs、中斷連接原因，以及其他診斷。
* **路由診斷記錄** –這些是與靜態路由、BGP、路由更新和其他診斷事件相關的記錄。
* **Ike 診斷記錄** –適用于 IPsec 連線的 ike 特定診斷。

### <a name="point-to-site-vpn-gateways"></a>點對站 VPN 閘道

下列診斷適用于 Azure 點對站 VPN 閘道：

* **閘道診斷記錄** –閘道特定的診斷，例如健康情況、設定、服務更新，以及其他診斷。
* **Ike 診斷記錄** –適用于 IPsec 連線的 ike 特定診斷。
* **P2S 診斷記錄** –這些是使用者 VPN (點對站) P2S 設定和用戶端事件。 其中包括用戶端連線/中斷連線、VPN 用戶端位址配置，以及其他診斷。

### <a name="view-diagnostic-logs"></a><a name="diagnostic-steps"></a>檢視診斷記錄

下列步驟可協助您找出並查看診斷：

1. 在入口網站中，流覽至您的虛擬 WAN 資源。 在入口網站中，于 [虛擬 WAN] 頁面的 [ **總覽** ] 區段中，選取 [ **基本** ] 以展開視圖並取得資源群組資訊。 複製資源群組資訊。

   :::image type="content" source="./media/logs-metrics/3.png" alt-text="螢幕擷取畫面顯示已選取 Azure 監視器中 View 的站對站 V P N 窗格。":::

2. 在 [監視] 區段中，流覽至資源群組。 選取 [ **診斷設定**]，然後輸入資源資訊。 這是您在步驟2中從本文稍早的 [ [View gateway 計量](#metrics-steps) ] 區段中複製的資源資訊。

   :::image type="content" source="./media/logs-metrics/4.png" alt-text="螢幕擷取畫面顯示已選取 Azure 監視器中 View 的站對站 V P N 窗格。":::

3. 在 [結果] 頁面上，選取 [ **+ 新增診斷設定**]，然後選取一個選項。 您可以選擇傳送至 Log Analytics、串流至事件中樞，或只封存至儲存體帳戶。

   :::image type="content" source="./media/logs-metrics/5.png" alt-text="螢幕擷取畫面顯示已選取 Azure 監視器中 View 的站對站 V P N 窗格。":::

### <a name="log-analytics-sample-query"></a><a name="sample-query"></a>Log Analytics 範例查詢

記錄檔位於 **Azure Log Analytics 工作區**中。 您可以在 Log Analytics 中設定查詢。 下列範例包含可取得站對站路由診斷的查詢。

```AzureDiagnostics | where Category == "RouteDiagnosticLog"```

視需要在 **= =** 之後取代下列值。

* "GatewayDiagnosticLog"
* "IKEDiagnosticLog"
* "P2SDiagnosticLog"
* "TunnelDiagnosticLog"
* "RouteDiagnosticLog"

## <a name="activity-logs"></a><a name="activity-logs"></a>活動記錄

預設會收集**活動記錄**專案，而且可以在 Azure 入口網站中查看。 您可以使用 Azure 活動記錄 (之前稱為「作業 *記錄* 」和「 *審核記錄* 」) 來查看提交至您 Azure 訂用帳戶的所有作業。

## <a name="next-steps"></a>後續步驟

* 若要了解如何監視 Azure 防火牆記錄和計量，請參閱[教學課程：監視 Azure 防火牆記錄](../firewall/tutorial-diagnostics.md)。
* 若要深入瞭解 Azure 監視器中的計量，請參閱 [Azure 監視器中的計量](../azure-monitor/platform/data-platform-metrics.md)。
