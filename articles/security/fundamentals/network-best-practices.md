---
title: 網路安全性的最佳作法-Microsoft Azure
description: 本文提供使用內建 Azure 功能的一些網路安全性最佳作法。
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 3ded20f37a394e6adf726ad40c01aa36d41e4e8d
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299340"
---
# <a name="azure-best-practices-for-network-security"></a>適用于網路安全性的 Azure 最佳作法
本文討論 Azure 最佳作法的集合，以加強您的網路安全性。 這些最佳作法衍生自我們的 Azure 網路經驗和客戶的經驗。

針對每個最佳做法，本文說明：

* 最佳作法是什麼
* 您為何想要啟用該最佳作法
* 如果無法啟用最佳作法，結果可能為何
* 最佳作法的可能替代方案
* 如何學習啟用最佳作法

這些最佳作法是根據共識的意見，以及 Azure 平臺功能和功能集，因為在撰寫本文時已存在。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

## <a name="use-strong-network-controls"></a>使用強式網路控制
您可以透過將 [Azure 虛擬機器 (VM)](https://azure.microsoft.com/services/virtual-machines/) 和設備置於 [Azure 虛擬網路](../../virtual-network/index.yml)上，來將它們連線到其他網路裝置。 也就是說，您可以將虛擬網路介面卡連線到虛擬網路，讓有網路功能的裝置之間可進行 TCP/IP 型通訊。 連線到 Azure 虛擬網路的虛擬機器能夠連線到在相同虛擬網路、其他虛擬網路、網際網路或甚至您自己的內部部署網路上的裝置。

當您規劃網路和網路的安全性時，我們建議您集中：

- 核心網路功能的管理，例如 ExpressRoute、虛擬網路和子網布建，以及 IP 位址。
- 網路安全性元素的治理，例如 ExpressRoute、虛擬網路和子網布建，以及 IP 位址等網路虛擬裝置功能。

如果您使用一組常用的管理工具來監視您的網路和網路的安全性，您可以清楚瞭解兩者。 簡單、統一的安全性策略可減少錯誤，因為它會增加人類的理解和自動化的可靠性。

## <a name="logically-segment-subnets"></a>以邏輯方式分割子網路
Azure 虛擬網路類似于內部部署網路上的 Lan。 Azure 虛擬網路背後的構想是您會根據單一私人 IP 位址空間建立網路，您可以在其上放置所有的 Azure 虛擬機器。 可用的私人 IP 位址空間位於類別 A (10.0.0.0/8)、類別 B (172.16.0.0/12) 和類別 C (192.168.0.0/16) 範圍中。

以邏輯方式分割子網路的最佳做法包括：

**最佳做法**：不要指派範圍廣泛的允許規則（例如，允許0.0.0.0 到255.255.255.255）。  
**詳細資料**：請確認疑難排解程式不鼓勵或禁止設定這些類型的規則。 這些允許規則會導致安全性的錯誤，並經常發現並利用 red 小組來入侵。

**最佳做法**：將較大的位址空間分割成子網路。   
**詳細資料**：您可以使用 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 型子網路原則來建立子網路。

**最佳做法**：建立子網路之間的網路存取控制。 子網之間的路由會自動發生，您不需要手動設定路由表。 根據預設，您在 Azure 虛擬網路上建立的子網之間沒有網路存取控制。   
**詳細資料**：使用[網路安全性群組](/azure/virtual-network/virtual-networks-nsg)來防止未經要求的流量進入 Azure 子網。 網路安全性群組是簡單的具狀態封包檢查裝置，其使用5元組方法（來源 IP、來源埠、目的地 IP、目的地埠和第4層通訊協定）來建立網路流量的允許/拒絕規則。 您可以允許或拒絕單一 IP 位址、多個 IP 位址或整個子網路的輸入或輸出流量。

當您使用網路安全性群組進行子網之間的網路存取控制時，您可以將屬於相同安全性區域或角色的資源放在其自己的子網中。

**最佳做法**：避免小型虛擬網路和子網，以確保簡單且彈性。   
**詳細資料**：大部分的組織會新增比一開始計畫更多的資源，而重新配置的位址則非常耗費人力。 使用小型子網可新增有限的安全性值，並將網路安全性群組對應到每個子網會增加額外負荷。 廣泛定義子網，以確保您有更大的成長彈性。

**最佳做法**：藉由定義[應用程式安全性群組](https://azure.microsoft.com/blog/applicationsecuritygroups/)來簡化網路安全性群組規則管理。  
**詳細資料**：針對您認為可能會在未來變更或用於多個網路安全性群組的 IP 位址清單定義應用程式安全性群組。 請務必清楚命名應用程式安全性群組，讓其他人可以瞭解其內容和用途。

## <a name="adopt-a-zero-trust-approach"></a>採用零信任方法
以周邊為基礎的網路會假設網路內的所有系統都可以受到信任。 但現今的員工會從各種裝置和應用程式上的任何地方存取其組織的資源，讓周邊安全性控制項變得不相關。 僅著重于誰可以存取資源的存取控制原則不夠。 為了讓安全性與生產力之間的平衡，安全性系統管理員也必須考慮資源的存取*方式*。

網路需要從傳統的防禦功能演變，因為網路可能容易受到入侵：攻擊者可能會危害受信任界限內的單一端點，然後在整個網路上快速擴充據點。 「[零信任](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/)網路」會根據周邊網路位置來排除信任的概念。 相反地，零信任架構會使用裝置和使用者信任宣告來閘道存取組織資料和資源。 針對新的計畫，請採用在存取時驗證信任的零信任方法。

最佳做法如下：

**最佳做法**：根據裝置、身分識別、保證、網路位置等，提供資源的條件式存取。  
**詳細資料**： [Azure AD 條件式存取](/azure/active-directory/conditional-access/overview)可讓您根據所需的條件來執行自動化存取控制決策，以套用正確的存取控制。 如需詳細資訊，請參閱[使用條件式存取來管理 Azure 管理的存取權](../../role-based-access-control/conditional-access-azure-management.md)。

**最佳做法**：只在工作流程核准後才啟用埠存取。  
**詳細資料**：您可以[在 Azure 資訊安全中心中使用即時 VM 存取](../../security-center/security-center-just-in-time.md)，以鎖定 Azure vm 的輸入流量、降低暴露于攻擊的風險，並在需要時輕鬆地連接到 vm。

**最佳做法**：授與暫時許可權以執行特殊許可權工作，以防止惡意或未經授權的使用者在許可權過期之後取得存取權。 只有當使用者需要時才會獲得存取權。  
**詳細資料**：在 Azure AD Privileged Identity Management 或協力廠商解決方案中使用即時存取，以授與許可權來執行特殊許可權的工作。

「零信任」是網路安全性的下一個進化。 網路攻擊的狀態會推動組織採用「假設缺口」的思維，但這種方法不應該受到限制。 零信任網路會保護公司資料和資源，同時確保組織可以使用技術來打造現代化工作場所，讓員工隨時隨地都能以生產力的方式進行工作。

## <a name="control-routing-behavior"></a>控制路由行為
當您在 Azure 虛擬網路上放置虛擬機器時，該 VM 可以連線到相同虛擬網路上的任何其他 VM，即使其他 VM 位於不同子網路亦然。 可以這麼做的原因是在預設情況下，有一組系統路由集合已啟用，使 VM 可以進行此類型的通訊。 這些預設的路由讓在相同虛擬網路上的 VM 可以起始彼此之間的連線，及與網際網路的連線 (僅對網際網路的輸出通訊)。

雖然預設系統路由適用於許多部署案例，但您有時會想為您的部署自訂路由設定。 您可以將下一個躍點位址設定成到達特定的目的地。

我們建議您在部署虛擬網路的安全性設備時，設定[使用者定義的路由](../../virtual-network/virtual-networks-udr-overview.md)。 我們稍後會在標題為[將重要的 Azure 服務資源只放到您的虛擬網路保護](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)的小節討中論此主題。

> [!NOTE]
> 不需要使用者定義的路由，而預設的系統路由通常能運作。
>
>

## <a name="use-virtual-network-appliances"></a>使用虛擬網路應用裝置
網路安全性群組和使用者定義路由可以在[OSI 模型](https://en.wikipedia.org/wiki/OSI_model)的網路和傳輸層提供特定的網路安全性量值。 但在某些情況下，您會想要或需要在堆疊的高層級啟用安全性。 在這類情況下，建議您部署 Azure 合作夥伴所提供的虛擬網路安全性應用裝置。

Azure 網路安全性設備可提供比網路層級控制更佳的安全性。 虛擬網路安全性設備的網路安全性功能包括：


* 防火牆
* 入侵偵測/入侵預防
* 弱點管理
* 應用程式控制
* 以網路為基礎的異常偵測
* Web 篩選
* 防毒
* 殭屍網路防護

若要尋找可用的 Azure 虛擬網路安全性設備，請移至[Azure Marketplace](https://azure.microsoft.com/marketplace/)並搜尋「安全性」和「網路安全性」。

## <a name="deploy-perimeter-networks-for-security-zones"></a>部署安全性區域的周邊網路
[周邊網路](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (也稱為 DMZ) 是實體或邏輯網路區段，可在您的資產與網際網路之間提供額外一層安全性。 在周邊網路邊緣上的特製化網路存取控制裝置，只允許所要的流量進入您的虛擬網路。

周邊網路非常實用，因為您可以將網路存取控制管理、監視、記錄和報告的重點放在位於 Azure 虛擬網路邊緣的裝置。 周邊網路是您通常會啟用分散式阻斷服務（DDoS）預防、入侵偵測/入侵預防系統（IDS/IPS）、防火牆規則和原則、web 篩選、網路反惡意程式碼等的地方。 網路安全性裝置位於網際網路與您的 Azure 虛擬網路之間，具有兩個網路均適用的介面。

雖然這是周邊網路的基本設計，但有許多不同的設計，像是回溯、三宿主和多宿主。

根據先前所述的零信任概念，建議您考慮使用周邊網路進行所有的高安全性部署，以增強 Azure 資源的網路安全性和存取控制層級。 您可以使用 Azure 或協力廠商解決方案，在您的資產與網際網路之間提供額外的安全性層級：

- Azure 原生控制項。 應用程式閘道中的[Azure 防火牆](/azure/firewall/overview)和[web 應用程式防火牆](../../application-gateway/features.md#web-application-firewall)提供具有完全具狀態的防火牆即服務的基本安全性、內建高可用性、不受限制的雲端擴充、FQDN 篩選、OWASP 核心規則集的支援，以及簡單的安裝和設定。
- 協力廠商供應專案。 搜尋[Azure Marketplace](https://azuremarketplace.microsoft.com/)以取得下一代的防火牆（NGFW）和其他協力廠商供應專案，提供熟悉的安全性工具及大幅增強的網路安全性層級。 設定可能比較複雜，但協力廠商供應專案可讓您使用現有的功能和技能集。

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>使用專用的 WAN 連結避免暴露于網際網路
許多組織已選擇混合式 IT 路由。 使用混合式 IT 時，部分公司的資訊資產會在 Azure 中，而其他則會保留在內部部署。 在許多情況下，服務的某些元件是在 Azure 中執行，而其他元件則維持在內部部署上。

在混合式 IT 案例中，通常會有某種類型的跨單位連線能力。 跨單位連線可讓公司將其內部部署網路連線到 Azure 虛擬網路。 可用的跨單位連線解決方案有兩個︰

* [站對站 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)。 這是受信任、可靠且已建立的技術，但連線會透過網際網路進行。 頻寬限制為最多 1.25 Gbps。 在某些情況下，站對站 VPN 是理想的選項。
* **Azure ExpressRoute**。 我們建議您針對跨單位連線改用 [ExpressRoute](../../expressroute/expressroute-introduction.md)。 ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 透過 ExpressRoute，您可以建立與 Microsoft 雲端服務（例如 Azure、Office 365 和 Dynamics 365）的連接。 ExpressRoute 是內部部署位置或 Microsoft Exchange 主機服務提供者之間專用的 WAN 連結。 因為這是一個電信連線，所以您的資料不會透過網際網路傳輸，因此不會暴露在網際網路通訊的潛在風險中。

ExpressRoute 連線的位置可能會影響防火牆容量、擴充性、可靠性和網路流量可見度。 您必須識別在現有（內部部署）網路中終止 ExpressRoute 的位置。 您可以：

- 如果您需要繼續現有的資料中心隔離，或只是將外部網路資源放在 Azure 上，請在防火牆外終止（周邊網路範例）。
- 在防火牆內終止（網路延伸模組範例）。 這是預設建議。 在所有其他情況下，建議您將 Azure 視為第 n 個資料中心。

## <a name="optimize-uptime-and-performance"></a>將執行時間和效能最佳化
如果服務已關閉，就無法存取訊號。 如果因為效能不佳而使資料無法使用，您可以將該資料視為無法存取。 從安全性觀點來看，您需要盡可能地確保您的服務有最佳的執行時間和效能。

增強可用性和效能之常見且有效的方法是負載平衡。 負載平衡是將網路流量分散於服務中各伺服器的方法。 比方說，如果您的服務中有前端 Web 伺服器，您可以使用負載平衡將流量分散於多部前端 Web 伺服器。

此流量分散會提高可用性，因為如果其中一部 Web 伺服器無法使用，負載平衡器將會停止將流量傳送到該伺服器，並將流量重新導向至仍在運作的伺服器。 負載平衡也有助於效能，因為服務要求的處理器、網路和記憶體額外負荷會分散於所有負載平衡的伺服器。

建議您儘可能為您的服務採用適當的負載平衡。 下列是在 Azure 虛擬網路層級和全域層級的案例，以及兩者的負載平衡選項。

**案例**：您擁有的應用程式：

- 需要來自相同使用者/用戶端工作階段的要求，到達相同的後端虛擬機器。 此情況的範例為：購物車應用程式和網頁郵件伺服器。
- 只接受安全連線，因此對伺服器的未加密通訊是不接受的選項。
- 需要在同一個長時間執行之 TCP 連線上的多個 HTTP 要求，會路由傳送到或進行負載平衡分散到不同的後端伺服器。

**負載平衡選項**：使用 [Azure 應用程式閘道](/azure/application-gateway/application-gateway-introduction) (HTTP Web 流量負載平衡器)。 應用程式閘道支援在閘道上的端對端 SSL 加密和 [SSL 終止](/azure/application-gateway/application-gateway-introduction)。 網頁伺服器可以不用再承受加密和解密的負荷，且流量未經加密就流向後端伺服器。

**案例**：您需要將來自網際網路的傳入連線，進行負載平衡分散到位在 Azure 虛擬網路中的伺服器之間。 案例如當您：

- 有接受來自網際網路傳入要求的無狀態應用程式時。
- 不需要粘滯話或 SSL 卸載。 黏性工作階段是搭配應用程式負載平衡，以達到伺服器親和性的方法。

**負載平衡選項**：使用 Azure 入口網站[建立外部負載平衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)，將傳入要求分散到多個 VM，以提供較高層級的可用性。

**案例**：您需要對來自虛擬機器 (不在網際網路上) 的連線進行負載平衡。 在大部分情況下，系統接受要進行負載平衡的連線是由 Azure 虛擬網路上的裝置起始，例如 SQL Server 執行個體或內部網頁伺服器。   
**負載平衡選項**：使用 Azure 入口網站[建立內部負載平衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)，將傳入要求分散到多個 VM，以提供較高層級的可用性。

**案例**：您需要全域負載平衡，因為：

- 有廣泛分散在多個區域的雲端解決方案，且需要有可行的最高層級執行時間 (可用性)。
- 需要有可行的最高層級執行時間，以確保當整個資料中心都無法使用時，依然可以取得您的服務。

**負載平衡選項**：使用 Azure 流量管理員。 流量管理員可以根據使用者的位置，對您服務的連線進行負載平衡。

例如，如果使用者從 EU 對您的服務提出要求，此連線則會被導向到您位於 EU 資料中心的服務。 這部分的流量管理員全域負載平衡有助於改善效能，因為連接到最近的資料中心比連接到遠處的資料中心還要快。

## <a name="disable-rdpssh-access-to-virtual-machines"></a>停用對虛擬機器的 RDP/SSH 存取
您可以使用[遠端桌面通訊協定](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)（RDP）和[安全殼層](https://en.wikipedia.org/wiki/Secure_Shell)（SSH）通訊協定來連接到 Azure 虛擬機器。 透過這些通訊協定可允許遠端的管理 VM，且它們是資料中心計算的標準。

在網際網路上使用這些通訊協定的潛在安全性問題是，攻擊者可以使用[暴力密碼破解](https://en.wikipedia.org/wiki/Brute-force_attack)技術來取得 Azure 虛擬機器的存取權。 攻擊者取得存取權之後，就可以使用您的虛擬機器作為破壞您虛擬網路上其他電腦的啟動點，或甚至攻擊 Azure 之外的網路裝置。

建議停用從網際網路對您 Azure 虛擬機器的直接 RDP 和 SSH 存取。 停用從網際網路的直接 RDP 和 SSH 存取之後，您有其他選項可用來存取這些 VM，以進行遠端管理。

**案例**：讓單一使用者能透過網際網路連線到 Azure 虛擬網路。   
**選項**：[點對站 VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) 是遠端存取 VPN 用戶端/伺服器連線的另一種說法。 建立點對站連線之後，使用者就能使用 RDP 或 SSH 連線到位於使用者透過點對站 VPN 連線之 Azure 虛擬網路上的任何 VM。 此案例假設使用者有權連到這些 VM。

點對站 VPN 比直接 RDP 或 SSH 連線更安全，因為使用者必須先經過兩次驗證，才會連線到 VM。 首先，使用者必須經過驗證 (並獲得授權) 以建立點對站 VPN 連線。 然後，使用者必須經過驗證 (並獲得授權) 以建立 RDP 或 SSH 工作階段。

**案例**：讓您內部部署網路上的使用者可以連線到您 Azure 虛擬網路上的 VM。   
**選項**：[站對站 VPN](/azure/vpn-gateway/vpn-gateway-site-to-site-create) 透過網際網路將整個網路連線到另一個網路。 您可以使用站對站 VPN 將內部部署網路連線到 Azure 虛擬網路。 您內部部署網路上的使用者是透過站對站 VPN 連線，使用 RDP 或 SSH 通訊協定來連線。 您不需要允許透過網際網路的直接 RDP 或 SSH 存取。

**案例**：使用專用的 WAN 連結來提供類似站對站 VPN 的功能。   
**選項**：使用 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/)。 它提供類似站對站 VPN 的功能。 主要差別在於：

- 專用 WAN 連結不會跨越網際網路。
- 專用的 WAN 連結通常比較穩定且效能較好。

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>將重要的 Azure 服務資源只放到您的虛擬網路保護
使用虛擬網路服務端點可透過直接連線，將您的虛擬網路私人位址空間和虛擬網路的身分識別延伸至 Azure 服務。 端點可讓您將重要的 Azure 服務資源只放到您的虛擬網路保護。 從您的虛擬網路到 Azure 服務的流量一律會保留在 Microsoft Azure 骨幹網路上。

服務端點可提供下列優點：

- **改善 Azure 服務資源的安全性**：透過服務端點，可以將 Azure 服務資源放到虛擬網路保護。 將服務資源放到虛擬網路保護可透過完全移除資源的公用網際網路存取，而且只允許來自您虛擬網路的流量，藉此改善安全性。
- **來自虛擬網路之 Azure 服務流量的最佳路由**：虛擬網路中強制網際網路流量通過內部部署和 (或) 虛擬設備的任何路由 (也稱為強制通道)，也會強制 Azure 服務流量採用與網際網路流量相同的路由。 服務端點可提供 Azure 流量的最佳路由。

  端點一律會直接採用從虛擬網路到 Azure 骨幹網路上服務的服務流量。 將流量保持在 Azure 骨幹網路上，可讓您透過強制通道，繼續稽核和監視來自虛擬網路的輸出網際網路流量，而不會影響服務流量。 深入了解[使用者定義的路由和強制通道](../../virtual-network/virtual-networks-udr-overview.md)。

- **設定簡單且管理額外負荷較小**：虛擬網路中不再需要保留的公用 IP 位址，就可以透過 IP 防火牆保護 Azure 資源。 設定服務端點時不需要 NAT 或閘道裝置。 只要在子網路上按一下，即可設定服務端點。 維護端點沒有額外的負荷。

若要深入了解服務端點，以及在哪些區域有哪些 Azure 服務的服務端點可取得，請參閱[虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)。

## <a name="next-steps"></a>後續步驟
如需更多安全性最佳做法，請參閱[azure 安全性最佳作法和模式](best-practices-and-patterns.md)，以瞭解使用 azure 設計、部署和管理雲端解決方案時的使用方式。
