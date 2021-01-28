---
title: 適用于 Azure Data Factory 的 Azure 安全性基準
description: 適用于 Azure Data Factory 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 690dcca23aedb129090b3bf4f0da8874705b5fcc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941477"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>適用于 Azure Data Factory 的 Azure 安全性基準

適用于 Azure Data Factory 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：建立 AZURE-SSIS INTEGRATION RUNTIME (IR) 時，您可以選擇將它與虛擬網路聯結。 這可讓 Azure Data Factory 建立特定的網路資源，例如 (NSG) 的網路安全性群組和負載平衡器。 您也可以提供自己的靜態公用 IP 位址，或 Azure Data Factory 為您建立一個位址。 在 Azure Data Factory 自動建立的 NSG 上，預設會針對所有流量開啟埠3389。 請將此鎖定，以確保只有您的系統管理員具有存取權。

您可以在內部部署機器或虛擬網路內的 Azure 虛擬機器上部署 Self-Hosted ir。 確定您的虛擬網路子網部署具有設定為僅允許系統管理存取權的 NSG。 根據預設，Azure-SSIS IR 在每個 IR 節點上的 windows 防火牆規則預設不允許埠3389輸出以進行保護。 您可以藉由將 NSG 與子網建立關聯，並設定嚴格的規則，來保護虛擬網路設定的資源。

如果有可用的 Private Link，請使用私人端點來保護任何連結至 Azure Data Factory 管線的資源，例如 Azure SQL Server。 使用 Private Link 時，您的虛擬網路與服務之間的流量會流經 Microsoft 骨幹網路，以消除公用網際網路的暴露。

* [如何建立 Azure-SSIS IR](./create-azure-ssis-integration-runtime.md)

* [如何建立和設定自我裝載 IR](./create-self-hosted-integration-runtime.md)

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [將 Azure-SSIS IR 加入虛擬網路](./join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

* [瞭解 Azure Private Link](../private-link/private-link-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針：使用 Azure 資訊安全中心，並針對與您 Integration Runtime 部署相關聯的虛擬網路和網路安全性群組，修復網路保護建議。

此外，啟用網路安全性群組 (NSG) 流量記錄，以保護您的 Integration Runtime 部署並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。

您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

* [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

* [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導** 方針：在與您的 Integration Runtime 部署相關聯的虛擬網路上啟用 DDoS 保護標準，以防止分散式阻絕服務攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

* [如何設定 DDoS 保護](../ddos-protection/manage-ddos-protection.md)

* [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：啟用網路安全性群組 (NSG) 流量記錄，以保護您的 Integration Runtime 部署，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。

您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

* [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：如果您想要檢查來自 Azure-SSIS IR 的輸出流量，您可以透過 Azure ExpressRoute 強制通道或網路虛擬裝置，將從 Azure-SSIS IR 起始的流量路由傳送至內部部署防火牆設備， (從支援 IDS/IPS 功能的 Azure Marketplace NVA) 。 如果基於酬載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。

* [將 Azure-SSIS Integration Runtime 加入虛擬網路](./join-azure-ssis-integration-runtime-virtual-network.md)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：不適用;這項建議適用于裝載 web 應用程式的 Azure 應用程式服務或計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導** 方針：使用虛擬網路服務標籤來定義網路安全性群組 (NSG) 或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如，DataFactoryManagement) ，您可以允許或拒絕對應服務的輸入流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

* [瞭解和使用服務標記](../virtual-network/service-tags-overview.md)

* [瞭解 Azure Data Factory 特定的服務標記](./join-azure-ssis-integration-runtime-virtual-network.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：使用 Azure 原則定義和執行與您的 Azure data Factory 實例相關聯的網路設定和網路資源的標準安全性設定。 使用 "DataFactory" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Data Factory 實例的網路設定進行審核或強制執行。 您也可以利用與網路或 Azure Data factory 實例相關的內建原則定義，例如：
- 應啟用 DDoS 保護標準

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [適用于網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network) 

* [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對您的 Azure Data Factory 實例的網路安全性和流量流程，使用與網路安全性和流量相關的資源標記，以提供中繼資料和邏輯組織。

使用與標記相關的任何內建 Azure 原則定義 (例如「需要標籤及其值」)，以確保建立的所有資源都具有標籤，並向您通知未加上標籤的現有資源。

您可使用 Azure PowerShell 或 Azure CLI，根以據資源的標籤對資源進行查詢或執行動作。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視網路資源設定，並偵測與您的 Azure Data Factory 實例相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

* [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的 Azure Data Factory。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：透過 Azure 監視器內嵌記錄，以匯總 Azure Data Factory 所產生的安全性資料。 在 Azure 監視器中，您可以查詢設定為接收 Azure Data Factory 活動記錄的 Log Analytics 工作區。 使用適用於長期/封存記錄儲存體或事件中樞的 Azure 儲存體帳戶，將資料匯出至其他系統。

或者，您也可以啟用和內部資料來 Azure Sentinel 或協力廠商安全性事件和事件管理 (SIEM) 。您也可以整合 Azure Data Factory 與 Git，以利用數個原始檔控制的優點，例如追蹤/審核變更的能力，以及還原引進 bug 之變更的能力。

* [如何設定診斷設定](../azure-monitor/platform/diagnostic-settings.md)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure Data Factory 中的原始檔控制](./source-control.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：若要進行控制平面審核記錄，請啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶以供封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

您可以使用診斷設定來設定 Azure Data Factory 中 noncompute 資源的診斷記錄，例如計量和管線執行資料。 Azure Data Factory 會將管線執行資料儲存45天。 若要將此資料保留較長的一段時間，請將您的診斷記錄儲存至儲存體帳戶以進行審核或手動檢查，並以天為單位指定保留時間。 您也可以串流記錄以 Azure 事件中樞，或將記錄傳送至 Log Analytics 工作區以進行分析。

* [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

* [瞭解 Azure Data Factory 診斷記錄](./monitor-using-azure-monitor.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) ，您可以使用 Azure 監視器從虛擬機器收集資料。 安裝 Log Analytics VM 擴充功能可讓 Azure 監視器從您的 Azure Vm 收集資料。 Azure 資訊安全中心接著可以為虛擬機器提供安全性事件記錄檔監視。 由於安全性事件記錄檔所產生的資料量，因此預設不會儲存。

如果您的組織想要保留安全性事件記錄檔資料，它可以儲存在資料收集層內，此時可以在 Log Analytics 中進行查詢。

* [如何在 Azure 監視器中從 Azure 虛擬機器收集資料](../azure-monitor/learn/quick-collect-azurevm.md)

* [在 Azure 資訊安全中心中啟用資料收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：啟用 Azure Data Factory 的診斷設定。 如果選擇將記錄儲存在 Log Analytics 工作區中，請根據貴組織的合規性法規來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

* [如何在 Azure Data Factory 中啟用診斷記錄](./monitor-using-azure-monitor.md)

* [如何設定 Log Analytics 工作區的記錄保留期參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：啟用 Azure Data Factory 的診斷設定，並將記錄傳送至 Log Analytics 工作區。 使用 Log Analytics 分析和監視記錄的異常行為，並定期查看結果。 確定您也針對與 Azure Data Factory 部署相關的任何資料存放區啟用診斷設定。 請參閱每個服務的安全性基準，以取得如何啟用診斷設定的指引。

如果您是在 Azure 虛擬機器 (VM) 中執行 Integration Runtime，也請啟用 VM 的診斷設定。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [Log Analytics 架構](./monitor-using-azure-monitor.md#schema-of-logs-and-events)

* [如何使用 Azure 監視器從 Azure 虛擬機器收集資料](../azure-monitor/learn/quick-collect-azurevm.md)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：移至 Azure 監視器中的 [警示計量] 區段，即可針對 Data Factory 中支援的計量提出警示 &amp; 。

針對 Azure Data Factory 設定診斷設定，並將記錄傳送至 Log Analytics 工作區。 在您的 Log Analytics 工作區中，設定在一組預先定義的條件發生時，所要採取的警示。 或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

此外，請確定您已啟用與資料存放區相關之服務的診斷設定。 您可以參考每個服務的安全性基準，以取得指導方針。

* [Azure Data Factory 中的警示](./monitor-visually.md#alerts)

* [所有支援的計量頁面](../azure-monitor/platform/metrics-supported.md)

* [如何在 Log Analytics 工作區中設定警示](../azure-monitor/platform/alerts-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：如果您是在 Azure 虛擬機器中執行您的 Integration Runtime，您可以使用 Microsoft Antimalware 作為 Azure 雲端服務和虛擬機器，並將虛擬機器設定為將事件記錄到 Azure 儲存體帳戶。 設定 Log Analytics 工作區，以內嵌來自儲存體帳戶的事件，並在適當時建立警示。 遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」。

* [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [如何啟用虛擬機器的來賓層級監視](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用;Azure Data Factory 不會處理或產生 DNS 相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) ，您可以啟用命令列審核記錄。 Azure 資訊安全中心為 Azure Vm 提供安全性事件記錄檔監視。 資訊安全中心會在所有支援的 Azure Vm 上布建 Microsoft Monitoring Agent，以及在啟用自動布建或您可以手動安裝代理程式時所建立的任何新虛擬機器。 代理程式支援處理序建立事件 4688，及事件 4688 中的 CommandLine 欄位。 EventLog 會記錄在 VM 上建立的新處理序，並由資訊安全中心的偵測服務監視。

* [Azure 資訊安全中心的資料收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導** 方針： Azure Data Factory 中，請務必定期追蹤和協調使用者存取權。 若要建立 Data Factory 執行個體，您用來登入 Azure 的使用者帳戶必須為「參與者」  或「擁有者」  角色，或是 Azure 訂用帳戶的「管理員」  。

此外，在租使用者層級，Azure Active Directory (AD) 具有必須明確指派且可查詢的內建角色。 使用 Azure AD PowerShell 模組來執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶，而這些帳戶具有您 Azure Data Factory 實例控制平面的系統管理存取權。

雖然 Azure AD 是管理使用者存取權的建議方法，但請記住，如果您是在 Azure 虛擬機器中執行 Integration Runtime (VM) ，則您的 VM 也可能有本機帳戶。 本機和網域帳戶都應該以最少的使用量進行審核及管理。 此外，我們也建議您針對「即時」功能審核特殊許可權身分識別管理員，以降低系統管理許可權的可用性。

* [Azure Data Factory 的角色和權限](./concepts-roles-permissions.md)

* [特殊許可權身分識別管理員的資訊](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember)

* [本機帳戶的資訊](../active-directory/devices/assign-local-admin.md#manage-the-device-administrator-role)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure Data Factory 使用 AZURE ACTIVE DIRECTORY (AD) 來提供 Azure 入口網站和 Azure Data Factory 主控台的存取權。 Azure AD 沒有預設密碼的概念，但是您必須負責變更或不允許任何自訂或協力廠商應用程式的預設密碼。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：使用專用的系統管理帳戶來建立標準作業程式，以存取 Azure 控制平面 (Azure 入口網站) 以及 Azure Data Factory 主控台。 使用 Azure 資訊安全中心身分識別和存取管理來監視 Azure AD 內的系統管理帳戶數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：
- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

如果您是在 Azure 虛擬機器上執行 Integration Runtime，Azure 虛擬機器上的系統管理員帳戶也可以使用 Azure 特殊許可權身分識別管理員設定 (PIM) 。 Azure 特殊許可權身分識別管理員提供數個選項，例如及時提高許可權、Multi-Factor Authentication 和委派選項，因此許可權僅適用于特定的時間範圍，而且需要第二人核准。

* [瞭解 Azure 資訊安全中心身分識別和存取權](../security-center/security-center-identity-access.md)

* [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [特殊許可權身分識別管理員的資訊](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [Azure Data Factory 的角色和權限](./concepts-roles-permissions.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：使用 Azure 應用程式註冊 (服務主體) 來取出您的應用程式或函式可用來存取和與您的復原服務保存庫進行互動的權杖。

* [如何呼叫 Azure REST Api](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [如何使用 Azure AD 向 (服務主體) 註冊用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure 復原服務 API 資訊](/rest/api/recoveryservices/)

* [Azure Data Factory REST API 的相關資訊](/rest/api/datafactory/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory Multi-Factor Authentication (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有的系統管理工作

**指引**：使用已設定多重要素驗證 (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

* [了解特殊權限存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

如果您是在 Azure 虛擬機器上執行 Integration Runtime (VM) ，您也可以將 VM 上線以進行 Azure Sentinel。 Microsoft Azure Sentinel 是可調整的雲端原生安全性資訊事件管理 (SIEM) 和安全性協調流程自動化回應 (SOAR) 解決方案。 Azure Sentinel 提供整個企業的智慧型安全性分析和威脅情報，並針對警示偵測、威脅可見性、主動式搜捕及回應威脅提供單一解決方案。

* [如何識別已標示為有風險活動的 Azure AD 使用者](../active-directory/identity-protection/overview-identity-protection.md)

* [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](../security-center/security-center-identity-access.md)

* [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。

* [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：資料處理站可以與代表特定資料處理站的 Azure 資源受控識別相關聯。 您可以使用此受控識別來進行 Azure SQL Database 驗證。 指定的處理站可以使用此身分識別從您資料庫存取資料，或從您的資料庫複製資料。

如果您是在 Azure 虛擬機器上執行 Integration Runtime (IR) ，則可以使用受控識別向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不需要在您的程式碼中使用任何認證。 您在虛擬機器上執行的程式碼可以使用受控識別來要求支援 Azure AD authentication 之服務的存取權杖。

* [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* [什麼是 Azure 資源受控識別？](../active-directory/managed-identities-azure-resources/overview.md)

* [使用 Azure Data Factory 在 Azure SQL Database 中複製和轉換資料](./connector-azure-sql-database.md)

* [如何使用 Azure SQL Database 來設定和管理 Azure Active Directory authentication](../azure-sql/database/authentication-aad-configure.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針： AZURE ACTIVE DIRECTORY (AD) 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

如果您執行的是 Azure 虛擬機器中的執行時間整合，您將需要檢查本機安全性群組和使用者，以確定沒有任何未預期的帳戶可能會危害系統。

* [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

* [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：您可以存取 AZURE ACTIVE DIRECTORY (AD) 登入活動、audit 和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。 您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 中設定所需的記錄警示。

如果您是在 Azure 虛擬機器中執行 Integration Runtime (VM) ，請在該 VM 上進行 Azure Sentinel。 Microsoft Azure Sentinel 是可調整的雲端原生安全性資訊事件管理 (SIEM) 和安全性協調流程自動化回應 (SOAR) 解決方案。 Azure Sentinel 提供整個企業的智慧型安全性分析和威脅情報，並針對警示偵測、威脅可見性、主動式搜捕及回應威脅提供單一解決方案。

* [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [使用 Azure Active Directory 授權存取事件中樞資源](../event-hubs/authorize-access-azure-active-directory.md)

* [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為 Azure Data Factory 資源（例如 Azure SQL Database 或 Azure 虛擬機器）的中央驗證和授權系統。 針對控制項平面上的帳戶登入行為偏差 (Azure 入口網站) ，請使用 Azure AD Identity Protection 和風險偵測功能，來設定偵測到與使用者身分識別相關的可疑動作的自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

* [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

* [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [使用 SQL 設定及管理 Azure Active Directory 驗證](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell)

* [啟用適用於 Azure-SSIS Integration Runtime 的 Azure Active Directory 驗證](./enable-aad-authentication-azure-ssis-ir.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：在 Microsoft 需要存取客戶資料的支援案例中，Azure 客戶加密箱會提供一個介面，讓客戶可以檢查及核准或拒絕客戶資料存取要求。 請注意，雖然 Azure 加密箱無法用於 Azure Data Factory 本身，但 Azure 加密箱可支援 Azure SQL Database 和 Azure 虛擬機器。

* [瞭解客戶加密箱](../security/fundamentals/customer-lockbox-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

使用 Azure SQL Database 資料探索和分類功能。 資料探索和分類提供內建于 Azure SQL Database 的先進功能，可用於探索、分類、標記 &amp; 保護資料庫中的敏感性資料。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何使用 Azure SQL Server 的資料探索和分類](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 整合執行時間應以虛擬網路 (VNet) /子網，並適當地標記。

 您也可以使用私人端點來執行網路隔離。 Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [瞭解 Private Link](../private-link/private-endpoint-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：針對資料來源 (例如 Azure SQL Database) 儲存或處理 Azure Data Factory 部署的機密資訊，請使用標記將相關的資源標記為機密。

如果有可用的 Private Link，請使用私人端點來保護任何連結至 Azure Data Factory 管線的資源。 虛擬網路和服務間的流量會在通過 Microsoft 骨幹網路時隨之減少，降低資料在網際網路中公開的風險。 您也可以在網路安全性群組上設定一組嚴格的輸出規則 (NSG) ，並將該 NSG 與您的子網產生關聯，以降低資料遭到外泄的風險。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [瞭解 Azure Private Link](../private-link/private-link-overview.md)

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：如果雲端資料存放區支援 HTTPS 或 tls，則 Data Factory 中的資料移動服務與雲端資料存放區之間的所有資料傳輸，都會透過安全通道 HTTPS 或 tls。 使用的 TLS 版本為 1.2。

當資料與資料庫之間傳輸時，與 Azure SQL Database 和 Azure Synapse Analytics 的所有連接都需要加密 (SSL/TLS) 。 當您使用 JSON 編輯器來編寫管線時，在連接字串中新增 encryption 屬性，並將它設定為 true。 針對「Azure 儲存體」，您可以在連接字串中使用 HTTPS。

* [瞭解 Azure Data Factory 中的傳輸加密](./data-movement-security-considerations.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：如果您使用 Azure Data Factory 複製和轉換 Azure SQL Database 實例，請使用 Azure SQL Database 資料探索和分類功能。 資料探索和分類提供內建于 Azure SQL Database 的先進功能，可用於探索、分類、標記 &amp; 保護資料庫中的敏感性資料。

其他 Azure 服務尚無法使用資料探索和分類功能。

* [如何使用 Azure SQL Server 的資料探索和分類](../azure-sql/database/data-discovery-and-classification-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 來控制 Azure 入口網站)  (Azure Data Factory 控制平面的存取權。

若要建立 Data Factory 執行個體，您用來登入 Azure 的使用者帳戶必須為「參與者」  或「擁有者」  角色，或是 Azure 訂用帳戶的「管理員」  。

針對您的 Data Factory 資料來源（例如 Azure SQL Database），請參閱該服務的安全性基準，以取得有關 Azure RBAC 的詳細資訊。

* [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [Azure Data Factory 的角色和權限](./concepts-roles-permissions.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針： Azure 儲存體或計算資源尚無法使用資料識別、分類和遺失防護功能。 若需要達到合規性目標，請實作協力廠商解決方案。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：建議您針對與 Azure Data Factory 部署相關的任何資料存放區啟用資料加密機制。 您可以參考該服務的安全性基準，以取得待用資料加密的相關資訊。

如果您是在 Azure 虛擬機器中執行 Integration Runtime，Windows 虛擬機器 (VM) 上的虛擬磁片會使用伺服器端加密或 Azure 磁片加密 (ADE) 進行靜態加密。 Azure 磁碟加密會利用 Windows 的 BitLocker 功能，在來賓 VM 中使用客戶管理的金鑰來加密受控磁碟。 使用客戶管理的金鑰進行伺服器端加密，可讓您藉由加密儲存庫服務中的資料，對您的 VM 使用任何作業系統類型和映像，而改善 ADE 的效能。

您可以將認證或秘密值儲存在 Azure Key Vault 中，並在管線執行期間使用它們來傳遞至您的活動。 您也可以在 Azure Key Vault 中儲存資料存放區和計算的認證。 執行活動 (該活動使用資料存放區/計算) 時，Azure Data Factory 會擷取認證。

* [瞭解 Azure Data Factory 中的靜止加密](./data-movement-security-considerations.md)

* [Azure 受控磁片的伺服器端加密](../virtual-machines/disk-encryption.md)

* [適用于 Windows Vm 的 Azure 磁碟加密](../virtual-machines/windows/disk-encryption-overview.md)

* [如何在管線活動中使用 Azure Key Vault 秘密](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [如何 Azure Key Vault 中的認證](./store-credentials-in-key-vault.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄檔，以建立 Azure Data Factory 和相關資源發生變更時的警示。

* [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

* [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

* [Azure 儲存體分析記錄](../storage/common/storage-analytics-logging.md) (部分機器翻譯)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：如果您使用 Azure SQL Database 作為資料存放區，請啟用 Azure SQL Database 的 Advanced data Security，並遵循 Azure 資訊安全中心在 Azure SQL server 上執行弱點評定的建議。

如果您是在 Azure 虛擬機器 (VM) 中執行 Integration Runtime，請遵循 Azure 資訊安全中心在 Vm 上執行弱點評定的建議。 使用「Azure 安全性建議」或協力廠商解決方案來執行虛擬機器的弱點評定。

* [如何在 Azure SQL Database 上執行弱點評定](../azure-sql/database/sql-vulnerability-assessment.md)

* [如何啟用 Advanced Data Security](../azure-sql/database/azure-defender-for-sql.md)

* [如何實行 Azure 資訊安全中心弱點評定建議](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針：如果您是在 Azure 虛擬機器 (VM) 中執行 Integration Runtime，請使用 Azure 更新管理解決方案來管理 vm 的更新和修補程式。 更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 System Center Updates Publisher (Updates Publisher) 之類的工具可讓您將自訂更新發佈至 Windows Server Update Services (WSUS) 。 此案例可讓更新管理修補使用設定管理員作為其更新存放庫（具有協力廠商軟體）的機器。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制和功能。

* [Azure 中的更新管理解決方案](../automation/update-management/overview.md)

* [管理 Azure VM 的更新和修補程式](../automation/update-management/manage-updates-for-vm.md)

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) ，您可以使用協力廠商修補程式管理解決方案。 您可以使用 Azure 更新管理解決方案來管理虛擬機器的更新和修補程式。 更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 System Center Updates Publisher (Updates Publisher) 之類的工具可讓您將自訂更新發佈至 Windows Server Update Services (WSUS) 。 此案例可讓更新管理修補使用設定管理員作為其更新存放庫（具有協力廠商軟體）的機器。

* [Azure 中的更新管理解決方案](../automation/update-management/overview.md)

* [管理 Azure VM 的更新和修補程式](../automation/update-management/manage-updates-for-vm.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針：如果您是在 Azure 虛擬機器上執行 Integration Runtime，請在一致的間隔匯出掃描結果，並比較結果，以確認已補救弱點。 使用 Azure 資訊安全中心建議的弱點管理建議時，您可能會進入所選解決方案的入口網站以查看歷史掃描資料。

* [瞭解虛擬機器的整合式弱點掃描器](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：如果您在 Azure 虛擬機器中執行您的 Integration Runtime，您可以使用原生弱點掃描器。 Azure 資訊安全中心隨附的弱點掃描器是由 Qualys 提供技術支援。 Qualys 的掃描器是用來即時識別 Azure 虛擬機器中弱點的引領工具。

資訊安全中心會在發現弱點時，以建議的形式來呈現結果和相關資訊。 相關資訊包括補救步驟、相關的 CVE、CVSS 分數等等。 您可以針對一或多個訂用帳戶或特定的虛擬機器，查看已識別的弱點。

* [虛擬機器的整合式弱點掃描器](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。 確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

* [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

* [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，您也可以使用下列內建原則定義，使用 Azure 原則對可在客戶訂用帳戶 () s 中建立的資源類型進行限制：
- 不允許的資源類型
- 允許的資源類型

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指引**：定義已核准的 Azure 資源，以及適用於計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) ，請利用 Azure 虛擬機器清查，將虛擬機器上所有軟體的相關資訊收集自動化。 Azure 自動化會在部署、作業和解除委任工作負載與資源期間，提供完整的控制權。

注意：您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得安裝日期和其他資訊的存取權，客戶必須啟用來賓層級的診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

* [Azure 自動化簡介](../automation/automation-intro.md)

* [如何啟用 Azure VM 清查](../automation/automation-tutorial-installed-software.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：如果您是在 Azure 虛擬機器中執行 Integration Runtime，Azure 自動化在部署、作業和解除委任工作負載和資源時，提供完整的控制權。 您可以使用變更追蹤來識別安裝在虛擬機器上的所有軟體。 您可以執行自己的處理常式，或使用 Azure 自動化狀態設定來移除未經授權的軟體。

* [Azure 自動化簡介](../automation/automation-intro.md)

* [使用變更追蹤解決方案來追蹤環境中的變更](../automation/change-tracking/overview.md)

* [Azure 自動化狀態設定總覽](../automation/automation-dsc-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) ，請使用 Azure 資訊安全中心的彈性應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 vm 上遭到封鎖而無法執行。

* [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導** 方針：自我調整應用程式控制是 Azure 資訊安全中心的智慧型、自動化、端對端解決方案，可協助您控制哪些應用程式可以在 azure 和非 azure 電腦上執行 (Windows 和 Linux) 。 如果這不符合您組織的需求，請執行協力廠商解決方案。

請注意，這只適用于您的 Integration Runtime 正在 Azure 虛擬機器中執行時。

* [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導** 方針：如果您是在 Azure 虛擬機器中執行執行時間整合，則根據腳本的類型，您可能會使用作業系統特定設定或協力廠商資源，以限制使用者在 azure 計算資源內執行腳本的能力。 您也可以運用 Azure 資訊安全中心的自我調整應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

* [如何在 Windows 環境中控制 PowerShell 腳本執行](/powershell/module/microsoft.powershell.security/set-executionpolicy)

* [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：在 azure 環境中部署的高風險應用程式可能會使用虛擬網路、子網、訂用帳戶、管理群組等來隔離，並使用 Azure 防火牆、Web 應用程式防火牆 (WAF) 或網路安全性群組 (NSG) 來充分保護這些應用程式。

* [Azure 中的虛擬網路和虛擬機器](../virtual-machines/network-overview.md)

* [何謂 Azure 防火牆？](../firewall/overview.md)

* [什麼是 Azure Web 應用程式防火牆？](../web-application-firewall/overview.md)

* [網路安全性群組](../virtual-network/network-security-groups-overview.md)

* [什麼是 Azure 虛擬網路？](../virtual-network/virtual-networks-overview.md)

* [使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md)

* [訂用帳戶決策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則定義和實行 Azure Data Factory 的標準安全性設定。 使用 "DataFactory" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Data Factory 實例的設定進行審核或強制執行。

* [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導** 方針：如果您要在 Azure 虛擬機器中執行您的執行時間整合，請使用 Azure 資訊安全中心建議 [補救您虛擬機器上安全性設定中的弱點]，以維護所有計算資源上的安全性設定。

* [如何監視 Azure 資訊安全中心建議](../security-center/security-center-recommendations.md)

* [如何修復 Azure 資訊安全中心建議](../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

* [建立 Azure Resource Manager 範本的資訊](../virtual-machines/windows/ps-template.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導** 方針：如果您是在 Azure 虛擬機器 (VM) 中執行 Integration Runtime，請注意有幾個選項可維護 vm 的安全設定以進行部署：
- Azure Resource Manager 範本：這些是以 JSON 為基礎的檔案，用來從 Azure 入口網站部署 VM，而且必須維護自訂範本。 Microsoft 會在基底範本上執行維護。
- 自訂虛擬硬碟 (VHD) ：在某些情況下，可能需要使用自訂 VHD 檔案，例如在處理無法透過其他方式管理的複雜環境時。 -Azure 自動化狀態設定：一旦部署基底作業系統之後，就可以使用它來更細微地控制設定，並透過自動化架構強制執行。

在大部分的情況下，與 Azure 自動化 Desired State Configuration 結合的 Microsoft 基底 VM 範本，可協助滿足和維護安全性需求。

* [如何下載 VM 範本的資訊](/previous-versions/azure/virtual-machines/windows/download-template)

* [建立 Azure Resource Manager 範本的資訊](../virtual-machines/windows/ps-template.md)

* [如何將自訂 VM VHD 上傳至 Azure](/azure-stack/operator/azure-stack-add-vm-image)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

* [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow)

* [Azure Repos 文件](/azure/devops/repos/index)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導** 方針：如果使用自訂映射，請使用 azure 角色型存取控制 (azure RBAC) ，以確保只有獲得授權的使用者可以存取影像。 針對容器映射，請將它們儲存在 Azure Container Registry 中，並利用 Azure RBAC 來確保只有授權的使用者可以存取影像。

Data Factory 參與者角色可以用來建立和管理 Data factory，以及其中的子資源。

* [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [瞭解適用于 Container Registry 的 Azure RBAC](../container-registry/container-registry-roles.md)

* [如何設定 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

* [Azure Data Factory 的角色和權限](./concepts-roles-permissions.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用內建的 Azure 原則定義以及 "DataFactory" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導** 方針：如果您的 Integration Runtime 是在 Azure 虛擬機器中執行，則可以套用這項建議。 Azure 自動化狀態設定是在任何雲端或內部部署資料中心內 Desired State Configuration (DSC) 節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](../automation/automation-dsc-onboarding.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用內建的 Azure 原則定義以及 "DataFactory" 命名空間中的 Azure 原則別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 資源的設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導** 方針：如果您的 Integration Runtime 是在 Azure 虛擬機器中執行，則可以套用這項建議。 Azure 自動化狀態設定是在任何雲端或內部部署資料中心內 Desired State Configuration (DSC) 節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](../automation/automation-dsc-onboarding.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。

您也可以將認證或秘密值儲存在 Azure Key Vault 中，並在管線執行期間使用它們來傳遞至您的活動。 確定已啟用虛刪除。

* [如何與 Azure 受控識別整合](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

* [如何建立 Key Vault](../key-vault/secrets/quick-create-portal.md)

* [如何驗證 Key Vault](../key-vault/general/authentication.md)

* [如何指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-portal.md)

* [在管線活動中使用 Azure Key Vault 祕密](./how-to-use-azure-key-vault-secrets-pipeline-activities.md)

* [Azure Key Vault 中的虛刪除](../key-vault/general/soft-delete-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：建立資料處理站時，可以建立受控識別，以及建立 factory。 受控識別是已向 Azure Active Directory 註冊的受控應用程式，並代表這個特定的資料處理站。

* [Azure Data Factory 的受控識別](./data-factory-service-identity.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導** 方針：如果您是在 Azure 虛擬機器中執行您的 Integration Runtime，您可以使用 azure Windows 虛擬機器的 Microsoft Antimalware，持續監視及保護您的資源。

* [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：支援 Azure 服務的基礎主機 (例如 Azure App Service) 已啟用 Microsoft 反惡意程式碼功能，但不會在您的內容上執行該功能。

預先掃描即將上傳至非計算 Azure 資源的任何檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。

使用 Azure 資訊安全中心的資料服務威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。

* [瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [瞭解 Azure 資訊安全中心的資料服務威脅偵測](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導** 方針：部署時，適用于 Azure 的 Microsoft Antimalware 預設會自動安裝最新的簽章、平臺和引擎更新。 遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」，以確保所有端點都是最新的簽章。 Windows OS 可利用額外的安全性進一步保護，以利用與 Azure 資訊安全中心整合的 Microsoft Defender 進階威脅防護服務，來限制病毒或惡意軟體攻擊的風險。

* [如何部署 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [Microsoft Defender 進階威脅防護](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) ，請啟用 Azure 備份並設定 vm，以及自動備份所需的頻率和保留期限。

針對您的任何資料存放區，請參閱該服務的安全性基準，以取得有關如何執行定期自動備份的建議。

* [Azure VM 備份的總覽](../backup/backup-azure-vms-introduction.md)

* [從 VM 設定備份 Azure VM](../backup/backup-azure-vms-first-look-arm.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) ，請啟用 Azure 備份和目標 azure vm，以及所需的頻率和保留期限。 Azure Key Vault 內備份客戶管理的金鑰。

針對您的任何資料存放區，請參閱該服務的安全性基準，以取得有關如何執行定期自動備份的建議。

* [Azure VM 備份的總覽](../backup/backup-azure-vms-introduction.md)

* [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：如果您是在 Azure 虛擬機器中執行 Integration Runtime，請確保能夠定期執行 Azure 備份內內容的資料還原。 如有必要，請測試將內容還原至隔離的 VLAN。 定期測試已備份之客戶管理金鑰的還原。

針對您的任何資料存放區，請參閱該服務的安全性基準，以取得驗證備份的指引。

* [如何從 Azure 虛擬機器備份復原檔案](../backup/backup-azure-restore-files-from-vm.md)

* [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：如果您要在 Azure 虛擬機器中執行 INTEGRATION RUNTIME (VM) 然後使用 Azure 備份將該 vm 還原，您的 vm 會使用儲存體服務加密 (SSE) 進行待用加密。 Azure 備份也可以使用 Azure 磁碟加密來備份已加密的 Azure Vm。 Azure 磁碟加密與 BitLocker 加密金鑰整合 (Bek) ，這些金鑰會在金鑰保存庫中做為秘密進行保護。 Azure 磁碟加密也會與 (Kek) 的 Azure Key Vault 金鑰加密金鑰整合。 在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

* [Vm 的虛刪除](../backup/backup-azure-security-feature-cloud.md)

* [Azure Key Vault 虛刪除概觀](../key-vault/general/soft-delete-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md)

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

* [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

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

*如需詳細資訊，請參閱 [安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針： 

* [遵循 Microsoft 的參與規則，以確保您的滲透測試不違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [您可以在這裡找到有關 Microsoft 所管理的雲端基礎結構、服務和應用程式的 Microsoft 策略和執行紅色小組和即時網站滲透測試的詳細資訊](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)