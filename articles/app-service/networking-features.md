---
title: 網路功能
description: 瞭解 Azure App Service 中的網路功能，並瞭解安全性或其他功能所需的功能。
author: ccompy
ms.assetid: 5c61eed1-1ad1-4191-9f71-906d610ee5b7
ms.topic: article
ms.date: 10/18/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 53c0d37d4a25c2f2092a9e52bcae8ea494046bb0
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210013"
---
# <a name="app-service-networking-features"></a>App Service 網路功能

您可以透過多種方式在 Azure App Service 中部署應用程式。 根據預設，在 App Service 中裝載的應用程式可直接透過網際網路存取，而且只能連線至網際網路裝載的端點。 但對於許多應用程式，您需要控制輸入和輸出網路流量。 App Service 中有幾項功能可協助您滿足這些需求。 挑戰在於知道要用來解決特定問題的功能。 本文將協助您根據某些範例使用案例，判斷要使用的功能。

Azure App Service 有兩種主要的部署類型： 
- 多租使用者公用服務會以免費、共用、基本、標準、Premium、>premiumv2 和 PremiumV3 定價 Sku 來裝載 App Service 方案。 
- 單一租使用者 App Service 環境 (ASE) 直接在您的 Azure 虛擬網路中裝載隔離的 SKU App Service 方案。 

您使用的功能將取決於您是在多租使用者服務或 ASE 中。 

## <a name="multitenant-app-service-networking-features"></a>多組織使用者共用 App Service 網路功能 

Azure App Service 是分散式系統。 處理傳入 HTTP 或 HTTPS 要求的角色稱為「 *前端*」。 裝載客戶工作負載的角色稱為背景 *工作* 角色。 App Service 部署中的所有角色都存在於多租使用者網路中。 因為相同的 App Service 縮放單位中有許多不同的客戶，所以您無法直接將 App Service 網路連線到您的網路。 

除了連接網路，您還需要功能來處理應用程式通訊的各個層面。 當您 *從* 應用程式進行呼叫 *時，處理應用程式要求的* 功能無法用來解決問題。 同樣地，無法使用解決應用程式呼叫問題的功能來解決應用程式的問題。  

| 輸入功能 | 輸出功能 |
|---------------------|-------------------|
| 應用程式指派的位址 | 混合式連線 |
| 存取限制 | 需要閘道的 VNet 整合 |
| 服務端點 | VNet 整合 |
| 私人端點 ||

除了所述的例外狀況之外，您也可以一起使用這些功能。 您可以混合使用這些功能來解決問題。

## <a name="use-cases-and-features"></a>使用案例和功能

針對任何特定的使用案例，可能有幾種方法可以解決此問題。 選擇最佳的功能有時會超越使用案例本身。 下列輸入使用案例會建議如何使用 App Service 網路功能，來解決控制傳送至應用程式之流量的問題：
 
| 輸入使用案例 | 功能 |
|---------------------|-------------------|
| 針對您的應用程式支援以 IP 為基礎的 SSL 需求 | 應用程式指派的位址 |
| 為您的應用程式支援非共用的專用輸入位址 | 應用程式指派的位址 |
| 從一組定義完善的位址限制對您應用程式的存取 | 存取限制 |
| 限制從虛擬網路中的資源存取您的應用程式 | 服務端點 </br> ILB ASE </br> 私人端點 |
| 在虛擬網路中的私人 IP 上公開您的應用程式 | ILB ASE </br> 私人端點 </br> 具有服務端點的應用程式閘道實例上輸入流量的私人 IP |
| 使用 web 應用程式防火牆保護您的應用程式 (WAF)  | 應用程式閘道和 ILB ASE </br> 具有私人端點的應用程式閘道 </br> 具有服務端點的應用程式閘道 </br> 具有存取限制的 Azure Front Door |
| 將流量負載平衡至不同區域中的應用程式 | 具有存取限制的 Azure Front Door | 
| 針對相同區域中的流量進行負載平衡 | [具有服務端點的應用程式閘道][appgwserviceendpoints] | 

下列輸出使用案例建議如何使用 App Service 網路功能來解決應用程式的輸出存取需求：

| 輸出使用案例 | 功能 |
|---------------------|-------------------|
| 存取相同區域中的 Azure 虛擬網路中的資源 | VNet 整合 </br> ASE |
| 存取不同區域中的 Azure 虛擬網路中的資源 | 需要閘道的 VNet 整合 </br> ASE 和虛擬網路對等互連 |
| 存取以服務端點保護的資源 | VNet 整合 </br> ASE |
| 存取未連線至 Azure 的私人網路中的資源 | 混合式連線 |
| 跨 Azure ExpressRoute 線路存取資源 | VNet 整合 </br> ASE | 
| 保護來自您 web 應用程式的輸出流量 | VNet 整合和網路安全性群組 </br> ASE | 
| 從您的 web 應用程式路由輸出流量 | VNet 整合和路由表 </br> ASE | 


### <a name="default-networking-behavior"></a>預設網路行為

Azure App Service 縮放單位支援每個部署中的許多客戶。 免費和共用的 SKU 方案會在多租使用者背景工作角色上裝載客戶工作負載。 基本和更高的方案會裝載僅限一個 App Service 方案專屬的客戶工作負載。 如果您有標準 App Service 方案，則該方案中的所有應用程式都會在相同的背景工作上執行。 如果您相應放大背景工作角色，該 App Service 方案中的所有應用程式都會在您 App Service 計畫中的每個實例的新背景工作角色上進行複寫。 

#### <a name="outbound-addresses"></a>輸出位址

背景工作 Vm 會在 App Service 計畫的大型部分進行細分。 免費、共用、基本、標準和 Premium 方案都使用相同的背景工作 VM 類型。 >premiumv2 方案會使用另一種 VM 類型。 PremiumV3 還會使用另一種 VM 類型。 當您變更 VM 系列時，會取得一組不同的輸出位址。 如果您從標準調整為 >premiumv2，您的輸出位址將會變更。 如果您從 >premiumv2 調整為 PremiumV3，您的輸出位址將會變更。 在某些較舊的縮放單位中，當您從標準級別調整為 >premiumv2 時，輸入和輸出位址都會變更。 

有一些位址可用於輸出呼叫。 您的應用程式用來進行輸出呼叫的輸出位址會列在應用程式的屬性中。 這些位址會由 App Service 部署中相同背景工作 VM 系列上執行的所有應用程式共用。 如果您想要查看您的應用程式可能會在縮放單位中使用的所有位址，會呼叫 `possibleOutboundAddresses` 會列出這些位址的屬性。 

![顯示應用程式屬性的螢幕擷取畫面。](media/networking-features/app-properties.png)

App Service 有許多用來管理服務的端點。  這些位址會在個別的檔中發行，而且也會在 `AppServiceManagement` IP 服務標記中。 `AppServiceManagement`只有在需要允許這類流量的 App Service 環境中，才會使用此標記。 App Service 的輸入位址會在 `AppService` IP 服務標記中追蹤。 沒有 IP 服務標記包含 App Service 使用的輸出位址。 

![顯示 App Service 輸入和輸出流量的圖表。](media/networking-features/default-behavior.png)

### <a name="app-assigned-address"></a>應用程式指派的位址 

應用程式指派的位址功能是以 IP 為基礎的 SSL 功能 offshoot。 您可以使用您的應用程式設定 SSL 來存取它。 您可以使用這項功能進行以 IP 為基礎的 SSL 呼叫。 您也可以使用它來為您的應用程式提供只有它所擁有的位址。 

![說明應用程式指派位址的圖表。](media/networking-features/app-assigned-address.png)

當您使用應用程式指派的位址時，您的流量仍會通過相同的前端角色來處理 App Service 縮放單位中的所有連入流量。 但指派給您應用程式的位址只能由您的應用程式使用。 這項功能的使用案例：

* 針對您的應用程式支援以 IP 為基礎的 SSL 需求。
* 為您的應用程式設定未共用的專用位址。

若要瞭解如何在您的應用程式上設定位址，請參閱 [在 Azure App Service 中新增 TLS/SSL 憑證][appassignedaddress]。 

### <a name="access-restrictions"></a>存取限制 

存取限制可讓您篩選 *輸入* 要求。 篩選動作會在與您的應用程式執行所在的背景工作角色上游的前端角色上進行。 由於前端角色是來自背景工作角色，因此您可以將存取限制視為應用程式的網路層級保護。 

這項功能可讓您建立以優先權順序評估的允許和拒絕規則清單。 它類似于 Azure 網路中 (NSG) 功能的網路安全性群組。 您可以在 ASE 或多租使用者服務中使用這項功能。 當您使用 ILB ASE 或私人端點時，您可以限制來自私人位址區塊的存取。
> [!NOTE]
> 每個應用程式最多可以設定512個存取限制規則。 

![說明存取限制的圖表。](media/networking-features/access-restrictions.png)

#### <a name="ip-based-access-restriction-rules"></a>以 IP 為基礎的存取限制規則

當您想要限制可用來存取應用程式的 IP 位址時，以 IP 為基礎的存取限制功能將有所説明。 可支援 IPv4 和 IPv6 兩者。 這項功能的一些使用案例：
* 從一組定義完善的位址限制對您應用程式的存取。 
* 使用具有已知輸出 IP 位址的外部負載平衡服務或其他網路設備，來限制對流量的存取。 

若要瞭解如何啟用這項功能，請參閱設定 [存取限制][iprestrictions]。

#### <a name="access-restriction-rules-based-on-service-endpoints"></a>以服務端點為基礎的存取限制規則 

服務端點可讓您鎖定應用程式的 *輸入* 存取，使來源位址必須來自您所選取的一組子網。 這項功能與 IP 存取限制搭配運作。 服務端點與遠端偵錯不相容。 如果您想要對應用程式使用遠端偵錯程式，您的用戶端就不能在已啟用服務端點的子網中。 設定服務端點的程式與設定 IP 存取限制的程式類似。 您可以建立存取規則的允許/拒絕清單，其中包含您虛擬網路中的公用位址和子網。 

這項功能的一些使用案例：

* 使用您的應用程式設定應用程式閘道，以鎖定應用程式的輸入流量。
* 將您的應用程式存取限制為虛擬網路中的資源。 這些資源可以包含 Vm、Ase，甚至是使用 VNet 整合的其他應用程式。 

![說明如何使用服務端點搭配應用程式閘道的圖表。](media/networking-features/service-endpoints-appgw.png)

若要深入瞭解如何使用您的應用程式來設定服務端點，請參閱 [Azure App Service 存取限制][serviceendpoints]。
#### <a name="access-restriction-rules-based-on-service-tags-preview"></a>以服務標籤為基礎的存取限制規則 (預覽) 
[Azure 服務標記][servicetags] 是一組定義完善的 AZURE 服務 IP 位址。 服務標記會將各種 Azure 服務中使用的 IP 範圍群組在一起，而且通常也會進一步設定為特定區域的範圍。 這可讓您篩選來自特定 Azure 服務的 *輸入* 流量。 

如需完整的標記清單和詳細資訊，請造訪上面的服務標記連結。 若要瞭解如何啟用這項功能，請參閱設定 [存取限制][iprestrictions]。
#### <a name="http-header-filtering-for-access-restriction-rules-preview"></a>存取限制規則的 Http 標頭篩選 (預覽) 
針對每個存取限制規則，您可以新增額外的 HTTP 標頭篩選。 這可讓您進一步檢查傳入的要求，並根據特定的 HTTP 標頭值進行篩選。 每個標頭的每個規則最多可以有8個值。 目前支援下列 HTTP 標頭清單： 
* X-Forwarded-For
* X-Forwarded-Host
* X-Azure-FDID
* X-FD-HealthProbe

Http 標頭篩選的一些使用案例包括：
* 限制從 proxy 伺服器轉送主機名稱的流量存取
* 使用服務標籤規則和 X FDID 標頭限制來限制存取特定的 Azure Front Door 實例
### <a name="private-endpoint"></a>私人端點

私人端點是一種網路介面，可讓您以私人且安全的方式連線到您的 Web 應用程式，並透過 Azure private link。 私人端點會使用您虛擬網路中的私人 IP 位址，有效地將 web 應用程式帶入您的虛擬網路。 這項功能僅適用于 web 應用程式的 *輸入* 流量。
如需詳細資訊，請參閱 [使用 Azure Web 應用程式的私人端點][privateendpoints]。

這項功能的一些使用案例：

* 從虛擬網路中的資源限制對您應用程式的存取。 
* 在虛擬網路中的私人 IP 上公開您的應用程式。 
* 使用 WAF 保護您的應用程式。

私人端點會防止資料遭到外泄，因為只有您可以在私人端點上達到的唯一東西，就是設定它的應用程式。 
 
### <a name="hybrid-connections"></a>混合式連線

App Service 混合式連線可讓您的應用程式對指定的 TCP 端點進行 *輸出* 呼叫。 端點可以是內部部署、在虛擬網路中，或允許埠443上的輸出流量連到 Azure 的任何位置。 若要使用此功能，您必須在 Windows Server 2012 或更新版本的主機上安裝稱為混合式連線管理員的轉送代理程式。 混合式連線管理員必須能夠連線到埠443上的 Azure 轉送。 您可以從入口網站中的 [App Service 混合式連線] UI 下載混合式連線管理員。 

![顯示混合式連接網路流量的圖表。](media/networking-features/hybrid-connections.png)

App Service 混合式連線建基於 Azure 轉送的混合式連線功能。 App Service 使用特殊形式的功能，僅支援從您的應用程式對 TCP 主機和埠發出輸出呼叫。 此主機和埠只需要在安裝混合式連線管理員的主機上進行解析。 

當 App Service 中的應用程式在您的混合式連線中定義的主機和埠上進行 DNS 查閱時，流量會自動重新導向至混合式連線，而不是混合式連線管理員。 若要深入瞭解，請參閱 [App Service 混合式連接][hybridconn]。

這項功能通常用於：

* 存取未使用 VPN 或 ExpressRoute 連線到 Azure 的私人網路中的資源。
* 支援將內部部署應用程式遷移至 App Service，而不需要移動支援的資料庫。  
* 針對每個混合式連線，提供單一主機和埠的增強式安全性存取。 大部分的網路功能都會開放存取網路。 使用混合式連線時，您只能連接到單一主機和埠。
* 涵蓋其他輸出連接方法未涵蓋的案例。
* 使用可讓應用程式輕鬆使用內部部署資源的方式，在 App Service 中執行開發。 

由於這項功能可讓您存取內部部署資源，而不需要輸入防火牆洞，因此很受開發人員歡迎。 其他輸出 App Service 網路功能與 Azure 虛擬網路相關。 混合式連線不需依賴虛擬網路。 它可用於更多不同的網路需求。 

請注意，App Service 混合式連線並不知道您在其上執行的工作。 您可以使用它來存取資料庫、web 服務，或大型主機上的任意 TCP 通訊端。 此功能基本上是通道 TCP 封包。 

混合式連線很常用於開發環境，但也用於生產應用程式。 它很適合用來存取 web 服務或資料庫，但它不適用於需要建立許多連接的情況。 

### <a name="gateway-required-vnet-integration"></a>需要閘道的 VNet 整合 

閘道-必要的 App Service VNet 整合可讓您的應用程式將 *輸出* 要求發出至 Azure 虛擬網路。 此功能的運作方式是使用點對站 VPN，將您的應用程式執行所在的主機連接到虛擬網路上的虛擬網路閘道。 當您設定此功能時，您的應用程式會取得指派給每個實例的其中一個點對站位址。 這項功能可讓您存取任何區域中傳統或 Azure Resource Manager 虛擬網路中的資源。 

![此圖說明閘道所需的 VNet 整合。](media/networking-features/gw-vnet-integration.png)

這項功能可解決存取其他虛擬網路中的資源時發生的問題。 它甚至可以用來透過虛擬網路連線到其他虛擬網路或內部部署。 它無法與連線到 ExpressRoute 的虛擬網路搭配運作，但它可以與站對站 VPN 連線的網路搭配使用。 從 App Service 環境 (ASE) 的應用程式中使用此功能，通常並不適合，因為 ASE 已在您的虛擬網路中。 這項功能的使用案例：

* 存取 Azure 虛擬網路中私人 Ip 上的資源。 
* 如果有站對站 VPN，請存取內部部署資源。 
* 存取對等互連虛擬網路中的資源。 

啟用這項功能時，您的應用程式會使用已設定目的地虛擬網路的 DNS 伺服器。 如需這項功能的詳細資訊，請參閱 [App Service VNet 整合][vnetintegrationp2s]。 

### <a name="vnet-integration"></a>VNet 整合

閘道-必要的 VNet 整合很有用，但無法解決跨 ExpressRoute 存取資源的問題。 在需要跨越 ExpressRoute 連線的情況下，應用程式必須能夠對服務端點所保護的服務進行呼叫。 另一個 VNet 整合功能可以滿足這些需求。 

新的 VNet 整合功能可讓您將應用程式的後端放置在與應用程式位於相同區域的 Resource Manager 虛擬網路的子網中。 這項功能無法從已在虛擬網路中的 App Service 環境中使用。 這項功能的使用案例：

* 存取相同區域 Resource Manager 虛擬網路中的資源。
* 存取以服務端點保護的資源。 
* 存取可跨 ExpressRoute 或 VPN 連線存取的資源。
* 協助保護所有輸出流量。 
* 強制通道所有輸出流量。 

![說明 VNet 整合的圖表。](media/networking-features/vnet-integration.png)

若要深入瞭解，請參閱 [App Service VNet 整合][vnetintegration]。

### <a name="app-service-environment"></a>App Service 環境 

App Service 環境 (ASE) 是在您的虛擬網路中執行的 Azure App Service 單一租使用者部署。 這項功能的某些案例如下：

* 存取虛擬網路中的資源。
* 存取 ExpressRoute 之間的資源。
* 使用虛擬網路中的私人位址公開您的應用程式。 
* 跨服務端點存取資源。 

使用 ASE 時，您不需要使用 VNet 整合或服務端點等功能，因為 ASE 已經在您的虛擬網路中。 如果您想要存取像是 SQL 或 Azure 儲存體服務端點的資源，請在 ASE 子網上啟用服務端點。 如果您想要存取虛擬網路中的資源，則不需要進行任何額外的設定。 如果您想要跨 ExpressRoute 存取資源，您已在虛擬網路中，而且不需要在 ASE 或其中的應用程式上進行任何設定。 

因為 ILB ASE 中的應用程式可以在私人 IP 位址上公開，所以您可以輕鬆地新增 WAF 裝置，只公開您想要連線到網際網路的應用程式，並協助保護其餘的安全。 這項功能可讓您更輕鬆地開發多層式應用程式。 

某些專案目前無法從多租使用者服務，但可以從 ASE 進行。 這裡有一些範例：

* 在私人 IP 位址上公開您的應用程式。
* 使用不屬於應用程式一部分的網路控制，協助保護所有輸出流量。
* 在單一租使用者服務中裝載您的應用程式。 
* 最多可擴大至多個多租使用者服務中的實例數目。 
* 載入私人 CA 用戶端憑證，以供您的應用程式搭配私人 CA 保護的端點使用。
* 在裝載于系統中的所有應用程式上強制執行 TLS 1.1，而不需在應用層級停用它。 
* 針對您 ASE 中未與客戶共用的所有應用程式，提供專用的輸出位址。 

![說明虛擬網路中 ASE 的圖表。](media/networking-features/app-service-environment.png)

ASE 提供有關隔離和專用應用程式裝載的最佳案例，但它牽涉到一些管理挑戰。 使用操作 ASE 之前需要考慮的事項：
 
 * ASE 會在您的虛擬網路內執行，但它在虛擬網路外部具有相依性。 必須允許這些相依性。 如需詳細資訊，請參閱 [App Service 環境的網路考慮][networkinfo]。
 * ASE 不會像多租使用者服務一樣立即調整。 您需要預測調整需求，而不是被動調整。 
 * ASE 的前期成本較高。 若要充分運用您的 ASE，您應該規劃將許多工作負載放入一個 ASE，而不是使用它來進行小型工作。
 * ASE 中的應用程式無法選擇性地限制對 ASE 中某些應用程式的存取，而不是其他應用程式的存取權。
 * ASE 位於子網中，而任何網路規則則適用于進出該 ASE 的所有流量。 如果您只想要將輸入流量規則指派給一個應用程式，請使用存取限制。 

## <a name="combining-features"></a>組合功能 

針對多租使用者服務所記下的功能可以一起使用，以解決更詳盡的使用案例。 這裡會說明兩個較常見的使用案例，但它們只是範例。 藉由瞭解各種功能，您幾乎可以滿足所有的系統架構需求。

### <a name="place-an-app-into-a-virtual-network"></a>將應用程式放入虛擬網路

您可能會想要知道如何將應用程式放入虛擬網路中。 如果您將應用程式放入虛擬網路中，則應用程式的輸入和輸出端點會在虛擬網路內。 ASE 是解決此問題的最佳方式。 但是您可以藉由合併功能，在多租使用者服務中滿足大部分的需求。 例如，您可以透過下列方式，使用私用輸入和輸出位址來裝載僅限內部網路的應用程式：

* 建立具有私人輸入和輸出位址的應用程式閘道。
* 使用服務端點保護應用程式的輸入流量。 
* 使用新的 VNet 整合功能，讓應用程式的後端在您的虛擬網路中。 

此部署樣式不會為您提供網際網路輸出流量的專用位址，也無法讓您鎖定應用程式的所有輸出流量。 它會為您提供許多您只需要使用 ASE 來取得的內容。 

### <a name="create-multitier-applications"></a>建立多層式應用程式

多層式應用程式是一種應用程式，可讓您只能從前端層存取 API 後端應用程式。 有兩種方式可建立多層式應用程式。 兩者都是從使用 VNet 整合開始，將前端 web 應用程式連線至虛擬網路中的子網。 這樣做可讓您的 web 應用程式呼叫您的虛擬網路。 當您的前端應用程式連線到虛擬網路之後，您必須決定如何鎖定對您的 API 應用程式的存取。 您可以：

* 將前端和 API 應用程式裝載在相同的 ILB ASE 中，並使用應用程式閘道將前端應用程式公開至網際網路。
* 在多租使用者服務和後端的 ILB ASE 中裝載前端。
* 將前端和 API 應用程式裝載于多租使用者服務中。

如果您同時為多層式應用程式裝載前端和 API 應用程式，您可以：

- 使用虛擬網路中的私人端點來公開您的 API 應用程式：

  ![說明在兩層式應用程式中使用私用端點的圖表。](media/networking-features/multi-tier-app-private-endpoint.png)

- 使用服務端點，以確保您的 API 應用程式的輸入流量只會來自前端 web 應用程式所使用的子網：

  ![說明如何使用服務端點來協助保護應用程式的圖表。](media/networking-features/multi-tier-app.png)

以下是一些可協助您決定要使用哪一種方法的考慮：

* 當您使用服務端點時，您只需要將 API 應用程式的流量安全地傳送至整合子網。 這有助於保護 API 應用程式的安全，但您仍然可以從前端應用程式將資料遭到外泄至 app service 中的其他應用程式。
* 當您使用私人端點時，您會在 play 中有兩個子網，這會增加複雜度。 此外，私人端點是最上層資源，可增加管理負擔。 使用私人端點的好處是，您不會有資料遭到外泄的可能性。 

這兩種方法都可以使用多個前端。 小規模的服務端點很容易使用，因為您只需在前端整合子網上啟用 API 應用程式的服務端點。 當您新增更多前端應用程式時，您需要調整每個 API 應用程式，以包含具有整合子網的服務端點。 當您使用私人端點時，會有更複雜的情況，但在設定私人端點之後，您就不需要在 API 應用程式上變更任何資訊。 

### <a name="line-of-business-applications"></a>企業營運應用程式

企業營運 (LOB) 應用程式是內部應用程式，這些應用程式通常不會公開以供網際網路存取。 這些應用程式是從公司網路內部呼叫，可嚴格控制存取權。 如果您使用 ILB ASE，很容易就能裝載您的企業營運應用程式。 如果您使用多租使用者服務，可以使用私人端點，或使用與應用程式閘道結合的服務端點。 使用應用程式閘道搭配服務端點的原因有兩個，而不是使用私人端點：
* 您需要在 LOB 應用程式上 WAF 保護。
* 您想要對 LOB 應用程式的多個實例進行負載平衡。

如果這兩項需求都不適用，您最好是使用私人端點。 在 App Service 中提供私人端點時，您可以在虛擬網路的私人位址上公開應用程式。 您放在虛擬網路中的私人端點可透過 ExpressRoute 和 VPN 連線來達成。 

設定私人端點會在私人位址上公開您的應用程式，但您必須將 DNS 設定為從內部部署連接到該位址。 若要讓此設定生效，您必須將包含私人端點的 Azure DNS 私人區域轉送至內部部署 DNS 伺服器。 Azure DNS 私人區域不支援區域轉送，但您可以針對該目的使用 DNS 伺服器來支援區域轉送。 [DNS](https://azure.microsoft.com/resources/templates/301-dns-forwarder/)轉寄站範本可讓您更輕鬆地將 Azure DNS 私人區域轉寄到內部部署 DNS 伺服器。

## <a name="app-service-ports"></a>App Service 埠

如果您掃描 App Service，您會發現多個針對輸入連線所公開的埠。 沒有任何方法可以封鎖或控制多租使用者服務中這些埠的存取。 以下是公開的埠清單：

| 使用 | 埠或埠 |
|----------|-------------|
|  HTTP/HTTPS  | 80、443 |
|  管理性 | 454、455 |
|  FTP/FTPS    | 21、990、10001-10020 |
|  Visual Studio 遠端偵錯  |  4020、4022、4024 |
|  Web Deploy 服務 | 8172 |
|  基礎結構使用 | 7654、1221 |

<!--Links-->
[appassignedaddress]: ./configure-ssl-certificate.md
[iprestrictions]: ./app-service-ip-restrictions.md
[serviceendpoints]: ./app-service-ip-restrictions.md
[hybridconn]: ./app-service-hybrid-connections.md
[vnetintegrationp2s]: ./web-sites-integrate-with-vnet.md
[vnetintegration]: ./web-sites-integrate-with-vnet.md
[networkinfo]: ./environment/network-info.md
[appgwserviceendpoints]: ./networking/app-gateway-with-service-endpoints.md
[privateendpoints]: ./networking/private-endpoint.md
[servicetags]: ../virtual-network/service-tags-overview.md