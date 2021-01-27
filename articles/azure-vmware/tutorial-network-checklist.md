---
title: 教學課程 - 網路規劃檢查清單
description: 深入瞭解 Azure VMware 解決方案上網路連線能力和網路埠的網路需求。
ms.topic: tutorial
ms.date: 01/27/2021
ms.openlocfilehash: f493443da617f5f76771e5d1b2b3b190b37f9b5d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918580"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Azure VMware 解決方案的網路規劃檢查清單 

Azure VMware 解決方案提供從內部部署和 Azure 架構環境或資源存取使用者和應用程式的 VMware 私用雲端環境。 連線是透過網路服務（例如 Azure ExpressRoute 和 VPN 連線）來傳遞。 它需要特定的網路位址範圍和防火牆埠來啟用服務。 本文提供您設定網路以正確使用 Azure VMware 解決方案所需的資訊。

在本教學課程中，您將了解：

> [!div class="checklist"]
> * 虛擬網路與 ExpressRoute 線路考量
> * 路由和子網路需求
> * 與服務通訊所需的網路埠
> * Azure VMware 解決方案中的 DHCP 和 DNS 考量

## <a name="prerequisite"></a>必要條件
確定所有閘道（包括 ExpressRoute 提供者的服務）都支援4位元組自發系統編號 (ASN) 。 Azure VMware 解決方案會使用4個位元組的公用 Asn 來進行廣告路由。

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>虛擬網路與 ExpressRoute 線路考量
當您在訂用帳戶中建立虛擬網路連線時，會透過對等互連建立 ExpressRoute 線路、使用授權金鑰，以及您在 Azure 入口網站中要求的對等互連識別碼。 對等互連是私人雲端與虛擬網路之間的私人一對一連線。

> [!NOTE] 
> ExpressRoute 線路不屬於私人雲端部署的一部分。 內部部署 ExpressRoute 線路已超出本文件的範圍。 如果您需要內部部署連線到私人雲端，則可以使用其中一個現有的 ExpressRoute 線路，或在 Azure 入口網站中購買線路。

部署私人雲端時，您會收到 vCenter 和 NSX-T Manager 的 IP 位址。 若要存取這些管理介面，您必須在訂用帳戶的虛擬網路中建立更多資源。 您可以在教學課程中找到建立這些資源及建立 [ExpressRoute 私人對等互連](tutorial-expressroute-global-reach-private-cloud.md)的程序。

私人雲端邏輯網路隨附於預先佈建的 NSX-T。 已為您預先佈建第 0 層閘道和第 1 層閘道。 您可以建立區段，並將其附加至現有的第 1 層閘道，或將其附加至您定義新的第 1 層閘道。 NSX-T 邏輯網路元件提供工作負載之間的東西向連線能力，並提供網際網路和 Azure 服務的南北向連線能力。

## <a name="routing-and-subnet-considerations"></a>路由和子網考量
Azure VMware 解決方案私人雲端會使用 Azure ExpressRoute 連線連接到您的 Azure 虛擬網路。 此高頻寬、低延遲連線可讓您從私人雲端環境存取在 Azure 訂用帳戶中執行的服務。 路由會以邊界閘道協定 (BGP) 為基礎、自動佈建，並依預設啟用每個私人雲端部署。 

Azure VMware 解決方案私人雲端至少需要 `/22` 子網的 CIDR 網路位址區塊，如下所示。 此網路會補足您的內部部署網路。 位址區塊不應與其他虛擬網路中使用的位址區塊重疊，而這些虛擬網路位於您的訂用帳戶和內部部署網路中。 在此位址區塊內，系統會自動佈建管理、佈建和 vMotion 網路。

>[!NOTE]
>您位址區塊允許的範圍為 RFC 1918 私人位址空間 (10.0.0.0/8、172.16.0.0/12、192.168.0.0/16)，但 172.17.0.0/16 除外。

範例 `/22` CIDR 網路位址區塊：`10.10.0.0/22`

子網路：

| 網路使用量             | 子網路 | 範例          |
| ------------------------- | ------ | ---------------- |
| 私人雲端管理  | `/26`  | `10.10.0.0/26`   |
| HCX 管理移轉       | `/26`  | `10.10.0.64/26`  |
| 保留全域觸達     | `/26`  | `10.10.0.128/26` |
| 保留 ExpressRoute     | `/27`  | `10.10.0.192/27` |
| ExpressRoute 對等互連      | `/27`  | `10.10.0.224/27` |
| ESXi 管理           | `/25`  | `10.10.1.0/25`   |
| vMotion 網路           | `/25`  | `10.10.1.128/25` |
| 複寫網路       | `/25`  | `10.10.2.0/25`   |
| vSAN                      | `/25`  | `10.10.2.128/25` |
| HCX 上行連結                | `/26`  | `10.10.3.0/26`   |
| 保留                  | `/26`  | `10.10.3.64/26`  |
| 保留                  | `/26`  | `10.10.3.128/26` |
| 保留                  | `/26`  | `10.10.3.192/26` |



## <a name="required-network-ports"></a>必要的網路連接埠

| 來源 | Destination | 通訊協定 | 連接埠 | 描述  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| 私人雲端 DNS 伺服器 | 內部部署 DNS 伺服器 | UDP | 53 | DNS 用戶端對任何內部部署 DNS 查詢的電腦 vCenter 轉寄要求 (檢查下面的 DNS 區段)  |  
| 內部部署 DNS 伺服器   | 私人雲端 DNS 伺服器 | UDP | 53 | 從內部部署服務到私人雲端 DNS 伺服器的 DNS 用戶端轉寄要求 (檢查下面的 DNS 區段)  |  
| 內部部署網路  | 私人雲端 vCenter Server  | TCP(HTTP)  | 80 | vCenter Server 需要端口80以進行直接 HTTP 連線。 埠80將要求重新導向至 HTTPS 埠443。 如果您使用而不是，則此重新導向會有所説明 `http://server` `https://server` 。  <br><br>WS-Management (也需要開啟埠 443)  <br><br>如果您使用自訂的 Microsoft SQL database，而不是 vCenter Server 上的配套 SQL Server 2008 資料庫，則 SQL Reporting 服務會使用埠80。 當您安裝 vCenter Server 時，安裝程式會提示您變更 vCenter Server 的 HTTP 埠。 將 vCenter Server 的 HTTP 埠變更為自訂值，以確保安裝成功。 Microsoft Internet Information Services (IIS) 也會使用埠80。 請參閱 vCenter Server 與 IIS 之間的埠80之間的衝突。 |  
| 私人雲端管理網路 | 內部部署 Active Directory  | TCP  | 389 | 此埠必須在 vCenter Server 的本機和所有遠端實例上開啟。 此埠是 vCenter Server 群組之目錄服務的 LDAP 埠號碼。 VCenter Server 系統必須系結至埠389，即使您未將此 vCenter Server 實例聯結至連結模式群組也一樣。 如果有其他服務在此埠上執行，最好將它移除，或將其埠變更為不同的埠。 您可以從1025到65535的任何埠上執行 LDAP 服務。  如果此實例做為 Microsoft Windows Active Directory，請將埠號碼從389變更為1025到65535的可用埠。 此連接埠是選用的，用來將內部部署 AD 設定為私人雲端 vCenter 上的身分識別來源。 |  
| 內部部署網路  | 私人雲端 vCenter Server  | TCP(HTTPS)  | 443 | 此埠可讓您從內部部署網路存取 vCenter。 VCenter Server 系統用來接聽 vSphere 用戶端連線的預設通訊埠。 若要讓 vCenter Server 系統接收來自 vSphere 用戶端的資料，請在防火牆中開啟埠443。 VCenter Server 系統也會使用埠443來監視從 SDK 用戶端傳送的資料。 此埠也用於下列服務： WS-Management (也需要開啟埠 80) 。 vSphere 用戶端對 vSphere 更新管理員的存取權。 VCenter Server 的第三方網路管理用戶端連線。 協力廠商網路管理用戶端對主機的存取權。 |  
| Web Browser  | 混合式雲端管理員  | TCP(HTTPS) | 9443 | 適用於混合式雲端管理員系統組態的混合式雲端管理員虛擬裝置管理介面。 |
| 管理員網路  | 混合式雲端管理員 | SSH | 22 | 存取混合式雲端管理員的管理員 SSH。 |
| HCM | 雲端閘道 | TCP(HTTPS) | 8123 | 將主機型複寫服務指示傳送至混合式雲端閘道。 |
| HCM | 雲端閘道 | HTTP  TCP(HTTPS) | 9443 | 使用 REST API 將管理指示傳送到本機混合式雲端閘道。 |
| 雲端閘道 | L2C | TCP(HTTPS) | 443 | 當 L2C 使用與混合式雲端閘道相同的路徑時，將管理指示從雲端閘道傳送至 L2C。 |
| 雲端閘道 | ESXi 主機 | TCP | 80,902 | 管理和 OVF 部署。 |
| 雲端閘道 (本機)| 雲端閘道 (遠端) | UDP | 4500 | IPSEC 的必要項目<br>   網際網路金鑰交換 (IKEv2) 可封裝雙向通道的工作負載。 也支援網路位址轉譯-周遊 (NAT-T)。 |
| 雲端閘道 (本機) | 雲端閘道 (遠端)  | UDP | 500 | IPSEC 的必要項目<br> 雙向通道的網際網路金鑰交換 (ISAKMP)。 |
| 內部部署 vCenter 網域 | 私人雲端管理網路 | TCP | 8000 |  從內部部署 vCenter 至私用雲端 vCenter 的 Vm vMotion   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP 和 DNS 解析考量
在私人雲端環境中執行的應用程式和工作負載需要名稱解析和 DHCP 服務，才能進行查閱和 IP 位址指派。 必須有適當的 DHCP 和 DNS 基礎結構，才能提供這些服務。 您可以設定虛擬機器，以在私人雲端環境中提供這些服務。  

使用 NSX 內建的 DHCP 服務，或使用私人雲端中的本機 DHCP 伺服器，而不是透過 WAN 將廣播 DHCP 流量路由回內部部署。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已瞭解部署 Azure VMware 解決方案私人雲端的考慮和需求。 


準備好適當的網路功能後，請繼續進行下一個教學課程，以建立您的 Azure VMware 解決方案私人雲端。

> [!div class="nextstepaction"]
> [建立 Azure VMware 解決方案私人雲端](tutorial-create-private-cloud.md)
