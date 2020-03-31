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
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74085226"
---
建立虛擬網路閘道時，您必須指定想要使用的閘道 SKU。 根據工作負載、輸送量、功能和 SLA 的類型，選取符合您需求的 SKU。 有關 Azure 可用性區域中的虛擬網路閘道 SKU，請參閱[Azure 可用性區域閘道 SKU](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md)。

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>依通道、連線和輸送量區分的閘道 SKU

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> VpnGw SKU（VpnGw1、VpnGw1AZ、VpnGw2、VpnGw2AZ、VpnGw3、VpnGw3AZ、VpnGw4、VpnGw4AZ、VpnGw5 和 VpnGw5AZ）僅支援資源管理器部署模型。 傳統虛擬網路應該繼續使用舊式 (舊版) SKU。
>  * 如需舊版閘道 SKU (基本、標準、高效能) 使用上的相關資訊，請參閱[使用 VPN 閘道 SKU (舊版 SKU)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)。
>  * 關於 ExpressRoute 閘道 SKU，請參閱 [ExpressRoute 的虛擬網路閘道](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>依功能集區分的閘道 SKU

新式 VPN 閘道 SKU 可簡化閘道上提供的功能集：

| **Sku**| **特徵**|
| ---    | ---         |
|**基本**（*）   | **路由式 VPN**：適用於 P2S/連線的 10 個通道；沒有適用於 P2S 的 RADIUS 驗證；沒有適用於 P2S 的 IKEv2<br>**原則式 VPN** (IKEv1)：1 個 S2S/連線通道；沒有 P2S|
| **除基本之外，所有第 1 代和第 2 代 SKU** | **路由式 VPN**：最多 30 個通道 ( * )，P2S、BGP、主動-主動、自訂 IPsec/IKE 原則、ExpressRoute/VPN 共存 |
|        |             |

(*)您可以配置"基於策略的流量選擇器"，將基於路由的 VPN 閘道連接到多個基於本地策略的防火牆設備。 如需詳細資訊，請參閱[使用 PowerShell 將 VPN 閘道連線至多個內部部署原則式 VPN 裝置](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md)。

(\*\*) 基本 SKU 被視為舊版 SKU。 基本 SKU 有某些功能限制。 您無法調整使用基本 SKU 的閘道大小來成為新的閘道 SKU，您必須改以變更為新的 SKU，而需要刪除並重新建立 VPN 閘道。

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>閘道 SKU - 生產與開發測試工作負載

由於 SLA 和功能集的差異，我們建議將下列 SKU 用於產生與開發測試：

| **[工作負載]**                       | **SKU**               |
| ---                                | ---                    |
| **生產、重要工作負載** | 除基本之外，所有第 1 代和第 2 代 SKU |
| **開發測試或概念證明**   | 基本 (\*\*)                 |
|                                    |                        |

(\*\*) 基本 SKU 被視為舊版 SKU，而且有功能限制。 請先確認其有支援您需要的功能，再使用基本 SKU。

如果您使用舊式 SKU (舊版)，生產 SKU 建議為標準和高效能。 如需舊式 SKU 的資訊和指示，請參閱[閘道 SKU (舊版)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md)。
