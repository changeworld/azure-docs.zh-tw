---
title: Azure VMware Solution by CloudSimple-設定從內部部署到 CloudSimple VPN 閘道的高可用性
description: 說明如何設定從內部部署環境到已啟用高可用性的 CloudSimple VPN 閘道的高可用性連接
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025260"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>設定從內部部署至 CloudSimple VPN 閘道的高可用性連接

網路系統管理員可以設定從其內部部署環境到 CloudSimple VPN 閘道的高可用性 IPsec 站對站 VPN 連線。

本指南提供設定 IPsec 站對站 VPN 高可用性連線的內部部署防火牆的步驟。 詳細步驟適用于內部部署防火牆的類型。 如範例所示，本指南提供兩種防火牆類型的步驟： Cisco ASA 和 Palo Alto Networks。

## <a name="before-you-begin"></a>開始之前

在設定內部部署防火牆之前，請先完成下列工作。

1. 確認您的組織已布 [建](create-nodes.md) 必要的節點，並至少建立一個 CloudSimple 私人雲端。
2. 設定內部部署網路與 CloudSimple 私人雲端之間[的站對站 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。

如需支援的階段1和第2階段提案，請參閱 [VPN 閘道總覽](cloudsimple-vpn-gateways.md) 。

## <a name="configure-on-premises-cisco-asa-firewall"></a>設定內部部署 Cisco ASA 防火牆

本節中的指示適用于 Cisco ASA 8.4 版和更新版本。 在設定範例中，Cisco 自動調整安全性應用裝置軟體版本9.10 會以 IKEv1 模式進行部署和設定。

若要讓站對站 VPN 運作，您必須在內部部署 Cisco ASA VPN 閘道的外部介面上，允許來自 CloudSimple 主要和次要公用 IP (對等 IP) 的 UDP 500/4500 和 ESP (IP 通訊協定 50) 。

### <a name="1-configure-phase-1-ikev1"></a>1. 設定第1階段 (IKEv1) 

若要啟用第1階段 (IKEv1) 在外部介面上，請在 Cisco ASA 防火牆中輸入下列 CLI 命令。

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. 建立 IKEv1 原則

建立 IKEv1 原則，以定義雜湊、驗證、Diffie-Hellman 群組、存留期和加密所使用的演算法和方法。

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. 建立通道群組

在 IPsec 屬性下建立通道群組。 設定您設定 [站對站 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)時所設定的對等 IP 位址和通道預先共用金鑰。

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. 設定 (IPsec) 的階段2

若要設定第2階段 (IPsec) ，請建立存取控制清單 (ACL) ，以定義要加密和通道傳送的流量。 在下列範例中，您感興趣的流量是來自內部部署本機子網的通道 (10.16.1.0/24) 至私人雲端遠端子網 (192.168.0.0/24) 。 如果網站之間有多個子網，ACL 可以包含多個專案。

在 Cisco ASA 8.4 和更新版本中，可以建立物件或物件群組，作為網路、子網、主機 IP 位址或多個物件的容器。 為本機和遠端子網的物件建立物件，並將其用於加密 ACL 和 NAT 語句。

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>將內部部署的本機子網定義為物件

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>將 CloudSimple 遠端子網定義為物件

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>為感興趣的流量設定存取清單

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. 設定轉換集

設定 (TS) 的轉換集，這必須包含關鍵字 ```ikev1``` 。 TS 中指定的加密和雜湊屬性必須與 [CLOUDSIMPLE VPN 閘道的預設](cloudsimple-vpn-gateways.md)設定中所列的參數相符。

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. 設定加密對應

設定包含下列元件的加密對應：

* 對等 IP 位址
* 定義的 ACL，包含感興趣的流量
* 轉換集

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. 套用加密對應

將加密對應套用至外部介面：

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. 確認適用的 NAT 規則

以下是所使用的 NAT 規則。 確定 VPN 流量不受任何其他 NAT 規則的規範。

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>範例 IPsec 站對站 VPN 已建立 Cisco ASA 的輸出

第1階段輸出：

![Cisco ASA 防火牆的第1階段輸出](media/ha-vpn-connection-cisco-phase1.png)

第2階段輸出：

![Cisco ASA 防火牆的第2階段輸出](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>設定內部部署 Palo Alto Networks 防火牆

本節中的指示適用于 Palo Alto Networks 7.1 版和更新版本。 在此設定範例中，Palo Alto Networks 的 VM-Series Software 版本8.1.0 會以 IKEv1 模式進行部署和設定。

若要讓站對站 VPN 運作，您必須在內部部署 Palo Alto Networks 閘道的外部介面上，允許來自 CloudSimple 主要和次要公用 IP (對等 IP) 的 UDP 500/4500 和 ESP (IP 通訊協定 50) 。

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. 建立主要和次要通道介面

登入 Palo Alto 防火牆，選取 [**網路**  >  **介面**通道  >  **Tunnel**  >  **新增**]，設定下欄欄位，然後按一下 **[確定]**。

* 介面名稱。 第一個欄位是以關鍵字 ' 通道 ' 自動填入。 在連續的欄位中，輸入介於1到9999之間的任一數字。 此介面將用作主要通道介面，以在內部部署資料中心與私人雲端之間攜帶站對站流量。
* 評論。 輸入批註，以方便識別通道的用途
* Netflow 設定檔。 保留預設值。
* 配置。指派介面至：虛擬路由器：選取 **預設值**。 
        安全性區域：選取受信任 LAN 流量的區域。 在此範例中，LAN 流量的區功能變數名稱稱是「信任」。
* IPv4。 按一下 [ **新增** ]，然後在您的環境中新增任何非重迭的未使用/32 ip 位址，這將會指派給主要通道介面，並將用於監視通道 (稍後) 說明。

因為這項設定適用于高可用性 VPN，所以需要兩個通道介面：一個主要和一個次要介面。 重複先前的步驟，以建立次要通道介面。 選取不同的通道識別碼和不同的未使用/32 ip 位址。

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. 設定要透過站對站 VPN 連線之私人雲端子網的靜態路由

若要讓內部部署子網達到 CloudSimple 私用雲端子網，則需要路由。

選取 [**網路**  >  **虛擬路由器**  >  *預設*  >  **靜態路由**  >  **新增**]、設定下欄欄位，然後按一下 **[確定]**。

* Name： 輸入任何名稱，以方便識別路由的用途。
* 目的地。 指定要從內部部署的 S2S 通道介面達到的 CloudSimple 私人雲端子網
* 介面。 從下拉式清單中選取在步驟1中建立的主要通道介面 (區段-2) 。 在此範例中，它是通道. 20。
* 下一個躍點。 選取 [無]。
* 管理距離。 保留預設值。
* 公制。 輸入介於1到65535之間的任何值。 此索引鍵是針對對應至主要通道介面的路由輸入較低的計量，相較于對應的次要通道介面（讓先前的路由優先）。 如果通道. 20 的計量值為20，而不是通道的計量值為30，則建議使用通道. 20。
* 路由表。 保留預設值。
* BFD 設定檔。 保留預設值。
* 路徑監視。 請勿核取。

重複先前的步驟，以建立另一個用於私用雲端子網的路由，以透過次要通道介面作為次要/備份路由。 這次請選取不同的通道識別碼和較高的計量，而不是主要路由。

### <a name="3-define-the-cryptographic-profile"></a>3. 定義密碼編譯設定檔

定義密碼編譯設定檔，指定用於在 IKEv1 階段1中設定 VPN 通道的識別、驗證和加密的通訊協定和演算法。

選取 [**網路**  >  **擴充網路設定檔**  >  **IKE 加密**]  >  **新增**、設定下欄欄位，然後按一下 **[確定]**。

* Name： 輸入任何 IKE 加密設定檔的名稱。
* DH 群組。 按一下 [ **新增** ]，然後選取適當的 DH 群組。
* 加密。 按一下 [ **新增** ]，然後選取適當的加密方法。
* 驗證。 按一下 [ **新增** ]，然後選取適當的驗證方法。
* 金鑰存留期。 保留預設值。
* IKEv2 驗證多重。 保留預設值。

### <a name="4-define-ike-gateways"></a>4. 定義 IKE 閘道

定義 IKE 閘道，以在 VPN 通道的每一端之間建立對等互連之間的通訊。

選取 [**網路**  >  **展開網路設定檔**  >  **IKE 閘道**  >  **新增**]，設定下欄欄位，然後按一下 **[確定]**。

[一般] 索引標籤：

* Name： 輸入要與主要 CloudSimple VPN 對等對等互連之 IKE 閘道的名稱。
* 版本。 選取 [ **僅限 IKEv1] 模式**。
* 網址類別型。 選取 [IPv4]。
* 介面。 選取對外公開或外部介面。
* 本機 IP 位址。 保留預設值。
* 對等 IP 位址類型。 選取 [ **IP**]。
* 對等位址。 輸入主要 CloudSimple VPN 對等互連 IP 位址。
* 驗證。 選取 **預先共用金鑰**。
* 預先共用金鑰/確認預先共用金鑰。 輸入預先共用金鑰以符合 CloudSimple VPN 閘道金鑰。
* 本機識別。 輸入內部部署 Palo Alto 防火牆的公用 IP 位址。
* 對等識別。 輸入主要 CloudSimple VPN 對等互連 IP 位址。

[Advanced Options] 索引標籤：

* 啟用被動模式。 請勿核取。
* 啟用 NAT 遍歷。 如果內部部署 Palo Alto 防火牆不在任何 NAT 裝置後方，請保持未核取。 否則，請選取核取方塊。

IKEv1

* Exchange 模式。 選取 [ **主要**]。
* IKE 加密設定檔。 選取您稍早建立的 IKE 加密設定檔。 保持未核取 [啟用片段] 方塊。
* 無效對等偵測。 將方塊保留為未核取。

重複先前的步驟，以建立次要 IKE 閘道。

### <a name="5-define-ipsec-crypto-profiles"></a>5. 定義 IPSEC 加密設定檔

選取 [**網路**  >  ]**展開 [網路設定檔**]  >  **IPSEC 加密**  >  **新增**，設定下欄欄位，然後按一下 **[確定]**。

* Name： 輸入 IPsec 加密設定檔的名稱。
* IPsec 通訊協定。 選取 [ **ESP**]。
* 加密。 按一下 [ **新增** ]，然後選取適當的加密方法。
* 驗證。 按一下 [ **新增** ]，然後選取適當的驗證方法。
* DH 群組。 選取 [ **無 pfs**]。
* 一生。 設定為30分鐘。
* 啟用。 將方塊保留為未核取。

重複上述步驟來建立另一個 IPsec 密碼編譯設定檔，此設定檔將作為次要 CloudSimple VPN 對等。 相同的 IPSEC 加密設定檔也可以同時用於主要和次要 IPsec 通道 (請參閱下列程式) 。

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. 定義用於監視通道的監視設定檔

選取 [**網路**  >  ]**展開 [網路設定檔**  >  **監視**  >  **新增**]，設定下欄欄位，然後按一下 **[確定]**。

* Name： 輸入監視設定檔的任何名稱，以用於通道監視，以主動回應失敗。
* 動作。 選取 [ **容錯移轉**]。
* 區間。 輸入值 **3**。
* 閾值。 輸入值 **7**。

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. 設定主要和次要 IPsec 通道。

選取 [**網路**  >  **IPsec 通道**  >  **新增**]、設定下欄欄位，然後按一下 **[確定]**。

[一般] 索引標籤：

* Name： 輸入要與主要 CloudSimple VPN 對等對等互連之主要 IPSEC 通道的任何名稱。
* 通道介面。 選取主要通道介面。
* 輸入 。 保留預設值。
* 網址類別型。 選取 [IPv4]。
* IKE 閘道。 選取主要 IKE 閘道。
* IPsec 加密設定檔。 選取主要 IPsec 設定檔。 選取 [ **顯示 Advanced options**]。
* 啟用重新執行保護。 保留預設值。
* 複製 TOS 標頭。 將方塊保留為未核取。
* 通道監視。 勾選方塊。
* 目的地 IP。 輸入屬於 CloudSimple 私人雲端子網的任何 IP 位址，該子網可透過站對站連線來允許。 請確定通道介面 (例如通道. 20-10.64.5.2/32 和通道. 30-10.64.6.2/32) Palo Alto 可透過站對站 VPN 連線到 CloudSimple 私用雲端 IP 位址。 請參閱下列 proxy 識別碼設定。
* 配置 檔。 選取監視設定檔。

[Proxy 識別碼] 索引標籤：按一下 [ **IPv4**  >  **新增**]，然後設定下列各項：

* Proxy 識別碼。 針對有趣的流量輸入任何名稱。 一個 IPsec 通道內可能會有多個 Proxy 識別碼。
* 本機。 指定允許透過站對站 VPN 與私人雲端子網進行通訊的內部部署本機子網。
* 遠端。 指定允許與本機子網通訊的私人雲端遠端子網。
* 協定。 選取 [ **任何**]。

重複上述步驟來建立另一個 IPsec 通道，以用於次要 CloudSimple VPN 對等。

## <a name="references"></a>參考資料

在 Cisco ASA 上設定 NAT：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">Cisco ASA 5500 系列設定指南</a>

Cisco ASA 支援 IKEv1 和 IKEv2 屬性：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">Cisco ASA 系列 CLI 設定指南</a>

在具有8.4 版和更新版本的 Cisco ASA 上設定 IPsec 站對站 VPN：

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">在 ASA 上使用 ASDM 或 CLI 來設定 IKEv1 IPsec 站對站通道</a>

在 Azure 上設定 Cisco 自我調整安全性設備虛擬 (ASAv) ：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">Cisco 自動調整安全性虛擬裝置 (ASAv) 快速入門手冊</a>

在 Palo Alto 上使用 Proxy 識別碼設定站對站 VPN：

[設定站對站 VPN](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

設定通道監視：

[設定通道監視](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE 閘道或 IPsec 通道作業：

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">啟用/停用、重新整理或重新開機 IKE 閘道或 IPsec 通道</a>
