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
ms.openlocfilehash: b2609a069872ec55ac9068fadcbb3f312d68a630
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680827"
---
本節介紹如何為 Direct 對等互連執行以下修改操作:

* 添加直接對等互連連接。
* 刪除直接對等互連連接。
* 升級或降級活動連接上的頻寬。
* 在活動連接上添加 IPv4 或 IPv6 會話。
* 在活動連接上刪除 IPv4 或 IPv6 會話。

### <a name="add-direct-peering-connections"></a>新增直接對等互連連線

此示例介紹如何向現有 Direct 對等互連添加連接。

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

### <a name="remove-direct-peering-connections"></a>刪除直接對等連結

PowerShell 目前不支援刪除連接。 有關詳細資訊,請與[微軟對等互連](mailto:peeringexperience@microsoft.com)。

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>在活動連線上升級或降級頻寬

此示例介紹如何向現有 Direct 連接添加 10 Gbps。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>新增 IPv4 或 IPv6 工作階段

此示例介紹如何在僅具有 IPv4 會話的現有 Direct 連接上添加 IPv6 會話。 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>在作用中移除 IPv4 或 IPv6 工作階段

PowerShell 目前不支援從現有連接中刪除 IPv4 或 IPv6 工作階段。 有關詳細資訊,請與[微軟對等互連](mailto:peeringexperience@microsoft.com)。