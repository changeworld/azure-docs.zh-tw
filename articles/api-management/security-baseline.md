---
title: 適用于 API 管理的 Azure 安全性基準
description: 適用于 API 管理的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: aa59a37f3a4413c92a483746ed4a08c363ab8457
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796521"
---
# <a name="azure-security-baseline-for-api-management"></a>適用于 API 管理的 Azure 安全性基準

適用于 API 管理的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自[Azure 安全性基準測試版本 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview)，其中提供有關如何在 Azure 上使用最佳作法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱[Azure 安全性基準總覽](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在您的虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指引**： Azure API 管理可以部署在 azure 虛擬網路（Vnet）內，因此它可以存取網路內的後端服務。 開發人員入口網站和 API 管理閘道可以設定為可從網際網路（外部）或僅在 Vnet （內部）中存取。
- 外部：可透過外部負載平衡器，從公用網際網路存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。
- 內部：只能透過內部負載平衡器，從虛擬網路內存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

您可以使用網路安全性群組來控制在其中部署 API 管理的子網中的輸入和輸出流量。

* [如何將 Azure API 管理與虛擬網路搭配使用](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [在內部虛擬網路中使用 Azure API 管理服務](https://docs.microsoft.com/azure/api-management/api-management-using-with-internal-vnet)

* [將內部 VNET 中的 API 管理與應用程式閘道整合](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視並記錄 Vnet、子網和 Nic 的設定和流量

**指導**方針：您可以使用網路安全性群組（nsg）來控制在其中部署 API 管理的子網中的輸入和輸出流量。 將 NSG 部署至您的 API 管理子網，並啟用 NSG 流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用「分析」來提供 Azure 雲端中流量的深入解析。 流量分析的一些優點是能夠將網路活動視覺化，並識別作用點、識別安全性威脅、瞭解流量模式，以及找出網路錯誤配置。

注意：在 API 管理子網上設定 NSG 時，有一組需要開啟的通訊埠。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。

* [瞭解 Azure API 管理的 NSG 設定](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何啟用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 web 應用程式

**指導**方針：若要保護重要的 WEB/HTTP api，請在內部模式的虛擬網路（Vnet）中設定 API 管理，並設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 它會作為反向 proxy，並提供 L7 負載平衡、路由、web 應用程式防火牆（WAF）和其他服務。

將內部 Vnet 中布建的 API 管理與應用程式閘道前端結合，可實現下列案例：
- 使用單一 API 管理資源，將所有 Api 公開給內部取用者和外部取用者。
- 使用單一 API 管理資源，將 Api 的子集公開給外部取用者。
- 提供一種方法，可從公用網際網路開啟和關閉 API 管理的存取權。

注意：這項功能適用于 API 管理的 Premium 和開發人員層級。

* [如何將內部 VNET 中的 API 管理與應用程式閘道整合](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [瞭解 Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指引**：在內部模式的虛擬網路（Vnet）內設定 API 管理，並設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 它會作為反向 proxy，並提供 L7 負載平衡、路由、web 應用程式防火牆（WAF）和其他服務。

將內部 Vnet 中布建的 API 管理與應用程式閘道前端結合，可實現下列案例：
- 使用單一 API 管理資源，將所有 Api 公開給內部取用者和外部取用者。
- 使用單一 API 管理資源，將 Api 的子集公開給外部取用者。
- 提供一種方法，可從公用網際網路開啟和關閉 API 管理的存取權。

注意：這項功能適用于 API 管理的 Premium 和開發人員層級。

在與您的 API 管理部署相關聯的 Vnet 上啟用 Azure DDoS 保護標準，以防止分散式阻斷服務（DDoS）攻擊。

使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意或未使用的網際網路 IP 位址的通訊。

* [如何將內部 VNET 中的 API 管理與應用程式閘道整合](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [瞭解 Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)

* [如何設定 Azure DDoS 保護 Standard](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [瞭解 Azure 資訊安全中心整合式威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導**方針：您可以使用網路安全性群組（NSG）來控制在其中部署 API 管理的子網中的輸入和輸出流量。 將 NSG 部署至您的 API 管理子網，並啟用 NSG 流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用「分析」來提供 Azure 雲端中流量的深入解析。 流量分析的一些優點是能夠將網路活動視覺化，並識別作用點、識別安全性威脅、瞭解流量模式，以及找出網路錯誤配置。

注意：在 API 管理子網上設定 NSG 時，有一組需要開啟的通訊埠。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。

* [瞭解 Azure API 管理的 NSG 設定](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何啟用和使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵預防系統（IDS/IPS）

**指引**：在內部模式的虛擬網路（Vnet）內設定 API 管理，並設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 它會作為反向 proxy，並提供 L7 負載平衡、路由、web 應用程式防火牆（WAF）和其他服務。 應用程式閘道 WAF 提供保護，防止常見的安全性攻擊和弱點，並可在下列兩種模式中執行：
- 偵測模式：監視並記錄所有威脅警示。 您可以在 [診斷] 區段中開啟應用程式閘道的記錄診斷。 您必須確定已選取 WAF 記錄並加以開啟。 在偵測模式中執行的 Web 應用程式防火牆不會封鎖傳入要求。
- 預防模式：封鎖規則偵測到的入侵和攻擊。 攻擊者會收到「403 未經授權存取」例外狀況，且連線會關閉。 預防模式會將這類攻擊記錄在 WAF 記錄中。

將內部 Vnet 中布建的 API 管理與應用程式閘道前端結合，可實現下列案例：
- 使用單一 API 管理資源，將所有 Api 公開給內部取用者和外部取用者。
- 使用單一 API 管理資源，將 Api 的子集公開給外部取用者。
- 提供一種方法，可從公用網際網路開啟和關閉 API 管理的存取權。

注意：這項功能適用于 API 管理的 Premium 和開發人員層級。

* [如何將內部 VNET 中的 API 管理與應用程式閘道整合](https://docs.microsoft.com/azure/api-management/api-management-howto-integrate-internal-vnet-appgateway)

* [瞭解 Azure 應用程式閘道 WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 web 應用程式的流量

**指導**方針：若要管理流向 WEB/HTTP api 的流量，請將 API 管理部署到與外部或內部模式 App Service 環境相關聯的虛擬網路（Vnet）。

在 [內部] 模式中，于 [API 管理] 前方設定 Azure 應用程式閘道。 應用程式閘道是 PaaS 服務。 它會作為反向 proxy，並提供 L7 負載平衡、路由、web 應用程式防火牆（WAF）和其他服務。 應用程式閘道 WAF 可防止常見的安全性攻擊和弱點。

將內部 Vnet 中布建的 API 管理與應用程式閘道前端結合，可實現下列案例：
- 使用單一 API 管理資源，將所有 Api 公開給內部取用者和外部取用者。
- 使用單一 API 管理資源，將 Api 的子集公開給外部取用者。
- 提供一種方法，可從公用網際網路開啟和關閉 API 管理的存取權。

注意：這項功能適用于 API 管理的 Premium 和開發人員層級。

* [如何將私用 Api 公開給外部取用者](https://docs.microsoft.com/azure/architecture/example-scenario/apps/publish-internal-apis-externally)

* [如何使用 Vnet 中的 API 管理](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

* [Azure 應用程式閘道上的 Web 應用程式防火牆](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [瞭解 Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負擔降至最低

**指導**方針：使用虛擬網路（Vnet）服務標籤，在您的 API 管理子網上使用的網路安全性群組（nsg）上定義網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當 [來源] 或 [目的地] 欄位中指定服務標籤名稱（例如，ApiManagement），您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標記。

注意：在 API 管理子網上設定 NSG 時，有一組需要開啟的通訊埠。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。

* [瞭解和使用服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [API 管理所需的埠](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet#-common-network-configuration-issues)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指引**：針對與您的 Azure API 管理部署相關的網路設定，定義及執行標準安全性設定。 使用 "ApiManagement" 和 "Microsoft" 命名空間中的 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure API 管理部署和相關資源的網路設定。 您也可以利用 Azure 虛擬網路的內建原則定義，例如：
- 應啟用 DDoS 保護標準

您也可以使用 Azure 藍圖，在單一藍圖定義中封裝金鑰環境成品（例如 Azure Resource Manager 範本、角色型存取控制（RBAC）和原則），以簡化大規模的 Azure 部署。 您可以輕鬆地將藍圖套用至新的訂用帳戶、環境，以及透過版本控制來微調控制和管理。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：檔流量設定規則

**指導**方針：使用網路安全性群組（nsg）的標記，以及與網路安全性和流量相關的其他資源。 針對個別的 NSG 規則，您可以使用 [描述] 欄位來指定允許進出網路流量的任何規則的商務需求和（或）持續時間（等）。

* [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指引**：使用 Azure 活動記錄來監視網路資源設定，並偵測與您的 Azure API 管理部署相關聯之網路資源的變更。 在 Azure 監視器中建立警示，以在重大網路資源的變更發生時觸發。

* [如何查看和取出 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure 監視器中建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指引**： Microsoft 會維護 Azure API 管理的時間來源。

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指引**：在 Azure 監視器中，使用 Log Analytics 工作區來查詢和執行分析、將記錄傳送至 Azure 儲存體以進行長期/封存儲存或離線分析，或使用 Azure 事件中樞將記錄匯出到 Azure 和其他位置上的其他分析解決方案。 根據預設，Azure API 管理會將記錄和計量輸出到 Azure 監視器。 記錄的詳細資訊可以在整個服務和每個 API 的基礎上進行設定。

除了 Azure 監視器，Azure API 管理也可以與一或多個 Azure 應用程式 Insights 服務整合。 Application Insights 的記錄設定可以根據每個服務或每個 API 來設定。

（選擇性） [啟用] 和 [內部資料]，以 Azure Sentinel 或協力廠商安全性事件和事件管理（SIEM）。

* [如何設定診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [如何上架 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [如何開始使用 Azure 監視器和協力廠商 SIEM 整合](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [如何建立自訂記錄和分析管線](https://docs.microsoft.com/azure/api-management/api-management-howto-log-event-hubs)

* [如何與 Azure 應用程式 Insights 整合](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的審核記錄

**指引**.. 針對控制平面的審核記錄，啟用 Azure 活動記錄診斷設定，並將活動記錄傳送至 Log Analytics 工作區以進行報告和分析，以 Azure 儲存體長期保存，以 Azure 事件中樞在 Azure 和其他位置的其他分析解決方案中匯出。 使用 Azure 活動記錄資料，您可以判斷在 Azure API 管理服務的控制平面層級執行的任何寫入作業（PUT、POST、DELETE）的「內容、物件和時間」。

針對資料平面的審核記錄，診斷記錄會提供有關用於審核和疑難排解之重要作業和錯誤的豐富資訊。 診斷記錄與活動記錄不同。 活動記錄可讓您深入了解 Azure 資源上所執行的作業。 診斷記錄能讓您了解資源執行的作業。

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何啟用 Azure API 管理的診斷設定](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指引**：在 Azure 監視器中，根據貴組織的合規性法規來設定您的 Log Analytics 工作區保留期間。 針對長期/封存儲存體使用 Azure 儲存體帳戶。

* [如何設定 Log Analytics 工作區的記錄檔保留參數](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [如何將記錄封存到 Azure 儲存體帳戶](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指引**： Azure API 管理會持續發出記錄和計量給 Azure 監視器，讓您近乎即時地瞭解 api 的狀態和健康情況。 使用 Azure 監視器和 Log Analytics 工作區，您可以審查、查詢、視覺化、路由、封存、設定警示，以及對來自 API 管理和相關資源的計量和記錄採取動作。 分析及監視異常行為的記錄，並定期審查結果。

（選擇性）將 API 管理與 Azure 應用程式深入解析整合，並使用它作為主要或次要監視、追蹤、報告和警示工具。

* [如何監視和查看 Azure API 管理的記錄](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor)

* [如何在 Azure 監視器中執行自訂查詢](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

* [瞭解 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

* [如何與 Azure 應用程式 Insights 整合](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指引**：啟用 Azure 活動記錄診斷設定，以及 Azure API 管理實例的診斷設定，並將記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中執行查詢來搜尋字詞、識別趨勢、分析模式，以及根據收集到的資料提供許多其他深入解析。 您可以根據您的 Log Analytics 工作區查詢來建立警示。

建立計量警示，讓您知道何時發生未預期的狀況。 例如，當您的 Azure API 管理實例在一段時間內超過預期的尖峰容量，或是在預先定義的一段時間內有特定數目的未經授權閘道要求或錯誤時，就會收到通知。

（選擇性）將 API 管理與 Azure 應用程式深入解析整合，並使用它作為主要或次要監視、追蹤、報告和警示工具。

（選擇性）您可以啟用和麵板上的資料，以 Azure Sentinel 或協力廠商 SIEM。

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何啟用 Azure API 管理的診斷設定](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs)

* [如何針對未經授權的要求設定警示規則](https://docs.microsoft.com/Azure/api-management/api-management-howto-use-azure-monitor#set-up-an-alert-rule-for-unauthorized-request)

* [如何查看 Azure API 管理實例的容量計量](https://docs.microsoft.com/azure/api-management/api-management-capacity)

* [如何與 Azure 應用程式 Insights 整合](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights)

* [如何上架 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中反惡意程式碼記錄

**指導**方針：不適用;Azure API 管理不會處理或產生與反惡意程式碼相關的記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;Azure API 管理不會處理或產生使用者可存取的 DNS 相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列審核記錄

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別和存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指引**：維護具有 Azure API 管理控制平面（Azure 入口網站）之系統管理存取權的帳戶清查。

Azure Active Directory （AD）具有必須明確指派且可供查詢的內建角色。 API 管理依賴這些角色和以角色為基礎的存取控制，為 API 管理服務和實體啟用更細緻的存取管理。

此外，API 管理會在 API 管理的使用者系統中包含內建的系統管理員群組。 API 管理中的群組在開發人員入口網站中控制 Api 的可見度，而 Administrators 群組的成員則可以查看所有 Api。

請遵循 Azure 資訊安全中心的建議，以管理和維護系統管理帳戶。

* [如何在 Azure API 管理中使用以角色為基礎的存取控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

* [如何取得 Azure API 管理實例底下的使用者清單](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何使用 PowerShell 取得在 Azure AD 中指派給目錄角色的使用者清單](https://docs.microsoft.com/powershell/module/az.resources/get-azroleassignment?view=azps-3.7.0)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色定義](https://docs.microsoft.com/powershell/module/az.resources/get-azroledefinition?view=azps-3.7.0)

* [瞭解 Azure 資訊安全中心的身分識別和存取建議](https://docs.microsoft.com/azure/security-center/recommendations-reference#recs-identity)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：變更預設密碼（若適用）

**指引**： Azure API 管理沒有預設密碼/金鑰的概念。

Azure API 管理訂用帳戶是保護 Api 存取的一種方法，不過，這也是一對產生的訂用帳戶金鑰。 客戶可以隨時重新產生這些訂用帳戶金鑰。

* [瞭解 Azure API 管理訂用帳戶](https://docs.microsoft.com/azure/api-management/api-management-subscriptions)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導**方針：使用專用的系統管理帳戶來建立標準操作程式。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用的系統管理帳戶，您可以使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：
- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

* [如何使用 Azure 資訊安全中心來監視身分識別和存取（預覽）](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何使用 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：搭配 Azure Active Directory 使用單一登入（SSO）

**指引**：您可以將 Azure API 管理設定為利用 Azure Active Directory 做為身分識別提供者，以在開發人員入口網站上驗證使用者，以受益于 Azure AD 所提供的 SSO 功能。 一旦設定之後，新的開發人員入口網站使用者就可以選擇遵循全新的註冊程式，方法是先透過 Azure AD 進行驗證，然後在入口網站上完成註冊程式後再進行驗證。

* [在 Azure API 管理中使用 Azure Active Directory 來授權開發人員帳戶](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

或者，您也可以透過委派進一步自訂登入/註冊程式。 委派可讓您使用現有的網站來處理開發人員登入/註冊和產品的訂閱，而不是在開發人員入口網站中使用內建功能。 它可讓您的網站擁有使用者資料，並以自訂方式執行這些步驟的驗證。

* [如何委派使用者註冊和產品訂用帳戶](https://docs.microsoft.com/azure/api-management/api-management-howto-setup-delegation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 AZURE ACTIVE DIRECTORY （AD）多重要素驗證（MFA），並遵循 Azure 資訊安全中心身分識別和存取管理建議。

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何監視 Azure 資訊安全中心內的身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦（特殊許可權存取工作站）進行所有系統管理工作

**指導**方針：使用特殊許可權存取工作站（PAW）搭配設定用來登入和設定 Azure 資源的多重要素驗證（MFA）。

* [瞭解特殊許可權存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：從系統管理客戶紀錄和警示可疑活動

**指引**：使用 AZURE ACTIVE DIRECTORY （AD） PRIVILEGED IDENTITY MANAGEMENT （PIM）來產生記錄，並在環境中發生可疑或不安全的活動時發出警示。

此外，您可以使用 Azure AD 風險偵測來查看警示和報告有風險的使用者行為。

* [如何部署 Privileged Identity Management （PIM）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [瞭解 Azure AD 風險偵測](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導**方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍或國家/地區的特定邏輯群組存取 Azure 入口網站。

* [如何在 Azure 中設定命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：盡可能使用 Azure AD 做為中央驗證和授權系統。 AAD 會針對待用和傳輸中的資料使用強式加密來保護資料。 Azure AD 也會 salts、雜湊並安全地儲存使用者認證。

設定 Azure API 管理開發人員入口網站，以使用 Azure Active Directory 來驗證開發人員帳戶。

設定 Azure API 管理實例，以使用 OAuth 2.0 通訊協定搭配 Azure Active Directory （AD）來保護您的 Api。

* [如何使用 Azure API 管理中的 Azure Active Directory 來授權開發人員帳戶](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [如何使用 OAuth 2.0 搭配 Azure Active Directory 和 API 管理來保護 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [如何建立和設定 AAD 實例](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期審查並協調使用者存取

**指導**方針： Azure Active Directory 提供記錄檔，以協助探索過時的帳戶。 客戶可以利用 Azure 身分識別存取審查，有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢查使用者存取權，以確保只有適當的使用者能夠繼續擁有適當的存取權。

客戶可以維護 API 管理使用者帳戶的清查，並視需要協調存取。 在 API 管理中，開發人員是以 API 管理公開的 Api 取用者。 依預設，新建立的開發人員帳戶為 [作用中]，且與 [開發人員] 群組相關聯。 處於「作用中」  狀態的開發人員帳戶可用來存取擁有訂用帳戶的所有 API。

系統管理員可以建立自訂群組，或利用相關聯 Azure Active Directory 租使用者中的外部群組。 自訂群組和外部群組可以與系統群組一起使用，提供開發人員 API 產品的能見度及存取權。

* [如何在 Azure API 管理中管理使用者帳戶](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [如何取得 API 管理使用者的清單](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [如何在 Azure API 管理中建立和使用群組來管理開發人員帳戶](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)

* [如何使用 Azure 身分識別存取評論](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視嘗試存取停用的帳戶

**指引**：使用 Azure Active Directory 做為 Azure api 管理中的身分識別提供者，設定您的 Azure api 管理實例來驗證開發人員帳戶。

設定 Azure API 管理實例，以使用 OAuth 2.0 通訊協定搭配 Azure Active Directory （AD）來保護您的 Api。

將 JWT 驗證原則設定為傳入的 API 要求，以協助強制執行有效權杖的存在和有效性。

建立 Azure AD 使用者帳戶的診斷設定，並將 audit 記錄和登入記錄傳送到 Log Analytics 工作區。 在 Log Analytics 中設定所需的警示。 此外，您可以將 Log Analytics 工作區上架到 Azure Sentinel 或協力廠商 SIEM。

使用`log-to-eventhub`原則來設定具有 API 管理的先進監視，並捕捉安全性分析所需的任何其他內容資訊，並傳送至 Azure Sentinel 或協力廠商 SIEM。

* [如何使用 Azure API 管理中的 Azure Active Directory 來授權開發人員帳戶](https://docs.microsoft.com/azure/api-management/api-management-howto-aad)

* [如何使用 OAuth 2.0 搭配 Azure Active Directory 和 API 管理來保護 API](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [API 管理存取限制原則](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies)

* [如何將 Azure AD 記錄整合至 Azure 監視器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [如何在面板上 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Api 的先進監視](https://docs.microsoft.com/azure/api-management/api-management-log-to-eventhub-sample)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導**方針：對於控制平面（Azure 入口網站）上的帳戶登入行為偏差，請使用 AZURE ACTIVE DIRECTORY （AD） Identity Protection 和風險偵測功能，針對偵測到與使用者身分識別相關的可疑動作，設定自動回應。 您也可以將資料內嵌到 Azure Sentinel，以進行進一步的調查。

* [如何觀看 Azure AD 有風險的登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何上架 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：提供 Microsoft 在支援案例期間存取相關的客戶資料

**指導**方針：目前無法使用;Azure API 管理目前不支援客戶加密箱。

* [客戶加密箱支援的服務清單](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護機密資訊的清查

**指引**：使用標記來協助追蹤儲存或處理敏感資訊的 Azure 資源。

* [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感資訊的系統

**指引**：在開發、測試和生產環境中，執行不同的訂用帳戶和/或管理群組。 Azure API 管理實例應該以虛擬網路（VNet）/子網區隔，並適當地標記。

* [如何建立額外的 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何將 Azure API 管理與虛擬網路搭配使用](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的機密資訊傳輸

**指導**方針：目前無法使用;Azure API 管理目前無法使用資料識別、分類及遺失防護功能。

Microsoft 會管理 Azure API 管理的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有機密資訊

**指導**方針：透過 TLS Azure Resource Manager 來進行管理平面呼叫。 需要有效的 JSON Web 權杖 (JWT)。 您可以使用 TLS 和支援的其中一種驗證機制（例如用戶端憑證或 JWT）來保護資料平面呼叫。

* [瞭解 Azure API 管理中的資料保護](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

* [管理 Azure API 管理中的 TLS 設定](https://docs.microsoft.com/azure/api-management/api-management-howto-manage-protocols-ciphers)

* [使用 Azure Active Directory 保護 Azure API 管理中的 Api](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad)

* [使用 Azure Active Directory B2C 保護 Azure API 管理中的 Api](https://docs.microsoft.com/azure/api-management/howto-protect-backend-frontend-azure-ad-b2c)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用 active discovery 工具來識別敏感性資料

**指導**方針：目前無法使用;Azure API 管理目前無法使用資料識別、分類及遺失防護功能。 標記可能會處理敏感資訊的 Azure API 管理服務，並視需要執行協力廠商解決方案，以符合規範目的。

針對由 Microsoft 管理的基礎平臺，Microsoft 會將所有客戶內容視為機密，並移至絕佳的長度，以防範客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制和功能。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指引**：使用以角色為基礎的存取控制來控制 Azure API 管理的存取權。 Azure API 管理需要 Azure 角色型存取控制 (RBAC)，才能針對 API 管理服務及實體 (例如 API 和原則) 啟用更細緻的存取管理。

* [如何在 Azure API 管理中使用以角色為基礎的存取控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用以主機為基礎的資料遺失防護來強制存取控制

**指導**方針：不適用;這項建議適用于計算資源。

Microsoft 會管理 Azure API 管理的基礎結構，並已實行嚴格的控制，以避免遺失或公開客戶資料。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密機密資訊待用

**指導**方針：機密資料（例如憑證、金鑰和秘密名稱）會使用服務管理的每個服務實例金鑰進行加密。 所有加密金鑰都是針對每個服務實例，而且受服務管理。

* [使用 Azure API 管理來瞭解資料保護/待用加密](https://docs.microsoft.com/azure/api-management/api-management-security-controls#data-protection)

**Azure 資訊安全中心監視**：不適用

**責任**： Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：對重要 Azure 資源的變更進行記錄和警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄，以針對生產 Azure Functions 應用程式以及其他重要或相關資源進行變更時，建立警示。

* [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [如何在 Azure API 管理中使用 Azure 監視器和 Azure 活動記錄](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：目前無法使用;Azure 資訊安全中心中的弱點評定目前不適用於 Azure API 管理。

Microsoft 所掃描和修補的基礎平臺。 查看可用來減少服務設定相關弱點的安全性控制項。

* [瞭解適用于 Azure API 管理的安全性控制項](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的協力廠商軟體修補程式管理解決方案

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較回溯弱點掃描

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程式來排定所發現弱點的補救優先順序

**指導**方針：目前無法使用;Azure 資訊安全中心中的弱點評定目前不適用於 Azure API 管理。

Microsoft 所掃描和修補的基礎平臺。 客戶可查看可供他們使用的安全性控制，以減少與服務設定相關的弱點。

* [瞭解適用于 Azure API 管理的安全性控制項](https://docs.microsoft.com/azure/api-management/api-management-security-controls)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱[安全性控制：清查和資產管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源（例如計算、儲存體、網路、埠及通訊協定等）。 請確定您的租使用者中有適當（讀取）許可權，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

* [如何使用 Azure Resource Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [如何查看您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [瞭解 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至 Azure 資源，讓中繼資料以邏輯方式將其組織成分類法。

* [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**.. 適當地使用標記、管理群組和個別訂用帳戶來組織和追蹤 Azure 資源。 定期協調清查，並確保未經授權的資源會及時從訂用帳戶中刪除。

此外，使用下列內建原則定義，透過 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何建立額外的 Azure 訂用帳戶](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標記](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護已核准 Azure 資源和軟體標題的清查

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：未核准的 Azure 資源的監視

**指引**：使用下列內建原則定義，透過 Azure 原則來限制可在訂用帳戶中建立的資源類型：
- 不允許的資源類型
- 允許的資源類型

使用 Azure Resource Graph 來查詢/探索其訂用帳戶內的資源。 請確定已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未核准的 Azure 資源和軟體應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用 Azure 原則來對可使用下列內建原則定義在客戶訂用帳戶中建立的資源類型進行限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：執行核准的應用程式清單

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：限制使用者透過腳本與 Azure 資源管理員互動的能力

**指引**：設定 Azure 條件式存取，以限制使用者與 Azure Resource Manager 的互動能力，方法是針對「Microsoft Azure 管理」應用程式設定「封鎖存取」。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

* [Azure API 管理中的角色型存取控制](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行腳本的能力

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：實際或邏輯上隔離高風險應用程式

**指導**方針：不適用;這項建議適用于在 Azure App Service 或計算資源上執行的 web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：使用 Azure 原則來定義和執行 Azure API 管理服務的標準安全性設定。 使用 "ApiManagement" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure API 管理服務的設定。

* [如何查看可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 Azure 原則，為您的 Azure API 管理服務定義及執行標準安全性設定。 使用 "ApiManagement" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure API 管理實例的設定。 使用 Azure 原則 [拒絕] 和 [在不存在時部署]，在您的 Azure 資源上強制執行安全設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導**方針：如果使用自訂的 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos，以安全地儲存和管理您的 Azure API 管理服務設定。

* [如何在 Azure DevOps 中儲存檔案](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 檔](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

* [瞭解 Azure API 管理 DevOps 資源套件](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂的作業系統映射

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導**方針：使用 Azure 原則，為您的 Azure API 管理服務定義及執行標準安全性設定。 使用 "ApiManagement" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure API 管理實例的設定。 使用 Azure 原則 [拒絕] 和 [在不存在時部署]，在您的 Azure 資源上強制執行安全設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統建構管理工具

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：執行 Azure 服務的自動化設定監視

**指引**：使用 Azure Api 管理 DevOps 資源套件來執行 Azure api 管理的設定管理。

此外，使用 Azure 原則來定義和執行 Azure API 管理服務的標準安全性設定。 使用 "ApiManagement" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 Azure API 管理實例的設定。 使用 Azure 原則 [拒絕] 和 [在不存在時部署]，在您的 Azure 資源上強制執行安全設定。

* [瞭解 Azure API 管理 DevOps 資源套件](https://docs.microsoft.com/azure/api-management/api-management-security-controls#configuration-management)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統執行自動化設定監視

**指導**方針：不適用;這項建議適用于計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導**方針：使用 Key Vault 來管理憑證，並將其設定為 autorotate。 如果您使用 Azure Key Vault 來管理自訂網域 SSL 憑證，請確定憑證是以憑證的形式插入 Key Vault，而不是秘密。

* [如何使用 Key Vault 金鑰輪替的指引來設定自訂功能變數名稱](https://docs.microsoft.com/azure/api-management/configure-custom-domain)

**Azure 資訊安全中心監視**：是

**責任**： Microsoft

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：使用 AZURE ACTIVE DIRECTORY （AD）所產生的受控服務識別，讓您的 API 管理實例能夠輕鬆且安全地存取其他受 Azure AD 保護的資源，例如 Azure Key Vault。

* [如何建立 API 管理實例的受控識別](https://docs.microsoft.com/azure/api-management/api-management-howto-use-managed-service-identity)

* [使用受控識別進行驗證的原則](https://docs.microsoft.com/azure/api-management/api-management-authentication-policies#ManagedIdentity)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證暴露

**指導**方針：執行認證掃描器來識別程式碼中的認證。 認證掃描器也鼓勵將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防護](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：不適用;這項建議適用于計算資源。

支援 Azure 服務的基礎主機（例如 Azure API 管理）已啟用 Microsoft 反惡意程式碼，但不會在客戶內容上執行。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：不適用;這項建議適用于設計用來儲存資料的非計算資源。

支援 Azure 服務的基礎主機（例如 Azure API 管理）已啟用 Microsoft 反惡意程式碼，但不會在客戶內容上執行。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確認反惡意程式碼軟體和簽章已更新

**指導**方針：不適用;這項建議適用于設計用來儲存資料的非計算資源。

支援 Azure 服務的基礎主機（例如 Azure API 管理）已啟用 Microsoft 反惡意程式碼，但不會在客戶內容上執行。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保週期性自動備份

**指導**方針：透過 Azure api 管理來發佈和管理您的 api，您將利用您以手動方式設計、執行和管理的容錯和基礎結構功能。 API 管理支援多區域部署，讓資料平面刀槍入到區域失敗，而不會增加任何營運額外負荷。

API 管理的服務備份和還原功能提供必要的建立區塊，以執行嚴重損壞修復策略。 備份和還原作業可以手動或自動執行。

* [如何將 API 管理資料平面部署到多個區域](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

* [如何在 Azure API 管理中使用服務備份和還原來實作災害復原](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [如何呼叫 API 管理備份作業](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/backup)

* [如何呼叫 API 管理還原作業](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份任何客戶管理的金鑰

**指引**： Azure API 管理所提供的備份和還原作業會執行完整的系統備份和還原。

受控識別可用於從 API 管理自訂功能變數名稱的 Azure Key Vault 取得憑證。 備份 Azure Key Vault 中儲存的任何憑證。

* [如何在 Azure API 管理中使用服務備份和還原來實作災害復原](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [如何備份 Azure Key Vault 憑證](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指引**：從備份執行服務的測試還原和憑證，以驗證備份。

* [如何呼叫 API 管理還原作業](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/apimanagementservice/restore)

* [如何還原 Azure Key Vault 憑證](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理金鑰的保護

**指引**： Azure API 管理會將備份寫入客戶擁有的 Azure 儲存體帳戶。 遵循 Azure 儲存體安全性建議來保護您的備份。

* [如何在 Azure API 管理中使用服務備份和還原來實作災害復原](https://docs.microsoft.com/azure/api-management/api-management-howto-disaster-recovery-backup-restore#calling-the-backup-and-restore-operations)

* [Blob 儲存體的安全性建議](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)

啟用 Key Vault 中的虛刪除，以防止金鑰遭到意外或惡意刪除。

* [如何在 Key Vault 中啟用虛刪除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為您的組織建立事件回應指南。 請確定有寫入的事件回應計畫，可定義人員的所有角色，以及從偵測到事件處理/管理的階段，以進行後續事件審查。

* [建立您自己的安全性事件回應程式的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [利用 NIST 的電腦安全性性事件處理指南，協助建立您自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程式

**指導**方針：資訊安全中心指派每個警示的嚴重性，以協助您排定應先調查哪些警示。 嚴重性是根據資訊安全中心在尋找中的信心，或用於發出警示的分析，以及導致警示的活動背後有惡意意圖的信賴等級。

此外，請清楚地標示訂閱（例如， 生產、非生產）：使用標記並建立命名系統，以清楚地識別和分類 Azure 資源，特別是處理敏感性資料的人員。 您必須負責根據發生事件的 Azure 資源和環境的重要性排定警示補救的優先順序。

* [Azure 資訊安全中心的安全性警示](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

* [使用標記來組織 Azure 資源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程式

**指導**方針：執行練習以定期測試系統的事件回應功能，以協助保護您的 Azure 資源。 識別弱式點和間距，並視需要修訂計畫。

* [NIST 的發行指南，適用于 IT 計畫和功能的測試、訓練和練習計畫](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指導**方針：如果 Microsoft 安全性回應中心（MSRC）發現您的資料已由非法或未經授權的合作物件存取，microsoft 將會使用安全性事件連絡人資訊來與您聯繫。 檢查事實後的事件，以確保解決問題。

* [如何設定 Azure 資訊安全中心安全性連絡人](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示納入事件回應系統中

**指引**：使用「連續匯出」功能來匯出您的 Azure 資訊安全中心警示和建議，以協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續的方式來匯出警示和建議。 您可以使用 Azure 資訊安全中心資料連線器，將警示串流至 Azure Sentinel。

* [如何設定連續匯出](https://docs.microsoft.com/azure/security-center/continuous-export)

* [如何將警示串流至 Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議，自動觸發回應。

* [如何設定工作流程自動化和 Logic Apps](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱[安全性控制：滲透測試和 red team 練習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1：進行 Azure 資源的定期滲透測試，並確保在60天內補救所有重大安全性結果

**指引**： *[請遵循 microsoft Engagement 規則，以確保您的滲透測試不會違反 microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [您可以在這裡找到有關 Microsoft 受管理雲端基礎結構、服務和應用程式的 Microsoft 策略與執行的 Red 小組和即時網站滲透測試的詳細資訊](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱[Azure 安全性基準測試](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 深入瞭解[Azure 安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
