---
title: 適用于 Batch 的 Azure 安全性基準
description: 適用于 Batch 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 1eb24871817f365efe58b8e687563727df74493c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400971"
---
# <a name="azure-security-baseline-for-batch"></a>適用于 Batch 的 Azure 安全性基準

適用于 Batch 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

如需詳細資訊，請參閱[安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md) (機器翻譯)。

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導**方針：在虛擬網路中部署 Azure Batch 集區 (s) 。 若要允許集區計算節點與其他虛擬機器或內部部署網路安全地通訊，您可以在 Azure 虛擬網路的子網中布建集區。 此外，在虛擬網路內部署您的集區，可讓您控制網路安全性群組 (NSG) 用來保護個別節點的網路介面 (NIC) 以及子網。 將 NSG 設定為只允許來自網際網路上受信任 IP (s) /locations 的流量。

如何在虛擬網路內建立 Azure Batch 集區：

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導**方針：使用 Azure 資訊安全中心和修復與您的 Batch 集區相關聯的虛擬網路/網路安全性群組 (NSG) 相關的網路保護建議。 在用來保護 Batch 集區的 NSG 上啟用流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送到 Azure Log Analytics 工作區，並使用 Azure 流量分析來提供 Azure 雲端中流量的深入解析。 Azure 流量分析的一些優點是能夠將網路活動視覺化、找出作用點、找出安全性威脅、瞭解流量流程模式，以及找出網路錯誤設定。

如何啟用 NSG 流量記錄： 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

如何啟用及使用流量分析： 

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

瞭解 Azure 資訊安全中心所提供的網路安全性：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：在保護您 Azure Batch 集區的虛擬網路上，啟用 Azure DDoS (分散式阻斷服務) 標準保護，以防範 DDoS 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

如何設定 DDoS 保護： 

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

瞭解 Azure 資訊安全中心整合威脅情報：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：在網路安全性群組上啟用流量記錄 (NSG) 用來保護您的 Azure Batch 集區，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。

如何啟用 NSG 流量記錄： 

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6：部署網路型入侵偵測/入侵防護系統

**指導**方針：如需合規性的需求，請從支援入侵偵測系統的 Azure Marketplace 中選取網路虛擬裝置 (識別碼) 和入侵防護系統 (ip) 具有承載檢查功能的功能。

如果不需要以承載檢查為基礎進行入侵偵測和/或預防，則可以使用具有威脅情報的 Azure 防火牆。 以 Azure 防火牆威脅情報為基礎的篩選，可以警示及拒絕來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在與您的 Azure Batch 集區節點相同的虛擬網路中部署具有公用 IP 位址的 Azure 防火牆。 在網際網路上信任的位置和個別集區節點的私人 IP 位址之間，設定網路位址轉譯 (NAT) 規則。 在 Azure 防火牆的 [威脅情報] 下，設定「警示和拒絕」以封鎖警示和封鎖進出已知惡意 IP 位址和網域的流量。 IP 位址和網域源自 Microsoft 威脅情報摘要，而且只包含最高的信賴記錄。 

如何在虛擬網路內建立 Azure Batch 集區：

https://docs.microsoft.com/azure/batch/batch-virtual-network

如何部署 Azure 防火牆：

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace：

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7：管理 web 應用程式的流量

**指導**方針：不適用，基準測試適用于在 Azure App Service 或 IaaS 實例上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組的網路存取控制，或與您的 Azure Batch 集區 () 相關聯的 Azure 防火牆。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

瞭解和使用服務標記：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：針對與您的 Azure Batch 集區 () 與 Azure 原則相關聯的網路資源，定義並執行標準安全性設定。 使用 "Microsoft.Batch" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Batch 集區的網路設定進行審核或強制執行。

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

**指導**方針：針對網路服務群組使用標籤 (nsg) ，以及與您的 Azure batch 集區相關聯的網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可使用 Azure PowerShell 或 Azure CLI，根以據資源的標籤對資源進行查詢或執行動作。

如何建立和使用標籤： 

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

如何建立虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

如何建立 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄監視網路資源設定，並偵測與您的 Azure Batch 集區相關的網路資源變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

如何檢視及擷取 Azure 活動記錄事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

如何在 Azure 監視器中建立警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

如需詳細資訊，請參閱[安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md) (機器翻譯)。

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針：針對 Azure Batch，Microsoft 預設會提供時間同步處理。但是，如果您有特定的時間同步需求，可以執行這些變更。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：將 Azure Batch 帳戶上架到 Azure 監視器，以匯總叢集裝置所產生的安全性資料。 利用自訂查詢來偵測和回應環境中的威脅。  針對 Azure Batch 資源層級監視，請使用 Batch Api 來監視或查詢您資源的狀態，包括工作、工作、節點和集區。

如何上架 Azure Batch 帳戶以 Azure 監視器：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的審核記錄

**指導**方針：若要 Azure Batch 帳戶層級監視，請使用 Azure 監視器的功能監視每個 Batch 帳戶。 Azure 監視器會收集 Batch 帳戶層級範圍內的資源計量和診斷記錄 (選擇性)，這些資源包括集區、作業和工作等。 以手動方式或程式設計方式收集和使用此資料，以監視您 Batch 帳戶中的活動並診斷問題。

針對 Azure Batch 資源層級監視，請使用 Azure Batch 的 Api 來監視或查詢您資源的狀態，包括工作、工作、節點和集區。

如何設定 Azure Batch 帳戶層級監視和記錄：

https://docs.microsoft.com/azure/batch/monitoring-overview

瞭解 Batch 資源層級監視：

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-system"></a>2.4：從作業系統收集安全性記錄檔

**指導**方針： Azure 監視器收集 Azure Batch 帳戶中資源的計量和診斷記錄。 以各種不同的方式收集和取用此資料，以監視您的 Azure Batch 帳戶和診斷問題。 您也可以設定計量警示，在計量達到指定值時接收通知。

如有必要，您可能會透過安全的 Shell (SSH) 或遠端桌面通訊協定 (RDP) 來連線至個別集區節點，以存取本機作業系統記錄。

如何從您的 Azure Batch 帳戶收集診斷記錄：

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

如何從遠端連線到您的 Azure Batch 集區節點：

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄檔存放區保留

**指導**方針：將 Azure Batch 帳戶上架到 Azure 監視器。 確定使用的 Azure Log Analytics 工作區已根據您組織的合規性法規設定記錄保留期限

如何設定 Azure Batch 監視和記錄：

https://docs.microsoft.com/azure/batch/monitoring-overview

如何設定 Azure Log Analytics 工作區保留期限：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：建立 Azure Batch 計量警示，以在指定的度量值超過指定的閾值時觸發。

如何設定 Azure Batch 計量警示：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針：建立 Azure Batch 計量警示，以在指定的度量值超過指定的閾值時觸發。

如何設定 Azure Batch 計量警示：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反惡意程式碼記錄

**指導**方針：在 Windows 作業系統的情況下，在個別批次節點上使用 Windows Defender，或如果您使用 Linux，請提供您自己的反惡意程式碼解決方案。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：執行 dns 記錄的協力廠商解決方案

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列審核記錄

**指導**方針：以個別節點為基礎，手動設定主控台記錄和 PowerShell 轉譯。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md) (機器翻譯)。

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導**：維護在布建 Azure Batch 集區時所建立的本機系統管理客戶紀錄，以及您所建立的任何其他帳戶的記錄。 此外，如果使用 Azure Active Directory (AAD) 整合，則 AAD 具有必須明確指派，因此可供查詢的內建角色。 使用 AAD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

此外，您也可以使用 Azure 資訊安全中心的身分識別和存取管理建議。

如何使用 PowerShell 在 AAD 中取得目錄角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

如何使用 PowerShell 取得 AAD 中目錄角色的成員：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

如何使用 Azure 資訊安全中心監視身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針：布建 Azure Batch 集區時，系統會提供您建立本機電腦帳戶的選項。 沒有要變更的預設密碼，不過您可以為安全的 Shell (SSH) 和遠端桌面通訊協定 (RDP) 存取指定不同的密碼。 設定 Azure Batch 集區之後，您可以為 Azure 入口網站內的個別節點或透過 Azure Resource Manager API 產生隨機使用者。

如何將使用者新增至特定的計算節點：

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3：確定使用專用的系統管理帳戶

**指導**方針：將 Azure Batch 應用程式的驗證與 Azure Active Directory 整合。 使用專用的系統管理帳戶來建立原則和程式。

此外，您也可以使用 Azure 資訊安全中心的身分識別和存取管理建議。

如何使用 Azure Active Directory 驗證 Batch 應用程式：

https://docs.microsoft.com/azure/batch/batch-aad-auth

如何使用 Azure 資訊安全中心監視身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4：利用單一 Sign-On (SSO) 搭配 Azure Active Directory

**指導**方針：不適用，但 Azure Batch 支援 Azure AD 驗證，則不支援單一登入。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證。

**指導**方針：整合 Azure Batch 應用程式的驗證與 AZURE ACTIVE DIRECTORY (AAD) 。  (MFA) 啟用 AAD 多重要素驗證，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

 

如何在 Azure 中啟用 MFA： 

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

如何在 Azure 資訊安全中心監視身分識別與存取： 

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：針對所有系統管理工作使用專用電腦 (特殊許可權的存取工作站) 

**指導**方針：使用 paw (特殊許可權的存取工作站) 搭配多重要素驗證 (MFA) 設定為登入和設定您的 Azure Batch 資源。

了解特殊權限存取工作站： 

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

如何在 Azure 中啟用 MFA： 

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄和警示可疑活動

**指導**方針：如果您已將 Azure Batch 應用程式的整合式驗證與 AZURE ACTIVE DIRECTORY (AAD) ，請在環境中發生可疑或不安全的活動時，使用 Azure Active Directory 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

如何識別標示為具風險活動的 Azure AD 使用者：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

如何在 Azure 資訊安全中心中監視使用者身分識別和存取活動：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：如果您已使用 Azure Active Directory Azure Batch 應用程式的整合式驗證，您可以使用名為「位置」的條件式存取，只允許來自特定 IP 位址範圍或國家/地區的邏輯群組的存取。

如何在 Azure 中設定具名位置： 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 AZURE ACTIVE DIRECTORY (aad) 作為中央驗證和授權系統，並整合 Azure Batch 應用程式與 AAD 的驗證。 AAD 會對待用和傳輸中資料使用強式加密以保護資料。 AAD 也會 salts、雜湊並安全地儲存使用者認證。

如何建立和設定 AAD 實例：

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

如何使用 AAD 驗證 Batch 應用程式：

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期審核和協調使用者存取

**指導**方針： AZURE ACTIVE DIRECTORY (AAD) 提供記錄以協助探索過時的帳戶。 此外，您可以使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期審核使用者的存取權，以確保只有適當的使用者可以繼續存取。

如何使用 Azure 身分識別存取權檢閱： 

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視嘗試存取已停用的帳戶

**指導**方針：為 Azure Active Directory 使用者帳戶建立診斷設定，將審核記錄和登入記錄傳送到 Azure Log Analytics 工作區。 在 Azure Log Analytics 工作區中設定所需的警示。

如何將 Azure 活動記錄整合到 Azure 監視器中： 

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 AZURE ACTIVE DIRECTORY (AAD) 風險偵測和 Identity Protection 功能，設定自動回應與使用者身分識別相關的偵測到可疑動作。 此外，您可以將資料內嵌到 Azure Sentinel 以進行進一步的調查。

如何查看 AAD 具風險的登入：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

如何設定及啟用 Identity Protection 風險原則：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

如何讓 Azure Sentinel 上線： 

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13： <div>在支援案例中提供 Microsoft 可存取相關客戶資料<br></div>

**指導**：無法使用;客戶加密箱尚不支援 Azure Batch。
 
客戶加密箱支援服務清單： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

如需詳細資訊，請參閱[安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md) (機器翻譯)。

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 Azure Batch 集區應以虛擬網路/子網分隔、適當標記，並以 (NSG) 的網路安全性群組來保護。 Azure Batch 的資料應該包含在安全的 Azure 儲存體帳戶內。

如何在虛擬網路內建立 Azure Batch 集區：

https://docs.microsoft.com/azure/batch/batch-virtual-network

如何保護 Azure 儲存體帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸。

**指導**方針：對於與包含機密資訊的 Azure Batch (集區) 相關聯的 Azure 儲存體帳戶，請使用標記將其標示為機密，並使用 Azure 最佳做法加以保護。

Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

如何保護 Azure 儲存體帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：加密傳輸中的所有機密資訊。 Microsoft Azure 資源預設會協商 TLS 1.2。 確定任何連線至 Azure Batch 集區或資料存放區的用戶端 (Azure 儲存體帳戶) 能夠協商 TLS 1.2 或更新版本。

請確定需要 HTTPS，才能存取包含您 Azure Batch 資料的儲存體帳戶。

瞭解傳輸中 Azure 儲存體帳戶加密：

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針：對於與包含機密資訊的 Azure Batch (集區) 相關聯的 Azure 儲存體帳戶，請使用標記將其標示為機密，並使用 Azure 最佳做法加以保護。

Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

如何保護 Azure 儲存體帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制 azure 資源管理平面的存取權，包括 batch 帳戶、Batch 集區 (s) 和儲存體帳戶。

了解 Azure RBAC： 

https://docs.microsoft.com/azure/role-based-access-control/overview

如何設定 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

了解 Azure 中的客戶資料保護： 

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的機密資訊

**指導**方針：對於與 Azure Batch 帳戶相關聯的儲存體帳戶，建議您允許 Microsoft 管理加密金鑰，不過，您可以視需要選擇管理您自己的金鑰。

如何管理 Azure 儲存體帳戶的加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對與 Azure Batch 帳戶/集區相關或相關聯的重要 Azure 資源進行變更時，建立警示。

針對與 Azure Batch 集區相關聯的儲存體帳戶設定診斷設定，以監視並記錄集區資料的所有 CRUD 作業。

如何建立 Azure 活動記錄事件的警示： 

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

如何啟用 Azure 儲存體帳戶的其他記錄/審核：

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

如需詳細資訊，請參閱[安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md) (機器翻譯)。

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：針對 Azure Batch 集區節點，您必須負責管理弱點管理解決方案。

（選擇性）如果您有 Rapid7、Qualys 或任何其他弱點管理平臺訂用帳戶，您可以手動將弱點評定代理程式安裝在 Batch 集區節點上，並透過個別的入口網站來管理節點。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針： Microsoft 維護並更新基底 Azure Batch 集區節點映射。 確定 Azure Batch 集區節點的作業系統在叢集存留期間仍會修補，這可能需要啟用自動更新、監視節點，或執行定期重新開機。


**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**方針：確定 Azure Batch 集區節點的協力廠商應用程式在叢集存留期間仍會修補，這可能需要啟用自動更新、監視節點，或執行定期重新開機。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較回溯弱點掃描

**指導**方針：如果您有 Rapid7、Qualys 或任何其他弱點管理平臺訂用帳戶，您可以使用該廠商的入口網站來查看並比較回溯弱點掃描。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序。

**指引**：使用常見的風險評分計畫 (例如，常見弱點評分系統) 或協力廠商掃描工具所提供的預設風險評等。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

如需詳細資訊，請參閱[安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md) (機器翻譯)。

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指導**方針：使用 Azure Resource Graph 來查詢/探索 () 訂用帳戶內的所有資源 (例如計算、儲存體、網路等 ) 。 確定您有適當的 (讀取您租使用者中) 的許可權，而且能夠列舉訂用帳戶中的所有 Azure 訂用帳戶及資源。

雖然可能會透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager (ARM) 資源繼續進行。

如何使用 Azure Resource Graph 建立查詢： 

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

如何檢視 Azure 訂用帳戶： 

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

了解 Azure RBAC： 

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別訂用帳戶（如果適用）來組織和追蹤資產。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

如何建立其他 Azure 訂用帳戶： 

https://docs.microsoft.com/azure/billing/billing-create-subscription

如何建立管理群組： 

https://docs.microsoft.com/azure/governance/management-groups/create

如何建立和使用者標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查。

**指導**方針：定義已核准的 Azure 資源清單和適用于計算資源的已核准軟體


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未核准的 Azure 資源

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索您的訂用帳戶 (s) 中的資源。 確保已核准環境中的所有 Azure 資源。

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：在計算資源內監視未核准的軟體應用程式

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案來監視未核准之軟體應用程式的叢集節點。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案來監視未核准之軟體應用程式的叢集節點。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案，以防止未經授權的軟體執行。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

如何使用 Azure 原則拒絕特定的資源類型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案，以防止未經授權的檔案類型執行。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11： <div>限制使用者透過腳本與 Azure Resource Manager 互動的能力</div>

**指引**：使用 Azure 條件式存取，藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。

如何設定條件式存取以封鎖 Azure Resource Manager 的存取： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行腳本的能力

**指導**方針：不適用

這不適用於 Azure Batch，因為 Azure Batch 集區 (非系統管理員) 的使用者不需要存取個別節點來執行工作。 叢集系統管理員已有所有節點的根目錄存取權。


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實體或邏輯上隔離高風險的應用程式

**指導**方針：不適用，基準測試適用于在 Azure App Service 或 IaaS 實例上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

如需詳細資訊，請參閱[安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md) (機器翻譯)。

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 "Microsoft.Batch" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Batch 帳戶和集區的設定進行審核或強制執行。

如何檢視可用的 Azure 原則別名： 

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2：建立作業系統的安全設定

**指導**方針：為 Batch 集區節點的作業系統建立安全的設定。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：維護所有 Azure 資源的安全設定

**指導**方針：使用 Azure 原則 [拒絕] 和 [部署（如果不存在）] 來針對與您的 Batch 帳戶 (和集區相關的 Azure 資源（例如虛擬網路、子網、Azure 防火牆、Azure 儲存體帳戶 ) 等等）強制執行安全設定。 您可以使用下列命名空間 Azure 原則別名來建立自訂原則：

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

了解 Azure 原則效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4：維護作業系統的安全設定

**指導**方針： Azure Batch 由 Microsoft 管理及維護的集區作業系統映射。 您必須負責實作 OS 層級狀態設定。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果您 Azure Batch 帳戶、集區或相關資源使用自訂 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。

如何在 Azure DevOps 中儲存程式碼： 

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos 文件： 

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

**指導**方針：如果您 Azure Batch 集區使用自訂映射，請使用 azure 角色型存取控制 (azure RBAC) ，以確保只有獲得授權的使用者可以存取這些映射。

了解 Azure RBAC： 

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

如何設定 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統建構管理工具

**指導**方針：使用內建的 Azure 原則定義來警示、審核和強制執行 Azure Batch 相關的資源設定。  使用 "Microsoft.Batch" 命名空間中 Azure 原則別名來為您的 Azure Batch 帳戶和集區建立自訂原則。 此外，開發流程和管線以管理原則例外狀況。

如何設定和管理 Azure 原則： 

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

**指導**方針：執行協力廠商解決方案，以維持您 Azure Batch 集區節點作業系統的預期狀態。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

**指導**方針：使用 "Microsoft.Batch" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Batch 實例的設定。 您也可以使用針對 Azure Batch 所建立的任何內建原則，或 Azure Batch 所使用的資源，例如：

- 子網路應該與網路安全性群組建立關聯

-儲存體帳戶應該使用虛擬網路服務端點

- 應啟用 Batch 帳戶中的診斷記錄

如何檢視可用的 Azure 原則別名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

**指導**方針：執行協力廠商解決方案，以監視 Azure Batch 集區節點作業系統的狀態。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="711-securely-manage-azure-secrets"></a>7.11：安全地管理 Azure 秘密

**指導**方針： Azure Key Vault 可用於 Azure Batch 部署，以管理 Azure 儲存體帳戶內集區儲存體的金鑰。

如何與 Azure 受控識別整合： 

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

如何建立 Key Vault： 

https://docs.microsoft.com/azure/key-vault/general/quick-create-portal

如何驗證 Key Vault：

https://docs.microsoft.com/azure/key-vault/general/authentication

如何指派 Key Vault 存取原則：

https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-securely-and-automatically-manage-identities"></a>7.12：安全且自動管理身分識別

**指導**：無法使用，Azure Batch 不支援受控服務識別


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md) (機器翻譯)。

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：在 Windows 作業系統的情況下，在個別的 Azure Batch 集區節點上使用 Windows Defender，或如果您使用 Linux，請提供您自己的反惡意程式碼解決方案。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針： Microsoft Antimalware 會在支援 Azure 服務的基礎主機上啟用 (例如 Azure Batch) ，不過它不會在客戶內容上執行。

預先掃描即將上傳至非計算 Azure 資源的任何檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。Microsoft 無法在這些實例中存取客戶資料。

瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware：

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確定已更新反惡意程式碼軟體和簽章

**指導**方針：在 Windows 作業系統的情況下，使用個別 Azure Batch 集區節點上的 Windows Defender，並確定已啟用自動更新。 如果您使用 Linux，請提供您自己的反惡意程式碼解決方案。


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

如需詳細資訊，請參閱[安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md) (機器翻譯)。

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份 Ups

**指導**方針：使用 Azure Batch 集區資料存放區的 Azure 儲存體帳戶時，請選擇適當的冗余選項 (LRS、ZRS、GRS、RA-GRS) 。 

如何設定 Azure 儲存體帳戶的儲存體冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針：使用 Azure Batch 集區資料存放區的 Azure 儲存體帳戶時，請選擇適當的冗余選項 (LRS、ZRS、GRS、RA-GRS) 。  如果您 Azure Batch 部署的任何部分都使用 Azure Key Vault，請確定您的金鑰已備份。

如何設定 Azure 儲存體帳戶的儲存體冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

如何在 Azure 中備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：如果您要為 Azure 儲存體帳戶或任何其他與 Azure Batch 執行相關的資源管理您自己的金鑰，請定期測試備份金鑰的還原。

如何在 Azure 中備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

如何使用 PowerShell 還原客戶管理的金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：如果 Azure Key Vault 用來保存任何與 Azure Batch 集區儲存體帳戶相關的金鑰，請啟用 Azure Key Vault 中的 Soft-Delete，以防止遭到意外或惡意刪除的金鑰。

如何在 Azure Key Vault 中啟用虛刪除：

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

如需詳細資訊，請參閱[安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md) (機器翻譯)。

### <a name="101-create-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：確定有撰寫的事件回應計畫，可定義人員角色以及事件處理/管理階段。

如何設定 Azure 資訊安全中心內的工作流程自動化： 

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程式

**指導**方針：資訊安全中心會將嚴重性指派給警示，以協助您優先處理每個警示的順序，如此一來，當資源遭到入侵時，您就可以立即取得。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

請參閱 NIST 的出版物：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities (IT 計畫和能力的測試、訓練和練習方案指南)： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定 &nbsp; 安全性事件的警示通知

**指導**方針： Microsoft 安全性回應中心 (MSRC) 發現您的資料已被非法或未經授權的物件存取，microsoft 會使用安全性事件連絡人資訊來與您聯繫。

如何設定 Azure 資訊安全中心的安全性連絡人： 

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

如何設定連續匯出：

https://docs.microsoft.com/azure/security-center/continuous-export

如何將警示串流至 Azure Sentinel： 

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用 Azure 資訊安全中心的工作流程自動化功能，以透過 "Logic Apps" 自動觸發對安全性警示和建議的回應。

如何設定工作流程自動化和 Logic Apps： 

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md) (機器翻譯)。

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在60天內補救所有重大的安全性調查結果。

**指導**方針：請遵循 Microsoft 的參與規則，以確保您的滲透測試不違反 Microsoft 原則：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

您可以在以下位置找到 Microsoft 針對 Microsoft 受控雲端基礎結構、服務和應用程式進行的 Microsoft 策略、Red 小組和即時網站滲透測試的詳細資訊： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md) (機器翻譯)
- 深入了解 [Azure 安全性基準](../security/benchmarks/security-baselines-overview.md) (機器翻譯)
