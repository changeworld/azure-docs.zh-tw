---
title: 適用于備份的 Azure 安全性基準
description: 適用于備份的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 68453341defd2d359e69e0ecf8a821464fead0d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744036"
---
# <a name="azure-security-baseline-for-backup"></a>適用于備份的 Azure 安全性基準

適用于備份的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導**方針：不適用;您無法將虛擬網路、子網或網路安全性群組與復原服務保存庫建立關聯。 備份 Azure 虛擬機器時，資料會透過 Azure 骨幹進行傳輸。 從內部部署機器進行備份時，會使用 Azure 中的特定端點來建立加密的通道，並使用認證來預先加密資料，然後再透過加密的通道進行傳送。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導**方針：不適用;您無法將虛擬網路、子網或網路安全性群組與復原服務保存庫建立關聯。 備份 Azure 虛擬機器時，資料會透過 Azure 骨幹進行傳輸。 從內部部署機器進行備份時，會使用 Azure 中的特定端點來建立加密的通道，並使用認證來預先加密資料，然後再透過加密的通道進行傳送。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指引**： Azure 備份（包括 Microsoft Azure 復原服務代理程式）所使用的端點都是由 Microsoft 管理。 您必須負責任何想要部署到內部部署系統的其他控制項。

- [瞭解 MARS 代理程式的網路和存取支援](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：不適用;您無法將虛擬網路、子網或網路安全性群組與復原服務保存庫建立關聯。 備份 Azure 虛擬機器時，資料會透過 Azure 骨幹進行傳輸。 從內部部署機器進行備份時，會使用 Azure 中的特定端點來建立加密的通道，並使用認證來預先加密資料，然後再透過加密的通道進行傳送。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指引**： Azure 備份（包括 Microsoft Azure 復原服務代理程式）所使用的端點都是由 Microsoft 管理。 您必須負責任何想要部署到內部部署系統的其他控制項。

- [瞭解 MARS 代理程式的網路和存取支援](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent#networking-and-access-support)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：如果您在 Azure 虛擬機器上使用 MARS 代理程式，請在 NSG 或 Azure 防火牆上使用 AzureBackup 服務標籤，以允許 Azure 備份的輸出存取。

- [備份 Azure Vm 中的 SQL Server 資料庫](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：不適用;Azure 備份（包括 Microsoft Azure 復原服務代理程式）所使用的端點都是由 Microsoft 管理。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：如果您在 Azure 虛擬機器上使用 MARS 代理程式，請將該 VM 與網路安全性群組建立關聯使用描述來指定規則的商務需求

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：如果您在受 NSG 或 azure 防火牆保護的 Azure 虛擬機器上使用 MARS 代理程式，請使用 Azure 活動記錄來監視 NSG 或防火牆的設定。 您可以在 Azure 監視器內建立警示，以在這些資源變更發生時觸發。

- [查看和取出 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [使用 Azure 監視器中建立、檢視及管理活動記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針：不適用;Microsoft 會針對記錄檔中的時間戳記，維護用於 Azure 資源的時間來源，例如 Azure 備份。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**.. 針對控制平面的審核記錄，啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

此外，透過 Azure 監視器內嵌記錄，以匯總 Azure 備份所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用儲存體帳戶進行長期/封存儲存體。 或者，您可以啟用 Azure Sentinel 或第三方安全性事件和事件管理 (SIEM)，然後使資料上線。

- [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [針對復原服務保存庫使用診斷設定](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指引**.. 針對控制平面的審核記錄，啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

此外，Azure 備份會傳送可收集的診斷事件，並用於分析、警示和報告。 您可以透過 Azure 入口網站來設定復原服務保存庫的診斷設定。 您可以將一或多個診斷事件傳送至儲存體帳戶、事件中樞或 Log Analytics 工作區。

- [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [針對復原服務保存庫使用診斷設定](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，根據貴組織的合規性規定，設定與 Azure 復原服務保存庫相關聯之 log Analytics 工作區的記錄保留週期。

- [如何設定記錄檔保留參數](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導**方針： Azure 備份在復原服務保存庫中提供內建的監視和警示功能。 這些功能不需要任何額外的管理基礎結構即可供使用。 您也可以使用 Azure 監視器來擴大監視和報告的規模。

啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中執行查詢，以搜尋詞彙、識別趨勢、分析模式，並根據復原服務保存庫可能收集到的活動記錄資料，提供許多其他深入解析。

- [監視 Azure 備份工作負載](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)

- [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何在 Azure 監視器中收集和分析 Log Analytics 工作區中的 Azure 活動記錄](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針： Azure 備份在復原服務保存庫中提供內建的監視和警示功能。 這些功能不需要任何額外的管理基礎結構即可供使用。 您也可以使用 Azure 監視器來擴大監視和報告的規模。

警示主要是通知使用者，讓他們可以採取相關動作的情況。 [備份警示] 區段會顯示 Azure 備份服務所產生的警示。 這些警示是由服務所定義，而且您無法自訂建立任何警示。

您也可以將 Log Analytics 工作區上架到 Azure Sentinel，因為它提供安全性協調流程自動化回應（攀升情況）解決方案。 如此可建立劇本 (自動化解決方案)，並用於修復安全性問題。 此外，您可以使用 Azure 監視器，在 Log Analytics 工作區中建立自訂記錄警示。

- [監視 Azure 備份工作負載](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [使用 Azure 監視器來建立、檢視及管理記錄警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：不適用;Azure 備份不會處理或產生與反惡意程式碼相關的記錄檔。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;Azure 備份不會處理或產生 DNS 相關的記錄檔。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： AZURE ACTIVE DIRECTORY （AD）具有必須明確指派且可查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

支援檔：

- [如何使用 PowerShell 取得 Azure AD 中的目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指引**：Azure AD 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制以複雜性需求和最小密碼長度來建立密碼，這會因服務而有所不同。 您必須負責可使用預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用的系統管理帳戶，您可以使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：應該從您的訂用帳戶移除具有擁有者許可權之已淘汰帳戶的多個擁有者

- [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [如何使用 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指引**：使用 Azure 應用程式註冊（服務主體）來抓取權杖，其可透過 API 呼叫來與您的復原服務保存庫進行互動。

- [如何呼叫 Azure REST Api](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [如何向 Azure AD 註冊您的用戶端應用程式（服務主體）](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure 復原服務 API 資訊](https://docs.microsoft.com/rest/api/recoveryservices/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導**方針：當您在 Azure 備份中執行重要作業時，您必須輸入 Azure 入口網站上可用的安全性 PIN 碼。 啟用 Multi-Factor Authentication 可多一道安全性。 只有具備有效 Azure 認證且從第二個裝置驗證的授權使用者，才能存取 Azure 入口網站。

- [Azure 備份中的多重要素驗證](https://docs.microsoft.com/azure/backup/backup-azure-security-feature)

- [規劃雲端式 Azure Multi-Factor Authentication 部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) \(部分機器翻譯\)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指引**：搭配使用特殊許可權存取工作站（PAW）與 Azure 多重要素驗證（MFA），並將其設定為登入並設定您的 Azure 備份啟用資源。

- [特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [規劃雲端式 Azure Multi-Factor Authentication 部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) \(部分機器翻譯\)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

- [如何部署 Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

- [了解 Azure AD 風險偵測](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取 Azure 入口網站。

- [如何在 Azure 中設定具名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指引**：使用 AZURE ACTIVE DIRECTORY （AD）做為 Azure 備份實例的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

- [如何設定 Azure 備份以使用 Azure AD 登入](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [如何建立和設定 AAD 執行個體](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**： AZURE ACTIVE DIRECTORY （AD）提供可協助您探索過時帳戶的記錄檔。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

- [瞭解 Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指引**：使用 AZURE ACTIVE DIRECTORY （AD）做為 Azure 備份實例的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您能夠與 Azure Sentinel 或協力廠商 SIEM 整合。

若要簡化此程式，您可以建立 Azure AD 使用者帳戶的診斷設定，並將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 中設定所需的記錄警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何讓 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AD）作為復原服務保存庫的中央驗證和授權系統。 如需控制平面（Azure 入口網站）上的帳戶登入行為偏差，請使用 Azure AD Identity Protection 和風險偵測功能來設定對偵測到與使用者身分識別相關之可疑動作的自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

- [如何設定 Azure 備份以使用 Azure AD 登入](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)

- [如何觀看 Azure AD 有風險的登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：目前無法使用;Azure 備份尚不支援客戶加密箱。

- [客戶加密箱支援的服務清單](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：備份 Azure IaaS vm 時，Azure 備份提供獨立和隔離的備份，以防止意外損毀原始資料。 備份會儲存在復原服務保存庫中，並進行內建的復原點管理。

針對開發、測試和生產復原服務保存庫，執行不同的訂用帳戶和/或管理群組。 資源應該以 VNet/子網分隔，並以適當的方式標記，並由 NSG 或 Azure 防火牆保護。 儲存或處理敏感性資料的資源應該充分地隔離。 針對虛擬機器儲存或處理敏感性資料，請在不使用時，執行原則和程式將其關閉。

支援檔：

- [Azure 備份概觀](https://docs.microsoft.com/azure/backup/backup-overview)

- [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：目前無法使用;資料識別、分類和遺失防護功能尚未提供 Azure 備份。

Microsoft 會管理 Azure 備份的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指引**：從伺服器到復原服務保存庫的備份流量會透過安全的 HTTPS 連結傳輸，並在儲存于保存庫時使用進階加密標準（AES）256加密。

- [瞭解 Azure 備份中的待用加密](https://docs.microsoft.com/azure/backup/backup-encryption)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針：目前無法使用;資料識別、分類和遺失防護功能尚未提供 Azure 備份。

Microsoft 會管理 Azure 備份的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：目前無法使用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**： azure 角色型存取控制（RBAC）可為 azure 提供更細緻的存取權管理。 RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。

Azure 備份提供三個內建角色來控制備份管理作業：「備份參與者」、「備份操作員」和「備份讀取器」。 您可以將備份內建角色對應到各種備份管理動作。

- [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

- [使用角色型存取控制來管理 Azure 備份復原點](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。 Microsoft 會管理 Azure 備份的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

- [Azure 客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針： Azure 備份支援待用資料的加密。 針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。 針對雲端工作負載，資料會使用儲存體服務加密（SSE）進行靜態加密。 Microsoft 不會解密在任何時間點所備份的資料。

當使用 MARS 代理程式進行備份，或使用以客戶管理的金鑰加密的復原服務保存庫時，只有您可以存取加密金鑰。 Microsoft 絕不會持有金鑰複本，也沒有存取金鑰的權限。 如果金鑰遺失，Microsoft 將無法復原備份資料。

- [瞭解 Azure 備份的待用加密](https://docs.microsoft.com/azure/backup/backup-encryption)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對生產 Azure 復原服務保存庫和其他重要或相關資源進行變更時，建立警示。

- [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指引**：尚未提供;Azure 資訊安全中心中的弱點評定尚未提供 Azure 備份。

Microsoft 所掃描和修補的基礎平台。 查看可供 Azure 備份的安全性控制，以減少與服務設定相關的弱點。

- [瞭解適用于 Azure 備份的安全性控制項](https://docs.microsoft.com/azure/backup/backup-security-controls)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：目前無法使用;Azure 資訊安全中心尚未支援 Azure 備份的安全性設定。

- [支援的 PaaS 服務 Azure 資訊安全中心清單](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

- [如何使用 Azure Resource Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，使用 Azure 原則來對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型施加限制：不允許的資源類型允許的資源類型

- [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指引**：定義已核准的 Azure 資源，以及適用於計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用 Azure 原則來限制可在您的訂用帳戶中建立的資源類型。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。  確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用 Azure 原則來對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型施加限制：不允許的資源類型允許的資源類型

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11： <div>限制使用者透過指令碼來與 Azure Resource Manager 互動的能力</div>

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：使用 Azure 原則定義和執行復原服務保存庫的標準安全性設定。 使用 "Azurerm.recoveryservices" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行復原服務保存庫的設定。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指引**：在 "azurerm.recoveryservices" 命名空間中使用內建的 Azure 原則定義和 Azure 原則別名，以建立自訂原則來警示、審查和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指引**：在 "azurerm.recoveryservices" 命名空間中使用內建的 Azure 原則定義和 Azure 原則別名，以建立自訂原則來警示、審查和強制執行系統組態。 使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在] 自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：設定 MARS 代理程式時，請將加密複雜密碼儲存在 Azure Key Vault 中。

- [如何建立 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [如何使用受控識別提供 Key Vault 驗證](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：不適用;Azure 備份不支援受控識別。

- [支援適用於 Azure 資源的受控識別服務](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：不適用;這項建議適用于計算資源。支援 Azure 服務的基礎主機（例如 Azure 備份）會啟用 Microsoft 反惡意程式碼，但不會在客戶內容上執行。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：在支援 Azure 服務的基礎主機（例如 Azure 備份）上啟用 Microsoft Antimalware，但不會在您的內容上執行。

預先掃描任何正在上傳到非計算 Azure 資源的檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。

使用適用于資料服務的 Azure 資訊安全中心威脅偵測來偵測已上傳至儲存體帳戶的惡意程式碼。

- [瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

- [瞭解 Azure 資訊安全中心的資料服務威脅偵測](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：不適用;這項建議適用于要備份的資源，而不是 Azure 備份本身。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指引**：本機多餘儲存體（LRS）會在資料中心的儲存體縮放單位中，複寫您的資料三次（它會建立三個數據複本）。 此資料的所有複本都存在於相同的區域內。 LRS 是保護您的資料免于本機硬體失敗的低成本選項。異地冗余儲存體（GRS）是預設和建議的複寫選項。 GRS 會將資料複寫到次要地區 (與來源資料主要位置距離數百英哩)。 GRS 的價格高於 LRS，但可為您的資料提供更高層級的持久性，即使遭受區域性中斷也不影響。

在 Azure Key Vault 內備份客戶管理的金鑰。

- [Azure 備份概觀](https://docs.microsoft.com/azure/backup/backup-overview)

- [如何在 Azure 中備份金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [瞭解 Azure 備份中的加密](https://docs.microsoft.com/azure/backup/backup-encryption#encryption-of-backup-data-using-customer-managed-keys)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：測試已備份的客戶管理金鑰的還原。

- [如何在 Azure 中還原金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指引**：針對內部部署備份，待用加密是使用您在備份至 Azure 時所提供的複雜密碼來提供。 針對 Azure VM，會使用「儲存體服務加密」(SSE) 對資料進行靜態加密。 您可以在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

- [如何在 Key Vault 中啟用虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

- [如何設定 Azure 資訊安全中心內的工作流程自動化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [您也可以利用 NIST 的「電腦安全性性」事件處理指南，協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

- [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

- [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

- [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指導**方針：[遵循 microsoft Engagement 規則，確保您的滲透測試不會違反 microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/overview) (機器翻譯)
- 深入了解 [Azure 安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)
