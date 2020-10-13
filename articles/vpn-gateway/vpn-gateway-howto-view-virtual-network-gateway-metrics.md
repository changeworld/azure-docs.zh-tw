---
title: 查看 Azure VPN 閘道計量
description: 查看 VPN 閘道計量的步驟
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 4b33af3e64726e124373f57920836bce145cd891
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89443172"
---
# <a name="view-vpn-gateway-metrics"></a>檢視 VPN 閘道計量

您可以使用 Azure 監視器來監視 Azure VPN 閘道。 本文討論可透過入口網站使用的計量。 計量很輕量，可支援近乎即時的案例，讓它們對警示和快速的問題偵測很有用。


|**計量**   | **單位** | **細微度** | **說明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 位元組/秒  | 5 分鐘| 閘道上所有站對站連線的平均合併頻寬使用率。     |
|**P2SBandwidth**| 位元組/秒  | 1 分鐘  | 閘道上所有點對站連線的平均合併頻寬使用率。    |
|**P2SConnectionCount**| Count  | 1 分鐘  | 閘道上的點對站連線計數。   |
|**TunnelAverageBandwidth** | 位元組/秒    | 5 分鐘  | 在閘道上建立之通道的平均頻寬使用率。 |
|**TunnelEgressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連出流量。   |
|**TunnelEgressPackets** | Count | 5 分鐘 | 在閘道上建立之通道上的傳出封包計數。   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 分鐘 | 流量選取器不相符所造成的通道上丟棄的傳出封包計數。 |
|**TunnelIngressBytes** | 位元組 | 5 分鐘 | 在閘道上建立的通道上的連入流量。   |
|**TunnelIngressPackets** | Count | 5 分鐘 | 在閘道上建立之通道上的傳入封包計數。   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 分鐘 | 流量選取器不相符所造成的通道上丟棄的傳入封包計數。 |

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>下列步驟可協助您找出並查看度量：

1. 流覽至入口網站中的虛擬網路閘道資源
2. 選取 **[總覽** ] 以查看通道輸入和輸出計量總數。

   ![建立警示規則的選項](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "檢視")

3. 以查看上面所列的任何其他度量。 按一下虛擬網路閘道資源底下的 [ **計量** ] 區段，然後從下拉式清單中選取度量。

   ![資源清單中的 [選取] 按鈕和 VPN 閘道](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Select")

## <a name="next-steps"></a>後續步驟

若要設定通道計量的警示，請參閱 [設定 VPN 閘道計量的警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)。

若要設定通道資源記錄的警示，請參閱 [設定 VPN 閘道資源記錄的警示](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
