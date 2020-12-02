---
title: 適用于 Service Fabric 的 Azure 安全性基準
description: Service Fabric 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 9eab908d2767bcdc9305daa625c70be9b63b9756
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96485541"
---
# <a name="azure-security-baseline-for-service-fabric"></a>適用于 Service Fabric 的 Azure 安全性基準

適用于 Service Fabric 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。



## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：確定所有虛擬網路子網部署都已套用網路安全性群組，並使用應用程式的受信任埠和來源所特有的網路存取控制。

* [使用範本來部署 Azure 防火牆](../firewall/deploy-template.md)

* [使用 Azure 網路安全性群組 (Nsg) 來建立周邊網路 ](../security/fundamentals/service-fabric-best-practices.md#use-network-isolation-and-security-with-azure-service-fabric)

* [如何整合您的 Azure Service Fabric 叢集與現有的虛擬網路](./service-fabric-patterns-networking.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針：使用 Azure 資訊安全中心，並修復用於保護 Azure Service Fabric 叢集的虛擬網路、子網和網路安全性群組的網路保護建議。 啟用網路安全性群組 (NSG) 流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送到 Azure Log Analytics 工作區，並使用 Azure 流量分析來提供 Azure 雲端中流量的深入解析。 Azure 流量分析的一些優點是能夠將網路活動視覺化、找出作用點、找出安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何啟用及使用 Azure 流量分析](../network-watcher/traffic-analytics.md)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：提供前端閘道，以提供使用者、裝置或其他應用程式的單一輸入點。 Azure API 管理會直接與 Service Fabric 整合，讓您可以安全地存取後端服務、使用節流防止 DOS 攻擊，以及驗證 API 金鑰、JWT 權杖、憑證和其他認證。

請考慮將 Azure Web 應用程式防火牆 (WAF) 部署在重要的 Web 應用程式之前，以便額外檢查連入流量。 啟用診斷設定以 WAF 記錄，並將其內嵌至儲存體帳戶、事件中樞或 Log Analytics 工作區。

* [Service Fabric 搭配 Azure API 管理概觀](./service-fabric-api-management-overview.md)

* [整合內部 VNET 中的 API 管理與應用程式閘道](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

* [如何部署 Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：若要防範 DDoS 攻擊，請在部署 azure Service Fabric 叢集的虛擬網路上啟用 Azure DDoS 標準保護。 使用 Azure 資訊安全中心整合式威脅情報，以拒絕與已知惡意或未使用的網際網路 IP 位址的通訊。

* [如何設定 DDoS 保護](../ddos-protection/manage-ddos-protection.md)

* [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：啟用網路安全性群組 (NSG) 流量記錄，以連接到用來保護 Azure Service Fabric 叢集的子網的 NSG。 將 NSG 流量記錄檔記錄到 Azure 儲存體帳戶以產生流程記錄。 如果需要調查異常活動，請啟用 Azure 網路監看員封包捕獲。

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

* [使用流量分析將 NSG 流量記錄視覺化](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：從支援使用承載檢查功能的 IDS/IPS 功能的 Azure Marketplace 選取供應專案。 如果基於酬載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。 Azure 防火牆威脅情報型篩選可以警示並拒絕傳向和來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路界限上部署您所選擇的防火牆解決方案，以偵測及/或拒絕惡意流量。

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：針對已啟用受信任憑證的 HTTPS/SSL 的 web 應用程式部署 Azure 應用程式閘道。

* [如何部署應用程式閘道](../application-gateway/quick-create-portal.md)

* [如何將應用程式閘道設定為使用 HTTPS](../application-gateway/create-ssl-portal.md)

* [瞭解 Azure web 應用程式閘道的第7層負載平衡](../application-gateway/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針：使用虛擬網路服務標籤來定義網路安全性群組的網路存取控制， (NSG) 連接到您的 Azure Service Fabric 叢集部署所在的子網。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

* [虛擬網路服務標籤](../virtual-network/service-tags-overview.md)

* [Service Fabric 網路的最佳作法](./service-fabric-best-practices-networking.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：定義及執行與 Azure Service Fabric 叢集相關之網路資源的標準安全性設定。 使用 "ServiceFabric" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Service Fabric 叢集的網路設定進行審核或強制執行。

您也可以使用 Azure 藍圖，在單一藍圖定義中封裝關鍵環境成品，例如 Azure Resource Manager 範本、Azure RBAC 控制項和原則，以簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂閱、環境，以及透過版本控制來微調控制和管理。

* [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對網路安全性群組使用標籤 (nsg) ，以及與您的 Azure Service Fabric 叢集相關聯的網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure 命令列介面 (CLI) 根據其標記查閱或執行資源的動作。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的 Azure Service Fabric 部署相關的網路資源變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

* [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護 Azure Service Fabric 叢集元件的時間來源，您可能會更新計算部署的時間同步處理。

* [如何設定 Azure 計算資源的時間同步處理](../virtual-machines/windows/time-sync.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：您可以將 Azure Service Fabric 叢集上線，以 Azure 監視器匯總叢集所產生的安全性資料。 請參閱 Service Fabric 中的診斷問題和解決方案範例。

* [設定 Azure 監視器記錄與 Service Fabric 整合](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure Service Fabric 中設定監視容器的 Azure 監視器記錄](./service-fabric-tutorial-monitoring-wincontainers.md)

* [診斷常見的 Service Fabric 案例](./service-fabric-diagnostics-common-scenarios.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用 Service Fabric 叢集的 Azure 監視器，將其導向至 Log Analytics 工作區。 這會記錄所有 Azure Service Fabric 叢集節點的相關叢集資訊和 OS 計量。

* [設定 Azure 監視器記錄與 Service Fabric 整合](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure Service Fabric 中設定監視容器的 Azure 監視器記錄](./service-fabric-tutorial-monitoring-wincontainers.md)

* [如何將 Log Analytics 代理程式部署到您的節點](./service-fabric-diagnostics-oms-agent.md)

* [Log Analytics 記錄搜尋](../azure-monitor/log-query/log-query-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導** 方針：讓 Azure Service Fabric 叢集上線以 Azure 監視器。 確定所使用的 Log Analytics 工作區已根據您組織的合規性法規設定記錄保留期限。

* [設定 Azure 監視器記錄與 Service Fabric 整合](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure Service Fabric 中設定監視容器的 Azure 監視器記錄](./service-fabric-tutorial-monitoring-wincontainers.md)

* [如何將 Log Analytics 代理程式部署到您的節點](./service-fabric-diagnostics-oms-agent.md)

* [如何設定 Log Analytics 工作區保留期限](../azure-monitor/platform/manage-cost-storage.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：讓 Azure Service Fabric 叢集上線以 Azure 監視器。 確定所使用的 Log Analytics 工作區已根據您組織的合規性法規設定記錄保留期限。

* [設定 Azure 監視器記錄與 Service Fabric 整合](./service-fabric-diagnostics-oms-setup.md)

* [在 Azure Service Fabric 中設定監視容器的 Azure 監視器記錄](./service-fabric-tutorial-monitoring-wincontainers.md)

* [如何將 Log Analytics 代理程式部署到您的節點](./service-fabric-diagnostics-oms-agent.md)

* [如何設定 Log Analytics 工作區保留期限](../azure-monitor/platform/manage-cost-storage.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** 方針：使用 Azure Log Analytics 工作區查詢來查詢 azure Service Fabric 記錄。

* [Log Analytics 記錄搜尋](../azure-monitor/log-query/log-query-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：使用 Azure Log Analytics 工作區來監視和警示安全性記錄中的異常活動，以及與您的 Azure Service Fabric 叢集相關的事件。

* [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

* [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：根據預設，Windows Defender 會安裝在 Windows Server 2016 上。 如果您不是使用 Windows Defender，請參閱 Antimaleware 檔中的設定規則。 Linux 不支援 Windows Defender。

* [如需詳細資訊，請參閱 Windows Defender Windows Server 2016 上的防毒軟體](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：為 DNS 記錄執行協力廠商解決方案。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導** 方針：手動設定每個節點的主控台記錄。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導**：維護在叢集布建期間建立的本機系統管理客戶紀錄，以及在 Azure Service Fabric 叢集和您建立的任何其他帳戶期間所建立的帳戶。 此外，如果使用 Azure AD 整合，Azure AD 內建角色必須明確指派，因此可供查詢。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

此外，您也可以使用 Azure 資訊安全中心的身分識別和存取管理建議。

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [如何使用 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針：布建叢集時，Azure 會要求您為 web 入口網站建立新的密碼。 沒有要變更的預設密碼，不過您可以指定不同的密碼供入口網站存取。

* [在 Azure 入口網站中建立](./service-fabric-cluster-creation-via-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：整合 Azure Service Fabric 的驗證與 Azure Active Directory。 使用專用的系統管理帳戶來建立原則和程式。

此外，您也可以使用 Azure 資訊安全中心的身分識別和存取管理建議。

* [設定 Azure Active Directory 用戶端驗證](./service-fabric-tutorial-create-vnet-and-windows-cluster.md#set-up-azure-active-directory-client-authentication)

* [如何使用 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：盡可能使用 Azure Active Directory SSO，而不是針對個別服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

* [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure AD MFA，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：使用 paw (特殊許可權的存取工作站) 搭配多重要素驗證 (MFA) 設定為登入及設定您的 Azure Service Fabric 叢集和相關資源。

* [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。 此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

* [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [了解 Azure AD 風險偵測](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。

* [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AAD) 作為中央驗證和授權系統，以安全地存取 Azure Service Fabric 叢集的管理端點。 AAD 會對待用和傳輸中資料使用強式加密以保護資料。 AAD 也會 salts、雜湊並安全地儲存使用者認證。

* [如何建立和設定 AAD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [Service Fabric 用戶端驗證的設定 Azure Active Directory](./service-fabric-cluster-creation-setup-aad.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AAD) 驗證與您的 Azure Service Fabric 叢集。 AAD 提供記錄以協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期審核使用者的存取權，以確保只有適當的使用者可以繼續存取。

* [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="311-alert-on-account-login-behavior-deviation"></a>3.11：帳戶登入行為偏差的警示

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AAD) 登入和審核記錄，以監視是否嘗試存取停用的帳戶;這些記錄可以整合到任何協力廠商 SIEM/監視工具中。

您可以建立 AAD 使用者帳戶的診斷設定、將審核記錄和登入記錄傳送到 Azure Log Analytics 工作區，以簡化此程式。 在 Azure Log Analytics 工作區中設定所需的警示。

* [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指引**：使用 Azure AD 風險和身分識別保護功能設定自動回應，以偵測與使用者身分識別相關的可疑動作。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

* [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

* [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**：無法使用;Azure Service Fabric 尚不支援客戶加密箱。

* [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指導** 方針：在與您的 azure Service Fabric 叢集部署相關的資源上使用標籤，以協助追蹤儲存或處理敏感性資訊的 Azure 資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 資源應該以 VNet/子網分隔，並適當地標記，並受到 NSG 或 Azure 防火牆的保護。 儲存或處理敏感性資料的資源應該要充分隔離。 針對儲存或處理敏感性資料的虛擬機器，請在不使用時將原則和程式 (的) 將其關閉。

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [如何使用 Azure 防火牆設定警示或警示和拒絕](../firewall/threat-intel.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：在網路周邊部署自動化工具，以監視未經授權的機密資訊傳輸，並封鎖這類傳輸，同時警示資訊安全專業人員。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制項和

能力。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：加密傳輸中的所有機密資訊。 確定任何連線至 Azure 資源的用戶端都能夠協商 TLS 1.2 或更新版本。

遵循 Azure 資訊安全中心待用加密及傳輸中加密的建議（適用時）。

* [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Service Fabric 叢集安全性案例](./service-fabric-cluster-security.md)

* [Service Fabric TLS 設定的疑難排解指南](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/TLS%20Configuration.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**：不適用，這項建議主要用於設計用來儲存資料的非計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針：針對儲存或處理敏感性資訊的 Azure Service Fabric 叢集，請使用標記將叢集和相關資源標示為機密。 Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：在所有 Azure 資源上使用待用加密。 Microsoft 建議讓 Azure 管理您的加密金鑰，但在某些情況下，您可以選擇管理您自己的金鑰。

* [了解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

* [如何設定客戶管理的加密金鑰](../storage/common/customer-managed-keys-configure-key-vault.md)

* [針對 Windows 中的 Azure Service Fabric 叢集節點啟用磁片加密](./service-fabric-enable-azure-disk-encryption-windows.md)

* [針對 Linux 中的 Azure Service Fabric 叢集節點啟用磁片加密](./service-fabric-enable-azure-disk-encryption-linux.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄檔，以建立在對重要 Azure 資源進行變更時所產生的警示。

* [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：定期執行 Service Fabric 錯誤分析服務和混亂服務，以模擬整個叢集中的錯誤，以評估服務的可靠性和可靠性。

遵循 Azure 資訊安全中心在 Azure 虛擬機器和容器映射上執行弱點評定的建議。

使用協力廠商解決方案來執行網路裝置和 web 應用程式的弱點評定。 進行遠端掃描時，請勿使用單一、永久的系統管理帳戶。 請考慮針對掃描帳戶執行 JIT 布建方法。 掃描帳戶的認證應受保護、監視，且僅用於弱點掃描。

* [Service Fabric 錯誤分析服務簡介](./service-fabric-testability-overview.md)

* [在 Service Fabric 叢集中引發受控制的混亂](./service-fabric-controlled-chaos.md)

* [如何實行 Azure 資訊安全中心弱點評定建議](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針：在 Azure Service Fabric 叢集的虛擬機器擴展集上啟用自動 OS 映射升級。

或者，若要在進入生產環境之前先測試 OS 修補程式，請使用手動觸發程式進行擴展集的 OS 映射升級。 請注意，手動觸發程式選項不會提供內建的復原功能。 使用 Azure 自動化的更新管理監視 OS 修補程式。

* [Service Fabric 叢集節點的修補程式管理](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 虛擬機器擴展集的自動 OS 映射升級](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [如何藉由最新的擴展集模型將 VM 更新至最新狀態](../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

* [Azure 自動化更新管理概觀](../automation/update-management/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導** 方針：在 Azure Service Fabric 叢集的虛擬機器擴展集上啟用自動 OS 映射升級。 修補協調流程應用程式 (POA) 是一種替代解決方案，適用于裝載于 Azure 外部的 Service Fabric 叢集。 POA 可搭配 Azure 叢集使用，並有一些額外的裝載負擔。

* [Service Fabric 叢集節點的修補程式管理](./service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration)

* [Azure 虛擬機器擴展集的自動 OS 映射升級](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)

* [如何設定 Service Fabric 叢集的作業系統修補排程](./service-fabric-patch-orchestration-application.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針：依一致的間隔匯出掃描結果並比較結果，以確認已補救弱點。 使用 Azure 資訊安全中心建議的弱點管理建議時，您可能會進入所選解決方案的入口網站以查看歷史掃描資料。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指引**：使用常見的風險評分計畫 (例如，常見弱點評分系統) 或協力廠商掃描工具所提供的預設風險評等。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

* [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

* [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指引**：定義已核准的 Azure 資源，以及適用於計算資源的已核准軟體。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

不允許的資源類型

允許的資源類型

使用 Azure Resource Graph 來查詢/探索您的訂用帳戶 (s) 中的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：執行協力廠商解決方案，以監視未核准之軟體應用程式的叢集節點。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：使用 Azure Resource Graph 來查詢/探索所有資源 (例如計算、儲存體、網路、埠和通訊協定等， () 中的訂用帳戶內 ) ，包括 Azure Service Fabric 叢集。 移除您探索到的任何未經核准 Azure 資源。 針對 Azure Service Fabric 叢集節點，請執行協力廠商解決方案，以移除或警示未核准的軟體。

* [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針：針對 Azure Service Fabric 叢集節點，請執行協力廠商解決方案，以防止未經授權的軟體執行。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則來限制您可以在環境中布建的服務。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導** 方針：針對 Azure Service Fabric 叢集節點，請執行協力廠商解決方案，以防止未經授權的檔案類型執行。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。

* [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導** 方針：使用作業系統特定設定或協力廠商資源，以限制使用者在 Azure 計算資源內執行腳本的能力。

* [例如，如何控制 Windows 環境中的 PowerShell 腳本執行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：商務營運所需但可能會對組織產生更高風險的軟體，應該隔離在其自己的虛擬機器和/或虛擬網路內，並使用 Azure 防火牆或網路安全性群組來充分保護。

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：在 "ServiceFabric" 命名空間中使用 Azure 原則別名來建立自訂原則，以對 Service Fabric 叢集的網路設定進行審核或強制執行。

* [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導** 方針： Azure Service Fabric 作業系統映射是由 Microsoft 所管理和維護。 客戶負責執行叢集節點作業系統的安全設定。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在] 來強制執行 Azure Service Fabric 叢集和相關資源的安全設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導** 方針： Azure Service Fabric 由 Microsoft 管理及維護的叢集作業系統映射。 負責執行作業系統層級狀態設定的客戶。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

* [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導** 方針：如果使用自訂映射，請使用 azure 角色型存取控制 (azure RBAC) ，以確保只有獲得授權的使用者可以存取影像。 針對容器映射，請將它們儲存在 Azure Container Registry 中，並利用 Azure RBAC 來確保只有授權的使用者可以存取影像。

* [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [瞭解適用于 Container Registry 的 Azure RBAC](../container-registry/container-registry-roles.md)

* [如何設定 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：在 "ServiceFabric" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：在 "ServiceFabric" 命名空間中使用 Azure 原則別名來建立自訂原則，以對 Service Fabric 叢集的設定進行審核或強制執行。

* [如何查看可用 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導** 方針：使用 Azure 資訊安全中心對容器的 OS 和 Docker 設定執行基準掃描。

* [了解 Azure 資訊安全中心容器建議](../security-center/container-security.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。

* [使用適用于 Azure 的受控識別搭配 Service Fabric](./concepts-managed-identity.md)

* [針對新的 Service Fabric 叢集設定受控識別支援](./configure-new-azure-service-fabric-enable-managed-identity.md)

* [搭配 Service Fabric 的應用程式使用受控識別](./how-to-managed-identity-service-fabric-app-code.md)

* [Service Fabric 應用程式的 KeyVaultReference 支援](./service-fabric-keyvault-references.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：受控識別可用於 azure 部署的 Service Fabric 叢集，以及部署為 azure 資源的應用程式。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

* [使用適用于 Azure 的受控識別搭配 Service Fabric](./concepts-managed-identity.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導** 方針：如果使用任何與您的 Azure Service Fabric 部署相關的程式碼，您可以執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

使用 Azure Key Vault 自動輪替 Service Fabric 叢集憑證。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

* [Service Fabric 叢集中的憑證管理](./cluster-security-certificate-management.md#certificate-rotation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導** 方針：根據預設，Windows Defender 的防毒軟體會安裝在 Windows Server 2016 上。 某些 SKU 上會預設安裝使用者介面，但並不需要。

如果您沒有使用 Windows Defender，請參閱您的反惡意程式碼文件，以了解設定規則。 Linux 不支援 Windows Defender。

* [瞭解 Windows Server 2016 上的 Windows Defender 防毒軟體](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：不適用，這項建議主要用於設計用來儲存資料的非計算資源。 支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Service Fabric) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保會更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於設計用來儲存資料的非計算資源。 支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Service Fabric) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針： Service Fabric 中的備份和還原服務可讓您輕鬆自動備份儲存在具狀態服務中的資訊。 定期備份應用程式資料是防止資料遺失和服務無法使用的基本原則。 Service Fabric 提供一項選用的備份與還原服務，可讓您無須撰寫任何額外的程式碼，即可設定具狀態 Reliable Services (包括「動作項目服務」) 的定期備份。 它也可以協助還原先前建立的備份。

* [Azure Service Fabric 叢集中的定期備份和還原](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：啟用 Service Fabric 叢集中的備份還原服務，並建立備份原則，定期和隨選備份具狀態服務。 Azure Key Vault 內備份客戶管理的金鑰。

* [Azure Service Fabric 叢集中的定期備份和還原](./service-fabric-backuprestoreservice-quickstart-azurecluster.md)

* [在 Azure Service Fabric 中了解定期備份設定](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：定期檢查備份設定資訊和可用的備份，以確保能夠從備份還原服務執行還原。 測試已備份之客戶管理金鑰的還原。

* [在 Azure Service Fabric 中了解定期備份設定](./service-fabric-backuprestoreservice-configure-periodic-backup.md)

* [在 Azure Service Fabric 中還原備份](./service-fabric-backup-restore-service-trigger-restore.md)

* [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針： Service Fabric 備份還原服務的備份會使用您訂用帳戶中的 Azure 儲存體帳戶。 Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰來加密儲存體資料。

如果您使用客戶管理的金鑰，請確定已啟用 Key Vault 中的 Soft-Delete，以防止金鑰遭到意外或惡意刪除。

* [Azure 儲存體待用加密](../storage/common/storage-service-encryption.md)

* [如何金鑰保存庫中啟用虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導** 方針：為您的組織開發事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以進行事件後的評論。

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [使用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導** 方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外，使用標記標記訂用帳戶，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料。 您必須負責根據發生事件的 Azure 資源和環境的重要性來排定補救警示的優先順序。

* [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

* [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

* [適用于 IT 方案和功能之測試、訓練和練習程式的 NIST 指南](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

* [如何設定連續匯出](../security-center/continuous-export.md)

* [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

* [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 對於紅隊和即時網站滲透測試的策略和執行方法。

* [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)