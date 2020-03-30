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
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774389"
---
PowerShell Cmdlet **Get-AzPeeringLocation**返回具有強制參數`Kind`的對等位置清單，您將在後面的步驟中使用該參數：

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

交換對等位置包含以下欄位：
* 交換名稱
* 對等位置
* Country
* 對等資料庫設施Id
* 對等DB設施連結
* 微軟IPv4位址
* 微軟IPv6位址

通過引用[對等互連資料庫](https://wwww.peeringdb.com)，驗證您是否在所需的對等設施中。

下面是一個示例，演示如何使用西雅圖作為對等位置來創建對等互連：

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```