---
title: 適用于 Azure HPC Cache 的 Azure 安全性基準
description: Azure HPC Cache 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e7f6cf905613866041e72433328ea742706a8c49
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453873"
---
# <a name="azure-security-baseline-for-azure-hpc-cache"></a>適用于 Azure HPC Cache 的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 Microsoft Azure HPC Cache。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure HPC Cache 的相關指引來分組。 已排除不適用 Azure HPC Cache 的 **控制項**。

若要查看 Azure HPC Cache 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure HPC Cache 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱 [Azure 安全性效能評定：網路安全性](../security/benchmarks/security-controls-v2-network-security.md)。

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：執行內部流量的安全性

**指導** 方針：當您部署 Azure HPC Cache 資源時，必須建立或使用現有的虛擬網路。 

確定所有 Azure 虛擬網路都遵循符合業務風險的企業分割原則。 任何可能會對組織產生更高風險的系統，都應該隔離在其自己的虛擬網路內，並使用網路安全性群組 (NSG) 和/或 Azure 防火牆來充分保護。

您應該先設定兩個網路相關的必要條件，才能使用您的快取： 

- Azure HPC Cache 實例的專用子網

- DNS 支援，讓快取可以存取儲存體和其他資源

Azure HPC Cache 需要具有下列品質的專用子網： 

- 子網必須至少有64個可用的 IP 位址。

- 子網無法裝載任何其他 Vm，即使是用戶端電腦等相關服務也是如此。

- 如果您使用多個 Azure HPC Cache 實例，每一個實例都需要它自己的子網。

最佳做法是為每個快取建立新的子網。 您可以建立新的虛擬網路和子網，作為建立快取的一部分。

若要搭配內部部署 NAS 儲存體使用 HPC Cache，您必須確定內部部署網路中的特定埠允許來自 Azure HPC Cache 子網的不受限制流量。 

- [如何設定 NFS 儲存體存取的埠](hpc-cache-prerequisites.md#nfs-storage-requirements)

- [如何建立具有安全性規則的網路安全性群組](../virtual-network/tutorial-filter-network-traffic.md)

 

- [如何部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ns-2-connect-private-networks-together"></a>NS-2：將私人網路連接在一起

**指導** 方針：使用 azure ExpressRoute 或 azure 虛擬私人網路 (VPN) ，在共置環境中的 azure 資料中心與內部部署基礎結構之間建立私人連線。 ExpressRoute 連線不會經過公用網際網路，而且提供比一般網際網路連線更可靠、速度更快且延遲更低的位置。 針對點對站 VPN 和站對站 VPN，您可以使用這些 VPN 選項和 Azure ExpressRoute 的任意組合，將內部部署裝置或網路連線到虛擬網路。 

若要將 Azure 中的兩個或多個虛擬網路連接在一起，請使用虛擬網路對等互連。 對等互連虛擬網路之間的網路流量是私人的，並且會保留在 Azure 骨幹網路上。

- [什麼是 ExpressRoute 連線模型](../expressroute/expressroute-connectivity-models.md) 

- [Azure VPN 總覽](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [虛擬網路對等互連](../virtual-network/virtual-network-peering-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ns-3-establish-private-network-access-to-azure-services"></a>NS-3：建立 Azure 服務的私人網路存取

**指導** 方針：使用 Azure 虛擬網路服務端點來提供 HPC Cache 的安全存取。 服務端點是透過 Azure 骨幹網路的優化路由，而不會跨越網際網路。 

HPC Cache 不支援使用 Azure Private Link 來保護其管理端點對私人網路的安全。 

除了 Azure 服務所提供的驗證和流量安全性之外，私用存取是額外的深度防禦措施。

- [瞭解虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md) 

- [瞭解如何掛接 Azure HPC Cache](hpc-cache-mount.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4：保護應用程式和服務免于外部網路攻擊

**指導** 方針：保護 HPC Cache 資源免于遭受外部網路的攻擊，包括分散式阻絕服務 (DDoS) 攻擊、應用程式特定的攻擊，以及未經要求和潛在的惡意網際網路流量。 

Azure 包含此保護的原生功能： 

- 使用 Azure 防火牆來保護應用程式和服務免于來自網際網路和其他外部位置的潛在惡意流量。 
- 藉由在 Azure 虛擬網路上啟用 DDoS 標準保護，來保護您的資產免于 DDoS 攻擊。 
- 使用 Azure 資訊安全中心來偵測與網路資源相關的錯誤設定風險。

Azure HPC Cache 並非用來執行 web 應用程式，而且您不需要設定任何其他設定，也不需要部署任何額外的網路服務來保護它免于遭受以 web 應用程式為目標的外部網路攻擊。

- [Azure 防火牆檔](../firewall/index.yml) 

- [使用 Azure 入口網站管理 Azure DDoS 保護標準](../ddos-protection/manage-ddos-protection.md) 

- [Azure 資訊安全中心建議](../security-center/recommendations-reference.md#recs-network)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5：部署入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：使用 Azure 防火牆搭配威脅情報型篩選，以警示及/或封鎖進出已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 

當需要承載檢查時，您可以部署協力廠商入侵偵測/入侵，防止系統 (IDS/IPS) 解決方案，Azure Marketplace 具有承載檢查功能。 或者，您也可以選擇使用以主機為基礎的 IDS/IPS，或是以主機為基礎的端點偵測和回應 (EDR) 解決方案與或（而不是以網路為基礎的識別碼/IP）一起使用。

注意：如果您有關于 IDS/IPS 使用的法規或其他需求，請務必調整它，以提供高品質的警示給您的 SIEM 解決方案。

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace 協力廠商識別碼功能](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Microsoft Defender ATP EDR 功能](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ns-6-simplify-network-security-rules"></a>NS-6：簡化網路安全性規則

**指導** 方針：不適用;這項建議適用于可部署至 Azure 虛擬網路的供應專案，或可定義有效管理的允許 IP 範圍群組。 HPC Cache 目前不支援服務標記。 

最佳做法是為每個快取建立新的子網。 您可以建立新的虛擬網路和子網，作為建立快取的一部分。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ns-7-secure-domain-name-service-dns"></a>NS-7：安全的功能變數名稱服務 (DNS) 

**指導** 方針：遵循 dns 安全性的最佳作法來緩和常見的攻擊，例如無關聯的 DNS、dns amplifications 攻擊、dns 中毒和詐騙等等。

Azure HPC Cache 需要 DNS 才能存取快取私人虛擬網路之外的資源。 如果您的工作流程包含 Azure 以外的資源，除了使用 Azure DNS 之外，您還需要設定和保護您自己的 DNS 伺服器。

- 若要存取 Azure Blob 儲存體端點、以 Azure 為基礎的用戶端電腦或其他 Azure 資源，請使用 Azure DNS。
- 若要存取內部部署儲存體，或從 Azure 外部的用戶端連線至快取，您必須建立可解析這些主機名稱的自訂 DNS 伺服器。
- 如果您的工作流程包含內部和外部資源，請設定您的自訂 DNS 伺服器，以將 Azure 特定的解析要求轉送至 Azure DNS 伺服器。 

當 Azure DNS 作為授權 DNS 服務使用時，請確定使用 Azure RBAC 和資源鎖定，以防止 DNS 區域和記錄遭到意外或惡意修改。

如果設定您自己的 DNS 伺服器，請務必遵循下列安全性指導方針：

- [安全的網域名稱系統 (DNS) 部署指南](https://csrc.nist.gov/publications/detail/sp/800-81/2/final) 

- [防止無關聯的 DNS 專案，並避免子域接管](../security/fundamentals/subdomain-takeover.md) 

- [深入瞭解 HPC Cache 的 DNS 存取需求](hpc-cache-prerequisites.md#dns-access)

- [Azure DNS 總覽](../dns/dns-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-management"></a>身分識別管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：身分識別管理](../security/benchmarks/security-controls-v2-identity-management.md)。

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別與驗證系統

**指導** 方針： Azure HPC Cache 未與內部作業的 Azure Active Directory 整合。 不過，Azure AD 可以用來驗證 Azure 入口網站或 CLI 中的使用者，以建立、查看和管理 HPC Cache 部署和相關元件。

Azure Active Directory (Azure AD) 是 Azure 中的預設身分識別和存取管理服務。 您應該將 Azure AD 標準化，以管理組織的身分識別和存取管理：

- Microsoft 雲端資源，例如 Azure 入口網站、Azure 儲存體、Azure 虛擬機器 (Linux 和 Windows) 、Azure Key Vault、PaaS 和 SaaS 應用程式。

- 您的組織資源，例如 Azure 上的應用程式或您的公司網路資源。

保護 Azure AD 在組織的雲端安全性實務中，應具有較高的優先順序。 Azure AD 提供身分識別安全分數，協助您評定相對於 Microsoft 最佳做法建議的身分識別安全性狀態。 請使用此分數來測量設定符合最佳做法建議的程度，並改善您的安全性狀態。

注意： Azure AD 支援外部身分識別，讓沒有 Microsoft 帳戶的使用者可以使用其外部身分識別登入其應用程式和資源。

- [Azure Active Directory 中的租用](../active-directory/develop/single-and-multi-tenant-apps.md) 

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

- [對應用程式使用外部識別提供者](../active-directory/external-identities/identity-providers.md) 

- [Azure Active Directory 中的身分識別安全分數為何](../active-directory/fundamentals/identity-secure-score.md) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2：安全且自動地管理應用程式識別碼

**指導** 方針： HPC Cache 針對非人力帳戶（例如服務或自動化）使用 Azure 受控身分識別。 建議使用 Azure 的受控識別功能，而不是建立功能更強大的人帳戶來存取或執行您的資源。 

HPC Cache 可以透過預先定義的存取授與規則，以原生方式向支援 Azure AD 驗證的 Azure 服務/資源進行驗證。 這樣就不必在原始程式碼或設定檔中使用硬式編碼的認證。

- [Azure 受控識別](../active-directory/managed-identities-azure-resources/overview.md) 

- [支援適用於 Azure 資源的受控識別服務](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 單一登入 (SSO) 進行應用程式存取

**指導** 方針： Azure HPC Cache 不會與內部作業的 Azure AD 整合。 不過，Azure AD 可以用來驗證 Azure 入口網站或 CLI 中的使用者，以建立、查看和管理 HPC Cache 部署和相關元件。

Azure Active Directory 可提供 Azure 資源、雲端應用程式和內部部署應用程式的身分識別和存取管理。 這包括企業身分識別 (例如員工)，以及外部身分識別 (例如合作夥伴、廠商和供應商)。 這可讓單一登入 (SSO) 管理及保護內部部署和雲端中的組織資料與資源存取。 請將您的所有使用者、應用程式和裝置連線到 Azure AD，以進行順暢且安全的存取，並提供更高的可見度和控制。

- [了解 Azure AD 的應用程式 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：針對所有以 Azure Active Directory 為基礎的存取，使用增強式驗證控制項

**指導** 方針：雖然 Azure HPC Cache 不會與內部作業的 Azure AD 整合，但是 Azure AD 可以用來驗證 AZURE 入口網站或 CLI 中的使用者，以建立、查看和管理 HPC Cache 部署和相關元件。  

Azure AD 透過多重要素驗證 (MFA) 和強式無密碼方法來支援增強式驗證控制項。

- 多重要素驗證：啟用 Azure AD MFA，並遵循 Azure 資訊安全中心身分識別和存取管理建議，以取得 MFA 設定中的一些最佳作法。 MFA 可以根據登入條件和風險因素，在所有使用者、選取使用者或依使用者層級強制執行。
- 無密碼驗證 – 有三種無密碼驗證選項可供使用：Windows Hello 企業版、Microsoft Authenticator 應用程式和內部部署驗證方法 (例如智慧卡)。

對於系統管理員和特殊許可權的使用者，請務必使用強式驗證方法的最高層級。 將適當的增強式驗證原則推出給其他使用者。

Azure HPC Cache 也針對連線到快取的用戶端系統，支援舊版密碼驗證。 這些連線類型包括僅限雲端的帳戶 (直接在 Azure) 中建立的使用者帳戶，其具有基準密碼原則或混合式帳戶 (來自內部部署 Active Directory) 且將遵循內部部署密碼原則的使用者帳戶。 

使用密碼驗證時，Azure AD 提供密碼保護功能，防止使用者設定容易猜測的密碼。 Microsoft 提供以遙測為基礎的禁用密碼全域清單，客戶可以根據自己的需求來增強清單 (例如，具有商標、文化參考等) 。 此密碼保護可用於僅限雲端和混合式帳戶。

注意：以密碼認證為基礎的驗證，很容易受到常見攻擊方法的影響。 為了提高安全性，請使用強式驗證，例如 MFA 和強式密碼原則。 如果您使用具有預設密碼的協力廠商應用程式和 marketplace 服務，請在第一次設定服務時設定新的安全密碼。 

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [Azure Active Directory 的無密碼驗證選項簡介](../active-directory/authentication/concept-authentication-passwordless.md)

- [Azure AD 預設密碼原則](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [使用 Azure AD 密碼保護排除不正確的密碼](../active-directory/authentication/concept-password-ban-bad.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：監視及警示帳戶異常

**指導** 方針： Azure HPC Cache 可以使用 Azure Active Directory 從 Azure 入口網站進行使用者管理。  Azure Active Directory 提供下列資料來源：

- 登入 - 登入報表會提供受控應用程式和使用者登入活動的使用情況相關資訊。

- 稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

- 有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

這些資料來源可以與 Azure 監視器、Azure Sentinel 或協力廠商 SIEM 系統整合。

Azure 資訊安全中心也可能會對某些可疑活動發出警示，例如驗證失敗嘗試次數過多，以及訂用帳戶中已淘汰的帳戶。

Azure 進階威脅防護 (ATP) 是一項安全性解決方案，可使用 Active Directory 訊號來識別、偵測及調查進階威脅、遭入侵的身分識別，以及惡意的內部人員動作。

- [Azure Active Directory 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md) 

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md) 

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

 

- [Azure 資訊安全中心的威脅情報保護模組中的警示](../security-center/alerts-reference.md) 

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7：消除非預期的認證暴露

**指導** 方針：不適用;HPC Cache 不允許客戶將任何保存的資料部署至執行中的環境。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="privileged-access"></a>特殊權限存取

如需詳細資訊，請參閱 [Azure 安全性效能評定：特殊權限存取](../security/benchmarks/security-controls-v2-privileged-access.md)。

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對業務關鍵系統的系統管理存取

**指導** 方針： HPC Cache 使用 Azure RBAC 來隔離對商務關鍵性系統的存取權，方法是限制哪些帳戶會授與他們所屬訂用帳戶和管理群組的特殊許可權存取權。

使用專用的系統管理帳戶建立標準作業程式。 若要建立快取，HPC Cache 要求使用者必須在訂用帳戶中擁有足夠的許可權，才能建立 Nic。 如果使用 Blob 儲存體，則需要 Azure 角色儲存體帳戶參與者和儲存體 Blob 資料參與者，才能讓 HPC Cache 存取儲存體。 

確定您也會限制存取管理、身分識別和安全性系統，這些系統具有業務關鍵資產的系統管理存取權，例如 Active Directory 網網域控制站 (Dc) 、安全性工具和系統管理工具，並將代理程式安裝在商務關鍵系統上。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [Azure HPC Cache RBAC 許可權](hpc-cache-prerequisites.md#permissions)

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [管理群組存取](../governance/management-groups/overview.md#management-group-access)

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期檢閱及協調使用者存取

**指導** 方針：定期審核使用者帳戶和存取指派，以確保帳戶及其存取層級有效。 

Azure HPC Cache 可以使用 Azure Active Directory (Azure AD) 帳戶來管理透過 Azure 入口網站和相關介面的使用者存取。 Azure AD 提供存取權評論，可協助您查看群組成員資格、企業應用程式的存取權，以及角色指派。 Azure AD 報告可以提供記錄以協助探索過時的帳戶。 您也可以使用 Azure AD Privileged Identity Management 建立存取權審核報表工作流程，以加速審核程式。

此外，Azure Privileged Identity Management 可以設定為在建立過多的系統管理員帳戶時發出警示，以及識別已過時或設定不正確的系統管理員帳戶。

注意：某些 Azure 服務支援不是透過 Azure AD 管理的本機使用者和角色。 您將需要分開管理這些使用者。

使用 NFS 儲存體目標時，您必須與網路系統管理員和防火牆管理員合作來確認存取設定，並確保 Azure HPC Cache 能夠與 NFS 儲存體系統進行通訊。

- [如需 HPC Cache 許可權的清單，請參閱 Azure HPC Cache 必要條件](hpc-cache-prerequisites.md) 

- [在 Privileged Identity Management (PIM) 中建立 Azure 資源角色的存取權審核 ](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中設定緊急存取

**指導** 方針： HPC Cache 可以使用 Azure Active Directory 來管理透過 Azure 入口網站的資源存取。 若要避免不小心遭到 Azure AD 的組織封鎖，請在無法使用一般系統管理帳戶時，設定緊急存取帳戶以進行存取。 緊急存取帳戶通常具有高權限，不應將其指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的緊急或「急用」狀況。

您應該確保緊急存取帳戶的認證 (例如密碼、憑證或智慧卡) 受到保護，而且只有在緊急情況下有權使用這些認證的個人才會知道。

- [在 Azure AD 中管理緊急存取帳戶](../active-directory/roles/security-emergency-access.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-5-automate-entitlement-management"></a>PA-5：自動化權利管理 

**指導** 方針： HPC Cache 可以使用 Azure Active Directory 透過 Azure 入口網站來管理快取資源的存取權。 

使用 Azure AD 權利管理功能來自動化存取要求工作流程，包括存取權指派、評論和到期日。 也支援雙重或多階段核准。 

- [什麼是 Azure AD 存取權評論](../active-directory/governance/access-reviews-overview.md) 

- [什麼是 Azure AD 權利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循恰到好處的系統管理 (最低權限原則) 

**指導** 方針： HPC Cache 已與 Azure 角色型存取控制整合 (RBAC) 來管理其資源。 Azure RBAC 可讓您透過角色指派來管理 Azure 資源存取。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站之類的工具進行清查或查詢。 

透過 Azure RBAC 指派給資源的許可權應該一律限制為角色所需的許可權。 這可補充 Azure AD Privileged Identity Management (PIM) 的即時 (JIT) 方法，並應定期檢查。

請使用內建角色來配置權限，且僅在必要時才建立自訂角色。

- [什麼是 Azure 角色型存取控制 (Azure RBAC) ](../role-based-access-control/overview.md) 

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md) 

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱 [Azure 安全性效能評定：資料保護](../security/benchmarks/security-controls-v2-data-protection.md)。

### <a name="dp-1-discover-classify-and-label-sensitive-data"></a>DP-1：探索、分類及標記敏感性資料 

**指導** 方針： HPC Cache 管理敏感性資料，但沒有探索、分類及標記敏感性資料的功能。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="dp-2-protect-sensitive-data"></a>DP-2：保護敏感性資料

**指導** 方針：使用 azure 角色型存取控制來限制存取權，以使用 azure 角色型存取控制來保護敏感性資料 (azure RBAC) 、網路型存取控制和 azure 服務中的特定控制項 (例如 SQL 中的加密和其他資料庫) 。

為確保存取控制的一致性，所有類型的存取控制都應與您的企業分割策略相符。 企業分割策略也應傳達至敏感性或業務關鍵資料和系統的所在位置。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作某些預設的資料保護控制項和功能。

- [Azure 角色型存取控制 (RBAC)](../role-based-access-control/overview.md) 

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3：監視未經授權的敏感性資料傳輸

**指導** 方針： HPC Cache 傳輸敏感性資料，但不支援對未經授權的機密資料傳輸進行監視。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4：加密傳輸中的敏感性資訊

**指導** 方針： HPC Cache 支援以 TLS 1.2 或更新版本傳輸的資料加密。

雖然這對私人網路的流量是選擇性的，但對於外部和公用網路上的流量而言是不可或缺的。 針對 HTTP 流量，請確定任何連線至 Azure 資源的用戶端都可以協調 TLS 1.2 或更新版本。 若要進行遠端系統管理，請使用適用于 Linux) 的 SSH (或適用于 Windows) 的 RDP/TLS (，而不是未加密的通訊協定。 已淘汰的 SSL、TLS 和 SSH 版本和通訊協定，以及弱式密碼都應停用。

根據預設，Azure 會為 Azure 資料中心之間傳輸中的資料提供加密功能。

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [TLS 安全性的資訊](/security/engineering/solving-tls1-problem) 

- [Azure 資料傳輸中的雙重加密](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="dp-5-encrypt-sensitive-data-at-rest"></a>DP-5：將敏感性待用資料加密

**指導** 方針：若要補充存取控制，待用資料應受限於「頻外」攻擊 (例如，使用加密存取基礎儲存體) 。 這有助於確保攻擊者無法輕易地讀取或修改資料。

Azure 預設會提供待用資料加密。 針對高度敏感的資料，您可以選擇在所有可用的 Azure 資源上執行額外的靜態加密。 根據預設，azure 會管理您的加密金鑰，但 Azure 會提供選項來管理您自己的金鑰， (特定 Azure 服務) 的客戶管理金鑰。

所有儲存在 Azure 中的資料（包括在快取磁片上）預設會使用 Microsoft 管理的金鑰進行靜態加密。 如果您想要管理用來加密資料的金鑰，您只需要自訂 Azure HPC Cache 設定。

如果需要對計算資源進行合規性，請執行協力廠商工具（例如自動化的主機型資料遺失防護解決方案），即使在系統上複製資料時，也會對資料強制執行存取控制。

- [如何搭配 Azure HPC Cache 使用客戶管理的加密金鑰](./hpc-cache-create.md?tabs=azure-portal#enable-azure-key-vault-encryption-optional)

- [了解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [如何設定客戶管理的加密金鑰](../storage/common/customer-managed-keys-configure-key-vault.md) 

- [加密模型和金鑰管理表](../security/fundamentals/encryption-atrest.md)

 

- [Azure 中的待用資料加密](../security/fundamentals/double-encryption.md#data-at-rest)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="asset-management"></a>資產管理

如需詳細資訊，請參閱 [Azure 安全性效能評定：資產管理](../security/benchmarks/security-controls-v2-asset-management.md)。

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：確保安全性小組能夠看到資產的風險

**指引**：確保安全性小組會獲得 Azure 租用戶和訂用帳戶中的「安全性讀取者」權限，而能夠使用 Azure 資訊安全中心來監視安全性風險。 

根據安全性小組的權責架構，監視安全性風險可能是由中央安全性小組或區域小組負責。 然而，安全性見解和風險務必要在組織內部集中彙總。 

「安全性讀取者」權限可以廣泛套用至整個租用戶 (根管理群組)，或將範圍限定於管理群組或特定訂用帳戶。 

注意：若要取得工作負載和服務的可見度，可能需要其他權限。 

- [安全性讀取者角色概觀](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組概觀](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2：確保安全性小組可以存取資產清查和中繼資料

**指導** 方針： Azure HPC Cache 支援使用標記。 將標籤套用至您的 Azure 資源、資源群組和訂用帳戶，以邏輯方式將這些標記組織成分類法。 每個標記都是由一個名稱和一個值配對組成。 

例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。 建立快取時，以及部署快取之後，都可以新增標記。 

使用 Azure 虛擬機器清查，將虛擬機器上有關軟體的資訊收集自動化。 您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得安裝日期和其他資訊的存取權，請啟用來賓層級的診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。
HPC Cache 不允許在其資源上執行應用程式或安裝軟體。 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md) 

- [Azure 資訊安全中心資產庫存管理](../security-center/asset-inventory.md) 

- [資源命名與標記決策指南](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json) 

- [如何啟用 Azure 虛擬機器清查](../automation/automation-tutorial-installed-software.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="am-3-use-only-approved-azure-services"></a>AM-3：僅使用已核准的 Azure 服務

**指導** 方針： HPC Cache 支援 Azure Resource Manager 部署。 使用 Azure 原則可稽核及限制使用者能夠在環境中佈建的服務。 使用 Azure Resource Graph 則可查詢及探索其訂用帳戶內的資源。 您也可以使用 Azure 監視器來建立規則，以在偵測到未核准的服務時觸發警示。

- [設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md) 

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：確保資產生命週期管理的安全性

**指引**：不適用。 Azure HPC Cache 不能用來確保生命週期管理程式中資產的安全性。 客戶必須負責維護視為高影響之資產的屬性和網路設定。 

建議客戶建立一個處理常式來捕獲屬性和網路設定變更、測量變更影響，以及建立適當的補救工作。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄與威脅偵測

如需詳細資訊，請參閱 [Azure 安全性效能評定：記錄與威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)。

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1：啟用 Azure 資源的威脅偵測

**指導** 方針：使用 Azure 資訊安全中心內建的威脅偵測功能，並針對您的 HPC Cache 資源，啟用 Azure Defender (正式 Azure 進階威脅防護) 。 適用于 HPC Cache 的 Azure Defender 提供額外一層的安全性情報，可偵測不尋常且可能有害的存取或惡意探索您的快取資源嘗試。

將任何記錄從 HPC Cache 轉寄至 SIEM，可用來設定自訂威脅偵測。 確定您正在監視不同類型的 Azure 資產，以找出潛在的威脅和異常。 專注于取得高品質的警示，以減少因分析師排序的誤報。 警示可能源自于記錄資料、代理程式或其他資料。

- [Azure 資訊安全中心內的威脅防護](../security-center/azure-defender.md) 

- [Azure 資訊安全中心安全性警示參考指南](../security-center/alerts-reference.md) 

- [建立自訂分析規則來偵測威脅](../sentinel/tutorial-detect-threats-custom.md) 

- [使用 Azure Sentinel 的網路威脅情報](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別與存取權管理的威脅偵測

**指導** 方針： Azure AD 提供下列使用者記錄檔，可在 Azure AD 報告中查看或與 Azure 監視器、Azure Sentinel 或其他 SIEM/監視工具整合，以取得更精密的監視和分析使用案例：
- 登入 – 登入報告會提供受控應用程式和使用者登入活動的使用情況相關資訊。

- Audit logs-透過記錄檔，針對 Azure AD 內各種功能所完成的所有變更，提供可追蹤性。 Audit 記錄檔的範例包括對 Azure AD 中任何資源所做的變更，例如新增或移除使用者、應用程式、群組、角色和原則。

- 有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

- 標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

Azure 資訊安全中心也可能會對某些可疑活動發出警示，例如驗證嘗試失敗的次數過多，或訂用帳戶中已淘汰的帳戶。 除了基本的安全性檢查監視以外，Azure 資訊安全中心的威脅防護模組也可以從個別的 Azure 計算資源 (虛擬機器、容器、App Service)、資料資源 (SQL DB 和儲存體) 與 Azure 服務層收集更深入的安全性警示。 這項功能可讓您在個別資源內看到帳戶異常。

- [Azure Active Directory 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Azure 資訊安全中心內的威脅防護](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

**指導** 方針：除了常用的封包捕獲工具之外，您還可以使用 VPN 閘道和其封包捕獲功能，來記錄在您的虛擬網路之間傳送的網路封包。

在部署 Azure HPC Cache 資源的網路上部署網路安全性群組。 在網路安全性群組上啟用網路安全性群組流量記錄以進行流量審核。

您的流程記錄會保留在儲存體帳戶中。 啟用流量分析解決方案，以處理並傳送這些流量記錄至 Log Analytics 工作區。 流量分析可讓您深入瞭解 Azure 網路的流量流程。 流量分析可協助您將網路活動視覺化、找出作用點、找出安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。

快取需要 DNS 才能存取其虛擬網路之外的資源。 視您使用的資源而定，您可能需要設定自訂的 DNS 伺服器，並設定該伺服器與 Azure DNS 伺服器之間的轉送。 

根據您的組織需求，根據 DNS 記錄解決方案的 Azure Marketplace 來執行協力廠商解決方案。

- [設定 VPN 閘道的封包捕獲](../vpn-gateway/packet-capture.md) 

- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md) 

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md) 

- [深入瞭解 DNS 必要條件](hpc-cache-prerequisites.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄功能

**指導** 方針： Azure HPC Cache 資源會自動建立活動記錄。 這些記錄包含所有寫入作業 (PUT、POST、DELETE) 但不包含讀取作業 (取得) 。 活動記錄可以用來在進行疑難排解時發現錯誤，或是監視組織中的使用者修改資源的方式。

您也可以使用 Azure 資訊安全中心和 Azure 原則來啟用適用于 HPC Cache 的 Azure 資源記錄，以及記錄資料收集。 這些記錄檔可能很重要，可供稍後調查安全性事件和執行法庭訓練。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md) 

- [瞭解 Azure 資訊安全中心資料收集](../security-center/security-center-enable-data-collection.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：將安全性記錄的管理與分析集中

**指導** 方針：集中記錄儲存體和分析以啟用相互關聯。 針對每個記錄來源，請確定您已指派資料擁有者、存取指引、儲存位置、使用哪些工具來處理和存取資料，以及資料保留需求。

確定您要將 Azure 活動記錄整合到您的集中記錄。 透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

此外，請啟用資料並將其上架至 Azure Sentinel 或協力廠商 SIEM。

許多組織選擇使用 Azure Sentinel 「經常性存取」資料，而這些資料經常使用，並 Azure 儲存體使用較不常使用的「冷」資料。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱 [Azure 安全性效能評定：事件回應](../security/benchmarks/security-controls-v2-incident-response.md)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備 – 更新 Azure 的事件回應流程

**指引**：確定您的組織具有回應安全性事件的流程、已更新 Azure 的這些處理序，而且會定期執行以確保就緒。

- [在企業環境中實作安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf) (英文)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--set-up-incident-notification"></a>IR-2：準備–設定事件通知 

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

Azure 資訊安全中心會指派每個警示的嚴重性，以協助您設定應優先調查哪些警示。 嚴重性是以「資訊安全中心」在「尋找」或 analytically 用來發出警示的信賴度為基礎，以及導致警示的活動背後有惡意意圖的信賴等級。

此外，請使用標籤來標示資源，並建立命名系統以識別及分類 Azure 資源，尤其是處理敏感資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

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

如需詳細資訊，請參閱 [Azure 安全性效能評定：狀況和弱點管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)。

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3：建立計算資源的安全設定

**指導** 方針：使用 Azure 資訊安全中心和 Azure 原則來建立所有計算資源的安全設定，包括 vm、容器及其他資源。

- [如何監視 Azure 資訊安全中心建議](../security-center/security-center-recommendations.md) 

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：執行一般攻擊模擬

**指引**：如有需要，請對您的 Azure 資源執行滲透測試或紅隊活動，並確保補救所有重要的安全性結果。
請遵循 Microsoft 雲端滲透測試參與規則，以確保您的滲透測試不會違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md) (機器翻譯)

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="backup-and-recovery"></a>備份及修復

如需詳細資訊，請參閱 [Azure 安全性效能評定：備份和復原](../security/benchmarks/security-controls-v2-backup-recovery.md)。

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1：確保定期自動備份

**指導** 方針：由於 Azure HPC Cache 是快取解決方案，而不是儲存系統，因此著重于確保定期備份其儲存目標中的資料。 遵循 Azure Blob 容器的標準程式，以及備份任何內部部署儲存體目標。 

若要在發生區域性中斷時將中斷情形降到最低，您可以採取步驟來確保跨區域資料存取。  

每個 Azure HPC Cache 實例都在特定訂用帳戶和一個區域內執行。 這表示如果區域有完全中斷的情況，您的快取工作流程可能會中斷。 為了將這項中斷降到最低，組織應該使用可從多個區域存取的後端儲存體。 此儲存體可以是具有適當 DNS 支援的內部部署 NAS 系統，或是位於快取的不同區域中的 Azure Blob 儲存體。

當您的工作流程在主要區域中繼續時，資料會儲存在區域以外的長期儲存體中。 如果快取區域變得無法使用，您可以在次要區域中建立重複的 Azure HPC Cache 實例、連線至相同的儲存體，然後從新的快取繼續工作。

- [深入瞭解區域容錯移轉](hpc-region-recovery.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="br-2-encrypt-backup-data"></a>BR-2：加密備份資料

**指導** 方針：確保您的備份受到保護，以防止遭受攻擊。 這應該包括備份的加密，以防止機密性遺失。

針對使用 Azure 備份的內部部署備份，會使用您提供的複雜密碼來提供靜態加密。 針對一般的 Azure 服務備份，系統會使用 Azure 平臺管理的金鑰自動加密備份資料。 您可以選擇使用客戶管理的金鑰來加密備份。 在此情況下，請確定金鑰保存庫中的客戶管理金鑰也在備份範圍中。

即使您為快取磁片新增客戶金鑰，Azure HPC Cache 也會在保存您快取資料的受控磁片上受到 VM 主機加密的保護。 新增客戶管理的金鑰進行雙重加密，為具有高安全性需求的客戶提供額外的安全性層級。 如需詳細資料，請參閱 Azure 磁片儲存體的伺服器端加密。

在 Azure 備份、Azure Key Vault 或其他資源中使用角色型存取控制，以保護備份和客戶管理的金鑰。 此外，您可以啟用 advanced security 功能來要求 MFA，然後才能改變或刪除備份。

- [VM 主機加密](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

- [Azure 磁片儲存體的伺服器端加密](../virtual-machines/disk-encryption.md)

- [Azure 備份中的安全性功能概觀](../backup/security-overview.md) 

- [使用客戶管理的金鑰來加密備份資料](../backup/encryption-at-rest-with-cmk.md)  

- [如何在 Azure 中備份 Key Vault 金鑰](/powershell/module/az.keyvault/backup-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3：驗證所有備份，包括客戶自控金鑰

**指導** 方針：定期確保您可以還原已備份的客戶管理金鑰。

- [如何在 Azure 中還原 Key Vault 金鑰](/powershell/module/az.keyvault/restore-azkeyvaultkey?amp;preserve-view=true&view=azps-5.1.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="br-4-mitigate-risk-of-lost-keys"></a>BR-4：降低遺失金鑰的風險

**指導** 方針：確定您已備妥量值，以防止和復原遺失的金鑰。 請啟用 Azure Key Vault 中的虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。

- [如何在 Key Vault 中啟用虛刪除和清除保護](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="governance-and-strategy"></a>控管與策略

如需詳細資訊，請參閱 [Azure 安全性效能評定：治理和策略](../security/benchmarks/security-controls-v2-governance-strategy.md)。

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理和資料保護策略 

**指引**：務必記載並傳達明確的策略，以持續監視及保護系統和資料。 請優先探索、評估、保護及監視業務關鍵資料和系統。 

此策略應該包含下列項目的已記載指引、原則和標準： 

-   符合商業風險的資料分類標準

-   安全性組織對風險和資產清查的可見度 

-   安全性組織核准 Azure 服務以供使用的程序 

-   資產在其生命週期中的安全性

-   符合組織資料分類的必要存取控制策略

-   使用 Azure 原生和協力廠商資料保護功能

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議 - 儲存體、資料和加密](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json) (機器翻譯)

- [Azure 安全性基礎觀念 - Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md) (機器翻譯)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](../security/fundamentals/data-encryption-best-practices.md?amp;bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json) (機器翻譯)

- [Azure 安全性效能評定 - 資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全性效能評定 - 資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)

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

- [Azure 安全性效能評定 - 狀態和弱點管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

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

- [Azure 安全性效能評定 - 網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)

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

- [Azure 安全性效能評定 - 身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全性效能評定 - 特殊權限存取](/azure/security/benchmarks/security-controls-v2-privileged-access)

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

- [Azure 安全性效能評定 - 記錄與威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全性效能評定 - 事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全性最佳做法 4 - 流程。更新雲端的事件回應流程](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud) (機器翻譯)

- [Azure 採用架構、記錄與報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理與監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring) (機器翻譯)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定 V2 概觀](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)