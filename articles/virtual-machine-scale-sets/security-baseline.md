---
title: 適用于虛擬機器擴展集的 Azure 安全性基準
description: 虛擬機器擴展集的安全性基準提供程式指引和資源，可用於執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 6db83d17a9e6f3df6d691d6de616ead5407e3f5f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133039"
---
# <a name="azure-security-baseline-for-virtual-machine-scale-sets"></a>適用于虛擬機器擴展集的 Azure 安全性基準

適用于虛擬機器擴展集的 Azure 安全性基準包含可協助您改善部署之安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路中的 Azure 資源

**指引**：當您建立 Azure 虛擬機器（VM）時，您必須建立虛擬網路，或使用現有的虛擬網路，並使用子網設定 VM。 請確定所有已部署的子網都已套用網路安全性群組，且該群組適用于應用程式信任的埠和來源的特定網路存取控制。

或者，如果您有集中式防火牆的特定使用案例，也可以使用 Azure 防火牆來滿足這些需求。

* [Azure 虛擬機器擴展集的網路](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [如何部署和設定 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指引**：使用 Azure 資訊安全中心來識別並遵循網路保護建議，以協助保護 azure 中的 Azure 虛擬機器（VM）資源。 針對不尋常的活動，啟用 NSG 流量記錄，並將記錄傳送到儲存體帳戶，以進行 Vm 的流量審核。

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：如果使用您的虛擬機器擴展集（VMSS）來裝載 web 應用程式，請使用 VMSS 子網上的網路安全性群組（NSG）來限制允許的網路流量、埠和通訊協定。 設定 Nsg 時，請遵循最低許可權的網路方法，只允許您的應用程式所需的流量。

您也可以在重要 Web 應用程式前方部署 Azure Web 應用程式防火牆（WAF），以進行連入流量的額外檢查。 啟用診斷設定以進行 WAF，並將記錄內嵌至儲存體帳戶、事件中樞或 Log Analytics 工作區。

* [Azure 虛擬機器擴展集的網路](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking)

* [使用 Azure 入口網站建立包含 Web 應用程式防火牆的應用程式閘道](https://docs.microsoft.com/azure/web-application-firewall/ag/application-gateway-web-application-firewall-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：在虛擬網路上啟用分散式阻斷服務（DDoS）標準保護，以防範 DDoS 攻擊。 使用 Azure 資訊安全中心整合式威脅情報，您可以監視與已知惡意 IP 位址的通訊。 在每個虛擬網路區段上設定 Azure 防火牆，並啟用威脅情報並將其設定為「警示和拒絕」，以取得惡意的網路流量。

您可以使用 Azure 資訊安全中心的即時網路存取，在有限期間內限制暴露于核准的 IP 位址 Windows 虛擬機器。 此外，您也可以使用 Azure 資訊安全中心的彈性網路強化，根據實際的流量和威脅情報來建議限制埠和來源 Ip 的 NSG 設定。

* [如何設定 DDoS 保護](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [如何部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [了解 Azure 資訊安全中心的整合式威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

* [瞭解 Azure 資訊安全中心的彈性網路強化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

* [瞭解 Azure 資訊安全中心及時網路存取控制](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指引**：您可以將 NSG 流量記錄錄製到儲存體帳戶，以產生 Azure 虛擬機器的流量記錄。 調查異常活動時，您可以啟用網路監看員封包捕獲，讓網路流量可以針對不尋常和非預期的活動進行審核。

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何啟用網路監看員](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統（IDS/IPS）

**指導**方針：結合網路監看員所提供的封包捕捉和開放原始碼的識別碼工具，您可以針對各式各樣的威脅執行網路入侵偵測。 此外，您可以適當地在虛擬網路區段上部署 Azure 防火牆，並啟用威脅情報，並將惡意網路流量設定為「警示和拒絕」。

* [使用網路監看員和開放原始碼工具執行網路入侵偵測](https://docs.microsoft.com/azure/network-watcher/network-watcher-intrusion-detection-open-source-tools)

* [如何部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [如何使用 Azure 防火牆設定警示](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導**方針：如果使用虛擬機器擴展集（VMSS）來裝載 web 應用程式，您可以針對已針對受信任憑證啟用 HTTPS/SSL 的 web 應用程式部署 Azure 應用程式閘道。 透過 Azure 應用程式閘道，您可以將接聽程式指派給埠、建立規則，以及將資源新增至後端集區（例如 VMSS 等），藉此將應用程式 web 流量導向至特定資源。

* [如何部署應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [如何設定應用程式閘道使用 HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [建立參考應用程式閘道的擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#create-a-scale-set-that-references-an-application-gateway)

* [瞭解 Azure web 應用程式閘道的第7層負載平衡](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指引**：使用虛擬網路服務標籤來定義網路安全性群組或為 azure 虛擬機器設定的 azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

* [瞭解和使用服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指引**：使用 Azure 原則定義和執行 Azure 虛擬機器擴展集的標準安全性設定。 您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝金鑰環境成品（例如 Azure Resource Manager 範本、角色指派和 Azure 原則指派）來簡化大規模的 Azure VM 部署。 您可以將藍圖套用至訂用帳戶，並透過藍圖版本設定來啟用資源管理。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解虛擬機器擴展集範本](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-start)

* [網路的 Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

* [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：您可以使用網路安全性群組（NSG）的標籤，以及針對 Windows 虛擬機器設定的網路安全性和流量流程的其他相關資源。 針對個別的 NSG 規則，請使用 [描述] 欄位來指定允許進出網路流量的任何規則的商務需求和/或持續時間。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視與您的 Azure 虛擬機器擴展集相關的網路資源設定變更。 在 Azure 監視器中建立警示，以在重大網路設定或資源的變更發生時觸發。

使用 Azure 原則來驗證（及/或補救）與虛擬機器擴展集相關的網路資源設定。

* [如何檢視及擷取 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure 監視器中建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [網路的 Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/#network)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**： Microsoft 會維護 Azure 資源的時間來源，不過，您可以選擇管理虛擬機器的時間同步處理設定。

* [如何設定 Azure Windows 計算資源的時間同步處理](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

* [如何設定 Azure Linux 計算資源的時間同步處理](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：活動記錄可用來審核虛擬機器擴展集資源上執行的作業和動作。 活動記錄包含資源的所有寫入作業（PUT、POST、DELETE），但讀取作業（GET）除外。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

您可以啟用 Azure 活動記錄檔或虛擬機器資源所產生的和麵板記錄資料，以 Azure Sentinel 或中央安全性記錄管理的協力廠商 SIEM。

使用 Azure 資訊安全中心提供 Azure 虛擬機器的安全性事件記錄檔監視。 由於安全性事件記錄檔產生的資料量，預設並不會儲存。

如果您的組織想要保留來自虛擬機器的安全性事件記錄檔資料，可以將它儲存在 Log Analytics 工作區中，並在 Azure 資訊安全中心中設定的所需資料收集層。

* [如何使用 Azure 監視器收集平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

* [Azure 資訊安全中心的資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [如何在 Azure 中監視虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指引**：活動記錄可用來審核虛擬機器擴展集資源上執行的作業和動作。 活動記錄包含資源的所有寫入作業（PUT、POST、DELETE），但讀取作業（GET）除外。 當您進行疑難排解時，可以使用活動記錄來尋找錯誤，或是監視組織中的使用者修改資源的方式。

藉由在您的虛擬機器（VM）上部署診斷擴充功能，來啟用來賓 OS 診斷資料的收集。 您可以使用診斷擴充功能，從 Azure 虛擬機器收集診斷資料，例如應用程式記錄或效能計數器。

若要深入瞭解 Azure 虛擬機器擴展集所支援的應用程式和服務，您可以同時啟用適用於 VM 的 Azure 監視器和 Application insights。 使用 Application Insights，您可以監視應用程式並捕捉遙測資料，例如 HTTP 要求、例外狀況等等，讓您可以將 Vm 與應用程式之間的問題相互關聯。

* [如何使用 Azure 監視器收集平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

* [查看和取出 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure 中監視虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [Application Insights 總覽](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：使用 Azure 資訊安全中心提供 Azure 虛擬機器的安全性事件記錄檔監視。 由於安全性事件記錄檔產生的資料量，預設並不會儲存。

如果您的組織想要保留來自虛擬機器的安全性事件記錄檔資料，可以將它儲存在 Log Analytics 工作區中，並在 Azure 資訊安全中心中設定的所需資料收集層。

* [Azure 資訊安全中心的資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [如何在 Azure 中監視虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導**方針：確定用來儲存虛擬機器記錄的任何儲存體帳戶或 log Analytics 工作區，都已根據貴組織的合規性法規來設定記錄保留期限。

* [如何在 Azure 中監視虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

* [如何設定 Log Analytics 工作區保留期限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**：分析和監視異常行為的記錄，並定期審查結果。 使用 Azure 監視器來審查記錄，並對記錄資料執行查詢。

或者，您可以啟用和內部資料，以 Azure Sentinel 或協力廠商 SIEM 來監視和檢查您的記錄。

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [了解 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：使用 Azure 資訊安全中心設定的 Log Analytics 工作區，以便在 Azure 虛擬機器的安全性記錄和事件中找到的異常活動上進行監視和警示。

或者，您可以啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM，以設定異常活動的警示。

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何管理 Azure 資訊安全中心中的警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [如何對 log analytics 記錄資料發出警示](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指引**：您可以使用 Microsoft 反惡意程式碼來進行 Azure 雲端服務和虛擬機器，並設定 Windows 虛擬機器將事件記錄到 Azure 儲存體帳戶。 設定 Log Analytics 工作區，以內嵌來自儲存體帳戶的事件，並在適當時建立警示。 遵循 Azure 資訊安全中心：「計算應用程式」中的建議 &amp; 。 針對 Linux 虛擬機器，您將需要協力廠商工具，以進行反惡意程式碼弱點偵測。

* [如何設定雲端服務和虛擬機器的 Microsoft 反惡意程式碼](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [如何啟用虛擬機器的來賓層級監視](https://docs.microsoft.com/azure/cost-management/azure-vm-extended-metrics)

* [將 Linux 伺服器上架到 Azure 安全性中心的指示](https://docs.microsoft.com/azure/security-center/quick-onboard-linux-computer)

* [下列連結提供 Microsoft 建議的安全性指導方針，可做為所選弱點軟體的準則清單](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：根據您的組織需求，從 AZURE MARKETPLACE 針對 DNS 記錄解決方案來執行協力廠商解決方案。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針： Azure 資訊安全中心提供 Azure 虛擬機器（VM）的安全性事件記錄檔監視。 資訊安全中心會在所有支援的 Azure Vm 上，以及在啟用自動布建或您可以手動安裝代理程式時所建立的任何新虛擬機器上，提供 Microsoft Monitoring Agent。 代理程式支援處理序建立事件 4688，及事件 4688 中的 CommandLine 欄位。 EventLog 會記錄在 VM 上建立的新處理序，並由資訊安全中心的偵測服務監視。

針對 Linux 虛擬機器，您可以手動設定每個節點的主控台記錄，並使用 syslog 來儲存資料。 此外，使用 Azure 監視器的 Log Analytics 工作區來審查記錄，並對 Azure 虛擬機器的 syslog 資料執行查詢。

* [Azure 資訊安全中心的資料收集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

* [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [Azure 監視器中的 Syslog 資料來源](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導**方針：雖然 Azure Active Directory 是管理使用者存取權的建議方法，但 Azure 虛擬機器可能會有本機帳戶。 本機和網域帳戶都應該經過檢查和管理，通常會有最少的使用量。 此外，請將 Azure Privileged Identity Management 用於用來存取虛擬機器資源的系統管理帳戶。

* [本機帳戶的資訊可在](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

* [許可權身分識別管理員的資訊](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指引**： Azure 虛擬機器擴展集和 Azure Active Directory 沒有預設密碼的概念。 客戶負責可使用預設密碼的協力廠商應用程式和 marketplace 服務。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：使用可存取虛擬機器的專用系統管理帳戶，建立有關的標準操作程式。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。 任何用來存取 Azure 虛擬機器資源的系統管理員帳戶，也可以由 Azure Privileged Identity Management （PIM）管理。 Azure Privileged Identity Management 提供數個選項，例如時間提升許可權、在假設角色之前需要多重要素驗證，以及委派選項，僅供特定的時間範圍使用，而且需要核准者。

* [瞭解 Azure 資訊安全中心身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [許可權身分識別管理員的資訊](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入（SSO）

**指導**方針：盡可能使用 SSO 搭配 Azure Active Directory，而不是設定個別服務的獨立認證。 使用 Azure 資訊安全中心身分識別和存取管理建議。

* [Azure Active Directory 中的應用程式單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

* [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指引**：啟用 Azure AD MFA，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站來進行系統管理工作

**指引**：使用已設定 MFA 的特殊權限存取 PAW (特殊權限存取工作站) 登入和設定 Azure 資源。

* [瞭解特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄及警示可疑活動

**指導**方針：當環境中發生可疑或不安全的活動時，使用 AZURE AD PRIVILEGED IDENTITY MANAGEMENT （PIM）來產生記錄檔和警示。 使用 Azure AD 風險偵測來檢視風險性使用者行為的相關警示和報告。 客戶可以選擇性地將 Azure 資訊安全中心風險偵測警示加入 Azure 監視器，並使用動作群組來設定自訂警示/通知。

* [如何部署 Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [瞭解 Azure 資訊安全中心風險偵測（可疑活動）](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

* [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [如何設定自訂警示和通知的動作群組](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用 Azure Active Directory 條件式存取原則和命名位置，只允許來自 IP 位址範圍或國家/地區之特定邏輯群組的存取權。

* [如何在 Azure 中設定命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 Azure Active Directory （Azure AD）做為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。 您可以使用受控識別來向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而您的程式碼中沒有任何認證。 在虛擬機器上執行的程式碼可以使用其受控識別，要求支援 Azure AD authentication 之服務的存取權杖。

* [如何建立和設定 Azure AD 實例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

* [Azure 資源受控識別概觀](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，您還可以使用 Azure Active Directory 身分識別存取審查，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者的存取權，以確保只有適當的使用者可以繼續存取。 使用 Azure 虛擬機器時，您必須檢查本機安全性群組和使用者，以確保沒有任何可能危害系統的非預期帳戶。

* [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導**方針：設定 Azure Active Directory 的診斷設定，以將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 此外，使用 Azure 監視器來檢查記錄，並對 Azure 虛擬機器的記錄資料執行查詢。

* [了解 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [如何在 Azure 中監視虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：使用 Azure Active Directory 的風險和身分識別保護功能，為偵測到與您儲存體帳戶資源相關的可疑動作設定自動回應。 您應該透過 Azure Sentinel 啟用自動回應，以執行貴組織的安全性回應。

* [如何檢視有風險的 Azure AD 登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指引**：在 Microsoft 需要存取客戶資料（例如在支援要求期間）的支援案例中，請使用 Azure 虛擬機器的客戶加密箱來審查及核准或拒絕客戶資料存取要求。

* [瞭解客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤儲存或處理敏感資訊的 Azure 虛擬機器。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 資源應該以虛擬網路/子網分隔、在網路安全性群組（NSG）或 Azure 防火牆內進行適當的標記和保護。 針對虛擬機器儲存或處理敏感性資料，請在不使用時，執行原則和程式將其關閉。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [如何部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [如何使用 Azure 防火牆來設定警示或警示和拒絕](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指引**：在網路周邊執行協力廠商解決方案，以監視未經授權的機密資訊傳輸，並在警示資訊安全性專業人員時封鎖這類傳輸。

針對由 Microsoft 管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：在執行 Windows 的虛擬機器（VM）之間傳輸的資料會以數種方式進行加密，視連線的本質而定（例如，連線至 RDP 或 SSH 會話中的 VM 時）。

Microsoft 會使用傳輸層安全性（TLS）通訊協定來保護雲端服務與客戶之間的資料。

* [VM 中的傳輸中加密](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針：使用協力廠商的 active discovery 工具來識別組織的技術系統所儲存、處理或傳輸的所有機密資訊，包括位於現場或遠端服務提供者的資料，以及更新組織的機密資訊清查。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用以角色為基礎的存取控制來控制對資源的存取

**指導**方針：使用角色型存取控制（RBAC），您可以將小組內的職責區隔，並僅授與執行其工作所需的虛擬機器（VM）使用者的存取權數量。 您不需為每個人授與 VM 的權限，而是只允許執行特定的動作。 您可以使用 Azure CLI 或 Azure PowerShell，在 Azure 入口網站中設定 VM 的存取控制。

* [Azure 資源的角色型存取控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

* [Azure 內建角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) (機器翻譯)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：執行協力廠商工具，例如自動化的主機型資料遺失防護解決方案，以強制執行存取控制以降低資料缺口的風險。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：虛擬機器（VM）上的虛擬磁片會使用伺服器端加密或 Azure 磁片加密（ADE）在待用時加密。 Azure 磁碟加密利用 Linux 的 DM Crypt 功能，在來賓 VM 內使用客戶管理的金鑰來加密受控磁片。 使用客戶管理的金鑰進行伺服器端加密，可讓您藉由加密儲存庫服務中的資料，對您的 VM 使用任何作業系統類型和映像，而改善 ADE 的效能。

* [虛擬機器擴展集的 Azure 磁碟加密](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以建立當虛擬機器擴展集和相關資源發生變更時的警示。

* [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Azure 儲存體分析記錄](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：請遵循在 Azure 虛擬機器上執行弱點評定 Azure 資訊安全中心的建議。 使用 Azure 安全性建議或協力廠商解決方案來執行虛擬機器的弱點評量。

* [如何實行 Azure 資訊安全中心弱點評估建議](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：針對支援的作業系統版本或儲存在共用映射資源庫中的自訂映射，啟用自動 OS 升級。

* [Azure 中虛擬機器擴展集的自動 OS 升級](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體標題部署自動修補程式管理解決方案

**指引**： Azure 虛擬機器擴展集（VMSS）可以使用自動 OS 映射升級。 在 VMSS 中，您可以使用適用于基礎虛擬機器的 Azure Desired State Configuration （DSC）延伸模組。 DSC 是用來在 Vm 上線時進行設定，因此它們正在執行您所需的軟體。

* [搭配 Azure DSC 擴充功能使用虛擬機器擴展集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-dsc)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：以一致的間隔匯出掃描結果，並比較結果以確認是否已補救弱點。 當您使用 Azure 資訊安全中心建議的弱點管理建議時，客戶可能會進入所選解決方案的入口網站，以查看歷程記錄掃描資料。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**方針：使用 Azure 資訊安全中心所提供的預設風險評等（安全分數）。

* [瞭解 Azure 資訊安全中心安全分數](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導**方針：使用 Azure Resource Graph 來查詢和探索訂用帳戶內的所有資源（包括虛擬機器）。 確保租用戶中有適當的 (讀取) 權限，且能列舉所有 Azure 訂用帳戶以及訂用帳戶中的資源。

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至 Azure 資源，讓中繼資料根據分類法以邏輯方式組織它們。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**.. 適當地使用標記、管理群組和個別的訂用帳戶來組織和追蹤虛擬機器擴展集和相關資源。 請定期調節清查，並確保會及時刪除訂用帳戶中未經授權的資源。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義並維護已核准 Azure 資源的清查

**指導**方針：根據我們的組織需求，建立已核准 Azure 資源的清查和適用于計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

此外，使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。 這有助於以高安全性為基礎的環境，例如具有儲存體帳戶的環境。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針： Azure 自動化在部署、作業和解除委任工作負載與資源期間提供完整的控制權。 利用 Azure 虛擬機器清查，自動收集虛擬機器上所有軟體的相關資訊。 注意： [軟體名稱]、[版本]、[發行者] 和 [重新整理時間] 可從 Azure 入口網站取得。 若要取得安裝日期和其他資訊的存取權，客戶必須啟用來賓層級診斷，並將 Windows 事件記錄檔帶入 Log Analytics 工作區。

目前無法使用彈性應用程式控制來進行虛擬機器擴展集。

* [Azure 自動化簡介](https://docs.microsoft.com/azure/automation/automation-intro)

* [如何啟用 Azure VM 清查](https://docs.microsoft.com/azure/automation/automation-tutorial-installed-software)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針： Azure 自動化在部署、作業和解除委任工作負載與資源期間提供完整的控制權。 您可以使用變更追蹤來識別虛擬機器上安裝的所有軟體。 您可以執行自己的進程，或使用 Azure 自動化狀態設定來移除未經授權的軟體。

* [Azure 自動化簡介](https://docs.microsoft.com/azure/automation/automation-intro)

* [使用變更追蹤解決方案來追蹤環境中的變更](https://docs.microsoft.com/azure/automation/change-tracking)

* [Azure 自動化狀態設定總覽](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：目前無法使用彈性應用程式控制來進行虛擬機器擴展集。 使用協力廠商軟體，只控制已核准應用程式的使用方式。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導**方針：目前無法使用彈性應用程式控制來進行虛擬機器擴展集。 如果這不符合您組織的需求，請執行協力廠商解決方案。

* [如何使用 Azure 資訊安全中心適應性應用程式控制](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：使用 Azure 條件式存取，藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，以限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導**方針：根據腳本的類型，您可以使用作業系統特定設定或協力廠商資源，以限制使用者在 Azure 計算資源內執行腳本的能力。

* [如何控制 Windows 環境中的 PowerShell 腳本執行](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導**方針：在您的 azure 環境中部署的高風險應用程式可能會使用虛擬網路、子網、訂用帳戶、管理群組等來進行隔離，並使用 Azure 防火牆、Web 應用程式防火牆（WAF）或網路安全性群組（NSG）進行充分的保護。

* [Azure 中的虛擬網路和虛擬機器](https://docs.microsoft.com/azure/virtual-machines/windows/network-overview)

* [Azure 防火牆概觀](https://docs.microsoft.com/azure/firewall/overview)

* [Web 應用程式防火牆概觀](https://docs.microsoft.com/azure/web-application-firewall/overview)

* [網路安全性概觀](https://docs.microsoft.com/azure/virtual-network/security-overview)

* [Azure 虛擬網路總覽](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

* [使用 Azure 管理群組來組織資源](https://docs.microsoft.com/azure/governance/management-groups/overview)

* [訂用帳戶決策指南](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全設定](/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則或 Azure 資訊安全中心來維護所有 Azure 資源的安全性設定。 此外，Azure Resource Manager 能夠在 JavaScript 物件標記法（JSON）中匯出範本，這應該進行檢查以確保設定符合/超過公司的安全性需求。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何下載 VM 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：使用 Azure 資訊安全中心建議 [修復虛擬機器上安全性設定中的弱點]，以維護所有計算資源的安全性設定。

* [如何監視 Azure 資訊安全中心建議](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

* [如何補救 Azure 資訊安全中心建議](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure Resource Manager 範本和 azure 原則，安全地設定與虛擬機器擴展集相關聯的 azure 資源。 Azure Resource Manager 範本是以 JSON 為基礎的檔案，用來部署虛擬機器以及 Azure 資源和自訂範本都必須維護。 Microsoft 會在基底範本上執行維護。 使用 Azure 原則 [拒絕] 和 [在不存在時部署]，對您的 Azure 資源強制使用安全設定。

* [建立 Azure Resource Manager 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [了解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：有數個選項可維護適用于部署的 Azure 虛擬機器（VM）的安全設定：

1. Azure Resource Manager 範本：這些是用來從 Azure 入口網站部署 VM 的 JSON 型檔案，而自訂範本則需要維護。 Microsoft 會在基底範本上執行維護。

2. 自訂虛擬硬碟（VHD）：在某些情況下，可能需要使用自訂 VHD 檔案，例如處理無法透過其他方式管理的複雜環境。

3. Azure 自動化狀態設定：部署基本 OS 之後，這可以用來更精細地控制設定，並透過自動化架構強制執行。

在大部分的情況下，與 Azure 自動化 Desired State Configuration 結合的 Microsoft 基底 VM 範本，可以協助滿足和維護安全性需求。

* [如何下載 VM 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

* [建立 ARM 範本的資訊](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

* [如何將自訂 VM VHD 上傳至 Azure](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指引**：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂的 Azure 原則、Azure Resource Manager 範本、Desired State Configuration 腳本等。 若要存取您在 Azure DevOps 管理的資源，例如程式碼、組建和工作追蹤，您必須擁有這些特定資源的許可權。 大部分許可權會透過內建安全性群組授與，如許可權和存取中所述。 如果與 Azure DevOps 整合，您可以授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory （Azure AD）中所定義群組的許可權（如果與 TFS 整合）或 Active Directory。

* [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [關於 Azure DevOps 中的許可權和群組](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導**方針：如果使用自訂映射（例如虛擬硬碟），請使用 Azure 角色型存取控制，以確保只有授權的使用者可以存取映射。

* [瞭解 Azure 中的 RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

* [如何在 Azure 中設定 RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：利用 Azure 原則來為您的虛擬機器進行警示、審查和強制執行系統設定。 此外，開發流程和管線以管理原則例外狀況。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導**方針： Azure 自動化狀態設定是適用于任何雲端或內部部署資料中心內 DESIRED STATE CONFIGURATION （DSC）節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指引**：利用 Azure 資訊安全中心來執行 Azure 虛擬機器的基準掃描。 自動設定的其他方法包括使用 Azure 自動化狀態設定。

* [如何修復 Azure 資訊安全中心中的建議](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

* [開始使用 Azure Automation State Configuration](https://docs.microsoft.com/azure/automation/automation-dsc-getting-started)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導**方針： Azure 自動化狀態設定是適用于任何雲端或內部部署資料中心內 DESIRED STATE CONFIGURATION （DSC）節點的設定管理服務。 它可讓您從中央、安全的位置快速且輕鬆地延展性到數千部電腦。 您可以輕鬆地上架機器、指派它們宣告式組態和檢視顯示每個電腦的符合性報告 (達您指定的所需狀態)。

* [將機器上架交由 Azure Automation State Configuration 管理](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：搭配 Azure Key Vault 使用受控服務識別，以簡化及保護雲端應用程式的秘密管理。

* [如何與 Azure 受控識別整合](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [如何建立 Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [如何使用受控識別提供 Key Vault 驗證](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指引**：在 Azure AD 中使用受控識別，以自動管理的身分識別來提供 Azure 服務。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

* [如何設定受控識別](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：使用適用于 Azure Windows 虛擬機器的 Microsoft Antimalware，持續監視及保護您的資源。 在 Azure Linux 虛擬機器中，您將需要協力廠商工具來進行反惡意程式碼保護。

* [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：不適用 Azure 虛擬機器，因為它是計算資源。

**Azure 資訊安全中心監視**：是

**責任**：不適用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導**方針：針對 Windows 虛擬機器部署時，Microsoft Antimalware for Azure 預設會自動安裝最新的簽章、平臺及引擎更新。 遵循 Azure 資訊安全中心中的建議：「計算 &amp; 應用程式」，以確保所有端點都是最新的簽章。 Windows OS 可以使用額外的安全性進一步保護，以使用與 Azure 資訊安全中心整合的 Microsoft Defender Advanced 威脅防護服務來限制病毒或惡意程式碼攻擊的風險。

在 Azure Linux 虛擬機器中，您將需要協力廠商工具來進行反惡意程式碼保護。

* [如何部署 Azure 雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

* [Microsoft Defender 進階威脅防護](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

* [如何設定雲端服務和虛擬機器的 Microsoft Antimalware](https://docs.microsoft.com/azure/virtual-machines/linux/security-recommendations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料復原](/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保週期性自動化備份

**指引**：使用 POWERSHELL 或 REST api，建立連接至實例的 Azure 虛擬機器擴展集實例或受控磁片的快照。 您也可以使用 Azure 自動化定期執行備份腳本。

* [如何製作虛擬機器擴展集實例和受控磁片的快照](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [Azure 自動化簡介](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指導**方針：使用 POWERSHELL 或 REST Api 建立 Azure 虛擬機器的快照集，或連接至這些實例的受控磁片。 備份 Azure Key Vault 中任何客戶管理的金鑰。

啟用 Azure 備份和目標 Azure 虛擬機器（VM），以及所需的頻率和保留期限。 這包括完整的系統狀態備份。 如果您使用 Azure 磁片加密，Azure VM 備份會自動處理客戶管理金鑰的備份。

* [在使用加密的 Azure Vm 上進行備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [Azure VM 備份的總覽](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction)

* [如何製作虛擬機器擴展集實例和受控磁片的快照](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#how-do-i-take-a-snapshot-of-a-virtual-machine-scale-set-instance)

* [如何在 Azure 中備份金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：確保能夠在 Azure 備份內定期執行受控磁片的資料還原。 如有必要，請測試將內容還原至隔離的虛擬網路或訂用帳戶。 客戶測試已備份客戶管理金鑰的還原。

如果您使用 Azure 磁片加密，您可以使用磁片加密金鑰來還原虛擬機器擴展集。 使用磁片加密時，您可以使用磁片加密金鑰來還原 Azure VM。

* [在使用加密的 Azure Vm 上進行備份](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)

* [在 Azure 中還原磁碟並建立已復原的 VM](https://docs.microsoft.com/azure/backup/tutorial-restore-disk)

* [如何在 Azure 中還原金鑰保存庫金鑰](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [如何啟用 Azure 虛擬機器擴展集的磁片加密](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指引**：使用鎖定啟用受控磁片的刪除保護。 啟用 Key Vault 中的虛刪除和清除保護，以防止金鑰遭到意外或惡意刪除。

* [鎖定資源以防止非預期的變更](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)

* [Azure Key Vault 虛刪除和清除保護總覽](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [利用 NIST 的電腦安全性事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。 您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

* [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

* [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

* [如何設定 Azure 資訊安全中心的安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

* [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導**方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」來自動觸發安全性警示和建議的回應，以保護您的 Azure 資源。

* [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 Red Team 練習](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：定期滲透測試您的 Azure 資源，並確保修復所有重要的安全性結果

**指導**方針：遵循 Microsoft Engagement 規則，確保您的滲透測試不會違反 microsoft 原則。 針對 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的 Red 小組和即時網站滲透測試策略和執行。

* [Engagement 的滲透測試規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
