---
title: 適用于 Azure Container Registry 的 Azure 安全性基準
description: 適用于 Azure Container Registry 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 0004b7689b6ede2200e3fe4cb06bba2bbc168d6d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259407"
---
# <a name="azure-security-baseline-for-azure-container-registry"></a>適用于 Azure Container Registry 的 Azure 安全性基準

適用于 Azure Container Registry 的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指引**： azure 虛擬網路為您的 azure 和內部部署資源提供安全的私用網路。 藉由限制從 Azure 虛擬網路存取您的私用 Azure container registry，您可以確保只有虛擬網路中的資源會存取登錄。 針對跨單位案例，您也可以設定防火牆規則，只允許來自特定 IP 位址的登錄存取。 從防火牆後方，設定防火牆存取規則和服務標籤，以存取您的 container registry。

使用 Azure 虛擬網路或防火牆規則來限制對 Azure container registry 的存取：https://docs.microsoft.com/azure/container-registry/container-registry-vnet 

設定規則以存取防火牆後方的 Azure container registry：https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指引**：使用 Azure 資訊安全中心並補救網路保護建議，以協助保護您在 Azure 中的網路資源。 啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶進行流量稽核。

如何啟用 NSG 流量記錄：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

保護您的網路資源：https://docs.microsoft.com/azure/security-center/security-center-network-recommendations



**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用。 基準測試適用于裝載 web 應用程式的 Azure App Service 或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指引**：在您的虛擬網路上啟用 ddos 標準保護，以防範 ddos 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。  在組織的每個網路界限部署 Azure 防火牆，並啟用威脅情報並設定為「警示和拒絕」，以取得惡意的網路流量。

您可以使用 Azure 資訊安全中心的即時網路存取來設定 Nsg，以限制在有限的期間內，將端點暴露在核准的 IP 位址。 此外，您也可以使用 Azure 資訊安全中心的彈性網路強化，根據實際的流量和威脅情報來建議限制埠和來源 Ip 的 NSG 設定。

如何設定 DDoS 保護：https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

如何部署 Azure 防火牆：https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

瞭解 Azure 資訊安全中心整合式威脅情報：https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

瞭解 Azure 資訊安全中心的彈性網路強化：https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Azure 資訊安全中心即時網路存取控制：https://docs.microsoft.com/azure/security-center/security-center-just-in-time


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：針對連結至用來保護 Azure container registry 之子網的 NSG，啟用網路安全性群組 (NSG) 流量記錄。 您可以將 NSG 流量記錄錄製到 Azure 儲存體帳戶以產生流量記錄。 如果需要調查異常活動，請啟用 Azure 網路監看員封包捕獲。

如何啟用 NSG 流量記錄：https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用網路監看員：https://docs.microsoft.com/azure/network-watcher/network-watcher-create


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指引**：從支援具有承載檢查功能之 IDS/IPS 功能的 Azure Marketplace 中選取供應專案。 如果基於酬載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。 Azure 防火牆威脅情報型篩選可以警示並拒絕傳向和來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在您組織的每個網路界限上部署您選擇的防火牆解決方案，以偵測及/或拒絕惡意流量。

Azure Marketplace：https://azuremarketplace.microsoft.com/marketplace/?term=Firewall 

如何部署 Azure 防火牆：https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆設定警示：https://docs.microsoft.com/azure/firewall/threat-intel


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用。 基準測試適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指引**：對於需要存取容器登錄的資源，請使用 Azure Container Registry 服務的虛擬網路服務標籤，以定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則適當的 [來源] 或 [目的地] 欄位中指定服務標記名稱 "AzureContainerRegistry"，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

允許依服務標記存取：https://docs.microsoft.com/azure/container-registry/container-registry-firewall-access-rules#allow-access-by-service-tag


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指引**：使用 Azure 原則來定義和執行與您的 Azure 容器登錄相關聯之網路資源的標準安全性設定。 使用 "ContainerRegistry" 和 "Microsoft" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行容器登錄的網路設定。 

您可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝金鑰環境成品（例如 Azure Resource Manager 範本、RBAC 控制項和原則），以簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。

使用 Azure 原則來審查 Azure container registry 的相容性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何建立 Azure 藍圖： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指引**：客戶可以使用 Azure 藍圖，在單一藍圖定義中封裝金鑰環境成品（例如 Azure Resource Manager 範本、RBAC 控制項和原則），以簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。

如何建立 Azure 藍圖： https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與容器登錄相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

如何查看和取出 Azure 活動記錄事件：https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

如何在 Azure 監視器中建立警示：https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**：不過，Microsoft 會維護 Azure 資源的時間來源，您可以選擇管理計算資源的時間同步設定。

如何設定 Azure 計算資源的時間同步處理：https://docs.microsoft.com/azure/virtual-machines/windows/time-sync


**Azure 資訊安全中心監視**：目前無法使用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，以匯總 Azure container registry 所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶進行長期/封存儲存。

用於診斷評估和審核的 Azure Container Registry 記錄：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針： Azure 監視器收集 (先前稱為診斷記錄的資源記錄，) 用於登錄中的使用者驅動事件。 收集並取用此資料以審查登錄驗證事件，並提供登錄成品（例如提取和推播事件）的完整活動記錄，讓您可以診斷登錄的安全性問題。

用於診斷評估和審核的 Azure Container Registry 記錄：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用。 基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，根據貴組織的合規性規定來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

如何設定 Log Analytics 工作區的記錄保留參數： https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指引**：分析及監視異常行為的 Azure Container Registry 記錄，並定期查看結果。 使用 Azure 監視器的 Log Analytics 工作區來審查記錄，並對記錄資料執行查詢。

用於診斷評估和審核的 Azure Container Registry 記錄：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

瞭解 Log Analytics 工作區：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢： https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指引**：使用 Azure Log Analytics 工作區來監視和警示有關安全性記錄檔中的異常活動，以及與 Azure container registry 相關的事件。

用於診斷評估和審核的 Azure Container Registry 記錄：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

如何對 log analytics 記錄資料發出警示：https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指引**：不適用。 Azure Container Registry 不會處理或產生與反惡意程式碼相關的記錄檔。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指引**：不適用。 Azure Container Registry 是端點，而且不會起始通訊，而且服務不會查詢 DNS。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用。 基準測試適用于計算資源。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md) (機器翻譯)。

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針： Azure Active Directory (Azure AD) 具有必須明確指派且可查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

針對每個 Azure container registry，追蹤內建的系統管理員帳戶是否已啟用或停用。 不使用帳戶時，請將它停用。

如何使用 PowerShell 在 Azure AD 中取得目錄角色：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員：https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Container Registry 系統管理員帳戶：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Azure Active Directory (Azure AD) 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制使用複雜性需求和最小密碼長度來建立密碼，這會因服務而有所不同。 您必須負責可使用預設密碼的協力廠商應用程式和 Marketplace 服務。

如果已啟用 Azure container registry 的預設系統管理帳戶，則會自動建立複雜密碼，且應加以輪替。 不使用帳戶時，請將它停用。

Azure Container Registry 系統管理員帳戶：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，建立程式以啟用容器登錄的內建系統管理員帳戶。 不使用帳戶時，請將它停用。

瞭解 Azure 資訊安全中心身分識別和存取：https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure Container Registry 系統管理員帳戶：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針：盡可能使用 Azure Active Directory SSO，而不是設定個別服務的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

針對容器登錄的個別存取，請使用與 Azure Active Directory 整合的個別登入。

瞭解具有 Azure AD 的 SSO：https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

容器登錄的個別登入：https://docs.microsoft.com/azure/container-registry/container-registry-authentication#individual-login-with-azure-ad


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導**方針：啟用 Azure Active Directory (Azure AD) 的多重要素驗證 (MFA) 並遵循 Azure 資訊安全中心身分識別和存取管理建議。

如何在 Azure 中啟用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何監視 Azure 資訊安全中心內的身分識別和存取：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指引**：使用已設定 MFA 的特殊權限存取 PAW (特殊權限存取工作站) 登入和設定 Azure 資源。

瞭解特殊許可權存取工作站：https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA： https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Azure 資訊安全中心監視**：N/A

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指導**方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (Azure AD) 用於產生記錄檔和警示的安全性報告。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

如何識別標示有風險活動 Azure AD 使用者：https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何在 Azure 資訊安全中心中監視使用者的身分識別和存取活動：https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。

如何在 Azure 中設定命名位置：https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

如何建立和設定 Azure AD 實例：https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： Azure Active Directory (Azure AD) 會提供記錄檔，以協助探索過時的帳戶。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

瞭解 Azure AD 報告：https://docs.microsoft.com/azure/active-directory/reports-monitoring/

如何使用 Azure 身分識別存取權審查：https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指引**：您可以存取 Azure Active Directory (Azure AD) 登入活動、Audit 和風險事件記錄檔來源，這可讓您與任何安全性資訊和事件管理 (SIEM) /Monitoring 工具整合。

若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。

如何將 Azure 活動記錄整合到 Azure 監視器：https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 Azure Active Directory (Azure AD) 風險和身分識別保護功能，為偵測到與使用者身分識別相關的可疑動作設定自動回應。 

如何檢視 Azure AD 風險性登入： https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定和啟用身分識別保護風險原則：https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：無法使用;客戶加密箱目前不支援 Azure Container Registry。

客戶加密箱支援服務清單： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指引**：使用資源標記協助追蹤儲存或處理敏感資訊的 Azure 容器登錄。

在登錄中標記和版本容器映射或其他成品，以及鎖定映射或存放庫，以協助追蹤儲存或處理敏感資訊的影像。

如何建立和使用標記：https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

標記和版本設定容器映射的建議：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version

鎖定 Azure container registry 中的容器映射：https://docs.microsoft.com/azure/container-registry/container-registry-image-lock



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：針對開發、測試和生產環境，執行不同的容器登錄、訂用帳戶和/或管理群組。 儲存或處理敏感性資料的資源應該充分地隔離。

資源應該以虛擬網路或子網分隔，並適當地加以標記，並受到網路安全性群組的保護 (NSG) 或 Azure 防火牆。

如何建立額外的 Azure 訂用帳戶：https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組：https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用標籤： https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

使用 Azure 虛擬網路或防火牆規則來限制對 Azure container registry 的存取：https://docs.microsoft.com/azure/container-registry/container-registry-vnet

如何建立具有安全性設定的 NSG：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

如何部署 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

如何使用 Azure 防火牆來設定警示或警示和拒絕：

https://docs.microsoft.com/azure/firewall/threat-intel



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指引**：在網路周邊部署自動化工具，監視未經授權的機密資訊傳輸，並在警示資訊安全性專業人員時封鎖這類傳輸。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：確定任何連線到您 Azure Container Registry 的用戶端都能協商 TLS 1.2 或更高版本。 Microsoft Azure 資源預設會協商 TLS 1.2。

遵循 Azure 資訊安全中心的待用加密建議和傳輸中的加密（如果適用）。

瞭解使用 Azure 傳輸的加密：https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#encryption-of-data-in-transit



**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure Container Registry 尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**：使用 Azure Active Directory (AZURE AD) RBAC 來控制對 Azure container registry 中的資料和資源的存取。 

如何在 Azure 中設定 RBAC：https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Container Registry 角色和許可權：https://docs.microsoft.com/azure/container-registry/container-registry-roles



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針：如果需要在計算資源上符合規範，請執行協力廠商工具（例如自動化主機型資料遺失防護解決方案），以強制執行資料存取控制，即使資料是從系統複製而來。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

瞭解 Azure 中的客戶資料保護：https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指引**：在所有 Azure 資源上使用靜態加密。 根據預設，Azure container registry 中的所有資料都會使用 Microsoft 管理的金鑰在待用時加密。

瞭解 Azure 中的待用加密：https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest

Azure Container Registry 中客戶管理的金鑰：https://aka.ms/acr/cmk



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針： Azure 監視器收集 (先前稱為診斷記錄的資源記錄，) 用於登錄中的使用者驅動事件。 收集並取用此資料以審查登錄驗證事件，並在登錄成品（例如提取和提取事件）上提供完整的活動記錄，以便您可以診斷登錄的操作問題。

用於診斷評估和審核的 Azure Container Registry 記錄：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：請遵循在您的容器映射上執行弱點評定 Azure 資訊安全中心中的建議。 選擇性地從 Azure Marketplace 部署協力廠商解決方案，以執行映射弱點評量。

如何實行 Azure 資訊安全中心弱點評估建議：https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

Azure Container Registry 與資訊安全中心 (Preview) 整合：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針： Microsoft 會在支援 Azure Container Registry 的基礎系統上執行修補程式管理。

當偵測到來自作業系統和其他修補程式的基底映射更新時，自動更新容器映射。

關於 Azure Container Registry 工作的基底映射更新：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指引**：您可以使用協力廠商解決方案來修補應用程式映射。  此外，您也可以執行 Azure Container Registry 工作，以根據基底映射中的安全性修補程式或其他更新，將容器登錄中的應用程式映射更新作業自動化。

關於 ACR 工作的基底映射更新：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-base-images



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針：整合 AZURE CONTAINER REGISTRY (ACR) 與 Azure 資訊安全中心，以針對弱點定期掃描容器映射。 選擇性地從 Azure Marketplace 部署協力廠商解決方案，以執行週期性映射弱點掃描。

Azure Container Registry 與資訊安全中心 (Preview) 整合：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：整合 AZURE CONTAINER REGISTRY (ACR) 與 Azure 資訊安全中心，以便定期掃描容器映射的弱點，並將風險分類。 選擇性地從 Azure Marketplace 部署協力廠商解決方案，以執行週期性映射弱點掃描和風險分類。

Azure Container Registry 與資訊安全中心 (Preview) 整合：https://docs.microsoft.com/azure/security-center/azure-container-registry-integration



**Azure 資訊安全中心監視**：N/A

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

如何使用 Azure Resource Graph 建立查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何查看您的 Azure 訂用帳戶：https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0 

瞭解 Azure RBAC：https://docs.microsoft.com/azure/role-based-access-control/overview



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導**方針： Azure Container Registry 維護登錄中的中繼資料，包括影像的標記和資訊清單。 遵循適用于標記成品的建議做法。

關於登錄、存放庫和映射：https://docs.microsoft.com/azure/container-registry/container-registry-concepts

標記和版本設定容器映射的建議：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針： Azure Container Registry 維護登錄中的中繼資料，包括影像的標記和資訊清單。 遵循適用于標記成品的建議做法。

關於登錄、存放庫和映射：https://docs.microsoft.com/azure/container-registry/container-registry-concepts

標記和版本設定容器映射的建議：https://docs.microsoft.com/azure/container-registry/container-registry-image-tag-version



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指引**：根據您的組織需求，您必須建立已核准 Azure 資源的清查。  

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則來對可在訂用帳戶 (s) 中建立的資源類型施加限制。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。  確保已核准環境中的所有 Azure 資源。

使用 Azure 原則來審查 Azure container registry 的相容性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何設定和管理 Azure 原則：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢：https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：分析及監視異常行為的 Azure Container Registry 記錄，並定期查看結果。 使用 Azure 監視器的 Log Analytics 工作區來審查記錄，並對記錄資料執行查詢。

用於診斷評估和審核的 Azure Container Registry 記錄：https://docs.microsoft.com/azure/container-registry/container-registry-diagnostics-audit-logs

瞭解 Log Analytics 工作區：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

如何在 Azure 監視器中執行自訂查詢：https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針： Azure 自動化在部署、作業和解除委任工作負載與資源期間提供完整的控制權。  您可以執行您自己的解決方案，以移除未經授權的 Azure 資源。 Azure 自動化簡介：https://docs.microsoft.com/azure/automation/automation-intro


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用。 基準測試是針對計算資源所設計。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：利用 Azure 原則來限制您可以在環境中布建的服務。

使用 Azure 原則來審查 Azure container registry 的相容性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何拒絕具有 Azure 原則的特定資源類型：https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引**：不適用。 基準測試是針對計算資源所設計。



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11：限制使用者透過腳本與 AzureResources Manager 互動的能力

**指導**方針：使用作業系統特定設定或協力廠商資源，限制使用者在 Azure 計算資源內執行腳本的能力。

如何設定條件式存取以封鎖對 Azure 資源管理員的存取：https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導**方針：使用作業系統特定設定或協力廠商資源，限制使用者在 Azure 計算資源內執行腳本的能力。

例如，如何在 Windows 環境中控制 PowerShell 腳本執行：https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導**方針：商務營運所需但可能對組織產生較高風險的軟體，應該隔離在自己的虛擬機器和/或虛擬網路中，並使用 Azure 防火牆或網路安全性群組進行充分的保護。

如何建立虛擬網路：https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立具有安全性設定的 NSG：https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則或 Azure 資訊安全中心來維護所有 Azure 資源的安全性設定。

如何設定和管理 Azure 原則：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

使用 Azure 原則來審查 Azure container registry 的相容性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：利用 Azure 資訊安全中心建議的「修復您虛擬機器上安全性設定中的弱點」，以維護所有計算資源的安全性設定。

如何監視 Azure 資訊安全中心建議：https://docs.microsoft.com/azure/security-center/security-center-recommendations

如何補救 Azure 資訊安全中心建議：https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

使用 Azure 原則來審查 Azure container registry 的相容性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

瞭解 Azure 原則效果：https://docs.microsoft.com/azure/governance/policy/concepts/effects



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用。 基準測試適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

如何將程式碼儲存在 Azure DevOps：https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 檔：https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用。 基準測試適用于計算資源。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統組態管理工具

**指導**方針：使用 Azure 原則來警示、審查和強制執行系統設定。 此外，開發流程和管線以管理原則例外狀況。

使用 Azure 原則來審查 Azure container registry 的相容性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy

如何設定和管理 Azure 原則：https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引**：不適用。 基準測試適用于計算資源。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指引**：使用 Azure 資訊安全中心來執行 Azure 資源的基準掃描。

使用 Azure 原則來限制可在訂用帳戶 (s) 中建立的資源類型。

如何修復 Azure 資訊安全中心中的建議：https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations

使用 Azure 原則來審查 Azure container registry 的相容性：https://docs.microsoft.com/azure/container-registry/container-registry-azure-policy



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用。 基準測試適用于計算資源。


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：搭配 Azure Key Vault 使用受控服務識別，以簡化及保護雲端應用程式的秘密管理。

如何與 Azure 受控識別整合：https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Key Vault：https://docs.microsoft.com/azure/key-vault/quick-create-portal

如何使用受控識別提供 Key Vault 驗證：https://docs.microsoft.com/azure/key-vault/managed-identity

在 Azure Container Registry 工作中使用 Azure 管理的身分識別：https://docs.microsoft.com/azure/container-registry/container-registry-tasks-authentication-managed-identity


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指引**：在 Azure AD 中使用受控識別，以自動管理的身分識別來提供 Azure 服務。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

如何設定受控識別：https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

使用受控識別來向 Azure container registry 進行驗證：https://docs.microsoft.com/azure/container-registry/container-registry-authentication-managed-identity


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

如何設定認證掃描器：https://secdevtools.azurewebsites.net/helpcredscan.html


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：使用適用于 Azure 雲端服務和虛擬機器的 Microsoft Antimalware，持續監視及保護您的資源。 針對 Linux，請使用協力廠商反惡意程式碼解決方案。

如何設定雲端服務和虛擬機器的 Microsoft Antimalware：https://docs.microsoft.com/azure/security/fundamentals/antimalware


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：在支援 Azure 服務的基礎主機上啟用 Microsoft Antimalware (例如 Azure Container Registry) ，但不會在客戶內容上執行。

預先掃描任何正在上傳到非計算 Azure 資源的檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用。 基準測試適用于計算資源。 Microsoft 會處理基礎平臺的反惡意程式碼。


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：您 Microsoft Azure 容器登錄中的資料一律會自動複寫，以確保持久性和高可用性。 Azure Container Registry 會複製您的資料，以防止計畫和未計畫的事件進行保護

選擇性地異地複寫容器登錄，以在多個 Azure 區域中維護登錄複本。 

Azure Container Registry 中的異地複寫：https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指引**：選擇性地從一個登錄匯入到另一個登錄，以備份容器映射。

使用 Azure 命令列工具或 Sdk，在 Azure Key Vault 中備份客戶管理的金鑰。

將容器映射匯入至容器登錄：https://docs.microsoft.com/azure/container-registry/container-registry-import-images

如何在 Azure 中備份金鑰保存庫金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：使用 Azure 命令列工具或 sdk 在 Azure Key Vault 中測試已備份客戶管理金鑰的還原。

如何在 Azure 中還原 Azure Key Vault 金鑰：https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指引**：您可以在 Azure Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

如何在 Key Vault 中啟用虛刪除：https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md) (機器翻譯)。

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

如何設定 Azure 資訊安全中心內的工作流程自動化：https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

建立您自己的安全性事件回應程式的指引：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft 安全性回應中心的事件剖析：https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

客戶也可以利用 NIST 的「電腦安全性性」事件處理指南，協助建立自己的事件回應計畫：https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針： Azure 資訊安全中心指派每個警示的嚴重性，以協助您排定應先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導**方針：執行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

請參閱 NIST 的發行集：適用于 IT 計畫和功能的測試、訓練和練習程式指南：https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢事件，以確保問題已解決。

如何設定 Azure 資訊安全中心安全性連絡人：https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

如何設定連續匯出：https://docs.microsoft.com/azure/security-center/continuous-export

如何將警示串流至 Azure Sentinel：https://docs.microsoft.com/azure/sentinel/connect-azure-security-center


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用 Azure 資訊安全中心的工作流程自動化功能，以透過 "Logic Apps" 自動觸發對安全性警示和建議的回應。

如何設定工作流程自動化和 Logic Apps：https://docs.microsoft.com/azure/security-center/workflow-automation


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指導**方針：遵循 Microsoft Engagement 規則，確保您的滲透測試不會違反 microsoft 原則：https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以在這裡找到有關 Microsoft 管理之雲端基礎結構、服務和應用程式的 Microsoft 策略與執行的 Red 小組和即時網站滲透測試的詳細資訊：https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e



**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md) (機器翻譯)
- 深入了解 [Azure 安全性基準](../security/benchmarks/security-baselines-overview.md) (機器翻譯)
