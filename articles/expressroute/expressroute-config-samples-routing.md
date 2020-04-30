---
title: Azure ExpressRoute：路由器設定範例
description: 此頁面提供適用於 Cisco 和 Juniper 路由器的路由器組態範例。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024807"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>設定和管理路由的路由器組態範例
當您使用 Azure ExpressRoute 時，此頁面提供 Cisco IOS-XE 和刺柏 MX 系列路由器的介面和路由設定範例。

> [!IMPORTANT]
> 本頁面上的範例純粹適用于指導方針。 您必須與廠商的銷售/技術團隊和網路小組合作，尋找適當的設定以符合您的需求。 Microsoft 不會支援此頁面中所列設定的相關問題。 請洽詢您的裝置廠商以取得支援問題。
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>路由器介面上的 MTU 和 TCP MSS 設定
ExpressRoute 介面的最大傳輸單位（MTU）為1500，這是路由器上 Ethernet 介面的典型預設 MTU。 除非您的路由器預設有不同的 MTU，否則沒有必要指定路由器介面上的值。

不同于 Azure VPN 閘道，不需要指定 ExpressRoute 線路的 TCP 最大區段大小（MSS）。

本文中的路由器設定範例適用于所有對等互連。 如需路由的詳細資訊，請檢閱 [ExpressRoute 對等互連](expressroute-circuit-peerings.md)和 [ExpressRoute 路由需求](expressroute-routing.md)。


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE 架構的路由器
本節中的範例適用于任何執行 IOS-XE OS 系列的路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>設定介面和子介面
在您連接到 Microsoft 的每個路由器中，每個對等互連都需要一個子介面。 子介面可以使用 VLAN ID 或堆疊配對的 VLAN 識別碼和 IP 位址來識別。

**Dot1Q 介面定義**

這個範例會針對具有單一 VLAN ID 的子介面提供子介面定義。 在每個對等互連中 VLAN ID 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ 介面定義**

這個範例會針對具有兩個 VLAN Id 的子介面提供子介面定義。 外部 VLAN ID （s-標記）（如果使用的話）在所有對等互連中保持不變。 在每個對等互連中內部的 VLAN ID (c-tag) 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>設定 eBGP 會話
您必須為每個對等互連設定與 Microsoft 的 BGP 會話。 使用下列範例來設定 BGP 會話。 如果您用於子介面的 IPv4 位址是. b. d，則 BGP 鄰居（Microsoft）的 IP 位址將會是. b. d + 1。 BGP 芳鄰的 IPv4 位址的最後一個八位元一定是偶數。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>設定要在 BGP 會話上公告的首碼
使用下列範例，將您的路由器設定為向 Microsoft 通告選取的首碼。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>路由對應
使用 [路由對應] 和 [首碼清單] 來篩選傳播到您網路的首碼。 請參閱下列範例，並確定您已設定適當的前置詞清單。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !

### <a name="configure-bfd"></a>設定 BFD

您會在兩個地方設定 BFD：一個在介面層級，另一個在 BGP 層級。 這裡的範例是針對 QinQ 介面。 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX 系列路由器
本章節中的範例適用于任何可為刺的 MX 系列路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>設定介面和子介面

**Dot1Q 介面定義**

這個範例會針對具有單一 VLAN ID 的子介面提供子介面定義。 在每個對等互連中 VLAN ID 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**QinQ 介面定義**

這個範例會針對具有兩個 VLAN Id 的子介面提供子介面定義。 外部 VLAN ID （s-標記）（如果使用的話）在所有對等互連中保持不變。 在每個對等互連中內部的 VLAN ID (c-tag) 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="set-up-ebgp-sessions"></a>設定 eBGP 會話
您必須為每個對等互連設定與 Microsoft 的 BGP 會話。 使用下列範例來設定 BGP 會話。 如果您用於子介面的 IPv4 位址是. b. d，則 BGP 鄰居（Microsoft）的 IP 位址將會是. b. d + 1。 BGP 芳鄰的 IPv4 位址的最後一個八位元一定是偶數。

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>設定要在 BGP 會話上公告的首碼
使用下列範例，將您的路由器設定為向 Microsoft 通告選取的首碼。

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="route-policies"></a>路由原則
您可以使用路由對應和前置詞清單來篩選傳播到您網路的首碼。 請參閱下列範例，並確定您已設定適當的前置詞清單。

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="configure-bfd"></a>設定 BFD
僅在 [通訊協定 BGP] 區段下設定 BFD。

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱〈 [ExpressRoute 常見問題集](expressroute-faqs.md) 〉。



