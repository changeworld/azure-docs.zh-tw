---
title: 適用于 Microsoft Azure 客戶加密箱的 Azure 安全性基準
description: 適用于 Microsoft Azure 客戶加密箱的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9784079ce892a14d101cf5e7a03709687197ef91
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412896"
---
# <a name="azure-security-baseline-for-customer-lockbox-for-microsoft-azure"></a>適用于 Microsoft Azure 客戶加密箱的 Azure 安全性基準

適用于 Microsoft Azure 客戶加密箱的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性基準測試版本 1.0](../benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [安全性控制：網路安全性](../benchmarks/security-control-network-security.md)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引** ：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引** ：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與 Azure 客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：不適用;您無法將虛擬網路、子網或網路安全性群組與客戶加密箱建立關聯。 沒有要進行或監視的網路設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [安全性控制：記錄和監視](../benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針：不適用;Microsoft 會維護資源（例如客戶加密箱）所使用的時間來源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：會在 Azure 活動記錄中自動啟用和維護客戶加密箱的 Audit 記錄。 您可以藉由將資料從 Azure 活動記錄串流至 Log analytics 工作區，然後在其中執行研究和分析，來查看這項資料。

將客戶加密箱所產生的活動記錄上架到 Azure Sentinel 或其他 SIEM，以啟用集中式記錄匯總和管理。

* [客戶加密箱的 Audit 記錄](./customer-lockbox-overview.md#auditing-logs)

* [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：會在 Azure 活動記錄中自動啟用和維護客戶加密箱的 Audit 記錄。 您可以藉由將資料從 Azure 活動記錄串流至 Log analytics 工作區，然後在其中執行研究和分析，來查看這項資料。

* [客戶加密箱的 Audit 記錄](./customer-lockbox-overview.md#auditing-logs)

* [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，根據您組織的合規性法規，為與您的客戶加密箱相關聯的 log Analytics 工作區設定記錄保留期限。

* [如何設定記錄保留參數](../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：會在 Azure 活動記錄中自動啟用和維護客戶加密箱的 Audit 記錄。 您可以藉由將資料從 Azure 活動記錄串流至 Log analytics 工作區，然後在其中執行研究和分析，來查看這項資料。 分析和監視來自客戶加密箱要求的記錄，以取得異常行為。 使用 Azure Sentinel 工作區中的 [記錄] 區段來執行查詢，或根據您的客戶加密箱記錄來建立警示。

* [客戶加密箱中的 Audit 記錄](./customer-lockbox-overview.md#auditing-logs)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導** 方針：會在 Azure 活動記錄中自動啟用和維護客戶加密箱的 Audit 記錄。 您可以藉由將資料從 Azure 活動記錄串流至 Log analytics 工作區，然後在其中執行研究和分析，來查看這項資料。 分析和監視來自客戶加密箱要求的記錄，以取得異常行為。 使用 Azure Sentinel 工作區中的 [記錄] 區段來執行查詢，或根據您的客戶加密箱記錄來建立警示。

* [客戶加密箱中的 Audit 記錄](./customer-lockbox-overview.md#auditing-logs)

* [如何對 log analytics 記錄資料發出警示](../../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用;客戶加密箱不會處理或產生反惡意程式碼的相關記錄。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用;客戶加密箱不會處理或產生 DNS 相關的記錄。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [安全性控制：身分識別與存取控制](../benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導** ：維護具有您客戶加密箱要求之系統管理存取權的使用者帳戶清查。 您可以使用訂用帳戶 Azure 入口網站中的身分識別和存取控制 (IAM) 窗格，來設定 Azure 角色型存取控制 (Azure RBAC) 。 角色會套用至 Azure Active Directory 中的使用者、群組、服務主體和受控識別。

在客戶組織中，擁有 Azure 訂用帳戶擁有者角色的使用者會收到來自 Microsoft 的電子郵件，通知他們有任何擱置的存取要求。 針對客戶加密箱要求，這位人員是指定的核准者。

* [瞭解自訂角色](../../role-based-access-control/custom-roles.md)

* [如何設定活頁簿的 Azure RBAC](../../sentinel/quickstart-get-visibility.md)

* [瞭解客戶加密箱中的存取要求許可權](./customer-lockbox-overview.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure Active Directory 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制建立具有複雜性需求和密碼長度下限的密碼，這會因服務而有所不同。 您必須負責可能使用預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引** ：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專屬的系統管理帳戶，您可以使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：
- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

* [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](../../security-center/security-center-identity-access.md)

* [如何使用 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：不適用;客戶加密箱的存取權是透過 Azure 入口網站，並為具有擁有者的租使用者角色的帳戶保留。 不支援單一登入。

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory Multi-Factor Authentication，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

* [如何在 Azure 中啟用 MFA](../../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用具特殊許可權的存取工作站 (PAW) 搭配 Azure MULTI-FACTOR AUTHENTICATION (MFA) 啟用以登入和設定您的客戶加密箱要求。

* [特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [規劃雲端式 Azure Multi-Factor Authentication 部署](../../active-directory/authentication/howto-mfa-getstarted.md) \(部分機器翻譯\)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指導** 方針：當環境中發生可疑或不安全的活動時，請使用 AZURE ACTIVE DIRECTORY PRIVILEGED IDENTITY MANAGEMENT (PIM) 來產生記錄和警示。

此外，使用 Azure Active Directory 風險偵測來查看警示和報告有風險的使用者行為。

* [如何部署 Privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [了解 Azure AD 風險偵測](../../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組存取 Azure 入口網站。

* [如何在 Azure 中設定命名位置](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：在適用的情況下，使用 Azure Active Directory 作為中央驗證和授權系統。 Azure Active Directory 針對待用和傳輸中的資料使用強式加密來保護資料。 Azure Active Directory 也會 salts、雜湊及安全地儲存使用者認證。

* [如何建立和設定 Azure Active Directory 實例](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針： Azure Active Directory 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure Active Directory 存取評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。

* [瞭解 Azure Active Directory 報告](../../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure Active Directory 存取權評論](../../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指導** 方針：在適用的情況下，使用 Azure Active Directory 作為中央驗證和授權系統。 Azure Active Directory 針對待用和傳輸中的資料使用強式加密來保護資料。 Azure Active Directory 也會 salts、雜湊及安全地儲存使用者認證。

您可以存取 Azure Active Directory 登入活動、audit 和風險事件記錄檔來源，讓您可以與 Azure Sentinel 或協力廠商 SIEM 整合。

您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 中設定所需的記錄警示。

* [如何將 Azure 活動記錄整合到 Azure 監視器](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何讓 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：對於控制項平面上的帳戶登入行為偏差 (例如 Azure 入口網站) ，請使用 Azure Active Directory identity protection 和風險偵測功能，來設定偵測到與使用者身分識別相關的可疑動作的自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

* [如何查看 Azure Active Directory 具風險的登入](../../active-directory/identity-protection/overview-identity-protection.md)

* [如何設定及啟用 identity protection 風險原則](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何使 Azure Sentinel 上線](../../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：這項建議不適用於客戶加密箱。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [安全性控制：資料保護](../benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指導** 方針：此建議不適用;客戶加密箱不支援標記。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：不適用;客戶加密箱將會布建在與您要授與存取權的資源相同的訂用帳戶中。 沒有可保護或隔離的公用端點。 客戶加密箱要求存取權會授與在租使用者層級擁有擁有者角色的使用者。

* [瞭解客戶加密箱工作流程](./customer-lockbox-overview.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針： Microsoft 管理客戶加密箱的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

* [瞭解 Azure 中的客戶資料保護](./protection-customer-data.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：根據預設，Microsoft 會使用傳輸層安全性 (TLS) 通訊協定，在雲端服務與客戶之間流動時保護資料。 Microsoft 資料中心會與連線到 Azure 服務的用戶端系統進行交涉以達成 TLS 連線。 TLS 提供增強式驗證、訊息隱私權、完整性 (可偵測訊息竄改、攔截和偽造)、互通性、演算法彈性，以及方便部署和使用。

* [瞭解 Azure 中的傳輸加密](./encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視** ：目前無法使用

**責任** ：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：不適用;客戶加密箱本身不會保存任何客戶資料。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：客戶加密箱要求核准會授與在租使用者層級擁有擁有者角色的使用者。

* [瞭解客戶加密箱工作流程](./customer-lockbox-overview.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引** ：不適用，這項建議主要用於計算資源。 Microsoft 管理客戶加密箱的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

* [Azure 客戶資料保護](./protection-customer-data.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：不適用;客戶加密箱本身不會保存客戶資料。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：會在 Azure 活動記錄中自動啟用和維護客戶加密箱的 Audit 記錄。 使用 Azure 活動記錄來監視和偵測 Azure 客戶加密箱資源的變更。 在 Azure 監視器中建立警示，在發生重大資源變更時將會觸發此警示。

* [如何在客戶加密箱中啟用審核](./customer-lockbox-overview.md)

* [如何檢視及擷取 Azure 活動記錄事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure 監視器中建立警示](../../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [安全性控制：弱點管理](../benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：不適用;Microsoft 會在支援客戶加密箱的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視** ：不適用

**責任** ：Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針：不適用;Microsoft 會在支援客戶加密箱的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：不適用;Microsoft 會在支援客戶加密箱的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引** ：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可以透過 Azure Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

* [如何使用 Azure Resource Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

* [如何檢視您的 Azure 訂閱](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [瞭解 azure 角色型存取控制 (Azure RBAC) ](../../role-based-access-control/overview.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：不支援客戶加密箱的標記。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引** ：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，您也可以使用下列內建原則定義，使用 Azure 原則對可在客戶訂用帳戶 () s 中建立的資源類型進行限制：
- 不允許的資源類型
- 允許的資源類型

* [如何建立額外的 Azure 訂閱](../../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Resource Graph 建立查詢](../../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引** ：使用下列內建原則定義，利用 Azure 原則對可在您訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](../../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure 原則拒絕特定的資源類型](../../governance/policy/samples/index.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制使用者透過腳本與 AzureResources Manager 互動的能力

**指引** ：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引** ：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引** ：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [安全性控制：安全設定](../benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：不適用，客戶加密箱沒有可設定的安全性設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：不適用，客戶加密箱沒有可設定的安全性設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：不適用;客戶加密箱沒有可設定的安全性設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導** 方針：不適用;客戶加密箱沒有可設定的安全性設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指導** 方針：不適用;客戶加密箱沒有可設定的安全性設定。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引** ：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導** 方針：不適用;客戶加密箱要求的存取權僅限於裝載資源之 Azure 訂用帳戶的擁有者。 在以租使用者擁有者身分登入以外的地方存取客戶加密箱不需要密碼、秘密或金鑰。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：不適用;客戶加密箱不會利用受控識別。

* [支援受控識別的 Azure 服務](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引** ：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [安全性控制：惡意程式碼防禦](../benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引** ：不適用，這項準則主要用於計算資源。 Microsoft Antimalware 在支援客戶加密箱解決方案的基礎主機上啟用。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：在支援客戶加密箱等 Azure 服務的基礎主機上啟用 Microsoft Antimalware。

您必須負責預先掃描即將上傳至非計算 Azure 資源的任何內容。 Microsoft 無法存取客戶資料，因此無法代表您執行客戶內容的反惡意程式碼掃描。

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引** ：不適用，這項建議主要用於計算資源。 支援 Azure 服務的基礎主機上已啟用 Microsoft Antimalware，但不會對客戶內容執行。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [安全性控制：資料復原](../benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：不適用;客戶加密箱本身不會儲存客戶資料。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導** 方針：不適用;客戶加密箱本身不會儲存客戶資料。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：不適用;客戶加密箱本身不會儲存客戶資料。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導** 方針：不適用;客戶加密箱本身不會儲存客戶資料，也不會使用金鑰或密碼進行存取。

**Azure 資訊安全中心監視** ：不適用

**責任** ：不適用

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [安全性控制：事件回應](../benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引** ：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [如何設定 Azure 資訊安全中心內的工作流程自動化](../../security-center/security-center-planning-and-operations-guide.md)

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引** ：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引** ：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

* [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引** ：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心的安全性連絡人](../../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視** ：是

**責任** ：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引** ：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

* [如何設定連續匯出](../../security-center/continuous-export.md)

* [如何將警示串流至 Azure Sentinel](../../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引** ：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

* [如何設定工作流程自動化和 Logic Apps](../../security-center/workflow-automation.md)

**Azure 資訊安全中心監視** ：不適用

**責任** ：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [安全性控制：滲透測試和 Red Team 練習](../benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指導** 方針： 

* [遵循 Microsoft 的參與規則，以確保您的滲透測試不違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視** ：不適用

**責任** ：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../benchmarks/security-baselines-overview.md)