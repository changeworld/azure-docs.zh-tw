---
title: 適用于 Windows 虛擬桌面的 Azure 安全性基準
description: Windows 虛擬桌面安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e987f96402f4c922bdaca8ecf32348bc99c34199
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798397"
---
# <a name="azure-security-baseline-for-windows-virtual-desktop"></a>適用于 Windows 虛擬桌面的 Azure 安全性基準

此安全性基準會將來自 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 Windows 虛擬桌面。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Windows 虛擬桌面的相關指引來分組。 尚未排除適用于 Windows 虛擬桌面的 **控制項**。

Windows 虛擬桌面服務包括服務本身、多會話虛擬 sku 的 Windows 10 企業版，以及 FSLogix。 如需 FSLogix 相關的安全性建議，請參閱 [存放裝置的安全性基準](../storage/common/security-baseline.md)。 此服務有一個在虛擬機器上執行的代理程式，但由於虛擬機器已完全掌控客戶，請遵循 [計算的安全性建議](../virtual-machines/windows/security-baseline.md)

若要查看 Windows 虛擬桌面如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Windows 虛擬桌面安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱 [Azure 安全性效能評定：網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：執行內部流量的安全性

**指導** 方針：當您部署要註冊至 Windows 虛擬桌面的虛擬機器時，您必須建立或使用現有的虛擬網路。 確定所有 Azure 虛擬網路都遵循符合業務風險的企業分割原則。 任何可能會對組織產生更高風險的系統，都應該隔離在其自己的虛擬網路內，並使用網路安全性群組或 Azure 防火牆來充分保護。

使用 Azure 資訊安全中心中的適應性網路強化功能來建議網路安全性群組設定，這些設定會限制埠和來源 Ip 的外部網路流量規則。

根據您的應用程式和企業分割策略，根據網路安全性群組規則來限制或允許內部資源之間的流量。 針對特定定義完善的應用程式 (例如) 的三層式應用程式，這可能是高度安全的「預設拒絕，允許例外狀況」的方法。 如果您有許多應用程式和端點彼此互動，則這可能無法妥善調整。 您也可以在需要集中管理的情況下，在中樞/輪輻拓撲的大量企業區段或輪輻 (上使用 Azure 防火牆) 

針對與虛擬機器相關聯的網路安全性群組 (屬於 Windows 虛擬桌面) 子網的一部分，您必須允許特定端點的連出流量。 

- [找出需要允許哪些 Url 存取 Windows 虛擬桌面](safe-url-list.md)

- [Azure 資訊安全中心中的適應性網路強化](../security-center/security-center-adaptive-network-hardening.md) 

- [適用于 Windows 虛擬桌面的 Azure 防火牆 ](../firewall/protect-windows-virtual-desktop.md)

- [如何建立具有安全性規則的網路安全性群組](../virtual-network/tutorial-filter-network-traffic.md)

 

- [如何部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-2-connect-private-networks-together"></a>NS-2：將私人網路連接在一起

**指導** 方針：使用 azure ExpressRoute 或 azure 虛擬私人網路，在共置環境中的 azure 資料中心與內部部署基礎結構之間建立私人連線。 ExpressRoute 連線不會經過公用網際網路，提供比一般網際網路連線更可靠、速度更快且延遲更低的延遲。 

針對點對站和站對站虛擬私人網路，您可以使用虛擬私人網路選項和 Azure ExpressRoute 的任意組合，將內部部署裝置或網路連線到虛擬網路。

使用虛擬網路對等互連，在 Azure 中將兩個或多個虛擬網路連接在一起。 對等互連虛擬網路之間的網路流量是私人的，而且會保留在 Azure 骨幹網路上。

- [什麼是 ExpressRoute 連線模型](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 總覽](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [虛擬網路對等互連](/azure/virtual-network/virtual-network-peering-overvie)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保護應用程式和服務免于外部網路攻擊

**指導** 方針：使用 Azure 防火牆來保護應用程式和服務免于來自網際網路和其他外部位置的潛在惡意流量。 保護您的 Windows 虛擬桌面資源免于來自外部網路的攻擊，包括分散式阻絕服務攻擊、應用程式特定攻擊、未經要求和潛在的惡意網際網路流量。 藉由在您的 Azure 虛擬網路上啟用 DDoS 標準保護，來保護您的資產免于分散式阻斷服務的攻擊。 使用 Azure 資訊安全中心來偵測與網路相關資源相關的錯誤設定風險。

Windows 虛擬桌面並非用來執行 web 應用程式，而且您不需要設定任何其他設定，也不需要部署任何額外的網路服務，就能防止以 web 應用程式為目標的外部網路攻擊。

- [Azure 防火牆檔](/azure/firewall)

- [使用 Azure 入口網站管理 Azure DDoS 保護標準](/azure/virtual-network/manage-ddos-protection) 

- [Azure 資訊安全中心建議](../security-center/recommendations-reference.md#networking-recommendations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：使用 Azure 防火牆搭配以威脅情報為基礎的篩選來發出警示，並選擇性地封鎖進出已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 當需要承載檢查時，您可以從 Azure Marketplace 部署協力廠商入侵偵測或預防解決方案。 

如果您有關于入侵偵測或預防解決方案使用的法規或其他需求，請務必調整它，以提供高品質的警示給您的安全性資訊和事件管理， (SIEM) 解決方案。

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace 包含協力廠商識別碼功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR 功能](/windows/security/threat-protection/microsoft-defender-atp/overviewendpoint-detection-response)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：簡化網路安全性規則

**指導** 方針：使用 Azure 虛擬網路服務標籤來定義網路安全性群組的網路存取控制，或針對您的 Windows 虛擬桌面資源設定的 Azure 防火牆。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如： WindowsVirtualDesktop) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [瞭解和使用服務標記](../virtual-network/service-tags-overview.md)

- [在這裡深入瞭解 Windows 虛擬桌面服務標記所涵蓋的內容 ](safe-url-list.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-management"></a>身分識別管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別與驗證系統

**指導** 方針： Windows 虛擬桌面使用 Azure Active Directory (Azure AD) 作為預設的身分識別和存取管理服務。 您應該將 Azure AD 標準化，以管理組織的身分識別和存取管理：

- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 應用程式。

- 您的組織資源，例如 Azure 上的應用程式或您的公司網路資源。

保護 Azure AD 在組織的雲端安全性實務中，應具有較高的優先順序。 Azure AD 提供身分識別安全分數，協助您評定相對於 Microsoft 最佳做法建議的身分識別安全性狀態。 請使用此分數來測量設定符合最佳做法建議的程度，並改善您的安全性狀態。

Azure AD 支援外部身分識別，讓沒有 Microsoft 帳戶的使用者可以使用其外部身分識別登入其應用程式和資源。

- [Azure AD 中的租用](../active-directory/develop/single-and-multi-tenant-apps.md)

- [使用應用程式的外部識別提供者](/azure/active-directory/b2b/identity-providers) (機器翻譯)

- [什麼是 Azure AD 中的身分識別安全分數](../active-directory/fundamentals/identity-secure-score.md)

- [操作 Windows 虛擬桌面所需的特定角色 ](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自動地管理應用程式識別碼

**指導** 方針： Windows 虛擬桌面針對非人力帳戶（例如服務或自動化）支援 Azure 受控識別。 建議使用 Azure 受控識別功能，而不是建立功能更強大的人帳戶來存取或執行您的資源。 

Windows 虛擬桌面建議使用 Azure Active Directory (Azure AD) 在資源層級建立具有限制許可權的服務主體，以設定具有憑證認證的服務主體，並切換回用戶端密碼。 在這兩種情況下，Azure Key Vault 可以搭配使用 Azure 受控識別，讓執行時間環境 (例如，Azure 函式) 可以從金鑰保存庫取得認證。

- [支援適用於 Azure 資源的受控識別服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Azure 服務主體](/powershell/azure/create-azure-service-principal-azureps) 

- [建立具有憑證的服務主體](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [使用 Azure Key Vault 來註冊安全性主體](../key-vault/general/authentication.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 單一登入 (SSO) 進行應用程式存取

**指導** 方針： Windows 虛擬桌面使用 Azure Active Directory (Azure AD) ，提供對 Azure 資源、雲端應用程式和內部部署應用程式的身分識別和存取管理。 這包括企業身分識別 (例如員工)，以及外部身分識別 (例如合作夥伴、廠商和供應商)。 這可讓單一登入 (SSO) 管理及保護內部部署和雲端中的組織資料與資源存取。 將您所有的使用者、應用程式和裝置連接到 Azure AD，以獲得更高的可見度和控制，以提供順暢的安全存取。

- [了解 Azure AD 的應用程式 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用增強式驗證控制項 

**指導** 方針： Windows 虛擬桌面使用 Azure Active Directory (Azure AD) ，可透過多重要素驗證和強式無密碼方法來支援強式驗證控制項。

- 多重要素驗證-啟用 Azure AD 多重要素驗證，並遵循來自 Azure 資訊安全中心的身分識別和存取管理建議，以取得多重要素驗證設定中的一些最佳作法。 您可以根據登入條件和風險因素，對所有、選取的使用者或依使用者的層級強制執行多重要素驗證。

- 無密碼驗證 – 有三種無密碼驗證選項可供使用：Windows Hello 企業版、Microsoft Authenticator 應用程式和內部部署驗證方法 (例如智慧卡)。

Windows 虛擬桌面支援舊版以密碼為基礎的驗證，例如僅限雲端的帳戶 (直接在 Azure) 中建立的使用者帳戶，其具有基準密碼原則或混合式帳戶 (來自內部部署 Azure AD 且遵循內部部署密碼原則) 的使用者帳戶。 使用密碼驗證時，Azure AD 提供密碼保護功能，可防止使用者設定容易猜測的密碼。 Microsoft 提供以遙測為基礎的禁用密碼全域清單，客戶可以根據其需求增強清單 (例如商標、文化參考等等) 。 此密碼保護可用於僅限雲端和混合式帳戶。

以密碼認證為基礎的驗證，很容易受到常見的攻擊方法所影響。 為了提高安全性，請使用強式驗證，例如多重要素驗證和強式密碼原則。 針對可能具有預設密碼的協力廠商應用程式和 marketplace 服務，您應該在服務初始設定時變更它們。

針對系統管理員和特殊許可權的使用者，請確定已使用最高層級的增強式驗證方法，然後向其他使用者推出適當的增強式驗證原則。

- [Azure Active Directory 的無密碼驗證選項簡介](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Azure AD 預設密碼原則](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [使用 Azure Active Directory 密碼保護來消除錯誤的密碼](../active-directory/authentication/concept-password-ban-bad.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：監視及警示帳戶異常

**指導** 方針： Windows 虛擬桌面與可提供下列資料來源的 Azure Active Directory (Azure AD) 整合：

- 登入報告可提供受控應用程式使用方式和使用者登入活動的相關資訊。

- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

- 具風險的登入-具風險的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。

- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

這些資料來源可以與 Azure 監視器、Azure Sentinel 或協力廠商安全性資訊和事件管理 (SIEM) 系統整合。 Azure 資訊安全中心也可對特定的可疑活動發出警示，例如驗證嘗試失敗次數過多，訂用帳戶中已淘汰的帳戶。 Azure 進階威脅防護 (ATP) 是一項安全性解決方案，可使用 Active Directory 訊號來識別、偵測及調查進階威脅、遭入侵的身分識別，以及惡意的內部人員動作。

- [Azure AD 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Azure 資訊安全中心的威脅情報保護模組中的警示](../security-center/alerts-reference.md)

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針： Windows 虛擬桌面支援根據使用者定義的條件，使用 Azure Active Directory (Azure AD) 進行細微存取控制的條件式存取。 例如，可能需要來自特定 IP 範圍的使用者登入，才能使用多重要素驗證來進行存取。 

此外，細微的驗證會話管理原則也可以用於不同的使用案例。

- [Azure 條件式存取概觀](../active-directory/conditional-access/overview.md) 

- [一般條件式存取原則](../active-directory/conditional-access/concept-conditional-access-policy-common.md) (機器翻譯) 

- [使用條件式存取來設定驗證工作階段管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) (機器翻譯) 

- [您可以在這裡找到 Windows 虛擬桌面特定的條件式存取設定資訊](set-up-mfa.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="privileged-access"></a>特殊權限存取

如需詳細資訊，請參閱 [Azure 安全性效能評定：特殊權限存取](/azure/security/benchmarks/security-controls-v2-privileged-access)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對業務關鍵系統的系統管理存取

**指導** 方針： Windows 虛擬桌面使用 azure 角色型存取控制 (azure RBAC) 來隔離對商務關鍵性系統的存取。 確定您也會限制對管理、身分識別和安全性系統的存取權，這些系統具有您商務關鍵性存取權的系統管理存取權，例如 Active Directory 網網域控制站、安全性工具和系統管理工具，以及安裝在商務關鍵性系統上的代理程式。 入侵這些管理和安全性系統的攻擊者，可能會立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理群組存取](../governance/management-groups/overview.md#management-group-access) 

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [管理 Windows 虛擬桌面所需的最低系統管理員許可權](faq.md#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期檢閱及協調使用者存取

**指導** 方針： Windows 虛擬桌面使用 Azure Active Directory (Azure AD) 帳戶來管理其資源、定期審核使用者帳戶和存取指派，以確保帳戶及其存取權都有效。

使用 Azure AD 存取評論來檢查群組成員資格、企業應用程式的存取權，以及角色指派。 Azure AD 報告可以提供記錄以協助探索過時的帳戶。

此外，Azure Privileged Identity Management 也可以設定為在建立過多的系統管理員帳戶時發出警示，以及識別已過時或設定不正確的系統管理員帳戶。

某些 Azure 服務支援無法透過 Azure AD 管理的本機使用者和角色。 您將需要分開管理這些使用者。

- [適用于 Windows 虛擬桌面的內建角色](rbac.md)

- [在 Privileged Identity Management (PIM) 中建立 Azure 資源角色的存取權審核 ](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中設定緊急存取

**指導** 方針： Windows 虛擬桌面使用 Azure Active Directory (Azure AD) 來管理其資源。 若要避免不小心遭到 Azure AD 的組織封鎖，請在無法使用一般系統管理帳戶時，設定緊急存取帳戶以進行存取。 緊急存取帳戶通常具有高權限，不應將其指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的緊急或「急用」狀況。

您應該確保緊急存取帳戶的認證 (例如密碼、憑證或智慧卡) 受到保護，而且只有在緊急情況下有權使用這些認證的個人才會知道。

- [在 Azure AD 中管理緊急存取帳戶](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-5-automate-entitlement-management"></a>PA-5：自動化權利管理 

**指導** 方針： Windows 虛擬桌面與 Azure Active Directory (Azure AD) 整合，以管理其資源。 使用 Azure AD 權利管理功能來自動化存取要求工作流程，包括存取指派、評論和到期時間。 另外，也支援雙重或多階段的核准。

- [什麼是 Azure AD 存取權評論](../active-directory/governance/access-reviews-overview.md) 

- [什麼是 Azure AD 權利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊權限存取工作站

**指導** 方針：安全且隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 使用高度安全的使用者工作站及/或 Azure 防禦來進行系統管理工作。 

使用 Azure Active Directory (Azure AD) 、Microsoft Defender 進階威脅防護 (ATP) 或 Microsoft Intune 部署安全且受管理的使用者工作站以進行系統管理工作。 受保護的工作站可以集中管理以強制執行安全的設定，包括強式驗證、軟體和硬體基準、受限的邏輯和網路存取。

- [瞭解特殊許可權的存取工作站](/azure/active-directory/devices/concept-azure-managed-workstation) 

- [部署特殊權限存取工作站](/azure/active-directory/devices/howto-azure-managed-workstation) (機器翻譯)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循恰到好處的系統管理 (最低權限原則) 

**指導** 方針： Windows 虛擬桌面與 azure 角色型存取控制 (azure RBAC) 的整合，以管理其資源。 Azure RBAC 可讓您透過角色指派來管理 Azure 資源存取。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站之類的工具進行清查或查詢。 

使用 Azure RBAC 指派給資源的許可權，應一律限制為角色所需的許可權。 這可補充 (JIT) Privileged Identity Management (PIM) 的方法，Azure Active Directory (Azure AD) ，而且應該定期檢查。

此外，您也可以使用內建角色來配置許可權，並只在必要時才建立自訂角色。

- [什麼是 Azure 角色型存取控制 (Azure RBAC) ](../role-based-access-control/overview.md) 

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

- [適用于 Windows 虛擬桌面的內建角色](rbac.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：選擇 Microsoft 支援的核准流程  

**指導** 方針：在 Microsoft 需要存取客戶資料的支援案例中，Windows 虛擬桌面支援客戶加密箱，以提供可供您檢查和核准或拒絕客戶資料存取要求的介面。

- [瞭解客戶加密箱](../security/fundamentals/customer-lockbox-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱 [Azure 安全性效能評定：資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)。

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1：探索、分類及標記敏感性資料

**指導** 方針：探索、分類和標示您的機密資料，讓您可以設計適當的控制項。 這是為了確保組織的技術系統會安全地儲存、處理及傳輸機密資訊。

針對 Azure、內部部署、Office 365 及其他位置上的 Office 檔中的機密資訊，使用 Azure 資訊保護 (及其相關的掃描工具) 。

您可以利用 Azure SQL 資訊保護來分類和標示儲存在 Azure SQL 資料庫中的資訊。

- [使用 Azure 資訊保護標記敏感資訊](/azure/information-protection/what-is-information-protection) 

- [如何實作 Azure SQL 資料探索](/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保護敏感性資料

**指導** 方針：使用 azure 角色型存取控制來限制存取權，以使用 azure 角色型存取控制來保護敏感性資料 (azure RBAC) 、網路型存取控制和 azure 服務中的特定控制項 (例如 SQL 中的加密和其他資料庫) 。

為確保存取控制的一致性，所有類型的存取控制都應與您的企業分割策略相符。 企業分割策略也應傳達至敏感性或業務關鍵資料和系統的所在位置。

Microsoft 會將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作某些預設的資料保護控制項和功能。

- [Azure 角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：監視未經授權的敏感資料傳輸

**指引**：監視是否有未經授權者將資料傳輸至企業可見度和控制範圍外的位置。 這通常牽涉到監視是否有異常活動 (大規模或不尋常的傳輸) 可能意味著未經授權的資料外洩。

先進的威脅防護 (ATP) 功能，Azure 儲存體和 Azure SQL ATP 都能在異常的資訊傳輸時發出警示，指出有哪些可能是未經授權的機密資訊傳輸。

Azure 資訊保護 (AIP) 提供對已分類和標示的資訊進行監視的功能。

使用資料遺失防護解決方案（例如主機型）來強制執行偵探和/或預防措施，以防止資料遭到外泄。

- [啟用 Azure SQL ATP](../azure-sql/database/threat-detection-overview.md) 

- [啟用 Azure 儲存體 ATP](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-security-center)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="asset-management"></a>資產管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：確保安全性小組能夠看到資產的風險

**指引**：確保安全性小組會獲得 Azure 租用戶和訂用帳戶中的「安全性讀取者」權限，而能夠使用 Azure 資訊安全中心來監視安全性風險。 

根據安全性小組責任的結構，監視安全性風險可能是中央安全性小組或本地小組的責任。 然而，安全性見解和風險務必要在組織內部集中彙總。 

「安全性讀取者」權限可以廣泛套用至整個租用戶 (根管理群組)，或將範圍限定於管理群組或特定訂用帳戶。 

可能需要其他許可權才能看到工作負載和服務。 

- [安全性讀取者角色概觀](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組概觀](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：確保安全性小組可以存取資產清查和中繼資料

**指導** 方針：將標記套用至您的 Azure 資源、資源群組和訂用帳戶，以邏輯方式將它們組織成分類法。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。

使用 Azure 虛擬機器清查，將虛擬機器上有關軟體的資訊收集自動化。 您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得安裝日期和其他資訊的存取權，請啟用來賓層級的診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md) 

- [Azure 資訊安全中心資產庫存管理](../security-center/asset-inventory.md) 

- [資源命名與標記決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

- [如何啟用 Azure 虛擬機器清查](../automation/automation-tutorial-installed-software.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來審核和限制使用者可以在您的環境中布建的服務。 使用 Azure Resource Graph 則可查詢及探索其訂用帳戶內的資源。 您也可以使用 Azure 監視器來建立規則，以在偵測到未核准的服務時觸發警示。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/built-in-policies.md#general) 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：確保資產生命週期管理的安全性

**指引**：不適用。 Windows 虛擬桌面無法用於確保生命週期管理程式中資產的安全性。 客戶必須負責維護資產的屬性和網路設定，這些資產會被視為高衝擊。 

建議客戶建立程式來捕獲屬性和網路設定變更、測量變更影響，以及建立補救工作（適用的話）。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>上午-6：僅在計算資源中使用已核准的應用程式

**指導** 方針：使用 Azure 虛擬機器清查來自動收集虛擬機器上所有軟體的相關資訊。 您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得安裝日期和其他資訊的存取權，請啟用來賓層級的診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

- [如何啟用 Azure 虛擬機器清查](../automation/automation-tutorial-installed-software.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄與威脅偵測

如需詳細資訊，請參閱 [Azure 安全性效能評定：記錄與威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-protection)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：啟用 Azure 資源的威脅偵測

**指導** 方針：使用 Azure 資訊安全中心內建的威脅偵測功能，並為您的 Windows 虛擬桌面資源啟用 Azure Defender (正式 Azure 進階威脅防護) 。 適用于 Windows 虛擬桌面的 Azure Defender 提供額外一層的安全性情報，可偵測不尋常且可能有害的存取或惡意探索您的 Windows 虛擬桌面資源的嘗試。

將任何記錄從 Windows 虛擬桌面轉送到安全性資訊事件管理， (SIEM) 解決方案，可用來設定自訂威脅偵測。 確定您正在監視不同類型的 Azure 資產，以找出潛在的威脅和異常。 專注于取得高品質的警示，以減少因分析師排序的誤報。 警示可能源自于記錄資料、代理程式或其他資料。

- [Azure 資訊安全中心內的威脅防護](/azure/security-center/threat-protection) 

- [Azure 資訊安全中心安全性警示參考指南](../security-center/alerts-reference.md)

- [建立自訂分析規則來偵測威脅](../sentinel/tutorial-detect-threats-custom.md) 

- [使用 Azure Sentinel 的網路威脅情報](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別與存取權管理的威脅偵測

**指導** 方針： Azure Active Directory (Azure AD) 會提供下列使用者記錄，可在 Azure AD 報告中查看或與 Azure 監視器、Azure Sentinel 或其他安全性資訊和事件管理 (SIEM) 或監視工具整合，以取得更精密的監視和分析使用案例：

- 登入-登入報告提供受控應用程式使用方式和使用者登入活動的相關資訊。

- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

- 有風險的登入-具風險的登入是指登入嘗試的指標，該嘗試可能是使用者帳戶的合法擁有者所執行。

- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

Azure 資訊安全中心也可能會對某些可疑活動發出警示，例如在訂用帳戶中有過多的失敗驗證嘗試次數和已淘汰的帳戶。 除了基本的安全性防護監視之外，Azure 資訊安全中心中的威脅防護模組也可以從個別的 Azure 計算資源收集更深入的安全性警示 (虛擬機器、容器、app service) 、資料資源 (SQL DB 和儲存體) ，以及 Azure 服務層級。 這項功能可讓您在個別資源內看到帳戶異常。

- [Azure Active Directory 中的稽核活動報表](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure 資訊安全中心內的威脅防護](/azure/security-center/threat-protection)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

**指導** 方針： Windows 虛擬桌面不會產生或處理功能變數名稱服務 (DNS) 查詢記錄。 但是，註冊至服務的資源可以產生流量記錄。

啟用和收集網路安全性群組資源和流量記錄、Azure 防火牆記錄和 Web 應用程式防火牆 (WAF) 記錄以進行安全性分析，以支援事件調查、威脅搜尋和安全性警示產生。 您可以將流量記錄傳送至 Azure 監視器 Log Analytics 工作區，然後使用流量分析來提供見解。

- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Azure 防火牆記錄和計量](/azure/firewall/logs-and-metrics) 

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md) 

- [Azure 監視器中的 Azure 網路監視解決方案](../azure-monitor/insights/azure-networking-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄功能

**指導** 方針：自動啟用的活動記錄會包含 Windows 虛擬桌面資源 (PUT、POST、DELETE) 的所有寫入作業， (取得) 。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：將安全性記錄的管理與分析集中

**指導** 方針：集中記錄儲存體和分析以啟用相互關聯。 針對每個記錄來源，請確定您已指派資料擁有者、存取指引、儲存位置、用來處理和存取資料的工具，以及資料保留需求。

確定您要將 Azure 活動記錄整合到您的集中記錄。 透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

此外，請啟用 Azure Sentinel 或協力廠商安全性資訊事件管理的資料，並將其上架 (SIEM) 。 許多組織選擇使用 Azure Sentinel 「經常性存取」資料，而這些資料經常使用，並 Azure 儲存體使用較不常使用的「冷」資料。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱 [Azure 安全性效能評定：事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備 – 更新 Azure 的事件回應流程

**指引**：確定您的組織具有回應安全性事件的流程、已更新 Azure 的這些處理序，而且會定期執行以確保就緒。

- [在企業環境中實作安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf) (英文)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備 – 設定事件通知

**指引**：在 Azure 資訊安全中心中設定安全性事件連絡人資訊。 當 Microsoft 安全回應中心 (MSRC) 發現您的資料已遭非法或未經授權的合作對象存取時，Microsoft 會使用此連絡人資訊來與您連絡。 您也可以選擇根據事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測與分析 – 根據高品質警示建立事件

**指引**：確定您具有建立高品質警示並測量警示品質的流程。 這可讓您從過去的事件吸取教訓，並為分析師設定警示優先順序，這樣他們就不會浪費時間在誤報上。 

您可以根據過去事件的經驗、已驗證的社區來源，以及設計成透過融合和相互關聯不同信號來源以產生和清除警示的工具，來建置高品質警示。 

Azure 資訊安全中心在許多 Azure 資產之間提供高品質的警示。 您可以使用 ASC 資料連接器將警示串流至 Azure Sentinel。 Azure Sentinel 可讓您建立進階警示規則，以自動產生事件供調查之用。 

使用匯出功能匯出 Azure 資訊安全中心警示和建議，以利找出 Azure 資源的風險。 以手動或持續不斷的方式匯出警示和建議。

- [如何設定匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4：偵測和分析 – 調查事件

**指引**：確定分析師在調查潛在的事件時可查詢和使用各種資料來源，以全面觀照發生的狀況。 您應收集各種記錄，以追蹤整個攻擊鏈中潛在攻擊者的活動，而避免產生盲點。  您也應確實獲取深入解析和知識，以供其他分析師和未來的歷程記錄參考使用。  

調查的資料來源包括從範圍內的服務和執行中的系統收集到的集中式記錄來源，但也可包括：

- 網路資料 – 使用網路安全性群組的流量記錄、Azure 網路監看員和 Azure 監視器，擷取網路流量記錄和其他分析資訊。 

- 執行中系統的快照集： 

    - 使用 Azure 虛擬機器的快照集功能，建立執行中系統磁碟的快照集。 

    - 使用作業系統的原生記憶體傾印功能，建立執行中系統記憶體的快照集。

    - 使用 Azure 服務的快照集功能或軟體本身的功能，建立執行中系統的快照集。

Azure Sentinel 可讓您對絕大多數的記錄來源進行廣泛的資料分析，並提供案例管理入口網站來管理事件的完整生命週期。 調查期間的情報資訊可以與事件相關聯，以供追蹤和報告之用。 

- [為 Windows 機器的磁碟建立快照集](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [為 Linux 機器的磁碟建立快照集](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure 支援診斷資訊和記憶體傾印收集](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [使用 Azure Sentinel 調查事件](../sentinel/tutorial-investigate-cases.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測與分析 – 設定事件的優先順序

**指引**：根據警示嚴重性和資產敏感度，為分析師提供應優先關注哪些事件的內容。 

Azure 資訊安全中心會指派每個警示的嚴重性，以協助您設定應優先調查哪些警示。 嚴重性是以「資訊安全中心」在「尋找」或用來發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

此外，請使用標籤來標示資源，並建立命名系統以識別及分類 Azure 資源，尤其是處理敏感資料的資源。 您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：圍堵、根除及復原 – 將事件處理自動化

**指引**：將手動重複的工作自動化，以縮短回應時間並降低分析師的負擔。 手動工作需要較長的時間來執行，進而降低每個事件的速度，以及減少分析師可以處理的事件數目。 手動工作也會使分析師更加疲勞，而增加人為錯誤導致延遲的風險，並降低分析師有效專注處理複雜工作的能力。 請使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能來自動觸發動作，或執行劇本以回應傳入的安全性警示。 劇本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在資訊安全中心設定工作流程自動化](../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心設定自動化威脅回應](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="posture-and-vulnerability-management"></a>狀況和弱點管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：狀況和弱點管理](/azure/security/benchmarks/security-controls-v2-vulnerability-management)。

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：建立計算資源的安全設定

**指導** 方針：使用 Azure 資訊安全中心和 Azure 原則來建立所有計算資源的安全設定，包括 vm、容器及其他資源。

您可以使用自訂作業系統映射或 Azure 自動化狀態設定，來建立您組織所需作業系統的安全性設定。

- [如何監視 Azure 資訊安全中心建議](../security-center/security-center-recommendations.md) 

- [Azure 自動化狀態設定總覽](../automation/automation-dsc-overview.md) 

- [Windows 虛擬桌面環境](environment-setup.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4：維持計算資源的安全設定

**指導** 方針：使用 Azure 資訊安全中心和 Azure 原則，定期評估及補救 Azure 計算資源的設定風險，包括虛擬機器、容器及其他資源。 此外，您可以使用 Azure Resource Manager 範本、自訂作業系統映射或 Azure 自動化狀態設定來維護您組織所需作業系統的安全性設定。 與 Azure 自動化狀態設定結合的 Microsoft 虛擬機器範本，可能有助於符合和維護安全性需求。

Microsoft 發佈的 Azure Marketplace 虛擬機器映射是由 Microsoft 所管理和維護。

Azure 資訊安全中心也可以掃描容器映射中的弱點，並針對中心網際網路安全性的 Docker 基準，在容器中執行 Docker 設定的持續監視。 您可以使用 [Azure 資訊安全中心建議] 頁面來查看建議和補救問題。

- [如何實行 Azure 資訊安全中心弱點評定建議](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [如何從 ARM 範本建立 Azure 虛擬機器](../virtual-machines/windows/ps-template.md) 

- [Azure 自動化狀態設定總覽](../automation/automation-dsc-overview.md) 

- [資訊安全中心的容器安全性](../security-center/container-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5：安全地儲存自訂作業系統和容器映射

**指導** 方針： Windows 虛擬桌面可讓客戶管理作業系統映射。 使用 Azure 角色型存取控制 (Azure RBAC) ，以確保只有獲得授權的使用者可以存取您的自訂映射。 您可以使用 Azure 共用映射庫，將映射分享給組織內的不同使用者、服務主體或 Active Directory 群組。 在 Azure Container Registry 中儲存容器映射，並使用 RBAC 來確保只有授權的使用者可以存取。

- [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [如何設定 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [共用映射庫總覽](/azure/virtual-machines/windows/shared-image-galleries)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6：執行軟體弱點評定

**指導** 方針： Windows 虛擬桌面可讓您部署自己的虛擬機器，並將其註冊至服務，並在環境中執行 SQL database。

Windows 虛擬桌面可以使用協力廠商解決方案來執行網路裝置和 web 應用程式的弱點評定。 進行遠端掃描時，請勿使用單一、永久的系統管理帳戶。 請考慮針對掃描帳戶執行 JIT 布建方法。 掃描帳戶的認證應受保護、監視，且僅用於弱點掃描。

如有需要，請在一致的間隔匯出掃描結果，並將結果與先前的掃描進行比較，以確認已補救弱點。

遵循 Azure 資訊安全中心中的建議，在您的 Azure 虛擬機器 (和 SQL server) 上執行弱點評定。 Azure 資訊安全中心具有適用于虛擬機器、容器映射和 SQL database 的內建弱點掃描器。

視需要，以一致的間隔匯出掃描結果，並將結果與先前的掃描進行比較，以確認已補救弱點。 使用 Azure 資訊安全中心建議的弱點管理建議時，您可以在選取的解決方案入口網站中，切換到所選解決方案的入口網站以查看歷程記錄掃描資料。

- [如何實行 Azure 資訊安全中心弱點評定建議](/azure/security-center/security-center-vulnerability-assessment-recommendations) 

- [虛擬機器的整合式弱點掃描器](/azure/security-center/built-in-vulnerability-assessment) 
- [SQL 弱點評估](../azure-sql/database/sql-vulnerability-assessment.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7：快速自動補救軟體弱點

**指導** 方針： Windows 虛擬桌面不會使用或要求任何協力廠商軟體。 不過，Windows 虛擬桌面可讓您部署自己的虛擬機器，並將其註冊至服務。

使用 Azure 自動化更新管理或協力廠商解決方案，以確定您的 Windows Server 虛擬機器上已安裝最新的安全性更新。 若為 Windows 虛擬機器，請確定已啟用 Windows Update，並將其設定為自動更新。

針對協力廠商軟體使用協力廠商修補程式管理解決方案，或針對設定管理員使用 System Center Updates Publisher。

- [如何在 Azure 中設定虛擬機器的更新管理](/azure/automation/automation-update-management) 

- [管理 Azure VM 的更新和修補程式](/azure/automation/automation-tutorial-update-management)

- [設定 Windows 虛擬桌面的 Microsoft Endpoint Configuration Manager](configure-automatic-updates.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：執行一般攻擊模擬

**指導** 方針： Windows 虛擬桌面不允許客戶在他們的 Windows 虛擬桌面資源上執行自己的滲透測試。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="endpoint-security"></a>端點安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：端點安全性](/azure/security/benchmarks/security-controls-v2-endpoint-security)。*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1：使用端點偵測和回應 (EDR) 

**指導** 方針： Windows 虛擬桌面不會提供任何特定功能來偵測 (EDR) 進程的端點偵測和回應。 但是，註冊至服務的資源可受益于端點偵測和回應功能。 

啟用伺服器和用戶端的端點偵測和回應功能，並將它們與安全性資訊和事件管理整合 (SIEM) 解決方案和安全性作業程式。

Microsoft Defender 的先進威脅防護提供端點偵測和回應功能，作為企業端點安全性平臺的一部分，以防止、偵測、調查及回應先進的威脅。

- [Microsoft Defender 進階威脅防護總覽](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [適用于 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [適用于非 Windows 伺服器的 Microsoft Defender ATP 服務](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [非持續性虛擬桌面基礎結構的 Microsoft Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2：使用集中管理的新式反惡意程式碼軟體

**指導** 方針：使用集中管理且現代化的端點反惡意程式碼解決方案，能夠即時和定期掃描，以保護您的 Windows 虛擬桌面資源。

Azure 資訊安全中心可以自動識別您的虛擬機器使用許多常用的反惡意程式碼解決方案，並報告 endpoint protection 執行狀態並提出建議。

適用于 Azure 雲端服務的 Microsoft Antimalware 是 Windows 虛擬機器 (Vm) 的預設反惡意程式碼。 此外，您可以搭配資料服務的 Azure 資訊安全中心使用威脅偵測，來偵測上傳至 Azure 儲存體帳戶的惡意程式碼。

- [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md) 

- [支援的 endpoint protection 解決方案](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3：確保反惡意程式碼軟體和簽章已更新

**指導** 方針：確保以快速且一致的方式更新反惡意程式碼簽章。

遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」，以確保所有虛擬機器和（或）容器都是最新的簽章。

Microsoft Antimalware 預設會自動安裝最新的簽章和引擎更新。

- [如何部署 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md) 

- [Azure 資訊安全中心中的 Endpoint protection 評定和建議](../security-center/security-center-endpoint-protection.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="backup-and-recovery"></a>備份及修復

如需詳細資訊，請參閱 [Azure 安全性效能評定：備份和復原](/azure/security/benchmarks/security-controls-v2-backup-recovery)。

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1：確保定期自動備份

**指導** 方針：確定您要備份系統和資料，以在發生未預期的事件之後維護商務持續性。 這應該是復原點目標 (RPO) 和復原時間目標 (RTO) 的任何目標的指引。

啟用 Azure 備份並設定備份來源 (例如，Azure Vm、SQL Server、HANA 資料庫或檔案共用) ，以及所需的頻率和保留期限。

針對較高的冗余層級，您可以啟用地理區域冗余儲存選項，以將備份資料複寫到次要區域，並使用跨區域還原進行復原。

- [企業級商務持續性和災害復原](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) \(部分機器翻譯\) 

- [如何啟用 Azure 備份](/azure/backup/) 

- [如何啟用跨區域還原](/azure/backup/backup-azure-arm-restore-vms#cross-region-restore) 

- [如何在 Windows 虛擬桌面中設定商務持續性和嚴重損壞修復計畫](disaster-recovery.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="br-2-encrypt-backup-data"></a>BR-2：加密備份資料

**指導** 方針：確保您的備份可防止遭受攻擊。 這應該包括備份的加密，以防止機密性遺失。

針對一般的 Azure 服務備份，系統會使用 Azure 平臺管理的金鑰自動加密備份資料。 您可以選擇使用客戶管理的金鑰來加密備份。 在此情況下，請確定金鑰保存庫中的客戶管理金鑰也在備份範圍中。

在 Azure 備份、Azure Key Vault 或其他資源中使用角色型存取控制，以保護備份和客戶管理的金鑰。 此外，您可以啟用「高階安全性」功能來要求多重要素驗證，然後才能改變或刪除備份。

Azure 備份/azure/backup/security-overview 中的安全性功能總覽 

- [使用客戶管理的金鑰來加密備份資料](/azure/backup/encryption-at-rest-with-cmk) 

- [如何在 Azure 中備份 Key Vault 金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0&amp;preserve-view=true)

- [協助保護混合式備份免于遭受攻擊的安全性功能](/azure/backup/backup-azure-security-feature#prevent-attacks)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：驗證所有備份，包括客戶自控金鑰

**指導** 方針：建議您執行資料還原程式，以確保備份正常運作，以定期驗證備份媒體上的資料完整性。

- [如何從 Azure 虛擬機器備份復原檔案](/azure/backup/backup-azure-restore-files-from-vm)

- [安全性實行](/azure/backup/backup-azure-restore-files-from-vm#security-implementations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="governance-and-strategy"></a>控管與策略

如需詳細資訊，請參閱 [Azure 安全性效能評定：治理和策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理和資料保護策略 

**指引**：務必記載並傳達明確的策略，以持續監視及保護系統和資料。 請優先探索、評估、保護及監視業務關鍵資料和系統。 

此策略應該包含下列項目的已記載指引、原則和標準： 

-   符合商業風險的資料分類標準

-   安全性組織對風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用的程序 

-   資產在其生命週期中的安全性

-   符合組織資料分類的必要存取控制策略

-   Azure 原生和第三方資料保護功能的使用

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議 - 儲存體、資料和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json) (機器翻譯)

- [Azure 安全性基礎觀念 - Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md) (機器翻譯)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json) (機器翻譯)

- [Azure 安全性效能評定 - 資產管理](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure 安全性效能評定 - 資料保護](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指引**：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立企業範圍的策略來分割對產的存取。

審慎權衡安全性區隔的需求，以及需要與彼此通訊並存取資料的啟用每日作業需求。

確保在各種控制項類型上一致地實作分割策略，這些控制項類型包括網路安全性、身分識別和存取模型，以及應用程式權限/存取模型與人力流程控制。

- [Azure 中的分割策略指引 (影片)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) (機器翻譯)

- [Azure 中的分割策略指引 (文件)](/security/compass/governance#enterprise-segmentation-strategy) (機器翻譯)

- [使用企業分割策略來調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性狀態管理策略

**指引**：持續測量並降低個別資產及其裝載環境的風險。 優先處理高價值資產和高度公開的攻擊面，例如已發佈的應用程式、網路輸入和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性效能評定 - 狀態和弱點管理](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：協調組織角色、責任及權責

**指引**：務必為安全性組織中的角色和責任記載並傳達清楚的策略。 優先為安全性決策提供清楚的權責、讓每個人熟知共同責任模型，並讓技術團隊熟知保護雲端的技術。

- [Azure 安全性最佳做法 1 – 人員：讓小組熟知雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey) (機器翻譯)

- [Azure 安全性最佳做法 2 - 人員：讓小組熟知雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology) (機器翻譯)

- [Azure 安全性最佳做法 3 - 流程：指派雲端安全性決策的權責](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指引**：建立 Azure 網路安全性方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式網路管理和安全性責任

-   與企業分割策略一致的虛擬網路分割模型

-   不同威脅和攻擊案例的補救策略

-   網際網路邊緣和輸入與輸出策略

-   混合式雲端和內部部署互連能力策略

-   最新的網路安全性成品 (例如網路圖、參考網路架構)

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 安全性效能評定 - 網路安全性](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [企業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊權限存取策略

**指引**：建立 Azure 身分識別和特殊權限存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應該包含下列項目的已記載指引、原則和標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同使用案例和條件中的增強式驗證方法

-   高權限使用者的保護

-   異常使用者活動監視和處理  

-   使用者身分識別、存取權檢閱與核對流程

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 身分識別管理](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure 安全性效能評定 - 特殊權限存取](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure 安全性最佳做法 11 - 架構。單一整合的安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy) (機器翻譯)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指引**：建立記錄和威脅回應策略，以快速偵測威脅並進行補救，同時符合合規性需求。 優先為分析師提供高品質警示和順暢體驗，讓他們能夠專注處理威脅，而非整合和手動步驟。 

此策略應該包含下列項目的已記載指引、原則和標準： 

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義且與 NIST 或其他產業架構一致的事件回應流程 

-   記錄擷取和保留，以支援威脅偵測、事件回應及合規性需求

-   使用 SIEM、原生 Azure 功能及其他來源，集中顯示及相互關聯威脅的相關資訊 

-   與客戶、供應商和相關公開合作對象之間的溝通和通知計畫

-   使用 Azure 原生和協力廠商平台進行事件處理，例如記錄和威脅偵測、鑑定，以及攻擊補救和根除

-   處理事件和事件後活動的流程，例如吸取的經驗和證據保留

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 記錄與威脅偵測](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure 安全性效能評定 - 事件回應](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure 安全性最佳做法 4 - 流程。更新雲端的事件回應流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [Azure 採用架構、記錄與報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理與監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定 V2 概觀](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
