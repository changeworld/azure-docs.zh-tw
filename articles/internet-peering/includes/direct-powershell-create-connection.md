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
ms.openlocfilehash: e1cb1bec6273fa79315b9439bec1412622ebfe28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774298"
---
下面的示例演示如何在西雅圖創建 10Gbps 直接對等互連。

使用 PowerShell Cmdlet**新-Az 對等互連直接連接物件**創建新的對等互連請求中使用的直接連接。

下面是創建直接連接的示例：

```powershell
$connection1 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.31.0/31 `
    -SessionPrefixV6 fe01::3e:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

> [!NOTE]
> <index>上例中$peeringLocation*<index>應對應于您選擇的對等位置

創建另一個連接，以防在給定對等位置需要冗余：

```powershell
$connection2 = New-AzPeeringDirectConnectionObject `
    -PeeringDBFacilityId $peeringLocation[0].PeeringDBFacilityId `
    -SessionPrefixV4 10.21.33.0/31 `
    -SessionPrefixV6 fe01::3f:0/127 `
    -MaxPrefixesAdvertisedIPv4 1000 `
    -MaxPrefixesAdvertisedIPv6 100 `
    -BandwidthInMbps 10000
```

使用 PowerShell Cmdlet**新阿茲對等**來創建新的直接對等互連。 此命令需要 ASN 資源識別碼，可以檢索如下所示。


```powershell
$asn = Get-AzPeerAsn
New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `
    -PeeringLocation  $peeringLocation[0].PeeringLocation `
    -PeerAsnResourceId $asn.Id `
    -DirectConnection $connection1 [, $connection2]
```
&nbsp;

下面是成功處理請求時的示例回應：

```powershell

    Name                 : SeattleDirectPeering
    Sku.Name             : Basic_Direct_Free
    Kind                 : Direct
    Connections          : 71
    PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/SeattleDirectPeering
    UseForPeeringService : False
    PeeringLocation      : Seattle
    ProvisioningState    : Succeeded
    Location             : centralus
    Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
    Type                 : Microsoft.Peering/peerings
    Tags                 : {}

```
請注意，為了代替上述輸出中的 **[訂閱 Id]，** 將顯示實際訂閱 ID。
