---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 05/06/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 5a5f831e72da6bedaf12d3ed82e79f0250a09062
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82876045"
---
| 資源 | 限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10000。 結合所有規則類型。|
|最大 DNAT 規則|298<br>如果規則的通訊協定同時設定為 TCP 和 UDP，則會計算為兩個規則。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|1 - 65535|
|公用 IP 位址|250 DNAT 和 SNAT 埠的最大值。|
|IP 群組 IP 位址|50 IP 群組或更少：每個防火牆實例的最大5000個個別 IP 位址。<br>51-100 IP 群組：每個防火牆實例500個別的 IP 位址。<br><br>如需詳細資訊，請參閱[Azure 防火牆中的 IP 群組（預覽）](../articles/firewall/ip-groups.md#ip-address-limits)
|路由表|根據預設，AzureFirewallSubnet 具有 0.0.0.0/0 路由，並將 NextHopType 值設為**Internet**。<br><br>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 透過 BGP 學習到內部部署網路的預設路由，您必須以 0.0.0.0/0 UDR 覆寫它，並將**NextHopType**值設為 [**網際網路**]，以維持直接的網際網路連線能力。 根據預設，Azure 防火牆不支援對內部部署網路的強制通道。<br><br>不過，如果您的設定需要對內部部署網路的強制通道，Microsoft 將以個別案例為原則提供支援。 連絡支援人員，以便我們檢閱您的案例。 受理後，我們會允許您的訂用帳戶，並確實維持必要的防火牆網際網路連線。|

<sup>1</sup>如果您需要增加這些限制，請聯絡 Azure 支援。
