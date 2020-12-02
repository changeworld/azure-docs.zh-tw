---
title: Azure 安全性基準測試 V2-網路安全性
description: Azure 安全性基準測試 V2 網路安全性
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: b6aaad9cce330d755fb69f8be48737f9a8f6c4a7
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487740"
---
# <a name="security-control-v2-network-security"></a>安全性控制 V2：網路安全性

網路安全性涵蓋保護和保護 Azure 網路的控制措施。 這包括保護虛擬網路、建立私人連線、預防和緩和外部攻擊，以及保護 DNS 的安全。

## <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：執行內部流量的安全性

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-1 | 9.2、9.4、14.1、14.2、14。3 | AC-4、CA-3、SC-7 |

確定所有 Azure 虛擬網路都遵循符合業務風險的企業分割原則。 任何可能會對組織產生更高風險的系統，都應該隔離在其自己的虛擬網路內，並使用網路安全性群組 (NSG) 和/或 Azure 防火牆來充分保護。 

根據您的應用程式和企業分割策略，根據網路安全性群組規則來限制或允許內部資源之間的流量。 針對特定定義完善的應用程式 (例如) 的三層式應用程式，這可能是高度安全的「預設拒絕，允許例外狀況」的方法。 如果您有許多應用程式和端點彼此互動，則這可能無法妥善調整。 您也可以在需要集中管理的情況下，在中樞/輪輻拓撲) 的大量企業區段或輪輻 (中使用 Azure 防火牆。 

使用 Azure 資訊安全中心調適型網路強化，以根據外部網路流量規則的參考，建議可限制埠和來源 Ip 的網路安全性群組設定。

使用 Azure Sentinel 探索舊版不安全的通訊協定（例如 SSL/TLSv1、SMBv1、LM/NTLMv1、wDigest、不帶正負號的 LDAP 系結，以及 Kerberos 中的弱式密碼）的使用方式。

- [如何建立具有安全性規則的網路安全性群組](../../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和設定 Azure 防火牆](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure 資訊安全中心中的適應性網路強化](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Sentinel 不安全的通訊協定活頁簿](../../sentinel/quickstart-get-visibility.md#use-built-in-workbooks)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)     

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-2-connect-private-networks-together"></a>NS-2：將私人網路連接在一起

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-2 | N/A | CA-3、AC-17、MA-4 |

使用 Azure ExpressRoute 或 Azure 虛擬私人網路 (VPN) ，在共置環境中的 Azure 資料中心與內部部署基礎結構之間建立私人連線。 ExpressRoute 連線不會經過公用網際網路，而且提供比一般網際網路連線更可靠、速度更快且延遲更低的位置。 針對點對站 VPN 和站對站 VPN，您可以使用這些 VPN 選項和 Azure ExpressRoute 的任意組合，將內部部署裝置或網路連線到虛擬網路。

若要將 Azure 中的兩個或多個虛擬網路連接在一起，請使用虛擬網路對等互連或 Private Link。 對等互連虛擬網路之間的網路流量是私人的，並且會保留在 Azure 骨幹網路上。 

- [什麼是 ExpressRoute 連線模型](../../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 總覽](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [虛擬網路對等互連](../../virtual-network/virtual-network-peering-overview.md)

- [Azure Private Link](../../private-link/private-link-service-overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立 Azure 服務的私人網路存取

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-3 | 14.1 | AC-4、CA-3、SC-7 |

使用 Azure Private Link 從您的虛擬網路啟用對 Azure 服務的私用存取，而不需要跨越網際網路。 在 Azure Private Link 尚無法使用的情況下，請使用 Azure 虛擬網路服務端點。  Azure 虛擬網路服務端點透過 Azure 骨幹網路上的優化路由，提供對服務的安全存取。  

除了 Azure 服務所提供的驗證和流量安全性之外，私用存取是額外的深度防禦措施。 

- [瞭解 Azure Private Link](../../private-link/private-link-overview.md)

- [瞭解虛擬網路服務端點](../../virtual-network/virtual-network-service-endpoints-overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保護應用程式和服務免于外部網路攻擊

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-4 | 9.5、12.3、12。9 | SC-5，SC-7 |

保護 Azure 資源免于來自外部網路的攻擊，包括分散式阻絕服務 (DDoS) 攻擊、應用程式特定攻擊，以及未經要求和潛在的惡意網際網路流量。 Azure 包含下列功能的原生功能：
-   使用 Azure 防火牆來保護應用程式和服務免于來自網際網路和其他外部位置的潛在惡意流量。 

-   使用 Web 應用程式防火牆 (Azure 應用程式閘道、Azure Front Door 和 Azure 內容傳遞網路 (CDN) 中的 WAF) 功能，以保護您的應用程式、服務和 Api 免于應用層的攻擊。 

-   藉由在 Azure 虛擬網路上啟用 DDoS 標準保護，來保護您的資產免于 DDoS 攻擊。 
-   使用 Azure 資訊安全中心來偵測與上述相關的設定錯誤的風險。 

- [Azure 防火牆檔](../../firewall/index.yml)

- [如何部署 Azure WAF](../../web-application-firewall/overview.md)

- [使用 Azure 入口網站管理 Azure DDoS 保護標準](../../ddos-protection/manage-ddos-protection.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

無

## <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵偵測/入侵防護系統 (IDS/IPS) 

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-5 | 12.6、12。7 | SI-4 |

使用 Azure 防火牆威脅情報型篩選來警示和/或封鎖進出已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 當需要承載檢查時，您可以部署協力廠商入侵偵測/入侵，防止系統 (IDS/IPS) 從具有承載檢查功能的 Azure Marketplace。 或者，您也可以使用以主機為基礎的 IDS/IPS，或是以主機為基礎的端點偵測和回應， (EDR) 解決方案與或（而不是以網路為基礎的識別碼/IP）一起使用。  

注意：如果您有關于 IDS/IPS 使用的法規或其他需求，請務必調整它，以提供高品質的警示給您的 SIEM 解決方案。 

- [如何部署 Azure 防火牆](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace 包含協力廠商識別碼功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS)

- [Microsoft Defender ATP EDR 功能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops) 

## <a name="ns-6-simplify-network-security-rules"></a>NS-6：簡化網路安全性規則

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-6 | 1.5 | IA-4 |

利用服務標記和應用程式安全性群組 (Asg) 來簡化網路安全性規則。 

使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的 [來源] 或 [目的地] 欄位中指定服務標記名稱，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

您也可以使用應用程式安全性群組來協助簡化複雜的安全性設定。 應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然延伸，而不是根據網路安全性群組中的明確 IP 位址來定義原則，可讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。

- [瞭解和使用服務標記](../../virtual-network/service-tags-overview.md)

- [瞭解及使用應用程式安全性群組](../../virtual-network/network-security-groups-overview.md#application-security-groups)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全的功能變數名稱服務 (DNS) 

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| NS-7 | N/A | SC-20、SC-21 |

遵循 DNS 安全性的最佳作法來緩和常見的攻擊，例如解除關聯的 DNS、DNS amplifications 攻擊、DNS 中毒和詐騙等等。 

當 Azure DNS 作為授權 DNS 服務使用時，請確定使用 Azure RBAC 和資源鎖定來防止 DNS 區域和記錄遭到意外或惡意修改。 

- [Azure DNS 總覽](../../dns/dns-overview.md)

- [安全的網域名稱系統 (DNS) 部署指南](https://csrc.nist.gov/publications/detail/sp/800-81/2/final)

- [防止無關聯的 DNS 專案，並避免子域接管](../fundamentals/subdomain-takeover.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)   

- [應用程式安全性與 DevOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)