---
title: Azure VMware 解決方案(按雲端簡單 - 將本地第 2 層網路擴展至私有雲)
description: 描述如何在雲端簡單私有雲上的 NSX-T 和本地獨立 NSX 邊緣用戶端的 NSX-T 之間設定第 2 層 VPN
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1f5ff48f4d5a658a1bbb4e6b9fb4b3f0f3fb190f
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81602697"
---
# <a name="migrate-workloads-using-layer-2-stretched-networks"></a>使用第 2 層延伸網路遷移工作負載

在本指南中,您將瞭解如何使用第 2 層 VPN (L2VPN) 將第 2 層網路從本地環境擴展至雲端簡單私有雲。 此解決方案允許將本地 VMware 環境中執行的工作負載遷移到 Azure 中的專用雲,這些工作負荷位於同一子網位址空間內,而無需重新對工作負荷進行 IP。

基於 L2VPN 的第 2 層網路拉伸可以在本地 VMware 環境中與或沒有基於 NSX 的網路配合使用。 如果本地沒有基於 NSX 的工作負載網路,則可以使用獨立的 NSX 邊緣服務閘道。

> [!NOTE]
> 本指南介紹了本地和私有雲數據中心通過網站到網站 VPN連接的情況。

## <a name="deployment-scenario"></a>部署案例

要使用 L2VPN 擴展本地網路,必須配置 L2VPN 伺服器(目標 NSX-T Tier0 路由器)和 L2VPN 用戶端(源獨立用戶端)。  

在此部署方案中,您的私有雲通過網站到網站 VPN 隧道連接到本地環境,該隧道允許本地管理和 vMotion 子網與私有雲管理和 vMotion 子網進行通信。 此安排對於交叉 vCenter vMotion (xVC-vMotion) 是必需的。 NSX-T Tier0 路由器作為 L2VPN 伺服器部署在私有雲中。

獨立 NSX 邊緣作為 L2VPN 用戶端部署在本地環境中,隨後與 L2VPN 伺服器配對。 GRE 隧道終結點在兩側創建,並配置為將本地第2層網路「拉伸」到私有雲。 下圖將描述此配置。

![部署案例](media/l2vpn-deployment-scenario.png)

要瞭解有關使用 L2 VPN 移轉的資訊,請參閱 VMware 文件中的[虛擬專用網路](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)。

## <a name="prerequisites-for-deploying-the-solution"></a>部署解決方案的先決條件

在部署與設定解決方案之前,請驗證以下內容是否已到位:

* 本地 vSphere 版本為 6.7U1+ 或 6.5P03*。
* 本地 vSphere 許可證處於企業升級級別(對於 vSphere 分散式交換機)。
* 確定要拉伸到私有雲的工作負載第 2 層網路。
* 在本地環境中識別用於部署 L2VPN 用戶端設備的第 2 層網路。
* [私有雲已建立](create-private-cloud.md)。
* 獨立 NSX-T 邊緣裝置的版本與私有雲環境中使用的 NSX-T 管理器版本 (NSX-T 2.3.0) 相容。
* 已在本地 vCenter 中創建了一個中繼埠組,並啟用了偽造的傳輸。
* 已保留公共 IP 位址以用於 NSX-T 獨立用戶端上行鏈路 IP 位址,並且 1:1 NAT 已到位,用於在兩個位址之間進行轉換。
* DNS 轉發設置在本地 DNS 伺服器上,以便az.cloudsimple.io域指向私有雲 DNS 伺服器。
* RTT 延遲小於或等於 150 毫秒,這是 vMotion 在兩個網站上工作所需的。

## <a name="limitations-and-considerations"></a>限制與注意事項

下表列出了支援的 vSphere 版本和網路適配器類型。  

| vSphere 版本 | 來源 vSwitch 類型 | 虛擬 NIC 驅動程式 | 目標 vSwitch 類型 | 是否支援？ |
------------ | ------------- | ------------ | ------------- | ------------- 
| 全部 | Dvs | 全部 | Dvs | 是 |
| vSphere 6.7UI 或更高版本,6.5P03 或更高版本 | Dvs | VMXNET3 | N-VDS | 是 |
| vSphere 6.7UI 或更高版本,6.5P03 或更高版本 | Dvs | E1000 | N-VDS | [每個 VWware 不受支援](https://kb.vmware.com/s/article/56991) |
| vSphere 6.7UI 或 6.5P03、NSX-V 或 NSX-T2.2、6.5P03 或更高版本 | 全部 | 全部 | N-VDS | [每個 VWware 不受支援](https://kb.vmware.com/s/article/56991) |

自 VMware NSX-T 2.3 版本:

* 通過 L2VPN 拉伸到本地的私有雲端的邏輯交換機不能同時路由。 拉伸的邏輯交換機無法連接到邏輯路由器。
* 只能使用 API 調用配置基於 L2VPN 和路由的 IPSEC VPN。

有關詳細資訊,請參閱 VMware 文件中的[虛擬專用網路](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58.html#GUID-A8B113EC-3D53-41A5-919E-78F1A3705F58__section_44B4972B5F12453B90625D98F86D5704)。

### <a name="sample-l2-vpn-deployment-addressing"></a>範例 L2 VPN 部署定址

### <a name="on-premises-network-where-the-standalone-esg-l2-vpn-client-is-deployed"></a>部署獨立 ESG(L2 VPN 用戶端)的本地網路

| **Item** | **ReplTest1** |
|------------|-----------------|
| 網路名稱 | MGMT_NET_VLAN469 |
| VLAN | 469 |
| CIDR| 10.250.0.0/24 |
| 獨立邊緣裝置 IP 位址 | 10.250.0.111 |
| 獨立邊緣裝置 NAT IP 位址 | 192.227.85.167 |

### <a name="on-premises-network-to-be-stretched"></a>要拉伸的本地網路

| **Item** | **ReplTest1** |
|------------|-----------------|
| VLAN | 472 |
| CIDR| 10.250.3.0/24 |

### <a name="private-cloud-ip-schema-for-nsx-t-tier0-router-l2-vpn-serve"></a>NSX-T Tier0 路由器的私有雲端 IP 架構(L2 VPN 服務)

| **Item** | **ReplTest1** |
|------------|-----------------|
| 環迴介面 | 192.168.254.254/32 |
| 隧道介面 | 5.5.5.1/29 |
| 邏輯開關(拉伸) | Stretch_LS |
| 環迴介面 (NAT IP 位址) | 104.40.21.81 |

### <a name="private-cloud-network-to-be-mapped-to-the-stretched-network"></a>要映射到拉伸網路的私有雲網路

| **Item** | **ReplTest1** |
|------------|-----------------|
| VLAN | 712 |
| CIDR| 10.200.15.0/24 |

## <a name="fetch-the-logical-router-id-needed-for-l2vpn"></a>取得 L2VPN 所需的邏輯路由器 ID

以下步驟演示如何獲取 IPsec 和 L2VPN 服務的 Tier0 DR 邏輯路由器實例的邏輯路由器 ID。 實現 L2VPN 時,稍後將需要邏輯路由器 ID。

1. 登入 NSX-T`https://*nsx-t-manager-ip-address*`管理員並選擇**網路** > **路由器** > **提供者-LR** > **概述**。 對於**高可用性模式**,選擇 **"活動待機**"。 此操作將打開一個彈出視窗,該視窗顯示第 0 層路由器當前處於活動狀態的邊緣 VM。

    ![選擇活動待機](media/l2vpn-fetch01.png)

2. 選擇**結構** > **節點** > **邊**。 記下上一步中識別的活動邊緣 VM(邊緣 VM1)的管理 IP 位址。

    ![註解 IP](media/l2vpn-fetch02.png)

3. 將 SSH 作業階段打開到邊緣 VM 的管理 IP 位址。 執行命令```get logical-router```與使用者名**管理員**和密碼**雲簡單123!**.

    ![取得邏輯路由器輸出](media/l2vpn-fetch03.png)

4. 如果您沒有看到"DR-提供程式-LR"的條目,則完成以下步驟。

5. 創建兩個疊加支援的邏輯交換機。 一個邏輯交換機被拉伸到遷移的工作負載所在的本地。 另一個邏輯交換機是虛擬交換機。 有關說明,請參閱在 VMware 文件中[建立邏輯交換機](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-23194F9A-416A-40EA-B9F7-346B391C3EF8.html)。

    ![建立邏輯交換器](media/l2vpn-fetch04.png)

6. 使用鏈路本地 IP 位址或任何來自本地或私有雲端的非重疊子網路將虛擬交換機連接到 Tier1 路由器。 請參考 VMware 文件中[在第 1 層邏輯路由器上新增下行鏈路連接埠](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.3/com.vmware.nsxt.admin.doc/GUID-E7EA867C-604C-4224-B61D-2A8EF41CB7A6.html)。

    ![連線虛擬開關](media/l2vpn-fetch05.png)

7. 在`get logical-router`邊緣 VM 的 SSH 會話上再次運行該命令。 將顯示"DR-提供程式-LR"邏輯路由器的 UUID。 記下 UUID,這是配置 L2VPN 時所必需的。

    ![取得邏輯路由器輸出](media/l2vpn-fetch06.png)

## <a name="fetch-the-logical-switch-id-needed-for-l2vpn"></a>取得 L2VPN 所需的邏輯交換機 ID

1. 登入[NSX-T 管理員](https://nsx-t-manager-ip-address)。
2. 選擇**網路** > **交換** > **交換機** > **Overview****\>**<\邏輯交換機概述。 > 
3. 記下拉伸邏輯交換機的 UUID,這是配置 L2VPN 時所必需的。

    ![取得邏輯路由器輸出](media/l2vpn-fetch-switch01.png)

## <a name="routing-and-security-considerations-for-l2vpn"></a>L2VPN 的路由和安全注意事項

要在 NSX-T Tier0 路由器和獨立 NSX Edge 客戶端之間建立基於 IPsec 路由的 VPN,NSX-T Tier0 路由器的環回介面必須能夠透過 UDP 500/4500 與 NSX 獨立用戶端的公共 IP 位址在本地通信。

### <a name="allow-udp-5004500-for-ipsec"></a>允許 UDP 500/4500 用於 IPsec

1. 為雲端簡單門戶中的 NSX-T Tier0 環回介面[建立公共 IP 位址](public-ips.md)。

2. [創建具有](firewall.md)允許 UDP 500/ 4500 入站流量的有狀態規則的防火牆表,並將防火牆表附加到 NSX-T 主機傳輸子網。

### <a name="advertise-the-loopback-interface-ip-to-the-underlay-network"></a>通告環回介面 IP 到底層網路

1. 為環回介面網路創建空路由。 登入 NSX-T 管理員並選擇**網路** > **路由** > **路由器** > **提供者-LR** > **路由** > **靜態路由**。 按一下 [新增]  。 對於**網路**,輸入回環介面 IP 位址。 對於**下一個躍點**,按兩下 **「添加**」,為下一個躍點指定「Null」,並為「管理員距離」保留預設值 1。

    ![新增靜態路由](media/l2vpn-routing-security01.png)

2. 建立 IP 首碼清單。 登入 NSX-T 管理員並選擇**網路** > **路** > **由 路由器** > **提供者-LR** > **路由** > IP**首碼清單**。 按一下 [新增]  。 輸入名稱以標識清單。 對於**首碼**,單擊「**添加**」兩次。 在第一行中,輸入"0.0.0.0/0"為 **"網络**"和"拒絕"**為操作**。 在第二行中,選擇 **「任意**為**網路和****操作許可**」。 **Action**
3. 將 IP 首碼清單附加到兩個 BGP 鄰居 (TOR)。 將 IP 首碼清單附加到 BGP 鄰居可防止在 BGP 中通告預設路由到 TOR 交換機。 但是,包含空路由的任何其他路由都將通告回環介面 IP 位址到 TOR 交換機。

    ![建立 IP 前置字串清單](media/l2vpn-routing-security02.png)

4. 登入 NSX-T 管理員並選擇**網路** > **路** > **由 路由器** > **提供者-LR** > **路由** > **BGP** > **鄰居**。 選擇第一個鄰居。 按下 **「編輯** > **位址族**」 。。 對於 IPv4 系列,編輯 **「退出篩選器」** 列並選擇您創建的 IP 首碼清單。 按一下 [檔案]  。 對第二個鄰居重複此步驟。

    ![附加 IP](media/l2vpn-routing-security03.png)![前置字串 1 附加 IP 前置字串 2](media/l2vpn-routing-security04.png)

5. 將空靜態路由重新分發到 BGP 中。 要通告到底層的環迴介面路由,必須將空靜態路由重新分發到 BGP 中。 登入 NSX-T 管理員並選擇**網路** > **路** > **由路由器** > **提供者-LR** > **路由路由** > **重新分配** > **鄰居**。 選擇**提供程式-LR-Route_Redistribution,** 然後按**下「編輯**」 。。 選擇 **「靜態**」複選框,然後按下 **「 儲存**」。

    ![將空靜態路由重新分發到 BGP 中](media/l2vpn-routing-security05.png)

## <a name="configure-a-route-based-vpn-on-the-nsx-t-tier0-router"></a>在 NSX-T 第 0 層路由器上設定基於路由的 VPN

使用以下範本填寫在 NSX-T Tier0 路由器上配置基於路由的 VPN 的所有詳細資訊。 後續 POST 調用中都需要每個 POST 調用中的 UUID。 L2VPN 的回回介面和隧道介面的 IP 位址必須是唯一的,不能與本地或私有雲網路重疊。

為 L2VPN 選擇的回環和隧道介面的 IP 位址必須是唯一的,並且不能與本地或私有雲端網路重疊。 迴環介面網路必須始終為 /32。

```
Loopback interface ip : 192.168.254.254/32
Tunnel interface subnet : 5.5.5.0/29
Logical-router ID : UUID of Tier0 DR logical router obtained in section "Steps to fetch Logical-Router ID needed for L2VPN"
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

對於以下所有 API 呼叫,請將 IP 位址取代為 NSX-T 管理員 IP 位址。 可以從 POSTMAN 用戶端`curl`或使用 命令運行所有這些 API 呼叫。

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

### <a name="create-profiles-ike"></a>建立設定檔:IKE

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

### <a name="create-profiles-dpd"></a>建立設定檔:DPD

```
POST  https://192.168.110.201/api/v1/vpn/ipsec/dpd-profiles  

{
"resource_type": "IPSecVPNDPDProfile",
"display_name": "nsx-default-dpd-profile",
"enabled": true
}
```

### <a name="create-profiles-tunnel"></a>建立設定檔:隧道

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

### <a name="create-a-local-endpoint"></a>建立本地端的結束點

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

### <a name="create-a-peer-endpoint"></a>建立對等終結點

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

### <a name="create-a-route-based-vpn-session"></a>建立基於路由的 VPN 工作階段

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

## <a name="configure-l2vpn-on-nsx-t-tier0-router"></a>在 NSX-T 第 0 層路由器上設定 L2VPN

每次 POST 調用後填寫以下資訊。 在隨後的 POST 調用中需要這些指示。

```
L2VPN Service ID:
L2VPN Session ID:
Logical Port ID:
```

### <a name="create-the-l2vpn-service"></a>建立 L2VPN 服務

以下 GET 命令的輸出將為空,因為配置尚未完成。

```
GET : https://192.168.110.201/api/v1/vpn/l2vpn/services
```

對於以下 POST 命令,邏輯路由器 ID 是前面獲取的 Tier0 DR 邏輯路由器的 UUID。

```
POST : https://192.168.110.201/api/v1/vpn/l2vpn/services

{
"logical_router_id": "Logical Router ID",
"enable_full_mesh" : true
}
```

### <a name="create-the-l2vpn-session"></a>建立 L2VPN 工作階段

對於以下 POST 命令,L2VPN 服務 ID 是您剛剛獲得的 ID,IPsec VPN 會話 ID 是上一節中獲得的 ID。

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

這些調用創建 GRE 隧道終結點。 要檢查狀態,請運行以下命令。

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

### <a name="create-logical-port-with-the-tunnel-id-specified"></a>使用指定的隧道 ID 建立邏輯連接埠

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

## <a name="obtain-the-peer-code-for-l2vpn-on-the-nsx-t-side"></a>在 NSX-T 端取得 L2VPN 的對等代碼

獲取 NSX-T 終結點的對等代碼。 配置遠端終結點時需要對等代碼。 可以從上一節獲取 L2VPN <会话 id>。 有關詳細資訊,請參閱[NSX-T 2.3 API 指南](https://www.vmware.com/support/nsxt/doc/nsxt_23_api.html)。

```
GET https://192.168.110.201/api/v1/vpn/l2vpn/sessions/<session-id>/peer-codes
```

## <a name="deploy-the-nsx-t-standalone-client-on-premises"></a>部署 NSX-T 獨立用戶端 (本地)

在部署之前,請驗證您的本地防火牆規則是否允許從/到 CloudSimple 公共 IP 位址的入站和出站 UDP 500/4500 流量,該流量是之前為 NSX-T0 路由器回環介面保留的。 

1. [下載獨立 NSX 邊緣客戶端](https://my.vmware.com/group/vmware/details?productId=673&rPId=33945&downloadGroup=NSX-T-230)OVF 並將檔從下載的捆綁包中提取到資料夾中。

    ![下載獨立的 NSX 邊緣客戶端](media/l2vpn-deploy-client01.png)

2. 跳到包含所有提取檔案的資料夾。 選擇適用於大型設備尺寸或 NSX-l2t-用戶端-Xlarge.mf 和 NSX-l2t-用戶端-大.ovf 的所有 vmdks(NSX-l2t-用戶端-大.mf 和 NSX-l2t-用戶端-Xlarge.ovf,用於超大設備大小)。 按 [下一步]  。

    ![選擇樣本](media/l2vpn-deploy-client02.png)![選擇樣本](media/l2vpn-deploy-client03.png)

3. 輸入 NSX-T 獨立用戶端的名稱,然後單擊 **「下一步**」 。。

    ![輸入樣本名稱](media/l2vpn-deploy-client04.png)

4. 根據需要按下 **「下一步**」以存取數據儲存設定。 為 NSX-T 獨立客戶端選擇適當的數據存儲,然後單擊 **「下一步**」 。。

    ![選擇資料儲存](media/l2vpn-deploy-client06.png)

5. 為 NSX-T 獨立用戶端選擇中繼 (Trunk PG)、公共(上行鏈路 PG) 和 HA 介面 (上行 PG) 的正確埠組。 按 [下一步]  。

    ![選擇連接埠群組](media/l2vpn-deploy-client07.png)

6. 在 **「自訂樣本」** 螢幕中填寫以下詳細資訊,然後按下 **「下一步**」 :

    延伸 L2T:

    * **通訊通訊地址** 輸入為 NSX-T 層 0 環回介面在 Azure CloudSimple 門戶上保留的 IP 位址。
    * **對等代碼**。 貼上從 L2VPN 伺服器部署的最後一步獲取的對等代碼。
    * **子介面 VLAN(隧道 ID)。** 輸入要拉伸的 VLAN ID。 在括弧 (),輸入以前配置的隧道 ID。

    延伸上行鏈路介面:

    * **DNS IP 位址**。 輸入本地 DNS IP 位址。
    * **預設閘道**。  輸入 VLAN 的預設閘道,該閘道將充當此用戶端的預設閘道。
    * **IP 位址**。 輸入獨立用戶端的上行 IP 位址。
    * **前置字串 :** 輸入上行 VLAN/子網的首碼長度。
    * **CLI 管理員/啟用/根使用者密碼**。 設置管理員/啟用/根帳戶的密碼。

      ![自訂樣本](media/l2vpn-deploy-client08.png)
      ![自訂樣本 ─ 更多](media/l2vpn-deploy-client09.png)

7. 查看設置並按下 **「完成**」。

    ![完成設定](media/l2vpn-deploy-client10.png)

## <a name="configure-an-on-premises-sink-port"></a>設定本地端連接埠

如果其中一個 VPN 網站未部署 NSX,則可以通過在該網站部署獨立的 NSX Edge 來配置 L2 VPN。 使用 NSX 未管理的主機上的 OVF 檔部署獨立的 NSX 邊緣。 這將部署 NSX 邊緣服務閘道設備以充當 L2 VPN 用戶端。

如果獨立邊緣中繼 vNIC 連接到 vSphere 分散式交換機,則 L2 VPN 功能需要混雜模式或接收器埠。 使用混雜模式可能會導致重複的 ping 和重複的回應。 因此,在 L2 VPN 獨立 NSX 邊緣配置中使用接收器埠模式。 請參閱 VMware 文件中的[「設定接收器埠](https://docs.vmware.com/en/VMware-NSX-Data-Center-for-vSphere/6.4/com.vmware.nsx.admin.doc/GUID-3CDA4346-E692-4592-8796-ACBEEC87C161.html)」 。

## <a name="ipsec-vpn-and-l2vpn-verification"></a>IPsec VPN 和 L2VPN 驗證

使用以下命令驗證來自獨立 NSX-T 邊緣的 IPsec 和 L2VPN 工作階段。

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

使用以下命令驗證來自 NSX-T Tier0 路由器的 IPsec 和 L2VPN 作業階段。

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

使用以下命令驗證 NSX-T 獨立用戶端 VM 駐留在本地環境中的 ESXi 主機上的接收器埠。

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
