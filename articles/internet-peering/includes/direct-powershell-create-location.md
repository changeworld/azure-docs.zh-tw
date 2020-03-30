---
title: 包含檔案
titleSuffix: Azure
description: 包含檔案
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774285"
---
PowerShell Cmdlet **Get-AzPeeringLocation**返回具有強制參數`Kind`的對等位置清單，您將在後面的步驟中使用該參數：

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接對等位置包含以下欄位：
* 對等位置 
* Country
* 對等資料庫設施Id
* 對等DB設施連結
* 頻寬提供

通過引用[對等互連資料庫](https://wwww.peeringdb.com)，驗證您是否在所需的對等設施中。

下面是一個示例，演示如何使用西雅圖作為對等位置來創建直接對等互連：

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```