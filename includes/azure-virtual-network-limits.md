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
ms.openlocfilehash: 59329fccda77f16e4a595e9b1789ef684c5cbf95
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256424"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>網路限制 - Azure Resource Manager
下列限制僅適用於透過每個訂用帳戶每一區域的 **Azure Resource Manager** 所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

> [!NOTE]
> 我們最近已將所有預設限制提升至其最大限制。 如果沒有上限欄，資源即沒有可調整的限制。 如果您先前已透過支援增加這些限制，卻未在下表中看到更新的限制，則可[免費提出線上客戶支援要求](../articles/azure-resource-manager/templates/error-resource-quota.md)

| 資源 | 限制 | 
| --- | --- |
| 虛擬網路 |1,000 |
| 每個虛擬網路的子網路數目 |3,000 |
| 每個虛擬網路的虛擬網路對等互連數目 |500 |
| [每個虛擬網路的虛擬網路閘道 (VPN 閘道)](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [每個虛擬網路的虛擬網路閘道 (ExpressRoute 閘道)](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| 每個虛擬網路的 DNS 伺服器 |20 |
| 每個虛擬網路的私人 IP 位址數目 |65,536 |
| 每個網路介面的私人 IP 位址 |256 |
| 每個虛擬機器的私人 IP 位址 |256 |
| 每個網路介面的公用 IP 位址 |256 |
| 每個虛擬機器的公用 IP 位址 |256 |
| [虛擬機器或角色執行個體之每個 NIC 的並行 TCP 或 UDP 流程](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) |500,000 |
| 網路介面卡 |65,536 |
| 網路安全性群組 |5,000 |
| 每一 NSG 的 NSG 規則 |1,000 |
| 針對安全性群組中的來源或目的地所指定的 IP 位址和範圍 |4,000 |
| 應用程式安全性群組 |3,000 |
| 每個 NIC、每個 IP 組態的應用程式安全性群組 |20 |
| 每個應用程式安全性群組的 IP 組態 |4,000 |
| 可在網路安全性群組的所有安全性規則內指定的應用程式安全性群組 |100 |
| 使用者定義的路由表 |200 |
| 每個路由表的使用者定義路由 |400 |
| 每個 Azure VPN 閘道的點對站根憑證 |20 |
| 虛擬網路 TAP |100 |
| 每個虛擬網路 TAP 的網路介面 TAP 設定 |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>公用 IP 位址限制
| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 公用 IP 位址<sup>1</sup> | 10 (適用於基本版)。 | 請連絡支援人員。 |
| 靜態公用 IP 位址<sup>1</sup> | 10 (適用於基本版)。 | 請連絡支援人員。 |
| 標準公用 IP 位址<sup>1</sup> | 10 | 請連絡支援人員。 |
| [每個資源群組的公用 IP 位址](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | 請連絡支援人員。 | 
| 公用 IP 前置詞 | 受訂用帳戶中標準公用 IP 數目的限制 | 請連絡支援人員。 |
| 公用 IP 前置長度 | /28 | 請連絡支援人員。 |

<sup>1</sup>公用 IP 的預設限制會因供應項目類別類型 (如免費試用、隨用隨付、CSP 等等) 而有所差異。 例如，Enterprise 合約訂用帳戶的預設值是 1000。

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>負載平衡器限制
下列限制僅適用於透過每個訂用帳戶每一區域的 Azure Resource Manager 所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

**Standard Load Balancer**

| 資源                                | 限制         |
|-----------------------------------------|-------------------------------|
| 負載平衡器                          | 1,000                         |
| 每個資源的規則                      | 1,500                         |
| 每個 NIC 的規則 (在 NIC 上的所有 IP) | 300                           |
| 前端 IP 組態              | 600                           |
| 後端集區大小                       | 1000 個 IP 組態，單一虛擬網路 |
| 每個 Load Balancer 的後端資源<sup>1<sup> | 250                   |
| 高可用性連接埠                 | 每個內部前端 1 個       |
| 每個 Load Balancer 的輸出規則        | 600                           |
| 每個 VM 的負載平衡器                   | 2 個 (1 個公用和 1 個內部)   |

<sup>1</sup>限制為最多 150 個資源，是獨立虛擬機器資源、可用性設定組資源以及虛擬機器擴展集放置群組的任意組合。

**基本負載平衡器**

| 資源                                | 限制        |
|-----------------------------------------|------------------------------|
| 負載平衡器                          | 1,000                        |
| 每個資源的規則                      | 250                          |
| 每個 NIC 的規則 (在 NIC 上的所有 IP) | 300                          |
| 前端 IP 組態              | 200                          |
| 後端集區大小                       | 300 個 IP 組態，單一可用性設定組 |
| 每個 Load Balancer 的可用性設定組     | 1                            |
| 每個 VM 的負載平衡器                   | 2 個 (1 個公用和 1 個內部)  |

<a name="virtual-networking-limits-classic"></a>下列限制僅適用於透過每個訂用帳戶的 **傳統** 部署模型所管理的網路資源。 深入了解如何[根據您的訂用帳戶限制檢視目前資源使用量](../articles/networking/check-usage-against-limits.md)。

| 資源 | 預設限制 | 上限 |
| --- | --- | --- |
| 虛擬網路 |100 |100 |
| 區域網路網站 |20 |50 |
| 每個虛擬網路的 DNS 伺服器 |20 |20 |
| 每個虛擬網路的私人 IP 位址數目 |4,096 |4,096 |
| 虛擬機器或角色執行個體之每個 NIC 的並行 TCP 或 UDP 流程 |500,000 個，最多 1,000,000 個 (適用於 2 個以上的 NIC)。 |500,000 個，最多 1,000,000 個 (適用於 2 個以上的 NIC)。 |
| 網路安全性群組 (NSG) |200 |200 |
| 每一 NSG 的 NSG 規則 |200 |1,000 |
| 使用者定義的路由表 |200 |200 |
| 每個路由表的使用者定義路由 |400 |400 |
| 公用 IP 位址 (動態) |500 |500 |
| 保留的公用 IP 位址 |500 |500 |
| 每個部署的公用 IP |5 |請連絡支援人員 |
| 每個部署的私人 IP (內部負載平衡) |1 |1 |
| 端點存取控制清單 (ACL) |50 |50 |
