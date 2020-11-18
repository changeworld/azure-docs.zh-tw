---
title: 適用于 Azure Load Balancer 的 Azure 安全性基準
description: Azure Load Balancer 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 37db72b2b5dadfba930ca003121140fd99c4736d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686067"
---
# <a name="azure-security-baseline-for-azure-load-balancer"></a>適用于 Azure Load Balancer 的 Azure 安全性基準

適用于 Microsoft Azure Load Balancer 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。 此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。 如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：使用內部 Azure 負載平衡器，只允許來自特定虛擬網路內的後端資源或對等互連虛擬網路的流量，而不會暴露于網際網路。 使用來源網路位址轉譯來執行外部 Load Balancer， (SNAT) 來偽裝後端資源的 IP 位址，以防止直接網際網路暴露。

Azure 提供兩種類型的 Load Balancer 供應專案：標準和基本。 將 Standard Load Balancer 用於所有生產工作負載。 執行網路安全性群組，並只允許存取您應用程式的受信任埠和 IP 位址範圍。 如果沒有任何網路安全性群組指派給後端虛擬機器的後端子網或 NIC，則不會將流量從負載平衡器允許到這些資源。 使用標準負載平衡器，提供輸出規則來定義具有網路安全性群組的輸出 NAT。 請參閱這些輸出規則來調整輸出連線的行為。 

建議您針對生產工作負載使用 Standard Load Balancer，且通常只會使用基本的 Load Balancer 來進行測試，因為根據預設，基本類型會開放到網際網路的連線，且不需要網路安全性群組來進行操作。 

- [Azure 中的輸出連線](load-balancer-outbound-connections.md)

- [升級 Azure 公用 Load Balancer](https://docs.microsoft.com/azure/load-balancer/upgrade-basic-standard)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針： Load Balancer 是通過服務，因為它依賴套用至後端資源的網路安全性群組規則，以及設定的輸出規則來控制網際網路存取。

檢查您的 Standard Load Balancer 針對您的 Load Balancer 設定的輸出規則，以及您可能會啟用隱含輸出規則的負載平衡規則分頁。

監視您的輸出連線計數，以追蹤資源抵達網際網路的頻率。 

使用資訊安全中心，並遵循網路保護建議來協助保護您的 Azure 網路資源。

遵循您後端資源的安全性建議，並啟用網路安全性群組流量記錄，並將記錄傳送至 Azure 儲存體帳戶進行審核。

也會將流量記錄傳送至 Log Analytics 工作區，然後使用流量分析來提供 Azure 雲端中的流量模式見解。 流量分析的優點包括能夠將網路活動視覺化、找出熱點和安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。

- [如何啟用網路安全性群組流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [如何啟用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [如何? 檢查我的輸出連接統計資料](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-outbound-connection-statistics)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：透過輸出規則和網路安全性群組，透過您的 Load Balancer 明確定義網際網路連線能力和有效的來源 ip，以使用 Microsoft 的威脅情報來保護您的 web 應用程式。

- [整合 Azure 防火牆](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：在您的 Azure 虛擬網路上啟用 azure 分散式阻絕服務 (DDoS) 標準保護，以防範 ddos 攻擊。 

在每個組織的網路界限上部署 Azure 防火牆，並啟用以威脅情報為基礎的篩選，並設定為惡意網路流量的「警示和拒絕」。

 

使用「安全性中心威脅防護」來偵測具有已知惡意 IP 位址的通訊。 

Standard Load Balancer 的設計預設為安全的，並且是私人和隔離虛擬網路的一部分。 除非由網路安全性群組開啟以明確准許允許的流量，且不允許已知的惡意 IP 位址，否則會關閉輸入流程。 除非您虛擬機器資源的子網或 NIC 上的網路安全性群組存在於 Load Balancer 後方，否則不允許流量觸達此資源。 

在每個組織的網路界限上部署 Azure 防火牆，並為其啟用以威脅情報為基礎的篩選，並設定為惡意網路流量的「警示和拒絕」，以防止惡意 IP 位址遭受攻擊。 

執行整合 Azure 防火牆與您 Load Balancer 的指引。

使用「安全性中心」威脅防護功能來偵測具有已知惡意 IP 位址的通訊。 

安全性中心 (標準層) 提供即時虛擬機器存取，並將允許的來源 IP 位址設定為只允許來自已核准的 IP 位址/範圍的存取。
 

使用資訊安全中心的彈性網路強化功能來建議網路安全性群組設定，以根據實際流量和威脅情報來限制埠和來源 Ip。
 

- [使用 Azure 入口網站管理 Azure DDoS 保護標準](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [以 Azure 防火牆威脅情報為主的篩選](https://docs.microsoft.com/azure/firewall/threat-intel)

- [Azure 資訊安全中心內的威脅防護](https://docs.microsoft.com/azure/security-center/threat-protection)

- [使用 Just-In-Time 存取來保護管理連接埠](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

- [Azure 資訊安全中心中的適應性網路強化](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [整合 Azure 防火牆與您的 Load Balancer](https://docs.microsoft.com/azure/firewall/overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：啟用網路監看員封包捕捉以調查異常活動。

- [如何建立網路監看員實例](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：利用 Azure Marketplace 中的供應專案，其支援具有您 Load Balancer 環境之承載檢查功能的 IDS/IPS 功能。 

如果不需要承載檢查，請使用 Azure 防火牆威脅情報。 Azure 防火牆威脅情報型篩選是用來警示和/或封鎖來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路界限上部署您所選擇的防火牆解決方案，以偵測及/或封鎖惡意流量。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火牆](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [如何使用 Azure 防火牆設定警示](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：透過輸出規則和網路安全性群組，透過您的 Load Balancer 明確定義網際網路連線能力和有效的來源 ip，以使用 Microsoft 的威脅情報功能來保護您的 web 應用程式。

- [整合 Azure 防火牆](https://docs.microsoft.com/azure/firewall/integrate-lb)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針：建立安全性規則時，請使用服務標籤來取代特定的 IP 位址。 在規則的 [來源] 或 [目的地] 欄位中指定服務標記名稱，以允許或拒絕對應服務的流量。 

Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。 

預設中，每個網路安全性群組皆含有服務標籤 AzureLoadBalancer，以許可健康情況探查流量。 

請參閱 Azure 檔，以取得可在網路安全性群組規則中使用的所有服務標記。

- [可用的服務標籤](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：使用 Azure 原則定義和執行網路資源的標準安全性設定。

使用 Azure 藍圖在單一藍圖定義中封裝重要環境成品（例如 Azure 資源管理員範本、Azure RBAC 控制項和原則），以簡化大規模的 Azure 部署。 

將藍圖套用至新的訂用帳戶，並透過版本控制來微調控制和管理。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [適用于網路的 Azure 原則範例](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：使用網路安全性群組的資源標記，以及與網路安全性和流量流程相關的其他資源。 

使用 [描述] 欄位，記錄允許個別網路安全性群組規則的網路流量進出的規則。

執行任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，這可確保所有資源都是使用標籤建立的，並通知任何現有未標記的資源。

使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [如何建立 Azure 虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何使用網路安全性群組規則來篩選網路流量](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 azure 活動記錄來監視資源設定，並偵測對您 Azure 資源的變更。 

在 Azure 監視器中建立警示，以在重大資源變更時通知您。

- [如何查看和取出 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：在您的訂用帳戶中查看活動記錄，以查看您的負載平衡器的輸出規則和網路安全性群組的變更。 

查看內部主機記錄檔，以確保您的後端資源是安全的。

將這些記錄匯出至 Log Analytics 或另一個儲存體平臺。 在 Azure 監視器中，請使用 Log Analytics 工作區來查詢和執行分析，並使用 Azure 儲存體帳戶來長期和封存儲存體。

根據您的組織商務需求，將此資料啟用並在面板上 Azure Sentinel 或協力廠商 SIEM。

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何使用 Azure 監視器收集平臺記錄和計量](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [如何使用 Azure 監視器收集 Azure 虛擬機器內部主機記錄檔](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [平臺活動記錄](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：檢查基本 Load Balancer 的活動記錄所捕捉的控制項和管理平面記錄和審核資訊。 預設會啟用這些捕獲設定。 

使用活動記錄來監視資源的動作，以查看所有活動及其狀態。 

使用活動記錄來判斷您訂用帳戶中的資源上所採取的作業： 

- 啟動作業的人員
- 作業進行的時間
- 作業的狀態

- 其他可能協助您研究作業的屬性值

透過 Azure PowerShell、Azure 命令列介面 (CLI) 、Azure REST API 或 Azure 入口網站，從活動記錄中取出資訊。 

使用 Azure 監視器來執行 Standard Load Balancer 設定功能的多維度診斷。  這些包括安全性的可用計量，包括來源網路位址轉譯 (SNAT) 連線、埠的相關資訊。 也可以取得 SYN (同步處理) 封包和封包計數器的其他計量。 

透過 Api 以程式設計方式取出多維度計量，並透過 [所有度量] 選項將它們寫入儲存體帳戶。

搭配 Microsoft Power BI 使用 Azure Audit Logs 內容套件，透過預先設定的儀表板來分析您的資料，或根據您的需求自訂這些視圖。

將記錄串流至事件中樞或 Log Analytics 工作區。 也可以從 Azure blob 儲存體解壓縮，並在不同的工具中查看，例如 Excel 和 Power BI。 

根據您的業務需求，啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM。

- [請參閱這篇文章，其中包含 Resource Manager 的 Audit 作業中詳細說明的每個方法的逐步指示。](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [公用基礎負載平衡器的 Azure 監視器記錄](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [查看活動記錄以監視資源的動作](https://docs.microsoft.com/azure/azure-resource-manager/management/view-activity-logs)

- [透過 API 以程式設計方式擷取多維度計量](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：依預設會啟用活動記錄，並在 Azure 的事件記錄存放區中保留90天。 根據組織在 Azure 監視器中的合規性法規來設定 Log Analytics 工作區保留期限。 使用 Azure 儲存體帳戶來取得長期和封存儲存體。

- [查看活動記錄以監視資源文章的動作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)

- [變更 Log Analytics 中的資料保留期限](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [如何設定 Azure 儲存體客戶紀錄的保留原則](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：使用 [Azure 入口網站] 中的 [Load Balancer] 頁面和 [Azure 監視器] 下的資源健康狀態頁面，來監視、管理 Standard Load Balancer 資源並進行疑難排解。 安全性的可用計量包括來源網路位址轉譯 (SNAT) 連線、埠的相關資訊。 此外，也可以取得 SYN (同步處理) 封包和封包計數器的其他計量。 

使用 Azure 監視器來檢查端點健康情況探查狀態，其中包含適用于標準、外部和內部負載平衡器的多維度計量。 透過 Api 以程式設計方式收集這些計量，並透過 [所有度量] 選項寫入至儲存體帳戶。

內部基本負載平衡器無法使用 Azure 監視器記錄。 

使用 Azure 監視器來查看基本外部 Load Balancer 的每個後端集區摘要的健康情況探查狀態，例如，因為健康情況探查失敗，所以後端集區中的實例數目未收到來自 Load Balancer 的要求。 

使用 Azure Operational Insights 執行記錄，以提供負載平衡器健全狀況狀態的相關統計資料。 

使用活動記錄來查看警示，並監視 Azure 訂用帳戶中資源及其狀態的動作。 預設會啟用活動記錄，並可在 Azure 入口網站中查看。 

使用 Microsoft Power BI 搭配 Azure Audit Logs 內容套件，並使用預先設定的儀表板來分析您的資料。 根據商務需求自訂視圖以符合您的需求。 

將記錄串流至事件中樞或 Log Analytics 工作區。 也可以從 Azure blob 儲存體解壓縮，並在不同的工具中查看，例如 Excel 和 Power BI。 您可以啟用和內部資料來 Azure Sentinel 或協力廠商 SIEM。

- [健康狀態探查](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) (機器翻譯)

- [Azure 監視器 REST API](https://docs.microsoft.com/rest/api/monitor)

- [如何透過 REST API 取得度量](https://docs.microsoft.com/rest/api/monitor/metrics/list)

- [使用計量、警示和資源健康狀態 Standard Load Balancer 診斷](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)

- [公用基礎負載平衡器的 Azure 監視器記錄](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)

- [在 Azure 入口網站中檢視負載平衡器計量](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#view-your-load-balancer-metrics-in-the-azure-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：搭配使用資訊安全中心與 Log Analytics 工作區，來監視和警示有關安全性記錄和事件中 Load Balancer 的異常活動。

啟用 Azure Sentinel 或使用協力廠商 SIEM 工具的資料。

- [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [如何在 Azure 資訊安全中心中管理警示](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [如何對 log analytics 記錄資料發出警示](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用 Azure Load Balancer。 這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用 Azure Load Balancer 是不會進行 DNS 查詢的核心網路服務。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導** 方針：不適用於 Azure Load Balancer，因為這是適用于計算資源的建議。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： azure 角色型存取控制 (azure RBAC) 可讓您透過角色指派來管理 azure 資源（例如您的 Load Balancer）的存取權。 將這些角色指派給使用者、群組服務主體和受控識別。 

使用 Azure CLI、Azure PowerShell 或 Azure 入口網站等工具，清查特定資源的預先定義和內建角色。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6：使用 Azure RBAC 來管理資源的存取權

**指導** 方針：使用 Azure RBAC 來控制 Load Balancer 資源的存取權。

- [如何設定 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針： Load Balancer 是不會儲存客戶資料的通過服務。 它是由 Microsoft 管理之基礎平臺的一部分。 

Microsoft 會將所有客戶內容視為機密資料，並將其移至絕佳的長度，以防止客戶資料遺失和公開。 

為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制項和功能。 

- [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄，在對重要的 Azure 資源進行變更時建立警示，例如用於重要生產工作負載的負載平衡器。

- [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢和探索所有資源 (例如，您訂用帳戶中) 的計算、儲存體、網路、埠、通訊協定等。 建議 Azure Resource Manager 建立和使用目前的資源。 

確定您的租使用者中有適當的 (讀取) 許可權，並列舉訂用帳戶中的所有 Azure 訂用帳戶和資源。

- [如何使用 Azure Resource Graph Explorer 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [如何查看您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：使用中繼資料將標記套用至 Azure 資源，以根據分類法以邏輯方式組織。

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：在適當的情況下使用標記、管理群組和個別訂用帳戶來組織和追蹤資產。 

定期協調清查，並確保會及時將未經授權的資源從您的訂用帳戶中刪除。

- [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

- [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據您的組織需求建立核准的 Azure 資源清單，您可以利用這些資源作為允許清單機制。 這可讓您的組織在正式審核並核准您組織的一般安全性評估程式之後，將任何新的可用 Azure 服務上線。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型施加限制。

使用擁有的訂用帳戶內的 Azure Resource Graph 來查詢及探索資源。 

確認環境中的所有 Azure 資源都已核准。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用 Azure Resource Graph Explorer 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：使用 Azure AD 條件式存取，藉由設定「Microsoft Azure 管理」應用程式的「封鎖存取」，來限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖對 Azure 資源管理員的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：商務營運所需但可能會對組織產生更高風險的軟體，應該在自己的虛擬機器和（或）虛擬網路內隔離，並使用 Azure 防火牆或網路安全性群組來充分保護。

- [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [如何使用安全性設定建立網路安全性群組](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的設定進行審核或強制執行。 使用內建的 Azure 原則定義。

Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合您組織的安全性需求。

將 Azure Resource Manager 範本匯出至 JavaScript 物件標記法 (JSON) 格式，並定期進行檢查以確保設定符合您的組織安全性需求。 

針對您的 Azure 資源，以安全的設定基準來執行來自「安全性中心」的建議。 

- [如何查看可用 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [教學課程：建立和管理原則來強制執行相容性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure 入口網站中的單一和多重資源匯出至範本](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [安全性建議 - 參考指南](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。  此外，您也可以使用 Azure Resource Manager 範本，維護您的組織所需的 Azure 資源安全性設定。 

- [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [建立和管理原則以強制執行合規性](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Resource Manager 範本總覽](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：使用 Azure DevOps 安全地儲存和管理您的程式碼，例如自訂 Azure 原則定義、Azure Resource Manager 範本和 desired state configuration 腳本。 

授與或拒絕特定使用者、內建安全性群組或 Azure Active Directory (中所定義之群組的許可權 Azure AD) 如果已與 Azure DevOps 整合，或與 TFS 整合時為 Active Directory。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [關於 Azure DevOps 中的許可權和群組](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用 Azure 原則定義和實行適用于 Azure 資源的標準安全性設定。  使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 執行與特定 Azure Load Balancer 資源相關的內建原則定義。  此外，使用 Azure 自動化部署設定變更。 Azure Load Balancer 資源。

- [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [如何使用別名](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用「安全性中心」為您的 Azure 資源執行基準掃描，並 Azure 原則警示和審核資源設定。

- [如何修復 Azure 資訊安全中心中的建議](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 

嚴重性是以「資訊安全中心」在「尋找」或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級為基礎。

使用標記標記訂用帳戶，並建立命名系統來識別和分類 Azure 資源，尤其是處理敏感性資料的資料。  

您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出您的資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 

使用安全中心的連續匯出功能，可讓您以手動方式或持續持續的方式來匯出警示和建議。 

利用「安全性中心」資料連線器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

- [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用安全中心的工作流程自動化功能，自動觸發安全性警示和建議的回應，以保護您的 Azure 資源。

- [如何在安全性輸入中設定工作流程自動化](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft Cloud 滲透測試的參與規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。 

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
