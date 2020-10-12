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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81680803"
---
PowerShell Cmdlet **AzPeeringLocation** 會傳回對等互連位置的清單，其中包含必要的參數 `Kind` ，您將在稍後的步驟中使用此參數。

```powershell
Get-AzPeeringLocation -Kind Direct
```

直接對等互連位置包含下欄欄位：
* PeeringLocation 
* 國家/地區
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

藉由參考 [PeeringDB](https://wwww.peeringdb.com)，驗證您是否存在於所需的對等互連設備上。

此範例顯示如何使用西雅圖作為對等互連位置，以建立直接對等互連。

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