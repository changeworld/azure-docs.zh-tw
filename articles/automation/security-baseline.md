---
title: 適用于 Azure 自動化的 Azure 安全性基準
description: 自動化的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 2d3f04777983d24d5219c55c5309b7d12c214594
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070685"
---
# <a name="azure-security-baseline-for-automation"></a>自動化的 Azure 安全性基準

適用于自動化的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導**方針： Azure 自動化帳戶尚不支援透過私人端點限制對服務的存取 Azure Private Link。 針對 Azure 中的資源進行驗證和執行的 runbook 會在 Azure 沙箱上執行，並運用 Microsoft 負責彼此隔離的共用後端資源;其網路功能不受限制，而且可以存取公用資源。 除了支援混合式 Runbook 背景工作角色之外，Azure 自動化目前沒有私人網路的虛擬網路整合。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

若要進一步隔離您的 runbook，您可以使用在 Azure 虛擬機器上執行的混合式 Runbook 背景工作角色。 當您建立 Azure 虛擬機器時，您必須建立虛擬網路 (VNet) 或使用現有的 VNet，並使用子網來設定您的 Vm。 確定所有已部署的子網都已套用網路安全性群組，且該網路存取控制適用于您應用程式的受信任埠和來源。 針對服務特定的需求，請參閱該特定服務的安全性建議。

或者，如果您有特定需求，也可以使用 Azure 防火牆來滿足此需求。

* [Azure 中的虛擬網路和虛擬機器](../virtual-machines/network-overview.md)

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [Runbook 執行環境](./automation-runbook-execution.md#runbook-execution-environment)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導**： Azure 自動化目前除了支援混合式 Runbook 背景工作角色之外，沒有私人網路的虛擬網路整合。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

如果您使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，請確定已使用網路安全性群組來啟用包含這些背景工作角色的子網 (NSG) 並設定流量記錄，以將記錄轉送至儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄轉送至 Log Analytics 工作區，並使用流量分析來提供 Azure 雲端中流量的深入解析。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

雖然 NSG 規則和使用者定義的路由不會套用至私人端點，但仍支援輸出連線的 NSG 流量記錄和監視資訊，而且可以使用。

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址通訊

**指導**方針： Azure 自動化在混合式 Runbook 背景工作角色的支援之外，目前沒有私人網路的虛擬網路整合。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

如果您使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，請在裝載混合式 Runbook 背景工作角色的虛擬網路上啟用分散式阻絕服務 (DDoS) 標準保護，以防範 DDoS 攻擊。 藉由使用 Azure 資訊安全中心整合式威脅情報，您可以拒絕與已知惡意 IP 位址的通訊。 在每個虛擬網路區段上設定 Azure 防火牆，並啟用威脅情報，並設定 **警示和拒絕** 惡意網路流量。

您可以使用 Azure 資訊安全中心的即時網路存取，將 Windows 虛擬機器的公開限制在一段有限的時間內，以獲得核准的 IP 位址。 此外，請使用 Azure 資訊安全中心 NSG 設定的彈性網路強化建議，根據實際流量和威脅情報來限制埠和來源 Ip。

* [如何設定 DDoS 保護](../virtual-network/manage-ddos-protection.md)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/azure-defender.md)

* [瞭解 Azure 資訊安全中心適應性網路強化](../security-center/security-center-adaptive-network-hardening.md)

* [瞭解 Azure 資訊安全中心及時的網路存取控制](../security-center/security-center-just-in-time.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針： Azure 自動化目前除了支援混合式 Runbook 背景工作角色之外，還沒有私人網路的虛擬網路整合，如果您使用現成可用的服務，而沒有混合式背景工作角色，則不適用此控制項。

如果您使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，則可以將 NSG 流量記錄記錄到儲存體帳戶，以產生作為 Runbook 背景工作角色的 Azure 虛擬機器的流量記錄。 調查異常活動時，您可以啟用網路監看員封包捕獲，讓網路流量可以針對不尋常和非預期的活動進行審核。

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導**方針： Azure 自動化在混合式 Runbook 背景工作角色的支援之外，目前沒有私人網路的虛擬網路整合。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

如果您使用裝載在 Azure 虛擬機器上的混合式 Runbook 背景工作角色，您可以將網路監看員所提供的封包捕捉與開放原始碼識別碼工具結合，以針對這些背景工作電腦的各種威脅執行網路入侵偵測。 此外，您可以在適當的情況下，將 Azure 防火牆部署至虛擬網路區段，並啟用威脅情報並設定為惡意網路流量的「警示和拒絕」。

* [使用網路監看員和開放原始碼工具執行網路入侵偵測](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：使用虛擬網路服務標籤來定義在 azure 中設定的網路安全性群組或 azure 防火牆上的網路存取控制，這需要存取您的自動化資源。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由指定服務標籤名稱 (例如，GuestAndHybridManagement) 在規則的適當來源或目的地欄位中，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

* [瞭解和使用服務標記](../virtual-network/service-tags-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：針對 Azure 自動化與 Azure 原則所使用的網路資源，定義並執行標準安全性設定。

您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝重要環境成品（例如 Azure 資源管理員範本、Azure RBAC 控制項和原則）來簡化大規模的 Azure 部署。 您可以將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [適用于網路的 Azure 原則範例](../governance/policy/samples/built-in-policies.md#network)

* [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：使用 Nsg 的標記，以及與網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄來監視資源設定，並偵測對您網路資源的變更。 在 Azure 監視器中建立警示，在發生重大資源變更時將會觸發此警示。

* [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針： Microsoft 會維護 Azure 資源的時間來源。 不過，您可以選擇管理在 Windows 虛擬機器上執行之任何混合式 Runbook 背景工作角色的時間同步處理設定。

* [如何設定 Azure 計算資源的時間同步處理](../virtual-machines/windows/time-sync.md)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：將記錄資料轉送至 Azure 監視器記錄，以匯總 Azure 自動化資源所產生的安全性資料。 在 Azure 監視器中，使用記錄查詢來搜尋和執行分析，並將 Azure 儲存體帳戶用於長期/封存儲存體。 Azure 自動化可以將 runbook 工作狀態、工作串流、Automation 狀態設定資料、更新管理，以及變更追蹤或清查記錄傳送至 Log Analytics 工作區。 這項資訊會顯示在 Azure 入口網站、Azure PowerShell 和 Azure 監視器記錄 API 中，讓您能夠執行簡單的調查。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

* [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [將 Azure 自動化作業資料轉送至 Azure 監視器記錄](./automation-manage-send-joblogs-log-analytics.md)

* [整合 DSC 與 Azure 監視器記錄](./automation-dsc-diagnostics.md)

* [已連結 Log Analytics 工作區的支援區域](./how-to/region-mappings.md)

* [查詢更新管理記錄](./update-management/update-mgmt-query-logs.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：啟用 Azure 監視器以存取您的 audit and activity 記錄，其中包括事件來源、日期、使用者、時間戳記、來源位址、目的地位址和其他有用的元素。

* [如何使用 Azure 監視器收集平臺記錄和計量](../azure-monitor/platform/diagnostic-settings.md)

* [查看和取出 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：使用 Azure 自動化搭配多租使用者 runbook 背景工作角色時，此控制項不適用，且平臺會處理基礎虛擬機器。

使用混合式 Runbook 背景工作角色功能時，Azure 資訊安全中心提供 Windows 虛擬機器的安全性事件記錄檔監視。 如果您的組織想要保留安全性事件記錄檔資料，它可以儲存在資料收集層內，此時可以在 Log Analytics 中進行查詢。 有不同的層級： [基本]、[通用] 和 [全部]，在下列連結中有詳細說明。

* [設定 Azure 資訊安全中心內的資料收集層](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：在 Azure 監視器中，您可以根據組織的合規性法規來設定 Log Analytics 工作區保留期限。 使用 Azure 儲存體帳戶進行長期/封存儲存。

* [變更 Log Analytics 中的資料保留期限](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [自動化帳戶的資料保留詳細資料](./automation-managing-data.md#data-retention)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導**方針：分析和監視記錄中的異常行為，並定期查看結果。 使用 Azure 監視器記錄查詢來檢查記錄，並對記錄資料執行查詢。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [瞭解 Azure 監視器中的記錄查詢](../azure-monitor/log-query/get-started-portal.md)

* [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：使用 Azure 資訊安全中心搭配 Azure 監視器進行監視，並針對在安全性記錄檔和事件中找到的異常活動發出警示。

或者，您也可以啟用和麵板上的資料以 Azure Sentinel。

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

* [如何對 Azure 監視器記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：搭配多租使用者 runbook 背景工作角色使用 Azure 自動化時，此控制項不適用，且平臺會處理基礎虛擬機器。

不過，使用混合式 Runbook 背景工作角色功能時，您可以將 Microsoft 反惡意程式碼用於 Azure 雲端服務和虛擬機器。 設定您的虛擬機器以將事件記錄到 Azure 儲存體帳戶。 設定 Log Analytics 工作區，以內嵌來自儲存體帳戶的事件，並在適當時建立警示。 遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」。

* [如何設定雲端服務和虛擬機器的 Microsoft 反惡意程式碼](../security/fundamentals/antimalware.md)

* [如何啟用虛擬機器的來賓層級監視](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：根據您的組織需求，針對 DNS 記錄解決方案的 Azure Marketplace 來執行協力廠商解決方案。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針：使用 Azure 自動化搭配多租使用者 runbook 背景工作角色時，此控制項不適用，且平臺會處理基礎虛擬機器。

不過，使用混合式 Runbook 背景工作角色功能時，Azure 資訊安全中心為 Azure 虛擬機器提供安全性事件記錄檔監視。 資訊安全中心會在所有支援的 Azure Vm 上布建 Log Analytics 代理程式，以及在啟用自動布建時所建立的任何新虛擬機器。 或者，您可以手動安裝代理程式。 代理程式會啟用進程建立事件4688和事件4688內的命令列欄位。 事件記錄檔會記錄在 VM 上建立的新進程，並由資訊安全中心的偵測服務進行監視。

* [Azure 資訊安全中心的資料收集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導**方針：使用 Azure Active Directory 可以明確指派且可進行查詢的內建系統管理員角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。 在您的 runbook 中使用自動化帳戶執行帳戶時，請確定這些服務主體也會在您的清查中追蹤，因為它們通常會有較高的許可權。 刪除任何未使用的執行身份帳戶，以將公開的受攻擊面降至最低。

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [刪除執行身分或傳統執行身分帳戶](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自動化執行身分帳戶](./manage-runas-account.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Azure 自動化帳戶沒有預設密碼的概念。 客戶須負責使用在服務上或其混合式 Runbook 背景工作角色上執行的預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。 在您的 runbook 中使用自動化帳戶執行帳戶時，請確定這些服務主體也會在您的清查中追蹤，因為它們通常會有較高的許可權。 將這些身分識別的範圍設為您的 runbook 成功執行自動化程式所需的最低許可權許可權。 刪除任何未使用的執行身份帳戶，以將公開的受攻擊面降至最低。

您也可以使用適用于 Microsoft 服務 Azure AD Privileged Identity Management 特殊許可權角色，並 Azure Resource Manager，來啟用及時且足夠的存取權。

* [深入瞭解 Privileged Identity Management](../active-directory/privileged-identity-management/index.yml)

* [刪除執行身分或傳統執行身分帳戶](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自動化執行身分帳戶](./manage-runas-account.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針：可能的話，請使用 SSO 搭配 Azure Active Directory，而不是針對個別服務設定個別的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

* [Azure Active Directory 中的應用程式單一登入](../active-directory/manage-apps/what-is-single-sign-on.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

* [使用 Azure AD 向 Azure 進行驗證](./automation-use-azure-ad.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指導**方針： (MFA) 啟用 Azure AD 多重要素驗證，並遵循 Azure 資訊安全中心身分識別和存取管理建議。

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3.6：針對所有系統管理工作使用專用的電腦

**指導**方針：使用 paw 搭配多重要素驗證設定，以在生產環境中登入和設定 Azure 自動化帳戶資源。

* [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：利用 Azure AD 風險偵測來查看警示和報告有風險的使用者行為。 客戶也可以選擇使用動作群組將 Azure 資訊安全中心風險偵測警示轉送到 Azure 監視器並設定自訂警示/通知。

* [瞭解 Azure 資訊安全中心風險偵測 (可疑活動) ](../active-directory/identity-protection/overview-identity-protection.md)

* [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何設定自訂警示和通知的動作群組](../azure-monitor/platform/action-groups.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：建議使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組進行存取。

* [如何在 Azure 中設定命名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure AD 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。 如果使用混合式 Runbook 背景工作角色，您可以利用受控識別（而非執行身分帳戶）來啟用更順暢的安全許可權。

* [如何建立和設定 Azure AD 實例](../active-directory-domain-services/tutorial-create-instance.md)

* [使用具有受控識別的 runbook 驗證](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，使用 Azure 身分識別存取權檢閱，有效地管理群組成員資格、對企業應用程式的存取，以及角色指派。 您可以定期檢查使用者存取權，以確定只有適當的使用者可以繼續存取。 只要針對您的 runbook 使用自動化帳戶執行帳戶，確保這些服務主體也會在您的清查中追蹤，因為它們通常會有較高的許可權。 刪除任何未使用的執行身份帳戶，以將公開的受攻擊面降至最低。

* [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

* [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

* [刪除執行身分或傳統執行身分帳戶](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自動化執行身分帳戶](./manage-runas-account.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：您可以存取 Azure AD 登入活動、稽核和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。

若要簡化此流程，您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 您可以在 Log Analytics 工作區中設定所需的警示。

* [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 Azure AD 風險和身分識別保護功能來設定自動回應，以偵測與網路資源的使用者身分識別相關的可疑動作。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

* [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

* [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：針對 Azure 自動化帳戶，Microsoft 支援人員可以在開啟支援案例期間存取平臺資源中繼資料，而不需要使用其他工具。

不過，使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，以及協力廠商需要存取客戶資料 (例如在支援要求) 期間，請使用 Azure 虛擬機器客戶加密箱 (預覽) 來檢查和核准或拒絕客戶資料存取要求。

* [瞭解客戶加密箱](../security/fundamentals/customer-lockbox-overview.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指導**方針：使用標記來協助追蹤儲存或處理機密資訊 Azure 自動化資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 使用個別的自動化帳戶資源來隔離環境。 混合式 Runbook 背景工作角色之類的資源應該以虛擬網路/子網分隔、適當地標記，並在網路安全性群組內受到保護， (NSG) 或 Azure 防火牆。 針對儲存或處理敏感性資料的虛擬機器，請在不使用時將原則和程式 (的) 將其關閉。

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

* [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

* [如何使用 Azure 防火牆設定警示或警示和拒絕](../firewall/threat-intel.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：使用混合式 Runbook 背景工作角色功能時，請利用網路周邊 Azure Marketplace 上的協力廠商解決方案，以監視是否有未經授權的機密資訊傳輸並封鎖這類傳輸，同時警示資訊安全專業人員。

針對 Microsoft 所管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並防止客戶資料遺失和公開。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：加密傳輸中的所有機密資訊。 確定任何連線至 Azure 虛擬網路中 Azure 資源的用戶端都能夠協商 TLS 1.2 或更高版本。 Azure 自動化透過 webhook、DSC 節點、混合式 runbook 背景工作角色) ，完全支援並強制執行傳輸層 (TLS) 1.2 和所有外部 HTPS (端點的所有用戶端呼叫或更新版本。

遵循 Azure 資訊安全中心待用加密及傳輸中加密的建議（適用時）。

* [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure 自動化 TLS 1.2 強制](https://azure.microsoft.com/updates/azure-automation-tls12-enforcement/)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針：使用協力廠商主動式探索工具來識別組織技術系統儲存、處理或傳輸的所有機密資訊，包括位於現場或遠端服務提供者的機密資訊，以及更新組織的機密資訊清查。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制使用內建角色定義存取 Azure 自動化資源、指派存取權給存取您自動化資源的使用者，並遵循最低許可權或「足夠的」存取模型。 使用混合式 Runbook 背景工作角色時，請利用這些虛擬機器的受控識別，以避免使用服務主體，同時使用多租使用者或混合式 Runbook 背景工作角色時，請務必在 Runbook 背景工作角色的身分識別上套用適當範圍的 Azure RBAC 許可權。

* [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

* [混合式 Runbook 背景工作角色的 Runbook 權限](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [管理角色權限與安全性](./automation-role-based-access-control.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

如果您使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，則您需要使用協力廠商的主機型資料遺失防護解決方案，對託管的混合式 Runbook 背景工作角色虛擬機器強制執行存取控制。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：搭配 Azure 自動化使用客戶管理的金鑰。 Azure 自動化支援使用客戶管理的金鑰來加密所有使用的安全資產，例如：認證、憑證、連線和加密變數。 將加密的變數與您的 runbook 搭配使用，以避免發生非預期的風險。

使用混合式 Runbook 背景工作角色時，虛擬機器上的虛擬磁片會使用伺服器端加密或 Azure 磁片加密 (ADE) 進行靜態加密。 Azure 磁片加密利用 Windows 的 BitLocker 功能，利用來賓 VM 內客戶管理的金鑰來加密受控磁片。 使用客戶管理的金鑰進行伺服器端加密，可讓您藉由加密儲存庫服務中的資料，對您的 VM 使用任何作業系統類型和映像，而改善 ADE 的效能。

* [Azure 受控磁片的伺服器端加密](../virtual-machines/windows/disk-encryption.md)

* [適用于 Windows Vm 的 Azure 磁碟加密](../virtual-machines/windows/disk-encryption-overview.md)

* [針對自動化帳戶使用客戶管理的金鑰](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure 自動化中的 Managed 變數](./shared-resources/variables.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以建立在 azure 資源（例如網路元件、Azure 自動化帳戶和 runbook）發生變更時的警示。

* [適用於網路安全性群組的診斷記錄](../private-link/private-link-overview.md#logging-and-monitoring)

* [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：遵循 Azure 資訊安全中心在您的 Azure 資源上執行弱點評定的建議

* [Azure 資訊安全中心的安全性建議](../security-center/security-center-recommendations.md)

* [安全性中心建議參考](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

如果您使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，請使用 Azure 更新管理來管理虛擬機器的更新和修補程式。 更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 System Center Updates Publisher (Updates Publisher) 之類的工具可讓您將自訂更新發佈至 Windows Server Update Services (WSUS) 。 此案例可讓更新管理修補使用 Configuration Manager 作為其更新存放庫（具有協力廠商軟體）的機器。

* [Azure 中的更新管理](./update-management/update-mgmt-overview.md)

* [管理 Vm 的更新和修補程式](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

如果您使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，您可以使用 Azure 更新管理來管理虛擬機器的更新和修補程式。 更新管理依賴本機設定的更新存放庫來修補支援的 Windows 系統。 System Center Updates Publisher (Updates Publisher) 之類的工具可讓您將自訂更新發佈至 Windows Server Update Services (WSUS) 。 此案例可讓更新管理修補使用 Configuration Manager 作為其更新存放庫（具有協力廠商軟體）的機器。

* [Azure 中的更新管理解決方案](./update-management/update-mgmt-overview.md)

* [管理 Azure VM 的更新和修補程式](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**方針：依一致的間隔匯出掃描結果並比較結果，以確認已補救弱點。 使用 Azure 資訊安全中心建議的弱點管理建議時，客戶可能會進入所選解決方案的入口網站以查看歷史掃描資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：使用預設的風險評等 (Azure 資訊安全中心提供的安全分數) ，以協助優先補救探索到的弱點。

* [瞭解 Azure 資訊安全中心安全分數](../security-center/secure-score-security-controls.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢及探索訂用帳戶中的所有 Azure 自動化資源。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

* [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

* [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導**方針：使用標記、管理群組和個別的訂用帳戶（如果適當）來組織和追蹤 Azure 自動化資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。 刪除任何未使用的執行身份帳戶，以將公開的受攻擊面降至最低。

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [刪除執行身分或傳統執行身分帳戶](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [管理 Azure 自動化執行身分帳戶](./manage-runas-account.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：您將需要根據組織的需求，建立已核准的 Azure 資源和已核准的計算資源軟體的清查。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：
- 不允許的資源類型
- 允許的資源類型

此外，您也可以使用 Azure Resource Graph 來查詢/探索訂用帳戶內的資源。 這有助於以高安全性為基礎的環境，例如具有儲存體帳戶的環境。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

* [Azure 自動化的 Azure 原則內建範例](./policy-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針： Azure 自動化供應專案目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器，而這是由平臺處理。 如果您使用不含混合式背景工作角色的現成服務，則不適用此控制項。 不過，您可以安裝、移除和管理 PowerShell，或 runbook 可透過入口網站或 Cmdlet 存取的 Python 模組。 應移除或更新 runbook 的未核准或舊模組。

如果您使用 Azure 虛擬機器所支援的混合式 Runbook 背景工作角色，Azure 自動化可在部署、作業和解除委任工作負載和資源時，提供完整的控制權。 利用 Azure 虛擬機器清查來自動收集虛擬機器上所有軟體的相關資訊。 您可以從 Azure 入口網站取得軟體名稱、版本、發行者和重新整理時間。 若要取得安裝日期和其他資訊的存取權，客戶必須啟用來賓層級的診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

* [Azure 自動化簡介](./automation-intro.md)

* [如何啟用 Azure VM 清查](./automation-tutorial-installed-software.md)

* [在 Azure 自動化中管理模組](./shared-resources/modules.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針：客戶可以根據客戶的公司指導方針的要求，防止資源建立或使用 Azure 原則。 您可以執行自己的流程，以移除未經授權的資源。 在 Azure 自動化供應專案中，您可以安裝、移除和管理 PowerShell，或 runbook 可透過入口網站或 Cmdlet 存取的 Python 模組。 應移除或更新 runbook 的未核准或舊模組。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [在 Azure 自動化中管理模組](./shared-resources/modules.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：使用混合式 Runbook 背景工作角色功能時，您可以使用 Azure 資訊安全中心的自我調整應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

* [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導**方針：使用 Azure 原則對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導**方針：使用混合式 Runbook 背景工作角色功能時，您可以搭配混合式背景工作角色虛擬機器使用 Azure 資訊安全中心的彈性應用程式控制功能。

自我調整應用程式控制是 Azure 資訊安全中心的智慧型、自動化、端對端解決方案，可協助您控制哪些應用程式可以在 Azure 和非 Azure 電腦上執行 (Windows 和 Linux) 。 如果這不符合您組織的需求，請執行協力廠商解決方案。

* [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導**方針：使用 Azure 條件式存取原則，藉由設定「封鎖存取」，從不安全或未核准 Microsoft Azure 的位置或裝置設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導**方針：使用混合式 Runbook 背景工作角色功能時，您可以使用作業系統專屬設定或協力廠商資源，以限制使用者在 Azure 計算資源內執行腳本的能力。 您也可以運用 Azure 資訊安全中心的自我調整應用程式控制，以確保只有授權的軟體會執行，而且所有未經授權的軟體都會在 Azure 虛擬機器上遭到封鎖而無法執行。

* [如何在 Windows 環境中控制 PowerShell 腳本執行](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [如何使用 Azure 資訊安全中心適應性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導**方針：在 azure 環境中部署的高風險應用程式可能會使用不同的網路和資源容器（例如虛擬網路、子網、訂用帳戶、管理群組）來隔離，而這些應用程式可以使用 Azure 防火牆、Web 應用程式防火牆 (WAF) 或網路安全性群組 (NSG) 來充分保護這些應用程式。

* [Azure 中的虛擬網路和虛擬機器](../virtual-machines/network-overview.md)

* [Azure 防火牆總覽](../firewall/overview.md)

* [Azure Web 應用程式防火牆總覽](../web-application-firewall/overview.md)

* [網路安全性群組](../virtual-network/network-security-groups-overview.md)

* [Azure 虛擬網路總覽](../virtual-network/virtual-networks-overview.md)

* [使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md)

* [訂用帳戶決策指南](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則別名來建立自訂原則，以對 Azure 自動化和相關資源的設定進行審核或強制執行。 您也可以使用內建的 Azure 原則定義。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，您應該檢查這些設定，以確保設定符合/超過組織的安全性需求。

您也可以使用 Azure 資訊安全中心中的建議作為 Azure 資源的安全設定基準。

* [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [教學課程：建立和管理原則來強制執行相容性](../governance/policy/tutorials/create-and-manage.md)

* [Azure 自動化的 Azure 原則內建範例](./policy-reference.md)

* [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

* [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器或 OS。 這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

使用混合式 Runbook 背景工作角色功能時，請使用 Azure 資訊安全中心建議 [補救您虛擬機器的安全性設定中的弱點]，以維護虛擬機器上的安全性設定。

* [如何監視 Azure 資訊安全中心建議](../security-center/security-center-recommendations.md)

* [如何修復 Azure 資訊安全中心建議](../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure Resource Manager 範本和 Azure 原則，安全地設定與 Azure 自動化相關聯的 Azure 資源。 Azure Resource Manager 範本是用來部署 Azure 資源的 JSON 型檔案，而且任何自訂範本都必須在程式碼存放庫中安全地儲存和維護。 使用原始檔控制整合功能，讓您的 runbook 在您的自動化帳戶中，與原始檔控制存放庫中的腳本保持最新狀態。 使用 Azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。

* [使用原始檔控制整合](./source-control-integration.md)

* [建立 Azure Resource Manager 範本的資訊](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [了解 Azure 原則效果](../governance/policy/concepts/effects.md)

* [使用 Azure Resource Manager 範本部署自動化帳戶](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure 自動化的 Azure 原則內建範例](./policy-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器或 OS，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

使用混合式 Runbook 背景工作角色功能時，有數個選項可維護 Azure 虛擬機器的安全設定以進行部署：

- Azure Resource Manager 範本：這些是以 JSON 為基礎的檔案，用來從 Azure 入口網站部署 VM，而且必須維護自訂範本。 Microsoft 會在基底範本上執行維護。
- 自訂虛擬硬碟 (VHD) ：在某些情況下，可能需要使用自訂 VHD 檔案，例如在處理無法透過其他方式管理的複雜環境時。
- Azure 自動化狀態設定：部署基底作業系統之後，這可用於更細微地控制設定，並透過自動化架構強制執行。

在大部分的情況下，與 Azure 自動化狀態設定結合的 Microsoft 基底 VM 範本，可協助滿足和維護安全性需求。

* [如何下載 VM 範本的資訊](../virtual-machines/windows/download-template.md)

* [建立 ARM 範本的資訊](../virtual-machines/windows/ps-template.md)

* [如何將自訂 VM VHD 上傳至 Azure](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則、Azure Resource Manager 範本和 Desired State Configuration 腳本。 若要存取您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory 中定義之群組的許可權（如果與 Azure DevOps 整合），或在與 TFS 整合時 Active Directory。 使用原始檔控制整合功能，讓您的 runbook 在您的自動化帳戶中，與原始檔控制存放庫中的腳本保持最新狀態。

* [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

* [使用原始檔控制整合](./source-control-integration.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器或 OS，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

使用混合式 Runbook 背景工作角色功能時，請確定您已正確地限制存取位於您儲存體帳戶中的自訂 OS 映射，因此只有授權的使用者可以存取該映射。

* [了解 Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [如何設定 Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：使用 Azure 原則定義和實行適用于 Azure 資源的標準安全性設定。 使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 您也可以使用與特定資源相關的內建原則定義。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用別名](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure 自動化的 Azure 原則內建範例](./policy-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器或 OS，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

使用混合式 Runbook 背景工作角色功能時，請在 Runbook 背景工作角色上使用 Azure 自動化狀態設定，這是在任何雲端或內部部署資料中心內 Desired State Configuration (DSC) 節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](./automation-dsc-onboarding.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用 Azure 原則警示和審核 Azure 資源設定，原則可用來偵測未以私人端點設定的特定資源。

使用混合式 Runbook 背景工作角色功能時，請利用 Azure 資訊安全中心來執行 Azure 虛擬機器的基準掃描。 自動化設定的其他方法包含 Azure 自動化狀態設定。

* [如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

* [開始使用 Azure Automation State Configuration](./automation-dsc-getting-started.md)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [Azure 自動化的 Azure 原則內建範例](./policy-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導**方針： Azure 自動化供應專案目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器或 OS，而這是由平臺處理。 如果您使用不含混合式背景工作角色的現成服務，則不適用此控制項。

使用混合式 Runbook 背景工作角色功能時，請使用 Azure 自動化狀態設定作為 Runbook 背景工作角色，這是在任何雲端或內部部署資料中心內 Desired State Configuration (DSC) 節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](./automation-dsc-onboarding.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：使用受控服務識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。

* [針對自動化帳戶使用客戶管理的金鑰](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [使用具有受控識別的 runbook 驗證](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [如何建立 Key Vault](../key-vault/secrets/quick-create-portal.md)

* [如何驗證 Key Vault](../key-vault/general/authentication.md)

* [如何指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：使用受控識別，在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

* [如何設定受控識別](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [針對自動化帳戶使用客戶管理的金鑰](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針： Azure 自動化供應專案目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器或 OS，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

使用混合式 Runbook 背景工作角色功能時，請使用適用于 Azure Windows 虛擬機器的 Microsoft 反惡意程式碼，持續監視和防護您的 Runbook Worker 資源。

* [如何設定雲端服務和虛擬機器的 Microsoft 反惡意程式碼](../security/fundamentals/antimalware.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：不適用;Azure 自動化即服務不會儲存檔案。 Microsoft Antimalware 在支援 Azure 服務的基礎主機上啟用 (例如 Azure 自動化) ，不過它不會在您的內容上執行。

* [瞭解 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導**方針： Azure 自動化目前不會公開基礎多租使用者 runbook 背景工作角色的虛擬機器或 OS，而這是由平臺處理。 如果您在沒有混合式 Runbook 背景工作角色的情況下使用現成可用的服務，則不適用此控制項。

使用混合式 Runbook 背景工作角色功能時，請使用適用于 Azure 的 Microsoft Antimalware，依預設會自動將最新的簽章、平臺和引擎更新安裝到您的 Runbook Worker。 遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」，以確保所有端點都是最新的簽章。 Windows OS 可利用額外的安全性進一步保護，以利用與 Azure 資訊安全中心整合的 Microsoft Defender 進階威脅防護服務，來限制病毒或惡意軟體攻擊的風險。

* [如何部署 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](../security/fundamentals/antimalware.md)

* [Microsoft Defender 進階威脅防護](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針：使用 Azure Resource Manager 部署 Azure 自動化帳戶和相關資源。 Azure Resource Manager 提供匯出範本的功能，可作為備份來還原 Azure 自動化帳戶和相關資源。 使用 Azure 自動化定期呼叫 Azure Resource Manager 範本匯出 API。

使用原始檔控制整合功能，讓您的 runbook 在您的自動化帳戶中，與原始檔控制存放庫中的腳本保持最新狀態。

* [Azure Resource Manager 的概觀](../azure-resource-manager/management/overview.md)

* [Azure 自動化資源的 Azure Resource Manager 範本參考](/azure/templates/microsoft.automation/allversions)

* [使用 Azure Resource Manager 範本建立自動化帳戶](./quickstart-create-automation-account-template.md)

* [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

* [資源群組-匯出範本](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure 自動化簡介](./automation-intro.md)

* [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [針對自動化帳戶使用客戶管理的金鑰](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [使用原始檔控制整合](./source-control-integration.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導**方針：使用 Azure Resource Manager 部署 Azure 自動化帳戶和相關資源。 Azure Resource Manager 提供匯出範本的功能，可作為備份來還原 Azure 自動化帳戶和相關資源。 使用 Azure 自動化定期呼叫 Azure Resource Manager 範本匯出 API。 Azure Key Vault 內備份客戶管理的金鑰。 您可以使用 Azure 入口網站或 PowerShell，將您的 runbook 匯出到腳本檔案。

* [Azure Resource Manager 的概觀](../azure-resource-manager/management/overview.md)

* [Azure 自動化資源的 Azure Resource Manager 範本參考](/azure/templates/microsoft.automation/allversions)

* [使用 Azure Resource Manager 範本建立自動化帳戶](./quickstart-create-automation-account-template.md)

* [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

* [資源群組-匯出範本](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure 自動化簡介](./automation-intro.md)

* [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [針對自動化帳戶使用客戶管理的金鑰](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [自動化帳戶的 Azure 資料備份](./automation-managing-data.md#data-backup)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：如有需要，請確定定期定期執行 Azure Resource Manager 範本部署至隔離式訂用帳戶的能力。 測試已備份之客戶管理金鑰的還原。

* [使用 ARM 範本和 Azure 入口網站部署資源](../azure-resource-manager/templates/deploy-portal.md)

* [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [針對自動化帳戶使用客戶管理的金鑰](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導**方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如 Azure Resource Manager 範本。 若要保護您在 Azure DevOps 中管理的資源，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory 中定義之群組的許可權（如果與 Azure DevOps 整合），或在與 TFS 整合時 Active Directory。

使用原始檔控制整合功能，讓您的 runbook 在您的自動化帳戶中，與原始檔控制存放庫中的腳本保持最新狀態。

* [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [關於 Azure DevOps 中的許可權和群組](/azure/devops/organizations/security/about-permissions)

* [使用原始檔控制整合](./source-control-integration.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。 您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

* [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

* [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導**方針：進行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

* [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

* [如何設定連續匯出](../security-center/continuous-export.md)

* [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。

* [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

* [滲透測試的參與規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)