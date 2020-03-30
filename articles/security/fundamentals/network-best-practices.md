---
title: 網路安全最佳實踐 - 微軟 Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299340"
---
# <a name="azure-best-practices-for-network-security"></a>網路安全的 Azure 最佳實踐
本文討論了增強網路安全的 Azure 最佳實踐的集合。 這些最佳作法衍生自我們的 Azure 網路經驗和客戶的經驗。

針對每個最佳做法，本文說明：

* 最佳作法是什麼
* 您為何想要啟用該最佳作法
* 如果無法啟用最佳作法，結果可能為何
* 最佳作法的可能替代方案
* 如何學習啟用最佳作法

這些最佳實踐基於共識，Azure 平臺功能和功能集，因為它們在撰寫本文時存在。 意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

## <a name="use-strong-network-controls"></a>使用強大的網路控制
您可以透過將 [Azure 虛擬機器 (VM)](https://azure.microsoft.com/services/virtual-machines/) 和設備置於 [Azure 虛擬網路](../../virtual-network/index.yml)上，來將它們連線到其他網路裝置。 也就是說，您可以將虛擬網路介面卡連線到虛擬網路，讓有網路功能的裝置之間可進行 TCP/IP 型通訊。 連線到 Azure 虛擬網路的虛擬機器能夠連線到在相同虛擬網路、其他虛擬網路、網際網路或甚至您自己的內部部署網路上的裝置。

在規劃網路和網路安全時，我們建議您集中：

- 管理核心網路功能，如 ExpressRoute、虛擬網路和子網預配以及 IP 定址。
- 管理網路安全元素，如網路虛擬裝置功能，如 ExpressRoute、虛擬網路和子網預配以及 IP 定址。

如果使用一組通用的管理工具來監視網路和網路的安全性，則可以清楚地瞭解這兩種功能。 簡單、統一的安全性原則減少了錯誤，因為它提高了人類的理解和自動化的可靠性。

## <a name="logically-segment-subnets"></a>以邏輯方式分割子網路
Azure 虛擬網路與本地網路上的 LAN 類似。 Azure 虛擬網路背後的理念是，您可以基於單個私人 IP 位址空間創建網路，您可以在其中放置所有 Azure 虛擬機器。 可用的私人 IP 位址空間位於類別 A (10.0.0.0/8)、類別 B (172.16.0.0/12) 和類別 C (192.168.0.0/16) 範圍中。

以邏輯方式分割子網路的最佳做法包括：

**最佳實踐**：不要分配範圍廣的允許規則（例如，允許 0.0.0.0 到 255.255.255）。  
**詳細資訊**：確保故障排除程式阻止或禁止設置這些類型的規則。 這些規則會導致虛假的安全感，並且經常被紅色團隊發現和利用。

**最佳做法**：將較大的位址空間分割成子網路。   
**詳細資料**：您可以使用 [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 型子網路原則來建立子網路。

**最佳做法**：建立子網路之間的網路存取控制。 子網之間的路由會自動發生，您無需手動設定路由表。 預設情況下，在 Azure 虛擬網路上創建的子網之間沒有網路訪問控制項。   
**詳細資訊**：使用[網路安全性群組](/azure/virtual-network/virtual-networks-nsg)防止未經請求的流量進入 Azure 子網。 網路安全性群組是簡單、有狀態的資料包檢測設備，使用 5 元組方法（源 IP、源埠、目標 IP、目標埠和第 4 層協定）為網路流量創建允許/拒絕規則。 您可以允許或拒絕單一 IP 位址、多個 IP 位址或整個子網路的輸入或輸出流量。

在子網之間使用網路安全性群組進行網路存取控制時，可以將屬於同一安全區域的資源或角色放在自己的子網中。

**最佳實踐**：避免小型虛擬網路和子網，以確保簡單性和靈活性。   
**詳細資訊**：大多陣列織添加的資源比最初計畫的資源多，重新分配位址是勞動密集型的。 使用小型子網會增加有限的安全值，並將網路安全性群組映射到每個子網會增加開銷。 廣泛定義子網，以確保您具有增長的靈活性。

**最佳實踐**：通過定義[應用程式安全性群組](https://azure.microsoft.com/blog/applicationsecuritygroups/)來簡化網路安全性群組規則管理。  
**詳細資訊**：為您認為將來可能會更改或跨多個網路安全性群組使用的 IP 位址清單定義應用程式安全性群組。 請務必明確命名應用程式安全性群組，以便其他人能夠瞭解其內容和目的。

## <a name="adopt-a-zero-trust-approach"></a>採用零信任方法
基於週邊的網路運行假設網路中的所有系統都可以信任。 但是，當今的員工在各種設備和應用程式上從任何位置訪問其組織的資源，這使得週邊安全控制變得無關緊要。 只關注誰可以訪問資源的存取控制策略是不夠的。 為了掌握安全性和工作效率之間的平衡，安全管理員還需要考慮*如何*訪問資源。

網路需要從傳統防禦中發展，因為網路可能容易受到破壞：攻擊者可能會破壞受信任邊界內的單個終結點，然後在整個網路中快速擴展立足點。 [零信任](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/)網路消除了基於周邊網路位置的信任概念。 相反，零信任體系結構使用設備和使用者信任聲明來對組織資料和資源的訪問進行門禁。 對於新計畫，採用零信任方法，在訪問時驗證信任。

最佳做法包括：

**最佳實踐**：根據設備、標識、保證、網路位置等對資源進行條件訪問。  
**詳細資訊**[：Azure AD 條件訪問](/azure/active-directory/conditional-access/overview)允許您根據所需的條件實現自動存取控制決策，從而應用正確的存取控制。 有關詳細資訊，請參閱[使用條件訪問管理 Azure 管理的訪問](../../role-based-access-control/conditional-access-azure-management.md)。

**最佳做法**：僅在工作流批准後啟用埠訪問。  
**詳細資訊**：您可以使用[Azure 安全中心中的即時 VM 訪問](../../security-center/security-center-just-in-time.md)來鎖定 Azure VM 的入站流量，從而減少攻擊風險，同時在需要時輕鬆訪問 VM。

**最佳做法**：授予執行特權任務的臨時許可權，以防止惡意或未經授權的使用者在許可權過期後獲取存取權限。 只有當使用者需要時才會獲得存取權。  
**詳細資訊**：在 Azure AD 特權標識管理或協力廠商解決方案中使用即時存取權限，以授予執行特權任務的許可權。

零信任是網路安全的下一個演變。 網路攻擊狀態促使組織採取"假定違約"的心態，但這種方法不應受到限制。 零信託網路保護企業資料和資源，同時確保組織能夠通過使用技術構建現代工作場所，使員工能夠隨時隨地、以任何方式提高工作效率。

## <a name="control-routing-behavior"></a>控制路由行為
當您在 Azure 虛擬網路上放置虛擬機器時，該 VM 可以連線到相同虛擬網路上的任何其他 VM，即使其他 VM 位於不同子網路亦然。 可以這麼做的原因是在預設情況下，有一組系統路由集合已啟用，使 VM 可以進行此類型的通訊。 這些預設的路由讓在相同虛擬網路上的 VM 可以起始彼此之間的連線，及與網際網路的連線 (僅對網際網路的輸出通訊)。

雖然預設系統路由適用於許多部署案例，但您有時會想為您的部署自訂路由設定。 您可以將下一個躍點位址設定成到達特定的目的地。

我們建議您在部署虛擬網路的安全性設備時，設定[使用者定義的路由](../../virtual-network/virtual-networks-udr-overview.md)。 我們稍後會在標題為[將重要的 Azure 服務資源只放到您的虛擬網路保護](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks)的小節討中論此主題。

> [!NOTE]
> 不需要使用者定義的路由，而預設的系統路由通常能運作。
>
>

## <a name="use-virtual-network-appliances"></a>使用虛擬網路應用裝置
網路安全性群組和使用者定義的路由可以為[OSI模型](https://en.wikipedia.org/wiki/OSI_model)的網路和傳輸層提供一定的網路安全。 但在某些情況下，您會想要或需要在堆疊的高層級啟用安全性。 在這類情況下，建議您部署 Azure 合作夥伴所提供的虛擬網路安全性應用裝置。

Azure 網路安全性設備可提供比網路層級控制更佳的安全性。 虛擬網路安全性設備的網路安全性功能包括：


* 防火牆
* 入侵偵測/入侵預防
* 弱點管理
* 應用程式控制
* 以網路為基礎的異常偵測
* Web 篩選
* 防毒
* 殭屍網路防護

要查找可用的 Azure 虛擬網路安全設備，請訪問[Azure 應用商店](https://azure.microsoft.com/marketplace/)並搜索"安全性"和"網路安全"。

## <a name="deploy-perimeter-networks-for-security-zones"></a>部署安全性區域的周邊網路
[周邊網路](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) (也稱為 DMZ) 是實體或邏輯網路區段，可在您的資產與網際網路之間提供額外一層安全性。 在周邊網路邊緣上的特製化網路存取控制裝置，只允許所要的流量進入您的虛擬網路。

周邊網路非常實用，因為您可以將網路存取控制管理、監視、記錄和報告的重點放在位於 Azure 虛擬網路邊緣的裝置。 周邊網路是您通常啟用分散式阻斷服務 （DDoS） 防護、入侵偵測/入侵防禦系統 （IDS/IPS）、防火牆規則和策略、Web 篩選、網路反惡意軟體等功能。 網路安全性裝置位於網際網路與您的 Azure 虛擬網路之間，具有兩個網路均適用的介面。

雖然這是周邊網路的基本設計，但有很多不同的設計，如背對背、三套和多家式設計。

基於前面提到的零信任概念，我們建議您考慮對所有高安全性部署使用周邊網路，以提高 Azure 資源的網路安全和存取控制級別。 您可以使用 Azure 或協力廠商解決方案在資產和 Internet 之間提供額外的安全層：

- Azure 本機控制項。 [Azure 防火牆](/azure/firewall/overview)和[應用程式閘道中的 Web 應用程式防火牆](../../application-gateway/features.md#web-application-firewall)提供基本安全性，以完全狀態的防火牆為服務、內置高可用性、不受限制的雲可伸縮性、FQDN 篩選、對 OWASP 核心規則集的支援以及簡單的設置和配置。
- 協力廠商產品。 在[Azure 應用商店](https://azuremarketplace.microsoft.com/)中搜索下一代防火牆 （NGFW） 和其他提供熟悉安全工具和顯著增強的網路安全級別的協力廠商產品。 配置可能更為複雜，但協力廠商產品可能允許您使用現有功能和技能集。

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>通過專用廣域網路連結避免接觸互聯網
許多組織已選擇混合式 IT 路由。 使用混合 IT，公司的某些資訊資產位於 Azure 中，而其他資訊資產則保留在本地。 在許多情況下，服務的某些元件是在 Azure 中執行，而其他元件則維持在內部部署上。

在混合 IT 方案中，通常存在某些類型的跨界連接。 跨單位連線可讓公司將其內部部署網路連線到 Azure 虛擬網路。 可用的跨單位連線解決方案有兩個︰

* [網站間 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)： 這是一個值得信賴的，可靠的，成熟的技術，但連接發生在互聯網上。 頻寬被限制到最大約 1.25 Gbps。 在某些情況下，網站到網站 VPN 是理想的選擇。
* **Azure 快速路由**。 我們建議您針對跨單位連線改用 [ExpressRoute](../../expressroute/expressroute-introduction.md)。 ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 使用 ExpressRoute，您可以建立與 Microsoft 雲服務（如 Azure、Office 365 和 Dynamics 365）的連接。 ExpressRoute 是本地位置或 Microsoft Exchange 託管供應商之間的專用 WAN 鏈路。 因為這是一個電信連接，您的資料不會通過互聯網傳播，所以它不會接觸到互聯網通信的潛在風險。

ExpressRoute 連接的位置可能會影響防火牆容量、可擴充性、可靠性和網路流量可見度。 您需要確定在現有（本地）網路中終止 ExpressRoute 的位置。 您可以：

- 如果需要對流量進行可見度，則需要繼續隔離資料中心的現有做法，或者僅將外聯網資源放在 Azure 上，則在防火牆外部終止（周邊網路範例）。
- 在防火牆內終止（網路擴展範例）。 這是預設建議。 在所有其他情況下，我們建議將 Azure 視為第 n 個資料中心。

## <a name="optimize-uptime-and-performance"></a>將執行時間和效能最佳化
如果服務已關閉，則無法訪問資訊。 如果因為效能不佳而使資料無法使用，您可以將該資料視為無法存取。 從安全性觀點來看，您需要盡可能地確保您的服務有最佳的執行時間和效能。

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
- 不需要粘性會話或 SSL 卸載。 黏性工作階段是搭配應用程式負載平衡，以達到伺服器親和性的方法。

**負載平衡選項**：使用 Azure 入口網站[建立外部負載平衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)，將傳入要求分散到多個 VM，以提供較高層級的可用性。

**案例**：您需要對來自虛擬機器 (不在網際網路上) 的連線進行負載平衡。 在大部分情況下，系統接受要進行負載平衡的連線是由 Azure 虛擬網路上的裝置起始，例如 SQL Server 執行個體或內部網頁伺服器。   
**負載平衡選項**：使用 Azure 入口網站[建立內部負載平衡器](../../load-balancer/quickstart-load-balancer-standard-public-portal.md)，將傳入要求分散到多個 VM，以提供較高層級的可用性。

**案例**：您需要全域負載平衡，因為：

- 有廣泛分散在多個區域的雲端解決方案，且需要有可行的最高層級執行時間 (可用性)。
- 需要有可行的最高層級執行時間，以確保當整個資料中心都無法使用時，依然可以取得您的服務。

**負載平衡選項**：使用 Azure 流量管理員。 流量管理員可以根據使用者的位置，對您服務的連線進行負載平衡。

例如，如果使用者從 EU 對您的服務提出要求，此連線則會被導向到您位於 EU 資料中心的服務。 這部分的流量管理員全域負載平衡有助於改善效能，因為連接到最近的資料中心比連接到遠處的資料中心還要快。

## <a name="disable-rdpssh-access-to-virtual-machines"></a>停用對虛擬機器的 RDP/SSH 存取
可以使用[遠端桌面協定](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)（RDP）[和安全外殼](https://en.wikipedia.org/wiki/Secure_Shell)（SSH） 協定訪問 Azure 虛擬機器。 透過這些通訊協定可允許遠端的管理 VM，且它們是資料中心計算的標準。

在網際網路上使用這些通訊協定的潛在安全性問題是，攻擊者可以使用[暴力密碼破解](https://en.wikipedia.org/wiki/Brute-force_attack)技術來取得 Azure 虛擬機器的存取權。 攻擊者取得存取權之後，就可以使用您的虛擬機器作為破壞您虛擬網路上其他電腦的啟動點，或甚至攻擊 Azure 之外的網路裝置。

建議停用從網際網路對您 Azure 虛擬機器的直接 RDP 和 SSH 存取。 停用從網際網路的直接 RDP 和 SSH 存取之後，您有其他選項可用來存取這些 VM，以進行遠端管理。

**案例**：讓單一使用者能透過網際網路連線到 Azure 虛擬網路。   
**選項**：[點對站 VPN](/azure/vpn-gateway/vpn-gateway-point-to-site-create) 是遠端存取 VPN 用戶端/伺服器連線的另一種說法。 建立點對站連線之後，使用者就能使用 RDP 或 SSH 連線到位於使用者透過點對站 VPN 連線之 Azure 虛擬網路上的任何 VM。 此案例假設使用者有權連到這些 VM。

點對站 VPN 比直接 RDP 或 SSH 連線更安全，因為使用者必須先經過兩次驗證，才會連線到 VM。 首先，使用者必須經過驗證 (並獲得授權) 以建立點對站 VPN 連線。 然後，使用者必須經過驗證 (並獲得授權) 以建立 RDP 或 SSH 工作階段。

**案例**：讓您內部部署網路上的使用者可以連線到您 Azure 虛擬網路上的 VM。   
**選項**：[站對站 VPN](/azure/vpn-gateway/vpn-gateway-site-to-site-create) 透過網際網路將整個網路連線到另一個網路。 您可以使用站對站 VPN 將內部部署網路連線到 Azure 虛擬網路。 您內部部署網路上的使用者是透過站對站 VPN 連線，使用 RDP 或 SSH 通訊協定來連線。 您不必允許通過互聯網直接存取 RDP 或 SSH。

**案例**：使用專用的 WAN 連結來提供類似站對站 VPN 的功能。   
**選項**：使用 [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/)。 它提供類似站對站 VPN 的功能。 主要差別在於：

- 專用 WAN 連結不會遍歷互聯網。
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
有關使用 Azure 設計、部署和管理雲解決方案時要使用的更多安全最佳實踐，請參閱[Azure 安全最佳實踐和模式](best-practices-and-patterns.md)。
