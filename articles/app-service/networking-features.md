---
title: 網路功能
description: 瞭解 Azure 應用服務中的網路功能，以及網路安全或功能需要哪些功能。
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bc3d35830d833c0223a400140c53e583d2f6ed37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475420"
---
# <a name="app-service-networking-features"></a>應用服務網路功能

Azure 應用服務中的應用程式可以通過多種方式部署。 預設情況下，應用服務託管應用可直接存取互聯網，並且只能訪問互聯網託管終結點。 但是，許多客戶應用程式需要控制入站和出站網路流量。 應用服務中有幾個功能可用於滿足這些需求。 挑戰在於知道應該使用什麼功能來解決給定的問題。 本文檔旨在説明客戶根據一些示例用例確定應該使用哪些功能。

Azure 應用服務有兩種主要部署類型。 有多租戶公共服務，在免費、共用、基本、標準、高級和高級 V2 定價 SKU 中託管應用服務方案。 然後是單個租戶應用服務環境 （ASE），它直接託管 Azure 虛擬網路 （VNet） 中的隔離 SKU 應用服務方案。 如果您在多租戶服務或 ASE 中，則使用的功能將有所不同。 

## <a name="multi-tenant-app-service-networking-features"></a>多租戶應用服務網路功能 

Azure 應用服務是分散式系統。 處理傳入 HTTP/HTTPS 請求的角色稱為前端。 承載客戶工作負載的角色稱為工作角色。 應用服務部署中的所有角色都存在於多租戶網路中。 由於同一應用服務規模單元中有許多不同的客戶，因此無法將應用服務網路直接連接到您的網路。 我們需要的功能來處理應用程式通信的不同方面，而不是連接網路。 處理應用請求的功能不能用於從應用進行調用時的問題。 同樣，解決應用調用問題的功能不能用於向應用解決問題。  

| 入站功能 | 出站功能 |
|---------------------|-------------------|
| 應用分配的位址 | 混合式連線 |
| 存取限制 | 閘道需要 VNet 集成 |
| 服務端點 | VNet 整合 |

除非另有說明，否則所有功能都可以一起使用。 您可以混合這些功能來解決您的各種問題。

## <a name="use-case-and-features"></a>用例和功能

對於任何給定的用例，可以有幾種方法可以解決此問題。  要使用的正確功能有時不僅僅是用例本身的原因。 以下入站用例建議如何使用應用服務網路功能來解決有關控制流量到應用的問題。 
 
| 入站用例 | 功能 |
|---------------------|-------------------|
| 支援應用基於 IP 的 SSL 需求 | 應用分配的位址 |
| 未共用的應用專用入站位址 | 應用分配的位址 |
| 限制從一組定義良好的位址訪問應用 | 存取限制 |
| 從 VNet 中的資源限制對應用的訪問 | 服務端點 </br> ILB ASE </br> 專用終結點（預覽） |
| 在 VNet 中的專用 IP 上公開我的應用 | ILB ASE </br> 具有服務終結點的應用程式閘道上入站專用 IP </br> 服務端點（預覽） |
| 使用 WAF 保護我的應用 | 應用程式閘道 + ILB ASE </br> 具有服務端點的應用程式閘道 </br> 具有訪問限制的 Azure 前門 |
| 載入不同區域中應用的流量 | 具有訪問限制的 Azure 前門 | 
| 同一區域中的負載平衡流量 | [具有服務端點的應用程式閘道][appgwserviceendpoints] | 

以下出站用例建議如何使用應用服務網路功能來解決應用的出站訪問需求。 

| 出站用例 | 功能 |
|---------------------|-------------------|
| 訪問同一區域中的 Azure 虛擬網路中的資源 | VNet 整合 </br> ASE |
| 訪問不同區域中的 Azure 虛擬網路中的資源 | 閘道需要 VNet 集成 </br> ASE 和 VNet 對等互連 |
| 訪問使用服務終結點保護的資源 | VNet 整合 </br> ASE |
| 訪問未連接到 Azure 的私人網路絡中的資源 | 混合式連線 |
| 跨 ExpressRoute 電路訪問資源 | VNet 整合 </br> ASE | 
| 從 Web 應用保護出站流量 | VNet 集成和網路安全性群組 </br> ASE | 
| 從 Web 應用路由出站流量 | VNet 集成和路由表 </br> ASE | 


### <a name="default-networking-behavior"></a>預設網路行為

Azure 應用服務擴展單元支援每個部署中的許多客戶。 "免費"和"共用 SKU"計畫在多租戶工作人員上託管客戶工作負載。 基本及以上計畫託管客戶工作負載，這些工作負載專用於一個應用服務方案 （ASP）。 如果您有標準應用服務方案，則該計畫中的所有應用都將在同一工作人員上運行。 如果橫向擴展了輔助角色，則該 ASP 中的所有應用都將在 ASP 中的每個實例的新工作執行緒上複製。 用於 Premiumv2 的工時與其他計畫中使用的工作人員不同。 每個應用服務部署都有一個 IP 位址，用於該應用服務部署中應用的所有入站流量。 但是，有 4 到 11 個位址用於進行出站呼叫。 這些位址由該應用服務部署中的所有應用共用。 出站位址因工作類型而異。 這意味著免費、共用、基本、標準和高級 ASP 使用的位址與用於 Premiumv2 ASP 的出站呼叫的位址不同。 如果查看應用的屬性，則可以看到應用使用的入站位址和出站位址。 如果需要使用 IP ACL 鎖定依賴項，請使用可能的出站位址。 

![應用程式屬性](media/networking-features/app-properties.png)

應用服務具有許多用於管理服務的終結點。  這些位址在單獨的文檔中發佈，並且也位於 AppService 管理 IP 服務標記中。 應用服務管理標記僅用於應用服務環境 （ASE），您需要在其中允許此類流量。 應用服務入站位址在 AppService IP 服務標記中跟蹤。 沒有包含應用服務使用的出站位址的 IP 服務標記。 

![應用服務入站和出站圖](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>應用分配的位址 

應用分配的位址功能是基於 IP 的 SSL 功能的一個分支，可通過使用應用設置 SSL 進行訪問。 此功能可用於基於 IP 的 SSL 調用，但它也可用於為應用提供只有其擁有的位址。 

![應用分配的位址圖](media/networking-features/app-assigned-address.png)

使用應用分配的位址時，流量仍會通過處理應用服務縮放單元中所有傳入流量的前端角色。 但是，分配給應用的位址僅由你的應用使用。 此功能的用例是：

* 支援應用基於 IP 的 SSL 需求
* 為應用設置未與其他內容共用的專用位址

通過[配置基於 IP 的 SSL][appassignedaddress]的教程，您可以瞭解如何在應用上設置位址。 

### <a name="access-restrictions"></a>存取限制 

訪問限制功能允許您根據發起 IP 位址篩選**入站**請求。 篩選操作發生在從應用運行的工作角色上游的前端角色上。 由於前端角色來自輔助角色，因此訪問限制功能可被視為應用的網路級保護。 該功能允許您生成按優先順序順序計算的允許和拒絕位址塊的清單。 它類似于 Azure 網路中存在的網路安全性群組 （NSG） 功能。  您可以在 ASE 或多租戶服務中使用此功能。 當與 ILB ASE 一起使用時，可以限制從專用位址塊進行訪問。

![存取限制](media/networking-features/access-restrictions.png)

訪問限制功能有助於在您希望限制可用於訪問應用的 IP 位址的情況下。 此功能的用例包括：

* 限制從一組定義良好的位址訪問應用 
* 限制通過負載平衡服務（如 Azure 前門）的訪問。 如果要鎖定到 Azure 前門的入站流量，請創建規則以允許流量從 147.243.0.0/16 和 2a01：111：2050：：/44。 

![前門的檢修限制](media/networking-features/access-restrictions-afd.png)

如果要鎖定對應用的訪問，以便只能從 Azure 虛擬網路 （VNet） 中的資源訪問該應用，則需要在 VNet 中的任何源上使用靜態公共位址。 如果資源沒有公共位址，則應改用服務終結點功能。 瞭解如何啟用此功能與[配置訪問限制][iprestrictions]的教程。

### <a name="service-endpoints"></a>服務端點

服務終結點允許您鎖定對應用**的入站**訪問，以便源位址必須來自您選擇的一組子網。 此功能與 IP 訪問限制結合使用。 服務終結點設置與 IP 訪問限制相同的使用者體驗。 您可以構建允許/拒絕訪問規則清單，其中包括 VNet 中的公共位址和子網。 此功能支援以下方案：

![服務端點](media/networking-features/service-endpoints.png)

* 使用應用設置應用程式閘道以鎖定應用的入站流量
* 限制對 VNet 中的資源的訪問。 這可能包括 VM、ASEs，甚至包括使用 VNet 集成的其他應用 

![具有應用程式閘道的服務終結點](media/networking-features/service-endpoints-appgw.png)

您可以在"[佈建服務終結點訪問限制][serviceendpoints]"教程中瞭解有關使用應用佈建服務終結點的更多資訊

### <a name="private-endpoint-preview"></a>專用終結點（預覽）

專用終結點是一個網路介面，通過 Azure 專用連結將您私下安全地連接到 Web 應用。 專用終結點使用來自 VNet 的私人 IP 位址，有效地將 Web 應用引入 VNet。 此功能僅適用于 Web 應用**的入站**流。
[為 Azure Web 應用使用專用終結點（預覽）][privateendpoints]
 
### <a name="hybrid-connections"></a>混合式連線

應用服務混合連接使應用能夠對指定的 TCP 終結點進行**出站**調用。 終結點可以是本地、VNet 或允許從站流量到埠 443 上的 Azure 的任何位置。 此功能要求在 Windows 伺服器 2012 或較新的主機上安裝稱為混合連線管理員 （HCM） 的中繼代理。 HCM 需要能夠在埠 443 處到達 Azure 中繼。 可以從門戶中的應用服務混合連接 UI 下載 HCM。 

![混合連接網路流](media/networking-features/hybrid-connections.png)

應用服務混合連接功能基於 Azure 中繼混合連接功能構建。 應用服務使用功能的專用形式，該功能僅支援從應用向 TCP 主機和埠進行出站調用。 此主機和埠只需在安裝 HCM 的主機上解析。 當應用在應用服務中對混合連接中定義的主機和埠執行 DNS 查找時，流量將自動重定向到通過混合連接並退出混合連線管理員。 要瞭解有關混合連接的更多內容，請閱讀有關[應用服務混合連接][hybridconn]的文檔

此功能通常用於：

* 使用 VPN 或 ExpressRoute 訪問未連接到 Azure 的私人網路絡中的資源
* 支援本地應用的提升和移位到應用服務，而無需同時移動支援資料庫  
* 安全地提供對每個混合連接單個主機和埠的訪問。 大多數網路功能都具有對網路的開放訪問，並且使用混合連接，您只能具有您可以訪問的單個主機和埠。
* 涵蓋其他出站連接方法未涵蓋的方案
* 在應用服務中執行開發，其中應用可以輕鬆地利用本地資源 

由於該功能允許訪問本地資源，而無需入站防火牆漏洞，因此深受開發人員的歡迎。 其他出站應用服務網路功能與 Azure 虛擬網路非常相關。 混合連接不依賴于通過 VNet，可用於更廣泛的網路需求。 請務必注意，"應用服務混合連接"功能不關心或不知道您在上面執行的操作。 也就是說，您可以使用它訪問大型電腦上的資料庫、Web 服務或任意 TCP 通訊端。 該功能實質上是隧道 TCP 資料包。 

雖然混合連接是開發的熱門，但它也用於許多生產應用程式。 它非常適合訪問 Web 服務或資料庫，但不適用於涉及創建許多連接的情況。 

### <a name="gateway-required-vnet-integration"></a>閘道需要 VNet 集成 

閘道所需的應用服務 VNet 集成功能使應用能夠將**出站**要求傳送到 Azure 虛擬網路。 此功能的工作原理是，使用點對點 VPN 將應用正在運行的主機連接到 VNet 上的虛擬網路閘道。 配置該功能時，你的應用會獲取分配給每個實例的點對點位址之一。 此功能使您能夠訪問任何區域中經典或資源管理器 VNet 中的資源。 

![閘道需要 VNet 集成](media/networking-features/gw-vnet-integration.png)

此功能解決了訪問其他 VNet 中的資源的問題，甚至可用於通過 VNet 連接到其他 VNet 甚至本地。 它不適用於與 ExpressRoute 連接的 VNet，但適用于網站到網站 VPN 連接的網路。 在應用服務環境 （ASE） 中應用使用此功能通常不合適，因為 ASE 已在 VNet 中。 此功能解決的用例包括：

* 在 Azure 虛擬網路中訪問私有 IP 上的資源 
* 如果存在網站到網站 VPN，則在本地訪問資源 
* 訪問對等 VNet 中的資源 

啟用此功能後，你的應用將使用目標 VNet 配置的 DNS 伺服器。 您可以在[有關應用服務 VNet 集成][vnetintegrationp2s]的文檔中瞭解有關此功能的更多內容。 

### <a name="vnet-integration"></a>VNet 整合

閘道所需的 VNet 集成功能非常有用，但仍不能解決通過 ExpressRoute 訪問資源的問題。 除了需要跨 ExpressRoute 連接進行訪問外，還需要應用能夠呼叫服務終結點安全服務。 為了解決這兩個額外的需求，添加了另一個 VNet 集成功能。 新的 VNet 集成功能使您能夠將應用的後端放在同一區域中的資源管理器 VNet 中的子網中。 此功能在已在 VNet 中的應用服務環境中不可用。 此功能支援：

* 訪問同一區域中的資源管理器 VNets 中的資源
* 訪問使用服務終結點保護的資源 
* 訪問可通過 ExpressRoute 或 VPN 連接訪問的資源
* 保護所有出站流量 
* 強制隧道所有出站流量。 

![VNet 整合](media/networking-features/vnet-integration.png)

要瞭解有關此功能的更多內容，請閱讀[應用服務 VNet 集成][vnetintegration]方面的文檔。

## <a name="app-service-environment"></a>App Service 環境 

應用服務環境 （ASE） 是在 VNet 中運行的 Azure 應用服務的單個租戶部署。 ASE 支援用例，例如：

* 訪問 VNet 中的資源
* 跨快速路由訪問資源
* 在 VNet 中使用專用位址公開應用 
* 跨服務終結點訪問資源 

使用 ASE 時，您無需使用 VNet 集成或服務終結點等功能，因為 ASE 已在 VNet 中。 如果要通過服務終結點訪問 SQL 或存儲等資源，則在 ASE 子網上啟用服務終結點。 如果要訪問 VNet 中的資源，則無需其他配置。  如果要通過 ExpressRoute 訪問資源，則您已經在 VNet 中，無需配置 ASE 或其內應用上的任何內容。 

由於 ILB ASE 中的應用可以在私人 IP 位址上公開，因此可以輕鬆地添加 WAF 設備，以便僅向 Internet 公開所需的應用，並確保其餘應用的安全。 它有助於輕鬆開發多層應用程式。 

從 ASE 的多租戶服務中還無法顯示某些內容。 其中包括：

* 在私人 IP 位址上公開應用
* 使用不屬於應用的網路控制項保護所有出站流量 
* 在單個租戶服務中託管應用 
* 擴展到多個租戶服務中的實例數更多 
* 載入專用 CA 用戶端憑證，供應用使用專用 CA 安全終結點 
* 強制 TLS 1.1 跨系統中託管的所有應用，而無需在應用級別禁用 
* 為 ASE 中的所有未與任何客戶共用的應用提供專用出站位址 

![VNet 中的 ASE](media/networking-features/app-service-environment.png)

ASE 提供了圍繞獨立和專用應用程式託管的最佳故事，但確實帶來了一些管理挑戰。 在使用可操作的 ASE 之前需要考慮的一些事項包括：
 
 * ASE 在 VNet 中運行，但確實具有 VNet 之外的依賴項。 必須允許這些依賴項。 閱讀[應用服務環境的網路注意事項][networkinfo]
 * ASE 不會立即像多租戶服務一樣擴展。 您需要預測擴展需求，而不是被動縮放。 
 * ASE 確實具有與其關聯的較高前期成本。 為了充分利用 ASE，您應該計畫將許多工作負載放入一個 ASE 中，而不是將其用於小型工作
 * ASE 中的應用不能限制對 ASE 中某些應用的訪問，而不是其他應用。
 * ASE 位於子網中，任何網路規則都適用于該 ASE 的所有流量。 如果只想為一個應用分配入站流量規則，請使用訪問限制。 

## <a name="combining-features"></a>組合功能 

多租戶服務所指出的功能可以一起使用來解決更複雜的用例。 此處介紹了兩個更常見的用例，但它們只是示例。 通過瞭解各種功能的作用，您可以解決幾乎所有系統體系結構需求。

### <a name="inject-app-into-a-vnet"></a>將應用注入 VNet

一個常見的請求是如何將你的應用放在 VNet 中。 將應用放入 VNet 意味著應用的入站端點和出站終結點位於 VNet 中。 ASE 提供瞭解決此問題的最佳解決方案，但是，您可以通過組合功能獲得多租戶服務中所需的大部分內容。 例如，您可以通過：

* 使用專用入站和出站位址創建應用程式閘道
* 使用服務終結點保護應用的入站流量 
* 使用新的 VNet 集成，使應用的後端位於 VNet 中 

此部署樣式不會為您提供用於 Internet 的出站流量的專用位址，或使您能夠鎖定應用中的所有出站流量。  此部署樣式將為您提供許多僅通過 ASE 獲得的內容。 

### <a name="create-multi-tier-applications"></a>創建多層應用程式

多層應用程式是一個應用程式，其中 API 後端應用程式只能從前端層訪問。 要創建多層應用程式，可以：

* 使用 VNet 集成將前端 Web 應用的後端與 VNet 中的子網連接
* 使用服務終結點保護 API 應用的入站流量，僅來自前端 Web 應用使用的子網

![多層應用](media/networking-features/multi-tier-app.png)

您可以使用來自 API 應用的其他前端應用和服務終結點的 VNet 集成及其子網，讓多個前端應用使用相同的 API 應用。  

<!--Links-->
[appassignedaddress]: https://docs.microsoft.com/azure/app-service/configure-ssl-certificate
[iprestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[serviceendpoints]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[hybridconn]: https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections
[vnetintegrationp2s]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[vnetintegration]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[networkinfo]: https://docs.microsoft.com/azure/app-service/environment/network-info
[appgwserviceendpoints]: https://docs.microsoft.com/azure/app-service/networking/app-gateway-with-service-endpoints
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
