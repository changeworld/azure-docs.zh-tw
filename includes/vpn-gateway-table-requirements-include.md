---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173866"
---
下表列出 PolicyBased 和 RouteBased VPN 閘道的需求。 此資料表適用於資源管理員與傳統部署模型。 就傳統模型而言，PolicyBased VPN 閘道與「靜態」閘道相同，而 RouteBased 閘道則與「動態」閘道相同。

|  | **基於策略的基本 VPN 閘道** | **基於路由的基本 VPN 閘道** | **基於路由的標準 VPN 閘道** | **基於路由的高性能 VPN 閘道** |
| --- | --- | --- | --- | --- |
| **網站到網站連接 （S2S）** |PolicyBased VPN 組態 |RouteBased VPN 組態 |RouteBased VPN 組態 |RouteBased VPN 組態 |
| **點對站連線 (P2S)** |不支援 |支援 (可與 S2S 並存) |支援 (可與 S2S 並存) |支援 (可與 S2S 並存) |
| **驗證方法** |預先共用金鑰 |S2S 連線的預先共用金鑰，P2S 連線的憑證 |S2S 連線的預先共用金鑰，P2S 連線的憑證 |S2S 連線的預先共用金鑰，P2S 連線的憑證 |
| **S2S 連接的數目上限** |1 |10 |10 |30 |
| **P2S 連接的數目上限** |不支援 |128 |128 |128 |
| **作用中路由支援 (BGP)** (*) |不支援 |不支援 |支援 |支援 |

  (*) BGP 傳統部署模型則不支援。
