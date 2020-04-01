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
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501915"
---
**不支援**具有 0.0.0.0/0 目標的用戶定義路由和閘道網上的 NSG。 使用此配置創建的閘道將阻止創建。 閘道需要存取管理控制器才能正常運行。 [BGP 路由傳播](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol])應設置為閘道網上的「已啟用」,以確保閘道的可用性。 如果設置為禁用,閘道將不起作用。
