---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556743"
---
**不支援** 具有 0.0.0.0/0 目的地的使用者定義路由和 GatewaySubnet 上的 NSG。 使用此組態建立的閘道將會遭到封鎖而無法建立。 閘道需要存取管理控制器，才能正常運作。 GatewaySubnet 上的 [BGP 路由傳播](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol)應該設定為 [已啟用]，以確保閘道的可用性。 如果這設定為已停用，閘道將無法運作。