---
title: 教學課程 - 網路規劃檢查清單
description: 深入了解 Azure VMware 解決方案網路需求的必要條件，以及網路連線能力和網路連接埠的詳細資料。
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 5538f9c5d6543ca312835f4ef6437e413dea231b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91576672"
---
# <a name="networking-planning-checklist-for-azure-vmware-solution"></a>Azure VMware 解決方案的網路規劃檢查清單 

Azure VMware 解決方案提供 VMware 私人雲端環境，可讓使用者和應用程式從內部部署和 Azure 型環境或資源存取。 連線會透過網路服務 (例如 Azure ExpressRoute 和 VPN 連線) 來傳遞，而且需要一些特定的網路位址範圍和防火牆連接埠來啟用服務。 本文提供正確設定網路以與 Azure VMware 解決方案搭配運作所需的資訊。

在本教學課程中，您將了解：

> [!div class="checklist"]
> * 虛擬網路與 ExpressRoute 線路考量
> * 路由和子網路需求
> * 與服務通訊所需的網路連接埠
> * Azure VMware 解決方案中的 DHCP 和 DNS 考量

## <a name="virtual-network-and-expressroute-circuit-considerations"></a>虛擬網路與 ExpressRoute 線路考量
當您在訂用帳戶中建立虛擬網路連線時，會透過對等互連建立 ExpressRoute 線路，並使用在 Azure 入口網站中要求的授權金鑰和對等互連識別碼。 對等互連是私人雲端與虛擬網路之間的私人一對一連線。

> [!NOTE] 
> ExpressRoute 線路不屬於私人雲端部署的一部分。 內部部署 ExpressRoute 線路已超出本文件的範圍。 如果您需要內部部署連線到私人雲端，則可以使用其中一個現有的 ExpressRoute 線路，或在 Azure 入口網站中購買線路。

部署私人雲端時，您會收到 vCenter 和 NSX-T Manager 的 IP 位址。 若要存取這些管理介面，您必須在訂用帳戶的虛擬網路中建立額外的資源。 您可以在教學課程中找到建立這些資源及建立 [ExpressRoute 私人對等互連](tutorial-expressroute-global-reach-private-cloud.md)的程序。

私人雲端邏輯網路隨附於預先佈建的 NSX-T。 已為您預先佈建第 0 層閘道和第 1 層閘道。 您可以建立區段，並將其附加至現有的第 1 層閘道，或將其附加至您定義新的第 1 層閘道。 NSX-T 邏輯網路元件提供工作負載之間的東西向連線能力，並提供網際網路和 Azure 服務的南北向連線能力。

## <a name="routing-and-subnet-considerations"></a>路由和子網考量
AVS 私人雲端會使用 Azure ExpressRoute 連線來連線到您的 Azure 虛擬網路。 此高頻寬、低延遲連線可讓您從私人雲端環境存取在 Azure 訂用帳戶中執行的服務。 路由會以邊界閘道協定 (BGP) 為基礎、自動佈建，並依預設啟用每個私人雲端部署。 

對於子網路，AVS 私人雲端需要至少 `/22` CIDR 網路位址區塊，如下所示。 此網路會補足您的內部部署網路。 位址區塊不應與其他虛擬網路中使用的位址區塊重疊，而這些虛擬網路位於您的訂用帳戶和內部部署網路中。 在此位址區塊內，系統會自動佈建管理、佈建和 vMotion 網路。

範例 `/22` CIDR 網路位址區塊：`10.10.0.0/22`

子網路：

| 網路使用量             | 子網路 | 範例          |
| ------------------------- | ------ | ---------------- |
| 私人雲端管理  | `/26`  | `10.10.0.0/26`   |
| vMotion 網路           | `/25`  | `10.10.1.128/25` |
| VM 工作負載              | `/24`  | `10.10.2.0/24`   |
| ExpressRoute 對等互連      | `/29`  | `10.10.3.8/29`   |


## <a name="required-network-ports"></a>必要的網路連接埠

| 來源 | Destination | 通訊協定 | 連接埠 | 描述  | 
| ------ | ----------- | :------: | :---:| ------------ | 
| 私人雲端 DNS 伺服器 | 內部部署 DNS 伺服器 | UDP | 53 | 來自 PC vCenter 的 DNS 用戶端轉送要求，適用於任何內部部署 DNS 查詢 (請查看下方的 DNS 一節) |  
| 內部部署 DNS 伺服器   | 私人雲端 DNS 伺服器 | UDP | 53 | 從內部部署服務到私人雲端 DNS 伺服器的 DNS 用戶端轉送要求 (請查看下方的 DNS 一節) |  
| 內部部署網路  | 私人雲端 vCenter Server  | TCP(HTTP)  | 80 | vCenter Server 需要連接埠 80 以進行直接 HTTP 連線。 連接埠 80 會將要求重新導向至 HTTPS 連接埠 443。 如果您使用  `http://server`  而不是  `https://server`，則此重新導向會有所幫助。  <br><br>WS-MANAGEMENT (也需要開啟連接埠 443) <br><br>如果您使用自訂的 Microsoft SQL 資料庫，而不是 vCenter Server 上的配套 SQL Server 2008 資料庫，SQL Reporting 服務會使用連接埠 80。 當您安裝 vCenter Server 時，安裝程式會提示您變更 vCenter Server 的 HTTP 連接埠。 將 vCenter Server HTTP 連接埠變更為自訂值，以確保安裝成功。 Microsoft Internet Information Services (IIS) 也會使用連接埠 80。 請參閱連接埠 80 的 vCenter Server 和 IIS 間的衝突。 |  
| 私人雲端管理網路 | 內部部署 Active Directory  | TCP  | 389 | 此連接埠必須在本機和所有 vCenter Server 的遠端執行個體上開啟。 此連接埠是 vCenter Server 群組之目錄服務的 LDAP 連接埠號碼。 VCenter Server 系統必須繫結至連接埠 389，即使您未將此 vCenter Server 執行個體聯結至連結的模式群組也一樣。 如果另一個服務正在此連接埠上執行，最好將其移除，或變更其連接埠。 您可以從 1025 到 65535 的任何連接埠上執行 LDAP 服務。  如果這個執行個體是用來提供 Microsoft Windows Active Directory 服務，請將連接埠號碼從 389 變更為 1025 到 65535 之間可用的連接埠。 此連接埠是選用的，用來將內部部署 AD 設定為私人雲端 vCenter 上的身分識別來源。 |  
| 內部部署網路  | 私人雲端 vCenter Server  | TCP(HTTPS)  | 443 | 此連接埠可讓您從內部部署網路存取 vCenter。 VCenter Server 系統用來接聽來自 vSphere 用戶端之連線的預設連接埠。 若要讓 vCenter Server 系統從 vSphere 用戶端接收資料，請在防火牆中開啟連接埠 443。 VCenter Server 系統也會使用連接埠 443 監視來自 SDK 用戶端的資料傳輸。 此連接埠也會用於下列服務：WS-Management (也需要開啟連接埠 80)。 vSphere 用戶端對 vSphere 更新管理員的存取權。 VCenter Server 的第三方網路管理用戶端連線。 第三方網路管理用戶端存取主機。 |  
| Web Browser  | 混合式雲端管理員  | TCP(HTTPS) | 9443 | 適用於混合式雲端管理員系統組態的混合式雲端管理員虛擬裝置管理介面。 |
| 管理員網路  | 混合式雲端管理員 | SSH | 22 | 存取混合式雲端管理員的管理員 SSH。 |
| HCM | 雲端閘道 | TCP(HTTPS) | 8123 | 將主機型複寫服務指示傳送至混合式雲端閘道。 |
| HCM | 雲端閘道 | HTTP  TCP(HTTPS) | 9443 | 使用 REST API 將管理指示傳送到本機混合式雲端閘道。 |
| 雲端閘道 | L2C | TCP(HTTPS) | 443 | 當 L2C 使用與混合式雲端閘道相同的路徑時，將管理指示從雲端閘道傳送至 L2C。 |
| 雲端閘道 | ESXi 主機 | TCP | 80,902 | 管理和 OVF 部署。 |
| 雲端閘道 (本機)| 雲端閘道 (遠端) | UDP | 4500 | IPSEC 的必要項目<br>   網際網路金鑰交換 (IKEv2) 可封裝雙向通道的工作負載。 也支援網路位址轉譯-周遊 (NAT-T)。 |
| 雲端閘道 (本機) | 雲端閘道 (遠端)  | UDP | 500 | IPSEC 的必要項目<br> 雙向通道的網際網路金鑰交換 (ISAKMP)。 |
| 內部部署 vCenter 網域 | 私人雲端管理網路 | TCP | 8000 |  從內部部署 vCenter 到私人雲端 vCenter 的 VM 之 vMotion   |     

## <a name="dhcp-and-dns-resolution-considerations"></a>DHCP 和 DNS 解析考量
在私人雲端環境中執行的應用程式和工作負載需要名稱解析和 DHCP 服務，才能進行查閱和 IP 位址指派。 必須有適當的 DHCP 和 DNS 基礎結構，才能提供這些服務。 您可以設定虛擬機器，以在私人雲端環境中提供這些服務。  

使用 NSX 內建的 DHCP 服務，或使用私人雲端中的本機 DHCP 伺服器，而不是透過 WAN 將廣播 DHCP 流量路由回內部部署。


## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解部署 Azure VMware 解決方案私人雲端的相關考量和需求。 


準備好適當的網路功能後，請繼續進行下一個教學課程，以建立您的 Azure VMware 解決方案私人雲端。

> [!div class="nextstepaction"]
> [建立 Azure VMware 解決方案私人雲端](tutorial-create-private-cloud.md)
