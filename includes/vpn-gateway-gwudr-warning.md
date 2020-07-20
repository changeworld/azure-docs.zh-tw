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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81275163"
---
**不支援**具有 0.0.0.0/0 目的地的使用者定義路由和 GatewaySubnet 上的 nsg。 使用此設定建立的閘道將會遭到封鎖而無法建立。 閘道需要存取管理控制器，才能正常運作。 [BGP 路由傳播](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol)應設定為 GatewaySubnet 上的「已啟用」，以確保閘道的可用性。 如果此設定為 [停用]，閘道將無法運作。
