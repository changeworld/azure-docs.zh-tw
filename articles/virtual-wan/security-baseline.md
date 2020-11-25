---
title: 適用于虛擬 WAN 的 Azure 安全性基準
description: 虛擬 WAN 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7f75f764f378118f9a34c1eee467e78ac279e19c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029042"
---
# <a name="azure-security-baseline-for-virtual-wan"></a>適用于虛擬 WAN 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 MICROSOFT AZURE 虛擬 WAN。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于虛擬 WAN 的相關指引來分組。 已排除不適用虛擬 WAN 的 **控制項**。

若要瞭解虛擬 WAN 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的虛擬 wan 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：執行內部流量的安全性

**指導** 方針： MICROSOFT AZURE 虛擬 WAN 提供自訂的路由功能，並為您的 ExpressRoute 流量提供加密功能。 所有路由管理都是由虛擬中樞路由器提供，這也會啟用虛擬網路之間的傳輸連線能力。 使用虛擬 WAN 加密您的 ExpressRoute 流量，可透過 ExpressRoute 在內部部署網路與 Azure 虛擬網路之間提供加密的傳輸，而不需要經過公用網際網路或使用公用 IP 位址。 

- [ExpressRoute 流量加密](virtual-wan-about.md#encryption)

- [在虛擬 WAN 中使用 Private Link](howto-private-link.md)

- [自訂路由案例](scenario-any-to-any.md)

- [自訂路由表檔](how-to-virtual-hub-routing.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-2-connect-private-networks-together"></a>NS-2：將私人網路連接在一起 

**指導** 方針： Microsoft Azure ExpressRoute 提供對 AZURE 虛擬 WAN 的私人連線能力。 由於 ExpressRoute 連線不會經過公用網際網路，因此 ExpressRoute 提供比一般網際網路連線更可靠、速度更快且延遲更低的情況。 您也可以透過站對站 (S2S) VPN 或點對站 (P2S) VPN，使用虛擬私人網路連線到 Azure。

- [虛擬 WAN 中的 ExpressRoute](virtual-wan-expressroute-portal.md)

- [站對站 VPN 總覽](virtual-wan-site-to-site-portal.md)

- [點對站 VPN 總覽](virtual-wan-point-to-site-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保護應用程式和服務免于外部網路攻擊

**指導** 方針：虛擬 WAN 不會將任何端點公開給外部網路，這些網路需要使用傳統網路保護來保護這些端點。 您可以使用虛擬網路保護服務 (任何連線至虛擬中樞) 虛擬網路的虛擬網路，以自由保護輪輻虛擬網路中的資源。 

使用 Azure 防火牆來保護應用程式和服務免于來自網際網路和其他外部位置的潛在惡意流量。 

選擇 Azure 提供的 DDoS 保護來保護您的資產，以防止您的 Azure 虛擬網路遭受攻擊。 使用 Azure 資訊安全中心來偵測與網路相關資源相關的錯誤配置風險。

- [Azure 防火牆檔](/azure/firewall)

- [使用 Azure 入口網站管理 Azure DDoS 保護標準](/azure/virtual-network/manage-ddos-protection) 

- [Azure 資訊安全中心建議](../security-center/recommendations-reference.md#recs-network)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：虛擬 WAN 是 Microsoft 管理的服務。 它不提供原生入侵偵測或入侵防護功能。 不過，有透過 Azure 防火牆提供給虛擬 WAN 的安全性功能，以啟用統一的原則控制點。 您可以建立 Azure 防火牆原則，並將原則連結至虛擬 WAN 中樞，以允許現有的虛擬 WAN 中樞作為受保護的虛擬中樞，並部署所需的 Azure 防火牆資源。

- [在虛擬 WAN 中樞中設定 Azure 防火牆](howto-firewall.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：簡化網路安全性規則

**指導** 方針：利用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制，以簡化網路安全性規則。 建立安全性規則時，可以使用服務標記來取代特定的 IP 位址。 藉由在規則的 [來源] 或 [目的地] 欄位中指定服務標記名稱，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [瞭解和使用服務標記](../virtual-network/service-tags-overview.md)

- [瞭解及使用應用程式安全性群組](/azure/virtual-network/security-overview#application-security-groups)

- [Azure 防火牆檔](/azure/firewall/)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全的功能變數名稱服務 (DNS) 

**指導** 方針：使用 Azure 防火牆將安全的 DNS 功能提供給虛擬 WAN。 設定 Azure 防火牆作為 DNS proxy，此 proxy 會成為從用戶端虛擬機器到 DNS 伺服器的 DNS 要求媒介。 若為自訂 DNS 伺服器設定，請啟用 DNS proxy 以避免 DNS 解析不相符，並啟用網路規則中的完整功能變數名稱篩選。 

- [Azure 防火牆檔](/azure/firewall/)

- [Azure 防火牆 DNS 設定](/azure/firewall/dns-settings)

- [使用 Azure 防火牆作為具有 Private Link 的 DNS 轉寄站](https://github.com/adstuart/azure-privatelink-dns-azurefirewall)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-management"></a>身分識別管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)。*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別和驗證系統

**指導** 方針： Azure Active Directory (Azure AD) 是適用于 Azure 服務的預設身分識別和存取管理服務。 包括虛擬 WAN。 將 Azure AD 的標準化，以管理組織的身分識別與存取管理：

- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows) 、Azure Key Vault、平臺即服務 (PaaS) ，以及軟體即服務 (SaaS) 應用程式。
- 您組織的資源，例如 Azure 或公司網路資源上的應用程式

在貴組織的雲端安全性實務中，以高優先順序保護 Azure AD。 利用資訊安全中心的安全性分數功能來評定您的身分識別和安全性狀態，以評估您的設定與 Microsoft 最佳作法建議的緊密關係。 如有必要，請執行 Microsoft 的最佳作法建議，以改善安全性狀態。

Azure AD 也支援外部身分識別，讓沒有 Microsoft 帳戶的使用者可以使用其外部身分識別登入其應用程式和資源。 

請參閱參考連結中有關使用點對站 VPN 案例中 Azure AD 的資訊。

- [為 P2S OpenVPN 通訊協定連線建立 Azure Active Directory (AD) 租使用者](openvpn-azure-ad-tenant-multi-app.md)

- [設定使用者 VPN 的 Azure Active Directory authentication](virtual-wan-point-to-site-azure-ad.md)

- [Azure Active Directory 中的租用](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用強式驗證控制項

**指導** 方針：目前透過整合虛擬 WAN 點對站 VPN 來提供 Azure Active Directory (Azure AD) 驗證。

Azure Active Directory (Azure AD) 是 Azure 服務的預設身分識別和存取管理服務。 Azure AD 支援具有多重要素驗證和強式無密碼方法的強大驗證控制項。

Azure AD 針對強式驗證控制項建議下列各項：

- 多重要素驗證-啟用 Azure AD 多重要素驗證，並遵循安全性最佳作法 Azure 資訊安全中心中的身分識別和存取管理建議。 在所有情況下強制執行多重要素驗證，根據登入條件和風險因素來選擇使用者或依使用者等級

- 無密碼 authentication –有三個無密碼 authentication 選項可供使用。 這些包括、Windows Hello 企業版、Microsoft Authenticator 應用程式和內部部署驗證方法（例如智慧卡）

確保系統管理員和特殊許可權的使用者可以使用強式驗證方法的最高層級，接著將強式驗證原則推出給其他使用者。

- [如何在 P2S VPN 中為虛擬 WAN 啟用 MFA](openvpn-azure-ad-mfa.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針：啟用 Azure Active Directory (Azure AD) 多重要素驗證，讓 VPN 使用者使用 (驗證進行點對站) 。 設定每個使用者的多重要素驗證，或利用條件式存取來利用多重要素驗證。 條件式存取可讓您更精細地控制第二個因素的升級方式。 它可以只允許將多重要素驗證指派給 VPN，並排除系結至 Azure AD 租使用者的其他應用程式。 

請注意，Azure AD authentication 僅適用于使用 OpenVPN 通訊協定的閘道和執行 Windows 的用戶端。

- [何謂條件式存取](../active-directory/conditional-access/overview.md)

- [設定使用者 VPN 的 Azure Active Directory authentication](virtual-wan-point-to-site-azure-ad.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除非預期的認證暴露

**指導** 方針：虛擬 WAN 中的站對站 VPN 會使用預先共用金鑰 (PSK) ，客戶會在其 Azure Key Vault 中探索、建立及管理這些金鑰。 執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

在 GitHub 中，您可以使用原生密碼掃描功能來識別程式碼中的認證或其他形式的秘密。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html) 

- [GitHub 秘密掃描](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="privileged-access"></a>特殊許可權存取

*如需詳細資訊，請參閱 [Azure 安全性基準測試：](/azure/security/benchmarks/security-controls-v2-privileged-access)特殊許可權存取。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對商務關鍵性系統的系統管理存取

**指導** 方針： AZURE 虛擬 WAN 使用 azure 角色型存取控制 (azure RBAC) ，藉由限制哪些帳戶被授與存取權給他們所在的訂用帳戶和管理群組，來隔離對商務關鍵性系統的存取。

也限制存取管理、身分識別和安全性系統，這些系統具有您商務關鍵性存取權的系統管理存取權，例如 Active Directory 網網域控制站、安全性工具和系統管理工具，並將代理程式安裝在商務關鍵系統上。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理群組存取](../governance/management-groups/overview.md#management-group-access) 

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)。*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密傳輸中的機密資訊

**指導** 方針：使用點對站 Vpn、站對站 vpn 和具虛擬 WAN 的加密 Express 路由，來滿足您的連線需求。 VPN 加密可保護傳輸中的資料免于遭受「頻外」攻擊 (例如，流量捕捉) ，以確保攻擊者無法讀取或修改資料。 

- [點對站 VPN](virtual-wan-point-to-site-portal.md)

- [站對站 VPN](virtual-wan-site-to-site-portal.md)

- [加密的 ExpressRoute](vpn-over-expressroute.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

## <a name="asset-management"></a>資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>上午-1：確保安全性小組能看到資產的風險

**指導** 方針：確定安全性小組已獲得 Azure 租使用者和訂用帳戶中的安全性讀取者許可權，讓他們可以使用 Azure 資訊安全中心監視安全性風險。 

根據安全性小組責任的結構，監視安全性風險可能是中央安全性小組或本地小組的責任。 如此一來，就必須在組織內集中匯總資訊安全見解和風險。 

安全性讀取者許可權可以廣泛地套用到整個租使用者， (根管理群組) 或限定于管理群組或特定訂用帳戶。 

注意：若要瞭解工作負載和服務，可能需要其他許可權。 

- [安全性讀取者角色總覽](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組總覽](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>上午-2：確保安全性小組可以存取資產清查和中繼資料

**指導** 方針：將標記套用至您的 Azure 資源、資源群組和訂用帳戶，以邏輯方式將它們組織成分類法。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。 Azure 虛擬 WAN 也支援以您可匯出資產範本的 Azure Resource Manager 為基礎的資源部署。 

- [資源命名與標記決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [Azure 資訊安全中心資產庫存管理](../security-center/asset-inventory.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="am-3-use-only-approved-azure-services"></a>上午-3：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 監視器建立規則，以在偵測到未核准的服務時觸發警示。 虛擬 WAN 將許多網路、安全性和路由功能整合在一起，以提供單一操作介面。 虛擬 WAN VPN 閘道、ExpressRoute 閘道和 Azure 防火牆具有可透過 Azure 監視器取得的記錄和計量。 
 

- [虛擬 WAN 記錄和計量](logs-metrics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄和威脅偵測

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：啟用 Azure 資源的威脅偵測

**指導** 方針：使用虛擬 WAN 的點對站 VPN 與 Azure Active Directory (Azure AD) 整合。 Azure AD 提供下列使用者記錄檔，可在 Azure AD 報告中查看或與 Azure 監視器、Azure Sentinel、SIEM 或監視工具整合，以取得更精密的威脅監控和分析使用案例。 這些警告是：

- 登入-[登入] 報表會提供受控應用程式使用方式和使用者登入活動的相關資訊。
- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 Audit 記錄檔的範例包括對 Azure AD 中任何資源所做的變更，例如新增或移除使用者、應用程式、群組、角色和原則。
- 有風險的登入-具風險的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。
- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

使用 Azure 資訊安全中心來建立特定可疑活動的警示，例如在訂用帳戶中包含已淘汰帳戶的失敗驗證嘗試次數過多。 除了基本的安全性防護監視之外，您還可以使用資訊安全中心的威脅防護模組，從個別的 Azure 計算資源收集更深入的安全性警示 (虛擬機器、容器、app service) 、資料資源 (SQL DB 和儲存體) ，以及 Azure 服務層級。 這項功能可讓您在個別資源內看到帳戶異常。

- [Azure Active Directory 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [在虛擬 WAN 中樞中設定 Azure 防火牆](howto-firewall.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別與存取管理的威脅偵測

**指導** 方針：使用虛擬 WAN 的點對站 VPN 與 Azure Active Directory (Azure AD) 整合。 Azure AD 提供下列使用者記錄檔，可在 Azure AD 報告中查看或與 Azure 監視器、Azure Sentinel、SIEM 或監視工具整合，以取得更精密的威脅監控和分析使用案例。 這些警告是：

- 登入-[登入] 報表會提供受控應用程式使用方式和使用者登入活動的相關資訊。
- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 Audit 記錄檔的範例包括對 Azure AD 中任何資源所做的變更，例如新增或移除使用者、應用程式、群組、角色和原則。
- 有風險的登入-具風險的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。
- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

使用 Azure 資訊安全中心來建立特定可疑活動的警示，例如在訂用帳戶中包含已淘汰帳戶的失敗驗證嘗試次數過多。 除了基本的安全性防護監視之外，您還可以使用資訊安全中心的威脅防護模組，從個別的 Azure 計算資源收集更深入的安全性警示 (虛擬機器、容器、app service) 、資料資源 (SQL DB 和儲存體) ，以及 Azure 服務層級。 這項功能可讓您在個別資源內看到帳戶異常。

- [Azure Active Directory 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [在虛擬 WAN 中樞中設定 Azure 防火牆](howto-firewall.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

**指導** 方針：使用 Azure 監視器監視 AZURE 虛擬 WAN。 虛擬 WAN 將許多網路、安全性和路由功能整合在一起，以提供單一操作介面。 虛擬 WAN VPN 閘道、ExpressRoute 閘道和 Azure 防火牆具有可透過 Azure 監視器取得的記錄和計量。 預設會收集活動記錄專案，而且可以在 Azure 入口網站中查看。 您可以使用 Azure 活動記錄 (之前稱為「作業記錄」和「審核記錄」) 來查看提交至您 Azure 訂用帳戶的所有作業。

虛擬 WAN 也可使用各種診斷記錄，並可使用 Azure 入口網站為虛擬 WAN 資源設定。  您可以選擇傳送至 Log Analytics、串流至事件中樞，或只封存至儲存體帳戶。 
 

- [虛擬 WAN 記錄和計量](logs-metrics.md)

- [Azure 防火牆記錄和計量](/azure/firewall/logs-and-metrics)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄

**指導** 方針： azure 活動記錄（自動啟用）會包含 AZURE 虛擬 WAN 資源 (PUT、POST、DELETE) 的所有寫入作業，但讀取 (取得) 作業除外。 活動記錄可以用來在進行疑難排解時找出錯誤，或是監視組織中的使用者修改資源的方式。

啟用虛擬 WAN 的 Azure 資源記錄。 您可以使用 Azure 資訊安全中心和 Azure 原則來啟用資源記錄和記錄資料收集。 這些記錄檔可能很重要，可供稍後調查安全性事件和執行法庭訓練。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md) 

- [瞭解 Azure 資訊安全中心資料收集](../security-center/security-center-enable-data-collection.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中安全性記錄檔管理和分析

**指導** 方針：使用 Azure 監視器啟用虛擬 WAN 的安全性記錄。 虛擬 WAN 將許多網路、安全性和路由功能整合在一起，以提供單一操作介面。 虛擬 WAN VPN 閘道、ExpressRoute 閘道和 Azure 防火牆具有可透過 Azure 監視器取得的記錄和計量。 預設會收集活動記錄專案，而且可以在 Azure 入口網站中查看。 您可以使用 Azure 活動記錄 (之前稱為「作業記錄」和「審核記錄」) 來查看提交至您 Azure 訂用帳戶的所有作業。 

虛擬 WAN 也可使用各種診斷記錄，並可使用 Azure 入口網站為虛擬 WAN 資源設定。 傳送至 Log Analytics、串流至事件中樞，或只封存至儲存體帳戶。 此外，請啟用資料並將其上架到 Azure Sentinel 或協力廠商安全性資訊和事件管理解決方案。 
 

- [虛擬 WAN 記錄和計量](logs-metrics.md)

- [Azure 防火牆記錄和計量](/azure/firewall/logs-and-metrics)

Azure 虛擬 WAN 安全性是透過 Azure 防火牆提供。 

- [Azure 防火牆檔](/azure/firewall/overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：設定記錄儲存體保留期

**指導** 方針：根據合規性、法規和商務需求來設定記錄保留期。 在 Azure 監視器中，您可以根據組織的合規性法規來設定 Log Analytics 工作區保留期限。 使用 Azure 儲存體、Data Lake 或 Log Analytics 工作區帳戶來取得長期和封存儲存體。

- [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

- [Azure 資訊安全中心警示和建議匯出](../security-center/continuous-export.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)。*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備–更新 Azure 的事件回應程式

**指導** 方針：確定您的組織有處理常式來回應安全性事件、已更新 Azure 的這些處理常式，並會定期執行這些程式以確保其就緒。

- [跨企業環境執行安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備–安裝事件通知

**指導** 方針：在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料已被非法或未經授權的物件存取，Microsoft 會使用此連絡人資訊來與您聯繫。 您也可以選擇根據您的事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析–根據高品質警示建立事件

**指導** 方針：確定您有建立高品質警示和測量警示品質的流程。 這可讓您學習過去事件的課程，並設定分析師的警示優先順序，讓他們不會浪費時間誤報。 

高品質的警示可以根據過去事件的體驗、驗證的社區來源，以及設計來藉由融合和關聯各種信號來源來產生和清除警示的工具來建立。 

Azure 資訊安全中心在許多 Azure 資產之間提供高品質的警示。 您可以使用 ASC 資料連線器，將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立 advanced 警示規則，以自動產生調查的事件。 

使用「匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 以手動方式或持續持續的方式匯出警示和建議。

- [如何設定匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測和分析–調查事件

**指導** 方針：確定分析師可以在調查潛在事件時查詢及使用不同的資料來源，以全面瞭解發生的情況。 您應收集不同的記錄檔，以追蹤潛在攻擊者在整個終止鏈上的活動，以避免點。  您也應該確定已針對其他分析師和未來的歷程記錄參考來捕捉見解和學習。  

用於調查的資料來源包括已從範圍內服務和執行中的系統收集的集中式記錄來源，但也可以包括：

- 網路資料–使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器來捕捉網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能來建立執行中系統磁片的快照集。 

    - 使用作業系統的原生記憶體傾印功能來建立執行中系統記憶體的快照。

    - 您可以使用 Azure 服務的快照集功能，或您軟體本身的功能來建立執行中系統的快照集。

Azure Sentinel 在幾乎任何記錄來源和案例管理入口網站中提供廣泛的資料分析，以管理事件的完整生命週期。 調查期間的智慧資訊可以與事件產生關聯，以供追蹤和報告之用。 

- [建立 Windows 機器磁片的快照集](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [建立 Linux 機器磁片的快照集](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印集合](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測和分析-設定事件優先順序

**指導** 方針：提供內容給分析人員，根據警示嚴重性和資產敏感度先將焦點放在哪個事件上。 

Azure 資訊安全中心會將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性取決於資訊安全中心在尋找或用來發出警示的分析中的可信度，以及導致警示的活動背後有惡意意圖的信賴等級。

此外，請使用標記來標示資源，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：內含專案、根除和復原–將事件處理自動化

**指導** 方針：自動化手動重複的工作，以加快回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行、讓每個事件變慢，並減少分析師可以處理的事件數目。 手動工作也會增加分析師疲勞，這樣會增加造成延遲的人為錯誤的風險，並降低分析師在複雜工作上有效地專注的能力。 使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能，自動觸發動作或執行腳本，以回應傳入的安全性警示。 腳本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在安全中心設定工作流程自動化](../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心中設定自動化威脅回應](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="posture-and-vulnerability-management"></a>狀況和弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：狀態和弱點管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。*

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：進行定期攻擊模擬

**指導** 方針：如有需要，請在您的 Azure 資源上進行滲透測試或 red team 活動，並確保修復所有重要的安全性結果。
遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md)

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="endpoint-security"></a>端點安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：端點安全性](/azure/security/benchmarks/security-controls-v2-endpoint-security)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用端點偵測和回應 (EDR) 

**指導** 方針：不明確允許客戶設定端點偵測和回應設定。 不過，Azure 虛擬 WAN 供應專案中使用的虛擬機器會使用這些功能。 若要深入瞭解這些一般功能，請參閱參考連結。 

- [Microsoft Defender 進階威脅防護總覽](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [適用于 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [適用于非 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Azure 資訊安全中心監視**：是

**責任**：共用

## <a name="governance-and-strategy"></a>控管與策略

*如需詳細資訊，請參閱 [Azure 安全性基準測試：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理與資料保護策略 

**指導** 方針：確保您可以記錄並傳達明確的策略，以持續監視和保護系統和資料。 優先探索、評估、保護及監視商務關鍵資料和系統。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   以商務風險為依據的資料分類標準

-   安全性組織風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用 

-   資產在其生命週期內的安全性

-   根據組織資料分類所需的存取控制策略

-   使用 Azure 原生和協力廠商資料保護功能

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議-儲存體、資料和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全性基礎-Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md)

- [雲端採用架構-Azure 資料安全性和加密最佳作法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全性基準測試-資產管理](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure 安全性基準測試-資料保護](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指導** 方針：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立企業級的策略來分割資產的存取權。

請仔細平衡安全性分隔的需求，以及針對需要彼此通訊並存取資料的系統，啟用每日作業的需求。

請確定跨控制項類型（包括網路安全性、身分識別和存取模型、應用程式許可權/存取模型，以及人力流程式控制件）一致地實行分割策略。

- [Azure 中的分割策略指引 (影片) ](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure (檔中的分割策略指引) ](/security/compass/governance#enterprise-segmentation-strategy)

- [利用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理原則

**指導** 方針：持續測量並減輕個別資產及其託管環境的風險。 設定高價值資產的優先順序以及高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性基準測試-狀態與弱點管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：讓組織角色、職責和責任保持一致

**指導** 方針：確保您可以記錄安全性組織中角色和責任的明確策略，並對其進行溝通。 優先提供安全性決策的明確責任、教育所有人共同責任模型，以及教育技術小組來保護雲端。

- [Azure 安全性最佳作法 1-人員：教育小組雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全性最佳作法 2-人員：教育小組雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全性最佳作法 3-處理：指派雲端安全性決策的責任](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指導** 方針：建立 Azure 網路安全性方法作為組織整體安全性存取控制策略的一部分。  

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   集中式網路管理和安全性責任

-   虛擬網路分割模型與企業分割策略一致

-   不同威脅和攻擊案例中的補救策略

-   網際網路邊緣與輸入和輸出策略

-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性構件 (例如網狀圖、參考網路架構) 

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全性基準測試-網路安全性](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [商業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊許可權存取策略

**指導** 方針：建立 Azure 身分識別和特殊許可權的存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同使用案例和條件中的強式驗證方法

-   保護高許可權的使用者

-   異常使用者活動監視和處理  

-   使用者身分識別和存取權檢查和協調流程

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性基準測試-身分識別管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 安全性基準測試-特殊許可權存取](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 安全性最佳做法 11-架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指導** 方針：建立記錄和威脅回應策略，以快速偵測和補救威脅，同時符合合規性需求。 排定為分析師提供高品質的警示和順暢的體驗，讓他們可以專注于威脅，而不是整合和手動步驟。 

此策略應該包含已記載的指導方針、原則，以及下列元素的標準： 

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義的事件回應程式，與 NIST 或其他產業架構一致 

-   記錄檔捕捉和保留，以支援威脅偵測、事件回應和合規性需求

-   使用 SIEM、原生 Azure 功能和其他來源，集中查看威脅的相關資訊以及相關資訊 

-   與您的客戶、供應商和感興趣的公開方相關的通訊和通知計畫

-   使用 Azure 原生和協力廠商平臺進行事件處理，例如記錄和威脅偵測、取證和攻擊補救和根除

-   處理事件和事件後活動的流程，例如經驗教訓和辨識項保留

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性基準測試-記錄和威脅偵測](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全性基準測試-事件回應](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全性最佳作法 4-處理。更新雲端的事件回應程式](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 採用架構、記錄和報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理及監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性基準測試 V2 總覽](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
