---
title: 查看 Azure VPN 閘道計量
description: 查看 VPN 閘道計量的步驟
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531253"
---
# <a name="view-vpn-gateway-metrics"></a>View VPN 閘道計量

您可以使用 Azure 監視器來監視 Azure VPN 閘道。 本文討論可透過入口網站取得的計量。 計量是輕量的，可支援近乎即時的案例，使其適用于警示和快速的問題偵測。


|**計量**   | **單位** | **細微度** | **說明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 位元組/秒  | 5 分鐘| 閘道上所有站對站連線的平均結合頻寬使用量。     |
|**P2SBandwidth**| 位元組/秒  | 1 分鐘  | 閘道上所有點對站連線的平均結合頻寬使用量。    |
|**P2SConnectionCount**| 計數  | 1 分鐘  | 閘道上的點對站連線計數。   |
|**TunnelAverageBandwidth** | 位元組/秒    | 5 分鐘  | 在閘道上建立之通道的平均頻寬使用量。 |
|**TunnelEgressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連出流量。   |
|**TunnelEgressPackets** | 計數 | 5 分鐘 | 在閘道上建立之通道上的傳出封包計數。   |
|**TunnelEgressPacketDropTSMismatch** | 計數 | 5 分鐘 | 流量選取器不相符所造成的通道上丟棄的傳出封包計數。 |
|**TunnelIngressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連入流量。   |
|**TunnelIngressPackets** | 計數 | 5 分鐘 | 在閘道上建立的通道上的傳入封包計數。   |
|**TunnelIngressPacketDropTSMismatch** | 計數 | 5 分鐘 | 因流量選取器不相符而在通道上捨棄的傳入封包計數。 |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>下列步驟可協助您找出並查看計量：

1. 流覽至入口網站中的虛擬網路閘道資源
2. 選取 **[總覽**] 以查看通道輸入和輸出計量的總數。

   ![建立警示規則的選取專案](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "檢視")

3. 若要查看以上所列的任何其他計量。 按一下虛擬網路閘道資源底下的 [**計量**] 區段，然後從下拉式清單中選取 [度量]。

   ![資源清單中的 [選取] 按鈕和 [VPN 閘道]](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "選取")

## <a name="next-steps"></a>後續步驟

若要設定通道計量的警示，請參閱[設定 VPN 閘道計量的警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。

若要設定通道資源記錄的警示，請參閱[在 VPN 閘道資源記錄上設定警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
