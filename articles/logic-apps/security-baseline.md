---
title: 適用于 Logic Apps 的 Azure 安全性基準
description: Logic Apps 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: e47dfeab7466faf9a60b161b05d8b3bf39ebdb6a
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98203298"
---
# <a name="azure-security-baseline-for-logic-apps"></a>適用于 Logic Apps 的 Azure 安全性基準

適用于 Logic Apps 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md) (機器翻譯)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：在「全域」、多租使用者 Logic Apps 服務中執行的連接器是由 Microsoft 所部署和管理。 這些連接器會提供觸發程式和動作，以存取雲端服務、內部部署系統（或兩者），包括 Office 365、Azure Blob 儲存體、SQL Server、Dynamics、Salesforce、SharePoint 等等。 在您的網路安全性群組或 Azure 防火牆上指定規則，以允許存取相關資源時，您可以使用 AzureConnectors 服務標記。

對於需要直接存取 Azure 虛擬網路中的資源的邏輯應用程式，您可以建立 (ISE) 的整合服務環境，以便在專用資源上建立、部署和執行邏輯應用程式。 某些 Azure 虛擬網路會使用私人端點 (Azure Private Link) 來提供 Azure PaaS 服務的存取權，例如 Azure 儲存體、Azure Cosmos DB、Azure SQL Database、合作夥伴服務，或裝載于 Azure 上的客戶服務。 如果您的邏輯應用程式需要存取使用私人端點的虛擬網路，您必須在 ISE 內建立、部署和執行這些邏輯應用程式。

當您建立 ISE 時，可以選擇使用內部或外部存取端點。 您的選擇會決定您 ISE 中的邏輯應用程式上的要求或 webhook 觸發程式是否可以接收來自虛擬網路外部的呼叫。 內部和外部存取端點也會影響您是否可以從虛擬網路內部或外部，查看邏輯應用程式的執行歷程記錄，包括執行的輸入和輸出。

請確定與您 ISE 相關的所有虛擬網路子網部署都已套用網路安全性群組，且該網路存取控制適用于您應用程式的受信任埠和來源。 當您在 ISE 中部署邏輯應用程式時，請使用 Private Link。 Azure Private Link 可讓您透過虛擬網路中的私人端點，存取 Azure PaaS 服務和 Azure 裝載的客戶擁有/合作夥伴服務。 或者，如果您有特定的使用案例，您可以藉由執行 Azure 防火牆來滿足這項需求。 若要在設定安全性規則時降低複雜性，請使用代表特定 Azure 服務 IP 位址首碼群組的服務標記。

- [瞭解 Logic Apps 的連接器](../connectors/apis-list.md)

- [瞭解 Azure 中的服務標記](../virtual-network/service-tags-overview.md)

- [瞭解如何使用整合服務環境 (Ise 從 Azure Logic Apps 存取 Azure 虛擬網路資源) ](connect-virtual-network-vnet-isolated-environment-overview.md)

- [瞭解虛擬網路服務端點](../virtual-network/virtual-network-service-endpoints-overview.md)

- [瞭解 Azure Private Link](../private-link/private-link-overview.md)

- [瞭解 ISE 端點存取](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

- [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何部署和設定 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

- [如何啟用 ISE 的存取權](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導** 方針：如果您在整合服務環境中執行邏輯應用程式， (ISE) 使用外部存取點，您可以使用網路安全性群組 (NSG) ，以降低資料遭到外泄的風險。 啟用 NSG 流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送至 Log Analytics 工作區，並使用流量分析來提供 Azure 雲端中流量的深入解析。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

- [瞭解 ISE 端點存取](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指引**：不適用；這項建議適用於在 Azure App Service 或計算資源上執行的 Web 應用程式。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：如果您的邏輯應用程式使用以要求為基礎的觸發程式，而該觸發程式會接收傳入的呼叫或要求，例如要求或 Webhook 觸發程式，您可以限制存取，讓只有經過授權的用戶端可以呼叫您的邏輯應用程式。

如果您在整合服務環境中執行 (ISE) 的邏輯應用程式，請在與 ISE 相關聯的虛擬網路上啟用 DDoS 保護標準，以防止 DDoS 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報，以拒絕與已知為惡意或未使用的網際網路 IP 位址通訊。

在每個組織的網路界限上部署 Azure 防火牆，並啟用威脅情報並設定為惡意網路流量的「警示和拒絕」。

使用 Azure 資訊安全中心的即時網路存取來設定 Nsg，將端點的公開限制在有限的期間內。

使用 Azure 資訊安全中心調適型網路強化，根據實際的流量和威脅情報，建議可限制埠和來源 Ip 的 NSG 設定。

- [如何保護 Logic Apps 的輸入呼叫](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [如何限制輸入 IP 位址](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [如何設定 DDoS 保護](../ddos-protection/manage-ddos-protection.md)

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

- [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/azure-defender.md)

- [瞭解 Azure 資訊安全中心適應性網路強化](../security-center/security-center-adaptive-network-hardening.md)

- [瞭解 Azure 資訊安全中心及時的網路存取控制](../security-center/security-center-just-in-time.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：如果您要在整合服務環境中執行邏輯應用程式， (ISE) 使用外部存取點，您可以使用網路安全性群組 (NSG) ，以降低資料遭到外泄的風險。 啟用 NSG 流量記錄，並將記錄傳送至 Azure 儲存體帳戶以進行流量審核。 您也可以將 NSG 流量記錄傳送至 Log Analytics 工作區，並使用流量分析來提供 Azure 雲端中流量的深入解析。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

若要提供網路流量的進一步保護和資訊，您可以參考存取記錄，只有當您已在每個應用程式閘道實例上啟用它們時，才會產生這些記錄。 您可以使用此記錄來檢視應用程式閘道存取模式並分析重要資訊。 這包含呼叫者的 IP、所要求的 URL、回應延遲、傳回碼，以及輸入和輸出位元組。

否則，您可以利用 marketplace 中的協力廠商解決方案來滿足這項需求。

- [瞭解 ISE 端點存取](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

- [如何整合內部 VNET 中的 API 管理與應用程式閘道](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [如何瞭解 WAF 存取記錄](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針：從 Azure Marketplace 選取供應專案，以支援具有承載檢查功能的 IDS/IPS 功能。  如果基於酬載檢查的入侵偵測和/或預防不是必要條件，則可以使用具有威脅情報的 Azure 防火牆。 Azure 防火牆威脅情報型篩選可以警示並拒絕傳向和來自已知惡意 IP 位址和網域的流量。 IP 位址和網域來自 Microsoft 威脅情報摘要。

在每個組織的網路界限上部署您所選擇的防火牆解決方案，以偵測及/或拒絕惡意流量。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

- [如何使用 Azure 防火牆設定警示](../firewall/threat-intel.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：如果您要在整合服務環境中執行邏輯應用程式 (ISE) ，請部署 Azure 應用程式閘道。

- [如何整合內部 VNET 中的 API 管理與應用程式閘道](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [如何將應用程式閘道設定為使用 HTTPS](../application-gateway/create-ssl-portal.md) 

- [瞭解 Azure web 應用程式閘道的第7層負載平衡](../application-gateway/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針：對於需要存取 Azure Logic Apps 實例的資源，請使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如 LogicApps、LogicAppsManagement) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [如需使用服務標記的詳細資訊](../virtual-network/service-tags-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：使用 Azure 原則來定義和執行與您的 Azure Logic Apps 實例相關之網路資源的標準安全性設定。 使用 "Microsoft. 邏輯" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure 邏輯應用程式實例的網路設定進行審核或強制執行。 您也可以利用內建的原則定義，例如：

- 應在 Logic Apps 中啟用診斷記錄

- 應啟用 DDoS 保護標準

您也可以使用 Azure 藍圖，藉由在單一藍圖定義中封裝關鍵環境成品（例如 Azure Resource Manager 範本、Azure 角色型存取控制 (Azure RBAC) 和原則）來簡化大規模的 Azure 部署。 輕鬆地將藍圖套用至新的訂閱、環境，以及透過版本控制來微調控制和管理。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對個別的 NSG 規則，請使用 [描述] 欄位來指定商務需求和/或持續時間 (等，以針對允許流量進出網路的任何規則 ) 。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可以使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

- [如何建立虛擬網路](../virtual-network/quick-create-portal.md)

- [如何建立具有安全性設定的 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [Logic Apps 的 Azure 原則定義清單](./policy-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄來監視網路資源設定，並偵測與 Azure Logic Apps 實例相關之網路資源的變更。 在 Azure 監視器中建立警示，以在重要網路資源發生變更時觸發。

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的 Azure Logic Apps。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：若要在執行時間期間取得邏輯應用程式的更豐富的偵錯工具，您可以設定和使用 Azure 監視器記錄檔，以記錄和儲存有關執行時間資料和事件的資訊，例如觸發程式事件、執行事件，以及 Log Analytics 工作區中的動作事件。 Azure 監視器可協助監視您的雲端和內部部署環境，以維護其可用性和效能。 藉由使用 Azure 監視器記錄檔，您可以建立記錄查詢，以協助您收集和檢查這項資訊。 您也可以將此診斷資料與其他 Azure 服務搭配使用，例如 Azure 儲存體和 Azure 事件中樞。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何設定 Azure 監視器記錄，並收集 Azure Logic Apps 的診斷資料](monitor-logic-apps-log-analytics.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md) 

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：若要在執行時間期間取得邏輯應用程式的更豐富的偵錯工具，您可以設定和使用 Azure 監視器記錄檔，以記錄和儲存有關執行時間資料和事件的資訊，例如觸發程式事件、執行事件，以及 Log Analytics 工作區中的動作事件。 Azure 監視器可協助監視您的雲端和內部部署環境，以維護其可用性和效能。 藉由使用 Azure 監視器記錄檔，您可以建立記錄查詢，以協助您收集和檢查這項資訊。 您也可以將此診斷資料與其他 Azure 服務搭配使用，例如 Azure 儲存體和 Azure 事件中樞。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何設定 Azure 監視器記錄，並收集 Azure Logic Apps 的診斷資料](monitor-logic-apps-log-analytics.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md) 

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：建立並執行邏輯應用程式之後，您可以檢查邏輯應用程式的執行狀態、執行歷程記錄、觸發程式歷程記錄和效能。 針對即時事件監視和更豐富的偵錯工具，請使用 Azure 監視器記錄檔來設定邏輯應用程式的診斷記錄。 此 Azure 服務可協助您監視您的雲端和內部部署環境，讓您可以更輕鬆地維護其可用性和效能。 然後，您可以尋找和查看事件，例如觸發程式事件、執行事件和動作事件。 藉由將此資訊儲存在 Azure 監視器記錄檔中，您可以建立記錄查詢，以協助您尋找並分析此資訊。 您也可以將此診斷資料與其他 Azure 服務搭配使用，例如 Azure 儲存體和 Azure 事件中樞。

在 Azure 監視器中，根據您組織的合規性法規，設定與您 Azure Logic Apps 實例相關聯的記錄檔保留期限。

- [如何監視執行狀態、檢查觸發程式記錄，以及設定 Azure Logic Apps 的警示](monitor-logic-apps.md)

- [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** 方針：若要設定邏輯應用程式的記錄，您可以在建立邏輯應用程式時啟用 log analytics，也可以在 log Analytics 工作區中安裝現有邏輯應用程式的 Logic Apps 管理解決方案。 此解決方案會提供邏輯應用程式執行的匯總資訊，並且包含狀態、執行時間、重新提交狀態和相互關聯識別碼等特定詳細資料。 然後，若要為此資訊啟用記錄和建立查詢，請設定 Azure 監視器記錄檔。

您也可以啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢，以搜尋詞彙、識別趨勢、分析模式，以及根據可能已針對 Azure Logic Apps 收集的活動記錄資料，提供許多其他見解。

或者，您也可啟用 Azure Sentinel 或第三方 SIEM，並讓資料上線。 

- [如何設定 Azure 監視器記錄，並收集 Azure Logic Apps 的診斷資料](monitor-logic-apps-log-analytics.md)

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何在 Azure 監視器中收集和分析 Log Analytics 中的 Azure 活動記錄](../azure-monitor/platform/activity-log.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md) 

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：使用 Azure 資訊安全中心與 Log Analytics 進行監視，並針對在安全性記錄檔和事件中找到的異常活動發出警示。

或者，您也可以啟用和麵板上的資料以 Azure Sentinel。

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

- [如何在 Azure 資訊安全中心中管理警示](../security-center/security-center-managing-and-responding-alerts.md)

- [如何對 log analytics 記錄資料發出警示](../azure-monitor/learn/tutorial-response.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用;Azure Logic Apps 不會處理或產生反惡意程式碼的相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用;Azure Logic Apps 不會處理或產生 DNS 相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： AZURE ACTIVE DIRECTORY (AD) 具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

若要輕鬆存取 Azure Active Directory (Azure AD) 保護的其他資源並驗證您的身分識別而不登入，您的邏輯應用程式可以使用受控識別 (原為「受控服務識別」或 MSI)，而不使用認證或祕密。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。

邏輯應用程式上的每個要求端點都具有共用存取簽章， (SAS) 端點的 URL 中。 對於以要求為基礎的觸發程序，如果您要將端點 URL 分享給其他人，您可以產生使用特定金鑰且有到期日的回呼 URL。 如此一來，您可以根據特定的時間範圍，無縫輪替金鑰，或限制有無存取權來觸發邏輯應用程式。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [使用 Azure Logic Apps 中的受控識別驗證及存取 Azure 資源](create-managed-service-identity.md)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [如何使用 SAS 保護 Azure Logic Apps 中的存取和資料](logic-apps-securing-a-logic-app.md#sas)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure Active Directory 和 Azure Logic Apps 沒有預設密碼的概念。

如果正在使用基本驗證，您將需要指定使用者名稱和密碼。 建立這些認證時，請務必設定強式密碼進行驗證。

如果您使用基礎結構即程式碼，請避免在程式碼中儲存密碼，而改為使用 Azure Key Vault 儲存和取出認證。

- [如何保護及存取 Logic Apps 中的資料](logic-apps-securing-a-logic-app.md)

- [如何從 Azure Key Vault 設定及取出秘密](../key-vault/general/quick-create-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專用系統管理帳戶，您可使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

- [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](../security-center/security-center-identity-access.md)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導** 方針：使用 Azure 應用程式註冊 (服務主體) 來取得權杖，此權杖可用來透過 API 呼叫來與您的復原服務保存庫互動。

許多連接器也會要求您先建立與目標服務或系統的連線，並提供驗證認證或其他設定詳細資料，您才能在邏輯應用程式中使用觸發程式或動作。 例如，您必須授權 Twitter 帳戶的連線，以存取資料或代表您張貼。

針對使用 Azure Active Directory (Azure AD) OAuth 的連接器，建立連線表示您的存取權杖會加密並安全地儲存在 Azure 秘密存放區中的服務，例如 Office 365、Salesforce 或 GitHub。 其他連接器（例如 FTP 和 SQL）需要具有設定詳細資料的連接，例如伺服器位址、使用者名稱和密碼。 這些連線的組態詳細資料也會加密並安全地儲存。

- [如何呼叫 Azure REST Api](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [如何向 Azure AD 註冊用戶端應用程式](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [工作流程觸發 API 資訊](/rest/api/logic/workflowtriggers)

- [瞭解連接器設定](../connectors/apis-list.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指引**：啟用 Azure Active Directory (AD) 多重要素驗證 (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指引**：使用已設定多重要素驗證 (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

- [了解特殊權限存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 風險偵測](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取 Azure 入口網站。

此外，邏輯應用程式上的每個要求端點都具有共用存取簽章 (SAS) 在端點的 URL 中。 您可以限制邏輯應用程式只接受來自特定 IP 位址的要求。

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [瞭解如何限制 Logic Apps 中的輸入 IP 位址](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為 Azure Logic Apps 實例的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

在 Logic Apps 支援的情況下，請使用受控識別輕鬆地存取受 Azure Active Directory (Azure AD) 的其他資源，並在不登入的情況下驗證您的身分識別，而不是認證或秘密。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。

Azure Logic Apps 支援系統指派的和使用者指派的受控識別。 您的邏輯應用程式可以使用系統指派的身分識別，或單一使用者指派的身分識別，您可以在一組邏輯應用程式之間共用，但無法同時使用兩者。 目前，只有特定的內建觸發和動作支援受控識別，而非受控的連接器或連線，例如：

-  HTTP

-  Azure Functions

-  Azure API 管理

-  Azure App Service 

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [使用 Azure Logic Apps 中的受控識別驗證及存取 Azure 資源](create-managed-service-identity.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針： AZURE ACTIVE DIRECTORY (AD) 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。 

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為 Azure Logic Apps 實例的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以與 Azure Sentinel 或協力廠商 SIEM 整合。

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 中設定所需的記錄警示。

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指引**：使用 Azure AD 風險和身分識別保護功能設定自動回應，以偵測與使用者身分識別相關的可疑動作。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。 

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md) 

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針：目前無法使用;Azure Logic Apps 尚不支援客戶加密箱。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：在「全域」、多租使用者 Logic Apps 服務中執行的連接器是由 Microsoft 所部署和管理。 這些連接器會提供觸發程式和動作，以存取雲端服務、內部部署系統（或兩者），包括 Office 365、Azure Blob 儲存體、SQL Server、Dynamics、Salesforce、SharePoint 等等。

對於需要直接存取 Azure 虛擬網路中的資源的邏輯應用程式，您可以建立 (ISE) 的整合服務環境，以便在專用資源上建立、部署和執行邏輯應用程式。 某些 Azure 虛擬網路會使用私人端點 (Azure Private Link) 來提供 Azure PaaS 服務的存取權，例如 Azure 儲存體、Azure Cosmos DB 或 Azure SQL Database、合作夥伴服務，或裝載在 Azure 上的客戶服務。 如果您的邏輯應用程式需要存取使用私人端點的虛擬網路，您必須在 ISE 內建立、部署和執行這些邏輯應用程式。

當您建立 ISE 時，可以選擇使用內部或外部存取端點。 您的選擇會決定您 ISE 中的邏輯應用程式上的要求或 webhook 觸發程式是否可以接收來自虛擬網路外部的呼叫。

此外，針對個別的安全性網域（例如環境類型和資料敏感度層級），使用個別的訂用帳戶和管理群組來執行隔離。 您可以限制您的應用程式和企業環境所需的 Azure 資源存取層級。 您可以透過 Azure 角色型存取控制 (Azure RBAC) 來控制 Azure 資源的存取權。

- [瞭解 Logic Apps 的連接器](../connectors/apis-list.md)

- [透過整合服務環境 (ISE) 從 Azure Logic Apps 存取 Azure 虛擬網路資源](connect-virtual-network-vnet-isolated-environment-overview.md)

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md) 

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md) 

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：目前無法使用;Azure Logic Apps 尚無法使用資料識別、分類和遺失防護功能。

利用來自網路周邊 Azure Marketplace 的協力廠商解決方案，以監視是否有未經授權的機密資訊傳輸並封鎖這類傳輸，同時警示資訊安全專業人員。 

Microsoft 管理 Azure Logic Apps 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：加密傳輸中的所有機密資訊。 在 Azure Logic Apps 中，邏輯應用程式執行期間的所有資料在傳輸期間會使用傳輸層安全性 (TLS) 和待用進行加密。 當您檢視邏輯應用程式的執行歷程記錄時，Logic Apps 會驗證您的存取，然後針對每次執行的要求和回應，提供連結指向輸入和輸出。 不過，如果動作處理任何密碼、秘密、金鑰或其他敏感性資訊，建議您防止其他人檢視和存取該資料。 例如，如果邏輯應用程式從 Azure Key Vault 取得祕密來用於驗證 HTTP 動作，建議您隱藏該秘密。

要求觸發程式僅支援針對輸入要求 (TLS) 1.2 的傳輸層安全性。 請確定任何連線至 Azure 資源的用戶端都能夠協商 TLS 1.2 或更新版本。 使用 HTTP 連接器的輸出呼叫支援 (TLS) 1.0、1.1 和1.2 的傳輸層安全性。 

遵循 Azure 資訊安全中心待用加密及傳輸中加密的建議（適用時）。

- [安全存取和資料 Azure Logic Apps-對要求型觸發程式的撥入電話](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [安全存取和資料 Azure Logic Apps 對其他服務和系統的輸出呼叫](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [瞭解 Azure 中的傳輸加密](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [瞭解 Azure 中的待用資料加密](../security/fundamentals/encryption-atrest.md)

- [設定客戶管理的金鑰，以在 Azure Logic Apps 中 (Ise) 為整合服務環境加密待用資料](customer-managed-keys-integration-service-environment.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：在 Azure Logic Apps 中，許多觸發程式和動作都有設定，可讓您透過遮蔽邏輯應用程式執行歷程記錄中的資料來保護輸入、輸出或兩者。

Microsoft 管理 Azure Logic Apps 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [安全存取以執行歷程記錄資料](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：您只能允許特定的使用者或群組執行特定的工作，例如管理、編輯和觀看邏輯應用程式。 若要控制其許可權，請使用 Azure 角色型存取控制 (Azure RBAC) ，讓您可以將自訂或內建角色指派給 Azure 訂用帳戶中的成員：

- 邏輯應用程式參與者：可讓您管理邏輯應用程式，但無法變更對邏輯應用程式的存取。
- 邏輯應用程式操作員：可讓您讀取、啟用及停用邏輯應用程式，但無法編輯或更新邏輯應用程式。

若要防止變更或刪除邏輯應用程式，您可以使用 Azure 資源鎖定。 這項功能可防止其他人變更或刪除生產資源。

- [安全存取 Azure Logic Apps 作業](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指引**：不適用，這項建議主要用於計算資源。 Microsoft 管理 Azure Logic Apps 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [Azure 客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針： Azure Logic Apps 依賴 Azure 儲存體來儲存和自動加密待用資料。 此加密可保護您的資料，並協助您符合組織的安全性和合規性承諾。 根據預設，Azure 儲存體會使用 Microsoft 管理的金鑰來加密您的資料。

當您建立整合服務環境 (ISE) 來裝載您的邏輯應用程式，而且您想要更充分掌控 Azure 儲存體所使用的加密金鑰時，可以使用 Azure Key Vault 來設定、使用和管理您自己的金鑰。 這項功能也稱為「攜帶您自己的金鑰」 (BYOK) ，而您的金鑰稱為「客戶管理的金鑰」。

- [Azure Logic Apps 中的整合服務環境加密待用資料](customer-managed-keys-integration-service-environment.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄，以建立 Azure Logic Apps 中發生變更的時間，以及其他重要或相關資源的警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：不適用;Microsoft 會在支援 Azure Logic Apps 的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針：不適用;Microsoft 會在支援 Azure Logic Apps 的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：不適用;Microsoft 會在支援 Azure Logic Apps 的基礎系統上執行弱點管理。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可以透過 Resource Graph 探索傳統的 Azure 資源，但強烈建議您建立並使用 Azure Resource Manager 的資源。

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指引**：將標籤套用至提供中繼資料的 Azure 資源，以邏輯方式依分類組織這些資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指引**：在適當的情況下，使用標籤、管理群組和個別訂閱來組織及追蹤 Azure 資源。 請定期調節清查，並確保會及時刪除訂閱中未經授權的資源。

此外，您也可以使用下列內建原則定義，使用 Azure 原則對可在客戶訂用帳戶 () s 中建立的資源類型進行限制：

- 不允許的資源類型
- 允許的資源類型

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據您的組織需求，建立核准的 Azure 資源清查 (例如連接器) 和已核准的計算資源軟體。

注意：由於 Google 的資料和隱私權原則，您只能使用 Gmail 連接器搭配 Google 核准的服務。 這種情況不斷演進，未來可能會影響其他 Google 連接器。

- [所有 Logic Apps 連接器的清單](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [瞭解 Gmail 連接器的問題和限制](/connectors/gmail/#known-issues-and-limitations)

- [Google 隱私權原則的詳細資訊](../connectors/connectors-google-data-security-privacy-policy.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。 

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。  確保已核准環境中的所有 Azure 資源。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指引**：使用下列內建原則定義，以利用 Azure 原則對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指引**：不適用，這項建議主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：與您的 Logic Apps 所需的資源相關的資源，但可能會對組織產生更高的風險，應該隔離在其自己的虛擬機器和/或虛擬網路內，並使用 Azure 防火牆或網路安全性群組來充分保護。

針對商務營運所需的 Logic Apps，但可能會對組織產生更高的風險，應該在可能的情況下，透過具有特定許可權的個別資源群組和 Azure RBAC 界限，盡可能隔離。

- [如何建立虛擬網路](../virtual-network/quick-create-portal.md) 

- [如何使用安全性設定建立 NSG](../virtual-network/tutorial-filter-network-traffic.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md) 

- [如何透過 Azure RBAC 安全地存取 Logic Apps](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則定義和執行 Azure Logic Apps 實例的標準安全性設定。 使用 "Microsoft. 邏輯" 命名空間中 Azure 原則別名來建立自訂原則，以對 Logic Apps 實例的設定進行審核或強制執行。 例如，您可以封鎖其他人建立或使用您想要限制存取之資源的連接。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合/超過組織的安全性需求。

此外，也請使用安全的參數來保護機密資料和秘密。

- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [封鎖連接器在 Azure Logic Apps 中建立的連接](block-connections-connectors.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [如何部署 Azure Logic Apps 的 Azure Resource Manager 範本](logic-apps-deploy-azure-resource-manager-templates.md)

- [瞭解安全動作參數](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指引**：使用 Azure 原則 [拒絕] 和 [在不存在時部署]，以對 Azure 資源強制使用安全設定。

使用 Azure 原則定義和執行 Azure Logic Apps 實例的標準安全性設定。 使用 "Microsoft. 邏輯" 命名空間中 Azure 原則別名來建立自訂原則，以對 Logic Apps 實例的設定進行審核或強制執行。 例如，您可以封鎖其他人建立或使用您想要限制存取之資源的連接。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合/超過組織的安全性需求。

此外，請務必使用模糊化來保護執行歷程記錄中的資料。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

- [封鎖連接器在 Azure Logic Apps 中建立的連接](block-connections-connectors.md)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

- [如何部署 Azure Logic Apps 的 Azure Resource Manager 範本](logic-apps-deploy-azure-resource-manager-templates.md)

- [安全存取執行歷程記錄輸入和輸出](logic-apps-securing-a-logic-app.md#obfuscate)

- [安全存取參數輸入](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：如果使用自訂 Azure 原則定義，請使用 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

此外，Azure Resource Manager 能夠在 JavaScript 物件標記法 (的 JSON) 中匯出範本，這應該經過檢查以確保設定符合/超過組織的安全性需求。

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops)

- [Azure 入口網站中的單一和多重資源匯出至範本](../azure-resource-manager/templates/export-template-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用內建的 Azure 原則定義以及 "Microsoft. 邏輯" 命名空間中 Azure 原則的別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則別名來建立自訂原則，以對 Azure 資源的網路設定進行審核或強制執行。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用內建的 Azure 原則定義以及 "Microsoft. 邏輯" 命名空間中 Azure 原則的別名來建立自訂原則，以警示、審核和強制執行系統組態。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：使用模糊化來保護邏輯應用程式執行歷程記錄中的輸入和輸出。 如果您在不同的環境中部署，請考慮將邏輯應用程式工作流程定義中的值參數化，而這些值會根據這些環境而有所不同。 如此一來，您可以使用 Azure Resource Manager 範本部署邏輯應用程式以避免硬式編碼的資料、定義受保護的參數來保護敏感性資料，以及使用參數檔案來透過範本的參數，將該資料當作個別輸入來傳遞。 您可以使用 Key Vault 儲存機密資料，並使用安全的範本參數，在部署時從 Key Vault 中取出這些值。 然後，您可以在參數檔案中參考金鑰保存庫和秘密。 

當您建立整合服務環境 (ISE) 來裝載您的邏輯應用程式，而且您想要更充分掌控 Azure 儲存體所使用的加密金鑰時，可以使用 Azure Key Vault 來設定、使用和管理您自己的金鑰。 這項功能也稱為「攜帶您自己的金鑰」 (BYOK) ，而您的金鑰稱為「客戶管理的金鑰」。

- [在 Azure Logic Apps 中保護執行歷程記錄中的輸入和輸出](logic-apps-securing-a-logic-app.md#obfuscate)

- [參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [安全存取 Azure Logic Apps 中的參數輸入](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [使用 Azure Key Vault 在部署期間傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)

- [設定客戶管理的金鑰，以在 Azure Logic Apps 中 (Ise) 為整合服務環境加密待用資料](customer-managed-keys-integration-service-environment.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：若要輕鬆地存取 Azure Active Directory (所保護的其他資源 Azure AD) 並驗證您的身分識別，而不登入，您的邏輯應用程式可以使用受控識別 (先前受控的服務身分識別或 MSI) ，而不是認證或秘密。 Azure 會為您管理此身分識別，並協助保護您的認證，因為您不需要提供或輪替使用祕密。

目前，只有特定的內建觸發和動作支援受控識別，而非受控的連接器或連線，例如：

- HTTP
- Azure Functions
- Azure API 管理
- Azure App Service

- [如何使用 Azure Logic Apps 中的受控識別來驗證 Azure 資源的存取權](create-managed-service-identity.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導** 方針：使用模糊化來保護邏輯應用程式執行歷程記錄中的輸入和輸出。 如果您在不同的環境中部署，請考慮將邏輯應用程式工作流程定義中的值參數化，而這些值會根據這些環境而有所不同。 如此一來，您可以使用 Azure Resource Manager 範本部署邏輯應用程式以避免硬式編碼的資料、定義受保護的參數來保護敏感性資料，以及使用參數檔案來透過範本的參數，將該資料當作個別輸入來傳遞。 您可以使用 Key Vault 儲存機密資料，並使用安全的範本參數，在部署時從 Key Vault 中取出這些值。 然後，您可以在參數檔案中參考金鑰保存庫和秘密。 

您也可以執行認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [在 Azure Logic Apps 中保護執行歷程記錄中的輸入和輸出](logic-apps-securing-a-logic-app.md#obfuscate)

- [參數的安全性建議](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [安全存取 Azure Logic Apps 中的參數輸入](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [使用 Azure Key Vault 在部署期間傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指引**：不適用，這項建議主要用於計算資源。 支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Logic Apps) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指導** 方針：支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure 備份) ，但不會在您的內容上執行。 

預先掃描即將上傳至非計算 Azure 資源的任何檔案，例如 App Service、Data Lake Storage、Blob 儲存體等等。 

使用 Azure 資訊安全中心的資料服務威脅偵測來偵測上傳至儲存體帳戶的惡意程式碼。 

- [瞭解 Azure 雲端服務和虛擬機器的 Microsoft 反惡意程式碼](../security/fundamentals/antimalware.md)

- [瞭解 Azure 資訊安全中心的資料服務威脅偵測](../security-center/azure-defender.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保更新反惡意程式碼軟體和簽章

**指引**：不適用，這項準則主要用於計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導** 方針：就地執行嚴重損壞修復 (DR) 解決方案，讓您可以保護資料，快速還原支援重要商務功能的資源，並讓作業持續執行，以維持 (BC) 的商務持續性。

這項嚴重損壞修復策略的重點在於設定您的主要邏輯應用程式，以容錯移轉到另一個位置中的待命或備份邏輯應用程式，也就是 Azure Logic Apps 也可以使用。 如此一來，如果主要複本可能遺失、中斷或失敗，次要複本就可以進行工作。 此策略會要求您的次要邏輯應用程式和相依資源已經部署，並可在替代位置中備妥。

此外，您應該將邏輯應用程式的基礎工作流程定義擴充到 Azure Resource Manager 範本中。 此範本會定義用來布建及部署邏輯應用程式的基礎結構、資源、參數和其他資訊。

- [深入瞭解 Azure Logic Apps 的商務持續性和嚴重損壞修復](business-continuity-disaster-recovery-guidance.md)

- [如何使用 Azure Resource Manager 範本將 Azure Logic Apps 的部署自動化](logic-apps-azure-resource-manager-templates-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：就地執行嚴重損壞修復 (DR) 解決方案，讓您可以保護資料，快速還原支援重要商務功能的資源，並讓作業持續執行，以維持 (BC) 的商務持續性。

這項嚴重損壞修復策略的重點在於設定您的主要邏輯應用程式，以容錯移轉到另一個位置中的待命或備份邏輯應用程式，也就是 Azure Logic Apps 也可以使用。 如此一來，如果主要複本可能遺失、中斷或失敗，次要複本就可以進行工作。 此策略會要求您的次要邏輯應用程式和相依資源已經部署，並可在替代位置中備妥。

此外，您應該將邏輯應用程式的基礎工作流程定義擴充到 Azure Resource Manager 範本中。 此範本會定義用來布建及部署邏輯應用程式的基礎結構、資源、參數和其他資訊。

邏輯應用程式上的每個要求端點都具有共用存取簽章， (SAS) 端點的 URL 中。 如果您使用 Azure Key Vault 儲存您的密碼，請務必定期自動備份您的金鑰和 Url。

- [深入瞭解 Azure Logic Apps 的商務持續性和嚴重損壞修復](business-continuity-disaster-recovery-guidance.md)

- [使用 Azure Resource Manager 範本將 Azure Logic Apps 的部署自動化](logic-apps-azure-resource-manager-templates-overview.md)

- [使用 SAS 安全地存取 Azure Logic Apps 中的資料](logic-apps-securing-a-logic-app.md#sas)

- [如何備份 Key Vault 金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：您的嚴重損壞修復策略應著重于設定主要邏輯應用程式，以容錯移轉至 Azure Logic Apps 也可使用的替代位置中的待命或備份邏輯應用程式。 如此一來，如果主要複本可能遺失、中斷或失敗，次要複本就可以進行工作。 此策略會要求您的次要邏輯應用程式和相依資源已經部署，並可在替代位置中備妥。

測試已備份之客戶管理金鑰的還原。 請注意，這只適用于 (ISE) 的整合服務環境上執行的 Logic Apps。

- [深入瞭解 Azure Logic Apps 的商務持續性和嚴重損壞修復](business-continuity-disaster-recovery-guidance.md)

- [設定客戶管理的金鑰，以在 Azure Logic Apps 中 (Ise) 為整合服務環境加密待用資料](customer-managed-keys-integration-service-environment.md)

- [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針：您的嚴重損壞修復策略應著重于設定主要邏輯應用程式，以容錯移轉至 Azure Logic Apps 也可使用的替代位置中的待命或備份邏輯應用程式。 如此一來，如果主要複本可能遺失、中斷或失敗，次要複本就可以進行工作。 此策略會要求您的次要邏輯應用程式和相依資源已經部署，並可在替代位置中備妥。 

保護備份客戶管理的金鑰。 請注意，這只適用于 (ISE) 的整合服務環境上執行的 Logic Apps。

在 Key Vault 中啟用 Soft-Delete 和清除保護，以防止金鑰遭到意外或惡意刪除。

- [深入瞭解 Azure Logic Apps 的商務持續性和嚴重損壞修復](business-continuity-disaster-recovery-guidance.md)

- [設定客戶管理的金鑰，以在 Azure Logic Apps 中 (Ise) 為整合服務環境加密待用資料](customer-managed-keys-integration-service-environment.md)

- [如何在 Key Vault 中啟用 Soft-Delete 和清除保護](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。 

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [利用 NIST 的電腦安全性事件處理指南，協助建立自己的事件回應計畫](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。 

此外，使用標記清楚地標示訂用帳戶 (例如， 生產、非生產) 並建立命名系統，以清楚地識別及分類 Azure 資源，尤其是處理敏感性資料的資源。  您需負責根據發生事件的 Azure 資源和環境的重要性，設定警示的補救優先順序。

- [Azure 資訊安全中心的安全性警示](../security-center/security-center-alerts-overview.md)

- [使用標記來組織 Azure 資源](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能，進而協助保護您的 Azure 資源。 找出弱點和落差，並視需要修訂計畫。

- [NIST 的出版品 - Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現您的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡資訊來連絡您。 事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心警示和建議，協助找出 Azure 資源的風險。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，透過「Logic Apps」安全性警示和建議來自動觸發回應，以保護您的 Azure 資源。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)