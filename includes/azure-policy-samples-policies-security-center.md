---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 439803f9a2f66044ec35044189c05fdb1c6f168e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170266"
---
|名稱 |描述 |效果 |版本 |
|---|---|---|---|
|[\[預覽\] 應在虛擬機器上啟用弱點評估](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |監視 Azure 資訊安全中心弱點評估在虛擬機器上偵測到的弱點 |AuditIfNotExists, Disabled |1.0.0-preview |
|[應針對您的訂用帳戶指定最多 3 位擁有者](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |建議您最多指定 3 位訂用帳戶擁有者，以降低遭入侵擁有者導致資料外洩的可能。 |AuditIfNotExists, Disabled |1.0.0 |
|[應為您的訂用帳戶提供安全性連絡人電子郵件地址](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |輸入電子郵件地址，以在 Azure 資訊安全中心偵測到資源洩露時收到通知 |AuditIfNotExists, Disabled |1.0.0 |
|[應為您的訂用帳戶提供安全性連絡人電話號碼](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_phone_number.json) |輸入要在 Azure 資訊安全中心偵測到資源洩露時，用來接收通知的電話號碼 |AuditIfNotExists, Disabled |1.0.0 |
|[應限制透過網際網路面向端點存取](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |Azure 資訊安全中心發現您某些網路安全性群組的輸入規則過於寬鬆。 輸入規則不應允許來自「任何」或「網際網路」範圍的存取。 這可能會讓攻擊者輕鬆鎖定您的資源。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在虛擬機器上啟用自適性應用程式控制](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_AppWhitelistMonitoring_Audit.json) |Azure 資訊安全中心會監視可能的允許清單設定 |AuditIfNotExists, Disabled |1.0.0 |
|[應在網際網路對應虛擬機器中套用自適性網路強化建議](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |Azure 資訊安全中心會分析網際網路對向虛擬機器的流量模式，然後提供降低潛在攻擊面的網路安全性群組規則建議 |AuditIfNotExists, Disabled |1.0.0 |
|[您的訂用帳戶上應啟用 Log Analytics 監視代理程式的自動化佈建](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |啟用 Azure 監視器記錄監視代理程式的自動化佈建，以收集安全性資料 |AuditIfNotExists, Disabled |1.0.0 |
|[應啟用 DDoS 保護標準](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableDDoSProtection_Audit.json) |所有虛擬網路只要其子網路屬於使用公用 IP 的應用程式閘道，就應啟用 DDoS 保護標準。 |AuditIfNotExists, Disabled |1.0.0 |
|[已取代帳戶應該從您的訂用帳戶中移除](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccounts_Audit.json) |已取代帳戶應該從您的訂用帳戶中移除。  已取代帳戶是已封鎖而無法登入的帳戶。 |AuditIfNotExists, Disabled |1.0.0 |
|[具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除。  已取代帳戶是已封鎖而無法登入的帳戶。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在虛擬機器上套用磁碟加密](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |Azure 資訊安全中心會依建議監視未啟用磁碟加密的 VM |AuditIfNotExists, Disabled |1.0.0 |
|[應針對高嚴重性警示啟用電子郵件通知](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |啟用傳送給安全性連絡人的電子郵件安全性警示，讓他們收到來自 Microsoft 的安全性警示電子郵件。 如此可確保適當的人員察覺任何潛在的安全性問題，並能及時降低風險 |AuditIfNotExists, Disabled |1.0.0 |
|[應已針對高嚴重性警示啟用傳送給訂用帳戶擁有者的電子郵件通知](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |啟用傳送給訂用帳戶擁有者的電子郵件安全性警示，讓他們收到來自 Microsoft 的安全性警示電子郵件。 如此可確保他們察覺任何潛在的安全性問題，並能及時降低風險 |AuditIfNotExists, Disabled |1.0.0 |
|[在訂用帳戶上啟用 Azure 資訊安全中心](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Register_To_Azure_Security_Center_Deploy.json) |識別未受 Azure 資訊安全中心 (ASC) 監視的現有訂用帳戶。 系統會將未受 ASC 監視的訂用帳戶註冊到免費定價層。 已受 ASC 監視的訂用帳戶 (免費或標準) 則視為符合規範。 若要註冊新建立的訂用帳戶，請開啟 [合規性] 索引標籤，選取不符合規範的相關指派，並建立補救工作。 當您有一個或多個要透過資訊安全中心監視的新訂用帳戶時，請重複此步驟。 |deployIfNotExists |1.0.0 |
|[應在虛擬機器擴展集上安裝端點保護解決方案](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |稽核虛擬機器擴展集上端點保護解決方案的存在與健康狀態，以免其遭受威脅及弱點的傷害。 |AuditIfNotExists, Disabled |1.0.0 |
|[具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |具有擁有者權限的外部帳戶應從您訂用帳戶移除，以避免未受監視的存取。 |AuditIfNotExists, Disabled |1.0.0 |
|[具有讀取權限的外部帳戶應該從您的訂用帳戶中移除](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |請移除您訂用帳戶中，具有讀取權限的外部帳戶，以避免出現未受監視的存取。 |AuditIfNotExists, Disabled |1.0.0 |
|[應從訂用帳戶移除具有寫入權限的外部帳戶](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |請移除您訂用帳戶中，具有寫入權限的外部帳戶，以避免出現未受監視的存取。 |AuditIfNotExists, Disabled |1.0.0 |
|[應使用網路安全性群組保護網際網路對應的虛擬機器](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnVirtualMachines_Audit.json) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的 VM 遠離潛在威脅。 若要深入了解如何使用 NSG 控制流量，請造訪 https://aka.ms/nsg-doc |AuditIfNotExists, Disabled |1.0.0 |
|[Just-In-Time 網路存取控制應套用在虛擬機器上](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |Azure 資訊安全中心會依建議監視可能的網路 Just-In-Time (JIT) 存取 |AuditIfNotExists, Disabled |1.0.0 |
|[應關閉虛擬機器上的管理連接埠](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |開放的遠端管理連接埠會使您的 VM 暴露在網際網路攻擊的高風險之下。 這些攻擊會嘗試對認證發動暴力密碼破解攻擊，來取得機器的系統管理員存取權。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在您訂用帳戶上具有寫入權限的帳戶上啟用 MFA](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |具備寫入權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在您訂用帳戶上具有擁有者權限的帳戶上啟用 MFA](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |具備擁有者權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在您訂用帳戶上具有讀取權限的帳戶上啟用 MFA](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |具備讀取權限的所有訂用帳戶，均應啟用多重要素驗證 (MFA)，以避免發生帳戶或資源資料外洩。 |AuditIfNotExists, Disabled |1.0.0 |
|[在 Azure 資訊安全中心中監視缺少的 Endpoint Protection](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |Azure 資訊安全中心會依建議監視未安裝 Endpoint Protection 代理程式的伺服器 |AuditIfNotExists, Disabled |1.0.0 |
|[應選取資訊安全中心標準定價層](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_Standard_pricing_tier.json) |標準定價層會為網路及虛擬機器啟用威脅偵測，在 Azure 資訊安全中心提供威脅情報、異常偵測及行為分析 |Audit, Disabled |1.0.0 |
|[子網路應該與網路安全性群組建立關聯](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |使用網路安全性群組 (NSG) 限制 VM 的存取，保護您的子網路遠離潛在威脅。 NSG 包含存取控制清單 (ACL) 規則的清單，可允許或拒絕子網路的網路流量。 |AuditIfNotExists, Disabled |1.0.0 |
|[應在虛擬機器擴展集上安裝系統更新](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |稽核是否遺漏了任何應安裝的系統安全性更新和重大更新，以確保您的 Windows 及 Linux 虛擬機器擴展集安全無虞。 |AuditIfNotExists, Disabled |1.0.0 |
|[您應在機器上安裝系統更新](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |Azure 資訊安全中心會依建議監視伺服器上缺少的安全性系統更新 |AuditIfNotExists, Disabled |1.0.0 |
|[應將一個以上的擁有者指派給您的訂用帳戶](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |建議指定多位訂用帳戶擁有者，如此才能設定系統管理員存取備援。 |AuditIfNotExists, Disabled |1.0.0 |
|[應補救容器安全性設定中的弱點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |在已安裝 Docker 且在 Azure 資訊安全中心顯示為建議的電腦上，稽核安全性設定中的弱點。 |AuditIfNotExists, Disabled |1.0.0 |
|[您應在機器上修復安全性組態的弱點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |Azure 資訊安全中心會依建議監視不符合設定基準的伺服器 |AuditIfNotExists, Disabled |1.0.0 |
|[應修復虛擬機器擴展集上安全性組態的弱點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |稽核虛擬機器擴展集上的 OS 弱點，以免其遭受攻擊。 |AuditIfNotExists, Disabled |1.0.0 |
|[應修復 SQL 資料庫的弱點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |監視弱點評定掃描結果及如何補救資料庫弱點的建議。 |AuditIfNotExists, Disabled |1.0.0 |
|[弱點評量解決方案應修復弱點](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_VMVulnerabilities_Audit.json) |Azure 資訊安全中心會依建議監視弱點評定解決方案偵測到的弱點，以及不具弱點評定解決方案的 VM。 |AuditIfNotExists, Disabled |1.0.0 |
|[在與您 VM 建立關聯的網路安全性群組上應限制 Web 連接埠](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedWebApps_Audit.json) |Azure 資訊安全中心發現有些虛擬機器在執行 Web 應用程式，而且與這些虛擬機器建立關聯的 NSG 在 Web 應用程式連接埠部份過於寬鬆 |AuditIfNotExists, Disabled |1.0.0 |
