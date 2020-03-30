---
title: 包含檔案
description: 包含檔案
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 8752585e731f905636f57d31741e2be67f7140b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335030"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>網路限制 - Azure 資源管理器
以下限制僅適用于每個訂閱每個訂閱通過**Azure 資源管理器**管理的網路資源。 瞭解如何[根據訂閱限制查看當前資源使用方式](../articles/networking/check-usage-against-limits.md)。

> [!NOTE]
> 我們最近已將所有預設限制提升至其最大限制。 如果沒有最大限制列，則資源沒有可調整的限制。 如果您過去因支援而增加這些限制，並且未在下表中看到更新的限制，[請免費打開線上客戶支援請求](../articles/azure-resource-manager/templates/error-resource-quota.md)

| 資源 | 限制 | 
| --- | --- |
| 虛擬網路 |1,000 |
| 每一虛擬網路的子網路 |3,000 |
| 每個虛擬網路的虛擬網路對等互連數目 |500 |
| [每個虛擬網路的虛擬網路閘道 （VPN 閘道）](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [每個虛擬網路的虛擬網路閘道（快速路由閘道）](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| 每個虛擬網路的 DNS 伺服器 |20 |
| 每個虛擬網路的私人 IP 位址數目 |65,536 |
| 每個網路介面的私人 IP 位址 |256 |
| 每個虛擬機器的私人 IP 位址 |256 |
| 每個網路介面的公共 IP 位址 |256 |
| 每個虛擬機器的公共 IP 位址 |256 |
| [虛擬機器或角色執行個體之每個 NIC 的並行 TCP 或 UDP 流程](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500,000 |
| 網路介面卡 |65,536 |
| 網路安全性群組 |5,000 |
| 每一 NSG 的 NSG 規則 |1,000 |
| 針對安全性群組中的來源或目的地所指定的 IP 位址和範圍 |4,000 |
| 應用程式安全性群組 |3,000 |
| 每個 NIC、每個 IP 組態的應用程式安全性群組 |20 |
| 每個應用程式安全性群組的 IP 組態 |4,000 |
| 可在網路安全性群組的所有安全性規則內指定的應用程式安全性群組 |100 |
| 使用者定義的路由表 |200 |
| 使用者定義的每個路由表的路由 |400 |
| 每個 Azure VPN 閘道的點對點根憑證 |20 |
| 虛擬網路 TAP |100 |
| 每個虛擬網路 TAP 的網路介面 TAP 設定 |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>公用 IP 位址限制
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 公共 IP 位址<sup>1</sup> | 10 表示基本。 | 請連絡支援人員。 |
| 靜態公共 IP 位址<sup>1</sup> | 10 表示基本。 | 請連絡支援人員。 |
| 標準公共 IP 位址<sup>1</sup> | 10 | 請連絡支援人員。 |
| 公用 IP 前置詞 | 受訂閱中標準公共 IP 數量的限制 | 請連絡支援人員。 |
| 公共 IP 前置長度 | /28 | 請連絡支援人員。 |

<sup>1</sup>公共 IP 位址的預設限制因產品/服務類別類型而異，例如免費試用、即用即付、CSP。 例如，企業協定訂閱的預設值為 1000。

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>負載等化器限制
下列限制僅適用於透過每個訂用帳戶每一區域的 Azure Resource Manager 所管理的網路資源。 瞭解如何[根據訂閱限制查看當前資源使用方式](../articles/networking/check-usage-against-limits.md)。

**Standard Load Balancer**

| 資源                                | 限制         |
|-----------------------------------------|-------------------------------|
| 負載平衡器                          | 1,000                         |
| 每個資源的規則                      | 1,500                         |
| 每個 NIC 的規則（跨 NIC 上的所有 IP） | 300                           |
| 前端 IP 配置              | 600                           |
| 後端集區大小                       | 1，000 個 IP 配置，單個虛擬網路 |
| 高可用性埠                 | 每個內部前端 1 個       |
| 每個負載等化器的出站規則        | 20                            |
| [TCP 空閒超時](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 分鐘/30 分鐘          |


**基本負載平衡器**

| 資源                                | 限制        |
|-----------------------------------------|------------------------------|
| 負載平衡器                          | 1,000                        |
| 每個資源的規則                      | 250                          |
| 每個 NIC 的規則（跨 NIC 上的所有 IP） | 300                          |
| 前端 IP 配置              | 200                          |
| 後端集區大小                       | 300 個 IP 配置，單一可用性集 |
| 每個負載等化器的可用性集     | 150                          |

<a name="virtual-networking-limits-classic"></a>以下限制僅適用于通過每個訂閱**的經典**部署模型管理的網路資源。 瞭解如何[根據訂閱限制查看當前資源使用方式](../articles/networking/check-usage-against-limits.md)。

| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 虛擬網路 |100 |100 |
| 區域網路網站 |20 |50 |
| 每個虛擬網路的 DNS 伺服器 |20 |20 |
| 每個虛擬網路的私人 IP 位址數目 |4,096 |4,096 |
| 虛擬機器或角色執行個體之每個 NIC 的並行 TCP 或 UDP 流程 |500，000，兩個或兩個以上 NIC 最多 1，000，000 個。 |500，000，兩個或兩個以上 NIC 最多 1，000，000 個。 |
| 網路安全性群組 (NSG) |200 |200 |
| 每一 NSG 的 NSG 規則 |1,000 |1,000 |
| 使用者定義的路由表 |200 |200 |
| 使用者定義的每個路由表的路由 |400 |400 |
| 公用 IP 位址 (動態) |500 |500 |
| 保留的公用 IP 位址 |500 |500 |
| 每個部署的公用 VIP |5 |請連絡支援人員 |
| 每個部署的專用 VIP（內部負載平衡） |1 |1 |
| 端點存取控制清單 （ACL） |50 |50 |
