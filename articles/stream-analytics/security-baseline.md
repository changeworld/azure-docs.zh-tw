---
title: 適用于串流分析的 Azure 安全性基準
description: 適用于串流分析的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c1e61e9434d96148fbce840f4bad7f6a3b2ecb6b
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98202720"
---
# <a name="azure-security-baseline-for-stream-analytics"></a>適用于串流分析的 Azure 安全性基準

適用于串流分析的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針： Azure 串流分析不支援使用 (NSG) 和 Azure 防火牆的網路安全性群組。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針： Azure 串流分析不支援使用虛擬網路和子網。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：使用 Azure 資訊安全中心威脅防護來偵測具有已知惡意或未使用網際網路 IP 位址的通訊併發出警示。

* [Azure 資訊安全中心中 Azure 服務層的威脅防護](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針： Azure 串流分析不會使用網路安全性群組，且不會捕獲 Azure Key Vault 的流量記錄。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：使用 Azure 資訊安全中心威脅防護來偵測 Azure 訂用帳戶環境中異常或可能有害的作業。

* [Azure 資訊安全中心中 Azure 服務層的威脅防護](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針： Azure 串流分析不支援使用虛擬網路和網路規則。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針： Azure 串流分析不支援使用虛擬網路和網路裝置。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針： Azure 串流分析不支援使用虛擬網路和流量設定規則。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄來監視資源設定，以及偵測串流分析資源的變更。 在 Azure 監視器中建立警示，在發生重大資源變更時將會觸發此警示。

* [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護用於 Azure 資源的時間來源，例如串流分析。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌記錄，以匯總安全性資料，例如 audit 事件和要求。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體 accountyfor 長期/封存儲存體，並選擇性地使用安全性功能，例如不可變的儲存體和強制保留保留。

* [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用 Azure 串流分析的診斷設定，以存取系統管理、安全性和診斷記錄。 您也可以啟用 Azure 活動記錄診斷設定，並將記錄傳送至相同的 Log Analytics 工作區或儲存體帳戶。

* [Azure 串流分析提供診斷記錄和活動資料以供審查](./stream-analytics-job-diagnostic-logs.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：將安全性事件記錄檔儲存在 Azure 儲存體帳戶或 Log Analytics 工作區時，您可以根據組織的需求來設定保留原則。

* [Azure 串流分析提供診斷記錄和活動資料以供審查](./stream-analytics-job-diagnostic-logs.md)

* [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

* [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：分析和監視記錄中的異常行為，並定期查看串流分析資源的結果。 使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [有關 Log Analytics 工作區的詳細資訊](../azure-monitor/log-query/log-analytics-tutorial.md)

* [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：啟用串流分析的診斷設定，並將記錄傳送至 Log Analytics 工作區。 請將 Log Analytics 工作區在 Azure Sentinel 上線，因為其可提供安全性協調流程自動化回應 (SOAR) 解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

* [Azure 串流分析提供診斷記錄和活動資料以供審查](./stream-analytics-job-diagnostic-logs.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用;串流分析不會處理或產生反惡意程式碼的相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針： Azure 監視器中的 Azure DNS 分析 (預覽版) 解決方案會收集安全性、效能和作業的 DNS 基礎結構見解。 這目前不支援 Azure 串流分析，但您可以使用協力廠商 dns 記錄解決方案。

* [收集搭配 DNS 分析預覽版解決方案使用 DNS 基礎結構的深入解析](../azure-monitor/insights/dns-analytics.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導** 方針： Azure AD 有必須明確指派的內建角色。 您可以查詢角色以探索成員資格。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針：串流分析沒有預設密碼的概念，因為 Azure Active Directory 提供驗證，並受到 azure 角色型存取控制的保護， (azure RBAC) 來管理服務。 視插入資料流程服務和輸出服務而定，您需要輪替在作業中設定的認證。

* [針對串流分析作業的輸入和輸出輪替登入認證](./stream-analytics-login-credentials-inputs-outputs.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：建立身分識別管理和角色安全性方案，遵循最佳作法，包括系統管理員角色的最低特殊許可權存取原則。 使用 Azure Privileged Identity Management (PIM) 提供 Azure AD 和 Azure 資源的即時特殊許可權存取。 使用 Azure PIM 警示和審核歷程記錄來監視系統管理帳戶的活動。 使用 Azure AD 的安全性報告來協助識別可能已遭盜用的系統管理帳戶。

* [深入了解](../active-directory/privileged-identity-management/index.yml)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：可能的話，請使用 Azure Active Directory SSO，而不是為每個服務設定獨立認證。 執行 Azure 資訊安全中心身分識別 &amp; 存取建議。

* [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory 的多重要素驗證 (MFA) ，並遵循 Azure 資訊安全中心身分識別和存取管理的建議，以協助保護您的串流分析資源。

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用 paw (特殊許可權的存取工作站) 搭配多重要素驗證 (MFA) 設定為登入和設定串流分析資源。

* [瞭解特殊權限存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

* [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)

* [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。

* [如何在 Azure 中設定命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 提供 Azure 角色型存取控制 (Azure RBAC) ，以精細控制用戶端對串流分析資源的存取。

* [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針：請參閱 Azure Active Directory 記錄，以協助探索可包含具有儲存體帳戶系統管理角色的過時帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您應定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。

* [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：啟用 Azure 串流分析和 Azure Active Directory 的診斷設定，將所有記錄傳送到 Log Analytics 工作區。 設定所需的警示 (例如，嘗試存取 Log Analytics 中) 的已停用秘密。

* [整合 Azure AD 記錄與 Azure 監視器記錄](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 Azure Active Directory 的風險和身分識別保護功能，設定對偵測到的串流分析資源相關可疑動作的自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行您組織的安全性回應。

* [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

* [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：不適用;Azure 串流分析不支援客戶加密箱。

* [正式推出的支援服務和案例](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指導** 方針：使用標籤來協助追蹤儲存或處理敏感性資訊的串流分析資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：將輸入、輸出和儲存體帳戶放在相同的訂用帳戶中，以隔離串流分析作業。 您可以限制串流分析，以控制您的應用程式和企業環境所需的串流分析資源存取層級。 您可以透過 Azure AD RBAC 來控制 Azure 串流分析的存取權。

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [了解 Azure 串流分析的輸入](./stream-analytics-add-inputs.md)

* [了解來自 Azure 串流分析的輸出](./stream-analytics-define-outputs.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針： Azure 串流分析資源尚未提供資料遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

* [如何保護 Azure 儲存體帳戶](../storage/blobs/security-recommendations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針： Azure 串流分析會將所有傳入和傳出的通訊加密，並支援 TLS 1.2。 內建的檢查點也會加密。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure 串流分析資源尚無法使用資料識別功能。 若需要達到合規性目標，請實作協力廠商解決方案。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 來控制使用者與服務的互動方式。

* [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：串流分析不會儲存傳入的資料，因為所有處理都是在記憶體中完成。 任何私用資料（包括串流分析所需保存的查詢和函式）都會儲存在設定的儲存體帳戶中。  (CMK) 使用客戶管理的金鑰來加密您的儲存體帳戶中待用的輸出資料。 即使沒有 CMK，串流分析也會自動在其基礎結構中採用最佳的加密標準，以加密及保護您的資料。

* [Azure 串流分析中的資料保護](./data-protection.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 azure 活動記錄，以針對 Azure 串流分析資源的生產實例進行變更時，建立警示。

* [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：遵循 Azure 資訊安全中心保護 Azure 串流分析資源的建議。

Microsoft 會在支援 Azure 串流分析的基礎系統上執行弱點管理。

* [瞭解 Azure 資訊安全中心建議](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：使用 (Azure 資訊安全中心提供的安全分數) 的預設風險評等。

* [瞭解 Azure 資訊安全中心安全分數](../security-center/secure-score-security-controls.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

* [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

* [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤 Azure 串流分析資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

此外，您也可以使用下列內建原則定義，使用 Azure 原則對可在客戶訂用帳戶 () s 中建立的資源類型進行限制：
- 不允許的資源類型
- 允許的資源類型

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指引**：不適用，這項建議主要用於計算資源和整體 Azure。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢/探索訂用帳戶 () 中的資源。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源和整體 Azure。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：在 ">mslearn-streamanalytics" 命名空間中使用 Azure 原則別名來建立自訂原則，以對 Azure 串流分析的設定進行審核或強制執行。 您也可以使用與 Azure 串流分析相關的內建原則定義，例如：應啟用 Azure 串流分析中的診斷記錄

* [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

* [Azure 原則的內建原則定義](../governance/policy/samples/built-in-policies.md)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：使用 Azure Repos 安全地儲存和管理您的程式碼，包括自訂 Azure 原則、Azure Resource Manager 範本、Desired State Configuration 腳本、使用者定義函數、查詢。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 TFS 整合的 Active Directory。

* [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops&preserve-view=true)

* [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：在 ">mslearn-streamanalytics" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：在 ">mslearn-streamanalytics" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 串流分析資源的設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：您的串流分析作業所使用之輸入或輸出資源的連線詳細資料，會儲存在設定的儲存體帳戶中。 加密您的儲存體帳戶，以保護您的所有資料。 此外，定期針對串流分析作業的輸入或輸出輪替認證。

* [Azure 串流分析中的資料保護](./data-protection.md)

* [針對串流分析作業的輸入和輸出替換登入認證](./stream-analytics-login-credentials-inputs-outputs.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：輸出的受控識別驗證可讓串流分析作業直接存取服務（包括 Power BI、儲存體帳戶），而不是使用連接字串。

* [使用受控識別向 Azure Data Lake Storage Gen1 驗證串流分析](./stream-analytics-managed-identities-adls.md)

* [使用受控識別來驗證您的 Azure 串流分析作業，以 Azure Blob 儲存體輸出](./blob-output-managed-identity.md)

* [使用受控識別來驗證您的 Azure 串流分析作業，以 Power BI](./powerbi-output-managed-identity.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：在支援 azure 服務的基礎主機上啟用 Microsoft 反惡意程式碼 (例如，Azure 串流分析) ，不過它不會在客戶內容上執行。

預先掃描即將上傳至 Azure 資源的任何內容，例如 App Service、串流分析、Blob 儲存體等等。Microsoft 無法在這些實例中存取您的資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：根據所選的輸出服務類型。 您可以根據輸出服務的建議指導方針，執行輸出資料的自動備份。 內部資料（包括使用者自訂函數、查詢、資料快照集）會儲存在設定的儲存體帳戶中，您可以定期進行備份。

您 Microsoft Azure 儲存體帳戶中的資料一律會自動進行複寫，以確保持久性和高可用性。 「Azure 儲存體」會複製您的資料，以保護該資料不受計劃性和非計劃性事件影響，包括暫時性硬體故障、網路或電力中斷和大規模天然災害。 您可以選擇在相同資料中心內、在相同地區的不同資料中心內、或跨不同區域複寫您的資料。

您也可以使用生命週期管理功能，將資料備份到封存層。 此外，針對儲存在儲存體帳戶中的備份啟用虛刪除。

* [Azure 串流分析中的資料保護](./data-protection.md#private-data-assets-that-are-stored)

* [瞭解 Azure 儲存體的冗余和 Service-Level 協定](../storage/common/storage-redundancy.md)

* [管理 Azure Blob 儲存體生命週期](../storage/blobs/storage-lifecycle-management-concepts.md)

Azure 儲存體 blob 的虛刪除： https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：內部資料（包括使用者自訂函數、查詢、資料快照集）會儲存在設定的儲存體帳戶中，您可以定期進行備份。

為了從儲存體帳戶支援的服務備份資料，有多種方法可供使用，包括使用 azcopy 或協力廠商工具。 Azure Blob 儲存體的固定儲存體可讓使用者以 WORM (一次寫入，多次讀取) 狀態儲存業務關鍵資料。 此狀態讓資料在使用者指定的間隔內不可清除，也不可修改。

* [Azure 串流分析中的資料保護](./data-protection.md#private-data-assets-that-are-stored)

* [開始使用 AzCopy](../storage/common/storage-use-azcopy-v10.md)

* [設定和管理 Blob 儲存體的不變性原則](../storage/blobs/storage-blob-immutability-policies-manage.md?tabs=azure-portal)

客戶管理/提供的金鑰可以在 Azure Key Vault 中使用 Azure CLI 或 PowerShell 進行備份。

* [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：定期執行備份資料的資料還原，以測試資料的完整性。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：儲存在 Azure 儲存體中的串流分析備份預設支援加密，而且無法關閉。 您應該將備份視為機密資料，並將相關的存取和資料保護控制項套用為此基準的一部分。

* [Azure 串流分析中的資料保護](./data-protection.md#private-data-assets-that-are-stored)

* [授權存取 Azure 儲存體中的資料](../storage/common/storage-auth.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。 您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

* [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

* [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

* [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

* [如何設定連續匯出](../security-center/continuous-export.md)

* [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。

* [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針： 

* [遵循 Microsoft 的參與規則，以確保您的滲透測試不違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [您可以在這裡找到有關 Microsoft 所管理的雲端基礎結構、服務和應用程式的 Microsoft 策略和執行紅色小組和即時網站滲透測試的詳細資訊](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)