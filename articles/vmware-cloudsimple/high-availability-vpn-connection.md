---
title: Azure VMware 解決方案（按雲簡單 ） - 配置從本地到雲簡單 VPN 閘道的高可用性
description: 描述如何配置高可用性連接，從本地環境到為高可用性啟用的 CloudSimple VPN 閘道
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e3118814eacc6cc63b5db59bd7f1877c1d347dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025260"
---
# <a name="configure-a-high-availability-connection-from-on-premises-to-cloudsimple-vpn-gateway"></a>配置從本地到雲簡單 VPN 閘道的高可用性連接

網路系統管理員可以從其本地環境配置高可用性 IPsec 網站到網站 VPN 連接，到 CloudSimple VPN 閘道。

本指南介紹了為 IPsec 網站到網站 VPN 高可用性連接配置本地防火牆的步驟。 詳細步驟特定于本地防火牆的類型。 例如，本指南介紹了兩種類型的防火牆的步驟：Cisco ASA 和帕洛阿爾托網路。

## <a name="before-you-begin"></a>開始之前

在配置本地防火牆之前，請完成以下任務。

1. 驗證您的組織是否[預配了](create-nodes.md)所需的節點並創建了至少一個雲簡單私有雲。
2. 在本地網路和雲簡單私有雲之間[配置網站到網站 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。

有關支援的階段 1 和階段 2 建議，請參閱[VPN 閘道概述](cloudsimple-vpn-gateways.md)。

## <a name="configure-on-premises-cisco-asa-firewall"></a>配置本地思科 ASA 防火牆

本節中的說明適用于 Cisco ASA 版本 8.4 及更高版本。 在配置示例中，思科自我調整安全設備軟體版本 9.10 在 IKEv1 模式下部署和配置。

要使網站到網站 VPN 正常工作，您必須允許本地 Cisco ASA VPN 閘道外部介面上的 CloudSimple 主和輔助公共 IP（對等 IP）中的 UDP 500/4500 和 ESP（IP 協定 50）。

### <a name="1-configure-phase-1-ikev1"></a>1. 配置第 1 階段 （IKEv1）

要在外部介面上啟用階段 1 （IKEv1），請在 Cisco ASA 防火牆中輸入以下 CLI 命令。

```crypto ikev1 enable outside```

### <a name="2-create-an-ikev1-policy"></a>2. 創建 IKEv1 策略

創建 IKEv1 策略，該策略定義用於雜湊、身份驗證、迪菲-赫爾曼組、存留期和加密的演算法和方法。

```
crypto ikev1 policy 1
authentication pre-share
encryption aes-256
hash sha
group 2
lifetime 28800
```

### <a name="3-create-a-tunnel-group"></a>3. 創建隧道組

在 IPsec 屬性下創建隧道組。 配置對等 IP 位址和隧道預共用金鑰，在[配置網站到網站 VPN 閘道](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)時設置該金鑰。

```
tunnel-group <primary peer ip> type ipsec-l2l
tunnel-group <primary peer ip> ipsec-attributes
ikev1 pre-shared-key *****

tunnel-group <secondary peer ip> type ipsec-l2l
tunnel-group <secondary peer ip> ipsec-attributes
ikev1 pre-shared-key *****
```

### <a name="4-configure-phase-2-ipsec"></a>4. 設定階段 2（IPsec）

要設定階段 2 （IPsec），請創建一個存取控制清單 （ACL），該清單定義要加密和隧道的流量。 在下面的示例中，感興趣的流量來自從本地本地本地子網 （10.16.1.0/24） 到私有雲遠端子網 （192.168.0/24） 的隧道。 如果網站之間有多個子網，則 ACL 可以包含多個條目。

在 Cisco ASA 版本 8.4 及更高版本中，可以創建物件或物件組，作為網路、子網、主機 IP 位址或多個物件的容器。 為本地網創建物件，為遠端子網創建物件，並將其用於加密 ACL 和 NAT 語句。

#### <a name="define-an-on-premises-local-subnet-as-an-object"></a>將本地本地本地子網定義為物件

```
object network AZ_inside
subnet 10.16.1.0 255.255.255.0
```

#### <a name="define-the-cloudsimple-remote-subnet-as-an-object"></a>將雲簡單遠端子網定義為物件

```
object network CS_inside
subnet 192.168.0.0 255.255.255.0
```

#### <a name="configure-an-access-list-for-the-traffic-of-interest"></a>為感興趣的流量配置訪問清單

```
access-list ipsec-acl extended permit ip object AZ_inside object CS_inside
```

### <a name="5-configure-the-transform-set"></a>5. 配置轉換集

配置轉換集 （TS），該轉換集必須涉及```ikev1```關鍵字 。 TS 中指定的加密和雜湊屬性必須與[CloudSimple VPN 閘道的預設配置](cloudsimple-vpn-gateways.md)中列出的參數匹配。

```
crypto ipsec ikev1 transform-set devtest39 esp-aes-256 esp-sha-hmac 
```

### <a name="6-configure-the-crypto-map"></a>6. 配置加密映射

配置包含以下元件的加密映射：

* 對等 IP 位址
* 包含感興趣流量的已定義 ACL
* 轉換集

```
crypto map mymap 1 set peer <primary peer ip> <secondary peer ip>
crypto map mymap 1 match address ipsec-acl
crypto map mymap 1 set ikev1 transform-set devtest39
```

### <a name="7-apply-the-crypto-map"></a>7. 應用加密映射

在外部介面上應用加密映射：

```crypto map mymap interface outside```

### <a name="8-confirm-applicable-nat-rules"></a>8. 確認適用的 NAT 規則

以下是使用的 NAT 規則。 確保 VPN 流量不受任何其他 NAT 規則的影響。

```nat (inside,outside) source static AZ_inside AZ_inside destination static CS_inside CS_inside```

### <a name="sample-ipsec-site-to-site-vpn-established-output-from-cisco-asa"></a>思科 ASA 建立的 IPsec 網站到網站 VPN 示例輸出

第 1 階段輸出：

![Cisco ASA 防火牆的第 1 階段輸出](media/ha-vpn-connection-cisco-phase1.png)

第 2 階段輸出：

![Cisco ASA 防火牆的第 2 階段輸出](media/ha-vpn-connection-cisco-phase2.png)

## <a name="configure-on-premises-palo-alto-networks-firewall"></a>配置本地帕洛阿爾托網路防火牆

本節中的說明適用于帕洛阿爾托網路版本 7.1 及更高版本。 在此配置示例中，帕洛阿爾托網路 VM 系列軟體版本 8.1.0 在 IKEv1 模式下部署和配置。

要使網站到網站 VPN 正常工作，您必須允許本地帕洛阿爾托網路閘道外部介面上的 CloudSimple 主和輔助公共 IP（對等 IP）中的 UDP 500/4500 和 ESP（IP 協定 50）。

### <a name="1-create-primary-and-secondary-tunnel-interfaces"></a>1. 創建主隧道和輔助隧道介面

登錄到帕洛阿爾托防火牆，選擇**網路** > **介面** > **隧道** > **添加**，配置以下欄位，然後按一下 **"確定**"。

* 介面名稱。 第一個欄位使用關鍵字"隧道"自動填滿。 在相鄰欄位中，輸入介於 1 到 9999 之間的任一數字。 此介面將用作主隧道介面，用於在本地資料中心和私有雲之間傳輸網站到網站的流量。
* 評論。 輸入注釋，以便輕鬆識別隧道的用途
* 淨流量設定檔。 保留預設值。
* 配置。將介面分配給：虛擬路由器：選擇**預設**。 
        安全區域：為受信任的 LAN 流量選擇區域。 在此示例中，LAN 流量區域的名稱為"信任"。
* IPv4。 按一下"**添加並**添加環境中任何非重疊未使用的 /32 ip 位址"，該位址將分配給主隧道介面，並將用於監視隧道（稍後說明）。

由於此配置適用于高可用性 VPN，因此需要兩個隧道介面：一個主介面和一個輔助介面。 重複前面的步驟以創建輔助隧道介面。 選擇其他隧道 ID 和未使用 /32 ip 位址。

### <a name="2-set-up-static-routes-for-private-cloud-subnets-to-be-reached-over-the-site-to-site-vpn"></a>2. 為通過網站到網站 VPN 到達的私有雲子網設置靜態路由

本地子網到達雲簡單專用雲子網時，必須路由。

選擇**網路** > **虛擬路由器** > *預設* > **靜態路由** > **添加**，配置以下欄位，然後按一下 **"確定**"。

* Name： 輸入任何名稱，以便輕鬆識別路線的用途。
* 目的地。 指定從本地通過 S2S 隧道介面到達的雲簡單私有雲子網
* 介面。 從下拉清單中選擇步驟 1（第 2 節）中創建的主隧道介面。 在此示例中，它是隧道。
* 下一跳。 選取 [無]****。
* 管理員距離。 保留預設值。
* 公制。 輸入從 1 到 65535 的任何值。 關鍵是輸入與主隧道介面對應的路由的較低指標，而與路由對應的輔助隧道介面相比，使前一個路由首選。 如果 tunnel.20 的公制值為 20，而隧道的公制值為 30，則最好使用隧道.20。
* 路由表。 保留預設值。
* BFD 設定檔。 保留預設值。
* 路徑監視。 請勿核取。

重複上述步驟，為私有雲子網創建另一個路由，以便通過輔助隧道介面用作輔助/備份路由。 這一次，選擇不同的隧道 ID 和比主路由更高的指標。

### <a name="3-define-the-cryptographic-profile"></a>3. 定義加密設定檔

定義一個加密設定檔，指定用於在 IKEv1 階段 1 中設置 VPN 隧道的標識、身份驗證和加密的協定和演算法。

選擇**網路** > **擴展網路設定檔** > **IKE 加密** > **添加**，配置以下欄位，然後按一下 **"確定**"。

* Name： 輸入 IKE 加密設定檔的任何名稱。
* DH集團。 按一下"**添加**"並選擇相應的 DH 組。
* 加密。 按一下"**添加"** 並選擇適當的加密方法。
* 驗證。 按一下"**添加**"並選擇適當的身份驗證方法。
* 金鑰存留期。 保留預設值。
* IKEv2 身份驗證倍數。 保留預設值。

### <a name="4-define-ike-gateways"></a>4. 定義 IKE 閘道

定義 IKE 閘道以在 VPN 隧道的每一端建立對等方之間的通信。

選擇**網路** > **擴展網路設定檔** > **IKE 閘道** > **添加**，配置以下欄位，然後按一下 **"確定**"。

常規選項卡：

* Name： 輸入要與主 CloudSimple VPN 對等體對等的 IKE 閘道的名稱。
* 版本。 選擇**僅 IKEv1 模式**。
* 網址類別型。 選取 [IPv4]****。
* 介面。 選擇面向公共的或外部介面。
* 本地 IP 位址。 保留預設值。
* 對等 IP 位址類型。 選擇**IP**。
* 對等位址。 輸入主雲簡單 VPN 對等 IP 位址。
* 驗證。 選擇**預共用金鑰**。
* 預共用金鑰/確認預共用金鑰。 輸入預共用金鑰以匹配雲簡單 VPN 閘道金鑰。
* 本地標識。 輸入本地帕洛阿爾托防火牆的公共 IP 位址。
* 對等識別。 輸入主雲簡單 VPN 對等 IP 位址。

高級選項選項卡：

* 啟用被動模式。 請勿核取。
* 啟用 NAT 遍歷。 如果本地帕洛阿爾托防火牆未位於任何 NAT 設備後面，請保持未選中狀態。 否則，選擇該核取方塊。

IKEv1：

* 交換模式。 選擇**主**。
* IKE 加密設定檔。 選擇您之前創建的 IKE 加密設定檔。 未選中啟用碎片框。
* 死對等檢測。 未選中該框。

重複上述步驟以創建輔助 IKE 閘道。

### <a name="5-define-ipsec-crypto-profiles"></a>5. 定義 IPSEC 加密設定檔

選擇**網路** > **擴展網路設定檔** > **IPSEC 加密** > **添加**，配置以下欄位，然後按一下 **"確定**"。

* Name： 輸入 IPsec 加密設定檔的名稱。
* IPsec 協定。 選擇**ESP**。
* 加密。 按一下"**添加"** 並選擇適當的加密方法。
* 驗證。 按一下"**添加**"並選擇適當的身份驗證方法。
* DH集團。 選擇**無 pfs**。
* 存留期。 設置為 30 分鐘。
* 啟用。 未選中該框。

重複上述步驟以創建另一個 IPsec 加密設定檔，該設定檔將用作輔助 CloudSimple VPN 對等體。 相同的 IPSEC 加密設定檔也可用於主和輔助 IPsec 隧道（請參閱以下過程）。

### <a name="6-define-monitor-profiles-for-tunnel-monitoring"></a>6. 定義隧道監控的監視器設定檔

選擇 **"網路** > **擴展網路設定檔** > **監視器添加** > **Add**"，配置以下欄位，然後按一下 **"確定**"。

* Name： 輸入用於隧道監控的監視器設定檔的任何名稱，以便對故障做出主動反應。
* 動作。 選擇 **"容錯移轉**"。
* 區間。 輸入值**3**。
* 閾值。 輸入值**7**。

### <a name="7-set-up-primary-and-secondary-ipsec-tunnels"></a>7. 設置主和輔助 IPsec 隧道。

選擇**網路** > **IPsec 隧道** > **添加**，配置以下欄位，然後按一下 **"確定**"。

常規選項卡：

* Name： 輸入要與主 CloudSimple VPN 對等互連的主 IPSEC 隧道的任何名稱。
* 隧道介面。 選擇主隧道介面。
* 輸入 。 保留預設值。
* 網址類別型。 選取 [IPv4]****。
* IKE 閘道。 選擇主 IKE 閘道。
* IPsec 加密設定檔。 選擇主 IPsec 設定檔。 選擇 **"顯示高級"選項**。
* 啟用重播保護。 保留預設值。
* 複製 TOS 標頭。 未選中該框。
* 隧道監視器。 選中此框。
* 目標 IP。 輸入通過網站到網站連接允許的屬於雲簡單私有雲子網的任何 IP 位址。 確保帕洛阿爾托上的隧道介面（如隧道.20 - 10.64.5.2/32 和隧道.30 - 10.64.6.2/32）允許通過網站到網站 VPN 到達雲簡單私有雲 IP 位址。 有關代理的，請參閱以下配置。
* 配置 檔。 選擇監視器設定檔。

代理 ID 選項卡：按一下**IPv4** > **添加**並配置以下內容：

* 代理 ID。 輸入有趣的流量的任何名稱。 在一個 IPsec 隧道中可能攜帶多個代理指示。
* 本機。 指定允許通過網站到網站 VPN 與私有雲子網通信的本地本地子網。
* 遠端。 指定允許與本地子網通信的私有雲遠端子網。
* 通訊協定。 選擇**任何**。

重複上述步驟以創建另一個 IPsec 隧道，用於輔助 CloudSimple VPN 對等體。

## <a name="references"></a>參考

在思科 ASA 上配置 NAT：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa84/configuration/guide/asa_84_cli_config/nat_objects.html" target="_blank">思科 ASA 5500 系列配置指南</a>

思科 ASA 上支援的 IKEv1 和 IKEv2 屬性：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa90/configuration/guide/asa_90_cli_config/vpn_ike.html#21661" target="_blank">思科 ASA 系列 CLI 配置指南</a>

在 Cisco ASA 上配置 IPsec 網站到網站 VPN，版本為 8.4 及更高版本：

<a href="https://www.cisco.com/c/en/us/support/docs/security/asa-5500-x-series-next-generation-firewalls/119141-configure-asa-00.html#anc8" target="_blank">使用 ASA 上的 ASDM 或 CLI 配置 IKEv1 IPsec 網站到網站隧道</a>

在 Azure 上配置思科自我調整安全設備虛擬 （ASAv）：

<a href="https://www.cisco.com/c/en/us/td/docs/security/asa/asa96/asav/quick-start-book/asav-96-qsg/asav-azure.html" target="_blank">思科自我調整安全虛擬裝置 （ASAv） 快速入門手冊</a>

在帕洛阿爾托使用代理 ID 配置網站到網站 VPN：

[佈建網站到網站 VPN](https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn#)

設置隧道監視器：

[設置隧道監控](https://docs.paloaltonetworks.com/pan-os/7-1/pan-os-admin/vpns/set-up-tunnel-monitoring.html)

IKE 閘道或 IPsec 隧道操作：

<a href="https://docs.paloaltonetworks.com/pan-os/9-0/pan-os-admin/vpns/set-up-site-to-site-vpn/enabledisable-refresh-or-restart-an-ike-gateway-or-ipsec-tunnel#" target="_blank">啟用/禁用、刷新或重新開機 IKE 閘道或 IPsec 隧道</a>
