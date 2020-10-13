---
title: 虛擬網路的 Azure 安全性基準
description: 虛擬網路安全性基準提供程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: virtual-network
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: c34ace92fffee3c135cb05e07f06d885751bbce5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91629738"
---
# <a name="azure-security-baseline-for-virtual-network"></a>虛擬網路的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview-v1.md) 的指引套用至 Azure 虛擬網路。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 Azure 虛擬網路的相關指引來分組。 尚未排除適用于 Azure 虛擬網路的**控制項**。

若要瞭解 Azure 虛擬網路如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 Azure 虛擬網路安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導**方針：使用資訊安全中心，並遵循網路保護建議來協助保護您在 Azure 中的網路資源。 

將網路安全性群組流量記錄傳送至 Log Analytics 工作區，並使用流量分析來提供 Azure 雲端流量的深入解析。 流量分析可讓您將網路活動視覺化、找出作用點、找出安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。 

使用 Azure 監視器記錄來提供您環境的見解。 工作區應用來 collate 和分析資料，並可與其他 Azure 服務（例如 Application Insights 和安全性中心）整合。 

選擇要傳送至 Azure 儲存體帳戶或事件中樞的資源記錄。 您也可以使用不同的平臺來分析記錄。 

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

- [瞭解安全性中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：在您的 Azure 虛擬網路上啟用分散式阻絕服務 (DDoS) 標準保護，以防範 ddos 攻擊。

在每個組織的網路界限上部署 Azure 防火牆，並啟用以威脅情報為基礎的篩選，並設定為惡意網路流量的「警示和拒絕」。

使用安全中心的威脅防護功能來偵測與已知惡意 IP 位址的通訊。

針對網路安全性群組設定套用資訊安全中心的彈性網路強化建議，以根據實際流量和威脅情報來限制埠和來源 Ip。 

- [使用 Azure 入口網站管理 Azure DDoS 保護標準](manage-ddos-protection.md)

- [以 Azure 防火牆威脅情報為主的篩選](../firewall/threat-intel.md)

- [安全性中心內的威脅防護](/azure/security-center/threat-protection)

- [Azure 資訊安全中心中的適應性網路強化](../security-center/security-center-adaptive-network-hardening.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針：除了常用的封包捕獲工具外，還會使用 VPN 閘道的封包捕獲來記錄網路封包。 

您也可以查看以代理程式為基礎或 NVA 解決方案，以提供終端機存取點 (透過 Azure Marketplace 供應專案中提供的封包仲介夥伴解決方案，來) 或網路可見度功能。

- [設定 VPN 閘道的封包捕獲](../vpn-gateway/packet-capture.md) 

- [網路虛擬裝置合作夥伴](https://azure.microsoft.com/solutions/network-appliances)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導**方針：在已啟用威脅情報的虛擬網路上使用部署的 Azure 防火牆。 使用以 Azure 防火牆威脅情報為基礎的篩選來警示或拒絕進出已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。 

您也可以從支援具有承載檢查功能之 IDS/IPS 功能的 Azure Marketplace 選取適當的供應專案。

在每個組織的網路界限上部署您所選擇的防火牆解決方案，以偵測及/或拒絕惡意流量。

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

- [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，可以使用服務標記來取代特定的 IP 位址。 藉由指定服務標籤名稱來允許或拒絕對應服務的流量 (例如，在規則的適當來源或目的地欄位中 ApiManagement) 。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

使用應用程式安全性群組有助於簡化複雜的安全性設定。 應用程式安全性群組可讓您將網路安全性設定為應用程式結構的自然延伸。 這可讓您將虛擬機器分組，並定義以這些群組為基礎的網路安全性原則。

- [瞭解和使用服務標記](service-tags-overview.md)

- [瞭解及使用應用程式安全性群組](/azure/virtual-network/security-overview#application-security-groups)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則定義和實行網路資源的標準安全性設定，並查看用於執行的內建網路原則定義。

請參閱安全中心的預設原則，其中包含與您虛擬網路相關的可用安全性建議。

使用 Azure 藍圖藉由在單一藍圖定義中封裝關鍵環境成品（例如 Azure Resource Manager 範本、角色型存取控制 (Azure RBAC) 指派和原則）來簡化大規模的 Azure 部署。 您可以將 Azure 藍圖套用至新的訂用帳戶，以透過版本控制進行微調控制和管理。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [適用于網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network) 

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

- [在 Azure 資訊安全中心中啟用監視](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Security%20Center/AzureSecurityCenter.json)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：針對網路安全性群組和其他與網路安全性和流量相關的資源使用標記。 使用 [描述] 欄位來指定任何規則的商務需求、持續時間和其他資訊，以允許網路的流量流向個別的網路安全性群組規則。
使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

選擇 Azure PowerShell 或 Azure CLI，以根據其標記查閱或執行資源的動作。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

- [如何建立虛擬網路](quick-create-portal.md)

- [如何建立具有安全性設定的 NSG](tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視資源設定，並偵測對您虛擬網路的變更。 在 Azure 監視器中建立警示，在發生重大資源變更時就會觸發此警示。

- [如何檢視及擷取 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：啟用 Azure 監視器以存取您的 audit and activity 記錄，其中包括事件來源、日期、使用者、時間戳記、來源位址、目的地位址和其他有用的元素。 

在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來取得長期/封存儲存體。
或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [查看和取出 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：啟用 Azure 監視器以存取您的 audit and activity 記錄，其中包括事件來源、日期、使用者、時間戳記、來源位址、目的地位址和其他有用的元素。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md) 

- [查看和取出 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：在 Azure 監視器中，根據貴組織的合規性規定來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶來儲存安全性記錄檔儲存保留的長期/封存儲存體。

- [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：分析和監視記錄中的異常行為，並定期查看結果。 使用 Azure 監視器的 Log Analytics 工作區來查詢和執行分析，並將 Azure 儲存體帳戶用於長期/封存儲存體。 

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

- [了解 Log Analytics 工作區](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：搭配使用資訊安全中心與 Log Analytics 工作區來監視和警示在安全性記錄和事件中找到的異常活動。

或者，您可以啟用資料並將其上架至 Azure Sentinel 或協力廠商 SIEM 以進行警示。

- [如何在安全中心管理警示](../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：根據您的組織需求，針對 DNS 記錄解決方案的 Azure Marketplace 來執行協力廠商解決方案。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針：使用 Azure Active Directory (Azure AD) 可以明確指派且可供查詢的內建系統管理員角色。 

使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用資訊安全中心的身分識別和存取管理來監視系統管理帳戶的數目。

使用適用于 Microsoft 服務和 Azure Resource Manager 的 Azure AD Privileged Identity Management 特殊許可權角色，來啟用及時/夠足夠的存取。 

- [深入瞭解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導**方針：使用 SSO 搭配 Azure Active Directory (Azure AD) ，而不是為每個服務設定個別的獨立認證。 使用資訊安全中心的身分識別和存取管理建議。

- [Azure Active Directory 中的應用程式單一登入](../active-directory/manage-apps/what-is-single-sign-on.md) 

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導**方針：啟用 Azure Active Directory (Azure AD) MULTI-FACTOR AUTHENTICATION (MFA) ，並遵循資訊安全中心的身分識別和存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md) 

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指導**方針：使用特殊許可權存取工作站 (PAW) 與 MULTI-FACTOR AUTHENTICATION (MFA) 設定為登入和存取 Azure 網路資源。

- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：利用 Azure Active Directory (Azure AD) 風險偵測，以針對具風險的使用者行為來查看警示和報告。 

使用動作群組將安全中心的風險偵測警示內嵌至 Azure 監視器並設定自訂警示/通知。

- [瞭解 (可疑活動) 的安全性中心風險偵測 ](/azure/active-directory/reports-monitoring/concept-risk-events) 

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) 

- [如何設定自訂警示和通知的動作群組](../azure-monitor/platform/action-groups.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。

- [如何在 Azure 中設定命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory (Azure AD) 作為服務的中央驗證和授權系統。 Azure AD 使用強式加密來保護待用和傳輸中的資料，以及 salts、雜湊及安全地儲存使用者認證，藉此保護資料。  

- [如何建立和設定 Azure AD 實例](../active-directory-domain-services/tutorial-create-instance.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導**方針：使用 Azure Active Directory (Azure AD) 提供記錄，以協助探索過時的帳戶。 

您可以執行 Azure 身分識別存取審查，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 應定期檢查使用者存取權，以確保只有作用中的使用者可以繼續存取。

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring/)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導**方針：根據您的存取權，整合 Azure Active Directory (Azure AD) 登入活動、Audit 和風險事件記錄檔來源，以及任何 SIEM 或監視工具。 

建立 Azure Active Directory 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 工作區中設定任何所需的警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory 的 (Azure AD) 風險和身分識別保護功能，以針對偵測到與您虛擬網路的使用者身分識別相關的可疑動作，設定自動回應。 將資料內嵌到 Azure Sentinel 以進行進一步的調查。

- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：加密傳輸中的所有機密資訊。 確定任何連接到您虛擬網路中 Azure 資源的用戶端都能夠協商 TLS 1.2 或更高的版本。 遵循針對待用加密及傳輸中加密的安全性中心建議。 

Microsoft 提供數個選項，可讓客戶利用這些選項來保護 Azure 網路內部傳輸中的資料，以及從網際網路外部傳送給終端使用者的資料。 其中包括透過虛擬私人網路進行通訊 (使用 IPsec/IKE 加密) 、傳輸層安全性 (TLS) 1.2 或更新版本 (透過 azure 元件（例如，Windows IPsec 或 SMB Azure Front Door 等）直接在 Azure 虛擬機器上使用通訊協定。

此外，「預設加密」使用 MACsec (資料連結層的 IEEE 標準，) 會針對在 Azure 資料中心之間的所有 Azure 流量啟用，以確保客戶資料的機密性和完整性。

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="46-use-role-based-access-control-to-manage-access-to-resources"></a>4.6：使用角色型存取控制來管理資源的存取權 

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來管理對資料和資源的存取。 否則，請使用服務特定的存取控制方法。 

選擇內建角色，例如擁有者、參與者或網路參與者，並將角色指派給適當的範圍。 例如，您可以使用虛擬網路對任何這些角色所需的特定許可權，指派虛擬網路功能的子集。 

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md)

- [規劃虛擬網路](virtual-network-vnet-plan-design-arm.md#permissions)

- [查看 Azure 內建角色](../role-based-access-control/built-in-roles.md#networking)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以建立在 azure 資源（例如虛擬網路和網路安全性群組）發生變更時的警示。

- [適用於網路安全性群組的診斷記錄](virtual-network-nsg-manage-log.md)

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢及探索所有網路資源，例如虛擬網路、訂用帳戶內的子網。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md) 

- [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0) 

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別的訂用帳戶（如果適當）來組織和追蹤虛擬網路和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription) 

- [如何建立管理群組](/azure/governance/management-groups/create) 

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：您將需要根據組織的需求，建立已核准的 Azure 資源和已核准的計算資源軟體的清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：
- 不允許的資源類型 

- 允許的資源類型 

使用高安全性環境中的 Azure Resource Graph 來查詢或探索訂用帳戶內的資源，例如具有 Azure 儲存體帳戶的環境。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [適用于虛擬網路的 Azure 原則範例內建](/azure/virtual-network/policy-samples)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針：防止資源建立或使用組織原則所需的 Azure 原則。 執行移除未經授權之資源的程式。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：
- 不允許的資源類型 

- 允許的資源類型 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure 原則拒絕特定的資源類型](/azure/governance/policy/samples/not-allowed-resource-types)

- [適用于虛擬網路的 Azure 原則範例內建](/azure/virtual-network/policy-samples)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導**方針：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則別名來建立自訂原則，以對 Azure 網路資源的設定進行審核或強制執行，也可以使用內建的 Azure 原則定義。

使用 JavaScript 物件標記法 (JSON) 表單中的 Azure Resource Manager 來匯出任何組建範本，並加以檢查以確保設定符合或超過組織的安全性需求。

針對您的 Azure 資源，以安全的設定基準來執行來自「安全性中心」的建議。

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)

- [適用于虛擬網路的 Azure 原則範例內建](/azure/virtual-network/policy-samples)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure Resource Manager 範本和 Azure 原則，安全地設定與虛擬網路和相關資源相關聯的 Azure 資源。  Azure Resource Manager 範本是 JSON (JavaScript 物件標記法用來部署虛擬機器和 Azure 資源的) 型檔案。 Microsoft 會在基底範本上執行維護。  

使用 Azure 原則 [拒絕] 和 [部署（如果不存在）] 效果，在您的 Azure 資源上強制執行安全設定。

- [建立 Azure Resource Manager 範本的資訊](../virtual-machines/windows/ps-template.md) 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [了解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [虛擬網路的 Azure Resource Manager 範本範例](template-samples.md)

- [適用于虛擬網路的 Azure 原則範例內建](/azure/virtual-network/policy-samples)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本、desired state configuration 腳本。 依此類推。

您必須有權存取您想要在 Azure DevOps 中管理的資源，例如程式碼、組建和工作追蹤。 大部分的許可權是透過內建安全性群組授與。 您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) （如果與 Azure DevOps 整合）中定義的群組，或與 Active Directory 整合時的 Team Foundation Server。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：使用 Azure 原則定義和實行適用于 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure 資源的網路設定，以及與特定資源相關的任何內建原則定義。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

- [適用于虛擬網路的 Azure 原則範例內建](/azure/virtual-network/policy-samples)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用「安全性中心」為您的 Azure 虛擬網路和相關資源執行基準掃描。 使用 Azure 原則來警示和審核 Azure 資源設定。

- [如何修復安全中心的建議](../security-center/security-center-remediate-recommendations.md)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [適用于虛擬網路的 Azure 原則範例內建](/azure/virtual-network/policy-samples)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：使用受控服務識別搭配 Azure Key Vault，為裝載于 Azure 虛擬網路中的 azure 資源簡化和保護秘密管理。

- [如何與 Azure 受控識別整合](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md) 

- [如何建立 Key Vault](/azure/key-vault/quick-create-portal) 

- [如何使用受控識別來提供 Key Vault authentication](/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導**方針：使用 Azure Resource Manager 部署虛擬網路和相關資源。 Azure Resource Manager 提供匯出範本的功能，可作為備份來還原虛擬網路和相關資源。  使用 Azure 自動化定期呼叫 Azure Resource Manager 範本匯出 API。

- [Azure Resource Manager 的概觀](../azure-resource-manager/management/overview.md)

- [虛擬網路的 Azure Resource Manager 範本範例](template-samples.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [資源群組-匯出範本](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自動化簡介](../automation/automation-intro.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針：使用 Azure Resource Manager 部署虛擬網路和相關資源。 Azure Resource Manager 提供匯出範本的功能，可作為備份來還原虛擬網路和相關資源。 使用 Azure 自動化定期呼叫 Azure Resource Manager 範本匯出 API。 在 Azure Key Vault 中備份客戶管理的金鑰。

- [Azure Resource Manager 的概觀](../azure-resource-manager/management/overview.md)

- [虛擬網路的 Azure Resource Manager 範本範例](template-samples.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [資源群組-匯出範本](/rest/api/resources/resourcegroups/exporttemplate)

- [Azure 自動化簡介](../automation/automation-intro.md)

- [如何在 Azure 中備份金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：定期將 Azure Resource Manager 範本部署至隔離的訂用帳戶，並測試備份客戶管理金鑰的還原。

- [使用 ARM 範本和 Azure 入口網站部署資源](../azure-resource-manager/templates/deploy-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則定義和 Azure Resource Manager 範本。 

如果與 Azure DevOps 整合，則授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (Azure AD) 中定義之群組的許可權。  

使用 Azure 角色型存取控制 (Azure RBAC) 來保護客戶管理的金鑰。   

在 Key Vault 中啟用 Soft-Delete 和清除保護，以防止金鑰遭到意外或惡意刪除。  

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

- [如何在 Key Vault 中啟用 Soft-Delete 和清除保護](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal) 

- [Azure 儲存體 Blob 的虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。  

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的電腦安全性事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

使用標記清楚地標示訂用帳戶 (例如生產或非生產) ，並建立命名系統來清楚識別和分類 Azure 資源，特別是處理敏感性資料。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [安全性中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

- [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

- [如何設定資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導**方針：使用連續匯出功能來匯出您的資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 

您也可以使用「安全性中心」資料連線器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用資訊安全中心的工作流程自動化功能，透過「Logic Apps」在安全性警示和建議上自動觸發回應，以保護您的 Azure 資源。

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
