---
title: 適用于 Azure Machine Learning 的 Azure 安全性基準
description: Azure Machine Learning 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 203ca95368d313055d7c715a3a3becbc1061582c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015079"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>適用于 Azure Machine Learning 的 Azure 安全性基準

適用于 Microsoft Azure Machine Learning 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。 此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。 如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導**方針： Azure Machine Learning 依賴其他 Azure 服務來取得計算資源。 計算資源 (計算目標) 用於定型和部署模型。 您可以在虛擬網路中建立這些計算目標。 例如，您可以使用 Azure 虛擬 Machine Learning 計算實例來定型模型，然後將模型部署至 Azure Kubernetes Service (AKS) 。 您可以藉由隔離 Azure 虛擬網路內的 Azure Machine Learning 訓練和推斷作業，來保護您的機器學習服務生命週期。

您可以使用 Azure 防火牆來控制 Azure Machine Learning 工作區和公用網際網路的存取權。

- [如何安全地在隔離的虛擬網路中執行實驗和推斷](how-to-enable-virtual-network.md)

- [使用 Azure 防火牆後方的工作區進行 Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導**方針： Azure Machine Learning 依賴其他 Azure 服務來取得計算資源。 將網路安全性群組指派給建立為您 Machine Learning 部署的網路。 

啟用網路安全性群組流量記錄，並將記錄傳送至 Azure 儲存體帳戶進行審核。 您也可以將流量記錄傳送至 Log Analytics 工作區，然後使用流量分析來提供 Azure 雲端中的流量模式見解。 流量分析的一些優點是能夠將網路活動視覺化、找出熱點和安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。

- [如何啟用網路安全性群組流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用和使用流量分析](../network-watcher/traffic-analytics.md)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：您可以啟用 HTTPS，以安全地與 Azure Machine Learning 所部署的 web 服務進行通訊。 Web 服務會部署在 Azure Kubernetes Services 上 (AKS) 或 Azure 容器實例 (ACI) 以及保護用戶端所提交的資料。 您也可以搭配 AKS 使用私人 IP 來限制計分，因此只有虛擬網路後方的用戶端可以存取 web 服務。

- [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)

- [&amp;使用私人虛擬網路進行定型推斷期間的網路隔離](how-to-enable-virtual-network.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：在與您的 Machine Learning 實例相關聯的虛擬網路上啟用 DDoS 保護標準，以防止分散式阻絕服務 (DDoS) 的攻擊。 使用 Azure 資訊安全中心整合威脅偵測來偵測具有已知惡意或未使用網際網路 IP 位址的通訊。

在每個組織的網路界限上部署 Azure 防火牆，並啟用以威脅情報為基礎的篩選，並設定為惡意網路流量的「警示和拒絕」。

- [如何設定 DDoS 保護](../virtual-network/manage-ddos-protection.md)

- [使用 Azure 防火牆後方的工作區進行 Azure Machine Learning](how-to-access-azureml-behind-firewall.md)

- [如需 Azure 資訊安全中心威脅偵測的詳細資訊](/azure/security-center/security-center-alerts-service-layer)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針：針對 Azure Machine Learning 服務中已安裝適當擴充功能的任何 vm，您可以啟用網路監看員封包捕獲來調查異常的活動。 

- [如何建立網路監看員實例](../network-watcher/network-watcher-create.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導**方針：在您組織的每個網路界限上部署您所選擇的防火牆解決方案，以偵測及/或封鎖惡意流量。

從支援具有承載檢查功能的 IDS/IPS 功能的 Azure Marketplace 中選取供應專案。  如果不需要承載檢查，則可以使用 Azure 防火牆威脅情報。 Azure 防火牆威脅情報型篩選是用來警示和/或封鎖來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

- [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：對於需要存取您 Azure Machine Learning 帳戶的資源，請使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由指定服務標籤名稱 (例如，AzureMachineLearning) 在規則的適當來源或目的地欄位中，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

Azure Machine Learning 服務在虛擬網路內記錄其計算目標的服務標籤清單，以協助將複雜性降至最低，您可以使用它作為網路管理的指導方針。

- [如需使用服務標記的詳細資訊](../virtual-network/service-tags-overview.md)

- [針對 Azure Machine Learning 的虛擬網路](how-to-enable-virtual-network.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則為與您的 Azure Machine Learning 命名空間相關聯的網路資源定義和執行標準安全性設定。 使用 "MachineLearning" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Machine Learning 命名空間的網路設定進行審核或強制執行。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：針對與您的 Azure Machine Learning 部署相關聯的網路資源使用標籤，以便根據分類法以邏輯方式組織這些標記。

針對 Azure Machine Learning 虛擬網路中支援 [描述] 欄位的資源，請使用它來記錄允許流量進出網路的規則。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄監視網路資源設定，並偵測與 Azure Machine Learning 相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

- [如何檢視及擷取 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的 Azure Machine Learning。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，以匯總 Azure Machine Learning 所產生的安全性資料。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。 或者，您可以啟用 Azure Sentinel 或第三方安全性事件和事件管理 (SIEM)，然後使資料上線。

- [如何設定 Azure Machine Learning 的診斷記錄](monitor-azure-machine-learning.md#configuration)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：啟用 Azure 資源上的診斷設定，以存取 audit、security 和診斷記錄。 活動記錄 (自動提供) 包含事件來源、日期、使用者、時間戳記、來源位址、目的地位址，以及其他有用的項目。

您也可以將 Machine Learning 服務作業記錄相互關聯，以進行安全性和合規性的用途。

- [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

- [瞭解 Azure 中的記錄和不同的記錄類型](../azure-monitor/platform/platform-logs-overview.md)

- [在 Azure Machine Learning 中啟用記錄](/azure/machine-learning/how-to-enable-logging)

- [監視 Azure Machine Learning](monitor-azure-machine-learning.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責收集和監視它。 

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的任何計算資源，請使用 Azure 資訊安全中心來監視作業系統。 

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](../azure-monitor/learn/quick-collect-azurevm.md)

- [瞭解 Azure 資訊安全中心資料收集](../security-center/security-center-enable-data-collection.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：在 Azure 監視器中，根據您組織的合規性法規，設定與您 Azure Machine Learning 實例相關聯的 log Analytics 工作區記錄保留期限。

- [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導**方針：分析和監視記錄中的異常行為，並定期檢查您 Azure Machine Learning 的結果。 使用 Azure 監視器與 Log Analytics 工作區來檢查記錄，並對記錄資料執行查詢。

或者，您可以啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM。 

- [如何在 Log Analytics 工作區中執行 Azure Machine Learning 的查詢](monitor-azure-machine-learning.md#analyzing-log-data)

- [在 Azure Machine Learning 中啟用記錄](/azure/machine-learning/how-to-enable-logging)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [開始使用 Log Analytics 查詢](../azure-monitor/log-query/get-started-portal.md)

- [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：在 Azure 監視器中，設定與活動記錄檔中 Azure Machine Learning 相關的記錄，並 Machine Learning 診斷設定，以將記錄傳送至 Log Analytics 工作區，以供長期封存儲存體進行查詢或儲存至儲存體帳戶。 使用 Log Analytics 工作區，針對在安全性記錄檔和事件中找到的異常活動建立警示。

或者，您也可以啟用和麵板上的資料以 Azure Sentinel。

- [如需有關 Azure Machine Learning 警示的詳細資訊](monitor-azure-machine-learning.md#alerts)

- [如何對 Log Analytics 工作區記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責 Azure Machine Learning 服務的反惡意程式碼部署。 

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對組織所擁有的計算資源，請針對 Azure 雲端服務和虛擬機器的 Microsoft Antimalware 啟用反惡意程式碼事件收集。

- [如何設定虛擬機器的 Microsoft Antimalware](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [如何設定雲端服務的 Microsoft Antimalware 擴充功能](/powershell/module/servicemanagement/azure.service/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [瞭解 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;Azure Machine Learning 不會處理或產生 DNS 相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 對於您的組織所擁有的計算資源，請使用 Azure 資訊安全中心來啟用 Azure 虛擬機器的安全性事件記錄檔監視。 Azure 資訊安全中心在所有支援的 Azure Vm 上布建 Log Analytics 代理程式，以及啟用自動布建時所建立的任何新虛擬機器。 或者，您可以手動安裝代理程式。 代理程式會啟用進程建立事件4688和事件4688內的命令列欄位。 事件記錄檔會記錄在 VM 上建立的新進程，並由資訊安全中心的偵測服務進行監視。

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**方針：您可以使用 Azure 入口網站中資源的 [身分識別與存取管理] 索引標籤，來設定 (RBAC) 的角色型存取控制，並維護 Azure Machine Learning 資源的清查。 角色會套用至 Active Directory 中的使用者、群組、服務主體和受控識別。 您可以針對個人和群組使用內建角色或自訂角色。

Azure Machine Learning 針對 Azure Machine Learning 中的常見管理案例提供內建的 RBAC。 在 Azure Active Directory (Azure AD) 中具有設定檔的個人可以將這些 RBAC 角色指派給使用者、群組、服務主體或受控識別，以授與或拒絕對 Azure Machine Learning 資源的資源和作業的存取權。

您也可以使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [瞭解 Azure Machine Learning 中的角色型存取控制](how-to-assign-roles.md)

- [如何使用 PowerShell 在 Azure Active Directory 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Machine Learning 資源的存取管理是透過 Azure Active Directory (Azure AD) 來控制。 Azure AD 沒有預設密碼的概念。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：當建立新的工作區時，Azure Machine Learning 會隨附三個預設角色，請建立使用擁有者帳戶的標準作業程式。

您也可以使用 Azure AD Privileged Identity Management 和 Azure Resource Manager 來啟用系統管理帳戶的即時存取。 

- [若要深入瞭解 Machine Learning 預設角色](how-to-assign-roles.md#default-roles)

- [深入瞭解 Privileged Identity Management](/azure/active-directory/privileged-identity-management/)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針： Machine Learning 與 Azure Active Directory 整合，請使用 Azure Active Directory SSO，而不是為每個服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取建議。  

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導**方針：啟用 Azure Active Directory Multi-Factor Authentication，並遵循 Azure 資訊安全中心身分識別和存取建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導**方針：使用安全且受 Azure 管理的工作站 (也稱為「特殊許可權存取工作站」，或 PAW 需要較高許可權的系統管理工作) 。

- [瞭解安全、受 Azure 管理的工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [如何啟用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：使用 Azure Active Directory 安全性報告和監視，來偵測環境中發生可疑或不安全的活動時。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

- [如何識別已標示為有風險活動的 Azure AD 使用者](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用 Azure AD 命名位置，只允許來自 IP 位址範圍或國家/地區之特定邏輯群組的存取。
 
 
 
- [如何設定 Azure AD 命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory (Azure AD) 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。
 
角色存取範圍可以設定為 Azure 中的多個層級。 針對 Machine Learning，可在工作區層級管理角色，例如，您擁有工作區的擁有者存取權時，可能無法擁有包含工作區之資源群組的擁有者存取權。 這可提供更細微的存取控制，以分隔相同資源群組內的角色。 

- [管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md) 
 
- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，使用 Azure AD 身分識別和存取評論，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。 
 
當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 來產生記錄和警示。

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring)

- [如何使用 Azure AD 身分識別和存取權評論](../active-directory/governance/access-reviews-overview.md)

- [部署 Azure AD Privileged Identity Management (PIM)](/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導**方針：您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 工作區中設定所需的警示。
 
 
- [如何整合 Azure 活動記錄與 Azure 監視器](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 Azure AD Identity Protection 功能，對偵測到與使用者身分識別相關的可疑動作設定自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。
 
- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)
 
- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)
 
- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：不適用;Azure Machine Learning 服務不支援客戶加密箱。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。
 
- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導**方針：針對個別的安全性網域（例如環境類型和資料敏感度層級），使用不同的訂用帳戶和管理群組來執行隔離。 您可以限制您的應用程式和企業環境所需的 Azure 資源存取層級。 您可以透過 Azure RBAC 來控制對 Azure 資源的存取。
 
- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](../governance/management-groups/create.md)
 
- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：從網路周邊的 Azure Marketplace 使用協力廠商解決方案，以監視是否有未經授權的機密資訊傳輸，並在警示資訊安全專業人員時封鎖這類傳輸。 

針對 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密資料，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。 

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：透過 Azure Machine Learning 部署的 Web 服務只支援在傳輸中強制執行資料加密的 TLS 1.2 版。

- [使用 TLS 來透過 Azure Machine Learning 保護 Web 服務](how-to-secure-web-service.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針： Azure Machine Learning 尚無法使用資料識別、分類和遺失防護功能。 視需要執行協力廠商解決方案，以符合合規性需求。

針對由 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並達到絕佳的長度，以防止客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 來管理資源的存取權

**指導**方針： Azure Machine Learning 支援使用 Azure Active Directory (Azure AD) 來授權 Machine Learning 資源的要求。 使用 Azure AD 時，您可以使用 Azure 角色型存取控制 (RBAC) 將許可權授與安全性主體（可能是使用者或應用程式服務主體）。

- [管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項準則主要用於計算資源。

Microsoft 管理 Machine Learning 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針： Azure Machine Learning 會將快照集、輸出和記錄儲存在與 Azure Machine Learning 工作區和訂用帳戶相關聯的 Azure Blob 儲存體帳戶中。 Azure Blob 儲存體中儲存的所有資料，都會使用 Microsoft 受控金鑰進行待用加密。 您也可以在 Machine Learning 服務中使用您自己的金鑰來加密儲存在 Azure Blob 儲存體中的資料。 

- [Azure Machine Learning 待用資料加密](concept-enterprise-security.md#encryption-at-rest)

- [瞭解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

- [如何設定客戶管理的加密金鑰](../storage/common/storage-encryption-keys-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對 Azure Machine Learning 和其他重要或相關資源的生產實例進行變更時，建立警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責 Azure Machine Learning 服務的弱點管理。 

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請遵循 Azure 資訊安全中心中的建議，在您的 Azure 虛擬機器、容器映射和 SQL server 上執行弱點評定。

- [如何實行 Azure 資訊安全中心弱點評定建議](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責 Azure Machine Learning 服務的修補程式管理。 

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對貴組織所擁有的任何計算資源，請使用 Azure 自動化更新管理，以確保您的 Windows 和 Linux Vm 上已安裝最新的安全性更新。 針對 Windows Vm，請確定已啟用 Windows Update，並將其設定為自動更新。

- [如何在 Azure 中設定虛擬機器的更新管理](/azure/automation/automation-update-management)

- [瞭解由安全性中心監視的 Azure 安全性原則](../security-center/security-center-policy-definitions.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您的組織所擁有的計算資源，請使用協力廠商修補管理解決方案。 已在環境中使用 Configuration Manager 的客戶也可以使用 System Center Updates Publisher，讓他們能夠將自訂更新發佈到 Windows Server Update 服務。 這可讓更新管理修補使用 Configuration Manager 作為其更新存放庫（具有協力廠商軟體）的機器。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請遵循 Azure 資訊安全中心中的建議，以在您的 Azure 虛擬機器、容器映射和 SQL server 上執行弱點評定。 以一致的間隔匯出掃描結果，並將結果與先前的掃描進行比較，以確認已補救弱點。 使用 Azure 資訊安全中心建議的弱點管理建議時，您可以在選取的解決方案入口網站中，切換到所選解決方案的入口網站以查看歷程記錄掃描資料。

- [如何實行 Azure 資訊安全中心弱點評定建議](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢和探索資源 (例如計算、儲存體、網路、埠和通訊協定等。您的訂用帳戶中 ) 。  確定您的租使用者中有適當的 (讀取) 許可權，並列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

雖然可以透過 Azure Resource Graph Explorer 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

- [如何使用 Azure Resource Graph Explorer 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何查看您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導**方針：將標記套用至 Azure 資源，並新增中繼資料，以邏輯方式根據分類法進行組織。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：在適當的情況下使用標記、管理群組和個別訂用帳戶來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。
 
 
 
- [ 如何建立額外的 Azure 訂用帳戶](/azure/billing/billing-create-subscription)
 
 
 
- [ 如何建立管理群組](../governance/management-groups/create.md)
 
 
 
- [ 如何建立和使用標記](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：根據您的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

* 不允許的資源類型
* 允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您的組織所擁有的計算資源，請使用 Azure 自動化變更追蹤和清查，將您的 Windows 和 Linux Vm 中的清查資訊收集自動化。 您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得軟體安裝日期和其他資訊，請啟用來賓層級的診斷，並將 Windows 事件記錄檔導向至 Log Analytics 工作區。

- [如何啟用 VM 的 Azure 自動化清查收集](../automation/automation-tutorial-installed-software.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請使用 Azure 資訊安全中心的檔案完整性監視 (FIM) 來識別安裝在 Vm 上的所有軟體。 另一個可用來取代或搭配 FIM 使用的選項是 Azure 自動化變更追蹤和清查，以從您的 Linux 和 Windows Vm 收集清查。 

您可以執行自己的流程，以移除未經授權的軟體。 您也可以使用協力廠商解決方案來識別未核准的軟體。

當不再需要 Azure 資源時，請將其移除。

- [如何使用檔案完整性監視](../security-center/security-center-file-integrity-monitoring.md#using-file-integrity-monitoring)

- [瞭解 Azure 自動化變更追蹤和清查](../automation/change-tracking.md)

- [如何啟用 Azure 虛擬機器清查](../automation/automation-tutorial-installed-software.md)

- [Azure 資源群組和資源刪除](../azure-resource-manager/management/delete-resource-group.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請使用 Azure 資訊安全中心的自我調整應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：

* 不允許的資源類型
* 允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢及探索訂用帳戶中的資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的任何計算資源，請使用 Azure 資訊安全中心的自動調整應用程式控制，以指定規則可能或可能不適用的檔案類型。

如果適應性應用程式控制不符合需求，則執行協力廠商解決方案。

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導**方針：使用 Azure AD 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure 資源管理員互動的能力。
 
- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制使用者在計算資源中執行腳本的能力

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對組織所擁有的計算資源，您可以使用作業系統特定的設定或協力廠商資源，以限制使用者在 Azure 計算資源中執行腳本的能力。  您也可以使用 Azure 資訊安全中心的自我調整應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

- [如何在 Windows 環境中控制 PowerShell 腳本執行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則來定義和實行 Azure Machine Learning 服務的標準安全性設定。 使用 "MachineLearning" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Machine Learning 服務的設定進行審核或強制執行。

Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合您組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

Azure Machine Learning 完全支援用於追蹤工作的 Git 存放庫;您可以直接將存放庫複製到您的共用工作區檔案系統、在本機工作站上使用 Git，以及確定安全設定會套用至程式碼資源，作為 Machine Learning 環境的一部分。

- [如何查看可用 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責 Azure Machine Learning 服務的作業系統安全設定。

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請使用 Azure 資訊安全中心建議來維護所有計算資源上的安全性設定。  此外，您可以使用自訂作業系統映射或 Azure 自動化狀態設定，來建立組織所需作業系統的安全性設定。

- [如何監視 Azure 資訊安全中心建議](../security-center/security-center-recommendations.md)

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

- [Azure 自動化狀態設定總覽](../automation/automation-dsc-overview.md)

- [上傳 VHD 並使用它在 Azure 中建立新的 Windows Vm](../virtual-machines/windows/upload-generalized-managed.md)

- [使用 Azure CLI 從自訂磁碟建立 Linux VM](../virtual-machines/linux/upload-vhd.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。 此外，您可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。 
 
 
 
- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)
 
- [建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)
 
- [ Azure Resource Manager 範本總覽](../azure-resource-manager/templates/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責 Azure Machine Learning 服務的作業系統安全設定。

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請遵循 Azure 資訊安全中心在您的 Azure 計算資源上執行弱點評定的建議。  此外，您可以使用 Azure Resource Manager 範本、自訂作業系統映射或 Azure 自動化狀態設定來維護組織所需作業系統的安全性設定。   與 Azure 自動化狀態設定結合的 Microsoft 虛擬機器範本，可能有助於符合和維護安全性需求。 

請注意，microsoft 發佈的 Azure Marketplace 虛擬機器映射是由 Microsoft 所管理和維護。 

- [如何實行 Azure 資訊安全中心弱點評定建議](../security-center/security-center-vulnerability-assessment-recommendations.md)

- [如何從 ARM 範本建立 Azure 虛擬機器](../virtual-machines/windows/ps-template.md)

- [Azure 自動化狀態設定總覽](../automation/automation-dsc-overview.md)

- [在 Azure 入口網站中建立 Windows 虛擬機器 ](../virtual-machines/windows/quick-create-portal.md)

- [如何下載 VM 範本的資訊](../virtual-machines/windows/download-template.md)

- [將 VHD 上傳至 Azure 並新建 VM 的範例指令碼](../virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果您 Machine Learning 或相關資源使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

Azure Machine Learning 完全支援用於追蹤工作的 Git 存放庫;您可以直接將存放庫複製到您的共用工作區檔案系統、在本機工作站上使用 Git，以及確定安全設定會套用至程式碼資源，作為 Machine Learning 環境的一部分。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導**方針： Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請使用 Azure 角色型存取控制 (RBAC) ，以確保只有獲得授權的使用者可以存取您的自訂映射。 您可以使用 Azure 共用映射庫，將映射分享給組織內的不同使用者、服務主體或 Azure AD 群組。 在 Azure Container Registry 中儲存容器映射，並使用 RBAC 來確保只有授權的使用者可以存取。

- [瞭解 Azure 中的 RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [瞭解 Container Registry 的 RBAC](../container-registry/container-registry-roles.md)

- [如何在 Azure 中設定 RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [共用映像資源庫概觀](../virtual-machines/windows/shared-image-galleries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：在 "MachineLearning" 命名空間中使用 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責 Azure Machine Learning 服務的安全設定部署。

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您的組織所擁有的計算資源，請使用 Azure 自動化狀態設定，以在任何雲端或內部部署資料中心內 Desired State Configuration (DSC) 節點。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。 

- [如何啟用 Azure 自動化狀態設定](../automation/automation-dsc-onboarding.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用 Azure 資訊安全中心針對您的 Azure 資源執行基準掃描。 此外，請使用 Azure 原則來警示和審核 Azure 資源設定。
 
 
 
- [ 如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導**方針：如果計算資源是由 microsoft 所擁有，則 microsoft 會負責 Azure Machine Learning 服務的自動安全設定監視。

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您組織所擁有的計算資源，請使用 Azure 資訊安全中心計算 &amp; 應用程式，並遵循 vm 和伺服器和容器的建議。

- [了解 Azure 資訊安全中心容器建議](/azure/security-center/security-center-container-recommendations)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：使用受控識別搭配 Azure Key Vault，以簡化雲端應用程式的秘密管理。

Azure Machine Learning 支援以客戶管理的金鑰進行資料存放區加密，您必須管理金鑰輪替，並依組織的安全性和合規性需求撤銷。 

使用 Azure Key Vault，在您的定型腳本中安全地將秘密傳遞給遠端執行，而不是純文字。

- [Azure Machine Learning 客戶管理的金鑰](concept-enterprise-security.md#azure-blob-storage)

- [在 Azure Machine Learning 定型執行中使用驗證認證秘密](how-to-use-secrets-in-runs.md)

- [如何使用適用于 Azure 資源的受控識別](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [如何建立 Key Vault](/azure/key-vault/quick-create-portal)

- [如何使用受控識別來提供 Key Vault authentication](/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針： Azure Machine Learning 支援內建角色和建立自訂角色的能力。 使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可讓您向支援 Azure AD authentication 的任何服務進行驗證，包括 Key Vault，而不需要在您的程式碼中提供任何認證。

 
- [管理 Azure Machine Learning 工作區的存取權](how-to-assign-roles.md)

- [如何設定 Azure 資源的受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Machine Learning) ，但不會對客戶內容執行。

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對您的組織所擁有的計算資源，請使用適用于 Azure 的 Microsoft Antimalware 來持續監視及保護您的資源。 若為 Linux，請使用協力廠商反惡意程式碼解決方案。 此外，使用 Azure 資訊安全中心的資料服務威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。

- [如何設定適用于 Azure 的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

- [Azure 資訊安全中心內的威脅防護](../security-center/threat-protection.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Machine Learning) ，但不會對客戶內容執行。

您必須負責預先掃描即將上傳至非計算 Azure 資源的任何內容。 Microsoft 無法存取客戶資料，因此無法代表您執行客戶內容的反惡意程式碼掃描。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8.3：確定已更新反惡意程式碼軟體和簽章

**指導**方針：為支援 azure 服務的基礎主機啟用和維護 Microsoft 反惡意程式碼 (例如，azure Machine learning) ，不過它不會在客戶內容上執行。

Azure Machine Learning 在不同的計算資源，甚至是您自己的計算資源之間有不同的支援。 針對貴組織所擁有的任何計算資源，請遵循 Azure 資訊安全中心中的建議、計算 &amp; 應用程式，以確保所有端點都是最新的簽章。 若為 Linux，請使用協力廠商反惡意程式碼解決方案。

- [如何部署適用于 Azure 的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針： Machine Learning 服務中的資料復原管理是透過連線資料存放區上的資料管理。 請務必追蹤已連線存放區的資料復原指導方針，以備份每個客戶組織原則的資料。

- [如何從 Azure 虛擬機器備份復原檔案](../backup/backup-azure-restore-files-from-vm.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針： Machine Learning 服務中的資料備份是透過連線資料存放區上的資料管理。 啟用 Vm 的 Azure 備份，並設定所需的頻率和保留期限。 在 Azure Key Vault 中備份客戶管理的金鑰。

- [如何從 Azure 虛擬機器備份復原檔案](../backup/backup-azure-restore-files-from-vm.md)
- [如何在 Azure 中還原 Key Vault 金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針： Machine Learning 服務中的資料備份驗證是透過連線資料存放區上的資料管理。 在 Azure 備份中定期執行內容的資料還原。 確定您可以還原已備份的客戶管理金鑰。

- [如何從 Azure 虛擬機器備份復原檔案](../backup/backup-azure-restore-files-from-vm.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：針對內部部署備份，會使用您在備份至 Azure 時所提供的複雜密碼來提供靜態加密。 使用角色型存取控制來保護備份和客戶管理的金鑰。 

啟用 Key Vault 中的虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。 如果使用 Azure 儲存體來儲存備份，請在刪除 blob 或 blob 快照集時，啟用虛刪除來儲存及復原您的資料。
 
 
- [了解 Azure RBAC](../role-based-access-control/overview.md)

- [如何啟用 Key Vault 中的虛刪除和清除保護](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Azure Blob 儲存體的虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：為您的組織開發事件回應指南。 請確定有撰寫的事件回應計畫，可定義人員的所有角色，以及事件處理和管理的階段，以進行事件後的評論。 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [使用 NIST 的電腦安全性性事件處理指南來協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導**方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性是以資訊安全中心在尋找或 analytically 中的信心，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

此外，使用標記標記訂用帳戶，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料。 您必須負責根據發生事件的 Azure 資源和環境的重要性來排定補救警示的優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導**方針：進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 識別弱式點數和間隙，然後視需要修改您的回應計畫。

- [NIST 的發行--測試、訓練和練習適用于 IT 計畫和功能的指南](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導**方針：使用連續匯出功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動方式或持續持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用工作流程自動化功能 Azure 資訊安全中心自動觸發對安全性警示和建議的回應，以保護您的 Azure 資源。

- [如何在安全性中心設定工作流程自動化](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
