---
title: Azure 安全性基準測試 V2-特殊許可權存取
description: Azure 安全性基準測試 V2 特殊許可權存取
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f6c990b3ce5edaab4d2ce6600c0291272058e092
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369288"
---
# <a name="security-control-v2-privileged-access"></a>安全性控制 V2：特殊許可權存取

特殊許可權存取涵蓋的控制項可保護您的 Azure 租使用者和資源的特殊許可權存取。 這包括一系列控制項，可保護您的系統管理模型、系統管理帳戶和特殊許可權的存取工作站免于刻意和不慎的風險。

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1：保護及限制高權限使用者

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-1 | 4.3、4。8 | AC-2 |

限制高許可權使用者帳戶的數目，並在提高許可權的層級保護這些帳戶。 Azure AD 中最重要的內建角色是全域管理員和特殊許可權角色管理員，因為指派給這兩個角色的使用者可以委派系統管理員角色。 使用這些許可權，使用者可以直接或間接讀取和修改 Azure 環境中的每個資源：

- 全域管理員/公司系統管理員：具備此角色的使用者可以存取 Azure AD 中的所有管理功能，以及使用 Azure AD 身分識別的服務。

- 特殊許可權角色管理員：具備此角色的使用者可以在 Azure AD 中管理角色指派，也可以在 Azure AD Privileged Identity Management (PIM) 內管理。 此外，此角色可讓您管理 PIM 和管理單位的所有層面。

注意：如果您使用自訂角色並指派某些特殊許可權許可權，您可能會有其他重要角色需要進行控管。 此外，您也可能想要將類似的控制項套用至重要商務資產的系統管理員帳戶。  

您可以使用 Azure AD Privileged Identity Management (PIM)，啟用對 Azure 資源和 Azure AD 的 Just-In-Time (JIT) 特殊權限存取。 JIT 只有在使用者需要時，才會授與暫時性權限以執行特殊權限的工作。 當您的 Azure AD 組織中有可疑或不安全的活動時，PIM 也會產生安全性警示。

- [Azure AD 中的系統管理員角色權限](../../active-directory/roles/permissions-reference.md)

- [使用 Azure Privileged Identity Management 安全性警示](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../../active-directory/roles/security-planning.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全性作業](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2：限制對業務關鍵系統的系統管理存取

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-2 | 13.2、2.10 | AC-2，SC-3，SC-7 |

藉由限制哪些帳戶被授與存取權給他們所在的訂用帳戶和管理群組，來隔離對商務關鍵性系統的存取。 確定您也會限制存取管理、身分識別和安全性系統，這些系統具有業務關鍵資產的系統管理存取權，例如 Active Directory 網網域控制站 (Dc) 、安全性工具和系統管理工具，以及安裝在商務關鍵性系統上的代理程式。 入侵這些管理和安全性系統的攻擊者，可以立即 weaponize 它們來入侵商務關鍵資產。 

所有類型的存取控制都應符合您的企業分割策略，以確保存取控制的一致性。 

請務必指派不同于電子郵件、流覽和產能工作所用標準使用者帳戶的不同特殊許可權帳戶。

- [Azure 元件和參考模型](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理群組存取](../../governance/management-groups/overview.md#management-group-access)

- [Azure 訂用帳戶管理員](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全性架構](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3：定期檢閱及協調使用者存取

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-3 | 4.1、16.9、16.10 | AC-2 |

定期審核使用者帳戶和存取指派，以確保帳戶及其存取層級都有效。 您可以使用 Azure AD 存取評論來查看群組成員資格、企業應用程式的存取權，以及角色指派。 Azure AD 報告可以提供記錄以協助探索過時的帳戶。 您也可以使用 Azure AD Privileged Identity Management 建立存取權審核報表工作流程，以協助審核程式。
此外，Azure Privileged Identity Management 可以設定為在建立過多的系統管理員帳戶時發出警示，以及識別已過時或設定不正確的系統管理員帳戶。 

注意：某些 Azure 服務支援本機使用者和不是透過 Azure AD 管理的角色。 您必須分別管理這些使用者。

- [在 Privileged Identity Management (PIM) 中建立 Azure 資源角色的存取權審核 ](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [如何使用 Azure AD 身分識別和存取權檢閱](../../active-directory/governance/access-reviews-overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4：在 Azure AD 中設定緊急存取

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-4 | 16 | AC-2，CP-2 |

若要避免不小心遭到 Azure AD 的組織封鎖，請在無法使用一般系統管理帳戶時，設定緊急存取帳戶以進行存取。 緊急存取帳戶通常具有高權限，不應將其指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的緊急或「急用」狀況。
您應該確保緊急存取帳戶的認證 (例如密碼、憑證或智慧卡) 受到保護，而且只有在緊急情況下有權使用這些認證的個人才會知道。

- [在 Azure AD 中管理緊急存取帳戶](../../active-directory/roles/security-emergency-access.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全性作業 (SecOps) ](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5：自動化權利管理

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-5 | 16 | AC-2、AC-5、PM-10 |

使用 Azure AD 權利管理功能來自動化存取要求工作流程，包括存取權指派、評論和到期日。 也支援雙重或多階段核准。
- [什麼是 Azure AD 存取權評論](../../active-directory/governance/access-reviews-overview.md) 

- [什麼是 Azure AD 權利管理](../../active-directory/governance/entitlement-management-overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6：使用特殊權限存取工作站

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-6 | 4.6、11.6、12.12 | AC-2，SC-3，SC-7 |

安全、隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 使用高度安全的使用者工作站及/或 Azure 防禦來進行系統管理工作。 請使用 Azure Active Directory、Microsoft Defender 進階威脅防護 (ATP) 和/或 Microsoft Intune，以部署安全且受控的使用者工作站來進行系統管理工作。 受保護的工作站可以集中管理以強制執行安全的設定，包括強式驗證、軟體和硬體基準，以及受限的邏輯和網路存取。 

- [瞭解特殊許可權的存取工作站](../../active-directory/devices/concept-azure-managed-workstation.md)

- [部署特殊權限存取工作站](../../active-directory/devices/howto-azure-managed-workstation.md) (機器翻譯)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性作業 (SecOps) ](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7：遵循恰到好處的系統管理 (最低權限原則)

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-7 | 14.6 | AC-2、AC-3、SC-3 |

Azure 角色型存取控制 (Azure RBAC) 可讓您透過角色指派來管理 Azure 資源存取權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，這些角色可透過 Azure CLI、Azure PowerShell 和 Azure 入口網站等工具進行清查或查詢。 透過 Azure RBAC 指派給資源的許可權應該一律限制為角色所需的許可權。 有限許可權可 (JIT) Azure AD Privileged Identity Management (PIM) 的方法，而這些許可權應定期檢查。
請使用內建角色來配置權限，且僅在必要時才建立自訂角色。 

- [什麼是 Azure 角色型存取控制 (Azure RBAC) ](../../role-based-access-control/overview.md)

- [如何設定 Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

- [如何使用 Azure AD 身分識別和存取權檢閱](../../active-directory/governance/access-reviews-overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)    

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8：選擇 Microsoft 支援的核准流程 

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP 800-53 r4 識別碼 (s)  |
|--|--|--|--|
| PA-8 | 16 | AC-2、AC-3、AC-4 |

在 Microsoft 需要存取客戶資料的支援案例中，客戶加密箱提供一項功能，讓您明確地審核和核准或拒絕每個客戶資料存取要求。

- [瞭解客戶加密箱](../fundamentals/customer-lockbox-overview.md)

**責任**：客戶

**客戶安全性專案關係人** ([深入瞭解](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)) ：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [身分識別與金鑰管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

