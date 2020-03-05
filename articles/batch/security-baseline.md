---
title: Batch 的 Azure 安全性基準
description: Batch 的 Azure 安全性基準
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 779b7d071b1161c53bdba08738539c4c682882d2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273552"
---
# <a name="azure-security-baseline-for-batch"></a>Batch 的 Azure 安全性基準

Batch 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自[Azure 安全性基準測試版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供有關如何在 Azure 上使用最佳作法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱[Azure 安全性基準總覽](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在您的虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指引**：在虛擬網路中部署 Azure Batch 集區。 若要讓集區計算節點與其他虛擬機器或內部部署網路安全地進行通訊，您可以在 Azure 虛擬網路的子網中布建集區。 此外，在虛擬網路內部署集區，可讓您控制用來保護個別節點網路介面（NIC）和子網的網路安全性群組（NSG）。 將 NSG 設定為只允許來自網際網路上受信任的 IP/locations 的流量。


如何在虛擬網路內建立 Azure Batch 集區：

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視並記錄 Vnet、子網和 NIC 的設定和流量

**指引**：使用 Azure 資訊安全中心並修復與您的 Batch 集區相關聯的虛擬網路/網路安全性群組（NSG）相關的網路保護建議。 在用來保護 Batch 集區的 NSG 上啟用流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送至 Azure Log Analytics 工作區，並使用 Azure 流量分析來提供 Azure 雲端中流量的深入解析。 Azure 流量分析的一些優點是能夠將網路活動視覺化，並識別作用點、識別安全性威脅、瞭解流量模式，以及找出網路錯誤設定。


如何啟用 NSG 流量記錄：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


如何啟用和使用流量分析：

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


瞭解 Azure 資訊安全中心所提供的網路安全性：

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：不適用，基準測試適用于在 Azure App Service 或 IaaS 實例上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：在虛擬網路上啟用 Azure DDoS （分散式阻斷服務）標準保護，以保護您的 Azure Batch 集區，以防範 DDoS 攻擊。 使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意或未使用的網際網路 IP 位址的通訊。


如何設定 DDoS 保護：

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


瞭解 Azure 資訊安全中心整合式威脅情報：

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指引**：在用來保護您 Azure Batch 集區的網路安全性群組（NSG）上啟用流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。


如何啟用 NSG 流量記錄：

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6：部署以網路為基礎的入侵偵測/入侵預防系統

**指導**方針：如果需要進行合規性，請從支援入侵偵測系統（IDS）和入侵防護系統（ip）功能的 Azure Marketplace 中，選取網路虛擬裝置，其中包含承載檢查功能。


如果基於承載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。 以 Azure 防火牆威脅情報為基礎的篩選可以警示和拒絕進出已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。


在與您的 Azure Batch 集區節點相同的虛擬網路中，部署具有公用 IP 位址的 Azure 防火牆。 在網際網路上的信任位置和個別集區節點的私人 IP 位址之間設定網路位址轉譯（NAT）規則。 在 Azure 防火牆的 [威脅情報] 底下，設定 [警示和拒絕] 以封鎖警示並封鎖進出已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要，而且只包含最高的信賴記錄。 


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

**指引**：使用虛擬網路服務標籤來定義網路安全性群組或與您的 Azure Batch 集區相關聯的 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標籤名稱（例如，ApiManagement），您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。


瞭解和使用服務標記：

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則，為與您的 Azure Batch 集區相關聯的網路資源定義和實施標準安全性設定。 使用 "Microsoft. Batch" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Batch 集區的網路設定。



如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

**指導**方針：使用網路服務群組（nsg）的標籤，以及與您的 Azure batch 集區相關聯的網路安全性和流量相關的其他資源。 針對個別的 NSG 規則，請使用 [描述] 欄位來指定允許進出網路流量的任何規則的商務需求和（或）持續時間（等）。


使用與標記相關的任何內建 Azure 原則定義，例如「需要標記和其值」，以確保所有資源都是以標籤建立，並通知您現有的未標記資源。


您可以使用 Azure PowerShell 或 Azure CLI，根據其標記來查閱或執行資源的動作。


如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


如何建立虛擬網路：

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


如何建立 NSG：

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的 Azure Batch 集區相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重大網路資源的變更發生時觸發。

如何查看和取出 Azure 活動記錄事件： https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

如何在 Azure 監視器中建立警示： https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針：針對 Azure Batch，Microsoft 預設會提供時間同步。不過，如果您有特定時間同步需求，您可以執行這些變更。

**Azure 資訊安全中心監視**：目前無法使用

**責任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：將 Azure Batch 帳戶上線，以 Azure 監視器匯總叢集裝置所產生的安全性資料。 利用自訂查詢來偵測及回應環境中的威脅。  如需 Azure Batch 資源層級監視，請使用 Batch Api 來監視或查詢資源的狀態，包括作業、工作、節點和集區。



如何將 Azure Batch 帳戶上架至 Azure 監視器：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的審核記錄

**指導**方針：如 Azure Batch 帳戶層級監視，請使用 Azure 監視器的功能來監視每個 Batch 帳戶。 Azure 監視器會針對以 Batch 帳戶層級為範圍的資源（例如集區、作業和工作），收集計量和選擇性的診斷記錄。 以手動方式或程式設計方式收集和使用此資料，以監視您 Batch 帳戶中的活動並診斷問題。


如需 Azure Batch 資源層級監視，請使用 Azure Batch Api 來監視或查詢資源的狀態，包括作業、工作、節點和集區。


如何設定 Azure Batch 帳戶層級監視和記錄：

https://docs.microsoft.com/azure/batch/monitoring-overview


瞭解 Batch 資源層級監視：

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-system"></a>2.4：從作業系統收集安全性記錄檔

**指導**方針： Azure 監視器收集 Azure Batch 帳戶中資源的計量和診斷記錄。 以各種方式收集並取用此資料，以監視您的 Azure Batch 帳戶並診斷問題。 您也可以設定計量警示，以便在計量達到指定的值時收到通知。


如有需要，您可以透過安全的 Shell （SSH）或遠端桌面通訊協定（RDP）連接到您的個別集區節點，以存取本機作業系統記錄。


如何從您的 Azure Batch 帳戶收集診斷記錄：

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics


如何從遠端連線到您的 Azure Batch 集區節點：

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指引**：將 Azure Batch 帳戶上線至 Azure 監視器。 請確定所使用的 Azure Log Analytics 工作區已根據貴組織的合規性法規設定記錄保留期限


如何設定 Azure Batch 監視和記錄：

https://docs.microsoft.com/azure/batch/monitoring-overview


如何設定 Azure Log Analytics 工作區保留期限：

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導**方針：建立當指定的計量值超過指定的閾值時，所觸發的 Azure Batch 計量警示。


如何設定 Azure Batch 計量警示：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導**方針：建立當指定的計量值超過指定的閾值時，所觸發的 Azure Batch 計量警示。


如何設定 Azure Batch 計量警示：

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反惡意程式碼記錄

**指引**：在 windows 作業系統的情況下，在您的個別 batch-節點上使用 Windows Defender，或如果您使用 Linux，請提供您自己的反惡意程式碼解決方案。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：針對 dns 記錄執行協力廠商解決方案

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列審核記錄

**指導**方針：手動設定每個節點的主控台記錄和 PowerShell 轉譯。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指引**：維護在布建 Azure Batch 集區時所建立的本機系統管理客戶紀錄，以及您所建立的任何其他帳戶。 此外，如果使用 Azure Active Directory （AAD）整合，AAD 就具有必須明確指派，因此可查詢的內建角色。 使用 AAD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。


此外，您可以使用 Azure 資訊安全中心身分識別和存取管理建議。


如何使用 PowerShell 取得 AAD 中的目錄角色：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


如何使用 PowerShell 取得 AAD 中目錄角色的成員：

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


如何使用 Azure 資訊安全中心監視身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：變更預設密碼（若適用）

**指導**方針：布建 Azure Batch 集區時，您可以選擇建立本機電腦帳戶。 沒有要變更的預設密碼，不過您可以為安全的 Shell （SSH）和遠端桌面通訊協定（RDP）存取指定不同的密碼。 設定 Azure Batch 集區之後，您可以針對 Azure 入口網站內的個別節點，或透過 Azure Resource Manager API 產生隨機使用者。


如何將使用者新增至特定的計算節點：

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3：確保使用專用的系統管理帳戶

**指引**：使用 Azure Active Directory 整合 Azure Batch 應用程式的驗證。 建立有關使用專用系統管理帳戶的原則和程式。


此外，您可以使用 Azure 資訊安全中心身分識別和存取管理建議。


如何使用 Azure Active Directory 驗證 Batch 應用程式：

https://docs.microsoft.com/azure/batch/batch-aad-auth


如何使用 Azure 資訊安全中心監視身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4：利用 Azure Active Directory 的單一登入（SSO）

**指導**方針：不適用，雖然 Azure Batch 支援 Azure AD 驗證，但不支援單一登入。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取，請使用多重要素驗證。

**指導**方針：整合 Azure Batch 應用程式與 AZURE ACTIVE DIRECTORY （AAD）的驗證。 啟用 AAD 多重要素驗證（MFA），並遵循 Azure 資訊安全中心身分識別和存取管理建議。
 


如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


如何監視 Azure 資訊安全中心內的身分識別和存取：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦（特殊許可權存取工作站）進行所有系統管理工作

**指導**方針：使用 paw （特殊許可權存取工作站）並設定多重要素驗證（MFA）來登入和設定您的 Azure Batch 資源。


瞭解特殊許可權存取工作站：

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


如何在 Azure 中啟用 MFA：

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄和警示可疑活動

**指導**方針：如果您已經使用 AZURE ACTIVE DIRECTORY （AAD） Azure Batch 應用程式的整合式驗證，請在環境中發生可疑或不安全的活動時，使用 Azure Active Directory 的安全性報告來產生記錄檔和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。


如何識別標示有風險活動 Azure AD 使用者：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


如何在 Azure 資訊安全中心中監視使用者的身分識別和存取活動：

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：如果您有 Azure Active Directory 的 Azure Batch 應用程式的整合式驗證，您可以使用名為「位置」的條件式存取，只允許來自 IP 位址範圍或國家/地區之特定邏輯群組的存取權。



如何在 Azure 中設定命名位置：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AAD）做為中央驗證和授權系統，並將 Azure Batch 應用程式的驗證與 AAD 整合。 AAD 會針對待用和傳輸中的資料使用強式加密來保護資料。 AAD 也會 salts、雜湊並安全地儲存使用者認證。


如何建立和設定 AAD 實例：

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance


如何使用 AAD 驗證 Batch 應用程式：

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期審查並協調使用者存取

**指導**方針： AZURE ACTIVE DIRECTORY （AAD）提供記錄檔，以協助探索過時的帳戶。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 使用者的存取權可以定期審查，以確保只有適當的使用者才能繼續存取。


如何使用 Azure 身分識別存取權審查：

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視嘗試存取停用的帳戶

**指導**方針：建立 Azure Active Directory 使用者帳戶的診斷設定，將 audit 記錄和登入記錄傳送到 Azure Log Analytics 工作區。 在 Azure Log Analytics 工作區中設定所需的警示。


如何將 Azure 活動記錄整合到 Azure 監視器：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指引**：使用 AZURE ACTIVE DIRECTORY （AAD）風險偵測和 Identity Protection 功能，為偵測到與使用者身分識別相關的可疑動作設定自動回應。 此外，您可以將資料內嵌到 Azure Sentinel 中，以供進一步調查。


如何查看 AAD 有風險的登入：

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


如何設定和啟用身分識別保護風險原則：

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


如何上架 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13： <div>在支援案例期間，為 Microsoft 提供相關客戶資料的存取權<br></div>

**指導**方針：無法使用;Azure Batch 尚不支援客戶加密箱。 客戶加密箱支援的服務清單： https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護機密資訊的清查

**指引**：使用標記來協助追蹤儲存或處理敏感資訊的 Azure 資源。


如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感資訊的系統

**指引**：在開發、測試和生產環境中，執行不同的訂用帳戶和/或管理群組。 Azure Batch 集區應以適當標記的虛擬網路/子網分隔，並使用網路安全性群組（NSG）加以保護。 Azure Batch 資料應該包含在安全的 Azure 儲存體帳戶中。


如何在虛擬網路內建立 Azure Batch 集區：

https://docs.microsoft.com/azure/batch/batch-virtual-network


如何保護 Azure 儲存體帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的機密資訊傳輸。

**指引**：對於與包含機密資訊的 Azure Batch 集區相關聯 Azure 儲存體帳戶，請使用標記將它們標示為機密，並使用 Azure 最佳做法來保護它們。


Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 視需要執行協力廠商解決方案，以符合合規性目的。


針對由 Microsoft 管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制和功能。


瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


如何保護 Azure 儲存體帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有機密資訊

**指引**：加密傳輸中的所有機密資訊。 Microsoft Azure 資源預設會協商 TLS 1.2。 請確定連線到您 Azure Batch 集區或資料存放區（Azure 儲存體帳戶）的任何用戶端都能協商 TLS 1.2 或更高版本。


請確定需要 HTTPS，才能存取包含您 Azure Batch 資料的儲存體帳戶。


瞭解傳輸中 Azure 儲存體帳戶加密：

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用 active discovery 工具來識別敏感性資料

**指引**：對於與包含機密資訊的 Azure Batch 集區相關聯 Azure 儲存體帳戶，請使用標記將它們標示為機密，並使用 Azure 最佳做法來保護它們。


Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 視需要執行協力廠商解決方案，以符合合規性目的。


針對由 Microsoft 管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制和功能。


瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


如何保護 Azure 儲存體帳戶：

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**：使用 AZURE ACTIVE DIRECTORY （AAD）角色型存取控制（RBAC）來控制 Azure 資源管理平面的存取權，包括 batch 帳戶、Batch 集區和儲存體帳戶。


瞭解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview


如何在 Azure 中設定 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用以主機為基礎的資料遺失防護來強制存取控制

**指導**方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 視需要執行協力廠商解決方案，以符合合規性目的。



針對由 Microsoft 管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制和功能。



瞭解 Azure 中的客戶資料保護：

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密機密資訊待用

**指引**：對於與您 Azure Batch 帳戶相關聯的儲存體帳戶，建議允許 Microsoft 管理加密金鑰，不過，您可以視需要選擇管理您自己的金鑰。



如何管理 Azure 儲存體帳戶的加密金鑰：

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：對重要 Azure 資源的變更進行記錄和警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對與您的 Azure Batch 帳戶/集區相關或相關聯的重要 Azure 資源進行變更時，建立警示。



設定與 Azure Batch 集區相關聯之儲存體帳戶的診斷設定，以監視和記錄針對集區資料的所有 CRUD 作業。



如何建立 Azure 活動記錄事件的警示：

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



如何啟用 Azure 儲存體帳戶的其他記錄/審核：

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：針對 Azure Batch 集區節點，您必須負責管理弱點管理解決方案。


（選擇性）如果您有 Rapid7、Qualys 或任何其他弱點管理平臺訂用帳戶，您可以手動在 Batch 集區節點上安裝弱點評估代理程式，並透過個別的入口網站管理節點。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**： Microsoft 維護並更新基底 Azure Batch 集區節點映射。 請確定 Azure Batch 集區節點的作業系統在叢集存留期間仍然修補，這可能需要啟用自動更新、監視節點，或定期執行重新開機。

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**方針：確定 Azure Batch 集區節點的協力廠商應用程式在叢集存留期間持續修補，這可能需要啟用自動更新、監視節點，或定期執行重新開機。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較回溯弱點掃描

**指導**方針：如果您有 Rapid7、Qualys 或任何其他弱點管理平臺訂用帳戶，您可以使用該廠商的入口網站來查看和比較回溯弱點掃描。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程式來優先處理探索到的弱點。

**指導**方針：使用常見的風險評分計畫（例如，常見的弱點評分系統）或協力廠商掃描工具所提供的預設風險評等。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指導**方針：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源（例如計算、儲存體、網路等）。 請確定您的租使用者中有適當的（讀取）許可權，而且能夠列舉所有的 Azure 訂用帳戶以及您訂用帳戶內的資源。


雖然可透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager （ARM）資源。


如何使用 Azure Graph 建立查詢：

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


如何查看您的 Azure 訂用帳戶：

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


瞭解 Azure RBAC：

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至 Azure 資源，讓中繼資料以邏輯方式將其組織成分類法。


如何建立和使用標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：使用標記、管理群組，以及個別的訂用帳戶（適當時）來組織和追蹤資產。 定期協調清查，並確保未經授權的資源會及時從訂用帳戶中刪除。



如何建立額外的 Azure 訂用帳戶：

https://docs.microsoft.com/azure/billing/billing-create-subscription



如何建立管理群組：

https://docs.microsoft.com/azure/governance/management-groups/create



如何建立和使用者標記：

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准的 Azure 資源和軟體標題的清查。

**指導**方針：定義已核准的 Azure 資源清單，以及適用于計算資源的已核准軟體

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：未核准的 Azure 資源的監視

**指引**：使用下列內建原則定義，透過 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型


使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 請確定已核准環境中的所有 Azure 資源。


如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


如何使用 Azure Graph 建立查詢： https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案來監視未核准軟體應用程式的叢集節點。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案來監視未核准軟體應用程式的叢集節點。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案，以防止未經授權的軟體執行。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，透過 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：


- 不允許的資源類型
- 允許的資源類型


如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


如何拒絕具有 Azure 原則的特定資源類型： https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：執行核准的應用程式清單

**指導**方針：針對 Azure Batch 集區節點，請執行協力廠商解決方案，以防止未授權的檔案類型執行。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11： <div>限制使用者透過腳本與 Azure Resource Manager 互動的能力</div>

**指引**：使用 Azure 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。


如何設定條件式存取以封鎖對 Azure Resource Manager 的存取： https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行腳本的能力

**指導**方針：不適用，

這不適用於 Azure Batch，因為 Azure Batch 集區的使用者（非系統管理員）不需要存取個別節點來執行作業。 叢集系統管理員已經具有所有節點的根存取權。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實際或邏輯上隔離高風險應用程式

**指導**方針：不適用，基準測試適用于在 Azure App Service 或 IaaS 實例上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：在 "Microsoft. Batch" 命名空間中使用 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Batch 帳戶和集區的設定。


如何查看可用的 Azure 原則別名：

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2：為您的作業系統建立安全設定

**指導**方針：為 Batch 集區節點的作業系統建立安全設定。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3：維護所有 Azure 資源的安全設定

**指引**：使用 azure 原則 [拒絕] 和 [部署（如果不存在）]，針對與 Batch 帳戶和集區相關的 Azure 資源（例如虛擬網路、子網、Azure 防火牆、Azure 儲存體帳戶等）強制執行安全設定。 您可以使用下列命名空間中的 Azure 原則別名來建立自訂原則：

- Microsoft.Batch
- Microsoft.Storage
- Microsoft.Network


如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


瞭解 Azure 原則效果： https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4：維護作業系統的安全設定

**指引**： Azure Batch 由 Microsoft 所管理和維護的集區作業系統映射。 您必須負責執行 OS 層級狀態設定。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果您的 Azure Batch 帳戶、集區或相關資源使用自訂的 Azure 原則定義，請使用 Azure Repos 安全地儲存和管理您的程式碼。


如何將程式碼儲存在 Azure DevOps：

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops


Azure Repos 檔：

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

**指導**方針：如果您的 Azure Batch 集區使用自訂映射，請使用角色型存取控制（RBAC），以確保只有授權的使用者可以存取映射。


瞭解 Azure 中的 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles


如何在 Azure 中設定 RBAC：

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指引**：使用內建的 Azure 原則定義來警示、audit 及強制執行 Azure Batch 相關的資源設定。  在 "Microsoft. Batch" 命名空間中使用 Azure 原則別名，為您的 Azure Batch 帳戶和集區建立自訂原則。 此外，開發處理常式和管線來管理原則例外狀況。



如何設定和管理 Azure 原則：

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

**指導**方針：執行協力廠商解決方案，為您的 Azure Batch 集區節點的作業系統維護所需的狀態。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

**指引**：在 "Microsoft. Batch" 命名空間中使用 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Batch 實例的設定。 您也可以使用專為 Azure Batch 所建立的內建原則，或 Azure Batch 所使用的資源，例如：

- 子網應與網路安全性群組相關聯-儲存體帳戶應使用虛擬網路服務端點
- 應啟用 Batch 帳戶中的診斷記錄

如何查看可用的 Azure 原則別名： https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

如何設定和管理 Azure 原則： https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

**指引**：執行協力廠商解決方案，以監視 Azure Batch 集區節點的作業系統狀態。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="711-securely-manage-azure-secrets"></a>7.11：安全地管理 Azure 秘密

**指導**方針： Azure Key Vault 可搭配 Azure Batch 部署使用，以在 Azure 儲存體帳戶內管理集區儲存體的金鑰。


如何與 Azure 受控識別整合：

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


如何建立 Azure Key Vault：

https://docs.microsoft.com/azure/key-vault/quick-create-portal


如何使用受控識別提供 Key Vault 驗證：

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-securely-and-automatically-manage-identities"></a>7.12：安全且自動管理身分識別

**指導**方針：無法使用，受控服務識別不支援 Azure Batch

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證暴露

**指導**方針：執行認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

如何設定認證掃描器： https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防護](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：在 windows 作業系統的案例中，使用個別 Azure Batch 集區節點上的 Windows Defender，或如果您使用 Linux，請提供您自己的反惡意程式碼解決方案。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：在支援 Azure 服務的基礎主機（例如 Azure Batch）上啟用 Microsoft Antimalware，但不會對客戶內容執行。


預先掃描任何正在上傳到非計算 Azure 資源的檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。Microsoft 無法存取這些實例中的客戶資料。


瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware：

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確認反惡意程式碼軟體和簽章已更新

**指引**：在 windows 作業系統的情況下，在您的個別 Azure Batch 集區節點上使用 Windows Defender，並確定已啟用自動更新。 如果您使用 Linux，請提供您自己的反惡意程式碼解決方案。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保週期性自動備份

**指導**方針：使用 Azure Batch 集區資料存放區的 Azure 儲存體帳戶時，請選擇適當的 [冗余] 選項（LRS、ZRS、GRS、RA-GRS）。 


如何設定 Azure 儲存體帳戶的儲存體冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針：使用 Azure Batch 集區資料存放區的 Azure 儲存體帳戶時，請選擇適當的 [冗余] 選項（LRS、ZRS、GRS、RA-GRS）。  如果您 Azure Batch 部署的任何部分使用 Azure Key Vault，請確定您的金鑰已備份。


如何設定 Azure 儲存體帳戶的儲存體冗余：

https://docs.microsoft.com/azure/storage/common/storage-redundancy


如何在 Azure 中備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：如果您要為 Azure 儲存體帳戶或任何其他與您的 Azure Batch 執行相關的資源管理您自己的金鑰，請定期測試備份金鑰的還原。


如何在 Azure 中備份金鑰保存庫金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


如何使用 PowerShell 還原客戶管理的金鑰：

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指導**方針：如果 Azure Key Vault 用來保存任何與 Azure Batch 集區儲存體帳戶相關的金鑰，請在 Azure Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。


如何在 Azure Key Vault 中啟用虛刪除：

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-incident-response-guide"></a>10.1：建立事件回應指南

**指導**方針：確定有撰寫的事件回應計畫會定義人員的角色，以及事件處理/管理的階段。



如何設定 Azure 資訊安全中心內的工作流程自動化：

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程式

**指導**方針：資訊安全中心指派警示的嚴重性，協助您排定參與每個警示的順序，如此一來，當資源遭到入侵時，您就可以立即開始使用。 嚴重性是根據資訊安全中心在尋找中的信心，或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程式

**指導**方針：執行練習以定期測試系統的事件回應功能。 識別弱式點和間距，並視需要修訂計畫。

請參閱 NIST 的發行集：適用于 IT 計畫和功能的測試、訓練和練習程式指南： https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知 &nbsp;

**指導**方針：如果 Microsoft 安全性回應中心（MSRC）發現您的資料已由非法或未經授權的合作物件存取，microsoft 將會使用安全性事件連絡人資訊來與您聯繫。



如何設定 Azure 資訊安全中心安全性連絡人：

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示納入事件回應系統中

**指引**：使用「連續匯出」功能來匯出您的 Azure 資訊安全中心警示和建議。 「連續匯出」可讓您以手動或持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。


如何設定連續匯出：

https://docs.microsoft.com/azure/security-center/continuous-export


如何將警示串流至 Azure Sentinel：

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議，自動觸發回應。



如何設定工作流程自動化和 Logic Apps：

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在60天內補救所有重大的安全性結果。

**指導**方針：請遵循 Microsoft Engagement 規則，以確保您的滲透測試不會違反 microsoft 原則：

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1第 1 課：建立 Windows Azure 儲存體物件{2}。



您可以在這裡找到有關 Microsoft 受管理的雲端基礎結構、服務和應用程式的 Microsoft 策略與執行的 Red 小組和即時網站滲透測試的詳細資訊： 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全性基準測試](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 深入瞭解[Azure 安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
