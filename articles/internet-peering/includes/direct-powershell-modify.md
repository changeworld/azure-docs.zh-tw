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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81680827"
---
本節說明如何針對直接對等互連執行下列修改作業：

* 新增直接對等互連連線。
* 移除直接對等互連連線。
* 升級或降級作用中連接的頻寬。
* 在使用中連接上新增 IPv4 或 IPv6 會話。
* 移除作用中連接上的 IPv4 或 IPv6 會話。

### <a name="add-direct-peering-connections"></a>新增直接對等互連連線

此範例說明如何將連接新增至現有的直接對等互連。

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

### <a name="remove-direct-peering-connections"></a>移除直接對等互連連線

目前 PowerShell 不支援移除連接。 如需詳細資訊，請聯絡[Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。

<!--
```powershell
$directPeering.Connections.Remove($directPeering.Connections[0])

$directPeering | Update-AzPeering
```
-->

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>升級或降級作用中連接的頻寬

此範例說明如何將 10 Gbps 新增至現有的直接連接。

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BandwidthInMbps  = 20000
$directPeering | Update-AzPeering

```

### <a name="add-ipv4-or-ipv6-sessions-on-active-connections"></a>在使用中連接上新增 IPv4 或 IPv6 會話

此範例說明如何在只有 IPv4 會話的現有直接連線上新增 IPv6 會話。 

```powershell

$directPeering = Get-AzPeering -Name "SeattleDirectPeering" -ResourceGroupName "PeeringResourceGroup"
$directPeering.Connections[0].BGPSession.SessionPrefixv6 = "fe01::3e:0/127"
$directPeering | Update-AzPeering

```

### <a name="remove-ipv4-or-ipv6-sessions-on-active-connections"></a>移除作用中連接的 IPv4 或 IPv6 會話

目前 PowerShell 不支援從現有的連線移除 IPv4 或 IPv6 會話。 如需詳細資訊，請聯絡[Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。