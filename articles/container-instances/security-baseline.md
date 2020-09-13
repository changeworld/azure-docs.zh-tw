---
title: 容器實例的 Azure 安全性基準
description: 容器實例的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a26581b61a4b99bd11f48a3d431a1bb85148d66b
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393423"
---
# <a name="azure-security-baseline-for-container-instances"></a>容器實例的 Azure 安全性基準

容器實例的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導**方針： Azure 虛擬網路為您的 azure 和內部部署資源提供安全的私人網路。 將 Azure 容器實例中的容器群組與 Azure 虛擬網路整合。 

* [虛擬網路案例和資源-Azure 容器實例](./container-instances-virtual-network-concepts.md)

* [將容器執行個體部署至 Azure 虛擬網路](./container-instances-vnet.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導**方針：使用 Azure 資訊安全中心並補救網路保護建議，以協助保護您在 Azure 中的網路資源。 啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶進行流量稽核。

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [保護您的網路資源](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：將 Azure Web 應用程式防火牆 (WAF) 部署在 Azure 容器實例中裝載的重要 Web 應用程式之前，以便額外檢查連入流量。 啟用診斷設定以 WAF 記錄，並將其內嵌至儲存體帳戶、事件中樞或 Log Analytics 工作區。

* [如何部署 Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)



**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：在您的 Azure 虛擬網路上啟用 ddos 標準保護，以防範 ddos 攻擊。 使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意 IP 位址的通訊。 在每個組織的網路界限上部署 Azure 防火牆，並啟用威脅情報並設定為惡意網路流量的「警示和拒絕」。 使用 Azure 資訊安全中心的即時網路存取來設定 Nsg，將端點的公開限制在有限的期間內。 使用 Azure 資訊安全中心調適型網路強化，根據實際的流量和威脅情報，建議可限制埠和來源 Ip 的 NSG 設定。 

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/threat-protection.md)

* [瞭解 Azure 資訊安全中心適應性網路強化](../security-center/security-center-adaptive-network-hardening.md)

* [Azure 資訊安全中心即時網路存取控制](../security-center/security-center-just-in-time.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：如果使用網路安全性群組 (nsg) 搭配虛擬網路執行，請針對附加至委派至 Azure 容器實例之子網的 NSG 啟用 NSG 流量記錄。 將 NSG 流量記錄檔記錄到 Azure 儲存體帳戶以產生流程記錄。 如果需要調查異常活動，請啟用 Azure 網路監看員封包捕獲。

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導**方針：從支援使用承載檢查功能的 IDS/IPS 功能的 Azure Marketplace 選取供應專案。 如果基於酬載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。 Azure 防火牆威脅情報型篩選可以警示並拒絕傳向和來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路界限上部署您所選擇的防火牆解決方案，以偵測及/或拒絕惡意流量。

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md) 



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導**方針：針對已啟用受信任憑證的 HTTPS/SSL 的 web 應用程式部署 Azure 應用程式閘道。

* [如何部署應用程式閘道](../application-gateway/quick-create-portal.md)

* [如何將應用程式閘道設定為使用 HTTPS](../application-gateway/create-ssl-portal.md) 

* [瞭解 Azure web 應用程式閘道的第7層負載平衡](../application-gateway/overview.md)

* [公開容器群組的靜態 IP 位址](./container-instances-application-gateway.md)

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md)



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。 

您也可以使用應用程式安全性群組來協助簡化複雜的安全性設定。 應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然擴充功能，讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。 

* [瞭解和使用服務標記](../virtual-network/service-tags-overview.md) 

* [瞭解及使用應用程式安全性群組](../virtual-network/security-overview.md#application-security-groups)

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝關鍵環境成品（例如 Azure 資源管理員範本、azure RBAC 控制項和原則）來簡化大規模的 azure 部署。 您可以將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。 

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [適用于網路的 Azure 原則範例](/azure/governance/policy/samples/#network)

* [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：使用 Nsg 的標記，以及與網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的容器實例相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

* [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**：不過，Microsoft 會維護 Azure 資源的時間來源，您可以選擇管理計算資源的時間同步設定。 例如，在執行中的容器中執行時間同步處理命令。

* [如何設定 Azure 計算資源的時間同步處理](../virtual-machines/windows/time-sync.md)

* [在執行中的 Azure 容器執行個體內執行命令](./container-instances-exec.md)



**Azure 資訊安全中心監視**：目前無法使用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，來匯總 Azure 容器群組所產生的安全性資料。 在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶進行長期/封存儲存。

* [使用 Azure 監視器記錄的容器群組和實例記錄](./container-instances-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針： Azure 監視器會針對您登錄中的使用者驅動事件，收集先前稱為診斷記錄) 的資源記錄檔 (。 Azure 容器實例包含將容器群組記錄和事件資料和容器記錄傳送至 Azure 監視器記錄的內建支援。

* [使用 Azure 監視器記錄的容器群組和實例記錄](../container-registry/container-registry-diagnostics-audit-logs.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用。 此指導方針適用于 IaaS 計算資源。


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指引**：在 Azure 監視器中，根據貴組織的合規性規定來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

* [如何設定 Log Analytics 工作區的記錄保留期參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導**方針：分析和監視記錄中的異常行為，並定期查看結果。 使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。 

* [使用 Azure 監視器記錄的容器群組和實例記錄](./container-instances-log-analytics.md)

* [了解 Log Analytics 工作區](../azure-monitor/log-query/get-started-portal.md)

* [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針：使用 Log Analytics 工作區來監視和警示在安全性記錄和事件中找到的異常活動。 

* [使用 Azure 監視器記錄的容器群組和實例記錄](./container-instances-log-analytics.md)

* [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：如果需要在容器中執行，請提供您自己的反惡意程式碼解決方案和事件集合。 


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：視需要提供您自己的解決方案，以查詢容器中的 DNS 記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針：如有需要，請在執行中的容器實例中設定主控台記錄。

* [在執行中的 Azure 容器執行個體內執行命令](./container-instances-exec.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導**方針： Azure Active Directory (Azure AD) 有必須明確指派且可查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

如果您使用 Azure container registry 搭配 Azure 容器實例，請針對每個 Azure container registry，追蹤內建的系統管理員帳戶是否已啟用或停用。 在不使用時停用帳戶。

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Azure Container Registry 系統管理員帳戶](../container-registry/container-registry-authentication.md#admin-account)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Azure Active Directory (Azure AD) 沒有預設密碼的概念。 需要密碼的其他 Azure 資源會強制建立具有複雜性需求的密碼，以及最小密碼長度（視服務而定）。 您必須負責可能使用預設密碼的協力廠商應用程式和 Marketplace 服務。

如果您使用 azure container registry 搭配 Azure 容器實例，且 Azure container registry 的預設系統管理員帳戶已啟用，則會自動建立複雜密碼，且應輪替。 在不使用時停用帳戶。

* [Azure Container Registry 系統管理員帳戶](../container-registry/container-registry-authentication.md#admin-account)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

如果您使用 Azure container registry 搭配 Azure 容器實例，請建立啟用容器登錄之內建系統管理員帳戶的程式。 在不使用時停用帳戶。

* [瞭解 Azure 資訊安全中心身分識別和存取權](../security-center/security-center-identity-access.md)

* [Azure Container Registry 系統管理員帳戶](../container-registry/container-registry-authentication.md#admin-account)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針：盡可能使用 Azure Active Directory SSO，而不是針對個別服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

* [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導**方針：啟用 Azure Active Directory (AZURE AD) MFA (的多重要素驗證，並遵循) 身分識別和存取管理建議。

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指引**：使用已設定 MFA 的特殊權限存取 PAW (特殊權限存取工作站) 登入和設定 Azure 資源。

* [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指導**方針：當環境中發生可疑或不安全的活動時，使用 Azure Active Directory (Azure AD) 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

* [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)

* [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。

* [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

* [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針： Azure Active Directory (Azure AD) 提供記錄以協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

* [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指導**方針：您可以存取 Azure Active Directory (Azure AD) 登入活動、Audit 和風險事件記錄檔來源，讓您可以與任何安全性資訊和事件管理 (SIEM) /Monitoring 工具整合。

若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。

* [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 Azure Active Directory (Azure AD) 風險和身分識別保護功能，以針對偵測到與使用者身分識別相關的可疑動作，設定自動回應。

* [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

* [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**：目前無法使用;Azure 容器實例目前不支援客戶加密箱。

* [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指導**方針：使用資源標記來協助追蹤儲存或處理敏感性資訊的 Azure 容器實例。 

標記和版本容器映射，以協助追蹤儲存或處理敏感性資訊的影像。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [標記和版本設定容器映射的建議](../container-registry/container-registry-image-tag-version.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：針對開發、測試和生產環境執行個別的訂用帳戶和/或管理群組。 資源應該以 VNet/子網分隔，並適當地標記，並受到 NSG 或 Azure 防火牆的保護。 儲存或處理敏感性資料的資源應該要充分隔離。

* [在執行中的 Azure 容器執行個體內執行命令](./container-instances-exec.md)

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md) 
* [如何使用安全性設定建立 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [如何使用 Azure 防火牆設定警示或警示和拒絕](../firewall/threat-intel.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：在網路周邊部署自動化工具，以監視未經授權的機密資訊傳輸，並封鎖這類傳輸，同時警示資訊安全專業人員。 從 Azure 檔案共用和其他掛接到容器實例的磁片區，監視和封鎖未經授權的資訊傳輸。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md) 

* [在 Azure 容器執行個體中掛接 Azure 檔案共用](./container-instances-volume-azure-files.md)


**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：確定任何連接到您 Azure 容器群組的用戶端都能夠協商 TLS 1.2 或更高版本。 Microsoft Azure 資源預設會協商 TLS 1.2。

遵循 Azure 資訊安全中心待用加密及傳輸中加密的建議（適用時）。

* [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure 容器實例目前無法使用資料識別、分類和遺失防護功能。 標記可能會處理敏感性資訊的容器群組，並視需要執行協力廠商解決方案，以符合合規性需求。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制 Azure 容器實例資料和資源的存取權。 

* [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：根據預設，Azure 容器實例中的所有部署資料都會使用 Microsoft 管理的金鑰進行靜態加密。 （選擇性）使用您自己的金鑰管理加密， (客戶管理的金鑰) 。

* [瞭解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

* [使用 Azure 容器實例加密部署資料](./container-instances-encrypt-data.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄來建立您的容器群組和容器實例發生變更時的警示。 

* [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：執行解決方案來掃描私人登錄中的容器映射，並找出潛在的弱點。 遵循 Azure 資訊安全中心在 Azure Container Registry 中儲存的容器映射上執行弱點評定的建議。 （選擇性）從 Azure Marketplace 部署協力廠商解決方案，以執行映射弱點評定。

* [Azure 容器實例的安全性考慮](./container-instances-image-security.md)

* [Azure Container Registry 與安全性中心整合](../security-center/azure-container-registry-integration.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針： Microsoft 會在支援執行中容器的基礎系統上執行修補程式管理。

使用自訂或協力廠商解決方案來修補容器映射。 如果您將容器映射儲存在 Azure Container Registry 中，請執行 Azure Container Registry 工作，以根據安全性修補程式或基底 OS 映射中的其他更新，將容器登錄中的應用程式映射更新作業自動化。

* [Azure 容器實例的安全性考慮](./container-instances-image-security.md)

* [關於 Azure Container Registry 工作的基底映射更新](../container-registry/container-registry-tasks-base-images.md)


**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指導**方針：使用自訂或協力廠商解決方案來修補容器映射。 如果您將容器映射儲存在 Azure Container Registry 中，請執行 Azure Container Registry 工作，以根據安全性修補程式或基底 OS 映射中的其他更新，將容器登錄中的應用程式映射更新作業自動化。

* [Azure 容器實例的安全性考慮](./container-instances-image-security.md)

* [關於 ACR 工作的基底映射更新](../container-registry/container-registry-tasks-base-images.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針：依一致的間隔匯出影像掃描結果並比較結果，以確認已補救弱點。 如果您將容器映射儲存在 Azure Container Registry 中，請將您的登錄與 Azure 資訊安全中心整合，以定期掃描容器映射是否有弱點。 （選擇性）從 Azure Marketplace 部署協力廠商解決方案，以執行定期映射弱點掃描。

* [Azure 容器實例的安全性考慮](./container-instances-image-security.md)

* [Azure Container Registry 與安全性中心整合](../security-center/azure-container-registry-integration.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：如果您將容器映射儲存在 Azure Container Registry 中，請將您的登錄與 Azure 資訊安全中心整合，以定期掃描容器映射是否有弱點，以及將風險分類。 （選擇性）從 Azure Marketplace 部署協力廠商解決方案，以執行定期映射弱點掃描和風險分類。

* [Azure 容器實例的安全性考慮](./container-instances-image-security.md)

* [Azure Container Registry 與安全性中心整合](../security-center/azure-container-registry-integration.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

* [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

* [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導**方針：將標記套用至 Azure 容器實例，以及提供中繼資料的相關資源，以邏輯方式將它們組織成分類法。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指導**方針：您將需要根據組織的需求建立已核准 Azure 資源的清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。 確保已核准環境中的所有 Azure 資源。

* [使用 Azure 原則的 Azure container registry 審核合規性](../container-registry/container-registry-azure-policy.md)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：實行您自己的解決方案或協力廠商解決方案，以清查經核准之容器化應用程式的軟體。 

執行解決方案來掃描私人登錄中的容器映射，並找出潛在的弱點。 遵循 Azure 資訊安全中心中的建議，以針對儲存在 Azure Container Registry 中的容器映射執行弱點評定。 （選擇性）從 Azure Marketplace 部署協力廠商解決方案，以執行映射弱點評定。

監視 Azure 容器實例記錄中的異常行為，並定期查看結果。 使用 Azure 監視器的 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。

* [使用 Azure 監視器記錄的容器群組和實例記錄](./container-instances-log-analytics.md)

* [了解 Log Analytics 工作區](../azure-monitor/log-query/get-started-portal.md)

* [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

* [Azure 容器實例的安全性考慮](./container-instances-image-security.md)
* [Azure Container Registry 與安全性中心整合](../security-center/azure-container-registry-integration.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針： Azure 自動化可在部署、作業和解除委任工作負載和資源時，提供完整的控制權。 您可以執行自己的解決方案，以移除未經授權的 Azure 資源和軟體應用程式。

* [Azure 自動化簡介](../automation/automation-intro.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：標記和版本容器映射，以協助追蹤執行核准的應用程式的影像。
* [標記和版本設定容器映射的建議](../container-registry/container-registry-image-tag-version.md)


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則來限制您可以在環境中布建的服務。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指導**方針：標記和版本容器映射，以協助追蹤執行核准的應用程式的影像。
* [標記和版本設定容器映射的建議](../container-registry/container-registry-image-tag-version.md)


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsbrdiv"></a>6.11： <div>限制使用者透過指令碼來與 Azure Resource Manager 互動的能力<br></div>

**指導**方針：使用 azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。 

* [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導**方針：具有 Azure 容器實例存取權的所有使用者都可以在容器內執行腳本。

使用不同的 Azure 訂用帳戶或管理群組來管理和檢查 Azure 容器實例資源的存取權，或使用虛擬網路和 Nsg 或 Azure 防火牆來隔離資源。

* [在執行中的 Azure 容器執行個體內執行命令](./container-instances-exec.md)

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create.md)

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md)

* [如何使用安全性設定建立 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導**方針：商務營運所需但可能會對組織產生更高風險的軟體，應該隔離在其自己的虛擬網路內，並使用 Azure 防火牆或網路安全性群組進行充分保護。

* [在虛擬網路中部署-Azure 容器實例](./container-instances-vnet.md) 

* [如何使用安全性設定建立 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure Resource Manager 範本或匯出至 YAML 檔案，以維護已核准的容器群組設定。 使用 Azure 原則來維護相關 Azure 資源的安全性設定。

* [Azure Container Instances 中的容器群組](container-instances-container-groups.md#deployment)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：使用自訂或協力廠商解決方案來修補容器映射。 如果您將容器映射儲存在 Azure Container Registry 中，請執行 Azure Container Registry 工作，以根據安全性修補程式或基底 OS 映射中的其他更新，將容器登錄中的應用程式映射更新作業自動化。 

* [關於 Azure Container Registry 工作的基底映射更新](../container-registry/container-registry-tasks-base-images.md)



**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：執行解決方案來掃描私人登錄中的容器映射，並找出作業系統設定中的潛在弱點。 遵循 Azure 資訊安全中心中的建議，以針對儲存在 Azure Container Registry 中的容器映射執行弱點評定。 （選擇性）從 Azure Marketplace 部署協力廠商解決方案，以執行映射弱點評定。

使用自訂或協力廠商解決方案來修補容器映射。 如果您將容器映射儲存在 Azure Container Registry 中，請執行 Azure Container Registry 工作，以根據安全性修補程式或基底 OS 映射中的其他更新，將容器登錄中的應用程式映射更新作業自動化。 

* [Azure 容器實例的容器監視和掃描安全性建議](./container-instances-image-security.md)

* [Azure Container Registry 與安全性中心整合](../security-center/azure-container-registry-integration.md)
* [關於 Azure Container Registry 工作的基底映射更新](../container-registry/container-registry-tasks-base-images.md)



**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：在原始檔控制中安全地儲存和管理 ARM 範本、YAML 檔案和自訂 Azure 原則定義。

* [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導**方針：將容器映射儲存在 Azure Container Registry 中，並利用 Azure RBAC 來確保只有獲授權的使用者可以存取影像。

* [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [瞭解適用于 Container Registry 的 Azure RBAC](../container-registry/container-registry-roles.md)

* [如何設定 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統組態管理工具

**指導**方針：使用 Azure 原則警示、審核和強制執行系統設定。 此外，開發流程和管線以管理原則例外狀況。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指導**方針：不適用;此指導方針適用于 IaaS 計算資源。


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指導**方針：使用 Azure 資訊安全中心針對您的 Azure 資源執行基準掃描。

使用 Azure 原則來限制可在訂用帳戶中建立的資源類型 (s) 。

* [如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

* [使用 Azure 原則的 Azure container registry 審核合規性](../container-registry/container-registry-azure-policy.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導**方針：如果您使用 Azure Container Registry 儲存容器映射，請使用 Azure 資訊安全中心來執行適用于容器的 OS 和 Docker 設定的基準掃描。

* [了解 Azure 資訊安全中心容器建議](../security-center/container-security.md)


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。

* [如何與 Azure 受控識別整合](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [如何建立 Key Vault](../key-vault/secrets/quick-create-portal.md)

* [如何驗證 Key Vault](../key-vault/general/authentication.md)

* [如何指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-portal.md)

* [如何搭配 Azure 容器執行個體使用受控識別](./container-instances-managed-identity.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向支援 Azure AD authentication 的任何服務進行驗證，包括 Key Vault，而不需要在您的程式碼中提供任何認證。

* [如何設定受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [如何搭配 Azure 容器執行個體使用受控識別](./container-instances-managed-identity.md)



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：在支援 azure (服務的基礎主機上啟用 Microsoft Antimalware （例如，Azure 容器實例) ），但不會對客戶內容執行。

預先掃描即將上傳至非計算 Azure 資源的任何檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。 


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

支援 Azure 服務 (基礎主機上已啟用 Microsoft 反惡意程式碼，例如，Azure 容器實例) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：啟用 Azure 備份並設定備份來源 (例如掛接到容器群組) 的檔案共用，以及所需的頻率和保留期限。 

* [如何啟用 Azure 備份](../backup/index.yml)

* [在 Azure 容器執行個體中掛接 Azure 檔案共用](./container-instances-volume-azure-files.md)


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指導**方針：使用 Azure 命令列工具或 sdk，在 Azure Key Vault 中備份客戶管理的金鑰。

（選擇性）從某個登錄匯入至另一個登錄來備份容器映射。
* [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [將容器映像匯入到容器登錄](../container-registry/container-registry-import-images.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：使用 Azure 命令列工具或 sdk，在 Azure Key Vault 中測試已備份客戶管理金鑰的還原。

* [如何在 Azure 中還原 Azure Key Vault 金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [加密部署資料-Azure 容器實例](./container-instances-encrypt-data.md)



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導**方針：您可以在 Azure Key Vault 中啟用虛刪除，以防止遭到意外或惡意刪除的金鑰。

* [如何金鑰保存庫中啟用虛刪除](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫。

* [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md)

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [NIST 電腦安全性事件處理指南](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) \(英文\)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

此外， (明確地標示訂閱。 生產、非生產) 以及建立命名系統來清楚識別和分類 Azure 資源。


**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導**方針：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

* [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢事件，以確保問題已解決。

* [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

* [如何設定連續匯出](../security-center/continuous-export.md)

* [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

* [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指導**方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

* [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)


**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)
