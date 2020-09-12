---
title: 設定 Azure VPN 閘道計量的警示
description: 瞭解如何使用 Azure 入口網站，根據虛擬網路 VPN 閘道的度量設定 Azure 監視器警示。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435652"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>設定 VPN 閘道計量的警示

本文可協助您設定 Azure VPN 閘道計量的警示。 Azure 監視器提供設定 Azure 資源警示的功能。 您可以為 "VPN" 類型的虛擬網路閘道設定警示。


|**計量**   | **單位** | **細微度** | **說明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 位元組/秒  | 5 分鐘| 閘道上所有站對站連線的平均合併頻寬使用率。     |
|**P2SBandwidth**| 位元組/秒  | 1 分鐘  | 閘道上所有點對站連線的平均合併頻寬使用率。    |
|**P2SConnectionCount**| 計數  | 1 分鐘  | 閘道上的點對站連線計數。   |
|**TunnelAverageBandwidth** | 位元組/秒    | 5 分鐘  | 在閘道上建立之通道的平均頻寬使用率。 |
|**TunnelEgressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連出流量。   |
|**TunnelEgressPackets** | 計數 | 5 分鐘 | 在閘道上建立之通道上的傳出封包計數。   |
|**TunnelEgressPacketDropTSMismatch** | 計數 | 5 分鐘 | 流量選取器不相符所造成的通道上丟棄的傳出封包計數。 |
|**TunnelIngressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連入流量。   |
|**TunnelIngressPackets** | 計數 | 5 分鐘 | 在閘道上建立之通道上的傳入封包計數。   |
|**TunnelIngressPacketDropTSMismatch** | 計數 | 5 分鐘 | 流量選取器不相符所造成的通道上丟棄的傳入封包計數。 |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>使用 Azure 入口網站根據計量設定 Azure 監視器警示

下列範例步驟會在閘道上建立警示：

- **度量：** TunnelAverageBandwidth
- **條件：** 頻寬 > 10 個位元組/秒
- **視窗：** 5 分鐘
- **警示動作：** 電子郵件



1. 移至虛擬網路閘道資源，然後從 [**監視**] 索引標籤中選取 [**警示**]。然後建立新的警示規則，或編輯現有的警示規則。

   ![建立警示規則的選項](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "建立")

2. 選取您的 VPN 閘道做為資源。

   ![資源清單中的 [選取] 按鈕和 VPN 閘道](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "選取")

3. 選取要為警示設定的度量。

   ![計量清單中選取的度量](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "選取")
4. 設定信號邏輯。 其中有三個元件：

    a. **維度**：如果計量具有維度，您可以選取特定的維度值，讓警示只會評估該維度的資料。 這些都是選擇性的。

    b. **條件**：這是用來評估度量值的運算。

    c. **時間**：指定度量資料的資料細微性，以及評估警示的時間。

   ![設定信號邏輯的詳細資料](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "選取")

5. 若要查看設定的規則，請選取 [ **管理警示規則**]。

   ![用於管理警示規則的按鈕](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "選取")

## <a name="next-steps"></a>接下來的步驟

若要設定通道資源記錄的警示，請參閱 [設定 VPN 閘道資源記錄的警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
