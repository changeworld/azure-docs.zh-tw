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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81275163"
---
**不支援**具有 0.0.0.0/0 目的地的使用者定義路由和 GatewaySubnet 上的 NSG。 使用此組態建立的閘道將會遭到封鎖而無法建立。 閘道需要存取管理控制器，才能正常運作。 GatewaySubnet 上的 [BGP 路由傳播](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol)應該設定為 [已啟用]，以確保閘道的可用性。 如果這設定為已停用，閘道將無法運作。
