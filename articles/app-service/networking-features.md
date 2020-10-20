---
title: 網路功能
description: 瞭解 Azure App Service 中的網路功能，以及網路在安全性或功能方面需要的功能。
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 860b1ac1713ac7afb7db2643d68974b399b5236b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207035"
---
# <a name="app-service-networking-features"></a>App Service 網路功能

Azure App Service 中的應用程式可以透過多種方式進行部署。 根據預設，App Service 裝載的應用程式可直接存取網際網路，而且只能連線至網際網路裝載的端點。 但是，許多客戶應用程式都需要控制輸入和輸出網路流量。 App Service 中有幾項功能可滿足這些需求。 挑戰在於知道應該使用哪一項功能來解決指定的問題。 本檔的目的是協助客戶根據某些範例使用案例來判斷應該使用哪一項功能。

Azure App Service 有兩種主要的部署類型。 有多租使用者公用服務，可在免費、共用、基本、標準、Premium、>premiumv2 和 Premiumv3 定價 Sku 中裝載 App Service 方案。 然後 App Service 環境 (ASE) 的單一租使用者，其會直接在 Azure 虛擬網路 (VNet) 中裝載隔離的 SKU App Service 方案。 如果您在多租使用者服務或 ASE 中，您使用的功能會有所不同。 

## <a name="multi-tenant-app-service-networking-features"></a>多租使用者 App Service 網路功能 

Azure App Service 是分散式系統。 處理傳入 HTTP/HTTPS 要求的角色稱為前端。 裝載客戶工作負載的角色稱為背景工作角色。 App Service 部署中的所有角色都存在於多租使用者網路中。 因為相同的 App Service 縮放單位中有許多不同的客戶，所以您無法直接將 App Service 網路連線到您的網路。 我們不需要連接網路，而是需要一些功能來處理應用程式通訊的不同層面。 處理應用程式要求的功能，在從您的應用程式進行呼叫時，無法用來解決問題。 同樣地，無法使用解決應用程式呼叫問題的功能來解決應用程式的問題。  

| 輸入功能 | 輸出功能 |
|---------------------|-------------------|
| 應用程式指派的位址 | 混合式連線 |
| 存取限制 | 閘道需要 VNet 整合 |
| 服務端點 | VNet 整合 |

除非另有說明，否則所有功能皆可一起使用。 您可以混合使用這些功能來解決各種問題。

## <a name="use-case-and-features"></a>使用案例和功能

針對任何特定的使用案例，有幾種方法可以解決此問題。  使用的正確功能有時是因為使用案例本身以外的原因。 下列輸入使用案例會建議如何使用 App Service 網路功能，來解決控制進入您應用程式之流量的問題。 
 
| 輸入使用案例 | 功能 |
|---------------------|-------------------|
| 針對您的應用程式支援以 IP 為基礎的 SSL 需求 | 應用程式指派的位址 |
| 您的應用程式不是共用的專用輸入位址 | 應用程式指派的位址 |
| 從一組定義完善的位址限制對您應用程式的存取 | 存取限制 |
| 從 VNet 中的資源限制對我的應用程式的存取 | 服務端點 </br> ILB ASE </br> 私人端點 |
| 在我的 VNet 中公開私人 IP 上的應用程式 | ILB ASE </br> 私人端點 </br> 具有服務端點的應用程式閘道上輸入的私人 IP |
| 使用 Web 應用程式防火牆保護我的應用程式 (WAF)  | 應用程式閘道 + ILB ASE </br> 具有私人端點的應用程式閘道 </br> 具有服務端點的應用程式閘道 </br> 具有存取限制的 Azure Front Door |
| 對我的應用程式在不同區域的流量進行負載平衡 | 具有存取限制的 Azure Front Door | 
| 針對相同區域中的流量進行負載平衡 | [具有服務端點的應用程式閘道][appgwserviceendpoints] | 

下列輸出使用案例會建議如何使用 App Service 網路功能，來解決應用程式的輸出存取需求。 

| 輸出使用案例 | 功能 |
|---------------------|-------------------|
| 存取 Azure 虛擬網路輸入相同區域中的資源 | VNet 整合 </br> ASE |
| 存取 Azure 虛擬網路輸入不同區域中的資源 | 閘道需要 VNet 整合 </br> ASE 和 VNet 對等互連 |
| 存取以服務端點保護的資源 | VNet 整合 </br> ASE |
| 存取未連線至 Azure 的私人網路中的資源 | 混合式連線 |
| 跨 ExpressRoute 線路存取資源 | VNet 整合 </br> ASE | 
| 保護來自您 web 應用程式的輸出流量 | VNet 整合和網路安全性群組 </br> ASE | 
| 從您的 web 應用程式路由輸出流量 | VNet 整合和路由表 </br> ASE | 


### <a name="default-networking-behavior"></a>預設網路行為

Azure App Service 的縮放單位支援每個部署中的許多客戶。 免費和共用的 SKU 方案會在多租使用者背景工作角色上裝載客戶工作負載。 基本版和以上方案可裝載僅限一個 App Service 方案 (ASP) 專屬的客戶工作負載。 如果您有標準 App Service 方案，則該方案中的所有應用程式都會在相同的背景工作上執行。 如果您相應放大背景工作角色，則該 ASP 中的所有應用程式都會在您 ASP 中的每個實例的新背景工作角色上進行複寫。 

#### <a name="outbound-addresses"></a>輸出位址

背景工作 Vm 會在 App Service 定價方案的大型部分細分。 免費、共用、基本、標準和 Premium 全都使用相同的背景工作 VM 類型。 >premiumv2 是在另一個 VM 類型上。 Premiumv3 還在另一種 VM 類型上。 每個 VM 系列的變更都有一組不同的輸出位址。 如果您從標準調整為 >premiumv2，您的輸出位址將會變更。 如果您從 >premiumv2 調整為 Premiumv3，您的輸出位址將會變更。 當您從標準級別調整為 >premiumv2 時，有一些較舊的縮放單位會變更輸入和輸出位址。 有幾個位址用於進行輸出呼叫。 您的應用程式用來進行輸出呼叫的輸出位址會列在應用程式的屬性中。 這些位址會由在 App Service 部署的相同背景工作 VM 系列上執行的所有應用程式共用。 如果您想要查看應用程式可能會在該縮放單位中使用的所有可能位址，還有另一個稱為 possibleOutboundAddresses 的屬性會列出它們。 

![應用程式屬性](media/networking-features/app-properties.png)

App Service 有許多用來管理服務的端點。  這些位址會在個別的檔中發行，而且也會在 AppServiceManagement IP 服務標記中。 AppServiceManagement 標記只適用于您需要允許這類流量的 App Service 環境。 App Service 的輸入位址會在 AppService IP 服務標記中進行追蹤。 沒有 IP 服務標記包含 App Service 使用的輸出位址。 

![App Service 輸入和輸出圖表](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>應用程式指派的位址 

應用程式指派的位址功能是以 IP 為基礎的 SSL 功能 offshoot，並藉由使用您的應用程式設定 SSL 來存取。 這項功能可用於以 IP 為基礎的 SSL 呼叫，但是也可以用來為您的應用程式提供只有它所擁有的位址。 

![應用程式指派的位址圖表](media/networking-features/app-assigned-address.png)

當您使用應用程式指派的位址時，您的流量仍會通過相同的前端角色來處理 App Service 縮放單位中的所有連入流量。 不過，指派給您應用程式的位址只會由您的應用程式使用。 這項功能的使用案例如下：

* 針對您的應用程式支援以 IP 為基礎的 SSL 需求
* 為您的應用程式設定未與其他任何人共用的專用位址

您可以透過在 [Azure App Service 中新增 TLS/SSL 憑證][appassignedaddress]的教學課程，瞭解如何在您的應用程式上設定位址。 

### <a name="access-restrictions"></a>存取限制 

存取限制功能可讓您根據來源 IP 位址篩選 **輸入** 要求。 篩選動作會在與您的應用程式執行所在的背景工作角色上游的前端角色上進行。 由於前端角色是來自背景工作角色，因此可將存取限制功能視為您應用程式的網路層級保護。 這項功能可讓您建立以優先權順序評估的允許和拒絕位址區塊清單。 它類似于 Azure 網路中 (NSG) 功能的網路安全性群組。  您可以在 ASE 或多租使用者服務中使用這項功能。 與 ILB ASE 搭配使用時，您可以限制來自私用位址區塊的存取。

![存取限制](media/networking-features/access-restrictions.png)

當您想要限制可用來存取應用程式的 IP 位址時，存取限制功能將有所説明。 這項功能的使用案例包括：

* 從一組定義完善的位址限制對您應用程式的存取 
* 透過負載平衡服務（例如 Azure Front Door）來限制存取。 如果您想要鎖定 Azure Front Door 的輸入流量，請建立規則以允許來自 147.243.0.0/16 和2a01：111：2050：：/44 的流量。 

![Front Door 的存取限制](media/networking-features/access-restrictions-afd.png)

如果您想要鎖定應用程式的存取權，以便只能從 Azure 虛擬網路中的資源連線到 (VNet) ，您需要在 VNet 中的任何來源都有靜態公用位址。 如果資源沒有公用位址，您應該改為使用服務端點功能。 瞭解如何使用設定 [存取限制][iprestrictions]的教學課程來啟用這項功能。

### <a name="service-endpoints"></a>服務端點

服務端點可讓您鎖定應用程式的 **輸入** 存取，使來源位址必須來自您所選取的一組子網。 這項功能與 IP 存取限制搭配運作。 服務端點與遠端偵錯不相容。 若要對您的應用程式使用遠端偵錯程式，您的用戶端不能在啟用服務端點的子網中。 服務端點會設定為與 IP 存取限制相同的使用者體驗。 您可以建立包含公用位址以及 Vnet 中子網的存取規則的允許/拒絕清單。 這項功能支援的案例如下：

![服務端點](media/networking-features/service-endpoints.png)

* 使用您的應用程式設定應用程式閘道，以鎖定應用程式的輸入流量
* 將您應用程式的存取限制為 VNet 中的資源。 這可能包括 Vm、Ase，甚至是使用 VNet 整合的其他應用程式 

![使用應用程式閘道的服務端點](media/networking-features/service-endpoints-appgw.png)

在設定[服務端點存取限制][serviceendpoints]的教學課程中，您可以深入瞭解如何使用您的應用程式來設定服務端點

### <a name="private-endpoints"></a>私人端點

私人端點是一種網路介面，可透過 Azure Private Link，以私人且安全的方式連線到您的 Web 應用程式。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將 Web 應用程式帶入您的 VNet 中。 這項功能僅適用于 Web 應用程式的 **輸入** 流量。
[使用 Azure Web 應用程式的私人端點][privateendpoints]

私人端點可啟用下列案例：

* 從 VNet 中的資源限制對我的應用程式的存取 
* 在我的 VNet 中公開私人 IP 上的應用程式 
* 使用 WAF 保護我的應用程式 

私人端點會防止資料遭到外泄，因為您可以在私人端點上達到唯一的目的，就是它所設定的應用程式。 
 
### <a name="hybrid-connections"></a>混合式連線

App Service 混合式連線可讓您的應用程式對指定的 TCP 端點進行 **輸出** 呼叫。 端點可以是內部部署、在 VNet 中，或在埠443上允許輸出流量至 Azure 的任何位置。 這項功能需要在 Windows Server 2012 或更新版本的主機上安裝稱為混合式連線管理員 (HCM) 的轉送代理程式。 HCM 必須能夠連線到埠443上的 Azure 轉送。 您可以從入口網站中的 App Service 混合式連線 UI 下載 HCM。 

![混合式連線網路流程](media/networking-features/hybrid-connections.png)

App Service 混合式連線功能是以 Azure 轉送的混合式連線功能為基礎。 App Service 使用特殊形式的功能，僅支援從您的應用程式對 TCP 主機和埠發出輸出呼叫。 此主機和埠只需要在安裝 HCM 的主機上進行解析。 當 App Service 中的應用程式在混合式連線中定義的主機和埠上進行 DNS 查閱時，會自動將流量重新導向至混合式連線，並將混合式連線管理員。 若要深入瞭解混合式連線，請閱讀[App Service 混合][hybridconn]式連線的相關檔

這項功能通常用於：

* 存取未使用 VPN 或 ExpressRoute 連線到 Azure 的私人網路中的資源
* 支援將內部部署應用程式隨即轉移至 App Service，而不需要同時移動支援的資料庫  
* 安全地針對每個混合式連線提供單一主機和埠的存取。 大部分的網路功能都會開放存取網路和混合式連線，您只需要單一主機和埠就可以到達。
* 涵蓋其他輸出連接方法未涵蓋的案例
* 在 App Service 中執行開發，讓應用程式可以輕鬆地利用內部部署資源 

因為這項功能可讓您存取內部部署資源，而不需要輸入防火牆洞，所以開發人員很普遍。 其他輸出 App Service 網路功能與 Azure 虛擬網路功能相關。 混合式連線並不會相依于進行 VNet，而且可以用於更多的網路需求。 請務必注意，App Service 混合式連線」功能並不在意或知道您在其上所做的工作。 也就是說，您可以使用它來存取資料庫、web 服務或大型主機上的任意 TCP 通訊端。 此功能基本上是通道 TCP 封包。 

雖然混合式連線很常用於開發，但也可用於許多生產環境應用程式。 它很適合用來存取 web 服務或資料庫，但不適合用來建立許多連接的情況。 

### <a name="gateway-required-vnet-integration"></a>閘道需要 VNet 整合 

App Service VNet 整合功能所需的閘道可讓您的應用程式對 Azure 虛擬網路提出 **輸出** 要求。 這項功能的運作方式是將您的應用程式執行所在的主機連接至 VNet 上的虛擬網路閘道，並使用點對站 VPN。 當您設定此功能時，您的應用程式會取得指派給每個實例的其中一個點對站位址。 這項功能可讓您存取任何區域中傳統或 Resource Manager Vnet 的資源。 

![閘道需要 VNet 整合](media/networking-features/gw-vnet-integration.png)

這項功能可解決在其他 Vnet 中存取資源的問題，甚至可以用來透過 VNet 連線至其他 Vnet 或甚至是內部部署。 它無法與 ExpressRoute 連線 Vnet 搭配運作，但會使用站對站 VPN 連線網路。 從 App Service 環境 (ASE) 的應用程式中使用這項功能，通常並不適合，因為 ASE 已在您的 VNet 中。 這項功能可解決的使用案例包括：

* 存取 Azure 虛擬網路中私人 Ip 的資源 
* 如果有站對站 VPN，則存取內部部署資源 
* 存取對等互連 Vnet 中的資源 

啟用這項功能時，您的應用程式會使用設定目的地 VNet 的 DNS 伺服器。 您可以在 [App Service VNet 整合][vnetintegrationp2s]檔中深入瞭解這項功能。 

### <a name="vnet-integration"></a>VNet 整合

閘道所需的 VNet 整合功能很有用，但仍無法解決跨 ExpressRoute 存取資源的問題。 在需要跨越 ExpressRoute 連線的情況下，應用程式必須能夠呼叫服務端點保護的服務。 若要解決這兩個額外的需求，請新增另一個 VNet 整合功能。 新的 VNet 整合功能可讓您將應用程式的後端放置在相同區域中 Resource Manager VNet 的子網中。 這項功能無法從已存在於 VNet 的 App Service 環境中使用。 這項功能可讓：

* 存取相同區域中 Resource Manager Vnet 的資源
* 存取以服務端點保護的資源 
* 存取可透過 ExpressRoute 或 VPN 連接存取的資源
* 保護所有輸出流量 
* 強制將所有輸出流量傳送到通道。 

![VNet 整合](media/networking-features/vnet-integration.png)

若要深入瞭解這項功能，請閱讀 [App Service VNet 整合][vnetintegration]的檔。

## <a name="app-service-environment"></a>App Service 環境 

App Service 環境 (ASE) 是在您的 VNet 中執行之 Azure App Service 的單一租使用者部署。 ASE 可啟用使用案例，例如：

* 存取 VNet 中的資源
* 跨 ExpressRoute 存取資源
* 使用 VNet 中的私人位址公開您的應用程式 
* 跨服務端點存取資源 

使用 ASE 時，您不需要使用 VNet 整合或服務端點等功能，因為 ASE 已在您的 VNet 中。 如果您想要透過服務端點存取 SQL 或儲存體之類的資源，請在 ASE 子網上啟用服務端點。 如果您想要存取 VNet 中的資源，則不需要進行其他設定。  如果您想要跨 ExpressRoute 存取資源，您已經在 VNet 中，而不需要在 ASE 或其內部的應用程式上設定任何內容。 

因為 ILB ASE 中的應用程式可以在私人 IP 位址上公開，所以您可以輕鬆地新增 WAF 裝置，只將您想要的應用程式公開到網際網路，並讓 rest 保持安全。 它本身適合用來輕鬆開發多層式應用程式。 

來自 ASE 的多租使用者服務尚無可能的一些專案。 這些專案包括：

* 在私人 IP 位址上公開您的應用程式
* 使用不屬於應用程式一部分的網路控制來保護所有輸出流量 
* 在單一租使用者服務中裝載您的應用程式 
* 最多可擴大多租使用者服務中的多個實例 
* 載入私人 CA 用戶端憑證，以供您的應用程式搭配私人 CA 安全端點使用 
* 在系統中裝載的所有應用程式上強制執行 TLS 1.1，而不需在應用層級停用該功能 
* 針對您 ASE 中所有未與任何客戶共用的應用程式，提供專用的輸出位址 

![VNet 中的 ASE](media/networking-features/app-service-environment.png)

ASE 提供有關隔離和專用應用程式裝載的最佳案例，但有一些管理挑戰。 使用操作 ASE 之前需要考慮的事項包括：
 
 * ASE 會在 VNet 內執行，但在 VNet 外部具有相依性。 必須允許這些相依性。 深入瞭解 [App Service 環境的網路考慮][networkinfo]
 * ASE 不會像多租使用者服務一樣立即調整。 您需要預測調整需求，而不是被動調整。 
 * ASE 具有與其相關聯的 front 成本較高。 為了充分運用您的 ASE，您應該規劃將許多工作負載放入一個 ASE，而不是將它用於小型工作
 * ASE 中的應用程式無法限制對 ASE 中某些應用程式的存取，而不是其他應用程式的存取權。
 * ASE 位於子網中，而任何網路規則則適用于進出該 ASE 的所有流量。 如果您只想要將輸入流量規則指派給一個應用程式，請使用存取限制。 

## <a name="combining-features"></a>組合功能 

針對多租使用者服務所記下的功能可以一起使用，以解決更詳盡的使用案例。 這裡會說明兩個較常見的使用案例，但它們只是範例。 藉由瞭解各種功能，您幾乎可以解決所有的系統架構需求。

### <a name="inject-app-into-a-vnet"></a>將應用程式插入 VNet

常見的要求是如何將應用程式放在 VNet 中。 將您的應用程式放入 VNet，表示應用程式的輸入和輸出端點位於 VNet 內。 ASE 提供解決此問題的最佳解決方案，但您可以藉由合併功能，在多租使用者服務中取得大部分需要的功能。 例如，您可以透過下列方式，只裝載具有私人輸入和輸出位址的內部網路應用程式：

* 建立具有私人輸入和輸出位址的應用程式閘道
* 使用服務端點保護應用程式的輸入流量 
* 使用新的 VNet 整合，讓應用程式的後端位於您的 VNet 中 

此部署樣式不會為您提供網際網路輸出流量的專用位址，也無法讓您鎖定應用程式的所有輸出流量。  此部署樣式可讓您瞭解如何使用 ASE。 

### <a name="create-multi-tier-applications"></a>建立多層式應用程式

多層式應用程式是一種應用程式，只能從前端層存取 API 後端應用程式。 有兩種方式可建立多層式應用程式。 兩者都是從使用 VNet 整合開始，以將您的前端 web 應用程式與 VNet 中的子網連線。 這可讓您的 web 應用程式對您的 VNet 進行呼叫。 當您的前端應用程式連線到 VNet 之後，您必須選擇如何鎖定對您的 API 應用程式的存取。  您可以：

* 將前端和 API 應用程式裝載在相同的 ILB ASE 中，並使用應用程式閘道將前端應用程式公開至網際網路
* 在 ILB ASE 中裝載多租使用者服務和後端的前端
* 在多租使用者服務中裝載前端和 API 應用程式

如果您同時裝載多層式應用程式的前端和 API 應用程式，您可以：

使用 VNet 中的私人端點公開您的 API 應用程式

![私人端點兩層應用程式](media/networking-features/multi-tier-app-private-endpoint.png)

使用服務端點來保護 API 應用程式的輸入流量，只來自您的前端 web 應用程式所使用的子網

![服務端點安全的應用程式](media/networking-features/multi-tier-app.png)

這兩種技術之間的取捨如下：

* 使用服務端點時，您只需要保護將 API 應用程式流向整合子網的流量。 這可保護 API 應用程式，但您仍然可以從前端應用程式將資料遭到外泄到 App Service 中的其他應用程式。
* 使用私人端點時，您會有兩個子網在播放中。 這會增加複雜度。 此外，私人端點是最上層資源，可增加更多管理。 使用私人端點的好處是，您沒有資料遭到外泄的可能性。 

這兩種技術都可使用多個前端。 就小規模而言，服務端點很容易使用，因為您只需在前端整合子網上啟用 API 應用程式的服務端點。 當您新增更多前端應用程式時，您必須將每個 API 應用程式調整為具有整合子網的服務端點。 使用私人端點時，您會有更複雜的工作，但在設定私人端點之後，您就不需要變更 API apps 上的任何資訊。 

### <a name="line-of-business-applications"></a>企業營運應用程式

企業營運 (LOB) 應用程式是通常不會公開以供網際網路存取的內部應用程式。 這些應用程式是從公司網路內部呼叫，可嚴格控制存取權。 如果您使用 ILB ASE，很容易就能裝載您的企業營運應用程式。 如果您使用多租使用者服務，可以使用私人端點或服務端點與應用程式閘道結合。 使用應用程式閘道與服務端點（而不是私人端點）有兩個原因：

* 您的 LOB 應用程式需要 WAF 保護
* 您想要對 LOB 應用程式的多個實例進行負載平衡

如果不是這種情況，則您最好使用私用端點。 使用 App Service 中的私人端點時，您可以在 VNet 的私人位址上公開您的應用程式。 您放置在 VNet 中的私人端點可透過 ExpressRoute 和 VPN 連線來達成。 設定私人端點將會在私人位址上公開您的應用程式，但您必須將 DNS 設定為從內部部署連接到該位址。 若要完成此工作，您必須將包含私人端點的 Azure DNS 私人區域轉送至內部部署 DNS 伺服器。 Azure DNS 私人區域不支援區域轉送，但您可以針對該目的使用 DNS 伺服器來支援此功能。 此範本（ [DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/)轉寄站）可讓您更輕鬆地將 Azure DNS 私人區域轉寄到內部部署 DNS 伺服器。

## <a name="app-service-ports"></a>App Service 埠

如果您掃描 App Service，將會發現數個針對輸入連線所公開的埠。 無法在多租使用者服務中封鎖或控制對這些埠的存取。 公開的埠如下所示：

| 使用 | 連接埠 |
|----------|-------------|
|  HTTP/HTTPS  | 80、443 |
|  管理性 | 454、455 |
|  FTP/FTPS    | 21、990、10001-10020 |
|  Visual Studio 遠端偵錯  |  4020、4022、4024 |
|  Web Deploy 服務 | 8172 |
|  基礎結構使用 | 7654、1221 |

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
