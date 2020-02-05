---
title: Azure VMware 解決方案（AVS）-將第2層網路的內部部署延展到 AVS 私人雲端
description: 說明如何在 AVS 私用雲端和內部部署獨立 NSX Edge 用戶端上的 NSX-T 之間設定第2層 VPN
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 975ffcd7142aac24363c2235db3742c155c1007b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019820"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>使用第 2 層延伸網路遷移工作負載

在本指南中，您將瞭解如何使用第2層 VPN （L2VPN），將第2層網路從內部部署環境延展至您的 AVS 私用雲端。 此解決方案可讓您將內部部署 VMware 環境中執行的工作負載，遷移到相同子網位址空間內的 Azure 中的 AVS 私人雲端，而不需要重新建立您的工作負載的 IP。

以 L2VPN 為基礎的第2層網路延展可以在您的內部部署 VMware 環境中，使用或不含以 NSX 為基礎的網路。 如果您沒有適用于內部部署工作負載的以 NSX 為基礎的網路，您可以使用獨立的 NSX Edge 服務閘道。

> [!NOTE]
> 本指南涵蓋內部部署和 AVS 私用雲端資料中心透過站對站 VPN 連線的案例。

## <a name="deployment-scenario"></a>部署案例

若要使用 L2VPN 延展您的內部部署網路，您必須設定 L2VPN 伺服器（目的地 NSX-T Tier0 路由器）和 L2VPN 用戶端（來源獨立用戶端）。 

在此部署案例中，您的 AVS 私人雲端會透過站對站 VPN 通道連線到您的內部部署環境，讓內部部署管理和 vMotion 子網與 AVS 私用雲端管理和 vMotion 子網進行通訊。 跨 vCenter vMotion （xVC-vMotion）必須進行這種安排。 Tier0 路由器會部署為 AVS 私人雲端中的 L2VPN 伺服器。

獨立的 NSX Edge 會部署在您的內部部署環境中做為 L2VPN 用戶端，然後再與 L2VPN 伺服器配對。 GRE 通道端點會在每一端建立，並設定為將內部部署層2網路「延展」至您的 AVS 私用雲端。 下圖說明此設定。

![部署案例](media/l2vpn-deployment-scenario.png)

若要深入瞭解如何使用 L2 VPN 進行遷移，請參閱 VMware 檔中的[虛擬私人網路](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)。

## <a name="prerequisites-for-deploying-the-solution"></a>部署解決方案的必要條件

在部署和設定解決方案之前，請先確認下列事項已準備就緒：

* 內部部署 vSphere 版本為 6.7 U1 + 或 6.5 P03 +。
* 內部部署 vSphere 授權位於企業 Plus 層級（適用于 vSphere 分散式交換器）。
* 識別要延展至您的 AVS 私人雲端的工作負載層2網路。
* 在您的內部部署環境中識別第2層網路，以部署您的 L2VPN 用戶端應用裝置。
* [已建立 AVS 私用雲端](create-private-cloud.md)。
* 獨立的 NSX-T Edge 設備版本與您的 AVS 私用雲端環境中使用的 NSX-T Manager 版本（NSX-T 2.3.0）相容。
* 已在內部部署 vCenter 中建立已啟用偽造傳輸的主幹埠群組。
* 公用 IP 位址已保留供 NSX-T 獨立用戶端上行 IP 位址使用，而 1:1 NAT 已準備好用於兩個位址之間的轉譯。
* DNS 轉送是在 az 的內部部署 DNS 伺服器上設定的。AVS.io 網域，指向 AVS 私人雲端 DNS 伺服器。
* RTT 延遲小於或等於150毫秒，因為 vMotion 必須在兩個網站上運作。

## <a name="limitations-and-considerations"></a>限制與注意事項

下表列出支援的 vSphere 版本和網路介面卡類型。 

| vSphere 版本 | 來源 vSwitch 類型 | 虛擬 NIC 驅動程式 | 目標 vSwitch 類型 | 支援? |
------------ | ------------- | ------------ | ------------- | ------------- 
| 所有 | DVS | 所有 | DVS | 是 |
| vSphere 6.7 UI 或更高版本、6.5 P03 或更高版本 | DVS | VMXNET3 | N-VDS | 是 |
| vSphere 6.7 UI 或更高版本、6.5 P03 或更高版本 | DVS | E1000 | N-VDS | [每個 VWware 不支援](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7 UI 或 6.5 P03、NSX-V 或版本低於 NSX-T 2.2、6.5 P03 或更高版本 | 所有 | 所有 | N-VDS | [每個 VWware 不支援](https://kb.vmware.com/s/article/56991) |

從 VMware NSX-T 2.3 版本：

* 在 AVS 私人雲端端，透過 L2VPN 延展到內部部署的邏輯交換器，無法同時路由傳送。 延伸的邏輯交換器無法連線到邏輯路由器。
* L2VPN 和路由式 IPSEC Vpn 只能使用 API 呼叫來設定。

如需詳細資訊，請參閱 VMware 檔中的[虛擬私人網路](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)。

### <a name="sample-l2-vpn-deployment-addressing"></a>L2 VPN 部署定址範例

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>部署獨立 ESG （L2 VPN 用戶端）的內部部署網路

| **Item** | **ReplTest1** |
|------------|-----------------|
| 網路名稱 | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| 獨立 Edge 應用裝置 IP 位址 | 10.250.0.111 |
| 獨立 Edge 應用裝置 NAT IP 位址 | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>要伸展的內部部署網路

| **Item** | **ReplTest1** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="avs-private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>適用于 NSX-T Tier0 路由器的 AVS 私人雲端 IP 架構（L2 VPN 服務）

| **Item** | **ReplTest1** |
|------------|-----------------|
| 回送介面 | 192.168.254.254/32 |
| 通道介面 | 5.5.5.1/29 |
| 邏輯交換器（已延展） | Stretch_LS |
| 回送介面（NAT IP 位址） | 104.40.21.81 |

### <a name="avs-private-cloud-network-to-be-mapped-to-the-stretched-network"></a>要對應至延伸網路的 AVS 私人雲端網路

| **Item** | **ReplTest1** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>提取 L2VPN 所需的邏輯路由器識別碼

下列步驟顯示如何針對 IPsec 和 L2VPN 服務，提取 Tier0 DR 邏輯路由器實例的邏輯路由器識別碼。 稍後在執行 L2VPN 時，需要邏輯路由器識別碼。

1. 登入 NSX *-t manager HTTPs://，* 並選取**網路** > **路由器** > **提供者-LR** > **總覽**。 針對 [**高可用性模式]** ，選取 [作用中 **-待命**]。 此動作會開啟一個快顯視窗，其中顯示 Tier0 路由器目前作用中的邊緣 VM。

    ![選取作用中-待命](media/l2vpn-fetch01.png)

2. 選取 網狀**架構** > **節點** > **邊緣**。 記下上一個步驟中所識別之作用中邊緣 VM （Edge VM1）的管理 IP 位址。

    ![注意管理 IP](media/l2vpn-fetch02.png)

3. 開啟邊緣 VM 管理 IP 位址的 SSH 會話。 以使用者名稱**管理員**和密碼**AVS 123！** 執行 ```get logical-router``` 命令。

    ![取得邏輯路由器輸出](media/l2vpn-fetch03.png)

4. 如果您沒有看到專案「DR-提供者-LR」，請完成下列步驟。

5. 建立兩個支援重迭的邏輯交換器。 其中一個邏輯交換器會延伸至已遷移工作負載所在的內部部署。 另一個邏輯交換器是虛擬交換器。 如需指示，請參閱 VMware 檔中的[建立邏輯交換器](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html)。

    ![建立邏輯交換器](media/l2vpn-fetch04.png)

6. 將虛擬交換器附加至第1層路由器，其中包含來自內部部署或您的 AVS 私人雲端的連結本機 IP 位址或任何非重迭的子網。 請參閱 VMware 檔中的在[第1層邏輯路由器上新增下行通訊埠](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html)。

    ![附加虛擬交換器](media/l2vpn-fetch05.png)

7. 在 Edge VM 的 SSH 會話上，再次執行 `get logical-router` 命令。 隨即顯示「DR-提供者-LR」邏輯路由器的 UUID。 記下 UUID，這是設定 L2VPN 時的必要項。

    ![取得邏輯路由器輸出](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>提取 L2VPN 所需的邏輯交換器識別碼

1. 登入 [ [NSX-T 管理員](https://nsx-t-manager-ip-address)]。
2. 選取**網路** > **切換** > **交換器** >  **< \Logical 交換器\>**  > **總覽**。
3. 記下延展邏輯交換器的 UUID，這是設定 L2VPN 時的必要項。

    ![取得邏輯路由器輸出](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>L2VPN 的路由和安全性考慮

若要在 NSX-T Tier0 路由器和獨立的 NSX Edge 用戶端之間建立以 IPsec 路由為基礎的 VPN，Tier0 路由器的回送介面必須能夠與內部部署的 NSX 獨立用戶端公用 IP 位址，透過 UDP 500/4500 進行通訊。

### <a name="allow-udp-5004500-for-ipsec"></a>允許 IPsec 的 UDP 500/4500

1. 在 AVS 入口網站中建立 Tier0 回送介面的[公用 IP 位址](public-ips.md)。

2. 建立具有可設定狀態規則的[防火牆資料表](firewall.md)，允許 UDP 500/4500 輸入流量，並將防火牆資料表附加到 HostTransport 子網。

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>將回送介面 IP 公告至 underlay 網路

1. 為回送介面網路建立 null 路由。 登入 NSX-T Manager，並選取**網路** > **路由** > **路由器** > **提供者-LR** > **路由** > **靜態路由**。 按一下 [新增]。 針對 [**網路**]，輸入回送介面的 IP 位址。 針對 [**下一個躍點]** ，按一下 [**新增**]，為下一個躍點指定 [Null]，並保留預設值1來管理距離。

    ![新增靜態路由](media/l2vpn-routing-security01.png)

2. 建立 IP 首碼清單。 登入 NSX-T Manager，然後選取 **網路** > **路由** > **路由器** > **提供者-LR** > **路由** > **IP 首碼清單**。 按一下 [新增]。 輸入名稱以識別清單。 針對**前置詞，請按一下**[**新增**兩次]。 在第一行中，輸入「0.0.0.0/0」作為 [**網路**] 和 [拒絕] 以進行**動作**。 在第二行中，為 [**網路**] 選取 [**任何**]，並**允許**[**動作**]。
3. 將 IP 首碼清單附加至這兩個 BGP 鄰近專案（TOR）。 將 IP 首碼清單附加至 BGP 鄰居，可防止在 BGP 中將預設路由公告至 TOR 交換器。 不過，任何包含 null 路由的其他路由都會向 TOR 交換器通告回送介面的 IP 位址。

    ![建立 IP 首碼清單](media/l2vpn-routing-security02.png)

4. 登入 [NSX-T Manager]，然後選取 [**網路** > **路由** > **路由器** > **提供者-LR** > **路由** > **BGP** > **鄰近**專案]。 選取第一個鄰近的節點。 按一下 [**編輯**] > **位址系列**。 針對 IPv4 系列，編輯**輸出篩選器**資料行，然後選取您所建立的 IP 首碼清單。 按一下 [檔案]。 針對第二個鄰近節點重複此步驟。

    ![附加 IP 首碼清單 1](media/l2vpn-routing-security03.png) ![附加 IP 首碼清單 2](media/l2vpn-routing-security04.png)

5. 將 null 靜態路由轉散發至 BGP。 若要將回送介面路由公告至 underlay，您必須將 null 靜態路由轉散發至 BGP。 登入 NSX-T Manager，並選取**網路** > **路由** > **路由器** > **提供者-LR** > **路由** > **路由**轉散發 > **鄰居**。 選取 [**提供者-LR-Route_Redistribution** ]，然後按一下 [**編輯**]。 選取 [**靜態**] 核取方塊，然後按一下 [**儲存**]。

    ![將 null 靜態路由轉散發至 BGP](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>在 NSX-T Tier0 路由器上設定以路由為基礎的 VPN

使用下列範本來填入在 NSX-T Tier0 路由器上設定路由式 VPN 的所有詳細資料。 後續的 POST 呼叫中需要每個 POST 呼叫中的 Uuid。 L2VPN 的回送和通道介面的 IP 位址必須是唯一的，而且不會與內部部署或 AVS 私人雲端網路重迭。

為用於 L2VPN 的回送和通道介面選擇的 IP 位址必須是唯一的，而且不會與內部部署或 AVS 私人雲端網路重迭。 回送介面網路必須一律為/32。

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section “Steps to fetch Logical-Router ID needed for L2VPN”
Logical-switch ID(Stretch) : UUID of Stretch Logical Switch obtained earlier
IPSec Service ID :
IKE profile ID :
DPD profile ID :
Tunnel Profile ID :
Local-endpoint ID :
Peer end-point ID :
IPSec VPN session ID (route-based) :
L2VPN service ID :
L2VPN session ID :
Logical-Port ID :
Peer Code :
```

針對下列所有的 API 呼叫，將 IP 位址取代為您的 NSX-T 管理員 IP 位址。 您可以從 POSTMAN 用戶端或使用 `curl` 命令來執行所有的 API 呼叫。

### <a name="enable-the-ipsec-vpn-service-on-the-logical-router"></a>在邏輯路由器上啟用 IPSec VPN 服務

```
POST   https://192.168.110.201/api/v1/vpn/ipsec/services/
{
"resource_type": "IPSecVPNService",
"description": "Manage VPN service",
"display_name": "IPSec VPN service",
"logical_router_id": "Logical-router ID",
"ike_log_level": "INFO",
"enabled": true
}
```

### <a name="create-profiles-ike"></a>建立設定檔： IKE

```
POST https://192.168.110.201/api/v1/vpn/ipsec/ike-profiles
 
{
"resource_type": "IPSecVPNIKEProfile",
"description": "IKEProfile for siteA",
"display_name": "IKEProfile siteA",
"encryption_algorithms": ["AES_128"],
"ike_version": "IKE_V2",
"digest_algorithms": ["SHA2_256"],
"sa_life_time":21600,
"dh_groups": ["GROUP14"]
}
```

### <a name="create-profiles-dpd"></a>建立設定檔： DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>建立設定檔：通道

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/tunnel-profiles
 
{
"resource_type": "IPSecVPNTunnelProfile",
"display_name": "nsx-default-tunnel-profile",
"enable_perfect_forward_secrecy": true,
"encryption_algorithms": ["AES_GCM_128"],
"digest_algorithms": [],
"sa_life_time":3600,
"dh_groups": ["GROUP14"],
"encapsulation_mode": "TUNNEL_MODE",
"transform_protocol": "ESP",
"df_policy": "COPY"
}
```

### <a name="create-a-local-endpoint"></a>建立本機端點

``` 
POST https://192.168.110.201/api/v1/vpn/ipsec/local-endpoints
 
{
"resource_type": "IPSecVPNLocalEndpoint",
"description": "Local endpoint",
"display_name": "Local endpoint",
"local_id": "<Public IP of Loopback interface>",
"ipsec_vpn_service_id": {
"target_id": "IPSec VPN service ID"},
"local_address": "<IP of Loopback interface>",
"trust_ca_ids": [],
"trust_crl_ids": []
}
```

### <a name="create-a-peer-endpoint"></a>建立對等端點

```
POST https://192.168.110.201/api/v1/vpn/ipsec/peer-endpoints

{
"resource_type": "IPSecVPNPeerEndpoint",
"description": "Peer endpoint for site B",
"display_name": "Peer endpoint for site B",
"connection_initiation_mode": "INITIATOR",
"authentication_mode": "PSK",
"ipsec_tunnel_profile_id": "IPSec Tunnel profile ID",
"dpd_profile_id": "DPD profile ID",
"psk":"nsx",
"ike_profile_id": "IKE profile ID",
"peer_address": "<Public IP of Standalone client",
"peer_id": "<Public IP of Standalone client>"
}
```

### <a name="create-a-route-based-vpn-session"></a>建立以路由為基礎的 VPN 會話

```
POST :  https://192.168.110.201/api/v1/vpn/ipsec/sessions
 
{
"resource_type": "RouteBasedIPSecVPNSession",
"peer_endpoint_id": "Peer Endpoint ID",
"ipsec_vpn_service_id": "IPSec VPN service ID",
"local_endpoint_id": "Local Endpoint ID",
"enabled": true,
"tunnel_ports": [
{
"ip_subnets": [
{
"ip_addresses": [
 "5.5.5.1"
],
"prefix_length": 24
}
  ]
}
]
}
```

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>在 NSX-T Tier0 路由器上設定 L2VPN

請在每次 POST 呼叫之後填入下列資訊。 後續的 POST 呼叫中需要識別碼。

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>建立 L2VPN 服務

下列 GET 命令的輸出會是空白，因為設定尚未完成。

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

在下列 POST 命令中，邏輯路由器識別碼是稍早取得之 Tier0 DR 邏輯路由器的 UUID。

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>建立 L2VPN 會話

針對下列 POST 命令，L2VPN 服務識別碼是您剛取得的識別碼，而 IPsec VPN 會話識別碼是在上一節中取得的識別碼。

``` 
POST: https://192.168.110.201/api/v1/vpn/l2vpn/sessions

{
"l2vpn_service_id" : "L2VPN service ID",
"transport_tunnels" : [
    {
    "target_id" : "IPSec VPN session ID"
    }]
}
```

這些呼叫會建立 GRE 通道端點。 若要檢查狀態，請執行下列命令。

```
edge-2> get tunnel-port
Tunnel      : 44648dae-8566-5bc9-a065-b1c4e5c3e03f
IFUID       : 328
LOCAL       : 169.254.64.1
REMOTE      : 169.254.64.2
ENCAP       : GRE

Tunnel      : cf950ca1-5cf8-5438-9b1a-d2c8c8e7229b
IFUID       : 318
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.152
ENCAP       : GENEVE

Tunnel      : 63639321-87c5-529e-8a61-92c1939799b2
IFUID       : 304
LOCAL       : 192.168.140.155
REMOTE      : 192.168.140.156
ENCAP       : GENEVE
```

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>建立具有指定之通道識別碼的邏輯埠

```
    POST https://192.168.110.201/api/v1/logical-ports/

{
"resource_type": "LogicalPort",
"display_name": "Extend logicalSwitch, port for service",
"logical_switch_id": "Logical switch ID",
"admin_state" : "UP",
"attachment": {
"attachment_type":"L2VPN_SESSION",
"id":"L2VPN session ID",
"context" : {
"resource_type" : "L2VpnAttachmentContext",
    "tunnel_id" : 10
}
    }
        }
```

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>在 NSX-T 端取得 L2VPN 的對等程式碼

取得 NSX-T 端點的對等程式碼。 設定遠端端點時，需要對等程式碼。 您可以從上一節取得 L2VPN < 會話識別碼 >。 如需詳細資訊，請參閱《 [NSX-T 2.3 API 指南》](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)。

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>部署 NSX-T 獨立用戶端（內部部署）

在部署之前，請確認您的內部部署防火牆規則允許輸入和輸出 UDP 500/4500 流量從/傳送到先前針對 NSX-T T0 路由器回送介面所保留的 AVS 公用 IP 位址。 

1. [下載獨立的 NSX Edge 用戶端](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230)從下載的配套 OVF 檔案，並將檔案解壓縮至資料夾。

    ![下載獨立的 NSX Edge 用戶端](media/l2vpn-deploy-client01.png)

2. 移至含有所有已解壓縮檔案的資料夾。 針對大型設備大小或 NSX-l2t-NSX-l2t-client-large-m3.xlarge-ovf 和 NSX-l2t-client-Xlarge. ovf，選取所有的 vmdk （l2t-client-大型. mf 和）。 按 [下一步]。

    ![選取範本](media/l2vpn-deploy-client02.png) ![選取範本](media/l2vpn-deploy-client03.png)

3. 輸入 NSX-T 獨立用戶端的名稱，然後按 **[下一步]** 。

    ![輸入範本名稱](media/l2vpn-deploy-client04.png)

4. 視需要按一下 **[下一步]** 以到達資料存放區設定。 為 NSX-T 獨立用戶端選取適當的資料存放區，然後按 **[下一步]** 。

    ![選取資料存放區](media/l2vpn-deploy-client06.png)

5. 針對 NSX-T 獨立用戶端選取正確的 [主幹（主幹 PG）]、[公用（上行 PG）] 和 [HA 介面（上行 PG）] 通訊埠群組。 按 [下一步]。

    ![選取埠群組](media/l2vpn-deploy-client07.png)

6. 在 [**自訂範本**] 畫面中填入下列詳細資料，然後按 **[下一步]** ：

    展開 L2T：

    * **對等位址**。 輸入在 Azure AVS 入口網站上為 Tier0 回送介面保留的 IP 位址。
    * **對等程式碼**。 貼上從 L2VPN 伺服器部署的最後一個步驟取得的對等程式碼。
    * **子介面 VLAN （通道識別碼）** 。 輸入要伸展的 VLAN ID。 在 [括弧（）] 中，輸入先前設定的通道識別碼。

    展開上行介面：

    * **DNS IP 位址**。 輸入內部部署的 DNS IP 位址。
    * **預設閘道**。 輸入將作為此用戶端預設閘道的 VLAN 預設閘道。
    * **IP 位址**。 輸入獨立用戶端的上行 IP 位址。
    * **前置長度**。 輸入上行 VLAN/子網的前置長度。
    * **CLI 系統管理員/啟用/根使用者密碼**。 設定 admin/enable/root 帳戶的密碼。

      ![自訂範本](media/l2vpn-deploy-client08.png)
      ![自訂範本-其他](media/l2vpn-deploy-client09.png)

7. 檢查設定，然後按一下 **[完成]** 。

    ![完成設定](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>設定內部部署接收埠

如果其中一個 VPN 網站未部署 NSX，您可以在該網站上部署獨立的 NSX 邊緣來設定 L2 VPN。 獨立的 NSX Edge 會使用主機上的 OVF 檔案來部署，此檔案不受 NSX 管理。 這會部署一個 NSX Edge 服務閘道設備，做為 L2 VPN 用戶端。

如果獨立邊緣主幹 vNIC 連線到 vSphere 分散式交換器，則必須使用混合模式或接收埠，才能進行 L2 VPN 功能。 使用混雜模式可能會導致重複的 ping 和重複的回應。 基於這個理由，請使用 L2 VPN 獨立 NSX Edge 設定中的接收埠模式。 請參閱 VMware 檔中的[設定接收埠](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html)。

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN 和 L2VPN 驗證

使用下列命令，從獨立的 NSX-T Edge 驗證 IPsec 和 L2VPN 會話。

```
nsx-l2t-edge> show service ipsec
-----------------------------------------------------------------------
vShield Edge IPSec Service Status:
IPSec Server is running.
AESNI is enabled.
Total Sites: 1, 1 UP, 0 Down
Total Tunnels: 1, 1 UP, 0 Down
----------------------------------
Site:  10.250.0.111_0.0.0.0/0-104.40.21.81_0.0.0.0/0
Channel: PeerIp: 104.40.21.81    LocalIP: 10.250.0.111  Version: IKEv2  Status: UP
Tunnel: PeerSubnet: 0.0.0.0/0    LocalSubnet: 0.0.0.0/0   Status: UP
----------------------------------
```

```
nsx-l2t-edge> show service l2vpn
L2 VPN is running
----------------------------------------
L2 VPN type: Client/Spoke

SITENAME                       IPSECSTATUS          VTI                  GRE
1ecb00fb-a538-4740-b788-c9049e8cb6c6 UP                   vti-100              l2t-1
```

使用下列命令來驗證來自 Tier0 路由器的 IPsec 和 L2VPN 會話。

```
edge-2> get ipsecvpn session
Total Number of Sessions: 1

IKE Session ID : 3
UUID           : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Type           : Route

Local IP       : 192.168.254.254      Peer IP        : 192.227.85.167
Local ID       : 104.40.21.81         Peer ID        : 192.227.85.167
Session Status : Up

Policy Rules
    VTI UUID       : 4bf96e3b-e50b-49cc-a16e-43a6390e3d53
    ToRule ID      : 560874406            FromRule ID    : 2708358054
    Local Subnet   : 0.0.0.0/0            Peer Subnet    : 0.0.0.0/0
    Tunnel Status  : Up
```

```
edge-2> get l2vpn session
Session       : f7147137-5dd0-47fe-9e53-fdc2b687b160
Tunnel        : b026095b-98c8-5932-96ff-dda922ffe316
IPSEC Session : 1ecb00fb-a538-4740-b788-c9049e8cb6c6
Status        : UP
```

使用下列命令來確認 ESXi 主機上的接收埠，其中的「NSX-T 獨立用戶端 VM」位於內部部署環境中。

```
 [root@esxi02:~] esxcfg-vswitch -l |grep NSX
  53                  1           NSXT-client-large.eth2
  225                1           NSXT-client-large.eth1
  52                  1           NSXT-client-large.eth0
```

```
[root@esxi02:~] net-dvs -l | grep "port\ [0-9]\|SINK\|com.vmware.common.alias"
                com.vmware.common.alias = csmlab2DS ,   propType = CONFIG
        port 24:
        port 25:
        port 26:
        port 27:
        port 13:
        port 19:
        port 169:
        port 54:
        port 110:
        port 6:
        port 107:
        port 4:
        port 199:
        port 168:
        port 201:
        port 0:
        port 49:
        port 53:
        port 225:
                com.vmware.etherswitch.port.extraEthFRP =   SINK
        port 52:
```
