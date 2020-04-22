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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680803"
---
PowerShell cmdlet **Get-AzPeeringLocation**傳`Kind`回具有強制參數的對等位置清單,您將在後面的步驟中使用該參數。

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接對等位置包含以下欄位:
* 對等位置 
* Country
* 對等資料庫設施Id
* 對等DB設施連結
* 頻寬提供

通過引用[對等互連](https://wwww.peeringdb.com)資料庫,驗證您是否在所需的對等互連設施中。

此示例演示如何使用西雅圖作為對等位置來創建直接對等互連。

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