---
title: 適用于 Azure Web 應用程式防火牆的 azure 安全性基準
description: Azure Web 應用程式防火牆安全性基準提供程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: edb184fa286eb6212f714c18830540e105ea3305
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92021172"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>適用于 Azure Web 應用程式防火牆的 azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview-v1.md) 的指引套用至 Azure Web 應用程式防火牆。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure Web 應用程式防火牆的相關指引來分組。 尚未排除適用于 Azure Web 應用程式防火牆的**控制項**。 

若要查看 Azure Web 應用程式防火牆如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure Web 應用程式防火牆安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：使用 Microsoft Azure Web 應用程式防火牆 (WAF) 來集中保護 web 應用程式，使其免于遭受常見的惡意探索和弱點攻擊，例如 SQL 插入式攻擊和跨網站腳本。 

- 偵測模式：使用此模式來學習網路流量、瞭解和檢查誤報。 它會監視並記錄所有威脅警示。 確定已選取並開啟診斷和 WAF 記錄。 請注意，WAF 在偵測模式中運作時，不會封鎖連入要求。
- 預防模式：封鎖規則偵測到的入侵和攻擊。 攻擊者會收到「403未經授權的存取」例外狀況，且連接已關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

使用 WAF 的偵測模式來基準網路流量。 判斷您的流量需求之後，請在預防模式中設定 WAF 來 bock 不想要的流量。

針對未受 WAF 保護的任何啟用 web 的資源，追蹤資訊安全中心的高嚴重性建議。  

- [Web 應用程式防火牆 CRS 規則群組和規則](ag/application-gateway-crs-rulegroups-rules.md) 

- [應用程式閘道上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [Front Door 上的 WAF 模式](afds/afds-overview.md#waf-modes)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：使用自訂規則搭配 Azure Web 應用程式防火牆 (WAF) 來允許和封鎖流量。 例如，來自某個 IP 位址範圍的所有流量都可以被封鎖。 將 Azure WAF 設定為在預防模式中執行，以封鎖規則偵測到的入侵和攻擊。 攻擊者會收到「403未經授權的存取」例外狀況，且連接已關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

- [應用程式閘道上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [Front Door 上的 WAF 模式](afds/afds-overview.md#waf-modes)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導**方針： Azure Web 應用程式防火牆 (WAF) 是 azure web 應用程式防護的核心元件。 使用 Azure WAF 來為 web 應用程式提供集中式保護，從 OWASP 前10個類別的已知攻擊簽章，使用預先設定的受控規則集來處理常見的攻擊和弱點。

使用規則和規則群組自訂 Azure WAF，以符合 web 應用程式需求並消除誤報。 為 WAF 背後的每個網站建立 Azure WAF 原則的關聯，以允許網站特定的設定。 Azure WAF 支援地理篩選、速率限制、Azure 管理的預設規則集規則。 您可以建立和自訂規則，以符合應用程式的需求。 

設定 Azure WAF 在將偵測模式中的網路流量基準化之後，于一段決定的時間內以預防模式執行。 Azure WAF 會封鎖預防模式中的規則所偵測到的入侵和攻擊。 攻擊者會收到「403未經授權的存取」例外狀況，且連接已關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

- [應用程式閘道上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [Front Door 上的 WAF 模式](afds/afds-overview.md#waf-modes)

- [Web 應用程式防火牆 CRS 規則群組和規則](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：在 Azure 訂用帳戶中建立、建立關聯，並以邏輯方式組織資源，並使用標記來偵測常見的應用程式錯誤 (例如 APACHE 和 IIS) 。 

將規則和規則群組套用至 Azure Web 應用程式防火牆 (根據套用的標記中繼資料 WAF) 原則。

- [應用程式閘道上的 WAF 原則](https://docs.microsoft.com/cli/azure/network/application-gateway/waf-policy?view=azure-cli-latest) 

- [Front Door 上的 WAF 原則](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：將標記用於與 Azure Web 應用程式防火牆相關聯的網路安全性群組 (WAF) Azure 應用程式閘道子網中，以及任何與網路安全性和流量相關的其他資源。 針對個別的網路安全性群組規則，使用 [描述] 欄位可針對允許進出網路流量的任何規則指定商務需求、持續時間等等。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

選擇 Azure PowerShell 或 Azure CLI，以根據其標記查閱或執行資源的動作。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

- [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

- [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的 Azure Web 應用程式防火牆相關的網路設定和資源變更， (WAF) 部署。 在 Azure 監視器中建立警示，在發生重大網路設定或資源的變更時，將會觸發這些警示。

- [如何檢視及擷取 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針：建立 Azure Web 應用程式防火牆 (WAF) 的網路規則，以允許使用適當的埠和通訊協定（例如埠 123 over UDP）來存取 NTP 伺服器。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：設定 Azure Web 應用程式防火牆 (WAF) 記錄傳送至中央安全性記錄管理解決方案，例如 Azure Sentinel 或協力廠商 SIEM。 這些記錄包括 Azure 活動、診斷和即時 WAF 記錄，接著可以在不同的工具中查看這些記錄，例如 Azure 監視器、Excel 和 Power BI。 Azure Web 應用程式防火牆記錄可讓您深入瞭解 Azure WAF 正在評估、比對和封鎖的資料。

Azure Sentinel 有內建的 Azure WAF 活頁簿，可概述 Azure WAF 上的安全性事件。 此活頁簿包含事件、相符和封鎖的規則，以及記錄在防火牆記錄中的所有專案。 此遙測可以用來啟動腳本自動化，以根據 Sentinel 所收集的 WAF 事件來通知或採取補救動作。

- [查看活動記錄](../azure-resource-manager/management/view-activity-logs.md)

- [應用程式閘道的診斷記錄](../application-gateway/application-gateway-diagnostics.md)

- [將資料從 Microsoft web 應用程式防火牆連接到 Azure Sentinel](/azure/sentinel/connect-microsoft-waf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：在您的 Azure Web 應用程式防火牆上啟用記錄 (WAF) 資源，以存取 audit、security 和診斷記錄。 Azure Web 應用程式防火牆會針對每個偵測到的威脅提供詳細報告，這些威脅可在設定的診斷記錄中取得。 活動記錄 (自動提供) 包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他有用的項目。

- [記錄總覽](ag/ag-overview.md#logging)

- [Azure 監視器記錄查詢總覽](../azure-monitor/log-query/log-query-overview.md)

- [Azure 平臺記錄總覽](../azure-monitor/platform/platform-logs-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：將 Azure Web 應用程式防火牆 (WAF) 記錄傳送至自訂儲存體帳戶，並定義保留原則。 使用 Azure 監視器根據您組織的合規性需求來設定 Log Analytics 工作區保留期限。
- [設定儲存體帳戶的監視](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針： Azure Web 應用程式防火牆 (WAF) 提供每個偵測到威脅的詳細報告。 記錄會與 Azure 診斷記錄整合，以提供有關進行審核和疑難排解的重要作業和錯誤的豐富資訊。 

Azure WAF 實例會與資訊安全中心整合，以傳送警示和健康情況資訊以供報告之用。 使用安全中心的建議來偵測未受保護的 web 應用程式，並保護這些易受攻擊的資源。 

Azure Sentinel 有內建的 WAF 防火牆事件活頁簿，可提供 WAF 上安全性事件的總覽。 這些包括事件、相符和封鎖的規則，以及記錄在防火牆記錄中的所有其他專案。

- [如何啟用 Azure 活動記錄的診斷設定](/azure/azure-monitor) 

- [如何啟用 Azure 應用程式閘道的診斷設定](../application-gateway/application-gateway-diagnostics.md)

- [監視 Azure Front Door 中的計量和記錄](../frontdoor/front-door-diagnostics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：啟用 Azure 活動記錄診斷設定，以及 azure WAF 的診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢以搜尋字詞、找出趨勢、分析模式，以及提供許多其他以所收集資料為基礎的見解。 根據 WAF 計量建立異常活動的警示。 例如，如果封鎖的要求數目超過 ' X '，則 ' Y '。

- [如何啟用 Azure 活動記錄的診斷設定](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何在 Azure 中建立警示](/azure/azure-monitor/learn/tutorial-response)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：將 Azure Web 應用程式防火牆 (WAF) 部署在重要的 Web 應用程式前面，以額外檢查連入流量。 

Azure WAF 為您的 web 應用程式提供集中式保護，免于遭受常見的惡意探索和弱點，並藉由檢查輸入的網路流量來封鎖攻擊，例如 SQL 插入式攻擊、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊。

- [如何部署 Azure WAF](ag/create-waf-policy-ag.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： Azure Active Directory (Azure AD) 有內建角色可供查詢，而且必須明確指派。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針： WAF 內沒有可用的本機系統管理員指派。 不過，可能會在環境中 Azure Active Directory (Azure AD) 系統管理員角色，以允許管理 Azure WAF 資源。
在使用可存取 Azure Web 應用程式防火牆 (WAF) 實例的專用系統管理帳戶時，最好建立標準作業程式。 使用資訊安全中心的身分識別和存取管理功能來監視系統管理帳戶的數目。

- [瞭解 Azure 資訊安全中心身分識別和存取權](../security-center/security-center-identity-access.md)

- [瞭解如何在適用於 PostgreSQL 的 Azure 資料庫中建立系統管理員使用者](../postgresql/howto-create-users.md#the-server-admin-account)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導**方針：啟用 Azure Active Directory (Azure AD) MULTI-FACTOR AUTHENTICATION (MFA) ，並遵循資訊安全中心的身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導**方針：使用特殊許可權存取工作站 (PAW) 與 MULTI-FACTOR AUTHENTICATION (MFA) 設定為登入及設定 Azure Web 應用程式防火牆 (WAF) 和相關資源。 

- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：當環境中發生可疑或不安全的活動時，使用 Azure Active Directory (Azure AD) 的安全性報告來產生記錄和警示。 使用資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：設定條件式存取原則的位置條件，以及管理您的命名位置。 

建立 IP 位址範圍的邏輯群組，或具有命名位置的國家/地區和區域。 將您的機密資源（例如 Azure Key Vault 秘密）存取限制為已設定的命名位置。

- [Azure Active Directory 條件式存取中的位置條件為何](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 使用強式加密來保護待用和傳輸中的資料，以及 salts、雜湊及安全地儲存使用者認證，藉此保護資料。
- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： Azure Active Directory (Azure AD) 提供記錄以協助探索過時的帳戶。 使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 定期審核使用者存取，以確保只有作用中的使用者可以繼續存取。

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導**方針：整合 Azure Active Directory (Azure AD) 登入活動、Audit 和風險事件記錄檔來源，以及任何 SIEM 或監視工具，例如 Azure Sentinel。

藉由建立 Azure Active Directory (Azure AD) 使用者帳戶，以及將審核記錄和登入記錄傳送至 Log Analytics 工作區的診斷設定，以簡化此程式。 在 Log Analytics 工作區中設定所需的警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory 的 (Azure AD) 風險和身分識別保護功能，來設定偵測到與使用者身分識別相關的可疑動作的自動回應。 將資料內嵌到 Azure Sentinel 以進行進一步的調查。

- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導**方針：使用標籤協助追蹤 Azure Web 應用程式防火牆 (WAF) 和儲存或處理敏感性資訊的相關資源。
- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：針對個別的安全性網域（例如，開發、測試和生產環境）使用不同的訂用帳戶和管理群組來執行隔離。 

使用 Azure Active Directory (Azure AD) 以角色為基礎的存取控制 (Azure RBAC) 來控制 Azure 資源的存取權。

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](/azure/governance/management-groups/create)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：加密傳輸中的所有機密資訊。 確定任何連線至 Azure Web 應用程式防火牆 (WAF) 實例和相關資源的用戶端都能夠協商 TLS 1.2 或更新版本。

遵循適用的待用加密及傳輸中加密的安全性中心建議。

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用角色型存取控制來控制資源的存取權

**指導**方針：使用 Azure Active Directory (Azure AD) 以角色為基礎的存取控制 (azure RBAC) 來控制 azure 資源的存取權。
- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：針對所有 azure 資源（包括 Azure Web 應用程式防火牆 (WAF) 和相關資源）使用待用加密。 Microsoft 建議讓 Azure 管理您的加密金鑰，但在某些情況下，您可以選擇管理您自己的金鑰。

- [瞭解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

- [如何設定客戶管理的加密金鑰](/azure/storage/common/storage-encryption-keys-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：設定 Azure Web 應用程式防火牆 (WAF) 在將偵測模式中的網路流量基準化之後，以預先決定的時間量為基準，以在預防模式中執行。 

在預防模式中，Azure WAF 會封鎖規則偵測到的入侵和攻擊。 攻擊者會收到「403 未經授權存取」例外狀況，且連線會關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

- [應用程式閘道與 Azure 資訊安全中心整合概觀](../application-gateway/application-gateway-integration-security-center.md#overview)

- [應用程式閘道上的 WAF 模式](ag/ag-overview.md#waf-modes)

- [Front Door 上的 WAF 模式](afds/afds-overview.md#waf-modes)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢或探索訂用帳戶內的所有資源，例如、計算、儲存體、網路、埠和通訊協定等等。 

確定您的租使用者中有適當的 (讀取) 許可權，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。 雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導**方針：在 Azure Web 應用程式防火牆上使用標記 (WAF) 原則。 標記可與資源相關聯，並以邏輯方式套用，以組織客戶訂用帳戶中對這些資源的存取。 

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤 Azure WAF 和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](/azure/governance/management-groups/create)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：建立已核准資源的清查，包括根據組織需求的設定。

使用 Azure 原則來限制可在訂用帳戶中建立的資源類型。 使用 Azure Resource Graph 來查詢及探索其訂用帳戶內的資源。 確認環境中的所有 Azure 資源都已核准。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型施加限制。
使用 Azure Resource Graph 來查詢或探索 Azure Web 應用程式防火牆 (在其訂用帳戶中 WAF) 資源。 確認環境中的所有 Azure WAF 和相關資源都已核准。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針：使用 Azure 原則來監視和移除未核准的 azure WAF 資源，以拒絕部署 azure WAF 或特定類型的 WAF，例如 Azure WAF V1 與 V2。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則來限制您可以在環境中布建的服務。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導**方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導**方針： Azure Web 應用程式防火牆 (WAF) 可透過網路、資源群組或訂用帳戶與不同的環境建立關聯，以隔離高風險的應用程式。

- [Azure 虛擬網路總覽](../virtual-network/virtual-networks-overview.md)

- [使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md)

- [訂用帳戶決策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：定義及執行與 Azure Web 應用程式防火牆相關之網路設定的標準安全性設定， (WAF) 部署。
使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure 應用程式閘道、虛擬網路、網路安全性群組的網路設定進行審核或強制執行，並使用內建原則定義。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在] 效果，在您的 Azure Web 應用程式防火牆上強制執行安全設定 (WAF) 和相關資源。 

使用 Azure Resource Manager 範本，維護您的組織所需的 Azure WAF 和相關資源的安全性設定。

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則和 Azure Resource Manager 範本。 

如果與 Azure DevOps Active Directory TFS Team Foundation Server 整合，則授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) 中定義之群組的許可權。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：使用內建的 Azure 原則定義以及 "Microsoft. Network" 命名空間中 Azure 原則的別名來建立自訂原則，以警示、審核和強制執行系統組態。 開發用於管理原則例外狀況的處理常式和管線。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [Azure 原則文件](/azure/governance/policy)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用內建的 Azure 原則定義以及 "Microsoft. Network" 命名空間中 Azure 原則的別名來建立自訂原則，以警示、審核和強制執行系統組態。 

使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在] 效果，自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [Azure 原則文件](/azure/governance/policy)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：使用 Azure Key Vault 安全地儲存憑證。 Azure Key Vault 是平臺管理的秘密存放區，您可以用來保護秘密、金鑰和 SSL 憑證。 

Azure 應用程式閘道支援與 Key Vault 整合，以使用連結到 HTTPS 所支援接聽程式的伺服器憑證。 

- [如何使用 Azure PowerShell 以 Key Vault 憑證設定 SSL 終止](../application-gateway/configure-keyvault-ps.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導**方針：執行認證掃描器來識別程式碼中的認證，也會鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。
- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：確定已啟用 Azure Key Vault 的虛刪除。 虛刪除可讓您復原已刪除的金鑰保存庫和保存庫物件，例如金鑰、秘密和憑證。

- [如何使用 Azure Key Vault 的虛刪除](/azure/key-vault/key-vault-soft-delete-powershell)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：為您的組織開發事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以進行事件後的評論。 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [使用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針：資訊安全中心會將嚴重性指派給每個警示，以協助排列應先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。
清楚地將訂用帳戶標示 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。
- [請參閱 NIST 的發行指南，以針對 IT 方案和功能進行測試、訓練和練習程式](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。
- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導**方針：使用連續匯出功能來匯出您的安全性中心警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 使用 Azure 資訊安全中心資料連線器，根據組織的需求將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用資訊安全中心的工作流程自動化功能，透過安全性警示和建議的「Logic Apps」來自動觸發回應。
- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。 

- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
