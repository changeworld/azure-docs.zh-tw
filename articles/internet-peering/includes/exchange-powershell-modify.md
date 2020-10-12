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
ms.openlocfilehash: 12d169697a35af446392843eb57e6ec3a5508e45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678611"
---
Exchange 對等互連支援下列修改作業：
* 新增 Exchange 對等互連連線。
* 移除 Exchange 對等互連連線。
* 在使用中的連接上新增 IPv4 或 IPv6 會話。
* 移除作用中連接上的 IPv4 或 IPv6 會話。


### <a name="add-exchange-peering-connections"></a>新增 Exchange 對等互連連接

此範例說明如何將連接新增至現有的 Exchange 對等互連。

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$connection = New-AzPeeringExchangeConnectionObject `
    -PeeringDBFacilityId $exchangeLocation[1].PeeringDBFacilityId `
    -PeerSessionIPv4Address 198.32.134.22 `
    -PeerSessionIPv6Address  2001:504:12::22 `
    -MaxPrefixesAdvertisedIPv4 2000 `
    -MaxPrefixesAdvertisedIPv6 2000 `

$exchangePeering.Connections.Add($connection)

$exchangePeering | Update-AzPeering

```

### <a name="remove-exchange-peering-connections"></a>移除 Exchange 對等互連連線

此範例說明如何移除現有 Exchange 對等互連的連接。

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

```

查看所有連接，然後選取您想要移除的連接。 

```powershell

$exchangePeering

Name              : SeattleExchangePeering
Sku.Name          : Basic_Exchange_Free
Kind              : Exchange
Connections       : {11}
PeerAsn.Id        : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{peerAsnName}
PeeringLocation   : Seattle
ProvisioningState : Succeeded
Location          : West US 2
Id                : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleExchangePeering
Type              : Microsoft.Peering/peerings
Tags              : {}

```

在下列命令（而不是0）中，輸入您想要移除之連接的索引編號。

```powershell

$connection = $exchangePeering.Connections[0]

$exchangePeering.Connections.Remove($connection)

$exchangePeering | Update-AzPeering

```

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>在作用中連接上新增 IPv4 或 IPv6 會話

此範例說明如何將 IPv6 會話新增至現有的 Exchange 連接。

```powershell

$exchangePeering = Get-AzPeering -Name "SeattleExchangePeering" -ResourceGroupName "PeeringResourceGroup"

$exchangePeering.Connections[0].BgpSession.PeerSessionIPv6Address = "2001:504:12::34"

$exchangePeering | Update-AzPeering

```

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>移除主動連線上的 IPv4 或 IPv6 會話

PowerShell 目前不支援從現有連線移除 IPv4 或 IPv6 會話。 如需詳細資訊，請洽詢 [Microsoft 對等互連](mailto:peeringexperience@microsoft.com)。