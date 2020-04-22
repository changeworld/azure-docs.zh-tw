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
ms.openlocfilehash: 2ebf5d574b4d185953ab0f7984648c440d6f107e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678694"
---
PowerShell cmdlet **Get-AzPeeringLocation**傳`Kind`回具有強制參數的對等位置清單,您將在後面的步驟中使用該參數。

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

交換對等位置包含以下欄位:
* 交換名稱
* 對等位置
* Country
* 對等資料庫設施Id
* 對等DB設施連結
* 微軟IPv4位址
* 微軟IPv6位址

通過引用[對等互連資料庫](https://wwww.peeringdb.com),驗證您是否在所需的對等設施中。

此示例演示如何使用西雅圖作為對等位置來創建對等互連。

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