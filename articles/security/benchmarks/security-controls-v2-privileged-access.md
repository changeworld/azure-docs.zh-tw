---
title: Azure 安全性基準測試 V2-特殊許可權存取
description: Azure 安全性基準測試 V2 特殊許可權存取
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8727cbd07fad1960f4bdb33742729b6bda3a369e
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059233"
---
# <a name="security-control-privileged-access"></a>安全性控制：特殊許可權存取

特殊許可權存取涵蓋的控制項可保護您的 Azure 租使用者和資源的特殊許可權存取。 這包括一系列控制項，可保護您的系統管理模型、系統管理帳戶和特殊許可權的存取工作站免于刻意和不慎的風險。

## <a name="pa-1-protect-and-limit-the-global-administrators"></a>PA-1：保護和限制全域管理員

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-1 | 4.3、4。8 | AC-2 |

獲指派全域管理員角色的使用者可以讀取及修改 Azure AD 組織中的每個系統管理設定。 將每個訂用帳戶的 Azure 全域管理員帳戶數目限制為不超過兩個。 Azure AD 中最重要的內建角色是全域管理員和特殊許可權角色管理員，因為指派給這兩個角色的使用者可以委派系統管理員角色：
- 全域管理員/公司系統管理員：具備此角色的使用者可以存取 Azure AD 中的所有管理功能，以及使用 Azure AD 身分識別的服務。

- 特殊許可權角色管理員：具備此角色的使用者可以在 Azure AD 中管理角色指派，也可以在 Azure AD Privileged Identity Management (PIM) 內管理。 此外，此角色可讓您管理 PIM 和管理單位的所有層面。

注意：如果您使用自訂角色並指派某些特殊許可權許可權，您可能會有其他重要角色需要進行控管。 此外，您也可能想要將類似的控制項套用至重要商務資產的系統管理員帳戶。  

您可以使用 Azure AD Privileged Identity Management (PIM) ，啟用對 Azure 資源和 Azure AD 的即時 (JIT) 特殊許可權存取。 JIT 只會在使用者需要時授與暫時許可權，以執行特殊許可權的工作。 當您的 Azure AD 組織中有可疑或不安全的活動時，PIM 也可以產生安全性警示。

注意：某些 Azure 服務（例如 Azure SQL）除了 Azure AD authentication 之外，還支援本機使用者驗證。 這種類型的本機使用者驗證不會透過 Azure AD 管理。 您將需要分開管理這些使用者。

- [Azure AD 中的系統管理員角色許可權](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)

- [Azure 自訂角色](../../role-based-access-control/custom-roles.md) (機器翻譯)

- [使用 Azure Privileged Identity Management 安全性警示](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [使用 Azure 資訊安全中心來監視身分識別和存取](../../security-center/security-center-identity-access.md)

**責任**：客戶

**客戶安全性專案關係人**：

- [身分識別和金鑰](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="pa-2-review-and-reconcile-user-access-regularly"></a>PA-2：定期審核和協調使用者存取

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-2 | 4.1、16.9、16.10 | AC-2 |

定期審核使用者帳戶和存取權利，以確保使用者帳戶及其存取權都有效。 

使用 Azure AD 身分識別和存取評論來管理群組成員資格、企業應用程式的存取權，以及角色指派。 Azure AD 報告可以提供記錄以協助探索過時的帳戶。 您也可以使用 Azure AD Privileged Identity Management 建立存取權審核報表工作流程，以加速審核程式。

針對 Azure 服務和工作負載層級的系統管理使用者，應該進行更頻繁的使用者和存取權審核。 您也可以使用 Azure Privileged Identity Management 安全性警示來偵測系統管理員帳戶的建立時間，以及尋找已過時或設定不正確的系統管理員帳戶。 

注意：某些 Azure 服務（例如 Azure SQL）支援不是透過 Azure AD 管理的本機使用者。 您將需要分開管理這些使用者。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [使用 Azure 資訊安全中心來監視身分識別和存取](../../security-center/security-center-identity-access.md)

- [使用 Azure Privileged Identity Management 安全性警示](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [跨小組調整管理責任](/azure/cloud-adoption-framework/organize/raci-alignment) 

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure AD 身分識別和存取權評論](../../active-directory/governance/access-reviews-overview.md)

- [Privileged Identity Management 審查 Azure AD 角色的存取權](../../active-directory/privileged-identity-management/pim-how-to-start-security-review.md)

- [Azure 資訊安全中心-監視身分識別和存取](../../security-center/security-center-identity-access.md)

**責任**：客戶

**客戶安全性專案關係人**：

- [身分識別和金鑰](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-3-set-up-an-emergency-access-account-in-azure-ad"></a>PA-3：在 Azure AD 中設定緊急存取帳戶

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-3 | 12.3 | AC-2 |

若要避免不小心遭到 Azure AD 的組織封鎖，請在無法使用一般系統管理帳戶時，設定緊急存取帳戶以進行存取。 緊急存取帳戶通常具有高度許可權，不應指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的緊急或「急用」狀況。

您應該確保緊急存取帳戶的認證 (（例如密碼、憑證或智慧卡) ）保持安全，而且只有獲授權可在緊急情況下使用這些認證的人員才知道。

- [在 Azure AD 中管理緊急存取帳戶](../../active-directory/users-groups-roles/directory-emergency-access.md)

**責任**：客戶

**客戶安全性專案關係人**：

- [身分識別和金鑰](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [安全性作業中心 (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-4-automate-azure-identity-and-access-request-workflow"></a>PA-4：自動化 Azure 身分識別和存取要求工作流程

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-4 | N/A | AC-2、AC-5、PM-10 |

使用 Azure AD 權利管理功能來自動化 Azure 存取要求工作流程，包括存取指派、評論和到期日。 也支援雙重或多階段核准。  

- [什麼是 Azure AD 權利管理](../../active-directory/governance/entitlement-management-overview.md)

- [如何設定存取要求和核准流程](../../active-directory/governance/entitlement-management-access-package-request-policy.md)

**責任**：客戶

**客戶安全性專案關係人**：

- [身分識別和金鑰](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-5-use-highly-secured-machines-for-administrative-tasks"></a>PA-5：使用高度安全的電腦進行系統管理工作

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-5 | 4.6、11.6、12.12 | AC-2，SC-7 |

安全、隔離的工作站對於機密角色（例如系統管理員、開發人員和重要服務操作員）的安全性至關重要。 使用高度安全的使用者工作站及/或 Azure 防禦來進行系統管理工作：
- 使用 Azure Active Directory、Microsoft Defender 進階威脅防護 (ATP) 和/或 Microsoft Intune 部署安全且受管理的使用者工作站以進行系統管理工作。 受保護的工作站可以集中管理以強制執行安全的設定，包括強式驗證、軟體和硬體基準、受限的邏輯和網路存取。 

- 使用 Azure 防禦功能來取得安全的路徑，以透過 RDP 或 SSH 存取您的虛擬機器。 Azure 防禦是完全受控的 PaaS 服務，可為每個虛擬網路布建，而不需建立新的虛擬網路。 

- [瞭解安全、受 Azure 管理的工作站](../../active-directory/devices/concept-azure-managed-workstation.md)

- [部署安全的 Azure 管理工作站](../../active-directory/devices/howto-azure-managed-workstation.md)

- [使用 Azure 堡壘主機](../../bastion/bastion-create-host-portal.md)

**責任**：客戶

**客戶安全性專案關係人**：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性作業中心 (SOC)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [身分識別和金鑰](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-6-assign-privileges-to-resources-using-azure-rbac"></a>PA-6：使用 Azure RBAC 將許可權指派給資源

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-6 | 14.6 | AC-2、AC-3 |

Azure 角色型存取控制 (RBAC) 可讓您透過角色指派來管理 Azure 資源存取權的許可權。 您可以將這些角色指派給使用者、群組服務主體和受控識別。 某些資源有預先定義的內建角色，而這些角色可透過 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具進行清查或查詢。 

- [什麼是 Azure 角色型存取控制 (Azure RBAC) ](../../role-based-access-control/overview.md)

- [如何在 Azure 中設定 RBAC](../../role-based-access-control/role-assignments-portal.md)

**責任**：客戶

**客戶安全性專案關係人**：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [狀態管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [身分識別和金鑰](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-choose-approval-process-for-microsoft-support"></a>PA-7：選擇 Microsoft 支援的核准流程

| Azure 識別碼 | CIS 控制7.1 識別碼 (s)  | NIST SP800-53 r4 ID (s)  |
|--|--|--|--|
| PA-7 | 16 | AC-2、AC-3、AC-4 |

在 Microsoft 需要存取客戶資料的支援案例中，客戶加密箱提供一項功能，讓您明確地審核和核准或拒絕每個客戶資料存取要求。

- [瞭解客戶加密箱](../fundamentals/customer-lockbox-overview.md)

**責任**：客戶

**客戶安全性專案關係人**：

- [應用程式安全性和 DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [安全性合規性管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [身分識別和金鑰](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

