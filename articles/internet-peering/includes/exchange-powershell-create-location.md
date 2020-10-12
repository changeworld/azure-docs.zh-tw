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
ms.openlocfilehash: fa8d8ccef7d6ad6e1b5d9f19de61e45ee8c439fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82587802"
---
PowerShell Cmdlet **AzPeeringLocation** 會傳回對等互連位置的清單，其中包含必要的參數 `Kind` ，您將在稍後的步驟中使用此參數。

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

交換對等互連位置包含下欄欄位：
* ExchangeName
* PeeringLocation
* 國家/地區
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

藉由參考 [PeeringDB](https://www.peeringdb.com)，驗證您是否存在於所需的對等互連設備上。

此範例示範如何使用西雅圖作為對等互連位置，以建立對等互連。

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
