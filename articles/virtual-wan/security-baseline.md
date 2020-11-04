---
title: 適用于虛擬 WAN 的 Azure 安全性基準
description: 虛擬 WAN 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 409c8d163b52e56a1eef68a23e4db79956a68722
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328676"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>適用于虛擬 WAN 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 MICROSOFT AZURE 虛擬 WAN。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于虛擬 WAN 的相關指引來分組。 已排除不適用虛擬 WAN 的 **控制項** 。

若要瞭解虛擬 WAN 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的虛擬 wan 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：執行內部流量的安全性

**指導** 方針： AZURE 虛擬 WAN 不支援直接部署至虛擬網路。 不過，您仍然可以對輪輻資源套用網路安全性群組規則、使用 Azure 防火牆的保護，或建立自訂路由表來防止或允許私人流量。

- [自訂路由案例](scenario-any-to-any.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="ns-2-connect-private-networks-together"></a>NS-2：將私人網路連接在一起 

**指導** 方針：使用 azure ExpressRoute 或 azure 虛擬私人網路 (VPN) ，在共置環境中的 azure 資料中心與內部部署基礎結構之間建立私人連線。 ExpressRoute 連線不會經過公用網際網路，提供比一般網際網路連線更可靠、速度更快且延遲更低的延遲。 

針對點對站 VPN 和站對站 VPN，使用這些 VPN 選項和 ExpressRoute 的任意組合，將內部部署裝置或網路連線到虛擬網路。 若要將 Azure 中的兩個或多個虛擬網路連接在一起，請使用虛擬網路對等互連。 對等互連虛擬網路之間的網路流量是私人的，並且會保留在 Azure 骨幹網路上。

- [虛擬 WAN 中的 ExpressRoute](virtual-wan-expressroute-portal.md)

- [站對站 VPN 總覽](virtual-wan-site-to-site-portal.md)

- [點對站 VPN 總覽](virtual-wan-point-to-site-portal.md)

- [私人連結](howto-private-link.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：共用

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立 Azure 服務的私人網路存取權

**指導** 方針：使用 Azure Private Link 從您的虛擬網路啟用對 AZURE 虛擬 WAN 的私用存取，而不需要跨越網際網路。 除了 Azure 服務所提供的驗證和流量安全性之外，私用存取是另一個深度防禦措施。

- [瞭解 Azure Private Link](../private-link/private-link-overview.md)

- [虛擬 Wan Private Link](howto-private-link.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保護應用程式和服務免于外部網路攻擊

**指導** 方針：保護您的 AZURE 虛擬 WAN 資源免于來自外部網路的攻擊，包括分散式阻絕服務 (DDoS) 攻擊、應用程式特定的攻擊，以及未經要求和潛在的惡意網際網路流量。 

使用 Azure 防火牆來保護應用程式和服務免于來自網際網路和其他外部位置的潛在惡意流量。 針對您的 Azure 虛擬網路上的攻擊，選擇您的資產 Azure DDoS 保護。 使用 Azure 資訊安全中心來偵測與網路相關資源相關的錯誤設定風險。

- [Azure 防火牆檔](/azure/firewall)

- [使用 Azure 入口網站管理 Azure DDoS 保護標準](/azure/virtual-network/manage-ddos-protection) 

- [Azure 資訊安全中心建議](../security-center/recommendations-reference.md#recs-network)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：在 AZURE 虛擬 WAN 中部署入侵偵測或防護系統的最佳方式，是在連接至虛擬中樞的輪輻網路中使用網路虛擬裝置。  如需詳細資訊，請參閱參考連結的路由案例。 

- [案例 - 透過 NVA 路由傳送流量](scenario-route-through-nva.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：簡化網路安全性規則

**指導** 方針：使用 Azure 虛擬網路服務標籤來定義 azure 網路安全性群組或針對您的虛擬 WAN 資源設定的 azure 防火牆上的網路存取控制。 

建立安全性規則時，請使用服務標記來取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如： {VirtualWAN： Virtual Network} ) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全的功能變數名稱服務 (DNS) 

**指導** 方針： AZURE 虛擬 WAN 為點對站 VPN 閘道提供自訂 DNS 伺服器。 

遵循 DNS 安全性的最佳作法來緩和常見的攻擊，例如無關聯的 DNS、DNS amplifications 攻擊、DNS 中毒和詐騙等等。

當 Azure DNS 做為您的授權 DNS 服務時，請使用 Azure 角色型存取控制來保護 DNS 區域和記錄，以防止意外或惡意的修改 (Azure RBAC) 和資源鎖定。

- [Azure DNS 總覽](../dns/dns-overview.md) 

- [安全的網域名稱系統 (DNS) 部署指南](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [防止無關聯的 DNS 專案，並避免子域接管](../security/fundamentals/subdomain-takeover.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="identity-management"></a>身分識別管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)。*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別和驗證系統

**指導** 方針： AZURE 虛擬 WAN 的點對站 VPN 與 Azure Active Directory 的 (Azure AD) （這是 azure 的預設身分識別和存取管理服務）整合。 您應該將 Azure AD 標準化，以管理組織的身分識別和存取管理：

- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 應用程式。
- 您組織的資源，例如 Azure 上的應用程式或公司網路資源。

在貴組織的雲端安全性實務中，以高優先順序保護 Azure AD。 使用 Azure 資訊安全中心的安全性分數功能來評定您的身分識別和安全性狀態，以評估您的設定與最佳作法建議的緊密程度。 針對安全性狀態的改進，執行 Microsoft 的最佳作法建議。

Azure AD 也支援外部身分識別，讓沒有 Microsoft 帳戶的使用者可以使用其外部身分識別登入其應用程式和資源。 請參閱參考連結中有關使用點對站 VPN 案例中 Azure AD 的資訊。

- [為 P2S OpenVPN 通訊協定連線建立 Azure Active Directory (AD) 租使用者](openvpn-azure-ad-tenant-multi-app.md)

- [設定使用者 VPN 的 Azure Active Directory authentication](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory 中的租用](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [使用應用程式的外部識別提供者](/azure/active-directory/b2b/identity-providers)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用強式驗證控制項

**指導** 方針： AZURE 虛擬 WAN 的點對站 VPN 與 Azure Active Directory 的 (Azure AD) 整合，其可支援具有多重要素驗證的強大驗證控制項，以及強式無密碼方法。

- 多重要素驗證-啟用 Azure AD 多重要素驗證，並遵循 Azure 資訊安全中心的身分識別和存取管理建議，以取得多重要素驗證設定的最佳做法。 您可以根據登入條件和風險因素，針對所有、選取的使用者或依使用者的層級強制執行多重要素驗證。

- 無密碼 authentication –有三個無密碼 authentication 選項可供使用。 這些包括、Windows Hello 企業版、Microsoft Authenticator 應用程式，以及內部部署驗證方法（例如智慧卡）。

對於系統管理員和特殊許可權的使用者，請確定使用強式驗證方法的最高層級，然後再向其他使用者推出適當的增強式驗證原則。

- [如何在 P2S VPN 中為虛擬 WAN 啟用 MFA](openvpn-azure-ad-mfa.md)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針： AZURE 虛擬 WAN 的點對站 VPN 支援根據使用者定義的條件，以更細微的存取控制 Azure AD 條件式存取。 例如，來自特定 IP 範圍的使用者登入必須使用多重要素驗證來進行登入。 細微驗證會話管理原則也可以用於不同的使用案例。

- [一般條件式存取原則](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [使用條件式存取設定驗證會話管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

- [虛擬 Wan P2S VPN 條件式存取](openvpn-azure-ad-mfa.md#conditional)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除非預期的認證暴露

**指引** ：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

在 GitHub 中，您可以使用原生密碼掃描功能來識別程式碼中的認證或其他形式的秘密。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub 秘密掃描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="privileged-access"></a>特殊許可權存取

*如需詳細資訊，請參閱 [Azure 安全性基準測試：](/azure/security/benchmarks/security-controls-v2-privileged-access)特殊許可權存取。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對商務關鍵性系統的系統管理存取

**指導** 方針： AZURE 虛擬 WAN 使用 azure 角色型存取控制 (azure RBAC) ，藉由限制哪些帳戶被授與存取權給他們所在的訂用帳戶和管理群組，來隔離對商務關鍵性系統的存取。

也限制存取管理、身分識別和安全性系統，這些系統具有您商務關鍵性存取權的系統管理存取權，例如 Active Directory 網網域控制站、安全性工具和系統管理工具，並將代理程式安裝在商務關鍵系統上。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理群組存取](../governance/management-groups/overview.md#management-group-access) 

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊許可權的存取工作站

**指導** 方針：安全、隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 使用高度安全的使用者工作站或 Azure 防禦來進行系統管理工作。 使用 Azure Active Directory (Azure AD) 、Microsoft Defender 進階威脅防護 (ATP) 或 Microsoft Intune 部署安全且受管理的使用者工作站以進行系統管理工作。 受保護的工作站可以集中管理以強制執行安全的設定，包括強式驗證、軟體和硬體基準、受限的邏輯和網路存取。

- [瞭解特殊許可權的存取工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [部署特殊許可權存取工作站](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)。*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密傳輸中的機密資訊

**指導** 方針：加密對於外部和公用網路上的流量而言是不可或缺的。

- 使用存取控制，傳輸資料時應受到保護，以防止「頻外」攻擊 (例如使用加密的流量捕捉) ，以確保攻擊者無法輕易地讀取或修改資料。

- 確定 HTTP 流量，任何連線至 Azure 資源的用戶端都可以與 TLS 1.2 或更新版本協商。-

- 針對遠端系統管理，請使用適用于 Linux) 的 SSH (或適用于 Windows) 的 RDP/TLS (，而不是未加密的通訊協定。 應停用已過時的 SSL/TLS/SSH 版本、通訊協定和弱式加密。-

- 在基礎結構上，Azure 預設會針對 Azure 資料中心之間的資料流量，提供傳輸加密中的資料。

一般而言，我們會在安全的 Microsoft 骨幹上提供加密，以及透過 Azure ExpressRoute 和點對站使用者 VPN，在站對站 VPN、站對站 VPN 中加密流量的機會。

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [TLS 安全性的資訊](/security/engineering/solving-tls1-problem)

- [Azure 資料傳輸中的雙重加密](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

## <a name="asset-management"></a>資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)。*

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>上午-2：確保安全性小組可以存取資產清查和中繼資料

**指導** 方針：將標記套用至您的 Azure 資源、資源群組和訂用帳戶，以邏輯方式將它們組織成分類法。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

Azure 虛擬 WAN 也支援以 Azure Resource Manager 為基礎的資源部署和 Azure Resource Graph 查詢。 

- [如需標記資產的詳細資訊，請參閱資源命名和標記決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md) 

- [Azure 資訊安全中心資產庫存管理](../security-center/asset-inventory.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="am-3-use-only-approved-azure-services"></a>上午-3：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來限制可以在您的環境中布建的服務。 使用訂用帳戶內的 Azure Resource Graph 來查詢和探索資源，並使用 Azure 監視器來建立規則，以在偵測到未核准的服務時觸發警示。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定資源類型](../governance/policy/samples/built-in-policies.md#general)

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="logging-and-threat-detection"></a>記錄和威脅偵測

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別與存取管理的威脅偵測

**指導** 方針： Azure Active Directory (Azure AD) 提供下列使用者記錄，可在 Azure AD 報告中查看，或與 Azure 監視器、AZURE SENTINEL、SIEM 或監視工具整合，以取得更精密的監視和分析使用案例。 這些警告是：

- 登入-[登入] 報表會提供受控應用程式使用方式和使用者登入活動的相關資訊。
- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 Audit 記錄檔的範例包括對 Azure AD 中任何資源所做的變更，例如新增或移除使用者、應用程式、群組、角色和原則。
- 有風險的登入-具風險的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。
- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

使用 Azure 資訊安全中心來建立特定可疑活動的警示，例如在訂用帳戶中包含已淘汰帳戶的失敗驗證嘗試次數過多。 除了基本的安全性防護監視之外，您還可以使用資訊安全中心的威脅防護模組，從個別的 Azure 計算資源收集更深入的安全性警示 (虛擬機器、容器、app service) 、資料資源 (SQL DB 和儲存體) ，以及 Azure 服務層級。 這項功能可讓您在個別資源內看到帳戶異常。

- [Azure Active Directory 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

**指導** 方針：使用 VPN 封包捕獲工具來記錄在您的 AZURE 虛擬 WAN 資源之間移動的網路封包。 這可協助您進行與混合式網路相關的偵錯工具。 雖然您無法在虛擬 WAN 上部署網路安全性群組，但您可以將它部署到輪輻虛擬網路資源。

在網路安全性群組上啟用網路安全性群組流量記錄以進行流量審核。

啟用 Azure 流量分析功能來處理保留在儲存體帳戶中的流量記錄，然後將它們傳送至 Log Analytics 工作區。 流量分析可讓您深入瞭解 Azure 網路的流量流程。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md) 

虛擬 Wan 不會產生或處理需要啟用的 DNS 查詢記錄。

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄

**指導** 方針： azure 活動記錄（自動啟用）會包含 AZURE 虛擬 WAN 資源 (PUT、POST、DELETE) 的所有寫入作業，但讀取 (取得) 作業除外。 活動記錄可以用來在進行疑難排解時找出錯誤，或是監視組織中的使用者修改資源的方式。 不過，虛擬 WAN 不會產生 Azure 資源記錄。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 
- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中安全性記錄檔管理和分析

**指導** 方針：集中記錄儲存體和分析以啟用相互關聯。 針對每個記錄來源，請確定您已指派資料擁有者、存取指引、儲存位置、使用哪些工具來處理和存取資料，以及資料保留需求。 確定您要將 Azure 活動記錄整合至中央記錄系統。 

透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，您可以使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。 此外，請啟用資料並將其上架至 Azure Sentinel 或協力廠商 SIEM。

許多組織選擇使用 Azure Sentinel 「經常性存取」資料，而這些資料經常使用，並 Azure 儲存體使用較不常使用的「冷」資料。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：共用

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備–更新 Azure 的事件回應程式

**指導** 方針：確定您的組織有處理常式來回應安全性事件、已更新 Azure 的這些處理常式，並會定期執行這些程式以確保其就緒。 確定您的服務供應專案已包含在事件回應程式中（適用的話）。

- [跨企業環境執行安全性](https://aka.ms/AzSec4) 
- [事件回應參考指南](https://aka.ms/IRRG)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備–安裝事件通知

**指導** 方針：在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料已被非法或未經授權的物件存取，Microsoft 會使用此連絡人資訊來與您聯繫。 您也可以選擇根據您的事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。

如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料已被非法或未經授權的物件存取，Microsoft 會使用安全性事件連絡人資訊來與您聯繫。 在事實之後查看事件，以確保能完全解決問題。

- [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md) 

- [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析–根據高品質警示建立事件

**指導** 方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性是以「資訊安全中心」在「尋找」或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

此外，清楚地將訂用帳戶標示 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md) 

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測和分析–調查事件

**指引** ：為組織製作事件回應指南。 進行練習，定期測試您的系統事件回應功能。 找出弱點和落差，並視需要修訂計畫。

請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

- [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md) 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process) 

- [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測和分析-設定事件優先順序

**指導** 方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，可清楚地將訂用帳戶標示 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md) 

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：內含專案、根除和復原–將事件處理自動化

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Azure Logic Apps」來自動觸發安全性警示和建議的回應。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

## <a name="posture-and-vulnerability-management"></a>狀況和弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：狀態和弱點管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：進行定期攻擊模擬

**指導** 方針：根據您的需求，針對您的 Azure 資源進行滲透測試或 red team 活動，並確保能補救所有重要的安全性結果。

遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 使用 Microsoft 的策略、對 Microsoft 管理的雲端基礎結構、服務和應用程式執行 Red 小組和即時網站滲透測試。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md)

- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="endpoint-security"></a>端點安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：端點安全性](/azure/security/benchmarks/security-controls-v2-endpoint-security)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用端點偵測和回應 (EDR) 

**指導** 方針：不明確允許客戶設定端點偵測和回應設定。 不過，Azure 虛擬 WAN 供應專案中使用的虛擬機器會使用這些功能。 若要深入瞭解這些一般功能，請參閱參考連結。 

- [Microsoft Defender 進階威脅防護總覽](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [適用于 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [適用于非 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure 資訊安全中心監視** ：是

**責任** ：共用

## <a name="governance-and-strategy"></a>控管與策略

*如需詳細資訊，請參閱 [Azure 安全性基準測試：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理與資料保護策略 

**指導** 方針：確保您可以記錄並傳達明確的策略，以持續監視和保護系統和資料。 優先探索、評估、保護及監視商務關鍵資料和系統。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

- 以商務風險為依據的資料分類標準
- 安全性組織風險和資產清查的可見度 
- 安全性組織核准 Azure 服務以供使用 
- 資產在其生命週期內的安全性
- 根據組織資料分類所需的存取控制策略
- 使用 Azure 原生和協力廠商資料保護功能
- 傳輸中和待用使用案例的資料加密需求
- 適當的密碼編譯標準

請參閱參考連結所提供的其他資訊。

- [Azure 安全性架構建議-儲存體、資料和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全性基礎-Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md)

- [雲端採用架構-Azure 資料安全性和加密最佳作法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指導** 方針：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立企業級的策略來分割資產的存取權。 請仔細平衡安全性分隔的需求，以及針對需要彼此通訊並存取資料的系統，啟用每日作業的需求。

請確定跨控制項類型（包括網路安全性、身分識別和存取模型、應用程式許可權或存取模型，以及人力流程式控制件）一致地實行分割策略。

- [Azure 中的分割策略指引 (影片) ](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure (檔中的分割策略指引) ](/security/compass/governance#enterprise-segmentation-strategy)

- [利用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理原則

**指導** 方針：持續測量並減輕個別資產及其裝載環境的風險。 設定高價值資產的優先順序以及高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性基準測試-狀態與弱點管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：讓組織角色、職責和責任保持一致

**指導** 方針：確保您可以記錄安全性組織中角色和責任的明確策略，並對其進行溝通。 根據安全性決策的明確責任來排定工作的優先順序、在共同責任模型上教育所有人，以及教育技術小組來保護雲端。

- [Azure 安全性最佳作法 1-人員：教育小組雲端安全性旅程](https://aka.ms/AzSec1)

- [Azure 安全性最佳作法 2-人員：教育小組雲端安全性技術](https://aka.ms/AzSec2)

- [Azure 安全性最佳作法 3-處理：指派雲端安全性決策的責任](https://aka.ms/AzSec3)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指導** 方針：建立 Azure 網路安全性方法作為組織整體安全性存取控制策略的一部分。 此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

- 集中式網路管理和安全性責任
- 虛擬網路分割模型與企業分割策略一致
- 不同威脅和攻擊案例中的補救策略
- 網際網路邊緣與輸入和輸出策略
- 混合式雲端和內部部署互連能力策略
- 最新的網路安全性構件 (例如網狀圖、參考網路架構) 

請參閱參考連結所提供的其他資訊。

- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](https://aka.ms/AzSec11)

- [Azure 安全性基準測試-網路安全性](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [商業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊許可權存取策略

**指導** 方針：建立 Azure 身分識別和特殊許可權的存取方法，作為組織整體安全性存取控制策略的一部分。 此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

- 集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力
- 不同使用案例和條件中的強式驗證方法
- 保護高許可權的使用者
- 異常使用者活動監視和處理  
- 使用者身分識別和存取權檢查和協調流程

請參閱參考連結所提供的其他資訊。

- [Azure 安全性基準測試-身分識別管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 安全性基準測試-特殊許可權存取](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](https://aka.ms/AzSec11)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指導** 方針：建立記錄和威脅回應策略，以快速偵測和補救威脅，同時符合合規性需求。 排定為分析師提供高品質的警示和順暢的體驗，讓他們可以專注于威脅，而不是整合和手動步驟。 

此策略應該包含下列元素的記載指引、原則和標準： 

- 安全性作業 (SecOps) 組織的角色和責任
- 妥善定義的事件回應程式，與 NIST 或其他產業架構一致
- 記錄檔捕捉和保留，以支援威脅偵測、事件回應和合規性需求
- 使用 SIEM、原生 Azure 功能和其他來源（與您的客戶、供應商和感興趣的公用方相關的通訊和通知計畫）集中查看和相關的威脅資訊
- 使用 Azure 原生和協力廠商平臺進行事件處理，例如記錄和威脅偵測、取證和攻擊補救和根除
- 處理事件和事件後活動的流程，例如經驗教訓和辨識項保留

請參閱參考連結所提供的其他資訊。
- [Azure 安全性基準測試-記錄和威脅偵測](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全性基準測試-事件回應](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全性最佳作法 4-處理。更新雲端的事件回應程式](https://aka.ms/AzSec4)

- [Azure 採用架構、記錄和報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性基準測試 V2 總覽](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
