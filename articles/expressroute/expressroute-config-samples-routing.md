---
title: Azure ExpressRoute：路由器組態範例
description: 使用這些介面和路由設定範例，將 Cisco IOS-XE 和刺柏 MX 系列路由器當做使用 Azure ExpressRoute 的範例。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: fdf50dc6b4c0976a713a1d436ac9af7c1bd9701b
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190784"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>設定和管理路由的路由器組態範例
此頁面提供在使用 Azure ExpressRoute 時，適用於 Cisco IOS XE 和 Juniper MX 系列路由器的介面和路由組態範例。

> [!IMPORTANT]
> 本頁面的範例僅供指導之用。 請務必和廠商的業務人員/技術小組及您的網路團隊合作，一起找出符合需求的合適組態。 Microsoft 不支援此頁面所列組態的相關問題， 請連絡您的裝置廠商協助解決問題。
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>路由器介面上的 MTU 和 TCP MSS 設定
ExpressRoute 介面的傳輸單元最大值 (MTU) 是 1500，這是路由器上乙太網路介面的典型預設 MTU。 除非您的路由器預設有不同的 MTU，否則沒有必要指定路由器介面上的值。

不同於 Azure VPN 網路閘道，不需要指定 ExpressRoute 線路的 TCP 最大區段大小 (MSS)。

本文中的路由器組態範例適用於所有對等互連。 如需路由的詳細資訊，請檢閱 [ExpressRoute 對等互連](expressroute-circuit-peerings.md)和 [ExpressRoute 路由需求](expressroute-routing.md)。


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE 架構的路由器
本節中的範例適用於任何執行 IOS-XE 作業系統系列的路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>設定介面和子介面
在您連線到 Microsoft 的每個路由器中，每個對等互連都需要有一個子介面。 子介面可使用 VLAN ID 或一組堆疊的 VLAN ID 和 IP 位址來識別。

**Dot1Q 介面定義**

此範例會提供子介面定義給具有單一 VLAN ID 的子介面。 在每個對等互連中 VLAN ID 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**QinQ 介面定義**

此範例會提供子介面定義給具有兩個 VLAN ID 的子介面。 外部的 VLAN ID (s-tag) 若有使用，則會在所有對等互連上之間維持不變。 在每個對等互連中內部的 VLAN ID (c-tag) 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>設定 eBGP 工作階段
您必須設定每個對等互連與 Microsoft 的 BGP 工作階段。 請使用以下範例設定 BGP 工作階段。 如果您對子介面使用的 IPv4 位址是 a.b.c.d，則 BGP 芳鄰 (Microsoft) 的 IP 位址會是 a.b.c.d+1。 BGP 芳鄰的 IPv4 位址的最後一個八位元一定是偶數。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>將前置詞設定為透過 BGP 工作階段公告
請使用下列範例，設定路由器將選取的前置詞公告給 Microsoft。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>路由對應
請使用路由對應和前置詞清單，篩選要傳播到您網路中的前置詞。 請參閱下列範例，並確定已設定適當的前置詞清單。

```console
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
```

### <a name="configure-bfd"></a>設定 BFD

您會在兩處設定 BFD：一個在介面層級，另一個在 BGP 層級。 這裡的範例適用於 QinQ 介面。 

```console
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
```


## <a name="juniper-mx-series-routers"></a>Juniper MX 系列路由器
本節中的範例適用於所有的 Juniper MX 系列路由器。

### <a name="configure-interfaces-and-subinterfaces"></a>設定介面和子介面

**Dot1Q 介面定義**

此範例會提供子介面定義給具有單一 VLAN ID 的子介面。 在每個對等互連中 VLAN ID 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

```console
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
```


**QinQ 介面定義**

此範例會提供子介面定義給具有兩個 VLAN ID 的子介面。 外部的 VLAN ID (s-tag) 若有使用，則會在所有對等互連上之間維持不變。 在每個對等互連中內部的 VLAN ID (c-tag) 都是唯一的。 IPv4 位址的最後一個八位元一定是奇數。

```console
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
```

### <a name="set-up-ebgp-sessions"></a>設定 eBGP 工作階段
您必須設定每個對等互連與 Microsoft 的 BGP 工作階段。 請使用以下範例設定 BGP 工作階段。 如果您對子介面使用的 IPv4 位址是 a.b.c.d，則 BGP 芳鄰 (Microsoft) 的 IP 位址會是 a.b.c.d+1。 BGP 芳鄰的 IPv4 位址的最後一個八位元一定是偶數。

```console
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
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>將前置詞設定為透過 BGP 工作階段公告
請使用下列範例，設定路由器將選取的前置詞公告給 Microsoft。

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
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
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>路由原則
您可以使用路由對應和前置詞清單，來篩選要傳播到您網路中的前置詞。 請參閱下列範例，並確定已設定適當的前置詞清單。

```console
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
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>設定 BFD
僅設定屬於通訊協定 BGP 區段的 BFD。

```console
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
```

### <a name="configure-macsec"></a>設定 MACSec
針對 MACSec 設定，連線關聯金鑰 (CAK) 和連線關聯金鑰名稱 (CKN) 必須與透過 PowerShell 命令設定的值相符。

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>後續步驟
如需詳細資訊，請參閱〈 [ExpressRoute 常見問題集](expressroute-faqs.md) 〉。



