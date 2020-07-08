---
title: 適用于 Azure Data Factory 的 Azure 安全性基準
description: 適用于 Azure Data Factory 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 771f692ade9c7616eb7d217b4728a8c80d2aeac5
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044170"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>適用于 Azure Data Factory 的 Azure 安全性基準

適用于 Azure Data Factory 的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路中的 Azure 資源

**指導**方針：建立 Azure SSIS INTEGRATION RUNTIME （IR）時，您可以選擇將它與虛擬網路聯結。 這可讓 Azure Data Factory 建立特定的網路資源，例如網路安全性群組（NSG）和負載平衡器。 您也能夠提供自己的靜態公用 IP 位址，或讓 Azure Data Factory 為您建立一個。 在 Azure Data Factory 自動建立的 NSG 上，埠3389預設會開放給所有流量。 請鎖定此限制，以確保只有您的系統管理員能夠存取。

自我裝載的 IRs 可以部署在內部部署機器或虛擬網路內的 Azure 虛擬機器上。 請確定您的虛擬網路子網部署已將 NSG 設定為只允許系統管理存取權。 根據預設，Azure SSIS IR 在每個 IR 節點的 windows 防火牆規則上不允許埠3389輸出，以供保護。 您可以藉由將 NSG 與子網產生關聯並設定嚴格的規則，來保護虛擬網路設定的資源。

若有可用的私用連結，請使用私人端點來保護任何連結到您 Azure Data Factory 管線的資源，例如 Azure SQL Server。 使用私人連結時，您的虛擬網路與服務之間的流量會通過 Microsoft 骨幹網路，消除公開網際網路的風險。

* [如何建立 Azure SSIS IR](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)

* [如何建立和設定自我裝載 IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [將 Azure SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#virtual-network-configuration)

* [瞭解 Azure 私人連結](https://docs.microsoft.com/azure/private-link/private-link-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導**方針：使用 Azure 資訊安全中心並修復與您的 Integration Runtime 部署相關聯的虛擬網路和網路安全性群組的網路保護建議。

此外，針對保護您的 Integration Runtime 部署的 NSG 啟用「網路安全性群組」（NSG）流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。

您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [如何啟用及使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指引**：在與您的 Integration Runtime 部署相關聯的虛擬網路上啟用 DDoS 保護標準，以保護免于分散式阻斷服務的攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

* [如何設定 DDoS 保護](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 資訊安全中心的整合式威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針：針對 NSG 保護您的 Integration Runtime 部署並將記錄傳送到用於流量審核的 Azure 儲存體帳戶，啟用網路安全性群組（NSG）流量記錄。

您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [如何啟用及使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統（IDS/IPS）

**指導**方針：如果您想要檢查來自 AZURE ssis ir 的輸出流量，您可以將從 AZURE ssis ir 起始的流量路由傳送至內部部署防火牆設備，透過 azure ExpressRoute 強制通道，或從支援 IDS/IPS 功能的 Azure Marketplace 到網路虛擬裝置（NVA）。 如果基於酬載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。

* [將 Azure SSIS Integration Runtime 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [如何部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [如何使用 Azure 防火牆設定警示](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導**方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組（NSG）或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標籤名稱（例如，DataFactoryManagement），您可以允許或拒絕對應服務的輸入流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

* [瞭解及使用服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [瞭解 Azure Data Factory 特定服務標記](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：使用 Azure 原則來定義和執行與 Azure data Factory 實例相關聯的網路設定和網路資源的標準安全性設定。 使用 "DataFactory" 和 "Microsoft" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Data Factory 實例的網路設定。 您也可以使用與網路或 Azure Data factory 實例相關的內建原則定義，例如：
- 應啟用 DDoS 保護標準

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [網路的 Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：使用標記來取得與 Azure Data Factory 實例的網路安全性和流量流程相關的資源，以提供中繼資料和邏輯組織。

使用與標記相關的任何內建 Azure 原則定義 (例如「需要標籤及其值」)，以確保建立的所有資源都具有標籤，並向您通知未加上標籤的現有資源。

您可使用 Azure PowerShell 或 Azure CLI，根以據資源的標籤對資源進行查詢或執行動作。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視網路資源設定，並偵測與 Azure Data Factory 實例相關的網路資源變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

* [如何檢視及擷取 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure 監視器中建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**： Microsoft 會維護 Azure 資源所使用的時間來源，例如記錄中時間戳記的 Azure Data Factory。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：透過 Azure 監視器內嵌記錄，以匯總 Azure Data Factory 所產生的安全性資料。 在 Azure 監視器中，您可以查詢設定為接收 Azure Data Factory 活動記錄的 Log Analytics 工作區。 使用適用於長期/封存記錄儲存體或事件中樞的 Azure 儲存體帳戶，將資料匯出至其他系統。

或者，您可以啟用和內部資料，以 Azure Sentinel 或協力廠商安全性事件和事件管理（SIEM）。您也可以將 Azure Data Factory 與 Git 整合，以運用數個原始檔控制的優點，例如追蹤/審核變更的能力，以及還原引入 bug 之變更的能力。

* [如何設定診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure Data Factory 中的原始檔控制](https://docs.microsoft.com/azure/data-factory/source-control)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指引**：針對控制平面的審核記錄，啟用 Azure 活動記錄診斷設定，並將記錄傳送到 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶以進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

使用診斷設定來設定 Azure Data Factory 中 noncompute 資源的診斷記錄，例如計量和管線執行資料。 Azure Data Factory 會儲存管線執行資料45天。 若要將此資料保留較長的時間，請將您的診斷記錄儲存到儲存體帳戶以進行審核或手動檢查，並指定保留時間（以天為單位）。 您也可以串流記錄以 Azure 事件中樞或將記錄傳送到 Log Analytics 工作區進行分析。

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [瞭解 Azure Data Factory 診斷記錄](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，您可以使用 Azure 監視器從虛擬機器收集資料。 安裝 Log Analytics VM 擴充功能可讓 Azure 監視器從您的 Azure Vm 收集資料。 Azure 資訊安全中心接著可以提供虛擬機器的安全性事件記錄檔監視。 由於安全性事件記錄檔產生的資料量，預設並不會儲存。

如果您的組織想要保留安全性事件記錄檔資料，可以將它儲存在資料收集層中，以便在 Log Analytics 中進行查詢。

* [如何從 Azure 監視器中的 Azure 虛擬機器收集資料](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [在 Azure 資訊安全中心中啟用資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指引**：啟用 Azure Data Factory 的診斷設定。 如果選擇將記錄儲存在 Log Analytics 工作區中，請根據貴組織的合規性法規來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

* [如何在 Azure Data Factory 中啟用診斷記錄](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#set-up-diagnostic-logs)

* [如何設定 Log Analytics 工作區的記錄保留期參數](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指引**：啟用 Azure Data Factory 的診斷設定，並將記錄傳送到 Log Analytics 工作區。 使用 Log Analytics 來分析和監視您的記錄中是否有異常行為，並定期審查結果。 請確定您也為任何與 Azure Data Factory 部署相關的資料存放區啟用診斷設定。 請參閱每個服務的安全性基準，以取得如何啟用診斷設定的指引。

如果您是在 Azure 虛擬機器（VM）中執行 Integration Runtime，請同時啟用 VM 的診斷設定。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [Log Analytics 架構](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events)

* [如何使用 Azure 監視器從 Azure 虛擬機器收集資料](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：您可以前往 Azure 監視器中的 [警示計量] 區段，針對 Data Factory 中支援的計量引發警示 &amp; 。

設定 Azure Data Factory 的診斷設定，並將記錄傳送到 Log Analytics 工作區。 在您的 Log Analytics 工作區中，設定在一組預先定義的條件發生時，所要採取的警示。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

此外，請確定您已針對與資料存放區相關的服務啟用診斷設定。 您可以參考每個服務的安全性基準，以取得指導方針。

* [Azure Data Factory 中的警示](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts)

* [所有支援的計量頁面](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)

* [如何在 Log Analytics 工作區中設定警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：如果您在 Azure 虛擬機器中執行 Integration Runtime，您可以使用 azure 雲端服務的 Microsoft Antimalware 和虛擬機器，並將虛擬機器設定為將事件記錄到 Azure 儲存體帳戶。 設定 Log Analytics 工作區，以內嵌來自儲存體帳戶的事件，並在適當時建立警示。 遵循 Azure 資訊安全中心：「計算應用程式」中的建議 &amp; 。

* [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [如何啟用虛擬機器的來賓層級監視](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;Azure Data Factory 不會處理或產生 DNS 相關的記錄檔。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，則可以啟用命令列審核記錄。 Azure 資訊安全中心提供適用于 Azure Vm 的安全性事件記錄檔監視。 資訊安全中心會在所有支援的 Azure Vm 上，以及在啟用自動布建或您可以手動安裝代理程式時所建立的任何新虛擬機器上，提供 Microsoft Monitoring Agent。 代理程式支援處理序建立事件 4688，及事件 4688 中的 CommandLine 欄位。 EventLog 會記錄在 VM 上建立的新處理序，並由資訊安全中心的偵測服務監視。

* [Azure 資訊安全中心的資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指引**：在 Azure Data Factory 中，請務必定期追蹤和協調使用者存取。 若要建立 Data Factory 執行個體，您用來登入 Azure 的使用者帳戶必須為「參與者」或「擁有者」角色，或是 Azure 訂用帳戶的「管理員」。

此外，在租使用者層級，Azure Active Directory （AD）具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索具有 Azure Data Factory 實例之控制平面管理許可權的系統管理群組成員帳戶。

雖然 Azure AD 是管理使用者存取權的建議方法，但請記住，如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，您的 VM 可能也會有本機帳戶。 本機和網域帳戶都應該經過檢查和管理，通常會有最少的使用量。 此外，我們也建議您針對「及時」功能檢查特殊許可權身分識別管理員，以降低系統管理許可權的可用性。

* [Azure Data Factory 的角色和權限](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

* [許可權身分識別管理員的資訊](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [如何使用 PowerShell 取得 Azure AD 中的目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [本機帳戶的資訊](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Azure Data Factory 使用 AZURE ACTIVE DIRECTORY （AD）來提供 Azure 入口網站以及 Azure Data Factory 主控台的存取權。 Azure AD 沒有預設密碼的概念，但是您必須負責變更或不允許任何自訂或協力廠商應用程式的預設密碼。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：建立使用專用系統管理帳戶的標準操作程式，以存取 Azure 控制平面（Azure 入口網站）以及 Azure Data Factory 主控台。 使用 Azure 資訊安全中心身分識別和存取管理來監視 Azure AD 內的系統管理帳戶數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：
- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

如果您是在 Azure 虛擬機器上執行 Integration Runtime，Azure 虛擬機器上的系統管理員帳戶也可以使用 Azure 特殊許可權身分識別管理員（PIM）進行設定。 Azure 特殊許可權身分識別管理員提供數個選項，例如 [時間提升]、[多重要素驗證] 和 [委派選項]，因此許可權僅適用于特定的時間範圍，而且需要第二個人員核准。

* [瞭解 Azure 資訊安全中心身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何使用 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [許可權身分識別管理員的資訊](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Azure Data Factory 的角色和權限](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指引**：使用 Azure 應用程式註冊（服務主體）來抓取權杖，讓您的應用程式或函式可以用來存取及與您的復原服務保存庫進行互動。

* [如何呼叫 Azure REST Api](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [如何向 Azure AD 註冊您的用戶端應用程式（服務主體）](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure 復原服務 API 資訊](https://docs.microsoft.com/rest/api/recoveryservices/)

* [Azure Data Factory REST API 的相關資訊](https://docs.microsoft.com/rest/api/datafactory/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory Multi-Factor Authentication (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有的系統管理工作

**指引**：使用已設定多重要素驗證 (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

* [了解特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄及警示可疑活動

**指導**方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄檔和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

如果您是在 Azure 虛擬機器（VM）上執行 Integration Runtime，您也可以將 VM 上線以 Azure Sentinel。 Microsoft Azure Sentinel 是可調整的雲端原生安全性資訊事件管理 (SIEM) 和安全性協調流程自動化回應 (SOAR) 解決方案。 Azure Sentinel 提供整個企業的智慧型安全性分析和威脅情報，並針對警示偵測、威脅可見性、主動式搜捕及回應威脅提供單一解決方案。

* [如何識別已標示為有風險活動的 Azure AD 使用者](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何讓 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。

* [如何在 Azure 中設定具名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：資料處理站可與 Azure 資源的受控識別相關聯，以代表特定的資料處理站。 您可以使用此受控識別來進行 Azure SQL Database 驗證。 指定的處理站可以使用此身分識別從您資料庫存取資料，或從您的資料庫複製資料。

如果您在 Azure 虛擬機器上執行 Integration Runtime （IR），您可以使用受控識別來向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不需要在您的程式碼中有任何認證。 在虛擬機器上執行的程式碼可以使用受控識別來要求支援 Azure AD authentication 之服務的存取權杖。

* [如何建立和設定 Azure AD 實例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

* [什麼是適用於 Azure 資源的受控識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

* [使用 Azure Data Factory 複製和轉換 Azure SQL Database 中的資料](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#using-service-principal-authentication)

* [如何使用 Azure SQL Database 設定和管理 Azure Active Directory 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**： AZURE ACTIVE DIRECTORY （AD）提供可協助您探索過時帳戶的記錄檔。 此外，您可以使用 Azure 身分識別存取審查來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

如果您在 Azure 虛擬機器中執行執行時間整合，您將需要檢查本機安全性群組和使用者，以確定沒有任何可能危害系統的非預期帳戶。

* [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [瞭解 Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：您可以存取 AZURE ACTIVE DIRECTORY （AD）登入活動、audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。 若要簡化此程式，您可以建立 Azure AD 使用者帳戶的診斷設定，並將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 中設定所需的記錄警示。

如果您是在 Azure 虛擬機器（VM）中執行您的 Integration Runtime，請在該 VM 上 Azure Sentinel。 Microsoft Azure Sentinel 是可調整的雲端原生安全性資訊事件管理 (SIEM) 和安全性協調流程自動化回應 (SOAR) 解決方案。 Azure Sentinel 提供整個企業的智慧型安全性分析和威脅情報，並針對警示偵測、威脅可見性、主動式搜捕及回應威脅提供單一解決方案。

* [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [使用 Azure Active Directory 授權事件中樞資源的存取權](https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory)

* [如何讓 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AD）做為您 Azure Data Factory 資源（例如 Azure SQL Database 或 Azure 虛擬機器）的中央驗證和授權系統。 如需控制平面（Azure 入口網站）上的帳戶登入行為偏差，請使用 Azure AD Identity Protection 和風險偵測功能來設定對偵測到與使用者身分識別相關之可疑動作的自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

* [如何檢視有風險的 Azure AD 登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [使用 SQL 設定及管理 Azure Active Directory 驗證](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell)

* [啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指引**：在 Microsoft 需要存取客戶資料的支援案例中，Azure 客戶加密箱會提供介面供客戶審查及核准或拒絕客戶資料存取要求。 請注意，雖然 Azure 加密箱無法供 Azure Data Factory 本身使用，但 Azure 加密箱確實支援 Azure SQL Database 和 Azure 虛擬機器。

* [瞭解客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

使用 Azure SQL Database 資料探索和分類功能。 資料探索和分類提供了內建于 Azure SQL Database 的先進功能，可用於探索、分類、標記 &amp; 保護資料庫中的敏感性資料。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何使用 Azure SQL Server 的資料探索和分類](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 整合執行時間應該以虛擬網路（VNet）/子網區隔，並適當地標記。

 您也可以使用私人端點來執行網路隔離。 Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [瞭解私用連結](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：若為數據源（例如 Azure SQL Database）儲存或處理 Azure Data Factory 部署的機密資訊，請使用標記將相關資源標示為機密。

若有可用的私用連結，請使用私人端點來保護任何連結到您 Azure Data Factory 管線的資源。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您也可以在網路安全性群組（NSG）上設定一組嚴格的輸出規則，並將該 NSG 與您的子網產生關聯，藉此降低資料外泄的風險。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [瞭解 Azure 私人連結](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：如果雲端資料存放區支援 HTTPS 或 tls，Data Factory 和雲端資料存放區之間的資料移動服務之間的所有資料傳輸都會透過安全通道 HTTPS 或 TLS 進行。 使用的 TLS 版本為 1.2。

所有連到 Azure SQL Database 和「Azure SQL 資料倉儲」的連線在資料透過傳輸進出資料庫時，需要加密 (SSL/TLS)。 當您使用 JSON 編輯器來編寫管線時，在連接字串中新增 encryption 屬性，並將它設定為 true。 針對「Azure 儲存體」，您可以在連接字串中使用 HTTPS。

* [瞭解 Azure Data Factory 中的傳輸中加密](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針：如果您使用 Azure Data Factory 來複製和轉換 Azure SQL Database 實例，請使用 Azure SQL Database 資料探索和分類功能。 資料探索和分類提供了內建于 Azure SQL Database 的先進功能，可用於探索、分類、標記 &amp; 保護資料庫中的敏感性資料。

資料探索和分類功能尚無法供其他 Azure 服務使用。

* [如何使用 Azure SQL Server 的資料探索和分類](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用以角色為基礎的存取控制來控制對資源的存取

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AD）角色型存取控制（RBAC）來控制 Azure Data Factory 控制平面（Azure 入口網站）的存取權。

若要建立 Data Factory 執行個體，您用來登入 Azure 的使用者帳戶必須為「參與者」或「擁有者」角色，或是 Azure 訂用帳戶的「管理員」。

如需 Data Factory 資料來源（例如 Azure SQL Database），請參閱該服務的安全性基準，以取得有關 RBAC 的詳細資訊。

* [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

* [Azure Data Factory 的角色和權限](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：建議您為與 Azure Data Factory 部署相關的任何資料存放區啟用資料加密機制。 如需有關待用資料加密的詳細資訊，您可以參考該服務的安全性基準。

如果您是在 Azure 虛擬機器中執行 Integration Runtime，Windows 虛擬機器（VM）上的虛擬磁片會使用伺服器端加密或 Azure 磁片加密（ADE）在待用狀態中進行加密。 Azure 磁碟加密會利用 Windows 的 BitLocker 功能，在來賓 VM 中使用客戶管理的金鑰來加密受控磁碟。 使用客戶管理的金鑰進行伺服器端加密，可讓您藉由加密儲存庫服務中的資料，對您的 VM 使用任何作業系統類型和映像，而改善 ADE 的效能。

您可以在 Azure Key Vault 中儲存認證或秘密值，並在管線執行期間將其用於傳遞至您的活動。 您也可以在 Azure Key Vault 中儲存資料存放區和計算的認證。 執行活動 (該活動使用資料存放區/計算) 時，Azure Data Factory 會擷取認證。

* [瞭解 Azure Data Factory 中的待用加密](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)

* [Azure 受控磁片的伺服器端加密](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption)

* [適用于 Windows Vm 的 Azure 磁碟加密](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption-overview)

* [如何在管線活動中使用 Azure Key Vault 秘密](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [如何 Azure Key Vault 中的認證](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄來建立 Azure Data Factory 和相關資源發生變更時的警示。

* [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Azure 儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging) (部分機器翻譯)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：如果您使用 Azure SQL Database 做為資料存放區，請啟用 Azure SQL Database 的 Advanced data Security，並遵循在 Azure SQL server 上執行弱點評定 Azure 資訊安全中心上的建議。

如果您是在 Azure 虛擬機器（VM）中執行 Integration Runtime，請遵循在 Vm 上執行弱點評定 Azure 資訊安全中心中的建議。 使用 Azure 安全性建議或協力廠商解決方案來執行虛擬機器的弱點評量。

* [如何在 Azure SQL Database 上執行弱點評定](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [如何啟用 Advanced Data Security](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [如何實行 Azure 資訊安全中心弱點評估建議](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，請使用 azure 更新管理解決方案來管理 vm 的更新和修補程式。 更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 System Center Updates Publisher （Updates Publisher）之類的工具可讓您將自訂更新發佈至 Windows Server Update Services （WSUS）。 此案例可讓更新管理利用協力廠商軟體，修補使用 Configuration Manager 作為其更新存放庫的機器。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

* [Azure 中的更新管理解決方案](https://docs.microsoft.com/azure/automation/automation-update-management)

* [管理 Azure VM 的更新和修補程式](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體標題部署自動修補程式管理解決方案

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，您可以使用協力廠商的修補程式管理解決方案。 您可以使用 Azure 更新管理解決方案來管理虛擬機器的更新和修補程式。 更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 System Center Updates Publisher （Updates Publisher）之類的工具可讓您將自訂更新發佈至 Windows Server Update Services （WSUS）。 此案例可讓更新管理利用協力廠商軟體，修補使用 Configuration Manager 作為其更新存放庫的機器。

* [Azure 中的更新管理解決方案](https://docs.microsoft.com/azure/automation/automation-update-management)

* [管理 Azure VM 的更新和修補程式](https://docs.microsoft.com/azure/automation/automation-tutorial-update-management)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針：如果您在 Azure 虛擬機器上執行 Integration Runtime，請以一致的間隔匯出掃描結果，並比較結果，以確認是否已補救弱點。 當您使用 Azure 資訊安全中心建議的弱點管理建議時，您可以將資料透視到選取的解決方案入口網站，以查看歷程記錄掃描資料。

* [瞭解虛擬機器的整合式弱點掃描器](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：如果您在 Azure 虛擬機器中執行 Integration Runtime，您可以使用原生弱點掃描器。 Azure 資訊安全中心隨附的弱點掃描器是由 Qualys 提供技術支援。 Qualys 的掃描器是用來即時識別 Azure 虛擬機器中弱點的引領工具。

資訊安全中心會在發現弱點時，以建議的形式來呈現結果和相關資訊。 相關資訊包括補救步驟、相關的 CVE、CVSS 分數等等。 您可以針對一或多個訂用帳戶或特定虛擬機器，來查看已識別的弱點。

* [虛擬機器的整合式弱點掃描器](https://docs.microsoft.com/azure/security-center/built-in-vulnerability-assessment)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

* [如何使用 Azure Resource Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，使用 Azure 原則來限制可以使用下列內建原則定義，在客戶訂用帳戶中建立的資源類型：
- 不允許的資源類型
- 允許的資源類型

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指引**：定義已核准的 Azure 資源，以及適用於計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用 Azure 原則來限制可在您的訂用帳戶中建立的資源類型。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，請利用 Azure 虛擬機器清查，自動收集有關虛擬機器上所有軟體的資訊。 Azure 自動化會在部署、作業和解除委任工作負載與資源期間，提供完整的控制權。

注意： [軟體名稱]、[版本]、[發行者] 和 [重新整理時間] 可從 Azure 入口網站取得。 若要取得安裝日期和其他資訊的存取權，客戶必須啟用來賓層級診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

* [Azure 自動化簡介](https://docs.microsoft.com/azure/automation/automation-intro)

* [如何啟用 Azure VM 清查](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針：如果您在 Azure 虛擬機器中執行 Integration Runtime，Azure 自動化會在部署、操作和解除委任工作負載和資源期間，提供完整的控制權。 您可以使用變更追蹤來識別虛擬機器上安裝的所有軟體。 您可以執行自己的進程，或使用 Azure 自動化狀態設定來移除未經授權的軟體。

* [Azure 自動化簡介](https://docs.microsoft.com/azure/automation/automation-intro)

* [使用變更追蹤解決方案來追蹤環境中的變更](https://docs.microsoft.com/azure/automation/change-tracking)

* [Azure 自動化狀態設定總覽](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：如果您是在 Azure 虛擬機器（VM）中執行 Integration Runtime，請使用 Azure 資訊安全中心彈性應用程式控制，以確保只有已授權的軟體會執行，而且所有未經授權的軟體都會遭到封鎖而無法在 vm 上執行。

* [如何使用 Azure 資訊安全中心適應性應用程式控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導**方針：彈性應用程式控制是來自 Azure 資訊安全中心的智慧型、自動化、端對端解決方案，可協助您控制哪些應用程式可在您的 azure 和非 azure 機器上執行（Windows 和 Linux）。 如果這不符合您組織的需求，請執行協力廠商解決方案。

請注意，這僅適用于您的 Integration Runtime 在 Azure 虛擬機器中執行的情況。

* [如何使用 Azure 資訊安全中心適應性應用程式控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導**方針：如果您在 Azure 虛擬機器中執行執行時間整合，視腳本類型而定，您可以使用作業系統特定的設定或協力廠商資源來限制使用者在 azure 計算資源內執行腳本的能力。 您也可以利用 Azure 資訊安全中心的彈性應用程式控制，確保只有授權的軟體會執行，而且所有未經授權的軟體都遭封鎖而無法在 Azure 虛擬機器上執行。

* [如何控制 Windows 環境中的 PowerShell 腳本執行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [如何使用 Azure 資訊安全中心適應性應用程式控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導**方針：在您的 azure 環境中部署的高風險應用程式可能會使用虛擬網路、子網、訂用帳戶、管理群組等來進行隔離，並使用 Azure 防火牆、Web 應用程式防火牆（WAF）或網路安全性群組（NSG）進行充分的保護。

* [Azure 中的虛擬網路和虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [何謂 Azure 防火牆？](https://docs.microsoft.com/azure/firewall/overview)

* [什麼是 Azure Web 應用程式防火牆？](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [什麼是 Azure 虛擬網路？](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [使用 Azure 管理群組來組織資源](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [訂用帳戶決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：使用 Azure 原則定義和執行 Azure Data Factory 的標準安全性設定。 使用 "DataFactory" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure Data Factory 實例的設定。

* [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：如果您在 Azure 虛擬機器中執行執行時間整合，請使用 Azure 資訊安全中心建議 [修復您虛擬機器] 上安全性設定中的弱點，以維護所有計算資源的安全性設定。

* [如何監視 Azure 資訊安全中心建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [如何補救 Azure 資訊安全中心建議](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

* [建立 Azure Resource Manager 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，請注意有數個選項可維護 vm 的安全設定以進行部署：
- Azure Resource Manager 範本：這些是用來從 Azure 入口網站部署 VM 的 JSON 型檔案，而自訂範本則需要維護。 Microsoft 會在基底範本上執行維護。
- 自訂虛擬硬碟（VHD）：在某些情況下，可能需要使用自訂 VHD 檔案，例如處理無法透過其他方式管理的複雜環境。 -Azure 自動化狀態設定：部署基本 OS 之後，這可用於更細微的設定控制，並透過自動化架構強制執行。

在大部分的情況下，與 Azure 自動化 Desired State Configuration 結合的 Microsoft 基底 VM 範本，可以協助滿足和維護安全性需求。

* [如何下載 VM 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [建立 Azure Resource Manager 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [如何將自訂 VM VHD 上傳至 Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

* [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導**方針：如果使用自訂映射，請使用角色型存取控制（RBAC），以確保只有授權的使用者可以存取映射。 針對容器映射，請將其儲存在 Azure Container Registry 中，並利用 RBAC 來確保只有授權的使用者可以存取影像。

Data Factory 參與者角色可以用來建立和管理 Data factory，以及其中的子資源。

* [瞭解 Azure 中的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [瞭解 Container Registry 的 RBAC](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

* [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

* [Azure Data Factory 的角色和權限](https://docs.microsoft.com/azure/data-factory/concepts-roles-permissions)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指引**：在 "DataFactory" 命名空間中使用內建的 Azure 原則定義和 Azure 原則別名，以建立自訂原則來警示、審查和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導**方針：如果您的 Integration Runtime 在 Azure 虛擬機器中執行，則可適用此建議。 Azure 自動化狀態設定是適用于任何雲端或內部部署資料中心內 Desired State Configuration （DSC）節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指引**：在 "DataFactory" 命名空間中使用內建的 Azure 原則定義和 Azure 原則別名，以建立自訂原則來警示、審查和強制執行系統組態。 使用 Azure 原則 [audit]、[deny] 和 [deploy if not 存在] 自動強制執行 Azure 資源的設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導**方針：如果您的 Integration Runtime 在 Azure 虛擬機器中執行，則可適用此建議。 Azure 自動化狀態設定是適用于任何雲端或內部部署資料中心內 Desired State Configuration （DSC）節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：搭配 Azure Key Vault 使用受控服務識別，以簡化及保護雲端應用程式的秘密管理。

您也可以在 Azure Key Vault 中儲存認證或秘密值，並在管線執行期間將其用於傳遞至您的活動。 請確定已啟用虛刪除。

* [如何與 Azure 受控識別整合](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [如何建立 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [如何使用受控識別提供 Key Vault 驗證](https://docs.microsoft.com/azure/key-vault/managed-identity)

* [在管線活動中使用 Azure Key Vault 祕密](https://docs.microsoft.com/azure/data-factory/how-to-use-azure-key-vault-secrets-pipeline-activities)

* [Azure Key Vault 中的虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：建立資料處理站時，可以建立受控識別，並搭配 factory 建立。 受控識別是向 Azure Active Directory 註冊的受控應用程式，代表此特定的 data factory。

* [Azure Data Factory 的受控識別](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：如果您在 Azure 虛擬機器中執行 Integration Runtime，您可以使用 azure Windows 虛擬機器的 Microsoft Antimalware 持續監視及保護您的資源。

* [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：支援 Azure 服務的基礎主機 (例如 Azure App Service) 已啟用 Microsoft 反惡意程式碼功能，但不會在您的內容上執行該功能。

預先掃描任何正在上傳到非計算 Azure 資源的檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。

使用適用于資料服務的 Azure 資訊安全中心威脅偵測來偵測已上傳至儲存體帳戶的惡意程式碼。

* [瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [瞭解 Azure 資訊安全中心的資料服務威脅偵測](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導**方針：部署之後，Azure 的 Microsoft Antimalware 預設會自動安裝最新的簽章、平臺及引擎更新。 遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」，以確保所有端點都是最新的簽章。 Windows OS 可以使用額外的安全性進一步保護，以使用與 Azure 資訊安全中心整合的 Microsoft Defender Advanced 威脅防護服務來限制病毒或惡意程式碼攻擊的風險。

* [如何部署 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Microsoft Defender 進階威脅防護](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料復原](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，請啟用 Azure 備份並設定 VM，以及自動備份所需的頻率和保留期限。

針對您的任何資料存放區，請參閱該服務的安全性基準，以取得如何執行定期、自動備份的建議。

* [Azure VM 備份總覽](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [從 VM 設定備份 Azure VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，請啟用 Azure 備份和目標 azure vm，以及所需的頻率和保留週期。 備份 Azure Key Vault 中客戶管理的金鑰。

針對您的任何資料存放區，請參閱該服務的安全性基準，以取得如何執行定期、自動備份的建議。

* [Azure VM 備份總覽](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [如何在 Azure 中備份金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：如果您在 Azure 虛擬機器中執行 Integration Runtime，請確定在 Azure 備份內定期執行內容資料還原的能力。 如有必要，請測試將內容還原至隔離的 VLAN。 定期測試已備份客戶管理金鑰的還原。

針對您的任何資料存放區，請參閱該服務的安全性基準，以取得驗證備份的指引。

* [如何從 Azure 虛擬機器備份復原檔案](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm)

* [如何在 Azure 中還原金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指導**方針：如果您在 Azure 虛擬機器（VM）中執行 Integration Runtime，並將該 vm 與 Azure 備份搭配使用，您的 vm 會使用儲存體服務加密（SSE）進行待用加密。 Azure 備份也可以使用 Azure 磁碟加密來備份已加密的 Azure Vm。 Azure 磁碟加密與 BitLocker 加密金鑰（Bek）整合，其在金鑰保存庫中受到保護以作為秘密。 Azure 磁碟加密也會與 Azure Key Vault 金鑰加密金鑰（Kek）整合。 在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

* [Vm 的虛刪除](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete)

* [Azure Key Vault 虛刪除概觀](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [如何設定 Azure 資訊安全中心內的工作流程自動化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [客戶也可以利用 NIST 的「電腦安全性性」事件處理指南，協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

* [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心的安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Sentinel。

* [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

* [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期滲透測試您的 Azure 資源，並確保修復所有重要的安全性結果

**指導**方針： 

* [請遵循 Microsoft Engagement 規則，以確保您的滲透測試不會違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
