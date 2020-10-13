---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 07/30/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 59699d493880034ad1d26a56c63a9ed8401ef371
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87507651"
---
| 資源 | 限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10,000。 結合所有規則類型。|
|DNAT 規則數上限|單一公用 IP 位址可有 298 個。<br>任何其他公用 IP 位址都會貢獻可用的 SNAT 連接埠，但會減少可用的 DNAT 規則數目。 例如，兩個公用 IP 位址能容納 297 個 DNAT 規則。 如果同時針對 TCP 和 UDP 設定規則的通訊協定，則會將其算成兩個規則。|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|1 - 65535|
|公用 IP 位址|最大值 250。 所有公用 IP 位址都可以在 DNAT 規則中使用，而且這些公用 IP 位址全都會提供給可用的 SNAT 埠。|
|IP 群組中的 IP 位址|每個防火牆最多 100 個 IP 群組。<br>每個 IP 群組最多 5000 個個別 IP 位址或 IP 首碼。
|路由表|根據預設，AzureFirewallSubnet 具有 NextHopType 值設為 **Internet** 的 0.0.0.0/0 路由。<br><br>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 AzureFirewallSubnet 學習到透過 BGP 連至您內部部署網路的預設路由，您必須將其覆寫為 0.0.0.0/0 UDR，且 **NextHopType** 值必須設為 **Internet**，以保有直接網際網路連線。 根據預設，Azure 防火牆不支援對內部部署網路的強制通道。<br><br>不過，如果您的設定需要對內部部署網路的強制通道，Microsoft 將以個別案例為原則提供支援。 連絡支援人員，以便我們檢閱您的案例。 受理後，我們會允許您的訂用帳戶，並確實維持必要的防火牆網際網路連線。|

<sup>1</sup> 如果需要增加這些限制，請連絡 Azure 支援。
