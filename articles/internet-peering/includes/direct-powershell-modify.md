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
ms.openlocfilehash: 03c67ccf88a8c73fe04f062c6af9520115c185a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774259"
---
本節介紹如何為 Direct 對等互連執行以下修改操作：

* 添加直接對等互連連接
* 刪除直接對等互連連接
* 升級或降級活動連接上的頻寬。
* 在活動連接上添加 IPv4/IPv6 會話。
* 在活動連接上刪除 IPv4/IPv6 會話。

### <a name="add-direct-peering-connections"></a>添加直接對等互連連接

下面的示例介紹如何向現有 Direct 對等互連添加連接

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringDirectConnection `
    -PeeringDBFacilityId $peeringLocation.PeeringDBFacilityId `
    -SessionPrefixV4 "10.22.31.0/31" `
    -SessionPrefixV6 "fe02::3e:0/127" `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000

$directPeering.Connections.Add($connection)

$directPeering | Update-AzPeering
```

### <a name="remove-direct-peering-connections"></a>刪除直接對等互連連接

PowerShell 當前不支援刪除連接。 聯繫[微軟對等互連](mailto:peeringexperience@microsoft.com)。

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>在活動連接上升級或降級頻寬

下面的示例介紹如何向現有直接連接添加 10Gbps。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4ipv6-session-on-active-connections"></a>在活動連接上添加 IPv4/IPv6 會話。

下面的示例介紹如何在僅與 IPv4 會話的現有直接連接上添加 IPv6 會話。 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4ipv6-session-on-active-connections"></a>在活動連接上刪除 IPv4/IPv6 會話。

PowerShell 目前不支援從現有連接中刪除 IPv4/IPv6 會話。 聯繫[微軟對等互連](mailto:peeringexperience@microsoft.com)。