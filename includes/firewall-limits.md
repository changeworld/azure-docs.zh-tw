---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 04/03/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 4972a80ec7c481ac7bc8860c005c8576ce37a090
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758454"
---
| 資源 | 限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10,000. 所有規則類型合併。|
|最大 DNAT 規則|298<br>如果為 TCP 和 UDP 配置了規則的協定,則它計為兩個規則。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|公用 IP 位址|最大值為 100 個(目前,僅為前五個公共 IP 位址添加 SNAT 連接埠。|
|IP 群組 IP 位址|50 個或更少 IP 組:每個防火牆實例最多 5000 個單獨的 IP 位址。<br>51 - 100 個 IP 組:每個防火牆實例 500 個單獨的 IP 位址。<br><br>有關詳細資訊,請參閱[Azure 防火牆中的 IP 組(預覽)](../articles/firewall/ip-groups.md#ip-address-limits)
|路由表|預設情況下,Azure 防火牆子網具有 0.0.0.0/0 路由,NextHopType 值設定為**Internet**。<br><br>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 Azure 防火牆 Subnet 通過 BGP 學習到本地網路的預設路由,則必須使用 0.0.0.0/0 UDR 重寫該路由 **,NextHopType**值設置為**Internet**以維護直接的 Internet 連接。 根據預設，Azure 防火牆不支援對內部部署網路的強制通道。<br><br>不過，如果您的設定需要對內部部署網路的強制通道，Microsoft 將以個別案例為原則提供支援。 連絡支援人員，以便我們檢閱您的案例。 受理後，我們會允許您的訂用帳戶，並確實維持必要的防火牆網際網路連線。|

<sup>1</sup>如果需要增加這些限制,請與 Azure 支援部門聯繫。
