---
title: 適用于 Azure 防禦的 azure 安全性基準
description: Azure 防禦安全性基準提供程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: bastion
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 461d3800491dbe382beecbcdfe1f2a93bd6b2e5c
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301562"
---
# <a name="azure-security-baseline-for-azure-bastion"></a>適用于 Azure 防禦的 azure 安全性基準

此安全性基準會將來自 [Azure 安全性基準測試版本 2.0](../security/benchmarks/overview.md) 的指引套用至 azure 防禦。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 azure 防禦的相關指引來分組。 已排除不適用 Azure 防禦的 **控制項**。

若要瞭解 Azure 防禦如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 azure 防禦安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)。*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1：執行內部流量的安全性

**指導** 方針：當您部署 Azure 防禦資源時，您必須建立或使用現有的虛擬網路。 確定所有 Azure 虛擬網路都遵循符合業務風險的企業分割原則。 任何可能會對組織產生更高風險的系統，都應該隔離在其自己的虛擬網路內，並使用網路安全性群組來充分保護 (NSG) 。

Azure 防禦服務需要開啟下列埠，服務才能正常運作：

- 輸入流量：
   - 輸入來自公用網際網路的流量： Azure 防禦會建立一個公用 IP，其需要在公用 IP 上啟用埠443來輸入流量。 不需要在 AzureBastionSubnet 上開啟埠3389/22。 

   - 從 Azure 防禦控制平面輸入流量：若為控制平面連線能力，請從 GatewayManager 服務標記啟用埠443輸入。 這可讓控制平面（也就是閘道管理員）能夠與 Azure 防禦通訊。

- 輸出流量：

   -  (Vm) 的目標虛擬機器輸出流量： Azure 防禦會透過私人 IP 連接目標 Vm。 Nsg 需要允許埠3389和22的其他目標 VM 子網的輸出流量。

   - Azure 中其他公用端點的輸出流量： Azure 防禦必須能夠連線到 Azure (中的各種公用端點，例如，將診斷記錄和計量記錄儲存) 。 基於這個理由，Azure 防禦需要輸出至443至 AzureCloud 服務標記。

閘道管理員和 Azure 服務標籤的連線受到保護， (由 Azure 憑證) 鎖定。 外部實體（包括這些資源的取用者）無法在這些端點上進行通訊。 

- [如何建立具有安全性規則的網路安全性群組](../virtual-network/tutorial-filter-network-traffic.md)

- [您可以在這裡深入瞭解防禦 NSG 需求](bastion-nsg.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ns-2-connect-private-networks-together"></a>NS-2：將私人網路連接在一起

**指導** 方針： Azure 防禦不會公開可透過私人網路存取的任何端點。 Azure 防禦支援部署至對等互連網路，以集中您的防禦部署並啟用跨網路連接。

- [Azure 防禦和虛擬網路對等互連](vnet-peering.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-management"></a>身分識別管理

*如需詳細資料，請參閱 [Azure 安全性效能評定：身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)。*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1：將 Azure Active Directory 標準化為中央身分識別和驗證系統

**指導** 方針： azure 防禦整合了 Azure Active Directory 的 (Azure AD) 這是 azure 的預設身分識別和存取管理服務。 使用者可以使用 Azure AD authentication 來存取 Azure 入口網站，以管理 Azure 防禦服務 (建立、更新及刪除防禦資源) 。

使用 Azure 防禦連接到虛擬機器依賴 SSH 金鑰或使用者名稱/密碼，且目前不支援使用 Azure AD 認證。

除了 SSH 金鑰或使用者名稱/密碼外，當使用 Azure 防禦連接至虛擬機器時，您的使用者將需要下列角色指派：
- 目標虛擬機器上的讀取者角色
- 具有目標虛擬機器之私人 IP 的 NIC 上的讀取者角色
- Azure Bastion 資源上的讀取者角色

如需詳細資訊，請參閱下列參考資料：

- [使用 Azure 防禦連接至 Linux 虛擬機器](bastion-connect-vm-ssh.md)

- [使用 Azure 防禦連接到 Windows 虛擬機器](bastion-connect-vm-rdp.md)

- [Azure 內建角色](../role-based-access-control/built-in-roles.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3：使用 Azure AD 單一登入 (SSO) 進行應用程式存取

**指導** 方針： Azure 防禦在向虛擬機器資源進行驗證時，不支援 SSO 進行驗證，只支援 SSH 或使用者名稱/密碼。 不過，Azure 防禦使用 Azure Active Directory (Azure AD) 為整體服務提供身分識別和存取管理。 使用者可以向 Azure AD 驗證以存取及管理其 Azure 防禦資源，並透過 Azure AD Connect 透過自己同步的企業身分識別體驗順暢的單一登入。 

- [了解使用 Azure AD 的應用程式 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure AD 連接](../active-directory/hybrid/whatis-azure-ad-connect.md)

- [使用 Azure 防禦連接至 Linux 虛擬機器](bastion-connect-vm-ssh.md)

- [使用 Azure 防禦連接到 Windows 虛擬機器](bastion-connect-vm-rdp.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4：對所有以 Azure Active Directory 為基礎的存取使用增強式驗證控制項

**指導** 方針： Azure 防禦整合了 Azure Active Directory 的 (Azure AD) ，以存取和管理服務。 為您的 Azure AD 租使用者設定 Azure Multi-Factor Authentication。 Azure AD 透過多重要素驗證 (MFA) 和強式無密碼方法來支援強式驗證控制項。  
- 多重要素驗證：啟用 Azure AD MFA，並遵循 MFA 設定的 Azure 資訊安全中心身分識別和存取管理建議。 MFA 可以根據登入條件和風險因素，在所有使用者、選取使用者或依使用者層級強制執行。 

- 無密碼 authentication：有三種可用的無密碼 authentication 選項： Windows Hello 企業版、Microsoft Authenticator 應用程式和內部部署驗證方法（例如智慧卡）。 

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Active Directory 的無密碼驗證選項簡介](../active-directory/authentication/concept-authentication-passwordless.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5：帳戶異常的監視和警示

**指導** 方針：啟用 Azure 防禦遠端會話的診斷記錄，並使用這些記錄來查看哪些使用者連接到哪些工作負載、哪些時間、從何處，以及其他這類相關的記錄資訊。 使用 Azure 監視器建立特定記錄防禦會話的警示，以在記錄中偵測到異常時收到通知。

- [您可以在這裡找到如何啟用診斷記錄的詳細資訊](diagnostic-logs.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6：根據條件限制 Azure 資源存取

**指導** 方針：您只能透過 Azure 入口網站存取 Azure 防禦服務，可以使用 Azure Active Directory (Azure AD) 條件式存取來限制 Azure 入口網站的存取權。 根據使用者定義的條件，使用 Azure AD 條件式存取來進行更細微的存取控制，例如要求特定 IP 範圍的使用者登入使用 MFA。

客戶也可以在已加入網域的虛擬機器層級使用不同的角色型存取控制原則，以進一步限制對虛擬機器的存取。

- [您可以在這裡閱讀 Azure AD 條件式存取的詳細資訊](../active-directory/conditional-access/overview.md)

- [Azure 條件式存取總覽](../active-directory/conditional-access/overview.md)

- [一般條件式存取原則](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [使用條件式存取設定驗證會話管理](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="privileged-access"></a>特殊權限存取

*如需詳細資料，請參閱 [Azure 安全性效能評定：特殊權限存取](/azure/security/benchmarks/security-controls-v2-privileged-access)。*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對商務關鍵性系統的系統管理存取

**指導** 方針： azure 防禦使用 azure 角色型存取控制 (azure RBAC) 藉由限制哪些帳戶被授與特定虛擬機器的存取權，來隔離對商務關鍵性系統的存取。 請務必遵循最低許可權的原則，讓使用者只具備執行其特定工作所需的許可權。

確定您也會限制對管理、身分識別和安全性系統的存取權，這些系統具有您商務關鍵存取權的系統管理存取權，例如 Active Directory 網網域控制站 (Dc) 、安全性工具和系統管理工具，以及安裝在商務關鍵性系統上的代理程式。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。

- [使用 Azure 防禦來存取虛擬機器的必要角色](bastion-faq.md#roles)

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理群組存取](../governance/management-groups/overview.md#management-group-access)

- [Azure 訂用帳戶管理員](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期檢閱和協調使用者存取

**指導** 方針： Azure 防禦使用 Azure Active Directory (Azure AD) 帳戶和 azure RBAC 來管理其資源。 定期審核使用者帳戶和存取指派，以確保帳戶及其存取權都有效。 您可以使用 Azure AD 存取評論來查看群組成員資格、企業應用程式的存取權，以及角色指派。 Azure AD 報告可以提供記錄以協助探索過時的帳戶。 您也可以使用 Azure AD Privileged Identity Management 建立存取權審核報表工作流程，以加速審核程式。

此外，Azure Privileged Identity Management 也可以設定為在建立過多的系統管理員帳戶時發出警示，以及識別已過時或設定不正確的系統管理員帳戶。

- [在 Privileged Identity Management (PIM) 中建立 Azure 資源角色的存取權審核 ](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [移除委派的存取權](../lighthouse/how-to/remove-delegation.md)

- [如何使用 Azure AD 身分識別和存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中設定緊急存取

**指導** 方針： azure 防禦已與 Azure Active Directory 和 azure RBAC 整合，以管理其資源。 若要避免不小心遭到 Azure AD 的組織封鎖，請在無法使用一般系統管理帳戶時，設定緊急存取帳戶以進行存取。 緊急存取帳戶通常具有高度許可權，不應指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的緊急或「急用」狀況。

您應該確保緊急存取帳戶的認證 (（例如密碼、憑證或智慧卡) ）保持安全，而且只有獲授權可在緊急情況下使用這些認證的人員才知道。

- [在 Azure AD 中管理緊急存取帳戶](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-5-automate-entitlement-management"></a>PA-5：自動化權利管理 

**指導** 方針： Azure 防禦整合了 Azure Active Directory (Azure AD) 和 azure RBAC 來管理其資源。 使用 Azure AD 權利管理功能來自動化存取要求工作流程，包括存取權指派、評論和到期日。 也支援雙重或多階段核准。

- [什麼是 Azure AD 存取權評論](../active-directory/governance/access-reviews-overview.md)

- [什麼是 Azure AD 權利管理](../active-directory/governance/entitlement-management-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊許可權的存取工作站

**指導** 方針：安全、隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 視您的需求而定，您可以使用高度安全的使用者工作站，在生產環境中使用您的 Azure 防禦資源來執行系統管理管理工作。 使用 Azure Active Directory、Microsoft Defender 進階威脅防護 (ATP) 和/或 Microsoft Intune 部署安全且受管理的使用者工作站以進行系統管理工作。 受保護的工作站可以集中管理以強制執行安全的設定，包括強式驗證、軟體和硬體基準，以及受限的邏輯和網路存取。 

- [瞭解特殊許可權的存取工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [部署特殊許可權存取工作站](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循恰到好處的系統管理 (最低權限原則) 

**指導** 方針： azure 防禦會與 azure 角色型存取控制整合 (RBAC) 來管理其資源。 Azure RBAC 可讓您透過角色指派來管理 Azure 資源存取。 您可以將這些內建角色指派給使用者、群組、服務主體和受控識別。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站之類的工具進行清查或查詢。 透過 Azure RBAC 指派給資源的權限，應一律限定為角色所需的權限。 這類權限可補強 Azure AD Privileged Identity Management (PIM) 的 Just-In-Time (JIT) 方法，且您應定期加以檢閱。 使用內建角色來配置許可權，而且只在必要時才建立自訂角色。

使用 Azure 防禦連接至虛擬機器時，您的使用者將需要下列角色指派：
- 目標虛擬機器上的讀取者角色
- 具有目標虛擬機器之私人 IP 的 NIC 上的讀取者角色
- Azure Bastion 資源上的讀取者角色

如需詳細資訊，請參閱下列參考資料：

- [使用 Azure 防禦連接至 Linux 虛擬機器](bastion-connect-vm-ssh.md)

- [使用 Azure 防禦連接到 Windows 虛擬機器](bastion-connect-vm-rdp.md)

- [Azure 內建角色](../role-based-access-control/built-in-roles.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="asset-management"></a>資產管理

*如需詳細資料，請參閱 [Azure 安全性效能評定：資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)。*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1：確保安全性小組能夠看到資產的風險

**指引**：確保安全性小組會獲得 Azure 租用戶和訂用帳戶中的「安全性讀取者」權限，而能夠使用 Azure 資訊安全中心來監視安全性風險。 

根據安全性小組的權責架構，監視安全性風險可能是由中央安全性小組或區域小組負責。 然而，安全性見解和風險務必要在組織內部集中彙總。 

「安全性讀取者」權限可以廣泛套用至整個租用戶 (根管理群組)，或將範圍限定於管理群組或特定訂用帳戶。 

注意：若要取得工作負載和服務的可見度，可能需要其他權限。 

- [安全性讀取者角色概觀](../role-based-access-control/built-in-roles.md#security-reader)

- [Azure 管理群組概觀](../governance/management-groups/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>上午-2：確保安全性小組可以存取資產清查和中繼資料

**指導** 方針：將標記套用至您的 Azure 防禦資源、資源群組和訂用帳戶，以邏輯方式將它們組織成分類法。 每個標記都是由一個名稱和一個值配對組成。 例如，您可以將「環境」名稱和「生產」值套用至生產環境中的所有資源。 請確定安全性小組可以存取 Azure 上不斷更新的資產清查。 他們可以使用 Azure Resource Graph 來查詢及探索訂用帳戶中的所有資源，包括 Azure 服務、應用程式和網路資源。

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

- [Azure 資訊安全中心資產庫存管理](../security-center/asset-inventory.md)

- [如需標記資產的詳細資訊，請參閱資源命名和標記決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="am-3-use-only-approved-azure-services"></a>上午-3：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來審核和限制使用者可以在您的環境中布建的服務，這包括能夠允許或拒絕 Azure 防禦資源的部署。 使用 Azure Resource Graph 來查詢及探索其訂用帳戶內的資源。 您也可以使用 Azure 監視器來建立規則，以在偵測到未核准的服務時觸發警示。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/built-in-policies.md#general)

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4：確保資產生命週期管理的安全性

**指導** 方針：當不再需要存取已部署的 Azure 防禦資源，以將攻擊面降至最低時，請移除這些資源的存取權。 使用者可以透過 Azure 入口網站、CLI 或 REST Api 來管理其 Azure 防禦資源。 如果不再需要或未識別為潛在威脅，您也可以刪除或強制中斷進行中的遠端會話。

- [刪除強制中斷連線遠端會話](session-monitoring.md#view)

- [Azure 網路 CLI](https://docs.microsoft.com/powershell/module/az.network/?view=azps-5.1.0#networking&amp;preserve-view=true)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>上午-5：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針： Azure 防禦整合了 Azure Active Directory 的 (Azure AD) 以進行身分識別和驗證。 您可以使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-threat-detection"></a>記錄與威脅偵測

*如需詳細資料，請參閱 [Azure 安全性效能評定：稽核與威脅偵測](/azure/security/benchmarks/security-controls-v2-data-protection)。*

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2：啟用 Azure 身分識別和存取管理的威脅偵測

**指導** 方針： Azure 防禦整合 Azure Active Directory (Azure AD) ，而且可透過 Azure 入口網站存取服務。 依預設，系統會透過 Azure 活動記錄檔來捕獲服務 (的管理動作，例如建立、更新和刪除) 。 使用者也應該啟用 Azure 防禦資源記錄，例如會話 BastionAuditLogs 來追蹤防禦會話。

Azure AD 提供下列使用者記錄檔，可在 Azure AD 報告中查看或與 Azure 監視器、Azure Sentinel 或其他 SIEM/監視工具整合，以取得更精密的監視和分析使用案例： 
-   登入 – 登入報告會提供受控應用程式和使用者登入活動的使用情況相關資訊。

-   稽核記錄 - 可針對各種功能在 Azure AD 內進行的所有變更，提供記錄追蹤功能。 稽核記錄的範例包括對 Azure AD 中任何資源所做的變更，像是新增或移除使用者、應用程式、群組、角色和原則。

-   有風險的登入 - 有風險的登入表示非使用者帳戶合法擁有者的某人嘗試登入。

-   標幟為有風險的使用者 - 有風險的使用者表示可能被盜用的使用者帳戶。

Azure 資訊安全中心也可能會對某些可疑活動發出警示，例如驗證嘗試失敗的次數過多，以及訂用帳戶中已淘汰的帳戶。 除了基本的安全性防護監視之外，Azure 資訊安全中心的威脅防護模組也可以從個別的 Azure 計算資源收集更深入的安全性警示 (例如虛擬機器、容器、app service) 、資料資源 (例如 SQL DB 和儲存體) ，以及 Azure 服務層。 這項功能可讓您查看個別資源內的帳戶異常。

- [Azure 防禦資源記錄](diagnostic-logs.md)

- [Azure AD 中的審核活動報告](../active-directory/reports-monitoring/concept-audit-logs.md)

- [啟用 Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure 資訊安全中心內的威脅防護](/azure/security-center/threat-protection)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3：啟用 Azure 網路活動的記錄功能

**指導** 方針：啟用 Azure 防禦資源記錄，使用這些診斷記錄來查看哪些使用者連接到哪些工作負載、哪些時間、從何處，以及其他這類相關記錄資訊。 使用者可以設定將這些記錄傳送至儲存體帳戶，以進行長期保留和審核。

啟用並收集網路安全性群組 (NSG) 資源記錄，以及套用至您已部署 Azure 防禦資源之虛擬網路的網路安全性群組上的 NSG 流量記錄。 這些記錄可用於分析網路安全性，以及支援事件調查、威脅搜尋和安全性警示產生。 您可以將流量記錄傳送至 Azure 監視器 Log Analytics 工作區，然後使用流量分析來提供見解。

- [啟用及使用 Azure 防禦記錄](diagnostic-logs.md)

- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure 防火牆記錄和計量](../firewall/logs-and-metrics.md)

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md)

- [使用網路監看員進行監視](../network-watcher/network-watcher-monitoring-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4：啟用 Azure 資源的記錄

**指導** 方針：自動提供的活動記錄會包含 Azure 防禦資源的所有寫入作業 (PUT、POST、DELETE) ，但讀取作業 (取得) 。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

- [啟用 Azure 防禦的 Azure 資源記錄 ](diagnostic-logs.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5：集中安全性記錄檔管理和分析

**指導** 方針：集中記錄儲存體和分析以啟用相互關聯。 針對每個記錄來源，請確定您已指派資料擁有者、存取指引、儲存位置、使用哪些工具來處理和存取資料，以及資料保留需求。

確定您要將 Azure 活動記錄整合到您的集中記錄。 透過 Azure 監視器內嵌記錄，以匯總端點裝置、網路資源和其他安全性系統所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

此外，請啟用資料並將其上架至 Azure Sentinel 或協力廠商 SIEM。

許多組織選擇使用 Azure Sentinel 「經常性存取」資料，而這些資料經常使用，並 Azure 儲存體使用較不常使用的「冷」資料。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="lt-6-configure-log-storage-retention"></a>LT-6：設定記錄儲存體保留期

**指導** 方針：確定用來儲存 Azure 防禦記錄的任何儲存體帳戶或 log Analytics 工作區，都已根據您組織的合規性法規設定記錄保留期限。

在 Azure 監視器中，您可以根據組織的合規性法規來設定 Log Analytics 工作區保留期限。

- [如何設定 Log Analytics 工作區保留期限](../azure-monitor/platform/manage-cost-storage.md)

- [在 Azure 儲存體帳戶中儲存資源記錄](/azure/azure-monitor/platform/resource-logs-collect-storage)

- [啟用及使用 Azure Bastion 記錄](diagnostic-logs.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

[如需詳細資料，請參閱 *Azure 安全性效能評定：* 事件回應](/azure/security/benchmarks/security-controls-v2-incident-response) (機器翻譯)。

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1：準備 – 更新 Azure 的事件回應程序

**指引**：確定您的組織具有回應安全性事件的程序、已針對 Azure 更新這些程序，且會定期執行以確保整備程度。

- [在企業環境中實作安全性](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [事件回應參考指南](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2：準備 – 設定事件通知

**指引**：在 Azure 資訊安全中心設定安全性事件連絡資訊。 如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 就會使用這項連絡資訊與您聯繫。 您也可以選擇根據事件回應需求，在不同的 Azure 服務中自訂事件警示和通知。 

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3：偵測和分析 – 根據高品質警示建立事件

**指導** 方針：確定您有建立高品質警示和測量警示品質的流程。 這可讓您從過去的事件中汲取經驗，並且為分析師設定警示的優先順序，以避免浪費時間處理誤報。

高品質的警示可以根據過去事件的體驗、驗證的社區來源，以及設計來藉由融合和相互關聯各種信號來源來產生和清除警示的工具來建立。 

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

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5：偵測和分析 – 設定事件的優先順序

**指引**：根據警示嚴重性和資產敏感度，為分析師提供應優先關注哪些事件的內容。 

Azure 資訊安全中心會指派每個警示的嚴重性，以協助您設定應優先調查哪些警示。 嚴重性是以「資訊安全中心」在「尋找」或用來發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

此外，請使用標籤來標示資源，並建立命名系統以識別及分類 Azure 資源，尤其是處理敏感資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6：內含項目、根除和復原 – 將事件處理自動化

**指引**：將手動的重複工作自動化，以縮短回應時間並減輕分析師的負擔。 手動工作需要較長的時間來執行，因此會拖慢每個事件的處理速度，並減少分析師可處理的事件數目。 手動工作也會使分析師更加疲勞，而增加人為錯誤導致延遲的風險，並降低分析師有效專注處理複雜工作的能力。 請使用 Azure 資訊安全中心和 Azure Sentinel 中的工作流程自動化功能來自動觸發動作，或執行劇本以回應傳入的安全性警示。 劇本會採取動作，例如傳送通知、停用帳戶，以及隔離有問題的網路。 

- [在資訊安全中心設定工作流程自動化](../security-center/workflow-automation.md)

- [在 Azure 資訊安全中心設定自動化威脅回應](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [在 Azure Sentinel 中設定自動化威脅回應](../sentinel/tutorial-respond-threats-playbook.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="posture-and-vulnerability-management"></a>態勢和弱點管理

*如需詳細資料，請參閱 [Azure 安全性效能評定：態勢和弱點管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)。*

### <a name="pv-1-establish-secure-configurations-for-azure-services"></a>PV-1：建立 Azure 服務的安全設定 

**指導** 方針：使用 Azure 原則定義和實行適用于 Azure 防禦的標準安全性設定。 使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure 防禦的網路設定。 客戶也可以利用 Azure 藍圖或 ARM 範本來建立安全的設定，以安全且一致的方式部署防禦資源。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [深入瞭解 ARM 範本](../azure-resource-manager/templates/overview.md)

- [Azure 藍圖總覽](../governance/blueprints/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-2-sustain-secure-configurations-for-azure-services"></a>PV-2：維持 Azure 服務的安全設定

**指導** 方針：使用 Azure 原則定義和實行適用于 Azure 防禦的標準安全性設定。 使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行防禦資源的網路設定。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8：執行常見攻擊的模擬

**指引**：視需要對您的 Azure 資源執行滲透測試或 Red Team 活動，並確實補救所有已發現的重大安全漏洞。

請遵循 Microsoft 雲端滲透測試參與規則，以確保您的滲透測試不會違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行方法。

- [Azure 中的滲透測試](../security/fundamentals/pen-testing.md)

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="governance-and-strategy"></a>控管與策略

*如需詳細資料，請參閱 [Azure 安全性效能評定：控管與策略](/azure/security/benchmarks/security-controls-v2-governance-strategy)。*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1：定義資產管理和資料保護策略 

**指引**：確實記載並傳達明確的策略，以持續監視和保護系統和資料。 設定探索、評估、保護及監視業務關鍵資料和系統的優先順序。 

此策略應包含針對下列元素而記載的指引、原則和標準： 

-   符合商務風險的資料分類標準

-   安全性組織對風險和資產清查的可見度 

-   安全性組織核准使用 Azure 服務的程序 

-   資產在其生命週期內的安全性

-   符合組織資料分類的必要存取控制策略

-   使用 Azure 原生和協力廠商資料保護功能

-   傳輸中和待用使用案例的資料加密需求

-   適當的密碼編譯標準

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性架構建議 - 儲存體、資料和加密](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure 安全性基本要素 - Azure 資料安全性、加密和儲存體](../security/fundamentals/encryption-overview.md)

- [雲端採用架構 - Azure 資料安全性和加密最佳做法](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure 安全性效能評定 - 資產管理](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Azure 安全性效能評定 - 資料保護](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2：定義企業分割策略 

**指引**：使用身分識別、網路、應用程式、訂用帳戶、管理群組和其他控制項的組合，建立對資產的存取進行分割的企業範圍策略。

審慎權衡安全性區隔的需求，以及為需要與彼此通訊及存取資料的系統啟用日常作業的需求。

請確實在各種控制項類型間一致地實作分割策略，包括網路安全性、身分識別和存取模型、應用程式權限/存取模型，以及手動程序等控制項。

- [Azure 中的分割策略指引 (影片)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Azure 中的分割策略指引 (文件)](/security/compass/governance#enterprise-segmentation-strategy)

- [根據企業分割策略調整網路分割](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3：定義安全性態勢管理策略

**指引**：持續測量並降低個別資產及其裝載環境的風險。 優先處理高價值資產和高度暴露的攻擊面，例如已發佈的應用程式、網路輸入點和輸出點、使用者和系統管理員端點等等。

- [Azure 安全性效能評定 - 態勢和弱點管理](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4：調整組織角色、責任和權責

**指引**：確實清楚記載角色和責任的策略，並在您的安全性組織中加以傳達。 優先為安全性決策提供明確的權責劃分、讓每個人清楚認知共同責任模式，並且讓技術小組熟知保護雲端的技術。

- [Azure 安全性最佳做法 1 – 人員：讓小組熟知雲端安全性旅程](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure 安全性最佳做法 2 - 人員：讓小組熟知雲端安全性技術](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure 安全性最佳做法 3 - 程序：指派雲端安全性決策的權責](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-5-define-network-security-strategy"></a>GS-5：定義網路安全性策略

**指引**：建立 Azure 網路安全性方法，作為組織整體安全性存取控制策略的一部分。  

此策略應包含針對下列元素而記載的指引、原則和標準： 

-   集中式網路管理和安全性責任

-   與企業分割策略一致的虛擬網路分割模型

-   不同的威脅和攻擊案例適用的補救策略

-   網際網路邊緣以及輸入與輸出策略

-   混合式雲端和內部部署互連策略

-   最新的網路安全性成品 (例如網路圖、參考網路架構)

如需詳細資訊，請參閱下列參考資料：
- [Azure 安全性最佳做法 11 - 結構。單一整合安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 安全性效能評定 - 網路安全性](/azure/security/benchmarks/security-controls-v2-network-security)

- [Azure 網路安全性概觀](../security/fundamentals/network-overview.md)

- [企業網路架構策略](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6：定義身分識別和特殊權限存取策略

**指引**：建立 Azure 身分識別和特殊權限存取方法，作為組織整體安全性存取控制策略的一部分。  

此策略應包含針對下列元素而記載的指引、原則和標準： 

-   集中式身分識別和驗證系統，以及其與其他內部和外部身分識別系統的互連能力

-   不同的使用案例與情況適用的增強式驗證方法

-   高權限使用者的保護

-   異常使用者活動的監視和處理  

-   使用者身分識別和存取權的檢閱和調整程序

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 身分識別管理](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure 安全性效能評定 - 特殊權限存取](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure 安全性最佳做法 11 - 結構。單一整合安全性策略](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure 身分識別管理安全性概觀](../security/fundamentals/identity-management-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7：定義記錄和威脅回應策略

**指引**：建立記錄和威脅回應策略，以快速偵測並補救威脅，同時符合合規性需求。 排定為分析師提供高品質的警示和順暢的體驗，讓他們可以專注于威脅，而不是整合和手動步驟。 

此策略應包含針對下列元素而記載的指引、原則和標準： 

-   安全性作業 (SecOps) 組織的角色和責任 

-   妥善定義且與 NIST 或其他產業架構一致的事件回應程序 

-   記錄擷取和保留，以支援威脅偵測、事件回應和合規性需求

-   使用 SIEM、原生 Azure 功能及其他來源，集中顯示和相互關聯威脅的相關資訊 

-   與您的客戶、供應商和相關的公開合作對象之間的溝通和通知計畫

-   使用 Azure 原生和第三方平台進行事件處理，例如記錄和威脅偵測、鑑識，以及攻擊的補救與根除

-   處理事件和事後活動的程序，例如汲取的經驗和辨識項保留

如需詳細資訊，請參閱下列參考資料：

- [Azure 安全性效能評定 - 記錄和威脅偵測](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure 安全性效能評定 - 事件回應](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure 安全性最佳做法 4 - 程序。更新雲端的事件回應程序](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure 採用架構、記錄和報告決策指南](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure 企業規模、管理和監視](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定 V2 概觀](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
