---
title: HIPAA HITRUST 9.2 的法規合規性詳細資料
description: HIPAA HITRUST 9.2 法規合規性內建方案的詳細資料。 每個控制措施都會對應至一或多個可協助評量的 Azure 原則定義。
ms.date: 01/25/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: bbec4e1ea72f458557281f76b5ca5178bce2c316
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805592"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>HIPAA HITRUST 9.2 法規合規性內建方案的詳細資料

下列文章詳細說明 Azure 原則法規合規性內建方案定義如何對應至 HIPAA HITRUST 9.2 中的 **合規性領域** 與 **控制項**。
如需此合規性標準的詳細資訊，請參閱 [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html)。 若要了解 _所有權_，請參閱 [Azure 原則原則定義](../concepts/definition-structure.md#type)與 [雲端中共同承擔的責任](../../../security/fundamentals/shared-responsibility.md)。

以下是 **HIPAA HITRUST 9.2** 控制項的對應。 使用右側的導覽區可直接跳到特定的 **合規性領域**。 許多控制措施都是以 [Azure 原則](../overview.md)方案定義實作的。 若要檢閱完整方案定義，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。
然後，尋找並選取 **HITRUST/HIPAA** 法規合規性內建方案的定義。

此內建方案會部署為 [HIPAA HITRUST 9.2 藍圖範例](../../blueprints/samples/hipaa-hitrust-9-2.md)的一部分。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../overview.md)定義相關聯。
> 這些原則可協助您使用工具[存取合規性](../how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是一對一或完整對應。 因此，Azure 原則中的 **符合規範** 只是指原則定義本身，這不保證您完全符合所有控制措施需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性標準的合規性領域、控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json) \(英文\)。

## <a name="privilege-management"></a>權限管理

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>針對裝載虛擬化系統的系統，其管理功能或管理主控台的存取權會依據最低權限原則來有限制地提供給個人，並透過技術控制項提供支援。

**識別碼**：11180.01c3System.6 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>權限會經過正式授權和控制，並根據使用者功能角色 (例如使用者或系統管理員) 的使用需求及個別事件，將權限配置給使用者，然後針對每個系統產品/元素加以記錄。

**識別碼**：1143.01c1System.123 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應關閉虛擬機器上的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |開放的遠端管理連接埠會使您的 VM 暴露在網際網路攻擊的高風險之下。 這些攻擊會嘗試對認證發動暴力密碼破解攻擊，來取得機器的系統管理員存取權。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>組織會明確授與指定安全性相關功能 (部署在硬體、軟體和韌體中) 和安全性相關資訊的存取權。

**識別碼**：1144.01c1System.4 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對您的訂用帳戶指定最多 3 位擁有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建議您最多指定 3 位訂用帳戶擁有者，以降低遭入侵擁有者導致資料外洩的可能。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>實作角色型存取控制，以將每個使用者對應至一個或多個角色，以及將每個角色對應至一個或多個系統功能。

**識別碼**：1145.01c2System.1 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應將一個以上的擁有者指派給您的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建議指定多位訂用帳戶擁有者，如此才能設定系統管理員存取備援。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>組織會升級程式的開發和使用，避免需要以較高的權限和系統常式來執行，進而避免將權限授與使用者的需求。

**識別碼**：1146.01c2System.23 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |具有擁有者權限的外部帳戶應從您訂用帳戶移除，以避免未受監視的存取。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>較高的權限會指派給用於一般商務用途的不同使用者識別碼，所有使用者都會以單一角色存取具特殊權限的服務，而這類特殊權限的存取權都僅具有最低權限。

**識別碼**：1147.01c2System.456 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除。  已取代帳戶是已封鎖而無法登入的帳戶。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>組織會限制特殊權限功能和所有安全性相關資訊的存取。

**識別碼**：1148.01c2System.78 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核自訂 RBAC 規則的使用方式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |稽核內建角色，例如「擁有者、參與者、讀取者」，而不是自訂的 RBAC 角色，這些角色容易發生錯誤。 使用自訂角色會視為例外狀況，而且需要嚴格審查和威脅模型化 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Windows 電腦應符合「安全性選項 - 帳戶」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Windows 電腦的 [安全性選項 - 帳戶] 類別中應具有指定群組原則設定，以限制空白密碼和來賓帳戶狀態下的本機帳戶使用方式。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>當組織所定義的內容允許使用者具有自行處理權時，組織會藉由讓授權的使用者決定企業合作夥伴的存取權，來促進檔案共用，並採用手動程序或自動化機制來協助使用者做出資訊共用/共同作業的決策。

**識別碼**：1149.01c2System.9 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |若要提供使用者可執行之動作的精細篩選，請使用角色型存取控制 (RBAC) 來管理 Kubernetes Service 叢集中的權限，並設定相關的授權原則。 |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>使用預設的 &quot;deny-all&quot; 設定來為儲存、處理或傳輸涵蓋資訊的系統元件設定存取控制系統。

**識別碼**：1150.01c2System.10 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應關閉虛擬機器上的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |開放的遠端管理連接埠會使您的 VM 暴露在網際網路攻擊的高風險之下。 這些攻擊會嘗試對認證發動暴力密碼破解攻擊，來取得機器的系統管理員存取權。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>組織會限制只有預先定義的使用者子集，可以具有資訊系統上特殊權限帳戶的授權。

**識別碼**：1151.01c3System.1 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對您的訂用帳戶指定最多 3 位擁有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建議您最多指定 3 位訂用帳戶擁有者，以降低遭入侵擁有者導致資料外洩的可能。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>組織會稽核資訊系統上特殊權限功能的執行，並確保資訊系統可防止未經授權的使用者執行特殊權限功能。

**識別碼**：1152.01c3System.2 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應將一個以上的擁有者指派給您的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建議指定多位訂用帳戶擁有者，如此才能設定系統管理員存取備援。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>所有未明確要求的檔案系統存取都會停用，只有授權使用者能夠存取使用者履行工作職責時明確要求的項目。

**識別碼**：1153.01c3System.35 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |若要提供使用者可執行之動作的精細篩選，請使用角色型存取控制 (RBAC) 來管理 Kubernetes Service 叢集中的權限，並設定相關的授權原則。 |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>只有在組織評估約聘人員的能力符合其安全性需求，且約聘人員也同意遵循時，才會提供最低限度的系統和實體存取權給約聘人員。

**識別碼**：1154.01c3System.4 - 01.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對您的訂用帳戶指定最多 3 位擁有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建議您最多指定 3 位訂用帳戶擁有者，以降低遭入侵擁有者導致資料外洩的可能。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>用於外部連線的使用者驗證

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>在組織網路的所有外部連線上都會實作強式驗證方法，例如多重要素、Radius 或 Kerberos (適用於特殊權限存取) 和 CHAP (用於加密撥號方法所用的認證)。

**識別碼**：1116.01j1Organizational.145 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>當廠商或商務合作夥伴不使用遠端存取 (例如要進行遠端維修) 時，停用遠端存取。

**識別碼**：1117.01j1Organizational.23 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>組織會實作加密作業 (例如 &nbsp;VPN 解決方案或私人線路)，並記錄員工、約聘人員或協力廠商對組織網路的遠端存取。

**識別碼**：1118.01j2Organizational.124 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>檢查網路設備是否有非預期的撥號功能。

**識別碼**：1119.01j2Organizational.3 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>遠端系統管理工作階段已獲授權、加密，並採用升級的安全性措施。

**識別碼**：1121.01j3Organizational.2 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>如果撥號連線未使用加密，則 CIO 或其指定的代表會提供特定書面授權。

**識別碼**：1173.01j1Organizational.6 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>針對包含敏感性資訊的系統，組織會藉由驗證使用者和裝置來保護系統的無線存取。

**識別碼**：1174.01j1Organizational.7 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>只有在成功識別和驗證之後，才能在公用網路上遠端存取商務資訊。

**識別碼**：1175.01j1Organizational.8 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>組織需要包含重新驗證的回呼功能，以驗證來自授權位置的撥號連線。

**識別碼**：1176.01j2Organizational.5 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>指派給廠商的使用者識別碼會根據組織的存取檢閱原則進行檢閱 (至少每年一次)。

**識別碼**：1177.01j2Organizational.6 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>節點驗證 (包括電腦憑證等密碼編譯技術) 可作為驗證遠端使用者群組的替代方法，讓使用者連線到安全的共用電腦設備。

**識別碼**：1178.01j2Organizational.7 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>資訊系統會監視並控制遠端存取方法。

**識別碼**：1179.01j3Organizational.1 - 01.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>遠端診斷和設定連接埠保護

### <a name="access-to-network-equipment-is-physically-protected"></a>對網路設備的存取會受到實際保護。

**識別碼**：1192.01l1Organizational.1 - 01.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>存取診斷和設定連接埠的控制措施包括使用鍵盤鎖定，以及實作可控制連接埠實際存取權的支援程序。

**識別碼**：1193.01l2Organizational.13 - 01.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應關閉虛擬機器上的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |開放的遠端管理連接埠會使您的 VM 暴露在網際網路攻擊的高風險之下。 這些攻擊會嘗試對認證發動暴力密碼破解攻擊，來取得機器的系統管理員存取權。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>安裝在電腦或網路系統上的連接埠、服務和類似的應用程式若不是商務功能的特定需求，則會停用或移除。

**識別碼**：1194.01l2Organizational.2 - 01.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應關閉 Web 應用程式的遠端偵錯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |遠端偵錯需要在 Web 應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>組織每三百六十五 (365) 天會檢查一次資訊系統，以識別並停用不必要和不安全的功能、連接埠、通訊協定和/或服務。

**識別碼**：1195.01l3Organizational.1 - 01.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[函式應用程式的遠端偵錯應關閉](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |遠端偵錯需要在函式應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>組織會識別資訊系統上未經授權 (已列入封鎖清單) 的軟體、根據未經授權 (已列入封鎖清單) 的軟體程式清單阻止程式執行、使用允許全部、拒絕例外原則來禁止執行已知的未經授權 (已列入封鎖清單) 軟體，以及每年檢查並更新未經授權 (已列入封鎖清單) 的軟體程式清單。

**識別碼**：1196.01l3Organizational.24 - 01.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應關閉 API 應用程式的遠端偵錯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |遠端偵錯需要在 API 應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>組織會停用資訊系統內判斷為不必要或不安全的藍牙和對等網路通訊協定。

**識別碼**：1197.01l3Organizational.3 - 01.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的電腦應啟用自適性應用程式控制，以定義安全應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |啟用應用程式控制項，以定義在您的電腦上執行的已知安全應用程式清單，並在其他應用程式執行時發出警示。 這有助於強化機器，以防範惡意程式碼的攻擊。 為了簡化設定和維護規則的程序，資訊安全中心使用機器學習來分析在每個機器上執行的應用程式，並建議已知安全的應用程式清單。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>網路中的隔離

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>組織的安全性閘道 (例如防火牆) 會強制執行安全性原則，並且會設定為可篩選網域之間的流量、封鎖未經授權的存取，以及用來維護內部有線、內部無線和外部網路部分 (例如網際網路) 之間的隔離 (包括 DMZ)，並且強制執行每個網域的存取控制原則。

**識別碼**：0805.01m1Organizational.12 - 01.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |此原則會稽核任何未設定為使用虛擬網路服務端點的 App Service。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Cosmos DB。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[事件中樞應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |此原則會稽核任何未設定為使用虛擬網路服務端點的事件中樞。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[閘道子網路不應設定網路安全性群組](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |若閘道子網路中設定了網路安全性群組，此原則將予以拒絕。 為閘道子網路指派網路安全性群組，將導致閘道停止運作。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Key Vault。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[服務匯流排應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |此原則會稽核任何未設定為使用虛擬網路服務端點的服務匯流排。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |此原則會稽核任何未設定為使用虛擬網路服務端點的 SQL Server。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[儲存體帳戶應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |此原則會稽核任何未設定為使用虛擬網路服務端點的儲存體帳戶。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>組織網路會透過定義的安全界限和一組分級的控制項，在邏輯及實體上進行分割，例如，可公開存取的系統元件子網路會根據組織需求，以邏輯方式與內部網路區隔；而流量則是根據風險評估及其各自的安全性需求，以所需的功能和資料/系統的分類為基礎來進行控制。

**識別碼**：0806.01m2Organizational.12356 - 01.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |此原則會稽核任何未設定為使用虛擬網路服務端點的 App Service。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Cosmos DB。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[事件中樞應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |此原則會稽核任何未設定為使用虛擬網路服務端點的事件中樞。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[閘道子網路不應設定網路安全性群組](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |若閘道子網路中設定了網路安全性群組，此原則將予以拒絕。 為閘道子網路指派網路安全性群組，將導致閘道停止運作。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Key Vault。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[服務匯流排應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |此原則會稽核任何未設定為使用虛擬網路服務端點的服務匯流排。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |此原則會稽核任何未設定為使用虛擬網路服務端點的 SQL Server。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[儲存體帳戶應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |此原則會稽核任何未設定為使用虛擬網路服務端點的儲存體帳戶。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>將實體伺服器、應用程式或資料遷移至虛擬化伺服器時，網路會與生產層級的網路隔離。

**識別碼**：0894.01m2Organizational.7 - 01.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |此原則會稽核任何未設定為使用虛擬網路服務端點的 App Service。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Cosmos DB。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[於虛擬網路建立時部署網路監看員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |此原則會在具有虛擬網路的區域中建立網路監看員資源。 您必須確定名為 networkWatcherRG 的資源群組是否存在，其將用來部署網路監看員執行個體。 |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[事件中樞應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |此原則會稽核任何未設定為使用虛擬網路服務端點的事件中樞。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[閘道子網路不應設定網路安全性群組](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |若閘道子網路中設定了網路安全性群組，此原則將予以拒絕。 為閘道子網路指派網路安全性群組，將導致閘道停止運作。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Key Vault。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[服務匯流排應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |此原則會稽核任何未設定為使用虛擬網路服務端點的服務匯流排。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |此原則會稽核任何未設定為使用虛擬網路服務端點的 SQL Server。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[儲存體帳戶應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |此原則會稽核任何未設定為使用虛擬網路服務端點的儲存體帳戶。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>網路連線控制

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>網路流量會根據組織的存取控制原則，透過每個網路存取點的防火牆和其他網路相關限制，或外部電信服務的受控介面來加以控制。

**識別碼**：0809.01n2Organizational.1234 - 01.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>資訊會在透過開放的公用網路傳輸時受到保護及進行最低程度的加密。

**識別碼**：0810.01n2Organizational.5 - 01.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>流量原則的例外狀況中會記載支援的任務/商務需求及例外狀況的持續時間，並至少每年檢查一次；當明確的任務/商務需求不再支援流量原則例外狀況時，則會將例外狀況移除。

**識別碼**：0811.01n2Organizational.6 - 01.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>建立非遠端連線的遠端裝置不得與外部 (遠端) 資源進行通訊。

**識別碼**：0812.01n2Organizational.8 - 01.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>根據存取控制原則和臨床和商務應用程式的需求，在受控介面上使用預設拒絕和允許例外的原則來限制使用者連線到內部網路的能力。

**識別碼**：0814.01n1Organizational.12 - 01.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[虛擬機器應該連線到已核准的虛擬網路](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |此原則會稽核任何連線到未核准之虛擬網路的虛擬機器。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>使用者識別與授權

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>組織會確保不會發行多餘的使用者識別碼給其他使用者，而且所有使用者都有唯一的識別和驗證方式，以用來存取本機和遠端的資訊系統。

**識別碼**：11109.01q1Organizational.57 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>非組織使用者 (組織使用者以外的所有資訊系統使用者，例如病人、客戶、約聘人員或外籍人士)，或是代表非組織使用者的程序 (已判斷其需要存取組織資訊系統上的資訊)，都有唯一的識別和驗證方式。

**識別碼**：11110.01q1Organizational.6 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>使用以 PKI 為基礎的驗證時，資訊系統會藉由為已接受的信賴起點建立並驗證憑證路徑 (包括檢查憑證狀態資訊)，來驗證憑證；強制存取對應的私密金鑰；將身分識別對應至個別或群組的相對應帳戶；以及在無法透過網路來存取撤銷資訊時，實作撤銷資料的本機快取來支援路徑探索和驗證。

**識別碼**：11111.01q2System.4 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>資訊系統會使用防止重新執行的驗證機制，例如 nonce、一次性密碼或時間戳記，來保護特殊權限帳戶的網路存取；而針對硬體的權杖型驗證，則會使用符合 NIST SP 800-63-2 電子驗證指導方針中所述最低權杖需求的機制。

**識別碼**：11112.01q2Organizational.67 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對您的訂用帳戶指定最多 3 位擁有者](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |建議您最多指定 3 位訂用帳戶擁有者，以降低遭入侵擁有者導致資料外洩的可能。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>組織會要求電子簽章只能專屬於一個人，不得由其他人重複使用或將其重新指派給其他人。

**識別碼**：11208.01q1Organizational.8 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應將一個以上的擁有者指派給您的訂用帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |建議指定多位訂用帳戶擁有者，如此才能設定系統管理員存取備援。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>針對電子記錄執行的電子簽章和手寫簽名，都應連結到其各自的電子記錄。

**識別碼**：11210.01q2Organizational.10 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核具有 Administrators 群組中指定成員的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本機系統管理員群組未包含原則參數中列出的一或多個成員，則電腦不相容。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>簽署的電子記錄應該包含人類可讀格式的簽署相關資訊。

**識別碼**：11211.01q2Organizational.11 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核遺漏 Administrators 群組中任一指定成員的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本機系統管理員群組未包含原則參數中列出的一或多個成員，則電腦不相容。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>執行特殊權限功能 (例如系統管理) 的使用者會在執行這些特殊權限功能時，使用不同的帳戶。

**識別碼**：1123.01q1System.2 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[在 Administrators 群組中審查具有額外帳戶的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本機系統管理員群組包含的成員未在原則參數中列出，則電腦不相容。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>多重要素驗證方法會根據組織原則 (例如遠端網路存取) 來使用。

**識別碼**：1125.01q2System.1 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核具有 Administrators 群組中指定成員的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本機系統管理員群組未包含原則參數中列出的一或多個成員，則電腦不相容。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>為多重要素驗證提供權杖時，授與存取權之前需要對使用者本人進行驗證。

**識別碼**：1127.01q2System.3 - 01.q **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核遺漏 Administrators 群組中任一指定成員的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果本機系統管理員群組未包含原則參數中列出的一或多個成員，則電腦不相容。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>識別與外部合作對象相關的風險

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>必須先進行盡職調查 (due diligence)、實施適當的控制措施，以及簽署反映安全性需求的合約/協定，確認他們了解並接受其義務後，才能允許外部合作夥伴存取組織資訊和系統。

**識別碼**：1401.05i1Organizational.1239 - 05.i **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>加密組織與外部合作對象之間的遠端存取連線。

**識別碼**：1402.05i1Organizational.45 - 05.i **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>僅授與外部合作夥伴最低需求的存取權，並僅針對必要的期間授與權限。

**識別碼**：1403.05i1Organizational.67 - 05.i **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>外部合作夥伴的盡職調查包括訪談、文件檢閱、檢查清單、認證檢閱 (例如 HITRUST)&nbsp;或其他遠端方法。

**識別碼**：1404.05i2Organizational.1 - 05.i **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>在與外部合作夥伴存取權相關的風險識別中，會將明確定義的最小問題集納入考慮。

**識別碼**：1418.05i1Organizational.8 - 05.i **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>組織會藉由執行年度審查 (其中包含審查其資訊供應鏈相依的所有合作夥伴/協力廠商提供者)，來獲得其資訊供應鏈中具有適當資訊安全性的滿意保證。

**識別碼**：1450.05i2Organizational.2 - 05.i **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>雲端服務提供者會藉由對供應鏈中所有人員制定適當的責任區分、角色型存取及最低權限存取，來設計和實作控制措施，以減輕和遏制資料安全性風險。

**識別碼**：1451.05iCSPOrganizational.2 - 05.i **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>稽核記錄

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>為系統上所有與涵蓋資訊相關的活動 (建立、讀取、更新、刪除) 建立安全的稽核記錄。

**識別碼**：1202.09aa1System.1 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Azure Data Lake Store 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[應在虛擬機器擴展集上安裝系統更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |稽核是否遺漏了任何應安裝的系統安全性更新和重大更新，以確保您的 Windows 及 Linux 虛擬機器擴展集安全無虞。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>稽核記錄包含唯一的使用者識別碼、唯一的資料主體識別碼、執行的功能，以及執行事件的日期/時間。

**識別碼**：1203.09aa1System.2 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Logic Apps 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>特殊權限使用者 (系統管理員、操作員等等) 的活動包括事件的成功/失敗、發生事件的時間、涉及的帳戶、涉及的程序，以及事件的其他相關資訊。

**識別碼**：1204.09aa1System.3 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 IoT 中樞內啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>維護已傳送和已接收的訊息記錄，包括訊息的日期、時間、來源和目的地，但不包含其內容。

**識別碼**：1205.09aa2System.1 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用 Batch 帳戶中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>當系統處於作用中狀態時，一律會提供稽核功能，並追蹤關鍵事件、成功/失敗的資料存取、系統安全性設定變更、特殊權限或公用程式的使用、產生的任何警示、保護系統&nbsp;(例如 A/V 和 IDS) 的啟用和停用、識別和驗證機制的啟用和停用，以及系統層級物件的建立和刪除。

**識別碼**：1206.09aa2System.23 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在虛擬機器擴展集中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |建議啟用記錄，以在發生事件或外洩需要調查時，能夠重新建立活動線索。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>稽核記錄會保留 90 天，而更舊的稽核記錄會封存一年。

**識別碼**：1207.09aa2System.4 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用 Azure 串流分析中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[應啟用事件中樞內的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>稽核記錄會針對管理活動、系統和應用程式啟動/關機/錯誤、檔案變更和安全性原則變更進行維護。

**識別碼**：1208.09aa3System.1 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在搜尋服務中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[應在服務匯流排中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>資訊系統會產生包含下列詳細資訊的稽核記錄：(i) 已存取的檔案名稱；(ii) 用來起始事件的程式或命令；以及 (iii) 來源和目的地位址。

**識別碼**：1209.09aa3System.2 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 App Service 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |稽核應用程式中診斷記錄的啟用情形。 如果發生安全性事件或網路遭到損害，這可讓您重新建立活動線索供調查之用 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>組織內部或外部所有涵蓋資訊的公開項目都會進行維護，包括公開的類型、事件的日期/時間、收件者和傳送者。

**識別碼**：1210.09aa3System.3 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核診斷設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |稽核所選資源類型的診斷設定 |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[應在 Data Lake Analytics 中啟用診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>組織每九十 (90) 天會檢查一次每個已記錄的涵蓋資訊擷取，檢查該資料是否已清除或仍需要使用。

**識別碼**：1211.09aa3System.4 - 09.aa **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用 SQL 伺服器上的稽核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |應在 SQL Server 上啟用稽核，以追蹤伺服器上所有資料庫的活動，並儲存在稽核記錄中。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>監視系統使用

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>監視資訊系統是否有未授權的遠端連線，並至少每季檢閱一次，如果發現未授權的連線，就會採取適當的行動。

**識別碼**：1120.09ab3System.9 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 監視器應從所有區域收集活動記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |此原則會稽核不從所有 Azure 支援區域 (包括全球) 匯出活動的 Azure 監視器記錄設定檔。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>組織會監視資訊系統，以識別可能是系統故障或危害指標的不當行為或異常狀況，並協助確認系統是否以最佳、可復原且安全的狀態運作。

**識別碼**：12100.09ab2System.15 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics 代理程式應該安裝在虛擬機器上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |此原則會稽核任何 Windows/Linux 虛擬機器是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>組織會指定稽核記錄的檢查頻率、檢查的記錄方式，以及檢查執行人員的特定角色和責任，包括專業認證或其他必要的資格。

**識別碼**：12101.09ab1Organizational.3 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics 代理程式應該安裝在虛擬機器擴展集上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |此原則會稽核任何 Windows/Linux 虛擬機器擴展集是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>組織應定期測試其監視和偵測程式、補救缺點及改善其處理程序。

**識別碼**：12102.09ab1Organizational.4 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核 Log Analytics 代理程式未如預期般連線的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 若未安裝代理程式，或已安裝但 COM 物件 AgentConfigManager.MgmtSvcCfg 傳回其所註冊的工作區，並非原則參數中所指定的識別碼，則電腦不相容。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>符合監視授權存取與未經授權存取嘗試的所有相關適用法律需求。

**識別碼**：1212.09ab1System.1 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 監視器記錄設定檔應收集 [寫入]、[刪除] 和 [動作] 分類的記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |此原則可確保記錄設定檔會收集「寫入」、「刪除」和「動作」類別的記錄 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>在整個組織環境中部署的自動化系統會用來監視關鍵事件和異常活動，以及分析系統記錄檔，而結果會定期進行檢閱。

**識別碼**：1213.09ab2System.128 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的訂用帳戶上應啟用 Log Analytics 代理程式的自動化佈建](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |為了監視是否有安全性弱點及威脅，Azure 資訊安全中心會從 Azure 虛擬機器收集資料。 資料會由 Log Analytics 代理程式 (先前稱為 Microsoft Monitoring Agent (MMA)) 進行收集，收集的方式是讀取機器的各種安全性相關組態和事件記錄，並將資料複製到 Log Analytics 工作區進行分析。 建議您啟用自動佈建，以將代理程式自動部署到所有受支援的 Azure VM 和任何新建立的 VM。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>監視包含特殊權限的作業、授權的存取或未經授權的存取嘗試，包括嘗試存取已停用的帳戶，以及系統警示或失敗。

**識別碼**：1214.09ab2System.3456 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 監視器應從所有區域收集活動記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |此原則會稽核不從所有 Azure 支援區域 (包括全球) 匯出活動的 Azure 監視器記錄設定檔。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>組織所採用的稽核和監視系統可簡化稽核和產生報告。

**識別碼**：1215.09ab2System.7 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics 代理程式應該安裝在虛擬機器上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |此原則會稽核任何 Windows/Linux 虛擬機器是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>自動化系統會每天檢查安全性系統的監視活動 (例如 IPS/IDS) 和系統記錄，並找出及記載異常狀況。

**識別碼**：1216.09ab3System.12 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics 代理程式應該安裝在虛擬機器擴展集上](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |此原則會稽核任何 Windows/Linux 虛擬機器擴展集是否未安裝 Log Analytics 代理程式。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>產生警示以供技術人員分析和調查可疑活動或可疑違規行為。

**識別碼**：1217.09ab3System.3 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核 Log Analytics 代理程式未如預期般連線的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 若未安裝代理程式，或已安裝但 COM 物件 AgentConfigManager.MgmtSvcCfg 傳回其所註冊的工作區，並非原則參數中所指定的識別碼，則電腦不相容。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>資訊系統能夠根據可選取的準則，自動處理關注事件的稽核記錄。

**識別碼**：1219.09ab3System.10 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 監視器記錄設定檔應收集 [寫入]、[刪除] 和 [動作] 分類的記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |此原則可確保記錄設定檔會收集「寫入」、「刪除」和「動作」類別的記錄 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>監視包括輸入和輸出通訊及檔案完整性監視。

**識別碼**：1220.09ab3System.56 - 09.ab **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的訂用帳戶上應啟用 Log Analytics 代理程式的自動化佈建](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |為了監視是否有安全性弱點及威脅，Azure 資訊安全中心會從 Azure 虛擬機器收集資料。 資料會由 Log Analytics 代理程式 (先前稱為 Microsoft Monitoring Agent (MMA)) 進行收集，收集的方式是讀取機器的各種安全性相關組態和事件記錄，並將資料複製到 Log Analytics 工作區進行分析。 建議您啟用自動佈建，以將代理程式自動部署到所有受支援的 Azure VM 和任何新建立的 VM。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>系統管理員與操作員的記錄

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>組織會確保已啟用適當的記錄功能，以檢閱系統管理員活動；並且會定期檢閱系統管理員和操作員記錄。

**識別碼**：1270.09ad1System.12 - 09.ad **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定系統管理作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |此原則會稽核未設定活動記錄警示的特定系統管理作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>在系統和網路管理員控制之外的受控入侵偵測系統，會用來監視系統和網路管理活動的合規性。

**識別碼**：1271.09ad1System.1 - 09.ad **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定系統管理作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |此原則會稽核未設定活動記錄警示的特定系統管理作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>權責區分

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>權責區分是用來限制未經授權或無意間修改資訊和系統的風險。

**識別碼**：1229.09c1Organizational.1 - 09.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |若要提供使用者可執行之動作的精細篩選，請使用角色型存取控制 (RBAC) 來管理 Kubernetes Service 叢集中的權限，並設定相關的授權原則。 |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>若未經過授權或偵測，任何人都不可以存取、修改或使用資訊系統。

**識別碼**：1230.09c2Organizational.1 - 09.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核自訂 RBAC 規則的使用方式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |稽核內建角色，例如「擁有者、參與者、讀取者」，而不是自訂的 RBAC 角色，這些角色容易發生錯誤。 使用自訂角色會視為例外狀況，而且需要嚴格審查和威脅模型化 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>&nbsp;對於負責系統管理存取控制的個人，其存取權會限制在每個使用者角色和責任所需的最低權限，而這些人員無法存取與這些控制項相關的稽核功能。

**識別碼**：1232.09c3Organizational.12 - 09.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「使用者權限指派」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |Windows 電腦的「使用者權限指派」類別中應具有指定的群組原則設定，以允許本機登入、RDP、從網路存取，以及其他許多使用者活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>安全性稽核活動是獨立的。

**識別碼**：1276.09c2Organizational.2 - 09.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自訂訂用帳戶擁有者角色不應存在](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |此原則可確保不存在自訂訂用帳戶擁有者角色。 |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>事件的起始會與其授權分開，以降低共謀的可能性。

**識別碼**：1277.09c2Organizational.4 - 09.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「安全性選項 - 使用者帳戶控制」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Windows 電腦的 [安全性選項 - 使用者帳戶控制] 類別中應該有指定的群組原則設定，以用於管理員模式、提高權限提示行為及虛擬化檔案和登錄寫入失敗。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>組織會識別需要區分的職責，並定義資訊系統存取授權以支援責任區分；而不相容的職責會在多個使用者之間隔離，以將誤用或詐騙的機會降到最低。

**識別碼**：1278.09c2Organizational.56 - 09.c **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自訂訂用帳戶擁有者角色不應存在](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |此原則可確保不存在自訂訂用帳戶擁有者角色。 |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>控制惡意程式碼

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>防毒軟體和防間諜軟體會在所有使用者裝置上安裝、操作及更新，以定期掃描系統來找出並移除未經授權的軟體。 伺服器軟體開發人員特別建議不要在其中安裝主機型防毒軟體和反間諜軟體的伺服器環境，可以透過網路型惡意程式碼偵測 (NBMD) 解決方案來滿足需求。

**識別碼**：0201.09j1Organizational.124 - 09.j **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的電腦應啟用自適性應用程式控制，以定義安全應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |啟用應用程式控制項，以定義在您的電腦上執行的已知安全應用程式清單，並在其他應用程式執行時發出警示。 這有助於強化機器，以防範惡意程式碼的攻擊。 為了簡化設定和維護規則的程序，資訊安全中心使用機器學習來分析在每個機器上執行的應用程式，並建議已知安全的應用程式清單。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[為 Windows Server 部署預設的 Microsoft IaaSAntimalware 延伸模組](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |此原則會在 VM 未設定反惡意程式碼軟體延伸模組時，部署預設組態的 Microsoft IaaSAntimalware 延伸模組。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[應在虛擬機器擴展集上安裝端點保護解決方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |稽核虛擬機器擴展集上端點保護解決方案的存在與健康狀態，以免其遭受威脅及弱點的傷害。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Azure 的 Microsoft Antimalware 應設定為自動更新保護簽章](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |此原則會稽核未設定自動更新 Microsoft Antimalware 保護簽章的任何 Windows 虛擬機器。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[在 Azure 資訊安全中心中監視缺少的 Endpoint Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Azure 資訊安全中心會依建議監視未安裝 Endpoint Protection 代理程式的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[您應在機器上安裝系統更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>備份

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>資訊和軟體的備份複本及媒體和還原程序的測試，會以適當的時間間隔來定期執行。

**識別碼**：1616.09l1Organizational.16 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 Azure SQL Database 啟用長期異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此原則會稽核所有未啟用長期異地備援備份的 Azure SQL Database。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>定義並記載每個系統所需備份層級的正式定義，包括每個系統的還原方式、要製作映像的資料範圍、映像處理頻率，以及根據相關合約、法律、法規和商務需求而設定的保留期限。

**識別碼**：1617.09l1Organizational.23 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |適用於 MySQL 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>這些備份會儲存在完全安全的遠端位置，該距離應讓備份能夠適當避開主要站台上的資料損毀，而且應具有適當的實體和環境控制項，以確保其在遠端位置中能受到保護。

**識別碼**：1618.09l1Organizational.45 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |適用於 PostgreSQL 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>維護備份副本的詳細目錄，包括內容和目前的位置。

**識別碼**：1619.09l1Organizational.7 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |適用於 MariaDB 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>由協力廠商提供備份服務時，服務等級協定會包含詳細的保護項目，以控制備份資訊的機密性、完整性和可用性。

**識別碼**：1620.09l1Organizational.8 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |藉由啟用 Azure 備份，確保您的 Azure 虛擬機器受到保護。 Azure 備份是適用於 Azure 安全且符合成本效益的資料保護解決方案。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>自動化工具可用來追蹤所有備份。

**識別碼**：1621.09l2Organizational.1 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 Azure SQL Database 啟用長期異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |此原則會稽核所有未啟用長期異地備援備份的 Azure SQL Database。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>維護備份複本的完整性和安全性，以確保其可在未來使用，並在發生區域性災害的情況下，識別和緩和備份複本的任何潛在存取性問題。

**識別碼**：1622.09l2Organizational.23 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 MySQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |適用於 MySQL 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>涵蓋的資訊會以加密格式進行備份，以確保機密性。

**識別碼**：1623.09l2Organizational.4 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |適用於 PostgreSQL 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>針對個別媒體，組織會每日執行一次增量或差異備份及每週執行一次完整備份。

**識別碼**：1624.09l3Organizational.12 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |適用於 MariaDB 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>三 (3) 個備份世代 (完整備份加上所有相關的增量或差異備份) 會儲存為異地備份，而本地和異地備份的記錄都會包含名稱、日期、時間和動作。

**識別碼**：1625.09l3Organizational.34 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |藉由啟用 Azure 備份，確保您的 Azure 虛擬機器受到保護。 Azure 備份是適用於 Azure 安全且符合成本效益的資料保護解決方案。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>組織會在移動伺服器之前，確保您可以取得涵蓋資訊的最新可擷取複本。

**識別碼**：1626.09l3Organizational.5 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 PostgreSQL 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |適用於 PostgreSQL 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>組織會在每次備份後測試備份資訊，以驗證媒體可靠性和資訊完整性，而之後會至少每年測試一次。

**識別碼**：1627.09l3Organizational.6 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為適用於 MariaDB 的 Azure 資料庫啟用異地備援備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |適用於 MariaDB 的 Azure 資料庫可讓您為資料庫伺服器選擇備援選項。 可以設定為異地備援備份儲存體，其中的資料不只會儲存在裝載您伺服器的區域內，也會複寫至配對的區域，以在發生區域失敗時提供復原選項。 您只可在伺服器建立期間，為備份設定異地備援儲存體。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>識別資料備份程序中的員工成員角色和責任並傳達給員工；特別是，自行攜帶裝置 (BYOD) 使用者必須在其裝置上執行組織和/或用戶端資料的備份。

**識別碼**：1699.09l1Organizational.10 - 09.l **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應該為虛擬機器啟用 Azure 備份](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |藉由啟用 Azure 備份，確保您的 Azure 虛擬機器受到保護。 Azure 備份是適用於 Azure 安全且符合成本效益的資料保護解決方案。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>網路控制

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>除非是由 CIO 或其指定代表撰寫的明確授權，否則組織會監視資訊系統的所有已授權和未授權無線存取，並禁止安裝無線存取點 (WAP)。

**識別碼**：0858.09m1Organizational.4 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[所有網路連接埠均應限制在與虛擬機器建立關聯的網路安全性群組](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure 資訊安全中心發現您某些網路安全性群組的輸入規則過於寬鬆。 輸入規則不應允許來自「任何」或「網際網路」範圍的存取。 這可能會讓攻擊者鎖定您的資源。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Windows 電腦應符合「Windows 防火牆屬性」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Windows 電腦的 [Windows 防火牆內容] 類別中應具有指定的群組原則設定，以用於防火牆狀態、連線、規則管理和通知。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>組織會確保網路中的資訊安全性、使用網路的網路服務和資訊服務具有可用性，以及避免已連線的服務遭到未授權的存取。

**識別碼**：0859.09m1Organizational.78 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>組織會正式管理網路上的設備，包括使用者區域中的設備。

**識別碼**：0860.09m1Organizational.9 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[部署網路安全性群組的診斷設定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |此原則會將診斷設定自動部署至網路安全性群組。 名為 '{storagePrefixParameter}{NSGLocation}' 的儲存體帳戶將會自動建立。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[服務匯流排應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |此原則會稽核任何未設定為使用虛擬網路服務端點的服務匯流排。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>若要在本機和 (或) 廣域網路 (包括無線網路) 上識別及驗證裝置，資訊系統會使用&nbsp;(i) 共用的已知資訊解決方案或 (ii) 組織驗證解決方案，其確切的選擇和強度取決於資訊系統的安全性分類。

**識別碼**：0861.09m2Organizational.67 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |此原則會稽核任何未設定為使用虛擬網路服務端點的 App Service。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Windows 電腦應符合「安全性選項 - 網路存取」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Windows 電腦的 [安全性選項 - 網路存取] 類別中應具有指定的群組原則設定，以包含匿名使用者的存取、本機帳戶及登錄的遠端存取。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>組織會確保資訊系統可保護已傳輸資訊的機密性和完整性，包括傳輸準備期間和接收期間。

**識別碼**：0862.09m2Organizational.8 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |此原則會稽核任何未設定為使用虛擬網路服務端點的 SQL Server。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>組織會建立防火牆設定，以限制非信任網路與涵蓋資訊環境中任何系統元件之間的連線；而對防火牆設定所做的任何變更都會在網路圖表中更新。

**識別碼**：0863.09m2Organizational.910 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[事件中樞應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |此原則會稽核任何未設定為使用虛擬網路服務端點的事件中樞。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>使用限制和實施指導方針是針對 VoIP 正式定義的，包括服務的授權和監視。

**識別碼**：0864.09m2Organizational.12 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cosmos DB 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Cosmos DB。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>組織 (i) 會使用互相連線安全性協定或其他正式合約，來授權從資訊系統到組織外部其他資訊系統的連線；(ii) 記錄每個連線、介面特性、安全性需求，以及所傳達資訊的性質；(iii) 採用「全部拒絕、允許例外」原則，允許從資訊系統連線到組織外部的其他資訊系統；以及 (iv) 套用預設的拒絕規則，以透過主機型防火牆或連接埠篩選工具在其端點 (工作站、伺服器等) 上阻斷所有流量，但明確允許的服務和連接埠除外。

**識別碼**：0865.09m2Organizational.13 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Key Vault。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>組織會描述網路元件邏輯管理的群組、角色和責任，並確保網路基礎結構元素的協調與一致性。

**識別碼**：0866.09m3Organizational.1516 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[儲存體帳戶應限制網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |應限制對儲存體帳戶的網路存取。 請設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，可將存取權授與來自特定 Azure 虛擬網路的流量，或授與公用網際網路 IP 位址範圍 |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>無線存取點會放在安全區域中，並在不使用時關閉 (例如夜間、週末)。

**識別碼**：0867.09m3Organizational.17 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[儲存體帳戶應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |此原則會稽核任何未設定為使用虛擬網路服務端點的儲存體帳戶。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>組織會建立防火牆設定，以限制涵蓋資料環境所需項目的輸入和輸出流量。

**識別碼**：0868.09m3Organizational.18 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>保護並同步路由器設定檔。

**識別碼**：0869.09m3Organizational.19 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>拒絕 Proxy 的所有存取，但不包括明確需要的主機、連接埠和服務。

**識別碼**：0870.09m3Organizational.20 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>授權的 DNS 伺服器會區分為內部和外部角色。

**識別碼**：0871.09m3Organizational.22 - 09.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Container Registry 應該使用虛擬網路服務端點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |此原則會稽核任何未設定為使用虛擬網路服務端點的 Container Registry。 |Audit, Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>網絡服務的安全性

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>網路服務提供者/管理員所提供的已同意服務會正式地受到管理及監視，以確保能安全地供人使用。

**識別碼**：0835.09n1Organizational.1 - 09.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Windows 虛擬機器上安裝網路流量資料收集代理程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |資訊安全中心會使用 Microsoft Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，例如網路地圖上的流量視覺效果、網路強化建議與特定的網路威脅。 |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[虛擬機器應遷移到新的 Azure Resource Manager 資源](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |在您的虛擬機器使用新 Azure Resource Manager 以加強安全性，除了增強存取控制 (RBAC)、改善稽核、提供以 Azure Resource Manager 為基礎的部署及治理、受控身分識別的存取、金鑰保存庫的祕密存取、以 Azure AD 為基礎的驗證，還支援標記和資源群組，讓安全性管理更加輕鬆。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>針對每個從資訊系統到組織外其他資訊系統的連線，組織都會正式授權並記載其特性。

**識別碼**：0836.09.n2Organizational.1 - 09.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Linux 虛擬機器上安裝網路流量資料收集代理程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |資訊安全中心會使用 Microsoft Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，例如網路地圖上的流量視覺效果、網路強化建議與特定的網路威脅。 |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>與外部資訊系統提供者簽訂正式合約，包括安全性和隱私權的特定責任。

**識別碼**：0837.09.n2Organizational.2 - 09.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用網路監看員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>組織會持續檢查並更新互相連線的安全性協定，以確認是否已強制執行安全性需求。

**識別碼**：0885.09n2Organizational.3 - 09.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Linux 虛擬機器上安裝網路流量資料收集代理程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |資訊安全中心會使用 Microsoft Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，例如網路地圖上的流量視覺效果、網路強化建議與特定的網路威脅。 |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>組織會在正式合約或其他文件中使用並記載 i) 全部允許、拒絕例外或 ii) 全部拒絕、允許例外 (慣用) 原則，以允許特定資訊系統連線到外部資訊系統。

**識別碼**：0886.09n2Organizational.4 - 09.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用網路監看員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>組織需要外部/外包服務提供者來識別外部/外包服務佈建時所使用的特定功能、連接埠及通訊協定。

**識別碼**：0887.09n2Organizational.5 - 09.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Windows 虛擬機器上安裝網路流量資料收集代理程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |資訊安全中心會使用 Microsoft Dependency Agent 從您的 Azure 虛擬機器收集網路流量資料，以啟用進階網路保護功能，例如網路地圖上的流量視覺效果、網路強化建議與特定的網路威脅。 |AuditIfNotExists, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>在與外部/外包服務提供者簽訂的合約中，會詳述服務提供者負責的涵蓋資訊共用保護工作。

**識別碼**：0888.09n2Organizational.6 - 09.n **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用網路監看員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>卸載式媒體的管理

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>根據資料分類層級，組織會在使用媒體前進行註冊 (包括膝上型電腦)、對這類媒體的使用方式制定合理的限制，以及對包含涵蓋資訊的媒體提供適當的實體和邏輯保護層級 (包括加密)，直到正確地終結或清理為止。

**識別碼**：0301.09o1Organizational.123 - 09.o **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 資料庫上啟用透明資料加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |應啟用透明資料加密，以保護待用資料，並滿足合規性需求 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>組織會在受控制區域外的傳輸期間，保護及控制包含敏感性資訊的媒體。

**識別碼**：0302.09o2Organizational.1 - 09.o **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在虛擬機器上套用磁碟加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 資訊安全中心依建議會監視未啟用磁碟加密的虛擬機器。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>可識別需要限制使用的數位和非數位媒體，以及用來限制其使用的特定保護措施。

**識別碼**：0303.09o2Organizational.2 - 09.o **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[未連結的磁碟應加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |此原則會稽核任何未啟用加密的未連結磁碟。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>組織會在組織系統中限制可寫入的卸載式媒體用法和個人擁有的卸載式媒體用法。

**識別碼**：0304.09o3Organizational.1 - 09.o **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Data Lake Store 帳戶需要加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |此原則可確保所有 Data Lake Store 帳戶上均已啟用加密 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[SQL 受控執行個體應使用客戶自控金鑰來加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |實作具有自備金鑰的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 這項建議適用於具有相關合規性需求的組織。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 伺服器應使用客戶自控金鑰來加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |實作具有自備金鑰的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 這項建議適用於具有相關合規性需求的組織。 |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>資訊交換原則和程序

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>雲端服務提供者會使用業界認可的虛擬化平台和標準虛擬化格式 (例如開放式虛擬化格式，OVF) 來協助確保互通性，並已記錄針對任何使用中 Hypervisor 所做的自訂變更，以及所有解決方案專屬的虛擬化勾點，以供客戶檢閱。

**識別碼**：0662.09sCSPOrganizational.2 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |用戶端憑證可讓應用程式針對連入要求來要求憑證。 只有具備有效憑證的用戶端才能存取該應用程式。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>組織會在允許使用資訊系統進行資訊交換之前，正式解決多種保護措施。

**識別碼**：0901.09s1Organizational.1 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS 不應允許每項資源存取您的 Web 應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |跨原始資源共用 (CORS) 不應允許所有網域存取 Web 應用程式。 請只允許必要網域與您的 Web 應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>對組織資訊資產的遠端 (外部) 存取，以及對外部資訊資產 (組織沒有其控制權) 的存取，都會以清楚定義的條款及條件為依據。

**識別碼**：0902.09s2Organizational.13 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS 不應允許每項資源存取函式應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |跨原始資源共用 (CORS) 不應允許所有網域存取函式應用程式。 請只允許必要網域與您的函式應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>組織會建立條款與條件，符合與其他組織擁有、作業、和/或維護外部資訊系統所建立之任何信任關係，可讓授權的個人使用外部資訊系統 (i) 從外部資訊系統存取資訊系統；以及 (ii) 處理、儲存或傳輸組織控制的資訊。

**識別碼**：0911.09s1Organizational.2 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS 不應允許每項資源存取您的 API 應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |跨原始資源共用 (CORS) 不應允許所有網域存取 API 應用程式。 請只允許必要網域與您的 API 應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>針對內部網路和外部系統的遠端存取工作階段，加密功能可用來保護其機密性和完整性。

**識別碼**：0912.09s1Organizational.4 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應關閉 Web 應用程式的遠端偵錯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |遠端偵錯需要在 Web 應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>透過較不受信任/開放的公用網路傳輸涵蓋資訊時，強式加密通訊協定可用來保護資訊。

**識別碼**：0913.09s1Organizational.5 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[函式應用程式的遠端偵錯應關閉](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |遠端偵錯需要在函式應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>組織會確保通訊保護需求 (包括資訊交換的安全性) 是原則開發和合規性稽核的主旨。

**識別碼**：0914.09s1Organizational.6 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應關閉 API 應用程式的遠端偵錯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |遠端偵錯需要在 API 應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>組織會透過授權外部資訊系統上的個人，來限制如何使用組織控制的可攜式存放媒體。

**識別碼**：0915.09s2Organizational.2 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |用戶端憑證可讓應用程式針對連入要求來要求憑證。 只有具備有效憑證的用戶端才能存取該應用程式。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>資訊系統會禁止遠端啟用共同作業計算裝置，並提供明確的使用指示給實際出現在裝置上的使用者。

**識別碼**：0916.09s2Organizational.4 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS 不應允許每項資源存取您的 Web 應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |跨原始資源共用 (CORS) 不應允許所有網域存取 Web 應用程式。 請只允許必要網域與您的 Web 應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>雲端服務提供者會使用安全 (例如非明文且已驗證的) 標準化網路通訊協定來匯入和匯出資料及管理服務，並將文件提供給消費者 (租用戶)，以詳細說明相關的互通性與可攜性標準。

**識別碼**：0960.09sCSPOrganizational.1 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS 不應允許每項資源存取函式應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |跨原始資源共用 (CORS) 不應允許所有網域存取函式應用程式。 請只允許必要網域與您的函式應用程式互動。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>針對所有類型的資訊交換 (口頭、紙本和電子設備)，對人員進行適當的主要原則及實務訓練。

**識別碼**：1325.09s1Organizational.3 - 09.s **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[函式應用程式的遠端偵錯應關閉](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |遠端偵錯需要在函式應用程式上開啟輸入連接埠。 應關閉遠端偵錯。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>線上交易

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>檢查與電子商務和線上交易相關的資料，以判斷其是否具有涵蓋資訊。

**識別碼**：0943.09y1Organizational.1 - 09.y **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>所有相關合作夥伴之間進行通訊所用的通訊協定都會使用加密技術 (例如 SSL) 來保護。

**識別碼**：0945.09y1Organizational.3 - 09.y **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核其受信任的根中未包含指定憑證的 Windows 電腦](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |需要將必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 如果電腦受信任的根憑證存放區 (Cert:\LocalMachine\Root) 未包含原則參數所列出的一或多個憑證，則電腦不相容。 |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>組織需要在交易中牽涉到的每一方之間使用加密，以及每一方都需使用電子簽章。

**識別碼**：0946.09y2Organizational.14 - 09.y **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[只允許對您 Azure Cache for Redis 的安全連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |稽核只允許透過 SSL 對 Azure Cache for Redis 進行連線。 使用安全連線可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>組織會確保交易詳細資料的儲存位置位於任何可公開存取的環境之外 (例如，組織內部網路上的現有儲存平台上)，而且不會在可直接從網際網路存取的儲存媒體上保留和公開。

**識別碼**：0947.09y2Organizational.2 - 09.y **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>使用受信任的授權單位 (例如，用於發行和維護數位簽章及/或數位憑證) 時，安全性會整合並內嵌於整個端對端憑證/簽章管理程序中。

**識別碼**：0948.09y2Organizational.3 - 09.y **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>增強用於通訊的通訊協定以解決任何新弱點，並盡速採用更新的通訊協定版本。

**識別碼**：0949.09y2Organizational.5 - 09.y **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[函式應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>作業軟體的控制

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>只有授權的管理員可以根據版本的商務需求和安全性影響，對軟體、應用程式和程式庫實作核准的升級。

**識別碼**：0605.10h1System.12 - 10.h **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Windows 電腦應符合「安全性選項 - 稽核」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Windows 電腦的「安全性選項 - 稽核」類別中應有指定的群組原則設定，以強制執行稽核原則子類別，並在無法記錄安全性審核時關閉。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Windows 電腦應符合「系統稽核原則 - 帳戶管理」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Windows 電腦的「系統稽核原則 - 帳戶管理」類別中應有指定的群組原則設定，以用於稽核應用程式、安全性和使用者群組管理及其他管理事件。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>進入生產環境之前，應用程式和作業系統的可用性、安全性及影響都會成功通過測試。

**識別碼**：0606.10h2System.1 - 10.h **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應補救容器安全性設定中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在已安裝 Docker 且在 Azure 資訊安全中心顯示為建議的電腦上，稽核安全性設定中的弱點。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>組織會使用其封存設定控制程式來維持所有已實作軟體及其系統文件的控制，並封存已實作軟體的先前版本和相關系統文件。

**識別碼**：0607.10h2System.23 - 10.h **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的電腦應啟用自適性應用程式控制，以定義安全應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |啟用應用程式控制項，以定義在您的電腦上執行的已知安全應用程式清單，並在其他應用程式執行時發出警示。 這有助於強化機器，以防範惡意程式碼的攻擊。 為了簡化設定和維護規則的程序，資訊安全中心使用機器學習來分析在每個機器上執行的應用程式，並建議已知安全的應用程式清單。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[應修復虛擬機器擴展集上安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>變更控制程序

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>負責應用程式系統的管理員也會負責專案或支援環境的嚴格控制 (安全性)，並確保所有提議的系統變更都會經過審查，以確認其不會危害系統或作業環境的安全性。

**識別碼**：0635.10k1Organizational.12 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>組織會正式制定組態管理的目的、範圍、角色、責任、管理承諾、組織實體之間的協調及合規性 (例如透過原則、標準、程序)。

**識別碼**：0636.10k2Organizational.1 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>組織已針對資訊系統開發、記載及實作組態管理計畫。

**識別碼**：0637.10k2Organizational.2 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>正式控制、記載及強制執行變更，以將資訊系統的損毀降至最低。

**識別碼**：0638.10k2Organizational.34569 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>使用安裝檢查清單和弱點掃描來驗證伺服器、工作站、裝置和設備的設定，並確保設定符合最低標準。

**識別碼**：0639.10k2Organizational.78 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>將開發工作外包時，合約中會包含因應安全性的變更控制程序，並特別要求開發人員追蹤系統、元件或服務內的安全性瑕疵和瑕疵解析，並向組織定義的人員或角色報告結果。

**識別碼**：0640.10k2Organizational.1012 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>組織不會在重要系統上使用自動更新。

**識別碼**：0641.10k2Organizational.11 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>組織會在組態控制措施下，開發、記載及維護資訊系統的目前基準組態，以及視需要檢閱和更新基準。

**識別碼**：0642.10k3Organizational.12 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>組織會 (i) 使用最新的安全性設定基準，針對資訊系統內所採用的資訊技術產品，建立並記載強制性設定；(ii) 根據明確的操作需求，識別、記載及核准為個別元件強制建立的組態設定例外；以及 (iii) 根據組織原則和程序來監視和控制組態設定的變更。

**識別碼**：0643.10k3Organizational.3 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>組織會採用自動化機制來 (i) 集中管理、套用及驗證組態設定；(ii) 回應網路和系統安全性相關設定的未經授權變更；以及 (iii) 強制執行存取限制和強制動作的稽核。

**識別碼**：0644.10k3Organizational.4 - 10.k **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「系統稽核原則 - 詳細追蹤」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows 電腦的「系統稽核原則 - 詳細追蹤」類別中應有指定的群組原則設定，以用於稽核 DPAPI、程序建立/終止、RPC 事件和 PNP 活動。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>技術弱點的控制

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>系統會識別技術弱點，針對風險進行評估，且及時進行更正。

**識別碼**：0709.10m1Organizational.1 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[虛擬機器上應啟用弱點評估解決方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |稽核虛擬機器，以偵測其是否正在執行支援的弱點評估解決方案。 每個網路風險和安全性計畫的核心部分都在於識別和分析弱點。 Azure 資訊安全中心的標準定價層包含掃描虛擬機器的弱點，不需額外費用。 此外，資訊安全中心可以自動為您部署此工具。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[應補救容器安全性設定中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在已安裝 Docker 且在 Azure 資訊安全中心顯示為建議的電腦上，稽核安全性設定中的弱點。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[應修復虛擬機器擴展集上安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[應修復 SQL 資料庫的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |監視弱點評定掃描結果及如何補救資料庫弱點的建議。 |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[應在 SQL 受控執行個體上啟用弱點評定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |稽核每個未啟用週期性弱點評估掃描的 SQL 受控執行個體。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[弱點評估應於您的 SQL 伺服器上啟用](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |稽核未啟用週期性弱點評估掃描的 Azure SQL 伺服器。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Windows 電腦應符合「安全性選項 - Microsoft 網路伺服器」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Windows 電腦在 [安全性選項 - Microsoft 網路伺服器] 類別中應具有指定的 [群組原則] 設定，才能停用 SMB v1 伺服器。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>所有系統和網路元件都有強化的設定標準。

**識別碼**：0710.10m2Organizational.1 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 受控執行個體上啟用弱點評定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |稽核每個未啟用週期性弱點評估掃描的 SQL 受控執行個體。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>技術弱點管理計畫已準備好監視、評估、排名及補救系統中所識別的弱點。

**識別碼**：0711.10m2Organizational.23 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[虛擬機器上應啟用弱點評估解決方案](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |稽核虛擬機器，以偵測其是否正在執行支援的弱點評估解決方案。 每個網路風險和安全性計畫的核心部分都在於識別和分析弱點。 Azure 資訊安全中心的標準定價層包含掃描虛擬機器的弱點，不需額外費用。 此外，資訊安全中心可以自動為您部署此工具。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>修補程式會在安裝之前進行測試和評估。

**識別碼**：0713.10m2Organizational.5 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>技術弱點管理計畫會每季評估一次。

**識別碼**：0714.10m2Organizational.7 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應修復虛擬機器擴展集上安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>系統會適當地強化 (例如，只設定必要和安全的服務、連接埠和通訊協定)。

**識別碼**：0715.10m2Organizational.8 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應補救容器安全性設定中的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |在已安裝 Docker 且在 Azure 資訊安全中心顯示為建議的電腦上，稽核安全性設定中的弱點。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>組織會根據組織 IS 程序，視需要在每 365 天內進行企業安全性狀態審查一次以上。

**識別碼**：0716.10m3Organizational.1 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應修復 SQL 資料庫的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |監視弱點評定掃描結果及如何補救資料庫弱點的建議。 |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>弱點掃描工具的功能包括可立即更新掃描到的資訊系統弱點。

**識別碼**：0717.10m3Organizational.2 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應修復虛擬機器擴展集上安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>組織會每月 (自動) 掃描資訊系統和託管應用程式中的弱點，以判斷瑕疵補救的狀態，並在識別和回報可能影響系統和網路環境的新弱點時，重新掃描一次 (手動或自動)。

**識別碼**：0718.10m3Organizational.34 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>組織會在每 30 天內或在識別並回報新的弱點時，更新已掃描的資訊系統弱點清單。

**識別碼**：0719.10m3Organizational.5 - 10.m **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 受控執行個體上啟用弱點評定](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |稽核每個未啟用週期性弱點評估掃描的 SQL 受控執行個體。 弱點評估可發現、追蹤並協助您補救資料庫的潛在弱點。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>商務持續性和風險評量

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>組織會識別需要商務持續性的重要商務流程。

**識別碼**：1634.12b1Organizational.1 - 12.b **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核未設定災害復原的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |稽核未設定災害復原的虛擬機器。 若要深入了解災害復原，請造訪 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>商務持續性的資訊安全性層面會 (i) 以識別事件 (或事件的順序) 為依據，這些事件可能導致組織的重要商務流程中斷 (例如設備故障、人為錯誤、遭竊、火災、自然災害、恐怖主義行動)；(ii) 接著是風險評估，用於判斷這類中斷的機率和影響 (以時間、損毀規模和復原期間來評估)；(iii) 根據風險評估的結果，開發商務持續性策略來識別商務持續性的整體方法；以及 (iv)，建立此策略後，就會由管理階層提供背書，並建立及支持實行這項策略的計畫。

**識別碼**：1635.12b1Organizational.2 - 12.b **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |惡意刪除金鑰保存庫可能會導致永久的資料遺失。 您組織中可能有惡意的內部人員能夠刪除和清除金鑰保存庫。 清除保護可對虛刪除的金鑰保存庫強制執行必要的保留期間，以保護您免於遭受內部攻擊。 您的組織內部人員或 Microsoft 在虛刪除保留期間內都無法清除您的金鑰保存庫。 |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>業務影響分析是用來評估災害、安全性失敗、服務中斷和服務可用性的後果。

**識別碼**：1637.12b2Organizational.2 - 12.b **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows 電腦應符合「安全性選項 - 修復主控台」的需求](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |Windows 電腦的 [安全性選項 - 復原主控台] 類別中應該有指定的群組原則設定，以允許對所有磁碟機和資料夾進行軟碟複製和存取。 此原則會要求必須已將來賓組態必要條件部署至原則指派範圍。 如需詳細資訊，請造訪 [https://aka.ms/gcpol](https://aka.ms/gcpol)。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>商務持續性風險評估 (i) 每年都會執行，並會讓商務資源和程序的擁有者完全參與；(ii) 會考慮所有商務程序，而不限於資訊資產，但包含資訊安全性特有的結果；以及 (iii) 針對與組織相關的主要商業目標和準則，識別、量化風險並排定優先順序，包括重要的資源、中斷的影響、允許的停機時間及復原優先順序。

**識別碼**：1638.12b2Organizational.345 - 12.b **所有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[稽核未設定災害復原的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |稽核未設定災害復原的虛擬機器。 若要深入了解災害復原，請造訪 [https://aka.ms/asr-doc](https://aka.ms/asr-doc)。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> 特定 Azure 原則定義的可用性可能因 Azure Government 和其他國家雲端而異。

## <a name="next-steps"></a>後續步驟

有關 Azure 原則的其他文章：

- [法規合規性](../concepts/regulatory-compliance.md)概觀。
- 請參閱[方案定義結構](../concepts/initiative-definition-structure.md)。
- 在 [Azure 原則範例](./index.md)檢閱其他範例。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
