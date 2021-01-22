---
title: CIS Microsoft Azure 基礎基準測試的法規合規性詳細資料
description: CIS Microsoft Azure 基礎基準測試法規合規性內建方案的詳細資料。 每個控制項都會對應至一或多個可協助評量的 Azure 原則定義。
ms.date: 01/21/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 0e1c63f92809b6afa1f6d2e61b8ae169f9eec07d
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685641"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-regulatory-compliance-built-in-initiative"></a>CIS Microsoft Azure 基礎基準測試法規合規性內建方案的詳細資料

下列文章詳細說明 Azure 原則法規合規性內建方案定義如何對應至 對應至 CIS Microsoft Azure 基礎基準測試中的 **合規性網域** 和 **控制項**。
如需關於此合規性標準的詳細資訊，請參閱 [CIS Microsoft Azure基礎基準測試](https://www.cisecurity.org/benchmark/azure/)。 若要了解 _擁有權_，請參閱 [Azure 原則原則定義](../concepts/definition-structure.md#type)和 [雲端中共同承擔的責任](../../../security/fundamentals/shared-responsibility.md)。

以下是 **CIS Microsoft Azure 基礎基準測試** 控制項的對應。 使用右側的導覽區可直接跳到特定的 **合規性網域**。 許多控制措施都是以 [Azure 原則](../overview.md)方案定義實作的。 若要檢閱完整方案定義，請在 Azure 入口網站中開啟 [原則]，然後選取 [定義] 頁面。
然後，尋找並選取 **CIS Microsoft Azure 基礎基準 1.1.0** 法規合規性內建方案的定義。

此內建方案會部署為 [CIS Microsoft Azure 基礎基準測試藍圖範例](../../blueprints/samples/cis-azure-1-1-0.md)的一部分。

> [!IMPORTANT]
> 下列每個控制措施都與一或多個 [Azure 原則](../overview.md)定義相關聯。
> 這些原則可協助您使用工具[存取合規性](../how-to/get-compliance-data.md)；不過，控制措施和一或多個原則之間，通常不是一對一或完整對應。 因此，Azure 原則中的 **符合規範** 只是指原則定義本身，這不保證您完全符合所有控制措施需求的規範。 此外，合規性標準包含目前未由任何 Azure 原則定義解決的控制措施。 因此，Azure 原則中的合規性只是整體合規性狀態的部分觀點。 此合規性標準的合規性領域、控制措施與 Azure 原則定義之間的關聯，可能會隨著時間而改變。 若要檢視變更歷程記錄，請參閱 [GitHub 認可歷程記錄](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0.json) \(英文\)。

## <a name="identity-and-access-management"></a>身分識別和存取管理

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>確定已為所有具特殊權限的使用者啟用多重要素驗證

**識別碼**：CIS Azure 1.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>確定已為所有不具特殊權限的使用者啟用多重要素驗證

**識別碼**：CIS Azure 1.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>確定沒有任何來賓使用者

**識別碼**：CIS Azure 1.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |具有擁有者權限的外部帳戶應從您訂用帳戶移除，以避免未受監視的存取。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[具有讀取權限的外部帳戶應該從您的訂用帳戶中移除](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |請移除您訂用帳戶中，具有讀取權限的外部帳戶，以避免出現未受監視的存取。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[應從訂用帳戶移除具有寫入權限的外部帳戶](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |請移除您訂用帳戶中，具有寫入權限的外部帳戶，以避免出現未受監視的存取。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>確定不會建立自訂訂用帳戶擁有者角色

**識別碼**：CIS Azure 1.23 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自訂訂用帳戶擁有者角色不應存在](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |此原則可確保不存在自訂訂用帳戶擁有者角色。 |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>資訊安全中心

### <a name="ensure-that-standard-pricing-tier-is-selected"></a>確定已選取標準定價層

**識別碼**：CIS Azure 2.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應選取資訊安全中心標準定價層](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1181c5f-672a-477a-979a-7d58aa086233) |標準定價層會為網路及虛擬機器啟用威脅偵測，在 Azure 資訊安全中心提供威脅情報、異常偵測及行為分析 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Standard_pricing_tier.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>確定 [監視代理程式的自動化佈建] 會設定為 [開啟]

**識別碼**：CIS Azure 2.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的訂用帳戶上應啟用 Log Analytics 代理程式的自動化佈建](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |為了監視是否有安全性弱點及威脅，Azure 資訊安全中心會從 Azure 虛擬機器收集資料。 資料會由 Log Analytics 代理程式 (先前稱為 Microsoft Monitoring Agent (MMA)) 進行收集，收集的方式是讀取機器的各種安全性相關組態和事件記錄，並將資料複製到 Log Analytics 工作區進行分析。 建議您啟用自動佈建，以將代理程式自動部署到所有受支援的 Azure VM 和任何新建立的 VM。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>確定 ASC 預設原則設定 [監視系統更新] 不是 [已停用]

**識別碼**：CIS Azure 2.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您應在機器上安裝系統更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>確定 ASC 預設原則設定 [監視 OS 弱點] 不是 [已停用]

**識別碼**：CIS Azure 2.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您應在機器上修復安全性組態的弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>確定 ASC 預設原則設定 [監視端點保護] 不是 [已停用]

**識別碼**：CIS Azure 2.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[在 Azure 資訊安全中心中監視缺少的 Endpoint Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Azure 資訊安全中心會依建議監視未安裝 Endpoint Protection 代理程式的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>確定 ASC 預設原則設定 [監視磁碟加密] 不是 [已停用]

**識別碼**：CIS Azure 2.6 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在虛擬機器上套用磁碟加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 資訊安全中心依建議會監視未啟用磁碟加密的虛擬機器。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>確定 ASC 預設原則設定 [監視網路安全性群組] 不是 [已停用]

**識別碼**：CIS Azure 2.7 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在網際網路對應的虛擬機器上套用自適性網路強化建議](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>確定 ASC 預設原則設定 [啟用新一代防火牆 (NGFW) 監視] 不是 [已停用]

**識別碼**：CIS Azure 2.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |使用網路安全性群組 (NSG) 限制對虛擬機器的存取，以保護您的虛擬機器遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 [https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[子網路應該與網路安全性群組建立關聯](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>確定 ASC 預設原則設定 [監視弱點評估] 不是 [已停用]

**識別碼**：CIS Azure 2.10 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[弱點評量解決方案應修復弱點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F760a85ff-6162-42b3-8d70-698e268f648c) |Azure 資訊安全中心會依建議監視弱點評定解決方案偵測到的弱點，以及不具弱點評定解決方案的 VM。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>確定 ASC 預設原則設定 [監視 JIT 網路存取] 不是 [已停用]

**識別碼**：CIS Azure 2.12 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應使用 Just-In-Time 網路存取控制來保護虛擬機器的管理連接埠](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>確定 ASC 預設原則設定 [監視自適性應用程式允許清單] 不是 [已停用]

**識別碼**：CIS Azure 2.13 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的電腦應啟用自適性應用程式控制，以定義安全應用程式](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |啟用應用程式控制項，以定義在您的電腦上執行的已知安全應用程式清單，並在其他應用程式執行時發出警示。 這有助於強化機器，以防範惡意程式碼的攻擊。 為了簡化設定和維護規則的程序，資訊安全中心使用機器學習來分析在每個機器上執行的應用程式，並建議已知安全的應用程式清單。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>確定 ASC 預設原則設定 [監視 SQL 稽核] 不是 [已停用]

**識別碼**：CIS Azure 2.14 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用 SQL 伺服器上的稽核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |應在 SQL Server 上啟用稽核，以追蹤伺服器上所有資料庫的活動，並儲存在稽核記錄中。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>確定 ASC 預設原則設定 [監視 SQL 加密] 不是 [已停用]

**識別碼**：CIS Azure 2.15 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 資料庫上啟用透明資料加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |應啟用透明資料加密，以保護待用資料，並滿足合規性需求 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>確定已設定 [安全性連絡人電子郵件]

**識別碼**：CIS Azure 2.16 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[訂用帳戶應具有連絡人電子郵件地址以處理安全性問題](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |為確保在您的其中一個訂用帳戶有潛在安全性缺口時，您組織中的相關人員會收到通知，請設定要接收資訊安全中心所傳來電子郵件通知的安全性連絡人。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-security-contact-phone-number-is-set"></a>確定已設定安全性連絡人的 [電話號碼]

**識別碼**：CIS Azure 2.17 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為您的訂用帳戶提供安全性連絡人電話號碼](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4d66858-c922-44e3-9566-5cdb7a7be744) |輸入要在 Azure 資訊安全中心偵測到資源洩露時，用來接收通知的電話號碼 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>確定 [在出現高嚴重性警示時傳送電子郵件通知] 會設定為 [開啟]

**識別碼**：CIS Azure 2.18 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對高嚴重性警示啟用電子郵件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |為確保在您的其中一個訂用帳戶有潛在安全性缺口時，您組織中的相關人員會收到通知，請在資訊安全中心內啟用高嚴重性警示的電子郵件通知。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>確定 [同時將電子郵件傳送給訂用帳戶擁有者] 會設定為 [開啟]

**識別碼**：CIS Azure 2.19 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應已針對高嚴重性警示啟用傳送給訂用帳戶擁有者的電子郵件通知](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |為確保訂用帳戶擁有者會在其訂用帳戶有潛在安全性缺口時收到通知，請在資訊安全中心內設定發送對象為訂用帳戶擁有者的高嚴重性警示電子郵件通知。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>儲存體帳戶

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>確定 [需要安全傳輸] 設定為 [已啟用]

**識別碼**：CIS Azure 3.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用儲存體帳戶的安全傳輸](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |稽核儲存體帳戶中安全傳輸的需求。 安全傳輸這個選項會強制您的儲存體帳戶僅接受來自安全連線 (HTTPS) 的要求。 使用 HTTPS 可確保伺服器與服務之間的驗證，避免傳輸中的資料遭受網路層的攻擊，例如中間人攻擊、竊聽及工作階段劫持 |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>確認 blob 容器的 [公用存取層級] 設定為 [私人]

**識別碼**： CIS Azure 3.6 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應禁止儲存體帳戶公用存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |以匿名的公開讀取權限來存取 Azure 儲存體中的容器和 Blob，是共用資料的便利方式，但也可能會帶來安全性風險。 為了防止不想要的匿名存取所造成的資料缺口，Microsoft 建議除非您的案例需要，否則避免公開存取儲存體帳戶。 |Audit, Deny, Disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>確定儲存體帳戶的預設網路存取規則設定為拒絕

**識別碼**：CIS Azure 3.7 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[儲存體帳戶應限制網路存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |應限制對儲存體帳戶的網路存取。 請設定網路規則，只允許來自認可之網路的應用程式存取儲存體帳戶。 若要允許來自特定網際網路或內部部署用戶端的連線，可將存取權授與來自特定 Azure 虛擬網路的流量，或授與公用網際網路 IP 位址範圍 |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>確定已啟用 [信任的 Microsoft 服務] 來進行儲存體帳戶存取

**識別碼**：CIS Azure 3.8 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[儲存體帳戶應允許來自信任 Microsoft 服務的存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |有些與儲存體帳戶互動的 Microsoft 服務是從無法透過網路規則授與存取權的網路運作。 若要讓這類服務如預期方式運作，請允許受信任的 Microsoft 服務集合略過網路規則。 這些服務會使用增強式驗證存取儲存體帳戶。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>資料庫服務

### <a name="ensure-that-auditing-is-set-to-on"></a>確定 [稽核] 設定為 [開啟]

**識別碼**：CIS Azure 4.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用 SQL 伺服器上的稽核](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |應在 SQL Server 上啟用稽核，以追蹤伺服器上所有資料庫的活動，並儲存在稽核記錄中。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>確定已在 SQL 伺服器的 [稽核] 原則中正確設定了 'AuditActionGroups' 原則

**識別碼**：CIS Azure 4.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 審核設定應已設定動作群組來擷取重要活動](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |AuditActionsAndGroups 屬性至少應包含 SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP、FAILED_DATABASE_AUTHENTICATION_GROUP、BATCH_COMPLETED_GROUP，才可確保完整記錄稽核 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>確定 [稽核] 保留期「大於 90 天」

**識別碼**：CIS Azure 4.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 伺服器應設定 90 天或更高的稽核保留期。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |SQL 伺服器應設定 90 天或更高的稽核保留期。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>確定 SQL 伺服器上的 [進階資料安全性] 設定為 [開啟]

**識別碼**：CIS Azure 4.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 受控執行個體上啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |在沒有進階資料安全性的情況下稽核 SQL 受控執行個體。 |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[應在 SQL 伺服器上啟用進階資料安全性](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |在沒有「進階資料安全性」的情況下稽核 SQL 伺服器 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>確定已設定 Azure Active Directory 系統管理員

**識別碼**：CIS Azure 4.8 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應針對 SQL 伺服器佈建 Azure Active Directory 管理員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |為 SQL Server 稽核 Azure Active Directory 系統管理員的佈建情況，以啟用 Azure AD 驗證。 Azure AD 驗證可針對資料庫使用者及其他 Microsoft 服務，簡化權限管理及集中管理身分識別 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>確定 SQL Database 上的 [資料加密] 設定為 [開啟]

**識別碼**：CIS Azure 4.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 SQL 資料庫上啟用透明資料加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |應啟用透明資料加密，以保護待用資料，並滿足合規性需求 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>確定 SQL 伺服器的 TDE 保護裝置已使用 BYOK 加密 (使用您自己的金鑰)

**識別碼**：CIS Azure 4.10 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL 受控執行個體應使用客戶自控金鑰來加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |實作具有自備金鑰的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 這項建議適用於具有相關合規性需求的組織。 |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL 伺服器應使用客戶自控金鑰來加密待用資料](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |實作具有自備金鑰的透明資料加密 (TDE)，能夠增加 TDE 保護裝置的透明度及控制權、透過 HSM 支援的外部服務提高安全性，以及提升職權劃分。 這項建議適用於具有相關合規性需求的組織。 |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>確定會為 MySQL 資料庫伺服器將 [強制執行 SSL 連線] 設定為 [已啟用]

**識別碼**：CIS Azure 4.11 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 MySQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>確定會為 PostgreSQL 資料庫伺服器將 [log_checkpoints] 伺服器參數設定為 [開啟]

**識別碼**：CIS Azure 4.12 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 PostgreSQL 資料庫伺服器啟用記錄檢查點](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |此原則可協助稽核您環境中任何未啟用 log_checkpoints 設定的 PostgreSQL 資料庫。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>確定會為 PostgreSQL 資料庫伺服器將 [強制執行 SSL 連線] 設定為 [已啟用]

**識別碼**：CIS Azure 4.13 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 PostgreSQL 資料庫伺服器啟用 [強制執行 SSL 連線]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |適用於 PostgreSQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 PostgreSQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將兩者之間的資料流加密，有助於抵禦「中間人」攻擊。 此設定會強制一律啟用 SSL，以存取您的資料庫伺服器。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>確定會為 PostgreSQL 資料庫伺服器將 [log_connections] 伺服器參數設定為 [開啟]

**識別碼**：CIS Azure 4.14 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 PostgreSQL 資料庫伺服器啟用記錄連線](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |此原則可協助稽核您環境中任何未啟用 log_connections 設定的 PostgreSQL 資料庫。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>確定會為 PostgreSQL 資料庫伺服器將 [log_disconnections] 伺服器參數設定為 [開啟]

**識別碼**：CIS Azure 4.15 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 PostgreSQL 資料庫伺服器記錄中斷連線。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |此原則可協助稽核您環境中任何未啟用 log_disconnections 的 PostgreSQL 資料庫。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>確定會為 PostgreSQL 資料庫伺服器將 [connection_throttling] 設定為 [開啟]

**識別碼**：CIS Azure 4.17 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應為 PostgreSQL 資料庫伺服器啟用連線節流](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |此原則可協助稽核您環境中任何未啟用連線節流的 PostgreSQL 資料庫。 此設定可針對每個發生太多無效密碼登入失敗的 IP 啟用暫時連線節流。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>記錄和監視

### <a name="ensure-that-a-log-profile-exists"></a>確定記錄設定檔存在

**識別碼**：CIS Azure 5.1.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 訂用帳戶應具有用於活動記錄的記錄設定檔](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |此原則可確保記錄設定檔已啟用，可用於匯出活動記錄。 如果沒有建立記錄檔設定檔來將記錄匯出至儲存體帳戶或事件中樞，則會進行稽核。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>確定 [活動記錄保留] 會設定為 365 天或更長天數

**識別碼**：CIS Azure 5.1.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[活動記錄至少應保留一年](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |若保留期未設為 365 天或永久 (保留期設為 0)，此原則就會稽核活動記錄。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>確定稽核設定檔會擷取所有活動

**識別碼**：CIS Azure 5.1.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 監視器記錄設定檔應收集 [寫入]、[刪除] 和 [動作] 分類的記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |此原則可確保記錄設定檔會收集「寫入」、「刪除」和「動作」類別的記錄 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>確定記錄設定檔會擷取所有區域 (包括全球) 的活動記錄

**識別碼**：CIS Azure 5.1.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure 監視器應從所有區域收集活動記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |此原則會稽核不從所有 Azure 支援區域 (包括全球) 匯出活動的 Azure 監視器記錄設定檔。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>確定儲存活動記錄的儲存體容器無法公開存取

**識別碼**： CIS Azure 5.1.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應禁止儲存體帳戶公用存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |以匿名的公開讀取權限來存取 Azure 儲存體中的容器和 Blob，是共用資料的便利方式，但也可能會帶來安全性風險。 為了防止不想要的匿名存取所造成的資料缺口，Microsoft 建議除非您的案例需要，否則避免公開存取儲存體帳戶。 |Audit, Deny, Disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>確保內含容器且有活動記錄的儲存體帳戶會以 BYOK (使用您自己的金鑰) 加密

**識別碼**：CIS Azure 5.1.6 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[儲存體帳戶內含的容器如有活動記錄，就必須以 BYOK 加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |此原則會稽核內含容器且有活動記錄的儲存體帳戶是否以 BYOK 加密。 根據設計，只有在儲存體帳戶位於與活動記錄相同的訂用帳戶時，原則才會生效。 如需 Azure 儲存體待用資料加密的詳細資訊，請參閱 [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok)。  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>確定 Azure KeyVault 的記錄是 [已啟用]

**識別碼**：CIS Azure 5.1.7 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用 Key Vault 中的診斷記錄](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |稽核診斷記錄的啟用情形。 這可讓您在發生安全性事件或網路遭到損害時，重新建立活動線索以供調查之用 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>確定建立原則指派的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定原則作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |此原則會稽核未設定活動記錄警示的特定原則作業。 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>確定建立或更新網路安全性群組的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定系統管理作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |此原則會稽核未設定活動記錄警示的特定系統管理作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>確定刪除網路安全性群組的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定系統管理作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |此原則會稽核未設定活動記錄警示的特定系統管理作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>確定建立或更新網路安全性群組規則的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定系統管理作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |此原則會稽核未設定活動記錄警示的特定系統管理作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>確定刪除網路安全性群組規則的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定系統管理作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |此原則會稽核未設定活動記錄警示的特定系統管理作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>確定建立或更新安全性解決方案的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.6 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定安全性作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |此原則會稽核未設定活動記錄警示的特定安全性作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>確定刪除安全性解決方案的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.7 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定安全性作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |此原則會稽核未設定活動記錄警示的特定安全性作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>確定建立、更新或刪除 SQL Server 防火牆規則的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.8 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定系統管理作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |此原則會稽核未設定活動記錄警示的特定系統管理作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>確定更新安全性原則的活動記錄警示是否存在

**識別碼**：CIS Azure 5.2.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定安全性作業應有活動記錄警示](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |此原則會稽核未設定活動記錄警示的特定安全性作業。 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>網路功能

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>確定已限制從網際網路進行 RDP 存取

**識別碼**：CIS Azure 6.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應禁止從網際網路存取 RDP](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |此原則會稽核任何允許從網際網路存取 RDP 的網路安全性規則 |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>確定已限制從網際網路進行 SSH 存取

**識別碼**：CIS Azure 6.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應禁止從網際網路存取 SSH](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |此原則會稽核任何允許從網際網路存取 SSH 的網路安全性規則 |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>確定網路監看員為 [已啟用]

**識別碼**：CIS Azure 6.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應啟用網路監看員](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |網路監看員是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路案例層級條件。 案例層級監視可讓您在端對端網路層級檢視診斷問題。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 |auditIfNotExists |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>虛擬機器

### <a name="ensure-that-os-disk-are-encrypted"></a>確定 [OS 磁碟] 已加密

**識別碼**：CIS Azure 7.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在虛擬機器上套用磁碟加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 資訊安全中心依建議會監視未啟用磁碟加密的虛擬機器。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>確定 [資料磁碟] 已加密

**識別碼**：CIS Azure 7.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在虛擬機器上套用磁碟加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Azure 資訊安全中心依建議會監視未啟用磁碟加密的虛擬機器。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>確定 [未連結的磁碟] 已加密

**識別碼**：CIS Azure 7.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[未連結的磁碟應加密](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |此原則會稽核任何未啟用加密的未連結磁碟。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>確定只安裝核准的擴充功能

**識別碼**：CIS Azure 7.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[僅應安裝已核准的 VM 擴充功能](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |此原則會控管未核准的虛擬機器延伸模組。 |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>確定已為所有虛擬機器套用最新 OS 修補程式

**識別碼**：CIS Azure 7.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您應在機器上安裝系統更新](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>確定已為所有虛擬機器安裝端點

**識別碼**：CIS Azure 7.6 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[在 Azure 資訊安全中心中監視缺少的 Endpoint Protection](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Azure 資訊安全中心會依建議監視未安裝 Endpoint Protection 代理程式的伺服器 |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>其他安全性考量

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>確定已在所有索引鍵上設定到期日

**識別碼**： CIS Azure 8.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault 金鑰應具有到期日](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |密碼編譯金鑰應具有已定義的到期日，而不是永久有效。 永久有效的金鑰會讓潛在攻擊者有更多的時間來危害金鑰。 建議的安全性做法是在密碼編譯金鑰上設定到期日。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>確定已在所有秘密上設定到期日

**識別碼**： CIS Azure 8.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault 祕密應設定到期日](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |祕密應具有已定義的到期日，而不是永久有效。 永久有效的祕密會讓潛在攻擊者有更多的時間來危害祕密。 建議的安全性做法是在祕密上設定到期日。 |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>確定金鑰保存庫可復原

**識別碼**：CIS Azure 8.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[金鑰保存庫應啟用清除保護](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |惡意刪除金鑰保存庫可能會導致永久的資料遺失。 您組織中可能有惡意的內部人員能夠刪除和清除金鑰保存庫。 清除保護可對虛刪除的金鑰保存庫強制執行必要的保留期間，以保護您免於遭受內部攻擊。 您的組織內部人員或 Microsoft 在虛刪除保留期間內都無法清除您的金鑰保存庫。 |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>在 Azure Kubernetes Services 中啟用角色型存取控制 (RBAC)

**識別碼**：CIS Azure 8.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[應在 Kubernetes 服務上使用角色型存取控制 (RBAC)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |若要提供使用者可執行之動作的精細篩選，請使用角色型存取控制 (RBAC) 來管理 Kubernetes Service 叢集中的權限，並設定相關的授權原則。 |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>確定已在 Azure App Service 上設定 App Service 驗證

**識別碼**：CIS Azure 9.1 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的 API 應用程式應啟用驗證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service 驗證這項功能可以防止匿名 HTTP 要求送達 API 應用程式，也可以在擁有權杖的要求送達 API 應用程式前予以驗證 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[您的函式應用程式應啟用驗證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service 驗證這項功能可以防止匿名 HTTP 要求送達函式應用程式，也可以在擁有權杖的要求送達函式應用程式前予以驗證 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[您的 Web 應用程式應啟用驗證](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service 驗證這項功能可以防止匿名 HTTP 要求送達 Web 應用程式，也可以在擁有權杖的要求送達 Web 應用程式前予以驗證 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>確定 Web 應用程式在 Azure App Service 中會將所有 HTTP 流量重新導向至 HTTPS

**識別碼**：CIS Azure 9.2 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web 應用程式應只可經由 HTTPS 存取](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |使用 HTTPS 可確保伺服器/服務驗證，並保護傳輸中的資料不受網路層的竊聽攻擊。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>確定 Web 應用程式使用最新版本的 TLS 加密

**識別碼**：CIS Azure 9.3 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[您的 API 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[您的函式應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[您的 Web 應用程式應使用最新的 TLS 版本](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |升級至最新的 TLS 版本 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]

**識別碼**：CIS Azure 9.4 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 API 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |用戶端憑證可讓應用程式針對連入要求來要求憑證。 只有具備有效憑證的用戶端才能存取該應用程式。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[確定 Web 應用程式已將 [用戶端憑證 (傳入用戶端憑證)] 設定為 [開啟]](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |用戶端憑證可讓應用程式針對連入要求來要求憑證。 只有具備有效憑證的用戶端才能存取該應用程式。 |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[函式應用程式應啟用「用戶端憑證 (傳入用戶端憑證)」](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |用戶端憑證可讓應用程式針對連入要求來要求憑證。 只有具備有效憑證的用戶端才能存取該應用程式。 |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>確定已在 App Service 上啟用 [向 Azure Active Directory 註冊]

**識別碼**：CIS Azure 9.5 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API 應用程式應該使用受控識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |使用受控識別來增強驗證安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[函式應用程式應該使用受控識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |使用受控識別來增強驗證安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web 應用程式應該使用受控識別](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |使用受控識別來增強驗證安全性 |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>確定 'PHP' 在用來執行 Web 應用程式時，版本是最新的

**識別碼**：CIS Azure 9.7 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 'PHP' 在作為 API 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |PHP 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 API 應用程式的最新 PHP 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[確定 'PHP' 在作為 Web 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |PHP 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 PHP 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>確定 'Python' 在用來執行 Web 應用程式時，版本是最新的

**識別碼**：CIS Azure 9.8 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 'Python' 在作為 API 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Python 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 API 應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[確定 'Python' 在作為函式應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Python 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於函式應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[確定 'Python' 在作為 Web 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Python 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>確定 'Java' 在用來執行 Web 應用程式時，版本是最新的

**識別碼**：CIS Azure 9.9 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 'Java' 在作為 API 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Java 會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 API 應用程式的最新 Python 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[確定 'Java' 在作為函式應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Java 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於函式應用程式的最新 Java 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[確定 'Java' 在作為 Web 應用程式的一部分時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Java 軟體會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 建議使用適用於 Web 應用程式的最新 Java 版本，以利用最新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>確定 'HTTP' 在用來執行 Web 應用程式時，版本是最新的

**識別碼**：CIS Azure 9.10 **擁有權**：客戶

|名稱<br /><sub>(Azure 入口網站)</sub> |描述 |效果 |版本<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[確定 'HTTP' 在用來執行 API 應用程式時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |HTTP 會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 請使用適用於 Web 應用程式的最新 HTTP 版本，以利用較新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[確定 'HTTP' 在用來執行函式應用程式時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |HTTP 會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 請使用適用於 Web 應用程式的最新 HTTP 版本，以利用較新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[確定 'HTTP' 在用來執行 Web 應用程式時，版本是最新的](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |HTTP 會定期發行較新的版本，以解決安全性缺陷或納入其他功能。 請使用適用於 Web 應用程式的最新 HTTP 版本，以利用較新版本的安全性修正 (如果有的話) 及 (或) 新功能。 此原則目前僅適用於 Linux Web 應用程式。 |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> 特定 Azure 原則定義的可用性可能因 Azure Government 和其他國家雲端而異。

## <a name="next-steps"></a>後續步驟

有關 Azure 原則的其他文章：

- [法規合規性](../concepts/regulatory-compliance.md)概觀。
- 請參閱[方案定義結構](../concepts/initiative-definition-structure.md)。
- 在 [Azure 原則範例](./index.md)檢閱其他範例。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[補救不符合規範的資源](../how-to/remediate-resources.md)。
