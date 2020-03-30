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
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838169"
---
不支援具有 0.0.0.0/0 目標的使用者定義路由和閘道**子網上的**NSG。 使用此配置創建的閘道將阻止創建。 閘道需要訪問管理控制器才能正常運行。
