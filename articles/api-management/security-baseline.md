---
title: 適用於 API 管理的 Azure 安全性基準
description: 適用於 API 管理的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 62b4629d258f8779dc53b4b8e00684d3b793291d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183748"
---
# <a name="azure-security-baseline-for-api-management"></a>適用於 API 管理的 Azure 安全性基準

適用於 API 管理的 Azure 安全性基準包含的建議有助於改善部署的安全性狀態。

此服務的基準取自 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview.md)，其中提供有關如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指引**：Azure API 管理可以部署在 Azure 虛擬網路 (Vnet) 內，因此可存取網路內的後端服務。 開發人員入口網站與 API 閘道，均可設定為從網際網路 (外部) 存取或只在 Vnet (內部) 中存取。
- 可透過外部負載平衡器，從公用網際網路存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。
- 只能透過內部負載平衡器，從虛擬網路內存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

使用網路安全性群組，可以控制到 APIM 部署於其中的子網路之輸入和輸出流量。

* [如何將 Azure API 管理與虛擬網路搭配使用](./api-management-using-with-vnet.md)

* [在內部虛擬網路中使用 Azure API 管理服務](./api-management-using-with-internal-vnet.md)

* [整合內部 VNET 中的 API 管理與應用程式閘道](./api-management-howto-integrate-internal-vnet-appgateway.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指引**：使用網路安全性群組 (NSG)，可以控制到 APIM 部署於其中的子網路之輸入和輸出流量。 將 NSG 部署至您的 API 管理子網路，並啟用 NSG 流量記錄，並將記錄傳送到 Azure 儲存體帳戶進行流量稽核。 您也可以將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用「流量分析」來提供 Azure 雲端流量的見解。 「流量分析」的一些優點包括能將網路活動視覺化並識別作用點、識別安全性威脅、了解流量模式，以及找出網路設定錯誤。

注意：在 API 管理子網路上設定 NSG 時，有一組需要開啟的通訊埠。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。

* [了解適用於 Azure API 管理的 NSG 設定](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：若要保護重要 Web/HTTP API，請在內部模式的虛擬網路 (Vnet) 內設定 API 管理並設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 其可當作反向 Proxy，並提供 L7 負載平衡、路由、Web 應用程式防火牆 (WAF) 和其他服務。

結合使用內部 Vnet 中佈建的 API 管理與應用程式閘道前端，可實現下列案例︰
- 使用單一 API 管理資源同時對內部取用者與外部取用者公開所有 API。
- 使用單一 API 管理資源對外部取用者公開 API 的子集。
- 提供一種方法來開啟和關閉從公用網際網路對 API 管理的存取權。

注意:這項功能適用於 API 管理的 [進階] 與 [開發人員] 層。

* [如何整合內部 VNET 中的 API 管理與應用程式閘道](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [了解 Azure 應用程式閘道](../application-gateway/index.yml)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：在內部模式的虛擬網路 (Vnet) 內設定 API 管理，並設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 其可當作反向 Proxy，並提供 L7 負載平衡、路由、Web 應用程式防火牆 (WAF) 和其他服務。

結合使用內部 Vnet 中佈建的 API 管理與應用程式閘道前端，可實現下列案例︰
- 使用單一 API 管理資源同時對內部取用者與外部取用者公開所有 API。
- 使用單一 API 管理資源對外部取用者公開 API 的子集。
- 提供一種方法來開啟和關閉從公用網際網路對 API 管理的存取權。

注意:這項功能適用於 API 管理的 [進階] 與 [開發人員] 層。

在與您的 API 管理部署相關聯的 Vnet 上啟用 Azure DDoS 保護標準，以防止分散式阻斷服務 (DDoS) 攻擊。

使用 Azure 資訊安全中心的整合式威脅情報來拒絕與已知惡意或未使用的網際網路 IP 位址通訊。

* [如何整合內部 VNET 中的 API 管理與應用程式閘道](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [了解 Azure 應用程式閘道](../application-gateway/index.yml)

* [如何設定 DDoS 保護標準](../ddos-protection/manage-ddos-protection.md)

* [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指引**：使用網路安全性群組 (NSG)，可以控制到 APIM 部署於其中的子網路之輸入和輸出流量。 將 NSG 部署至您的 API 管理子網路，並啟用 NSG 流量記錄，並將記錄傳送到 Azure 儲存體帳戶進行流量稽核。 您也可以將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用「流量分析」來提供 Azure 雲端流量的見解。 「流量分析」的一些優點包括能將網路活動視覺化並識別作用點、識別安全性威脅、了解流量模式，以及找出網路設定錯誤。

注意：在 API 管理子網路上設定 NSG 時，有一組需要開啟的通訊埠。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。

* [了解適用於 Azure API 管理的 NSG 設定](./api-management-using-with-vnet.md#-common-network-configuration-issues)

* [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指引**：在內部模式的虛擬網路 (Vnet) 內設定 API 管理，並設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 其可當作反向 Proxy，並提供 L7 負載平衡、路由、Web 應用程式防火牆 (WAF) 和其他服務。 應用程式閘道 WAF 會提供保護，以防止常見的安全性攻擊和弱點，並可在下列兩種模式下執行：
- 偵測模式：監視並記錄所有威脅警示。 您可以在 [診斷] 區段中開啟應用程式閘道的記錄診斷。 您必須確保已選取並開啟 WAF 記錄。 在偵測模式中執行的 Web 應用程式防火牆不會封鎖傳入要求。
- 預防模式：封鎖規則偵測到的入侵和攻擊。 攻擊者會收到「403 未經授權存取」例外狀況，且連線會關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

結合使用內部 Vnet 中佈建的 API 管理與應用程式閘道前端，可實現下列案例︰
- 使用單一 API 管理資源同時對內部取用者與外部取用者公開所有 API。
- 使用單一 API 管理資源對外部取用者公開 API 的子集。
- 提供一種方法來開啟和關閉從公用網際網路對 API 管理的存取權。

注意:這項功能適用於 API 管理的 [進階] 與 [開發人員] 層。

* [如何整合內部 VNET 中的 API 管理與應用程式閘道](./api-management-howto-integrate-internal-vnet-appgateway.md)

* [了解 Azure 應用程式閘道 WAF](../web-application-firewall/ag/ag-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理流向 Web 應用程式的流量

**指引**：若要管理流向 Web/HTTP API 的流量，請將 API 管理部署到與外部或內部模式下 App Service 環境相關聯的虛擬網路 (Vnet)。

在內部模式下，於 API 管理前方設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 其可當作反向 Proxy，並提供 L7 負載平衡、路由、Web 應用程式防火牆 (WAF) 和其他服務。 應用程式閘道 WAF 會提供保護，以防止常見的安全性攻擊和弱點。

結合使用內部 Vnet 中佈建的 API 管理與應用程式閘道前端，可實現下列案例︰
- 使用單一 API 管理資源同時對內部取用者與外部取用者公開所有 API。
- 使用單一 API 管理資源對外部取用者公開 API 的子集。
- 提供一種方法來開啟和關閉從公用網際網路對 API 管理的存取權。

注意:這項功能適用於 API 管理的 [進階] 與 [開發人員] 層。

* [如何對外部取用者公開私人 API](/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [如何在 Vnet 中使用 API 管理](./api-management-using-with-vnet.md)

* [Azure 應用程式閘道上的 Web 應用程式防火牆](../web-application-firewall/ag/ag-overview.md)

* [了解 Azure 應用程式閘道](../application-gateway/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指引**：使用虛擬網路 (Vnet) 服務標籤，在您的 API 管理子網路上使用的網路安全性群組 (NSG) 上定義網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 在規則的適當來源或目的地欄位中指定服務標籤名稱 (例如 ApiManagement)，即可允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

注意：在 API 管理子網路上設定 NSG 時，有一組需要開啟的通訊埠。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。

* [了解和使用服務標籤](../virtual-network/service-tags-overview.md)

* [API 管理所需的連接埠](./api-management-using-with-vnet.md#-common-network-configuration-issues)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指引**：為您的 Azure API 管理佈署相關的網路設定，定義及實作標準安全性設定。 使用 "Microsoft.ApiManagement" 和 "Microsoft.Network" 命名空間中的 Azure 原則別名來建立自訂原則，以稽核或強制執行 Azure API 管理部署和相關資源的網路設定。 您也可以利用 Azure 虛擬網路的內建原則定義，例如：
- 應啟用 DDoS 保護標準

您也可以使用 Azure 藍圖，將金鑰環境成品（例如 Azure Resource Manager 範本、Azure 角色型存取控制 (Azure RBAC) 和原則）封裝在單一藍圖定義中，以簡化大規模的 Azure 部署。 您可以輕鬆地將藍圖套用至新的訂用帳戶、環境，以及透過版本控制來微調控制和管理。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指引**：將標籤使用於網路安全性群組 (NSG) 以及與網路安全性和流量相關的其他資源。 對於個別的 NSG 規則，您可以使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

* [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的 Azure API 管理部署相關聯的網路資源變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

* [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**：Microsoft 會維護 Azure API 管理的時間來源。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析、將記錄傳送至 Azure 儲存體進行長期/封存儲存或離線分析，或使用 Azure 事件中樞將記錄匯出至 Azure 上和別處的其他分析解決方案。 根據預設，Azure API 管理會將記錄和計量輸出到 Azure 監視器。 您可針對整個服務和每個 API 來設定記錄作業的詳細資訊。

除了 Azure 監視器，Azure API 管理也可與一或數個 Azure Application Insights 服務整合。 您可針對每個服務或每個 API 來設定 Application Insights 的記錄作業設定。

(選擇性) 啟用 Azure Sentinel 或第三方安全性事件與事件管理 (SIEM)，然後讓資料上線。

* [如何設定診斷設定](../azure-monitor/platform/diagnostic-settings.md#create-in-azure-portal)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [如何開始使用 Azure 監視器和第三方 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [如何建立自訂記錄和分析管線](./api-management-howto-log-event-hubs.md)

* [如何與 Azure Application Insights 整合](./api-management-howto-app-insights.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指引**：針對控制平面稽核記錄，啟用 Azure 活動記錄診斷設定，並將活動記錄傳送至 Log Analytics 工作區進行報告和分析、傳送至 Azure 儲存體進行長期保管，傳送至 Azure 事件中樞以便在 Azure 上和別處的其他分析解決方案中匯出。 使用 Azure 活動記錄資料，您可判斷在 Azure API 管理服務的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

針對資料平面稽核記錄，診斷記錄會提供豐富的作業與錯誤資訊，這些資訊對於稽核和疑難排解用途來說很重要。 診斷記錄與活動記錄不同。 活動記錄可讓您深入了解 Azure 資源上所執行的作業。 診斷記錄能讓您了解資源執行的作業。

* [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

* [如何啟用 Azure API 管理的診斷設定](./api-management-howto-use-azure-monitor.md#activity-logs)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指引**：在 Azure 監視器中，根據貴組織的合規性規定來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

* [如何設定 Log Analytics 工作區的記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [如何將記錄封存到 Azure 儲存體帳戶](../azure-monitor/platform/resource-logs.md#send-to-azure-storage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指引**：Azure API 管理會持續向 Azure 監視器發出記錄和計量，讓您能近乎即時地查看 API 的狀態和健康情況。 透過 Azure 監視器和 Log Analytics 工作區，您可檢閱、查詢、視覺化、路由傳送、封存、設定警示，以及對來自 API 管理和相關資源的計量和記錄採取動作。 分析及監視異常行為的記錄，並定期檢閱結果。

(選擇性) 將 API 管理與 Azure Application Insights 整合，並將其當作主要或次要監視、追蹤、報告和警示工具使用。

* [如何監視和檢閱 Azure API 管理的記錄](./api-management-howto-use-azure-monitor.md)

* [如何在 Azure 監視器中執行自訂查詢](../azure-monitor/log-query/get-started-queries.md)

* [了解 Log Analytics 工作區](../azure-monitor/log-query/log-analytics-tutorial.md)

* [如何與 Azure Application Insights 整合](./api-management-howto-app-insights.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指引**：啟用 Azure 活動記錄診斷設定以及 Azure API 管理執行個體的診斷設定，並將記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中執行查詢以搜尋字詞、找出趨勢、分析模式，以及提供許多其他以所收集資料為基礎的見解。 您可以根據 Log Analytics 工作區查詢來建立警示。

建立計量警示以得知有非預期的情況發生。 例如，當 Azure API 管理執行個體在一段時間內超過其預期的尖峰容量時，或者如果預先定義的期間內有特定數目的未經授權閘道要求或錯誤，就會收到通知。

(選擇性) 將 API 管理與 Azure Application Insights 整合，並將其當作主要或次要監視、追蹤、報告和警示工具使用。

(選擇性) 您可啟用 Azure Sentinel 或第三方 SIEM，並且讓資料上線。

* [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

* [如何啟用 Azure API 管理的診斷設定](./api-management-howto-use-azure-monitor.md#activity-logs)

* [如何設定 Azure API 管理的警示規則](./api-management-howto-use-azure-monitor.md#set-up-an-alert-rule)

* [如何檢視 Azure API 管理執行個體的容量計量](./api-management-capacity.md)

* [如何與 Azure Application Insights 整合](./api-management-howto-app-insights.md)

* [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指引**：不適用；Azure API 管理不會處理或產生與反惡意程式碼相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指引**：不適用；Azure API 管理不會處理或產生使用者可存取的 DNS 相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指引**：維護具有 Azure API 管理控制平面 (Azure 入口網站) 系統管理存取權的帳戶清查。

Azure Active Directory (AD) 具有必須明確指派且可供查詢的內建角色。 API 管理依賴這些角色和角色型存取控制，才能對 API 管理服務和實體進行細部的存取管理。

此外，API 管理在 API 管理的使用者系統中包含內建的 Administrators 群組。 API 管理中的群組在開發人員入口網站中控制 API 的可見度，而 Administrators 群組的成員可以查看所有的 API。

請遵循 Azure 資訊安全中心的建議來管理和維護系統管理帳戶。

* [如何在 Azure API 管理中使用角色型存取控制](./api-management-role-based-access-control.md)

* [如何取得 Azure API 管理執行個體之下的使用者清單](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [如何使用 PowerShell 在 Azure AD 中取得指派給目錄角色的使用者清單](/powershell/module/az.resources/get-azroleassignment)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色定義](/powershell/module/az.resources/get-azroledefinition)

* [了解 Azure 資訊安全中心的身分識別和存取建議](../security-center/recommendations-reference.md#recs-identity)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指引**：Azure API 管理沒有預設密碼/金鑰的概念。

Azure API 管理訂用帳戶是保護 API 存取的一種方法，不過其隨附了一對產生的訂用帳戶金鑰。 客戶可以隨時重新產生這些訂用帳戶金鑰。

* [了解 Azure API 管理訂用帳戶](./api-management-subscriptions.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：
- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

* [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](../security-center/security-center-identity-access.md)

* [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指引**：您可將 Azure API 管理設定成以 Azure Active Directory 作為識別提供者，在開發人員入口網站上驗證使用者，進而受益於 Azure AD 所提供的 SSO 功能。 設定之後，新的開發人員入口網站使用者即可選擇遵循現成的註冊程序，首先是透過 Azure AD 進行驗證，然後在驗證後於入口網站上完成註冊程序。

* [在 Azure API 管理中使用 Azure Active Directory 來授權開發人員帳戶](./api-management-howto-aad.md)

或者，您也可透過委派進一步自訂登入/註冊程序。 委派可讓您使用現有的網站來處理開發人員登入/註冊和產品訂閱，而非使用開發人員入口網站中的內建功能。 其可讓您的網站擁有使用者資料，並以自訂方式來執行這些步驟的驗證。

* [如何委派使用者註冊和產品訂用帳戶](./api-management-howto-setup-delegation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory (AD) 多重要素驗證 (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

* [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有的系統管理工作

**指引**：使用已設定多重要素驗證 (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

* [了解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

* [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

* [了解 Azure AD 風險偵測](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取 Azure 入口網站。

* [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指引**：可能的話，請使用 Azure AD 作為中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

設定 Azure API 管理開發人員入口網站，以使用 Azure Active Directory 來驗證開發人員帳戶。

使用 OAuth 2.0 通訊協定搭配 Azure Active Directory (Azure AD)，設定 Azure API 管理執行個體以保護 API。

* [如何在 Azure API 管理中使用 Azure Active Directory 來授權開發人員帳戶](./api-management-howto-aad.md)

* [如何使用 OAuth 2.0 搭配 Azure Active Directory 與 API 管理來保護 API](./api-management-howto-protect-backend-with-aad.md)

* [如何建立和設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並調節使用者存取權

**指引**：Azure Active Directory 會提供記錄來協助探索過時的帳戶。 客戶可以利用 Azure 身分識別存取權檢閱，有效地管理群組成員資格、對企業應用程式的存取，以及角色指派。 您可定期檢閱使用者存取權，以確保只有適當的使用者能繼續擁有適當的存取權。

客戶可以維護 API 管理使用者帳戶的清查，並視需要調節存取權。 在 API 管理中，開發人員就是使用 API 管理所公開 API 的取用者。 依預設，新建立的開發人員帳戶為 [作用中]，並與 [開發人員] 群組相關聯。 處於作用中狀態的開發人員帳戶可用來存取擁有訂用帳戶的所有 API。

系統管理員可以建立自訂群組，或利用相關聯 Azure Active Directory 租用戶中的外部群組。 自訂群組和外部群組可以與系統群組一起使用，提供開發人員 API 產品的能見度及存取權。

* [如何在 Azure API 管理中管理使用者帳戶](./api-management-howto-create-or-invite-developers.md)

* [如何取得 API 管理的使用者清單](/powershell/module/az.apimanagement/get-azapimanagementuser)

* [如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶](./api-management-howto-create-groups.md)

* [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視存取已停用帳戶的嘗試

**指引**：設定 Azure API 管理執行個體，以使用 Azure Active Directory 作為 Azure API 管理中的識別提供者來驗證開發人員帳戶。

使用 OAuth 2.0 通訊協定搭配 Azure Active Directory (Azure AD)，設定 Azure API 管理執行個體以保護 API。

將 JWT 驗證原則設定為傳入的 API 要求，以協助強制有效權杖的存在性和有效性。

建立 Azure AD 使用者帳戶的診斷設定，並將稽核記錄和登入記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中設定所需的警示。 此外，您可將 Log Analytics 工作區上架到 Azure Sentinel 或第三方 SIEM。

使用 `log-to-eventhub` 原則透過 API 管理來設定進階監視，擷取安全性分析所需的任何其他內容資訊，並傳送至 Azure Sentinel 或第三方 SIEM。

* [如何在 Azure API 管理中使用 Azure Active Directory 來授權開發人員帳戶](./api-management-howto-aad.md)

* [如何使用 OAuth 2.0 搭配 Azure Active Directory 與 API 管理來保護 API](./api-management-howto-protect-backend-with-aad.md)

* [API 管理存取限制原則](./api-management-access-restriction-policies.md)

* [如何將 Azure AD 記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

* [API 的進階監視](./api-management-log-to-eventhub-sample.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指引**：對於控制平面 (Azure 入口網站) 上的帳戶登入行為偏差，請使用 Azure Active Directory (AD) Identity Protection 和風險偵測功能，設定自動回應偵測到與使用者身分識別相關的可疑動作。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

* [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

* [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指引**：目前無法使用；Azure API 管理目前不支援客戶加密箱。

* [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶和/或管理群組。 Azure API 管理執行個體應該以虛擬網路 (VNet)/子網路區隔，並適當地標記。

* [如何建立額外的 Azure 訂用帳戶](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

* [如何將 Azure API 管理與虛擬網路搭配使用](./api-management-using-with-vnet.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指引**：目前無法使用；Azure API 管理目前無法使用資料識別、分類及外洩防護功能。

Microsoft 會管理 Azure API 管理的基礎結構，並已實作嚴格的控制項，以避免遺失或公開客戶資料。

* [了解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密所有傳輸中的敏感性資訊

**指引**：管理平面呼叫會經由 Azure Resource Manager 透過 TLS 進行。 需要有效的 JSON Web 權杖 (JWT)。 您可使用 TLS 和其中一種支援的驗證機制 (例如用戶端憑證或 JWT) 來保護資料平面呼叫。

* [了解 Azure API 管理中的資料保護](#data-protection)

* [管理 Azure API 管理中的 TLS 設定](./api-management-howto-manage-protocols-ciphers.md)

* [使用 Azure Active Directory 來保護 Azure API 管理中的 API](./api-management-howto-protect-backend-with-aad.md)

* [使用 Azure Active Directory B2C 來保護 Azure API 管理中的 API](./howto-protect-backend-frontend-azure-ad-b2c.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指引**：目前無法使用；Azure API 管理目前無法使用資料識別、分類及外洩防護功能。 標記可能會處理敏感性資訊的 Azure API 管理服務，並視需要實作第三方解決方案以期符合規範。

對於 Microsoft 所管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力來防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

* [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**：使用角色型存取控制來控制對 Azure API 管理的存取。 Azure API 管理依賴 Azure 角色型存取控制 (Azure RBAC) ，以針對 API 管理服務和 (實體啟用更細緻的存取管理，例如 Api 和原則) 。

* [如何在 Azure API 管理中使用角色型存取控制](./api-management-role-based-access-control.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。

Microsoft 會管理 Azure API 管理的基礎結構，並已實作嚴格的控制項，以避免遺失或公開客戶資料。

* [了解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指引**：敏感性資料 (例如憑證、金鑰和秘密具名值) 會使用服務所管理的各服務執行個體金鑰進行加密。 所有加密金鑰都會屬於服務所管理的各服務執行個體。

* [了解如何使用 Azure API 管理進行待用資料保護/加密](#data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指引**：使用 Azure 監視器搭配 Azure 活動記錄，以建立適用於生產 Azure Functions 應用程式及其他重要或相關資源發生變更時的警示。

* [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

* [如何在 Azure API 管理中使用 Azure 監視器和 Azure 活動記錄](./api-management-howto-use-azure-monitor.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指引**：目前無法使用；Azure 資訊安全中心的弱點評定目前不適用於 Azure API 管理。

Microsoft 所掃描和修補的基礎平台。 檢閱可用來減少服務設定相關弱點的安全性控制項。

* [了解 Azure API 管理可用的安全性控制項]()

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指引**：目前無法使用；Azure 資訊安全中心的弱點評定目前不適用於 Azure API 管理。

Microsoft 所掃描和修補的基礎平台。 客戶會檢閱可用來減少服務設定相關弱點的安全性控制項。

* [了解 Azure API 管理可用的安全性控制項]()

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

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

此外，使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

* [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

* [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指引**：使用下列內建原則定義，利用 Azure 原則對可在您訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制使用者透過指令碼來與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

* [Azure API 管理中的角色型存取控制](./api-management-role-based-access-control.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指引**：使用 Azure 原則，為您的 Azure API 管理服務定義及實作標準安全性設定。 使用 "Microsoft.ApiManagement" 命名空間中的 Azure 原則別名來建立自訂原則，以稽核或強制執行 Azure API 管理服務的設定。

* [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias)

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則，為您的 Azure API 管理服務定義及實作標準安全性設定。 使用 "Microsoft.ApiManagement" 命名空間中的 Azure 原則別名來建立自訂原則，以稽核或強制執行 Azure API 管理執行個體的設定。 使用 Azure 原則 [拒絕] 和 [在不存在時部署]，對您的 Azure 資源強制使用安全設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指引**：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存及管理您的 Azure API 管理服務設定。

* [如何在 Azure DevOps 中儲存檔案](/azure/devops/repos/git/gitworkflow)

* [Azure Repos 文件](/azure/devops/repos/index)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統組態管理工具

**指引**：使用 Azure 原則，為您的 Azure API 管理服務定義及實作標準安全性設定。 使用 "Microsoft.ApiManagement" 命名空間中的 Azure 原則別名來建立自訂原則，以稽核或強制執行 Azure API 管理執行個體的設定。 使用 Azure 原則 [拒絕] 和 [在不存在時部署]，對您的 Azure 資源強制使用安全設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指引**：使用 Azure API 管理的 DevOps 資源套件來執行 Azure API 管理的組態管理。

此外，使用 Azure 原則，為您的 Azure API 管理服務定義及實作標準安全性設定。 使用 "Microsoft.ApiManagement" 命名空間中的 Azure 原則別名來建立自訂原則，以稽核或強制執行 Azure API 管理執行個體的設定。 使用 Azure 原則 [拒絕] 和 [在不存在時部署]，對您的 Azure 資源強制使用安全設定。

* [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

* [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指引**：使用 Key Vault 來管理憑證，並將其設定為自動輪替。 如果使用 Azure Key Vault 來管理自訂網域 SSL 憑證，請確定憑證是以憑證 (而不是秘密) 的形式插入 Key Vault 中。

* [如何使用 Key Vault 金鑰輪替的指引來設定自訂網域名稱](./configure-custom-domain.md)

**Azure 資訊安全中心監視**：是

**責任**：Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指引**：使用 Azure Active Directory (AD) 所產生的受控服務識別，可讓您的「API 管理」執行個體輕鬆且安全地存取其他受 Azure AD 保護的資源 (例如 Azure Key Vault)。

* [如何為 API 管理執行個體建立受控識別](./api-management-howto-use-managed-service-identity.md)

* [使用受控識別進行驗證的原則](./api-management-authentication-policies.md#ManagedIdentity)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。

支援 Azure 服務的基礎主機 (例如 Azure API 管理) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：不適用，這項建議主要用於設計用來儲存資料的非計算資源。

支援 Azure 服務的基礎主機 (例如 Azure API 管理) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保會更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於設計用來儲存資料的非計算資源。

支援 Azure 服務的基礎主機 (例如 Azure API 管理) 已啟用 Microsoft 反惡意程式碼功能，但不會在客戶內容上執行該功能。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指引**：透過 Azure API 管理來發佈及管理 API，您將能夠利用非 Azure API 管理使用者要另行手動設計、實作及管理的容錯和基礎結構功能。 API 管理支援多區域部署，讓資料平面完全不受區域失敗的影響，而不會增加任何額外的運作負荷。

API 管理的服務備份與還原功能可提供實作災害復原策略時必要的構成要素。 您可手動或自動執行備份和還原作業。

* [如何將 API 管理資料平面部署到多個區域](./api-management-howto-deploy-multi-region.md)

* [如何在 Azure API 管理中使用服務備份和還原來實作災害復原](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [如何呼叫 API 管理備份作業](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup)

* [如何呼叫 API 管理還原作業](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指引**：Azure API 管理所提供的備份和還原作業會執行完整的系統備份和還原。

受控識別可用來從 API 管理自訂網域名稱的 Azure Key Vault 中取得憑證。 備份 Azure Key Vault 中所儲存的任何憑證。

* [如何在 Azure API 管理中使用服務備份和還原來實作災害復原](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [如何備份 Azure Key Vault 憑證](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：從備份執行服務的測試還原和憑證，藉此驗證備份。

* [如何呼叫 API 管理還原作業](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore)

* [如何還原 Azure Key Vault 憑證](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰受到保護

**指引**：Azure API 管理會將備份寫入客戶擁有的 Azure 儲存體帳戶。 遵循 Azure 儲存體安全性建議來保護您的備份。

* [如何在 Azure API 管理中使用服務備份和還原來實作災害復原](./api-management-howto-disaster-recovery-backup-restore.md#calling-the-backup-and-restore-operations)

* [Blob 儲存體的安全性建議](../storage/blobs/security-recommendations.md)

在 Key Vault 中啟用虛刪除，以防止金鑰遭到意外或惡意刪除。

* [如何在 Key Vault 中啟用虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [利用 NIST 的電腦安全性事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

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

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

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

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

* [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [安全性控制項：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在 60 天內補救所有重大安全性發現

**指引**： * [請遵循 Microsoft 參與規則，確保您的滲透測試不會違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於紅隊演練(Red Teaming) 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)