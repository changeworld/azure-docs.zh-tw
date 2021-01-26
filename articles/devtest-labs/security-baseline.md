---
title: 適用于 Azure DevTest Labs 的 Azure 安全性基準
description: 適用于 Azure DevTest Labs 的 Azure 安全性基準
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 09b4e28437c79961d53cf07b068e3169d20fe0c4
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789899"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>適用于 Azure DevTest Labs 的 Azure 安全性基準

適用于 Azure DevTest Labs 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

這項服務的基準取自 [Azure 安全性效能評定1.0 版](../security/benchmarks/overview.md)，其提供如何在 azure 上保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源
**指導方針：** Microsoft 會維護 Azure 資源的時間來源。 不過，您可以管理計算資源的時間同步處理設定。

請參閱下列文章，以瞭解如何設定 Azure 計算資源的時間同步處理： [azure 中 Windows vm 的時間同步](../virtual-machines/windows/time-sync.md)。 

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 微軟

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理
**指導方針：** 啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在管理平面層級的 Azure DevTest Labs 實例上所執行的作業。 您可以使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在 DevTest Labs 實例的管理平面層級完成。

如需詳細資訊，請參閱[建立診斷設定以將平台記錄和計量傳送至不同目的地](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄
**指導方針：** 啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶進行封存。 活動記錄可讓您深入瞭解在管理平面層級的 Azure DevTest Labs 實例上所執行的作業。 您可以使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在 DevTest Labs 實例的管理平面層級完成。

如需詳細資訊，請參閱[建立診斷設定以將平台記錄和計量傳送至不同目的地](../azure-monitor/platform/diagnostic-settings.md)。

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄
**指導方針：** Azure DevTest Labs 虛擬機器 (Vm) 由客戶建立及擁有。 因此，組織負責監視它。 您可以使用 Azure 資訊安全中心來監視計算作業系統。 由安全性中心從作業系統收集的資料包括作業系統類型和版本、作業系統 (Windows 事件記錄檔) 、執行中進程、電腦名稱稱、IP 位址，以及已登入的使用者。 Log Analytics 代理程式也會收集損毀傾印檔案。

如需詳細資訊，請參閱下列文章： 

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](../azure-monitor/learn/quick-collect-azurevm.md)
- [瞭解 Azure 資訊安全中心資料收集](../security-center/security-center-enable-data-collection.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期
**_指導方針：_* 在 Azure 監視器中，根據您組織的合規性法規，為與您的 Azure DevTest Labs 實例相關聯的 Log Analytics 工作區設定記錄保留期限。

如需詳細資訊，請參閱下列文章： [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄
**指導方針：** 啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢，以搜尋詞彙、識別趨勢、分析模式，以及根據可能已針對 Azure DevTest Labs 收集的活動記錄資料，提供許多其他見解。

如需詳細資訊，請參閱下列文章：

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/diagnostic-settings.md)
- [如何在 Azure 監視器的 Log Analytics 工作區中收集和分析 Azure 活動記錄](../azure-monitor/platform/activity-log.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示
**指導方針：** 使用 Azure Log Analytics 工作區來監視和警示有關安全性記錄檔中的異常活動，以及與您的 Azure DevTest Labs 相關的事件。

如需詳細資訊，請參閱下列文章： [如何針對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄
**指導方針：** 不適用。 Azure DevTest Labs 不會處理或產生反惡意程式碼的相關記錄。

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄
**指導方針：** 不適用。 Azure DevTest Labs 不會處理或產生 DNS 相關的記錄。

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄
**指導方針：** Azure DevTest Labs 會建立客戶所擁有和管理 Azure 計算的機器。 在所有支援的 Azure Windows Vm 上使用 Microsoft Monitoring Agent，以記錄進程建立事件和 `CommandLine` 欄位。 針對支援的 Azure Linux Vm，您可以針對每個節點手動設定主控台記錄，並使用 Syslog 來儲存資料。 此外，請使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並從 Azure Vm 對記錄的資料執行查詢。

- [Azure 資訊安全中心的資料收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)
- [Azure 監視器中的 Syslog 資料來源](../azure-monitor/platform/data-sources-syslog.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制
如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查
**指導方針：** Azure Active Directory (Azure AD) 有必須明確指派且可查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)
- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)
- [Azure DevTest Labs 角色](devtest-lab-add-devtest-user.md)  

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼
**指導方針：** Azure Active Directory (Azure AD) 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制建立具有複雜性需求的密碼，以及最小密碼長度（視服務而定）。 您必須負責可能使用預設密碼的協力廠商應用程式和 Marketplace 服務。

DevTest Labs 沒有預設密碼的概念。 

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶
**指導方針：** 使用專用的系統管理帳戶建立標準作業程式。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

- [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](../security-center/security-center-identity-access.md)  
- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)
- [Azure DevTest Labs 角色](devtest-lab-add-devtest-user.md)  

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory
**指導方針：** DevTest Labs 使用 Azure AD 服務進行身分識別管理。 當您授與使用者以 DevTest Labs 為基礎之環境的存取權時，請考慮下列兩個重要層面：

- **資源管理：** 它會提供 Azure 入口網站的存取權來管理資源 (建立 Vm、建立環境、啟動、停止、重新開機、刪除和套用成品等等) 。 在 Azure 中使用 azure 角色型存取控制 (Azure RBAC) 來進行資源管理。 您將角色指派給使用者，並設定資源和存取層級許可權。
- **虛擬機器 (網路層級)**：在預設設定中，vm 會使用本機系統管理員帳戶。 如果有可用的網域 (Azure AD Domain Services、內部部署網域或雲端架構網域) ，則可以將電腦加入網域。 接著，使用者可以使用網域聯結構件來連線至電腦，以使用其網域型身分識別。 

- [DevTest Labs 的參考架構](devtest-lab-reference-architecture.md#architecture)
- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證
**指導方針：** 啟用 Azure Active Directory (AD) Multi-Factor Authentication (MFA) ，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)  
- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視：** _ 是

_ *責任：** 客戶


### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作
**指導方針：** 使用特殊許可權存取工作站 (Paw) ，並設定 MFA 以登入和設定 Azure 資源。

- [瞭解特殊權限存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)  
- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)  

**Azure 資訊安全中心監視：** N/A

**責任：** 客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示
**指導方針：** 當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (Azure AD) 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)  
- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)  

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源
**指導方針：** 使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。

- [如何在 Azure 中設定命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)  

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory
**指導方針：** 使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)  

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取
**指導方針：** Azure Active Directory (Azure AD) 提供記錄以協助探索過時的帳戶。 此外，您也可以使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/overview-reports.md)  
- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)  

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試
**指導方針：** 您可以存取 Azure Active Directory (Azure AD) 登入活動、Audit 和風險事件記錄檔來源，讓您可以與任何安全性資訊和事件管理 (SIEM) /Monitoring 工具整合。

若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示
**指導方針：** 使用 Azure Active Directory (Azure AD) 風險和身分識別保護功能，來設定偵測到與使用者身分識別相關的可疑動作的自動回應。

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)  
- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)  

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權
**指導方針：** 客戶加密箱目前不支援 Azure DevTest Labs。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability) 

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

## <a name="data-protection"></a>資料保護
如需詳細資訊，請參閱[安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查
**指導方針：** 使用標籤協助追蹤儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統
**指導方針：** 針對開發、測試和生產環境執行個別的訂用帳戶或管理群組。 Azure DevTest Labs 實例應以虛擬網路/子網分隔並適當地標記。 

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)
- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)
- [如何設定 DevTest Labs 的虛擬網路](devtest-lab-configure-vnet.md)
- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)
- [如何建立和使用 DevTest Labs 的標記](devtest-lab-add-tag.md)

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸
**指導方針：** 尚未提供;Azure DevTest Labs 尚無法使用資料識別、分類和遺失防護功能。

Microsoft 管理 Azure DevTest Labs 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊
**指導方針：** Azure DevTest Labs 預設需要 TLS 加密通訊。 目前支援 TLS 1.2 版。 如果您的用戶端程式庫或工具不支援 TLS，則啟用未加密的連接可透過 Azure 入口網站或管理 Api 來完成。 在無法進行加密連接的情況下，建議將實驗室和用戶端應用程式放入虛擬網路中。

[瞭解 DevTest Labs 的傳輸案例加密](https://techcommunity.microsoft.com/t5/azure-developer-community-blog/azure-devtest-labs-enforcing-tls-1-2-starting-may-01-2020/ba-p/1236279)

**Azure 資訊安全中心監視：** 是的

**責任：** 共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料
**指導方針：** Azure DevTest Labs 尚無法使用資料識別、分類和遺失防護功能。 標記包含敏感性資訊的實例，並視需要執行協力廠商解決方案，以符合合規性需求。

針對由 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並達到絕佳的長度，以防止客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視：** 目前無法使用

**責任：** 客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權
**指導方針：** 使用 Azure 角色型存取控制 (Azure RBAC) 來控制 Azure DevTest Labs 中的實驗室存取權。

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)
- [瞭解 DevTest Labs 中的角色](devtest-lab-add-devtest-user.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制
**指導方針：** 如果在 DevTest Labs 中建立的計算資源必須符合規範，請執行協力廠商工具（例如自動化的主機型資料遺失防護解決方案），即使資料是從系統複製而來，也可以對資料強制執行存取控制。

針對由 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並達到絕佳的長度，以防止客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊
**指導方針：** Azure DevTest Labs 儲存下列客戶資料：

- 包含從套用成品產生的部署和延伸模組記錄的成品[結果](add-artifact-vm.md)
- 用來從公式建立虛擬機器的[公式檔](devtest-lab-manage-formulas.md)
- 實驗室虛擬機器的作業系統和資料磁片 

成品結果和公式檔會傳送至在每個實驗室部署中建立的 Azure 儲存體帳戶。 Azure 儲存體中的資料會以透明的方式使用256位 AES 加密（可用的最強區塊編碼器之一）進行加密和解密，並符合 FIPS 140-2 規範。 無法停用 Azure 儲存體加密。 您可以依賴 Microsoft 管理的金鑰來加密您的儲存體帳戶，也可以使用您自己的金鑰來管理加密。 如需詳細資訊，請參閱 [實驗室儲存體帳戶的加密](encrypt-storage.md)。

根據預設，所有實驗室作業系統和資料磁片都會使用平臺管理的金鑰進行加密。 所有受控磁片、快照集、映射以及寫入現有受控磁片的資料，都會自動使用平臺管理的金鑰進行待用加密。 作為實驗室擁有者，您可以設定要使用客戶管理的金鑰來加密實驗室作業系統磁片。 使用實驗室資料磁片的客戶管理金鑰進行加密，目前無法透過實驗室本身進行設定。 不過，訂用帳戶管理員現在可以針對訂用帳戶內的實驗室磁片設定此設定。 如需詳細資訊，請參閱 [使用客戶管理的金鑰來加密 Lab DevTest LABS 作業系統磁片](encrypt-disks-customer-managed-keys.md)。

**Azure 資訊安全中心監視：** 不適用

**責任：** 共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示
**指導方針：** 使用 Azure 監視器搭配 Azure 活動記錄來建立 DevTest Labs 實例和其他重要或相關資源發生變更時的警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)
- [如何建立 DevTest Labs 活動記錄事件的警示](create-alerts.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶



## <a name="vulnerability-management"></a>弱點管理
如需詳細資訊，請參閱[安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具
**指導方針：** 遵循 Azure 資訊安全中心保護 Azure DevTest Labs 實例和相關資源的建議。

Microsoft 會對支援 Azure DevTest Labs 的基礎資源執行弱點管理。

- [瞭解 Azure 資訊安全中心建議](../security-center/recommendations-reference.md) 

**Azure 資訊安全中心監視：** 是的

**責任：** 共用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案
**指導方針：** 使用 Azure 更新管理，以確保在裝載于 DevTest Labs 中的 Windows 和 Linux Vm 上安裝最新的安全性更新。 針對 Windows Vm，請確定已啟用 Windows Update，並將其設定為自動更新。 這項設定目前無法透過 DevTest Labs 進行設定，但實驗室系統管理員/訂用帳戶管理員可以在其訂用帳戶中的基礎計算 Vm 上設定此設定。 

- [如何在 Azure 中設定 Vm 的更新管理](../automation/update-management/overview.md)
- [瞭解由安全性中心監視的 Azure 安全性原則](../security-center/policy-reference.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案
**_指導方針：_* _ 作為實驗室系統管理員，您可以使用 [DevTest Labs 構件](add-artifact-vm.md) 來自動化實驗室自訂映射的更新，包括安全性修補程式和其他更新。 

深入瞭解 [DevTest Labs 映射](image-factory-create.md)處理站，這是一個設定即程式碼的解決方案，可讓您定期自動建立及發佈映射，並使用所有所需的設定。 

訂用帳戶管理員也可以使用 Azure 更新管理解決方案來管理 DevTest Labs Vm 的更新和修補程式。 更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 System Center Updates Publisher (Updates Publisher) 之類的工具可讓您將自訂更新發佈至 Windows Server Update Services (WSUS) 。 此案例可讓更新管理修補使用設定管理員作為其更新存放庫（具有協力廠商軟體）的機器。

- [Azure 中的更新管理解決方案](../automation/update-management/overview.md)
- [管理 Vm 的更新和修補程式](../automation/update-management/overview.md)

_ *Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描
**指導方針：** 以一致的間隔匯出掃描結果，並比較結果來確認已補救弱點。 使用 Azure 資訊安全中心建議的弱點管理建議時，客戶可能會進入所選解決方案的入口網站以查看歷史掃描資料。

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序
**指導方針：** 使用 (Azure 資訊安全中心提供的安全分數) 的預設風險評等。

- [瞭解 Azure 資訊安全中心安全分數](../security-center/secure-score-security-controls.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理
*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案
**指導方針：** 使用 Azure Resource Graph 來查詢及探索所有資源， (包括訂用帳戶內) 的 DevTest Labs 資源。 確定您有適當的 (讀取租使用者中) 許可權，而且可以列舉訂用帳戶中的所有 Azure 訂用帳戶和資源。

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)
- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)
- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料
**指導方針：** 將標記套用至 Azure 資源，以根據分類法以邏輯方式組織資料。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)
- [設定 DevTest Labs 的標記](devtest-lab-add-tag.md)

**Azure 資訊安全中心監視：** 無法使用

**責任：** 客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源
**指導方針：** 您可以使用標記、管理群組和個別的訂用帳戶，並在適當時使用個別的實驗室，來組織及追蹤實驗室和實驗室相關的資源。 定期協調清查，並確保從訂用帳戶快速刪除未經授權的資源。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)
- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)
- [如何使用 DevTest Labs 建立實驗室](devtest-lab-create-lab.md)
- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)
- [如何設定實驗室的標記](devtest-lab-add-tag.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查
**指導方針：** 依據組織的需求，建立已核准的 Azure 資源和已核准的計算資源軟體的清查。 訂用帳戶管理員也可以使用自動調整應用程式控制項，這是一項 Azure 資訊安全中心的功能，可協助您定義一組允許在已設定的實驗室電腦群組上執行的應用程式。 這項功能適用于 Azure 和非 Azure Windows (所有版本、傳統或 Azure Resource Manager) 和 Linux 機器。

- [如何啟用適應性應用程式控制](../security-center/security-center-adaptive-application.md)
 
**Azure 資訊安全中心監視：** 不適用 **責任：** 客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源
**指導方針：** 使用 Azure 原則來限制可在客戶訂用帳戶 () 中建立的資源類型，並使用下列內建原則定義：

- 不允許的資源類型
- 允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢/探索訂用帳戶 () 中的資源。 它有助於以高安全性為基礎的環境，例如具有儲存體帳戶的環境。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)
- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式
**指導方針：** Azure 自動化可在部署、作業和解除委任工作負載和資源時，提供完整的控制權。 訂用帳戶管理員可讓您使用 Azure 虛擬機器清查，將訂用帳戶中有關 DevTest Labs Vm 上所有軟體的資訊集合自動化。 您可以從 Azure 入口網站取得 [軟體名稱]、[版本]、[發行者] 和 [重新整理時間] 屬性。 若要取得安裝日期和其他資訊的存取權，客戶必須啟用來賓層級的診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

除了使用變更追蹤監視軟體應用程式之外，Azure 資訊安全中心中的適應性應用程式控制使用機器學習服務來分析在電腦上執行的應用程式，並從這項智慧建立允許清單。 這項功能可大幅簡化設定和維護應用程式允許清單原則的程式，讓您可以避免在您的環境中使用不必要的軟體。 您可以設定 audit 模式或強制執行模式。 Audit 模式只會審核受保護 Vm 上的活動。 強制模式會強制執行規則，並確定不允許執行的應用程式會遭到封鎖。 

- [Azure 自動化簡介](../automation/automation-intro.md)
- [如何啟用 Azure VM 清查](../automation/automation-tutorial-installed-software.md)
- [瞭解適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶


### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式
**指導方針：** Azure 自動化可在部署、作業和解除委任工作負載和資源時，提供完整的控制權。 訂用帳戶管理員可以使用變更追蹤來識別 DevTest Labs 中裝載之 Vm 上安裝的所有軟體。 您可以執行自己的處理常式，或使用 Azure 自動化狀態設定來移除未經授權的軟體。

- [Azure 自動化簡介](../automation/automation-intro.md)
- [使用變更追蹤解決方案來追蹤環境中的變更](../automation/change-tracking/overview.md)
- [Azure 自動化狀態設定總覽](../automation/automation-dsc-overview.md)

**Azure 資訊安全中心監視：** 無法使用

**責任：** 客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式
**指導方針：** 訂用帳戶管理員可讓您使用 Azure 資訊安全中心的自我調整應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 DevTest Labs 中裝載的 Azure Vm 上遭到封鎖而無法執行。

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務
**指導方針：** 使用 Azure 原則來限制可在客戶訂用帳戶 () 中建立的資源類型，並使用下列內建原則定義： 

- 不允許的資源類型
- 允許的資源類型

查看下列文章： 
- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)
- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶


### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查
**指導方針：** 自我調整應用程式控制是 Azure 資訊安全中心的智慧型、自動化、端對端解決方案，可協助您控制哪些應用程式可以在 Azure 上執行，以及裝載于 DevTest Labs (Windows 和 Linux) 的非 Azure 機器。 請注意，您必須是訂用帳戶管理員，才能針對 DevTest Labs 中裝載的基礎計算資源設定此設定。 如果此設定不符合您組織的需求，請執行協力廠商解決方案。

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力
**指導方針：** 使用 Azure 條件式存取來限制使用者透過設定「 **封鎖存取**」來與 Azure Resource Manager 互動的能力」 * *，以 **Microsoft Azure 管理** 應用程式。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力
**指導方針：** 根據腳本的類型而定，您可以使用作業系統專屬的設定或協力廠商資源，以限制使用者在裝載于 DevTest Labs 的 Vm 內執行腳本的能力。 您也可以使用 Azure 資訊安全中心的彈性應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在基礎 Azure Vm 上遭到封鎖而無法執行。

- [如何在 Windows 環境中控制 PowerShell 腳本執行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)
- [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視：** 無法使用

**責任：** 客戶


### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實體或邏輯上隔離高風險的應用程式
**指導方針：** 使用虛擬網路、子網、訂用帳戶、管理群組等，可隔離部署在 Azure 環境中的高風險應用程式。 並使用 Azure 防火牆、Web 應用程式防火牆 (WAF) 或網路安全性群組 (NSG) 來充分保護。

- [設定 DevTest Labs 的虛擬網路](devtest-lab-configure-vnet.md)
- [Azure 防火牆概觀](../firewall/overview.md)
- [Web 應用程式防火牆概觀](../web-application-firewall/overview.md)
- [網路安全性概觀](../virtual-network/network-security-groups-overview.md)
- [Azure 虛擬網路總覽]()
- [使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md)
- [訂用帳戶決策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 資訊安全中心監視：** 無法使用

**責任：** 客戶

## <a name="secure-configuration"></a>安全設定
**如需詳細資訊，請參閱安全性控制：安全設定。**

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定
**指導方針：** 使用 Azure 原則別名來建立自訂原則，以對建立為 DevTest Labs 一部分的 Azure 資源進行審核或強制執行設定。 您也可以使用內建的 Azure 原則定義。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，您應該檢查這些設定，以確保設定符合/超過組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

- [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)
- [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)
- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)
- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定
**指導方針：** 使用 Azure 資訊安全中心建議，在 DevTest Labs 中建立的所有基礎計算資源上維護安全性設定。 此外，您也可以使用自訂的作業系統映射或 Azure 自動化狀態設定或 DevTest Labs 構件，來建立您組織所需作業系統的安全性設定。

- [如何監視 Azure 資訊安全中心建議](../security-center/security-center-recommendations.md)
- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)
- [Azure Automation State Configuration 概觀](../automation/automation-dsc-overview.md)
- [上傳 VHD 並使用它在 Azure 中建立新的 Windows Vm](../virtual-machines/windows/upload-generalized-managed.md)
- [使用 Azure CLI 從自訂磁碟建立 Linux VM](../virtual-machines/linux/upload-vhd.md)
- [建立自訂映射並散發至多個 DevTest Labs](image-factory-save-distribute-custom-images.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定
**指導方針：** 使用 Azure 原則 **拒絕** 和 **部署（如果不存在** ）規則，在 DevTest Labs 中建立的 Azure 資源上強制執行安全設定。 此外，您也可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)
- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)
- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定
**指導方針：** 遵循 Azure 資訊安全中心中的建議，在您于實驗室中建立的基礎 Azure 計算資源上執行弱點評定。 此外，您也可以使用 Azure Resource Manager 範本、自訂作業系統映射或 Azure 自動化狀態設定，以維護您組織所需作業系統的安全性設定。 您也可以使用映射處理站解決方案，此解決方案是一個設定即程式碼解決方案，會定期以所有所需的設定來自動建立和發佈映射。

此外，microsoft 發佈的 Azure Marketplace 虛擬機器映射是由 Microsoft 所管理和維護。

- [如何實行 Azure 資訊安全中心弱點評定建議](../security-center/deploy-vulnerability-assessment-vm.md)
- [Azure Automation State Configuration 概觀](../automation/automation-dsc-overview.md)
- [將 VHD 上傳至 Azure 並新建 VM 的範例指令碼](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)
- [如何在 DevTest Labs 中建立映射 factory](image-factory-create.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定
**指導方針：** 使用 Azure DevOps 來安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本和 Desired State Configuration 腳本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組許可權。

- [Azure Repos Git 教學課程](/azure/devops/repos/git/gitworkflow)
- [關於許可權和群組](/azure/devops/organizations/security/about-permissions?tabs=preview-page&view=azure-devops)
- [Azure DevTest Labs 與 Azure DevOps 工作流程之間的整合](devtest-lab-dev-ops.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像
**指導方針：** 如果使用自訂映射，請使用 Azure 角色型存取控制 (Azure RBAC) ，以確保只有獲得授權的使用者可以存取影像。 您可以使用共用映射庫，將映射分享給需要的特定實驗室。 針對容器映射，請將它們儲存在 Azure Container Registry 中，並使用 Azure RBAC 來確保只有授權的使用者可以存取影像。

- [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)
- [如何設定 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)
- [設定 DevTest Labs 的共用映射庫](configure-shared-image-gallery.md)
- [瞭解適用于 Container Registry 的 Azure RBAC](../container-registry/container-registry-roles.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具
**指導方針：** 使用 Azure 原則定義和實行 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以針對在 DevTest Labs 下建立的 Azure 資源進行審核或強制執行網路設定。 您也可以使用與特定資源相關的內建原則定義。 此外，您可以使用 Azure 自動化來部署設定變更。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)
- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具
**指導方針：** Azure 自動化狀態設定是在任何雲端或內部部署資料中心內 Desired State Configuration (DSC) 節點的設定管理服務。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。 您也可以撰寫可安裝在每部實驗室電腦上的自訂成品，以確保這些專案遵循組織原則。 

- [將機器上架交由 Azure Automation State Configuration 管理](../automation/automation-dsc-onboarding.md)
- [為 DevTest Labs 虛擬機器建立自訂構件](devtest-lab-artifact-author.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視
**指導方針：** 使用 Azure 資訊安全中心針對在 DevTest Labs 下建立的 Azure 資源執行基準掃描。 此外，請使用 Azure 原則來警示和審核 Azure 資源設定。

- [如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)
 
**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視
**指導方針：** 使用 Azure 資訊安全中心對容器的 OS 和 Docker 設定執行基準掃描。

- [了解 Azure 資訊安全中心容器建議](../security-center/container-security.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密
**指導方針：** 使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。

- [設定受控識別，以在 DevTest Labs 中部署 Azure Resource Manager 環境](use-managed-identities-environments.md)
- [設定受控識別在 DevTest Labs 中部署虛擬機器](enable-managed-identities-lab-vms.md)
- [如何建立金鑰保存庫](../key-vault/general/quick-create-portal.md)
- [如何驗證 Key Vault](../key-vault/general/authentication.md)
- [如何指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-portal.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別
**指導方針：** 使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

- [設定受控識別，以在 DevTest Labs 中部署 Azure Resource Manager 環境](use-managed-identities-environments.md)
- [設定受控識別在 DevTest Labs 中部署虛擬機器](enable-managed-identities-lab-vms.md)
 
**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開
**指導方針：** 執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- 如何設定認證掃描器

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶


## <a name="malware-defense"></a>惡意程式碼防禦
*如需詳細資訊，請參閱安全性控制：惡意程式碼防禦。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體
**指導方針：** 針對 Azure 雲端服務和虛擬機器使用 Microsoft Antimalware，以持續監視及保護您的資源。 若為 Linux，請使用協力廠商反惡意程式碼解決方案。 此外，使用 Azure 資訊安全中心的資料服務威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。

- 如何設定雲端服務和虛擬機器的 Microsoft Antimalware
- Azure 資訊安全中心內的威脅防護

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案
**指導方針：** 支援 Azure 服務的基礎主機上已啟用 Microsoft Antimalware (例如，裝載于實驗室) 中的 Azure App Service，但不會在您的內容上執行。
預先掃描即將上傳至非計算 Azure 資源的任何檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。

使用 Azure 資訊安全中心的資料服務威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。

- 瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware
- 瞭解 Azure 資訊安全中心的資料服務威脅偵測

**Azure 資訊安全中心監視：** 是的

**責任：** 不適用


### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章
**指導方針：** 部署時，適用于 Azure 的 Microsoft Antimalware 預設會自動安裝最新的簽章、平臺和引擎更新。 遵循 Azure 資訊安全中心中的建議：「計算 & 應用程式」，以確保 DevTest Labs 基礎計算資源的所有端點都是最新的簽章。 Windows OS 可利用額外的安全性進一步保護，以利用與 Azure 資訊安全中心整合的 Microsoft Defender 進階威脅防護服務，來限制病毒或惡意軟體攻擊的風險。

- 如何部署 Azure 雲端服務和虛擬機器的 Microsoft Antimalware
- Microsoft Defender 進階威脅防護

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

## <a name="data-recovery"></a>資料復原
*如需詳細資訊，請參閱 [安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份
**指導方針：** 目前 Azure DevTest Labs 不支援 VM 備份和快照集。 不過，您可以在 DevTest Labs 託管的基礎 Azure Vm 上啟用和設定 Azure 備份。 此外，只要您有適當的基礎計算資源存取權，您也可以設定自動備份所需的頻率和保留期限。 

- [Azure VM 備份的總覽](../backup/backup-azure-vms-introduction.md)
- [從 VM 設定備份 Azure VM](../backup/backup-azure-vms-first-look-arm.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰
**指導方針：** 目前 Azure DevTest Labs 不支援 VM 備份和快照集。 不過，您可以使用 PowerShell 或 REST Api 來建立裝載于 DevTest Labs 或受控磁片的基礎 Azure Vm 的快照集，只要您有適當的基礎計算資源存取權即可。 您也可以在 Azure Key Vault 內備份任何客戶管理的金鑰。

在目標 Azure Vm 上啟用 Azure 備份，以及想要的頻率和保留期限。 它包含完整的系統狀態備份。 如果您是使用 Azure 磁片加密，Azure VM 備份會自動處理客戶管理金鑰的備份。

- [在使用加密的 Azure Vm 上進行備份](../backup/backup-azure-vms-encryption.md)
- [Azure VM 備份總覽](../backup/backup-azure-vms-introduction.md)
- [Azure VM 備份的總覽](../backup/backup-azure-vms-introduction.md)
- [如何在 Azure 中備份 Key Vault 金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰
**指導方針：** 確保在 Azure 備份內定期執行內容資料還原的能力。 如有必要，請將內容還原至隔離的虛擬網路或訂用帳戶。 此外，也可以測試已備份客戶管理金鑰的還原。

如果您是使用 Azure 磁片加密，您可以使用磁片加密金鑰來還原 Azure VM。 使用磁片加密時，您可以使用磁片加密金鑰來還原 Azure VM。

- [在使用加密的 Azure Vm 上進行備份](../backup/backup-azure-vms-encryption.md)
- [如何從 Azure VM 備份復原檔案](../backup/backup-azure-restore-files-from-vm.md)
- [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)
- [如何備份和還原已加密的 VM](../backup/backup-azure-vms-encryption.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護
**指導方針：** 當您使用 Azure 備份備份受控磁片時，Vm 會以儲存體服務加密 (SSE) 進行靜態加密。 Azure 備份也可以使用 Azure 磁碟加密來備份已加密的 Azure Vm。 Azure 磁碟加密與 BitLocker 加密金鑰整合 (Bek) ，這些金鑰會在金鑰保存庫中做為秘密進行保護。 Azure 磁碟加密也會與 (Kek) 的 Azure Key Vault 金鑰加密金鑰整合。 在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

- [Vm 的虛刪除](../backup/soft-delete-virtual-machines.md)
- [Azure Key Vault-虛刪除總覽](../key-vault/general/soft-delete-overview.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

## <a name="incident-response"></a>事件回應
如需詳細資訊，請參閱[安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南
**指導方針：** 為您的組織建立事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色以及事件處理/管理階段的所有角色，以及事後檢查事件。

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)
- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)
- [使用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序
**指導方針：** Azure 資訊安全中心會將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性是以「資訊安全中心」在「尋找」或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。 您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)
- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視：** 是的

**責任：** 客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序
**指導方針：** 進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

- [NIST 的發行指南，適用于 IT 計畫和功能的測試、訓練和練習程式](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知
**指導方針：** 如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料已被非法或未經授權的物件存取，Microsoft 會使用安全性事件連絡人資訊來與您聯繫。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統
**指導方針：** 使用連續匯出功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)
- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視：** 不適用

**責任：** 客戶

#### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示
**指導方針：** 使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)
 
Azure 資訊安全中心監視： * * * * 不適用

**責任：** 客戶


## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習
*如需詳細資訊，請參閱安全性控制： [滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*


### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現
**指導方針：** 遵循 Microsoft 的參與規則，以確保您的滲透測試不會違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)
- [Microsoft cloud red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視：** 不適用

**責任：** 共用

## <a name="next-steps"></a>後續步驟
請參閱下列文章：

- [Azure DevTest Labs 環境的安全性警示](environment-security-alerts.md)