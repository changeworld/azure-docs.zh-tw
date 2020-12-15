---
title: Azure ExpressRoute：設定 BFD
description: 本文提供如何透過 ExpressRoute 線路的私人對等互連設定 BFD (雙向轉送偵測) 的相關指示。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511258"
---
# <a name="configure-bfd-over-expressroute"></a>透過 ExpressRoute 設定 BFD

ExpressRoute 支援透過私人和 Microsoft 對等互連 (BFD) 雙向轉送偵測。 當您透過 ExpressRoute 啟用 BFD 時，您可以加速 Microsoft Enterprise edge (MSEE) 裝置和您的 ExpressRoute 線路設定 (CE/PE) 的路由器之間的連結失敗偵測。 如果您使用受控第3層聯機服務) ，您可以透過 edge 路由裝置或合作夥伴 Edge 路由裝置來設定 ExpressRoute (。 本文件將逐步引導您了解 BFD 的需求，以及如何透過 ExpressRoute 啟用 BFD。

## <a name="need-for-bfd"></a>需要 BFD

下圖顯示透過 ExpressRoute 路線啟用 BFD 的好處：[![1]][1]

您可以透過第 2 層連線或受控的第 3 層連線啟用 ExpressRoute 線路。 在這兩種情況下，如果 ExpressRoute 連線路徑中有一個以上的第2層裝置，則偵測路徑中任何連結失敗的責任會與過量 BGP 會話相關。

在 MSEE 裝置上，BGP keep-alive 和保留時間通常會分別設定為60和180秒。 基於這個原因，當連結失敗時，最多可能需要三分鐘的時間來偵測任何連結失敗，並將流量切換到其他連線。

您可以控制 BGP 計時器，方法是在 edge 對等互連裝置上設定較低的 BGP keep-alive 和保存時間。 如果兩個對等互連裝置之間的 BGP 計時器不同，則會使用較低的時間值來建立 BGP 會話。 BGP 保持運作的最小可設定為3秒，而保留時間最少可設定為10秒。 但是，不建議設定非常積極的 BGP 計時器，因為通訊協定需要大量處理。

在此案例中，BFD 可以提供協助。 BFD 會以次秒時間間隔提供低額外負荷的連結失敗偵測。 


## <a name="enabling-bfd"></a>啟用 BFD

BFD 預設會設定於 MSEE 上所有新建立的 ExpressRoute 私用對等互連介面上。 因此，若要啟用 BFD，您只需要在主要和次要裝置上設定 BFD。 設定 BFD 是兩個步驟的程式。 您可以在介面上設定 BFD，然後將它連結至 BGP 會話。

使用 Cisco IOS XE) 設定的範例 CE/PE (如下所示。 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>若要在已經存在的私用對等互連下方啟用 BFD，您需要重設該對等互連。 請參閱[重設 ExpressRoute 對等互連][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>BFD 計時器交涉

在 BFD 對等之間，這兩個對等中速度較慢者會決定傳輸速率。 MSEE BFD 傳輸/接收間隔會設定為 300 毫秒。 在某些情況下，間隔可能會設定為高於 750 毫秒的值。 藉由設定較高的值，您可以強制讓這些間隔更長，但無法使其變得較短。

>[!NOTE]
>如果您已設定異地冗余 ExpressRoute 線路，或使用站對站 IPSec VPN 連線作為備份。 啟用 BFD 有助於在 ExpressRoute 連線失敗之後更快進行容錯移轉。 
>

## <a name="next-steps"></a>後續步驟

如需詳細資訊或協助，請看看下列連結：

- [建立和修改 ExpressRoute 線路][CreateCircuit]
- [建立和修改 ExpressRoute 路線的路由][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD 會加速連結失敗推算時間"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md