---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74828901"
---
|**VPN<br>閘道<br>世代** |**SKU**   | **S2S/VNet-to-VNet<br>通道** | **P2S<br> SSTP 連線** | **P2S<br> IKEv2/OpenVPN 連線** | **彙總<br>輸送量基準測試** | **BGP** | **Zone-redundant** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**第 1 代**|**基本**   | 最大 10    | 最大 128  | 不支援  | 100 Mbps  | 不支援| 否 |
|**第 1 代**|**VpnGw1**  | 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支援 | 否 |
|**第 1 代**|**VpnGw2**  | 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支援 | 否 |
|**第 1 代**|**VpnGw3**  | 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支援 | 否 |
|**第 1 代**|**VpnGw1AZ**| 最大 30*   | 最大 128  | 最大 250       | 650 Mbps  | 支援 | 是 |
|**第 1 代**|**VpnGw2AZ**| 最大 30*   | 最大 128  | 最大 500       | 1 Gbps    | 支援 | 是 |
|**第 1 代**|**VpnGw3AZ**| 最大 30*   | 最大 128  | 最大 1000      | 1.25 Gbps | 支援 | 是 |
|        |            |            |           |                |           |           |     |
|**第 2 代**|**VpnGw2**  | 最大 30*   | 最大 128  | 最大 500       | 1.25 Gbps | 支援 | 否 |
|**第 2 代**|**VpnGw3**  | 最大 30*   | 最大 128  | 最大 1000      | 2.5 Gbps  | 支援 | 否 |
|**第 2 代**|**VpnGw4**  | 最大 30*   | 最大 128  | 最大 5000      | 5 Gbps    | 支援 | 否 |
|**第 2 代**|**VpnGw5**  | 最大 30*   | 最大 128  | 最大 10000      | 10 Gbps   | 支援 | 否 |
|**第 2 代**|**VpnGw2AZ**| 最大 30*   | 最大 128  | 最大 500       | 1.25 Gbps | 支援 | 是 |
|**第 2 代**|**VpnGw3AZ**| 最大 30*   | 最大 128  | 最大 1000      | 2.5 Gbps  | 支援 | 是 |
|**第 2 代**|**VpnGw4AZ**| 最大 30*   | 最大 128  | 最大 5000      | 5 Gbps    | 支援 | 是 |
|**第 2 代**|**VpnGw5AZ**| 最大 30*   | 最大 128  | 最大 10000      | 10 Gbps   | 支援 | 是 |

(*) 如果您需要超過 30 個 S2S VPN 通道，請使用[虛擬 WAN](../articles/virtual-wan/virtual-wan-about.md)。

* VpnGw SKU 的大小重新調整可在相同世代內進行，但「基本」SKU 的大小重新調整除外。 「基本」SKU 是舊版 SKU，而且有功能限制。 若要從「基本」移到另一個 VpnGw SKU，您必須刪除「基本」SKU VPN 閘道，並使用所需的世代和 SKU 大小組合建立新閘道。

* 這些連線數限制是個別的。 例如，您在 VpnGw1 SKU 上可以有 128 個 SSTP 連線和 250 個 IKEv2 連線。

* 在 [價格](https://azure.microsoft.com/pricing/details/vpn-gateway) 頁面上可以找到價格資訊。

* 可以在 [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) 頁面上找到 SLA (服務等級協定) 資訊。

* 在單一通道上，可以達到最多 1 Gbps 的輸送量。 上表中的「彙總輸送量基準測試」是以透過單一閘道所彙總多個通道的量值為基礎。 VPN 閘道的彙總輸送量基準是 S2S + P2S 的組合。 **如果您有許多 P2S 連線，S2S 連線即可能因為輸送量限制而受到負面影響。** 由於網際網路流量條件和您的應用程式行為，彙總輸送量基準測試不是保證的輸送量。

為了協助我們的客戶了解 SKU 使用不同演算法的相對效能，我們使用了可公開取得的 iPerf 和 CTSTraffic 工具來測量效能。 下表列出第 1 代、VpnGw SKU 的效能測試結果。 如您所見，當我們針對 IPsec 加密和完整性使用 GCMAES256 演算法時，將會取得最佳效能。 當您針對 IPsec 加密和完整性使用 AES256 和 SHA256 時，我們會取得平均效能。 當您針對 IPsec 加密和完整性使用 DES3 和 SHA256 時，我們會取得最低效能。

|**世代**|**SKU**   | **使用的<br>演算法** | **觀察到的<br>輸送量** | **觀察到的<br>每秒封包數** |
|---           |---       | ---                 | ---            | ---                    |
|**第 1 代**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**第 1 代**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**第 1 代**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
|**第 1 代**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mbps<br>500 Mbps<br>120 Mbps   | 58,000<br>50,000<br>50,000|
|**第 1 代**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gbps<br>500 Mbps<br>120 Mbps | 90,000<br>80,000<br>55,000|
|**第 1 代**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1.25 Gbps<br>550 Mbps<br>120 Mbps | 105,000<br>90,000<br>60,000|
