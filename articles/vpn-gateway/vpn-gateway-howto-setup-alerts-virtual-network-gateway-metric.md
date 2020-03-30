---
title: 在 Azure VPN 閘道指標上設置警報
description: 在 VPN 閘道指標上配置警報的步驟
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: d57663f683ba4e2107ec6813a19fac7b2dcdd26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67605220"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>在 VPN 閘道指標上設置警報

本文可説明您在 Azure VPN 閘道指標上設置警報。 Azure 監視器提供了為 Azure 資源設置警報的能力。 您可以為"VPN"類型的虛擬網路閘道設置警報。


|**度量**   | **單位** | **細微度** | **描述** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 位元組/s  | 5 分鐘| 閘道上所有網站到網站連接的平均總頻寬利用率。     |
|**P2SBandwidth**| 位元組/s  | 1 分鐘  | 閘道上所有點對點連接的平均總頻寬利用率。    |
|**P2SConnectionCount**| Count  | 1 分鐘  | 閘道上的點對點連接計數。   |
|**TunnelAverageBandwidth** | 位元組/s    | 5 分鐘  | 在閘道上創建的隧道的平均頻寬利用率。 |
|**TunnelEgressBytes** | 位元組 | 5 分鐘 | 在閘道上創建的隧道上的傳出流量。   |
|**TunnelEgressPackets** | Count | 5 分鐘 | 在閘道上創建的隧道上傳出資料包計數。   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 分鐘 | 由於流量選擇器不匹配而丟棄在隧道上的傳出資料包計數。 |
|**TunnelIngressBytes** | 位元組 | 5 分鐘 | 在閘道上創建的隧道上的傳入流量。   |
|**TunnelIngressPackets** | Count | 5 分鐘 | 在閘道上創建的隧道上傳入資料包計數。   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 分鐘 | 由於流量選擇器不匹配而丟棄在隧道上的傳入資料包計數。 |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>使用 Azure 門戶根據指標設置 Azure 監視警報

以下示例步驟將在閘道上創建警報，用於：

- **指標：** 隧道平均頻寬
- **條件：** 頻寬> 10 位元組/秒
- **視窗：** 5 分鐘
- **警報操作：** 電子郵件



1. 轉到虛擬網路閘道資源並從 **"監視"** 選項卡中選擇 **"警報**"。然後創建新的警報規則或編輯現有的警報規則。

   ![創建警報規則的選擇](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "建立")

2. 選擇 VPN 閘道作為資源。

   ![資源清單中的"選擇"按鈕和 VPN 閘道](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "選取")

3. 選擇要為警報配置的指標。

   ![指標清單中的選定指標](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "選取")
4. 配置信號邏輯。 它有三個元件：

    a. **維度**：如果指標具有維度，則可以選擇特定的維度值，以便警報僅評估該維度的資料。 這些是可選的。

    b. **條件**：這是計算指標值的操作。

    c. **時間**：指定指標資料的細微性以及評估警報的時間段。

   ![配置信號邏輯的詳細資訊](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "選取")

5. 要查看配置的規則，請選擇 **"管理警報規則**"。

   ![用於管理警報規則的按鈕](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "選取")

## <a name="next-steps"></a>後續步驟

要配置隧道診斷日誌上的警報，請參閱[在 VPN 閘道診斷日誌上設置警報](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
