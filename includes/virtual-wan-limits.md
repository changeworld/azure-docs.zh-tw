---
title: 包含檔案
description: 包含檔案
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/26/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 85c66aa6a8f0cb0efa231acee44f8fa02169d27b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88602538"
---
| 資源 |  限制 |
| --- | --- |
| 每個區域的虛擬 WAN 中樞 | 1  |
| 每個虛擬 WAN 的虛擬 WAN 中樞 |Azure 區域 |
| 每個中樞的 VPN (分支) 連線 | 1,000 |
| 彙總每個虛擬 WAN 站對站 VPN 閘道的輸送量 | 20 Gbps |
| 每個虛擬 WAN VPN 連線的輸送量 (2 個通道) | 2 Gbps 與 1 Gbps/IPsec 通道 |
| 每個中樞的點對站使用者| 10,000 |
| 彙總每個虛擬 WAN 使用者 VPN (點對站) 閘道的輸送量 | 20 Gbps |
| 彙總每個虛擬 WAN ExpressRoute 閘道的輸送量 | 20 Gbps |
| 每個中樞的 ExpressRoute 線路連線 | 4 |
| 每個中樞的 VNet 連線  | 500 減去虛擬 WAN 中的中樞總數 |
| 彙總每個虛擬 WAN 中樞路由器的輸送量 | 50 Gbps 適用於 VNet 對 VNet 傳輸 |
| 連線至單一虛擬 WAN 中樞的所有 VNet 上的 VM 工作負載 | 2000 
