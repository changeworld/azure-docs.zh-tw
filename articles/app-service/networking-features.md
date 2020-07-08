---
title: 網路功能
description: 瞭解 Azure App Service 中的網路功能，以及您的網路需要哪些功能來保護安全性或功能。
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 03/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 87a8291387d50fae7027d007eedf49fa55b39b74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85052767"
---
# <a name="app-service-networking-features"></a>App Service 網路功能

Azure App Service 中的應用程式可以透過多種方式進行部署。 根據預設，App Service 裝載的應用程式可直接存取網際網路，而且只能連線到網際網路託管的端點。 不過，許多客戶應用程式都需要控制輸入和輸出網路流量。 App Service 中有幾項功能可滿足這些需求。 挑戰在於知道應該使用哪一種功能來解決特定的問題。 本檔的目的是要協助客戶根據某些範例使用案例來判斷應該使用哪一項功能。

Azure App Service 有兩種主要的部署類型。 有多租使用者公用服務，可在免費、共用、基本、標準、Premium 和 Premiumv2 定價 Sku 中裝載 App Service 計畫。 然後有單一租使用者 App Service 環境（ASE），它會直接在您的 Azure 虛擬網路（VNet）中裝載隔離的 SKU App Service 方案。 如果您是在多租使用者服務或 ASE 中，您使用的功能會有所不同。 

## <a name="multi-tenant-app-service-networking-features"></a>多租使用者 App Service 網路功能 

Azure App Service 是分散式系統。 處理傳入 HTTP/HTTPS 要求的角色稱為「前端」。 裝載客戶工作負載的角色稱為「背景工作」。 App Service 部署中的所有角色都存在於多租使用者網路中。 因為相同的 App Service 縮放單位中有許多不同的客戶，所以您無法將 App Service 網路直接連線到您的網路。 我們不會連接網路，而是需要一些功能來處理應用程式通訊的不同層面。 處理應用程式要求的功能無法用來解決從您的應用程式進行呼叫時的問題。 同樣地，解決應用程式呼叫問題的功能無法用來解決應用程式的問題。  

| 輸入功能 | 輸出功能 |
|---------------------|-------------------|
| 應用程式指派的位址 | 混合式連線 |
| 存取限制 | 閘道所需的 VNet 整合 |
| 服務端點 | VNet 整合 |

除非另有說明，否則所有功能都可以一起使用。 您可以混用這些功能來解決各種問題。

## <a name="use-case-and-features"></a>使用案例和功能

針對任何指定的使用案例，有幾種方法可以解決此問題。  使用的正確功能有時是因為只是使用案例本身以外的原因。 下列輸入使用案例會建議如何使用 App Service 的網路功能，以解決控制傳送至您應用程式之流量的問題。 
 
| 輸入使用案例 | 功能 |
|---------------------|-------------------|
| 針對您的應用程式支援以 IP 為基礎的 SSL 需求 | 應用程式指派的位址 |
| 您的應用程式未共用專屬輸入位址 | 應用程式指派的位址 |
| 限制從一組定義完善的位址存取您的應用程式 | 存取限制 |
| 限制從 VNet 中的資源存取我的應用程式 | 服務端點 </br> ILB ASE </br> 私人端點（預覽） |
| 在我的 VNet 中的私人 IP 上公開我的應用程式 | ILB ASE </br> 在具有服務端點的應用程式閘道上輸入的私人 IP </br> 服務端點（預覽） |
| 使用 WAF 保護我的應用程式 | 應用程式閘道 + ILB ASE </br> 具有服務端點的應用程式閘道 </br> 具有存取限制的 Azure Front 門 |
| 將流量負載平衡至不同區域中的應用程式 | 具有存取限制的 Azure Front 門 | 
| 相同區域中的負載平衡流量 | [具有服務端點的應用程式閘道][appgwserviceendpoints] | 

下列輸出使用案例會建議如何使用 App Service 的網路功能，以解決應用程式的輸出存取需求。 

| 輸出使用案例 | 功能 |
|---------------------|-------------------|
| 存取相同區域中 Azure 虛擬網路中的資源 | VNet 整合 </br> ASE |
| 在不同區域的 Azure 虛擬網路中存取資源 | 閘道所需的 VNet 整合 </br> ASE 和 VNet 對等互連 |
| 存取以服務端點保護的資源 | VNet 整合 </br> ASE |
| 存取未連線至 Azure 之私人網路中的資源 | 混合式連線 |
| 跨 ExpressRoute 線路存取資源 | VNet 整合 </br> ASE | 
| 保護來自 web 應用程式的輸出流量 | VNet 整合和網路安全性群組 </br> ASE | 
| 從您的 web 應用程式路由傳送輸出流量 | VNet 整合和路由表 </br> ASE | 


### <a name="default-networking-behavior"></a>預設網路功能行為

Azure App Service 的縮放單位支援每個部署中的許多客戶。 「免費」和「共用」 SKU 方案會在多租使用者背景工作角色上裝載客戶工作負載。 「基本」和「上述」方案會裝載僅供一個 App Service 方案（ASP）專用的客戶工作負載。 如果您有標準 App Service 方案，則該方案中的所有應用程式都會在相同的背景工作上執行。 如果您相應放大背景工作，則該 ASP 中的所有應用程式都會針對 ASP 中的每個實例複寫至新的背景工作角色。 用於 Premiumv2 的工作者與用於其他方案的背景工作不同。 每個 App Service 部署都有一個 IP 位址，用於該 App Service 部署中應用程式的所有輸入流量。 不過，有4到11個位址可用來進行撥出電話的地方。 這些位址會由該 App Service 部署中的所有應用程式共用。 輸出位址會根據不同的背景工作類型而有所不同。 這表示免費、共用、基本、標準和高階 Asp 所使用的位址，與從 Premiumv2 Asp 撥出電話所使用的位址不同。 如果您查看應用程式的屬性，您可以看到您的應用程式所使用的輸入和輸出位址。 如果您需要鎖定與 IP ACL 的相依性，請使用 possibleOutboundAddresses。 

![應用程式屬性](media/networking-features/app-properties.png)

App Service 有數個用來管理服務的端點。  這些位址會在個別的檔中發佈，而且也會在 AppServiceManagement IP 服務標記中。 AppServiceManagement 標記僅適用于您需要允許這類流量的 App Service 環境（ASE）。 系統會在 AppService IP 服務標籤中追蹤 App Service 輸入位址。 沒有 IP 服務標記包含 App Service 所使用的輸出位址。 

![App Service 輸入和輸出圖表](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>應用程式指派的位址 

應用程式指派的位址功能是以 IP 為基礎的 SSL 功能 offshoot，並透過設定應用程式的 SSL 來存取。 這項功能可用於以 IP 為主的 SSL 呼叫，但它也可以用來為您的應用程式提供只有其所擁有的位址。 

![應用程式指派的位址圖表](media/networking-features/app-assigned-address.png)

當您使用應用程式指派的位址時，您的流量仍然會經歷相同的前端角色，以處理連入 App Service 縮放單位的所有連入流量。 不過，指派給您應用程式的位址僅供您的應用程式使用。 這項功能的使用案例如下：

* 針對您的應用程式支援以 IP 為基礎的 SSL 需求
* 為您的應用程式設定未與任何其他專案共用的專用位址

您可以透過在[Azure App Service 中新增 TLS/SSL 憑證][appassignedaddress]的教學課程，瞭解如何在您的應用程式上設定位址。 

### <a name="access-restrictions"></a>存取限制 

「存取限制」功能可讓您根據原始 IP 位址篩選**輸入**要求。 篩選動作會在從您的應用程式執行所在之背景工作角色上游的前端角色上進行。 由於前端角色是來自背景工作的上游，因此可將存取限制功能視為您應用程式的網路層級保護。 此功能可讓您建立依優先順序評估的允許和拒絕位址區塊清單。 這類似于 Azure 網路中存在的「網路安全性群組」（NSG）功能。  您可以在 ASE 或多租使用者服務中使用這項功能。 與 ILB ASE 搭配使用時，您可以限制來自私人位址區塊的存取。

![存取限制](media/networking-features/access-restrictions.png)

當您想要限制可用來連線到應用程式的 IP 位址時，存取限制功能會有説明。 這項功能的使用案例包括：

* 限制從一組定義完善的位址存取您的應用程式 
* 限制透過負載平衡服務（例如 Azure Front 大門）進入的存取權。 如果您想要鎖定 Azure Front 的輸入流量，請建立規則以允許來自 147.243.0.0/16 和2a01：111：2050：/44 的流量。 

![Front 門的存取限制](media/networking-features/access-restrictions-afd.png)

如果您想要鎖定應用程式的存取權，使其只能從 Azure 虛擬網路（VNet）中的資源連線，您必須在任何來源位於 VNet 中的靜態公用位址。 如果資源沒有公用位址，您應該改用 [服務端點] 功能。 瞭解如何在設定[存取限制][iprestrictions]的教學課程中啟用這項功能。

### <a name="service-endpoints"></a>服務端點

服務端點可讓您鎖定應用程式的**輸入**存取，使來源位址必須來自您選取的一組子網。 這項功能會與 IP 存取限制搭配運作。 服務端點會設定為與 IP 存取限制相同的使用者體驗。 您可以在 Vnet 中建立包含公用位址和子網的存取規則的允許/拒絕清單。 這項功能支援下列案例：

![服務端點](media/networking-features/service-endpoints.png)

* 使用您的應用程式設定應用程式閘道，以鎖定應用程式的輸入流量
* 將應用程式的存取限制為 VNet 中的資源。 這可能包括 Vm、Ase，甚至是其他使用 VNet 整合的應用程式 

![應用程式閘道的服務端點](media/networking-features/service-endpoints-appgw.png)

在設定[服務端點存取限制][serviceendpoints]的教學課程中，您可以深入瞭解如何設定應用程式的服務端點

### <a name="private-endpoint-preview"></a>私人端點（預覽）

私人端點是一種網路介面，可讓您以私人且安全的方式，透過 Azure 私人連結連線到您的 Web 應用程式。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將 Web 應用程式帶入您的 VNet。 這項功能僅適用于您 Web 應用程式的**輸入**流量。
[使用 Azure Web 應用程式的私人端點 (預覽)][privateendpoints]
 
### <a name="hybrid-connections"></a>混合式連線

App Service 混合式連線可讓您的應用程式對指定的 TCP 端點進行**輸出**呼叫。 端點可以是內部部署、在 VNet 中，或在埠443上允許對 Azure 輸出流量的任何位置。 此功能需要在 Windows Server 2012 或更新版本的主機上安裝稱為混合式連線管理員（HCM）的轉送代理程式。 HCM 必須能夠連線到埠443上的 Azure 轉送。 您可以從入口網站中的 App Service 混合式連線] UI 下載 HCM。 

![混合式連接網路流程](media/networking-features/hybrid-connections.png)

App Service 混合式連線功能是以 Azure 轉送混合式連接功能為基礎。 App Service 使用一種特殊形式的功能，僅支援從您的應用程式對 TCP 主機和埠進行輸出呼叫。 此主機和埠只需要在已安裝 HCM 的主機上解析。 當 App Service 中的應用程式在您的混合式連線中定義的主機和埠上進行 DNS 查閱時，流量會自動重新導向至混合式連線並輸出混合式連線管理員。 若要深入瞭解混合式連線，請閱讀[App Service 混合][hybridconn]式連線的相關檔

這項功能通常用於：

* 使用 VPN 或 ExpressRoute 存取未連線至 Azure 之私人網路中的資源
* 支援將內部部署應用程式隨即轉移至 App Service，而不需要同時移動支援的資料庫  
* 安全地為每個混合式連線提供單一主機和埠的存取權。 大部分的網路功能都開放存取網路，而使用混合式連線時，您只有單一主機和埠可以連接。
* 涵蓋其他輸出連線方法未涵蓋的案例
* 在應用程式可輕鬆運用內部部署資源的 App Service 中執行開發 

因為此功能可讓您存取內部部署資源，而不需要輸入防火牆漏洞，所以開發人員很熟悉它。 其他輸出 App Service 網路功能與 Azure 虛擬網路相關。 混合式連線並不會相依于執行 VNet，而且可以用於更多的網路需求。 請務必注意，App Service 混合式連線] 功能並不在意或瞭解您在其上執行的作業。 也就是說，您可以用它來存取資料庫、web 服務或大型主機上的任意 TCP 通訊端。 此功能基本上會通道 TCP 封包。 

雖然混合式連線在開發上很普遍，但也會用於許多生產應用程式中。 這很適合用來存取 web 服務或資料庫，但不適用於建立許多連接的情況。 

### <a name="gateway-required-vnet-integration"></a>閘道所需的 VNet 整合 

App Service VNet 整合功能所需的閘道，可讓您的應用程式對 Azure 虛擬網路發出**輸出**要求。 此功能的運作方式是將您的應用程式執行所在的主機，連接到具有點對站 VPN 的 VNet 上的虛擬網路閘道。 當您設定此功能時，您的應用程式會取得指派給每個實例的其中一個點對站位址。 這項功能可讓您存取任何區域中傳統或 Resource Manager Vnet 中的資源。 

![閘道所需的 VNet 整合](media/networking-features/gw-vnet-integration.png)

這項功能可解決存取其他 Vnet 中資源的問題，甚至可以用來透過 VNet 連線至其他 Vnet 或甚至是內部部署。 它不適用於 ExpressRoute 連線 Vnet，而是使用站對站 VPN 連線網路。 通常不適合從 App Service 環境（ASE）中的應用程式使用這項功能，因為 ASE 已經在您的 VNet 中。 這項功能可解決的使用案例如下：

* 存取 Azure 虛擬網路中私人 Ip 的資源 
* 如果有站對站 VPN，則存取內部部署資源 
* 存取對等互連 Vnet 中的資源 

啟用這項功能時，您的應用程式將會使用已設定目的地 VNet 的 DNS 伺服器。 您可以在有關[App Service VNet 整合][vnetintegrationp2s]的檔中閱讀更多有關這項功能的資訊。 

### <a name="vnet-integration"></a>VNet 整合

閘道所需的 VNet 整合功能非常有用，但仍無法解決跨 ExpressRoute 存取資源的問題。 在需要跨越 ExpressRoute 連線的最上層，應用程式必須能夠呼叫服務端點保護的服務。 為了解決這兩個額外的需求，已新增另一個 VNet 整合功能。 新的 VNet 整合功能可讓您將應用程式的後端放在相同區域中 Resource Manager VNet 的子網中。 這項功能無法從已在 VNet 中的 App Service 環境使用。 這項功能可讓：

* 存取相同區域中 Resource Manager Vnet 中的資源
* 存取以服務端點保護的資源 
* 存取可跨 ExpressRoute 或 VPN 連線存取的資源
* 保護所有輸出流量 
* 強制通道所有輸出流量。 

![VNet 整合](media/networking-features/vnet-integration.png)

若要深入瞭解這項功能，請閱讀[App Service VNet 整合][vnetintegration]上的檔。

## <a name="app-service-environment"></a>App Service 環境 

App Service 環境（ASE）是在您的 VNet 中執行之 Azure App Service 的單一租使用者部署。 ASE 可讓您使用下列案例：

* 存取 VNet 中的資源
* 跨 ExpressRoute 存取資源
* 在您的 VNet 中使用私人位址公開您的應用程式 
* 跨服務端點存取資源 

使用 ASE 時，您不需要使用 VNet 整合或服務端點等功能，因為 ASE 已經在您的 VNet 中。 如果您想要透過服務端點存取 SQL 或儲存體之類的資源，請在 ASE 子網上啟用服務端點。 如果您想要存取 VNet 中的資源，則不需要進行其他設定。  如果您想要透過 ExpressRoute 存取資源，您已在 VNet 中，而且不需要在 ASE 或其中的應用程式上進行任何設定。 

由於 ILB ASE 中的應用程式可以在私人 IP 位址上公開，因此您可以輕鬆地新增 WAF 裝置，只公開您想要網際網路的應用程式，並讓其餘的安全。 它本身可輕鬆地開發多層式應用程式。 

來自 ASE 的多租使用者服務尚不可能有一些專案。 這些專案包括：

* 在私人 IP 位址上公開您的應用程式
* 使用不屬於您應用程式的網路控制來保護所有輸出流量 
* 在單一租使用者服務中裝載您的應用程式 
* 相應增加至多租使用者服務中的多個實例 
* 載入私人 CA 用戶端憑證，以供您的應用程式使用私人 CA 受保護的端點 
* 在裝載于系統的所有應用程式中強制執行 TLS 1.1，而不需在應用層級停用任何功能 
* 針對 ASE 中所有不與任何客戶共用的應用程式，提供專用的輸出位址 

![VNet 中的 ASE](media/networking-features/app-service-environment.png)

ASE 提供有關隔離和專用應用程式裝載的最佳案例，但有一些管理挑戰。 使用作業 ASE 之前應考慮的事項包括：
 
 * ASE 會在您的 VNet 內執行，但在 VNet 外部則具有相依性。 必須允許這些相依性。 深入瞭解[App Service 環境的網路功能考慮][networkinfo]
 * ASE 不會和多租使用者服務一樣直接進行調整。 您必須預測調整需求，而不是被動調整。 
 * ASE 具有與它相關聯的最上層成本。 為了充分運用您的 ASE，您應該規劃將許多工作負載放入一個 ASE，而不是將它用於小型工作
 * ASE 中的應用程式無法限制對 ASE 中某些應用程式的存取，而不是其他 app。
 * ASE 位於子網中，且所有網路規則都會套用至進出該 ASE 的所有流量。 如果您只想要指派一個應用程式的輸入流量規則，請使用存取限制。 

## <a name="combining-features"></a>結合功能 

針對多租使用者服務所記下的功能可以一起使用，以解決更複雜的使用案例。 這裡描述兩個較常見的使用案例，但它們只是範例。 藉由瞭解各種功能的作用，您可以解決幾乎所有的系統架構需求。

### <a name="inject-app-into-a-vnet"></a>將應用程式插入 VNet

常見的要求是關於如何將您的應用程式放在 VNet 中。 將您的應用程式放入 VNet，表示應用程式的輸入和輸出端點都在 VNet 中。 ASE 提供解決此問題的最佳解決方案，但是您可以藉由結合功能，在多租使用者服務中取得所需的大部分內容。 例如，您可以透過下列方式，僅裝載內部網路的應用程式，具有私人的輸入和輸出位址：

* 建立具有私用輸入和輸出位址的應用程式閘道
* 使用服務端點保護應用程式的輸入流量 
* 使用新的 VNet 整合，讓應用程式的後端位於您的 VNet 中 

此部署樣式不會提供您連到網際網路的輸出流量專用位址，或讓您能夠鎖定來自您應用程式的所有輸出流量。  這種部署樣式可提供您只使用 ASE 取得的許多功能。 

### <a name="create-multi-tier-applications"></a>建立多層式應用程式

多層式應用程式是一種應用程式，其中 API 後端應用程式只能從前端層存取。 若要建立多層式應用程式，您可以：

* 使用 VNet 整合，將前端 web 應用程式的後端與 VNet 中的子網連線
* 使用服務端點來保護 API 應用程式的輸入流量，使其僅來自您的前端 web 應用程式所使用的子網

![多層式應用程式](media/networking-features/multi-tier-app.png)

您可以讓多個前端應用程式使用來自其他前端應用程式的 VNet 整合和其子網的 API 應用程式中的服務端點，來使用相同的 API 應用程式。  

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
