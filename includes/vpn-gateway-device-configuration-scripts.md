---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77111240"
---
| **供應商** | **裝置系列** | **韌體版本** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (預覽)|
|Cisco | ASA | 適用於版本低於 9.8 之 ASA 的 ASA ( * ) RouteBased (IKEv2 - 沒有 BGP) |
|Cisco | ASA | 適用於 9.8 版以上之 ASA 的 ASA RouteBased (IKEv2 - 沒有 BGP) |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * )必需：窄 Azure 流量選擇器（啟用基於使用策略的流量選擇選項）和自訂 Azure 策略（IKE/IPsec）
>
