---
title: Azure 快速路由：重置電路對等互連
description: 如何停用和啟用 ExpressRoute 線路對等互連。
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/13/2018
ms.author: charwen
ms.openlocfilehash: 9f32eb439872de9e4687d046745c03bafd86b2fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941744"
---
# <a name="reset-expressroute-circuit-peerings"></a>重設 ExpressRoute 線路對等互連

本文說明如何使用 PowerShell 來停用和啟用 ExpressRoute 線路的對等互連。 當您停用對等互連時，位於 ExpressRoute 線路之主要連線與次要連線上的 BGP 工作階段將會關閉。 您將遺失透過此對等互連來連至 Microsoft 的連線。 當您啟用對等互連時，位於 ExpressRoute 線路之主要連線與次要連線上的 BGP 工作階段將會上線。 您將重新獲得透過此對等互連來連至 Microsoft 的連線。 您可以在 ExpressRoute 線路上單獨啟用和停用 Microsoft 對等互連和 Azure 私用對等互連。 當您第一次在 ExpressRoute 線路上設定對等互連時，預設會啟用對等互連。

有數個案例可能會讓您覺得重設 ExpressRoute 對等互連很實用。
* 測試您的災害復原設計和實作。 例如，您有兩個 ExpressRoute 線路。 您可以停用一個線路的對等互連，並強制將網路流量容錯移轉至另一個線路。
* 在 Azure 專用對等互連或 ExpressRoute 電路的 Microsoft 對等互連上啟用雙向轉發檢測 （BFD）。 如果 ExpressRoute 電路是在 2018 年 8 月 1 日之後創建的，則預設情況下在 Azure 專用對等互連上啟用 BFD;如果 2020 年 1 月 10 日之後創建了 ExpressRoute 電路，則在 Microsoft 對等互連上啟用 BFD。 如果您的線路是在那之前建立的，則不會啟用 BFD。 您可以藉由停用對等互連並重新啟用它來啟用 BFD。 

### <a name="working-with-azure-powershell"></a>使用 Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>重設對等互連

1. 如果您在本機執行 PowerShell，請以提高的權限開啟 PowerShell 主控台並連線至您的帳戶。 使用下列範例來協助您連接：

   ```azurepowershell
   Connect-AzAccount
   ```
2. 如果您有多個 Azure 訂用帳戶，請檢查帳戶的訂用帳戶。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. 指定您要使用的訂用帳戶。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. 執行下列命令來擷取您的 ExpressRoute 線路。

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. 識別您想要停用或啟用的對等互連。 「對等互連」** 是一個陣列。 在下列範例中，Peerings[0] 是 Azure 私用對等互連，而 Peerings[1] 是 Microsoft 對等互連。

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. 執行下列命令以變更對等互連的狀態。

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   對等互連應該處於您所設定的狀態。 

## <a name="next-steps"></a>後續步驟
如果您需要協助以進行 ExpressRoute 問題的疑難排解，請參閱下列文章：
* [驗證快速路由連接](expressroute-troubleshooting-expressroute-overview.md)
* [網路效能疑難排解](expressroute-troubleshooting-network-performance.md)
