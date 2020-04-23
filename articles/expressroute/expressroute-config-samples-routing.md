---
title: Azure 快速路由:路由器設定範例
description: 此頁面提供適用於 Cisco 和 Juniper 路由器的路由器組態範例。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024807"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>設定和管理路由的路由器組態範例
本頁提供使用 Azure ExpressRoute 時 Cisco IOS-XE 和瞻美寶 MX 系列路由器的介面和路由配置示例。

> [!IMPORTANT]
> 本頁的示例僅用於指導。 您必須與供應商的銷售/技術團隊和網路團隊合作,以找到適當的配置以滿足您的需求。 Microsoft 不支援與本頁中列出的配置相關的問題。 有關支援問題,請與您的設備供應商聯繫。
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>路由器介面上的 MTU 和 TCP MSS 設定
ExpressRoute 介面的最大傳輸單元 (MTU) 為 1500,這是路由器上乙太網路介面的典型預設 MTU。 除非您的路由器預設有不同的 MTU，否則沒有必要指定路由器介面上的值。

與 Azure VPN 閘道不同,不需要指定 ExpressRoute 電路的 TCP 最大段大小 (MSS)。

本文中的路由器配置範例適用於所有對等互連。 如需路由的詳細資訊，請檢閱 [ExpressRoute 對等互連](expressroute-circuit-peerings.md)和 [ExpressRoute 路由需求](expressroute-routing.md)。


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE 架構的路由器
本節中的示例適用於運行 IOS-XE OS 系列的任何路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>設定介面與子介面
在連接到 Microsoft 的每個路由器中,每個對等互連都需要一個子介面。 子介面可以使用 VLAN ID 或堆疊的 VLAN ID 和對 IP 位址進行標識。

**Dot1Q 介面定義**

此示例提供具有單個 VLAN ID 的子介面的子介面定義。 在每個對等互連中 VLAN ID 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ 介面定義**

此示例提供具有兩個 VLAN ID 的子介面的子介面定義。 如果使用外部 VLAN ID (s 標記),則在所有對等互連中保持不變。 在每個對等互連中內部的 VLAN ID (c-tag) 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>設定 eBGP 工作階段
您必須為每個對等互連設置與 Microsoft 的 BGP 會話。 使用以下範例設置 BGP 會話。 如果您用於子介面的 IPv4 位址是 a.b.c.d,則 BGP 鄰居 (Microsoft) 的 IP 位址將為 a.b.c.d_1。 BGP 芳鄰的 IPv4 位址的最後一個八位元一定是偶數。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>設定要在 BGP 工作階段上播發的前置字串
使用以下範例將路由器配置為向 Microsoft 通告選擇首碼。

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
使用路由映射和首碼清單篩選傳播到網路的前置碼。 請參閱以下示例,並確保設置了相應的首碼清單。

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

您將在兩個位置配置 BFD:一個在介面級別配置,另一個在 BGP 級別配置。 此處的範例用於 QinQ 介面。 

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
本節中的示例適用於任何瞻比珀 MX 系列路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>設定介面與子介面

**Dot1Q 介面定義**

此示例提供具有單個 VLAN ID 的子介面的子介面定義。 在每個對等互連中 VLAN ID 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

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

此示例提供具有兩個 VLAN ID 的子介面的子介面定義。 如果使用外部 VLAN ID (s 標記),則在所有對等互連中保持不變。 在每個對等互連中內部的 VLAN ID (c-tag) 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

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

### <a name="set-up-ebgp-sessions"></a>設定 eBGP 工作階段
您必須為每個對等互連設置與 Microsoft 的 BGP 會話。 使用以下範例設置 BGP 會話。 如果您用於子介面的 IPv4 位址是 a.b.c.d,則 BGP 鄰居 (Microsoft) 的 IP 位址將為 a.b.c.d_1。 BGP 芳鄰的 IPv4 位址的最後一個八位元一定是偶數。

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>設定要在 BGP 工作階段上播發的前置字串
使用以下範例將路由器配置為向 Microsoft 通告選擇首碼。

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


### <a name="route-policies"></a>路由策略
您可以使用路由映射和前置清單來篩選傳播到網路中的前置碼。 請參閱以下示例,並確保設置了相應的首碼清單。

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
僅在協定 BGP 部分下配置 BFD。

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



