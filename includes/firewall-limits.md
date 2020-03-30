---
title: 包含檔案
description: 包含檔案
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 01/22/2020
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: 0df38533afe97f010d1050c3ee2a4a69a54d4cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334730"
---
| 資源 | 限制 |
| --- | --- |
| 資料輸送量 |30 Gbps<sup>1</sup> |
|規則|10,000. 所有規則類型合併。|
|最大 DNAT 規則|299|
|AzureFirewallSubnet 大小下限 |/26|
|網路和應用程式規則中的連接埠範圍|0-64,000。 我們正努力放寬這項限制。|
|公用 IP 位址|最大值為 100 個（目前，僅為前五個公共 IP 位址添加 SNAT 埠。|
|路由表|預設情況下，Azure 防火牆子網具有 0.0.0.0/0 路由，NextHopType 值設置為**Internet**。<br><br>「Azure 防火牆」必須能夠直接連線到網際網路。 如果您的 Azure 防火牆 Subnet 通過 BGP 學習到本地網路的預設路由，則必須使用 0.0.0.0/0 UDR 重寫該路由 **，NextHopType**值設置為**Internet**以維護直接的 Internet 連接。 根據預設，Azure 防火牆不支援對內部部署網路的強制通道。<br><br>不過，如果您的設定需要對內部部署網路的強制通道，Microsoft 將以個別案例為原則提供支援。 連絡支援人員，以便我們檢閱您的案例。 受理後，我們會允許您的訂用帳戶，並確實維持必要的防火牆網際網路連線。|

<sup>1</sup>如果需要增加這些限制，請與 Azure 支援部門聯繫。
