---
title: 適用于 Azure 應用程式閘道的 Azure 安全性基準
description: 適用于 Azure 應用程式閘道的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b470fa72518f805c10403931280bdec96bda0fbe
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226534"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>適用于 Azure 應用程式閘道的 Azure 安全性基準

適用于 Azure 應用程式閘道的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](https://docs.microsoft.com/azure/security/benchmarks/overview)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱[安全性控制：網路安全性](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導**方針：確定所有虛擬網路 Azure 應用程式閘道子網部署都有網路安全性群組 (NSG) 套用至應用程式的受信任埠和來源特定的網路存取控制。 Azure 應用程式閘道支援網路安全性群組時，必須遵守一些限制和需求，才能讓您的 NSG 和 Azure 應用程式閘道如預期般運作。

* [瞭解搭配 Azure 應用程式閘道使用 Nsg 的限制和需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導**方針：若為網路安全性群組 (nsg) 與 Azure 應用程式閘道子網相關聯，請啟用 NSG 流量記錄，並將記錄傳送至儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送至 Log Analytics 工作區，並使用流量分析來提供 Azure 雲端中流量的深入解析。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

注意：在某些情況下，與 Azure 應用程式閘道子網相關聯的 NSG 流量記錄不會顯示已允許的流量。 如果您的設定符合下列情況，則在 NSG 流量記錄中就看不到允許的流量：
- 您已部署應用程式閘道 v2
- 您在應用程式閘道子網路上有 NSG
- 您已在該 NSG 上啟用 NSG 流量記錄

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何啟用及使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure 應用程式閘道的診斷和記錄常見問題](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導**方針：將 Azure Web 應用程式防火牆 (WAF) 部署在重要的 Web 應用程式前面，以額外檢查連入流量。 Web 應用程式防火牆 (WAF) 是 Azure 應用程式網) 關的服務 (功能，可為 web 應用程式提供集中式保護，使其免于遭受常見的惡意探索和弱點攻擊。 Azure WAF 可透過檢查輸入的網路流量來防止攻擊，例如 SQL 插入、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊，藉此協助保護您 Azure App Service web 應用程式的安全。 WAF 會根據 OWASP (Open Web Application Security Project) 核心規則集 3.1 (僅限 WAF_v2)、3.0 或 2.2.9 中的規則提供保護。

* [瞭解 Azure 應用程式閘道功能](https://docs.microsoft.com/azure/application-gateway/features)

* [瞭解 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導**方針：在與 Azure 應用程式閘道的生產實例相關聯的 Azure 虛擬網路上啟用 ddos 標準保護，以防範 ddos 攻擊。 使用 Azure 資訊安全中心整合式威脅情報來拒絕與已知惡意 IP 位址的通訊。

* [如何設定 DDoS 保護](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [了解 Azure 資訊安全中心的整合式威脅情報](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導**方針：若為網路安全性群組 (nsg) 與 Azure 應用程式閘道子網相關聯，請啟用 NSG 流量記錄，並將記錄傳送至儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送至 Log Analytics 工作區，並使用流量分析來提供 Azure 雲端中流量的深入解析。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

注意：在某些情況下，與 Azure 應用程式閘道子網相關聯的 NSG 流量記錄不會顯示已允許的流量。 如果您的設定符合下列情況，則在 NSG 流量記錄中就看不到允許的流量：
- 您已部署應用程式閘道 v2
- 您在應用程式閘道子網路上有 NSG
- 您已在該 NSG 上啟用 NSG 流量記錄

* [如何啟用 NSG 流量記錄](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [如何啟用及使用流量分析](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [瞭解 Azure 資訊安全中心所提供的網路安全性](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure 應用程式閘道的診斷和記錄常見問題](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導**方針：將 Azure Web 應用程式防火牆 (WAF) 部署在重要的 Web 應用程式前面，以額外檢查連入流量。 Web 應用程式防火牆 (WAF) 是 Azure 應用程式網) 關的服務 (功能，可為 web 應用程式提供集中式保護，使其免于遭受常見的惡意探索和弱點攻擊。 Azure WAF 可透過檢查輸入的網路流量來防止攻擊，例如 SQL 插入、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊，藉此協助保護您 Azure App Service web 應用程式的安全。 WAF 會根據 OWASP (Open Web Application Security Project) 核心規則集 3.1 (僅限 WAF_v2)、3.0 或 2.2.9 中的規則提供保護。

另外還有多個 marketplace 選項（例如 Barracuda WAF for Azure）可用於 Azure Marketplace，其中包含 IDS/IPS 功能。

* [瞭解 Azure 應用程式閘道功能](https://docs.microsoft.com/azure/application-gateway/features)

* [瞭解 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [瞭解 Barracuda WAF 雲端服務](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導**方針：針對已啟用受信任憑證的 HTTPS/SSL 的 web 應用程式部署 Azure 應用程式閘道。

* [如何部署應用程式閘道](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [如何將應用程式閘道設定為使用 HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [瞭解 Azure web 應用程式閘道的第7層負載平衡](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導**方針：使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如，GatewayManager) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

針對與 Azure 應用程式閘道子網相關聯 (Nsg) 的網路安全性群組，您必須在應用程式閘道 v1 SKU 的 TCP 通訊埠65503-65534 上允許連入的網際網路流量，以及使用目的地子網做為 GatewayManager 服務標籤的任何來源和來源，以取得 v2 SKU 的 TCP 埠65200-65535。 Azure 基礎結構通訊需要此連接埠範圍。 這些埠受到保護， (Azure 憑證) 鎖定。 外部實體（包括這些閘道的客戶）無法在這些端點上進行通訊。

* [瞭解和使用服務標記](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Azure 應用程式閘道設定總覽](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導**方針：定義和實行與 Azure 應用程式閘道部署相關之網路設定的標準安全性設定。 使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure 應用程式閘道、Azure 虛擬網路和網路安全性群組的網路設定進行審核或強制執行。 您也可以利用內建原則定義。

您也可以使用 Azure 藍圖，將金鑰環境成品（例如 Azure Resource Manager 範本、Azure 角色型存取控制 (Azure RBAC) 和原則）封裝在單一藍圖定義中，以簡化大規模的 Azure 部署。 您可以輕鬆地將藍圖套用至新的訂用帳戶、環境，以及透過版本控制來微調控制和管理。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何建立 Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導**方針：將標記用於網路安全性群組 (nsg) 與 Azure 應用程式閘道子網相關聯，以及任何與網路安全性和流量相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [如何建立虛擬網路](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導**方針：使用 Azure 活動記錄監視網路資源設定，以及偵測與您的 Azure 應用程式閘道部署相關之網路設定和資源的變更。 在 Azure 監視器中建立警示，在發生重大網路設定或資源的變更時，將會觸發這些警示。

* [如何檢視及擷取 Azure 活動記錄事件](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [如何在 Azure 監視器中建立警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱[安全性控制：記錄和監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導**方針： Microsoft 會維護 Azure 資源（例如 Azure App Service）所使用的時間來源。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導**方針：若要控制平面審核記錄，請啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶。 您可以使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在 Azure 應用程式閘道的控制平面層級執行，以及用來保護) 閘道子網的相關資源，例如網路安全性群組 (Nsg Azure 應用程式。

除了活動記錄之外，您還可以設定 Azure 應用程式閘道部署的診斷設定。 診斷設定可用來設定將資源串流匯出至您選擇的目的地 (儲存體帳戶、事件中樞和 Log Analytics) 的資源。

Azure 應用程式閘道也提供與 Azure 應用程式 Insights 的內建整合。 Application Insights 會收集記錄檔、效能和錯誤資料。 Application Insights 會自動偵測效能異常，並包含強大的分析工具，可協助您診斷問題，並瞭解您的 web 應用程式的使用方式。 您可以啟用連續匯出，將遙測從 Application Insights 匯出至集中位置，以保留資料超過標準保留期限。

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何啟用 Azure 應用程式閘道的診斷設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何啟用 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [如何設定連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導**方針：若要控制平面審核記錄，請啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶。 您可以使用 Azure 活動記錄資料來判斷任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在 Azure 應用程式閘道的控制平面層級執行，以及用來保護) 閘道子網的相關資源，例如網路安全性群組 (Nsg Azure 應用程式。

除了活動記錄之外，您還可以設定 Azure 應用程式閘道部署的診斷設定。 診斷設定可用來設定將資源串流匯出至您選擇的目的地 (儲存體帳戶、事件中樞和 Log Analytics) 的資源。

Azure 應用程式閘道也提供與 Azure 應用程式 Insights 的內建整合。 Application Insights 會收集記錄檔、效能和錯誤資料。 Application Insights 會自動偵測效能異常，並包含強大的分析工具，可協助您診斷問題，並瞭解您的 web 應用程式的使用方式。 您可以啟用連續匯出，將遙測從 Application Insights 匯出至集中位置，以保留資料超過標準保留期限。

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何啟用 Azure 應用程式閘道的診斷設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何啟用 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [如何設定連續匯出](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留

**指引**：在 Azure 監視器中，根據貴組織的合規性規定來設定您的 Log Analytics 工作區保留期間。 使用 Azure 儲存體帳戶進行長期/封存儲存。

* [如何設定 Log Analytics 工作區的記錄保留期參數](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導**方針：啟用 Azure 活動記錄診斷設定以及 Azure 應用程式閘道的診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢以搜尋字詞、找出趨勢、分析模式，以及提供許多其他以所收集資料為基礎的見解。

針對所有已部署的網路資源（包括您的 Azure 應用程式閘道），使用 Azure 監視器的網路來全面查看健康情況和計量。

(選擇性) 您可啟用 Azure Sentinel 或第三方 SIEM，並且讓資料上線。

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何啟用 Azure 應用程式閘道的診斷設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何使用網路的 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導**方針：在重要的 web 應用程式前面部署 Azure Web 應用程式防火牆 (WAF) v2 SKU，以額外檢查連入流量。 Web 應用程式防火牆 (WAF) 是 Azure 應用程式網) 關的服務 (功能，可為 web 應用程式提供集中式保護，使其免于遭受常見的惡意探索和弱點攻擊。 Azure WAF 可透過檢查輸入的網路流量來防止攻擊，例如 SQL 插入、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊，藉此協助保護您 Azure App Service web 應用程式的安全。 WAF 會根據 OWASP (Open Web Application Security Project) 核心規則集 3.1 (僅限 WAF_v2)、3.0 或 2.2.9 中的規則提供保護。

啟用 Azure 活動記錄診斷設定以及 Azure WAF 的診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢以搜尋字詞、找出趨勢、分析模式，以及提供許多其他以所收集資料為基礎的見解。 您可以根據 Log Analytics 工作區查詢來建立警示。

針對所有已部署的網路資源（包括您的 Azure 應用程式閘道），使用 Azure 監視器的網路來全面查看健康情況和計量。 在 [網路] 主控台的 [Azure 監視器] 中，您可以查看並建立 Azure 應用程式閘道的警示。

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [如何啟用 Azure 活動記錄的診斷設定](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [如何啟用 Azure 應用程式閘道的診斷設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [如何使用網路的 Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

* [如何在 Azure 中建立警示](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導**方針：在重要的 web 應用程式前面部署 Azure Web 應用程式防火牆 (WAF) v2，以額外檢查連入流量。 Web 應用程式防火牆 (WAF) 是 Azure 應用程式網) 關的服務 (功能，可為 web 應用程式提供集中式保護，使其免于遭受常見的惡意探索和弱點攻擊。 Azure WAF 可透過檢查輸入的網路流量來防止攻擊，例如 SQL 插入、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊，藉此協助保護您 Azure App Service web 應用程式的安全。

設定 Azure 應用程式閘道部署的診斷設定。 診斷設定可用來設定將資源串流匯出至您選擇的目的地 (儲存體帳戶、事件中樞和 Log Analytics) 的資源。

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [如何設定 Azure WAF 的診斷設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導**方針：不適用;Azure 應用程式閘道不會處理或產生使用者可存取的 DNS 相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導**方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱[安全性控制：身分識別與存取控制](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導**方針： AZURE ACTIVE DIRECTORY (AD) 具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導**方針： Azure 應用程式閘道的控制平面存取權是透過 AZURE ACTIVE DIRECTORY (AD) 來控制。 Azure AD 沒有預設密碼的概念。

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：
- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

* [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [如何使用 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導**方針：使用 Azure 應用程式註冊 (服務主體) 來取得權杖，此權杖可用來透過 API 呼叫來與您的 Azure 應用程式閘道互動。

* [如何呼叫 Azure REST Api](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [如何使用 Azure AD 向 (服務主體) 註冊用戶端應用程式](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure 復原服務 API 資訊](https://docs.microsoft.com/rest/api/recoveryservices/)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure AD MFA，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [如何在 Azure 資訊安全中心監視身分識別和存取](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指引**：使用已設定 MFA 的特殊權限存取 PAW (特殊權限存取工作站) 登入和設定 Azure 資源。

* [瞭解特殊權限存取工作站](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [如何在 Azure 中啟用 MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導**方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory 的安全性報告來產生記錄和警示。 使用 Azure 資訊安全中心來監視身分識別和存取活動。

* [如何識別已標示為有風險活動的 Azure AD 使用者](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [如何在 Azure 資訊安全中心監視使用者的身分識別和存取活動](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取。

* [如何在 Azure 中設定具名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導**方針：使用 AZURE ACTIVE DIRECTORY (AAD) 作為中央驗證和授權系統。 AAD 會對待用和傳輸中資料使用強式加密以保護資料。 AAD 也會 salts、雜湊並安全地儲存使用者認證。

* [如何建立和設定 AAD 執行個體](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指引**：Azure AD 會提供記錄來協助探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。

* [瞭解 Azure AD 報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [如何使用 Azure 身分識別存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指引**：您可以存取 Azure AD 登入活動、稽核和風險事件記錄檔來源，讓您可以與任何 SIEM/監視工具整合。

您可以建立 Azure Active Directory 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 工作區中設定所需的警示。

* [如何將 Azure 活動記錄整合到 Azure 監視器中](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導**方針：使用 Azure AD Identity Protection 和風險偵測功能，對偵測到與使用者身分識別相關的可疑動作設定自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

* [如何檢視有風險的 Azure AD 登入](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [如何設定和啟用身分識別保護風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [如何使 Azure Sentinel 上線](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**方針：不適用;客戶加密箱不適用於 Azure 應用程式閘道。

* [客戶加密箱支援的服務清單](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱[安全性控制：資料保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指導**方針：使用標籤協助追蹤儲存或處理敏感性資訊的 Azure 資源。

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 確定所有虛擬網路 Azure 應用程式閘道子網部署都有網路安全性群組 (NSG) 套用至應用程式的受信任埠和來源特定的網路存取控制。 Azure 應用程式閘道支援網路安全性群組時，必須遵守一些限制和需求，才能讓您的 NSG 和 Azure 應用程式閘道如預期般運作。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [瞭解搭配 Azure 應用程式閘道使用 Nsg 的限制和需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導**方針：確定所有虛擬網路 Azure 應用程式閘道子網部署都有網路安全性群組 (NSG) 套用至應用程式的受信任埠和來源特定的網路存取控制。 將輸出流量限制為僅限信任的位置，以協助降低資料遭到外泄的威脅。 Azure 應用程式閘道支援網路安全性群組時，必須遵守一些限制和需求，才能讓您的 NSG 和 Azure 應用程式閘道如預期般運作。

* [瞭解搭配 Azure 應用程式閘道使用 Nsg 的限制和需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導**方針：針對您的 Azure 應用程式閘道，使用 TLS 設定端對端加密。

* [如何使用 Azure 應用程式閘道設定端對端 TLS](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**方針：不適用，Azure 應用程式閘道不會儲存待用的客戶資料。

Microsoft 會管理 Azure 應用程式閘道的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

* [瞭解 Azure 中的客戶資料保護](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導**方針：使用 azure 角色型存取控制 (azure RBAC) 來控制 Azure 應用程式閘道控制平面 (Azure 入口網站) 的存取權。

* [如何設定 Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導**方針：不適用;Azure 應用程式閘道不會儲存客戶資料。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導**方針：使用 Azure 監視器搭配 Azure 活動記錄來建立生產 Azure 應用程式閘道實例的變更，以及其他重要或相關資源時的警示。

* [如何建立 Azure 活動記錄事件的警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱[安全性控制：弱點管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導**方針：目前無法使用;Azure 應用程式閘道尚無法使用 Azure 資訊安全中心中的弱點評定。

Microsoft 所掃描和修補的基礎平台。 複習 Azure 應用程式閘道可用的安全性控制，以減少服務設定的相關弱點。

* [功能涵蓋範圍 (包括 Azure PaaS 服務的弱點評定) ](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導**：尚未提供;Azure 應用程式閘道尚無法使用 Azure 資訊安全中心中的弱點評定。

Microsoft 所掃描和修補的基礎平台。 複習 Azure 應用程式閘道可用的安全性控制，以減少服務設定的相關弱點。

* [功能涵蓋範圍 (包括 Azure PaaS 服務的弱點評定) ](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導**：尚未提供;Azure 應用程式閘道尚無法使用 Azure 資訊安全中心中的弱點評定。

Microsoft 所掃描和修補的基礎平台。 複習 Azure 應用程式閘道可用的安全性控制，以減少服務設定的相關弱點。

* [功能涵蓋範圍 (包括 Azure PaaS 服務的弱點評定) ](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure 資訊安全中心監視**：目前無法使用

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

此外，使用下列內建原則定義，利用 Azure 原則對可在客戶訂閱中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導**方針：定義核准的 Azure 資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導**方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。 確保已核准環境中的所有 Azure 資源。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure Graph 建立查詢](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：
- 不允許的資源類型
- 允許的資源類型

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [如何使用 Azure 原則拒絕特定的資源類型](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

* [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 確定所有虛擬網路 Azure 應用程式閘道子網部署都有網路安全性群組 (NSG) 套用至應用程式的受信任埠和來源特定的網路存取控制。 Azure 應用程式閘道支援網路安全性群組時，必須遵守一些限制和需求，才能讓您的 NSG 和 Azure 應用程式閘道如預期般運作。

* [如何建立額外的 Azure 訂閱](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [如何建立管理群組](https://docs.microsoft.com/azure/governance/management-groups/create)

* [如何建立和使用標籤](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [瞭解搭配 Azure 應用程式閘道使用 Nsg 的限制和需求](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [如何建立具有安全性設定的 NSG](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱[安全性控制：安全設定](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導**方針：定義和實行與 Azure 應用程式閘道部署相關之網路設定的標準安全性設定。 使用 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure 應用程式閘道、Azure 虛擬網路和網路安全性群組的網路設定進行審核或強制執行。 您也可以利用內建原則定義。

* [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導**方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [瞭解 Azure 原則效果](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指引**：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存及管理您的程式碼。

* [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導**方針：使用內建的 Azure 原則定義以及 "Microsoft. Network" 命名空間中 Azure 原則的別名來建立自訂原則，以警示、審核和強制執行系統組態。 此外，開發流程和管線以管理原則例外狀況。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導**方針：使用內建的 Azure 原則定義以及 "Microsoft. Network" 命名空間中 Azure 原則的別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 資源的設定。

* [如何設定和管理 Azure 原則](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導**方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導**方針：使用受控識別，在 AZURE ACTIVE DIRECTORY (AD) 中，為您的 Azure 應用程式閘道提供自動管理的身分識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

使用 Azure Key Vault 來安全地儲存憑證。 Azure Key Vault 是平臺管理的秘密存放區，您可以用來保護秘密、金鑰和 SSL 憑證。 Azure 應用程式閘道支援與 Key Vault 整合，以使用連結到 HTTPS 所支援接聽程式的伺服器憑證。 此支援僅限於應用程式閘道 v2 SKU。

* [如何使用 Azure PowerShell 以 Key Vault 憑證設定 SSL 終止](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導**方針：使用受控識別，在 AZURE ACTIVE DIRECTORY (AD) 中，為您的 Azure 應用程式閘道提供自動管理的身分識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

使用 Azure Key Vault 來安全地儲存憑證。 Azure Key Vault 是平臺管理的秘密存放區，您可以用來保護秘密、金鑰和 SSL 憑證。 Azure 應用程式閘道支援與 Key Vault 整合，以使用連結到 HTTPS 所支援接聽程式的伺服器憑證。 此支援僅限於應用程式閘道 v2 SKU。

* [如何使用 Azure PowerShell 以 Key Vault 憑證設定 SSL 終止](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

* [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱[安全性控制：惡意程式碼防禦](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導**方針：在重要的 web 應用程式前面部署 Azure Web 應用程式防火牆 (WAF) v2，以額外檢查連入流量。 Web 應用程式防火牆 (WAF) 是 Azure 應用程式網) 關的服務 (功能，可為 web 應用程式提供集中式保護，使其免于遭受常見的惡意探索和弱點攻擊。 Azure WAF 可透過檢查輸入的網路流量來防止攻擊，例如 SQL 插入、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊，藉此協助保護您 Azure App Service web 應用程式的安全。

設定 Azure 應用程式閘道部署的診斷設定。 診斷設定可用來設定將資源串流匯出至您選擇的目的地 (儲存體帳戶、事件中樞和 Log Analytics) 的資源。

* [如何部署 Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [如何設定 Azure WAF 的診斷設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導**方針：不適用;Azure 應用程式閘道不會儲存客戶資料。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指導**方針：使用 Azure Web 應用程式防火牆 (WAF) 時，您可以設定 WAF 原則。 WAF 原則包含兩種類型的安全性規則：客戶所撰寫的自訂規則，以及屬於 Azure 管理之預先設定規則集合的受控規則集。 Azure 管理的規則集可讓您以簡單的方式部署防護，以抵禦一組常見的安全性威脅。 由於這類規則集是由 Azure 所管理，因此會視需要更新規則，以防止新的攻擊簽章。

* [瞭解 Azure 管理的 WAF 規則集](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱[安全性控制：資料復原](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導**方針： Azure 應用程式閘道不會儲存客戶資料。 不過，如果使用自訂的 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

Azure DevOps Services 會利用許多 Azure 儲存體功能，以確保在發生硬體故障、服務中斷或區域嚴重損壞的情況下，可進行資料的可用性。 此外，Azure DevOps 團隊會遵循程式來防止資料遭到意外或惡意刪除。

* [瞭解 Azure DevOps 中的資料可用性](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指導**方針： Azure Key Vault 內備份客戶管理的憑證。

* [如何在 Azure 中備份金鑰保存庫憑證](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導**方針：測試已備份之客戶管理憑證的還原。

* [如何還原金鑰保存庫憑證](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導**方針：確定已啟用 Azure Key Vault 的虛刪除。 虛刪除可讓您復原已刪除的金鑰保存庫和保存庫物件，例如金鑰、秘密和憑證。

* [如何使用 Azure Key Vault 的虛刪除](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱[安全性控制：事件回應](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

* [如何設定 Azure 資訊安全中心內的工作流程自動化](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導**方針： 

* [遵循 Microsoft 的參與規則，以確保您的滲透測試不違反 Microsoft 原則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
