---
title: 包含檔案
description: 包含檔案
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854065"
---
| 資源                                | 限制        |
|-----------------------------------------|------------------------------|
| VNet 位址首碼                   | 每個 VPN 閘道600          |
| 匯總 BGP 路由                    | 每個 VPN 閘道4000        |
| 局域網路閘道位址首碼  | 每個局域網路閘道1000               |
| S2S 連線                         | [取決於閘道 SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| P2S 連線                         | [取決於閘道 SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| P2S 路由限制-IKEv2                 | 適用于 Windows 的非 Windows 25 的 256 **/**           |
| P2S 路由限制-OpenVPN               | 1000                         |
| 最大 flows                              | 適用于  **/**  VpnGw2-4/az 的 VpnGw1/az 512K 10 萬|