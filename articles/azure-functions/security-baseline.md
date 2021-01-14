---
title: 適用于 Azure Functions 的 Azure 安全性基準
description: 適用于 Azure Functions 的 Azure 安全性基準
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 5b38da5539cb80110b2a769a219213a5c74e1506
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98198550"
---
# <a name="azure-security-baseline-for-azure-functions"></a>適用于 Azure Functions 的 Azure 安全性基準

適用于 Azure Functions 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。

此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。

如需詳細資訊，請參閱 [Azure 安全性基準概觀](../security/benchmarks/security-baselines-overview.md)。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [安全性控制：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1：在虛擬網路上使用網路安全性群組或 Azure 防火牆來保護資源

**指導** 方針：整合您的 Azure Functions 應用程式與 Azure 虛擬網路。 在 Premium 方案中執行的函式應用程式與 Azure App Service 中的 web apps 具有相同的裝載功能，包括「VNet 整合」功能。  Azure 虛擬網路可讓您將許多 Azure 資源（例如 Azure Functions）放在非網際網路可路由傳送的網路中。

- [如何整合函式與 Azure 虛擬網路](./functions-create-vnet.md)

- [瞭解 Azure Functions 和 Azure App Service 的 Vnet 整合](../app-service/web-sites-integrate-with-vnet.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2：監視和記錄 VNet、子網路與 NIC 的設定和流量

**指導** 方針：使用 Azure 資訊安全中心並遵循網路保護建議，以協助保護與您 Azure Functions 應用程式相關的網路資源和網路設定。

如果使用網路安全性群組 (Nsg) 搭配您的 Azure Functions 執行，請啟用 NSG 流量記錄，並將記錄傳送到 Azure 儲存體帳戶以進行流量審核。 您也可將 NSG 流量記錄傳送到 Log Analytics 工作區，並使用流量分析來提供對 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式並找到錯誤的網路設定。

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：若要在生產環境中完整保護您的 Azure Functions 端點，您應該考慮採用下列其中一個函式應用層級安全性選項：
- 開啟函式應用程式的 App Service 驗證/授權，
- 使用 Azure API 管理 (APIM) 來驗證要求，或
- 將您的函數應用程式部署至 Azure App Service 環境。

此外，請確定已針對您的生產環境 Azure Functions 停用遠端偵錯程式。 此外，跨原始來源資源分享 (CORS) 不應允許所有網域存取 Azure 中的函數應用程式。 只允許必要網域與您的函數應用程式互動。

請考慮在網路設定中部署 Azure Web 應用程式防火牆 (WAF) ，以便額外檢查連入流量。 啟用診斷設定以 WAF 記錄，並將其內嵌至儲存體帳戶、事件中樞或 Log Analytics 工作區。 

- [如何保護生產環境中 Azure Functions 端點的安全](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [如何部署 Azure WAF](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：在與您的函式應用程式相關聯的虛擬網路上啟用 Ddos 保護標準，以防止 ddos 攻擊。 使用 Azure 資訊安全中心的整合式威脅情報來拒絕與已知惡意或未使用的公開 IP 位址通訊。
此外，請設定前端閘道（例如 Azure Web 應用程式防火牆）來驗證所有傳入的要求，並篩選出惡意流量。 Azure Web 應用程式防火牆會檢查輸入的 Web 流量以封鎖 SQL 插入、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊，藉此協助保護您的函式應用程式。 引入 WAF 需要 App Service 環境或使用私人端點 (Preview) 。 在將私人端點與生產工作負載搭配使用之前，請先確定私人端點不再處於 (預覽版) 。

- [Azure Functions 網路選項](./functions-networking-options.md)

- [Azure Functions Premium 方案](./functions-premium-plan.md)

- [App Service 環境簡介](../app-service/environment/intro.md)

- [App Service 環境的網路考量](../app-service/environment/network-info.md)

- [如何設定 DDoS 保護](../ddos-protection/manage-ddos-protection.md)

- [如何部署 Azure 防火牆](../firewall/tutorial-firewall-deploy-portal.md)

- [了解 Azure 資訊安全中心的整合式威脅情報](../security-center/azure-defender.md)

- [瞭解 Azure 資訊安全中心適應性網路強化](../security-center/security-center-adaptive-network-hardening.md)

- [瞭解 Azure 資訊安全中心及時的網路存取控制](../security-center/security-center-just-in-time.md)

- [使用私人端點進行 Azure Functions](../app-service/networking/private-endpoint.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets-and-flow-logs"></a>1.5：記錄網路封包和流量記錄

**指導** 方針：如果使用網路安全性群組 (nsg) 搭配您的 Azure Functions 執行，請啟用網路安全性群組流量記錄，並將記錄傳送至儲存體帳戶，以進行流量審核。 您也可以將流量記錄傳送到 Log Analytics 工作區，並使用「流量分析」來提供 Azure 雲端流量的見解。 流量分析的優點包括能將網路活動視覺化並找出作用點、識別安全性威脅、了解流量模式，並且找到網路設定的錯誤。

- [如何啟用 NSG 流量記錄](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [如何啟用及使用流量分析](../network-watcher/traffic-analytics.md)

- [如何啟用網路監看員](../network-watcher/network-watcher-create.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署網路型入侵偵測/入侵預防系統 (IDS/IPS)

**指導** 方針：設定前端閘道（例如 Azure Web 應用程式防火牆）來驗證所有連入要求，並篩選出惡意流量。 Azure Web 應用程式防火牆會檢查輸入的 Web 流量，以封鎖 SQL 插入、跨網站腳本、惡意程式碼上傳和 DDoS 攻擊，藉此協助保護您的函式應用程式。 引入 WAF 需要 App Service 環境或使用私人端點 (Preview) 。 在將私人端點與生產工作負載搭配使用之前，請先確定私人端點不再處於 (預覽版) 。

另外還有多個 marketplace 選項（例如 Barracuda WAF for Azure）可用於 Azure Marketplace，其中包含 IDS/IPS 功能。

- [Azure Functions 網路選項](./functions-networking-options.md)

- [Azure Functions Premium 方案](./functions-premium-plan.md)

- [App Service 環境簡介](../app-service/environment/intro.md)

- [App Service 環境的網路考量](../app-service/environment/network-info.md)

- [瞭解 Azure Web 應用程式防火牆](../web-application-firewall/index.yml)

- [使用私人端點進行 Azure Functions](../app-service/networking/private-endpoint.md)

- [瞭解 Barracuda WAF 雲端服務](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：為您的網路設定前端閘道，例如具有端對端 TLS 加密的 Azure Web 應用程式防火牆。 引入 WAF 需要 App Service 環境或使用私人端點 (Preview) 。 在將私人端點與生產工作負載搭配使用之前，請先確定私人端點不再處於 (預覽版) 。

- [Azure Functions 網路選項](./functions-networking-options.md)

- [Azure Functions Premium 方案](./functions-premium-plan.md)

- [App Service 環境簡介](../app-service/environment/intro.md)

- [App Service 環境的網路考量](../app-service/environment/network-info.md)

- [瞭解 Azure Web 應用程式防火牆](../web-application-firewall/index.yml)

- [如何透過入口網站使用應用程式閘道設定端對端 TLS](../application-gateway/end-to-end-ssl-portal.md)

- [使用私人端點進行 Azure Functions](../app-service/networking/private-endpoint.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針：使用虛擬網路服務標籤來定義網路安全性群組或 Azure 防火牆上的網路存取控制。 建立安全性規則時，您可以使用服務標籤取代特定的 IP 位址。 藉由在規則的適當來源或目的地欄位中指定服務標記名稱 (例如，AzureAppService) ，您可以允許或拒絕對應服務的流量。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [如需使用服務標記的詳細資訊](../virtual-network/service-tags-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：定義和實行與您 Azure Functions 相關之網路設定的標準安全性設定。 使用 "Microsoft. Web" 和 "Microsoft. Network" 命名空間中 Azure 原則別名來建立自訂原則，以對 Azure Functions 的網路設定進行審核或強制執行。 您也可以使用 Azure Functions 的內建原則定義，例如：
- CORS 不應允許每個資源存取您的函數應用程式
- 函數應用程式應該只能透過 HTTPS 存取
- 您的函數應用程式應使用最新的 TLS 版本

您也可以使用 Azure 藍圖，將金鑰環境成品（例如 Azure Resource Manager 範本、Azure 角色型存取控制 (Azure RBAC) 和原則）封裝在單一藍圖定義中，以簡化大規模的 Azure 部署。 您可以輕鬆地將藍圖套用至新的訂用帳戶、環境，以及透過版本控制來微調控制和管理。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何建立 Azure 藍圖](../governance/blueprints/create-blueprint-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：如果使用網路安全性群組 (nsg) 搭配您的 Azure Functions 執行，請使用 nsg 的標記，以及與網路安全性和流量流程相關的其他資源。 對於個別的 NSG 規則，使用 [描述] 欄位，針對允許進出網路流量的任何規則指定商務需求和/或持續時間 (等等)。

使用任何與標記相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您現有的未標記資源。

您可使用 Azure PowerShell 或 Azure CLI，根以據資源的標籤對資源進行查詢或執行動作。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視網路資源設定，以及偵測與您的 Azure Functions 部署相關之網路設定和資源的變更。 在 Azure 監視器中建立警示，在發生重大網路設定或資源的變更時，將會觸發這些警示。 

- [如何檢視及擷取 Azure 活動記錄事件](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [安全性控制：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護用於 Azure 資源的時間來源，例如記錄中時間戳記的 Azure Functions。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：若要進行控制平面審核記錄，請啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

Azure Functions 也提供與 Azure 應用程式 Insights 的內建整合來監視函式。 Application Insights 會收集記錄檔、效能和錯誤資料。 其會自動偵測效能異常，並隨附強大的分析工具，以協助您診斷問題，以及了解如何使用您的函式。

如果您的函數應用程式內有內建的自訂安全性/審核記錄，請啟用診斷設定 "FunctionAppLogs"，並將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶以供封存。 

(選擇性) 您可啟用 Azure Sentinel 或第三方 SIEM，並且讓資料上線。 

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何使用 Azure 應用程式 Insights 設定 Azure Functions](./functions-monitoring.md)

- [如何針對 Azure Functions 啟用 (使用者產生的記錄) 的診斷設定](./functions-monitor-log-analytics.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：若要進行控制平面審核記錄，請啟用 Azure 活動記錄診斷設定，並將記錄傳送至 Log Analytics 工作區、azure 事件中樞或 azure 儲存體帳戶進行封存。 使用 Azure 活動記錄資料，您可判斷在 Azure 資源的控制平面層級執行任何寫入作業 (PUT、POST、DELETE) 的「內容 (What)、人員 (Who) 和時間 (When)」。

如果您的函數應用程式內有內建的自訂安全性/審核記錄，請啟用診斷設定 "FunctionAppLogs"，並將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶以供封存。 

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何針對 Azure Functions 啟用 (使用者產生的記錄) 的診斷設定](./functions-monitor-log-analytics.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4：從作業系統收集安全性記錄

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，根據您組織的合規性法規，為與您的函數應用程式相關聯的 log Analytics 工作區設定記錄保留期限。

- [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：啟用 Azure 活動記錄診斷設定，以及函式應用程式的診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢以搜尋字詞、找出趨勢、分析模式，以及提供許多其他以所收集資料為基礎的見解。

為您的函數應用程式啟用 Application Insights，以收集記錄、效能和錯誤資料。 您可以在 Azure 入口網站內 Application Insights 查看收集的遙測資料。

如果您的函數應用程式內有內建的自訂安全性/審核記錄，請啟用診斷設定 "FunctionAppLogs"，並將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶以供封存。 

(選擇性) 您可啟用 Azure Sentinel 或第三方 SIEM，並且讓資料上線。 

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何啟用 Azure Functions 的診斷設定](./functions-monitor-log-analytics.md)

- [如何使用 Azure 應用程式 Insights 設定 Azure Functions 並查看遙測資料](./functions-monitoring.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7：啟用異常活動的警示

**指導** 方針：啟用 Azure 活動記錄診斷設定，以及函式應用程式的診斷設定，並將記錄傳送至 Log Analytics 工作區。 在 Log Analytics 中執行查詢以搜尋字詞、找出趨勢、分析模式，以及提供許多其他以所收集資料為基礎的見解。 您可以根據 Log Analytics 工作區查詢來建立警示。

為您的函數應用程式啟用 Application Insights，以收集記錄、效能和錯誤資料。 您可以查看 Application Insights 所收集的遙測資料，並在 Azure 入口網站內建立警示。

(選擇性) 您可啟用 Azure Sentinel 或第三方 SIEM，並且讓資料上線。 

- [如何啟用 Azure 活動記錄的診斷設定](../azure-monitor/platform/activity-log.md)

- [如何啟用 Azure Functions 的診斷設定](./functions-monitor-log-analytics.md)

- [如何啟用 Azure Functions 的 Application Insights](./configure-monitoring.md#enable-application-insights-integration)

- [如何在 Azure 中建立警示](../azure-monitor/learn/tutorial-response.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：不適用;函數應用程式不會處理或產生反惡意程式碼的相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="29-enable-dns-query-logging"></a>2.9：啟用 DNS 查詢記錄

**指導** 方針：不適用;函數應用程式不會處理或產生使用者可存取的 DNS 相關記錄。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="210-enable-command-line-audit-logging"></a>2.10：啟用命令列稽核記錄

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [安全性控制：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的詳細目錄

**指導** 方針： AZURE ACTIVE DIRECTORY (AD) 具有必須明確指派且可供查詢的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。 

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure Functions 的控制平面存取權是透過 AZURE ACTIVE DIRECTORY (AD) 來控制。 Azure AD 沒有預設密碼的概念。

您可以透過數種方式來控制資料平面存取，包括授權金鑰、網路限制，以及驗證 Azure AD 身分識別。 連接至您 Azure Functions HTTP 端點的用戶端會使用授權金鑰，而且可以隨時重新產生。 預設會為新的 HTTP 端點產生這些金鑰。

函數應用程式可使用多個部署方法，其中一些可利用一組產生的認證。 檢查將用於您應用程式的部署方法。

- [保護 HTTP 端點](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [如何取得和重新產生授權金鑰](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Azure Functions 中的部署技術](./functions-deployment-technologies.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 使用 Azure 資訊安全中心的身分識別與存取管理來監視系統管理帳戶的數目。

此外，為了協助您追蹤專屬的系統管理帳戶，您可能會使用來自 Azure 資訊安全中心或內建 Azure 原則的建議，例如：應從訂用帳戶中移除具有擁有者許可權的訂用帳戶已被取代帳戶，應從您的訂用帳戶中移除擁有者許可權的外部帳戶

- [如何使用 Azure 資訊安全中心來監視身分識別和存取 (預覽)](../security-center/security-center-identity-access.md)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：盡可能使用 Azure Active Directory SSO，而不是設定個別的獨立認證來存取您的函數應用程式的資料。 使用 Azure 資訊安全中心身分識別和存取管理建議。 使用 App Service 驗證/授權功能，為您的函數應用程式執行單一登入。

- [瞭解 Azure Functions 中的驗證和授權](../app-service/overview-authentication-authorization.md#identity-providers)

- [瞭解 Azure AD 的 SSO](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：針對所有以 Azure Active Directory 為基礎的存取使用多重要素驗證

**指引**：啟用 Azure Active Directory (AD) 多重要素驗證 (MFA)，並遵循 Azure 資訊安全中心的身分識別與存取管理建議。

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有的系統管理工作

**指引**：使用已設定多重要素驗證 (MFA) 的特殊權限存取工作站 (PAW) 來登入和設定 Azure 資源。

- [了解特殊權限存取工作站](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7：針對來自系統管理帳戶的可疑活動留下記錄和發出警示

**指引**：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (AD) 的 Privileged Identity Management (PIM) 來產生記錄和警示。

此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 風險偵測](../active-directory/identity-protection/overview-identity-protection.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指引**：使用條件式存取具名位置，只允許從 IP 位址範圍或國家/區域的特定邏輯群組存取 Azure 入口網站。

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為函數應用程式的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

- [如何設定函數應用程式以使用 Azure AD 登入](../app-service/configure-authentication-provider-aad.md)

- [如何建立及設定 Azure AD 執行個體](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針： AZURE ACTIVE DIRECTORY (AD) 提供記錄檔，以協助您探索過時的帳戶。 此外，使用 Azure 身分識別存取審核來有效率地管理群組成員資格、企業應用程式的存取權，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的使用者具有持續存取權。 

- [瞭解 Azure AD 報告](../active-directory/reports-monitoring/index.yml)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11：監視對已停用帳戶的存取嘗試

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為函數應用程式的中央驗證和授權系統。 Azure AD 會對待用資料和傳輸中資料使用增強式加密，以保護資料安全。 Azure AD 也會對使用者認證進行 Salt 處理、雜湊處理並安全儲存資料。

您可以存取 Azure AD 登入活動、audit 和風險事件記錄檔來源，讓您可以與 Azure Sentinel 或協力廠商 SIEM 整合。

您可以建立 Azure AD 使用者帳戶的診斷設定，並將審核記錄和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 中設定所需的記錄警示。

- [如何設定函數應用程式以使用 Azure AD 登入](../app-service/configure-authentication-provider-aad.md)

- [如何將 Azure 活動記錄整合到 Azure 監視器中](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12：帳戶登入行為偏差警示

**指導** 方針：使用 AZURE ACTIVE DIRECTORY (AD) 作為函數應用程式的中央驗證和授權系統。 針對控制項平面上的帳戶登入行為偏差 (Azure 入口網站) ，請使用 Azure Active Directory (AD) Identity Protection 和風險偵測功能，來設定偵測到與使用者身分識別相關的可疑動作的自動回應。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。

- [如何檢視有風險的 Azure AD 登入](../active-directory/identity-protection/overview-identity-protection.md)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [如何使 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導**：目前無法使用;Azure Functions 目前不支援客戶加密箱。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [安全性控制：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的詳細目錄

**指引**：使用標籤協助追蹤可儲存或處理敏感性資訊的 Azure 資源。

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指引**：針對開發、測試和生產，實作不同的訂用帳戶及/或管理群組。 函式應用程式應該以虛擬網路區隔 (VNet) /子網並適當地標記。

您也可以使用私人端點來執行網路隔離。 Azure 私人端點是一種網路介面，可讓您以私人且安全的方式連接到服務 (例如：函式應用程式 HTTPs 端點) Azure Private Link 提供支援。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 私人端點在高階方案中執行的函式應用程式 (預覽) 。 在將私人端點與生產工作負載搭配使用之前，請先確定私人端點不再處於 (預覽版) 。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

- [Azure Functions 網路選項](./functions-networking-options.md)

- [Azure Functions Premium 方案](./functions-premium-plan.md)

- [瞭解私人端點](../private-link/private-endpoint-overview.md)

- [使用私人端點進行 Azure Functions](../app-service/networking/private-endpoint.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：在網路周邊部署自動化工具，以監視未經授權的機密資訊傳輸，並封鎖這類傳輸，同時警示資訊安全專業人員。 

Microsoft 管理 Azure Functions 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：不適用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：在函式應用程式的 Azure 入口網站中，于 [平臺功能：網路功能： SSL] 下啟用 [僅 HTTPs] 設定，並將最低的 TLS 版本設定為1.2。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導**：目前無法使用;Azure Functions 目前無法使用資料識別、分類和遺失防護功能。 標記函式應用程式，這些應用程式可能會以這種方式處理敏感性資訊，並視需要執行協力廠商解決方案（如果符合規範）。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6：使用 Azure RBAC 來控制資源的存取權

**指導** 方針：使用 azure 角色型存取控制 (azure RBAC) 來控制 Azure 入口網站)  (函數應用程式控制平面的存取權。 

- [如何設定 Azure RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

Microsoft 管理 Azure Functions 的基礎結構，並已實行嚴格的控制，以防止客戶資料遺失或洩漏。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針：建立函數應用程式時，您必須建立或連結到支援 Blob、佇列和資料表儲存體的一般用途 Azure 儲存體帳戶。 這是因為 Functions 依賴「Azure 儲存體」來進行作業，例如管理觸發程序和記錄函數執行。 Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰來加密 blob 和檔案資料。 這些金鑰必須存在於 Azure Key Vault 中，函數應用程式才能存取儲存體帳戶。

- [瞭解 Azure Functions 的儲存體考慮](./storage-considerations.md)

- [瞭解待用資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄來建立生產環境函數應用程式以及其他重要或相關資源變更時的警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [安全性控制：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：採用 DevSecOps 實務，以確保您的函式應用程式安全，並在其生命週期期間維持盡可能安全的狀態。 DevSecOps 將您組織的安全性小組及其功能納入您的 DevOps 實務中，讓小組的每個人都能承擔安全性。

此外，請遵循 Azure 資訊安全中心的建議，以協助保護您的函數應用程式。

- [如何將持續安全性驗證新增至 CI/CD 管線](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [如何實行 Azure 資訊安全中心弱點評定建議](../security-center/deploy-vulnerability-assessment-vm.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3：部署自動化的第三方軟體修補程式管理解決方案

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4：比較連續性弱點掃描

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針： Microsoft 會在支援 Azure Functions 的基礎系統上執行弱點管理，不過，您可以使用 Azure 資訊安全中心內建議的嚴重性以及安全分數來測量環境內的風險。 您的安全分數取決於您已緩和的資訊安全中心建議數量。 若要優先解決建議，請考慮每一個建議的嚴重性。

- [安全性建議參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：是

**責任**：共用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [安全性控制：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-azure-asset-discovery"></a>6.1：使用 Azure 資產探索

**指引**：使用 Azure Resource Graph 來查詢/探索訂用帳戶內的所有資源 (例如計算、儲存體、網路、連接埠及通訊協定等)。  確保您的租用戶中有適當的 (讀取) 權限，並列舉所有 Azure 訂用帳戶以及訂用帳戶內的資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

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

此外，使用 Azure 原則來限制可在客戶 (訂用帳戶中建立的資源類型的限制) 使用下列內建原則定義：不允許的資源類型允許的資源類型

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [如何建立和使用標籤](../azure-resource-manager/management/tag-resources.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4：維護受核准 Azure 資源和軟體標題的清查

**指引**：定義已核准的 Azure 資源，以及適用於計算資源的已核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型（ (s) ）施加限制。 

使用 Azure Resource Graph 來查詢/探索其訂閱內的資源。  確保已核准環境中的所有 Azure 資源。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：使用 Azure 原則對可在客戶訂用帳戶 () s 中建立的資源類型限制使用下列內建原則定義的限制：不允許資源類型允許的資源類型

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/index.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-implement-approved-application-list"></a>6.10：實作已核准的應用程式清單

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11：透過指令碼限制使用者與 Azure Resource Manager 互動的能力

**指引**：設定 Azure 條件式存取，以藉由對「Microsoft Azure 管理」應用程式設定「封鎖存取」，限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導** 方針：不適用;這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：對於敏感或高風險的函式應用程式，請執行個別的訂用帳戶和/或管理群組以提供隔離。

將高風險函式應用程式部署到他們自己的虛擬網路中 (VNet) 。 函數應用程式的周邊安全性是透過 Vnet 達成。 在 Premium 方案中執行的函式或 App Service 環境 (ASE) 可以與 Vnet 整合。 選擇最適合您使用案例的架構。

- [Azure Functions 網路選項](./functions-networking-options.md)

- [Azure Functions Premium 方案](./functions-premium-plan.md)

- [App Service 環境的網路考量](../app-service/environment/network-info.md)

- [如何建立外部 ASE](../app-service/environment/create-external-ase.md)

如何建立內部 ASE：

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](../virtual-network/quick-create-portal.md)

- [如何使用安全性設定建立 NSG](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [安全性控制：安全設定](../security/benchmarks/security-control-secure-configuration.md)。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則定義和執行函數應用程式的標準安全性設定。 使用 "Microsoft. Web" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行您的函數應用程式設定。 您也可以利用內建的原則定義，例如：
- 函式應用程式應該使用受控識別
- 應關閉函數應用程式的遠端偵錯程式
- 函數應用程式應該只能透過 HTTPS 存取

- [如何檢視可用的 Azure 原則別名](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="72-establish-secure-operating-system-configurations"></a>7.2：建立安全的作業系統設定

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 azure 原則 [拒絕] 和 [部署是否不存在]，在您的 Azure 資源上強制執行安全設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4：維護安全的作業系統設定

**指導** 方針：不適用;雖然可以部署內部部署功能，但此指導方針適用于 IaaS 計算資源。 部署內部部署功能時，您必須負責環境的安全設定。

- [瞭解內部部署函數](./functions-runtime-install.md)

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：在原始檔控制中安全地儲存和管理 ARM 範本和自訂 Azure 原則定義。

- [什麼是基礎結構即程式碼](/azure/devops/learn/what-is-infrastructure-as-code)

- [設計原則即程式碼工作流程](../governance/policy/concepts/policy-as-code.md)

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="76-securely-store-custom-operating-system-images"></a>7.6：安全地儲存自訂作業系統映像

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="77-deploy-system-configuration-management-tools"></a>7.7：部署系統設定管理工具

**指導** 方針：使用內建的 Azure 原則定義以及 "Microsoft. Web" 命名空間中 Azure 原則的別名，建立自訂原則來警示、審核和強制執行系統設定。 此外，開發流程和管線以管理原則例外狀況。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的系統設定管理工具

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9：為 Azure 服務實作自動化的設定監視

**指導** 方針：使用內建的 Azure 原則定義以及 "Microsoft. Web" 命名空間中 Azure 原則的別名，建立自訂原則來警示、審核和強制執行系統設定。 使用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，自動強制執行 Azure 資源的設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 祕密

**指導** 方針：使用受控識別搭配 Azure Key Vault，以簡化和保護雲端應用程式的秘密管理。 受控識別可讓您的函式應用程式向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不需要您程式碼中的任何認證。

- [如何建立 Key Vault](../key-vault/secrets/quick-create-portal.md)

- [如何使用 App Service 和 Azure Functions 的受控身分識別](../app-service/overview-managed-identity.md)

* [如何驗證 Key Vault](../key-vault/general/authentication.md)

* [如何指派 Key Vault 存取原則](../key-vault/general/assign-access-policy-portal.md)

- [使用 App Service 和 Azure Functions 的 Key Vault 參考](../app-service/app-service-key-vault-references.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：使用受控識別，在 Azure AD 中為您的函數應用程式提供自動管理的身分識別。 受控識別可供對支援 Azure AD 驗證的任何服務進行驗證 (包括 Key Vault)，不需要程式碼中的任何認證。

- [如何使用 App Service 和 Azure Functions 的受控身分識別](../app-service/overview-managed-identity.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。 

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [安全性控制：惡意程式碼防禦](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導** 方針：不適用;此指導方針適用于 IaaS 計算資源。

支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Functions) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2：預先掃描要上傳至非計算 Azure 資源的檔案

**指引**：不適用，這項建議主要用於設計用來儲存資料的非計算資源。


**Azure 資訊安全中心監視**：不適用

**責任**：不適用

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3：確保會更新反惡意程式碼軟體和簽章

**指引**：不適用，這項建議主要用於設計用來儲存資料的非計算資源。

支援 Azure 服務的基礎主機上已啟用 Microsoft 反惡意程式碼 (例如 Azure Functions) ，但不會對客戶內容執行。

**Azure 資訊安全中心監視**：不適用

**責任**：不適用

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [安全性控制：資料復原](../security/benchmarks/security-control-data-recovery.md)。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確保會定期自動備份

**指導** 方針：使用備份和還原功能來排程您應用程式的定期備份。 在 Premium 方案中執行的函式應用程式與 Azure App Service 中的 web 應用程式具有相同的裝載功能，包括「備份與還原」功能。

也可以使用原始檔控制解決方案（例如 Azure Repos 和 Azure DevOps 安全地儲存和管理您的程式碼）。 Azure DevOps Services 採用許多 Azure 儲存體功能，為的是在發生硬體失敗、服務中斷或區域災害時確保資料的可用性。 此外，Azure DevOps 小組會遵循程序來防止資料遭到意外或惡意刪除。

- [在 Azure 中備份應用程式](../app-service/manage-backup.md)

- [瞭解 Azure DevOps 中的資料可用性](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份，並備份客戶管理的任何金鑰

**指導** 方針：使用備份和還原功能來排程您應用程式的定期備份。 在 Premium 方案中執行的函式應用程式與 Azure App Service 中的 web 應用程式具有相同的裝載功能，包括「備份與還原」功能。 Azure Key Vault 內備份客戶管理的金鑰。

也可以使用原始檔控制解決方案（例如 Azure Repos 和 Azure DevOps 安全地儲存和管理您的程式碼）。 Azure DevOps Services 採用許多 Azure 儲存體功能，為的是在發生硬體失敗、服務中斷或區域災害時確保資料的可用性。 此外，Azure DevOps 小組會遵循程序來防止資料遭到意外或惡意刪除。

- [在 Azure 中備份應用程式](../app-service/manage-backup.md)

- [如何在 Azure 中備份金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [瞭解 Azure DevOps 中的資料可用性](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [如何在 Azure DevOps 中儲存程式碼](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos 文件](/azure/devops/repos/index?view=azure-devops)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：確保能夠定期從備份和還原功能執行還原。 如果使用其他離線位置來備份您的程式碼，請定期確定能夠執行完整還原。 測試已備份之客戶管理金鑰的還原。

- [從備份還原 Azure 中的應用程式](../app-service/web-sites-restore.md)

- [在 Azure 中透過快照集還原應用程式](../app-service/app-service-web-restore-snapshots.md)

- [如何在 Azure 中還原金鑰保存庫金鑰](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰受到保護

**指導** 方針：備份和還原功能的備份會使用您訂用帳戶中的 Azure 儲存體帳戶。 Azure 儲存體會加密待用儲存體帳戶中的所有資料。 根據預設，資料是以使用 Microsoft 管理的金鑰加密。 若要進一步控制加密金鑰，您可以提供客戶管理的金鑰來加密儲存體資料。

如果您使用客戶管理的金鑰，請確定已啟用 Key Vault 中的 Soft-Delete，以防止金鑰遭到意外或惡意刪除。

- [Azure 儲存體待用加密](../storage/common/storage-service-encryption.md)

- [如何金鑰保存庫中啟用虛刪除](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure 資訊安全中心監視**：是

**責任**：共用

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [安全性控制：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

- [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md)

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指引**：資訊安全中心會指派每個警示的嚴重性，以協助設定應優先調查哪些警示。 嚴重性會依據資訊安全中心對用於發出警示的發現或分析其信心程度，以及信賴等級具有活動背後會導致警示的惡意意圖。

此外，清楚地標示訂用帳戶 (例如 生產、非生產) 並建立命名系統，以清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

- [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可讓您以手動或持續不斷的方式來匯出警示和建議。 您可使用 Azure 資訊安全中心的資料連接器，將警示串流至 Azure Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用 Azure 資訊安全中心中的工作流程自動化功能，自動觸發 Logic Apps 的安全性警示和建議的回應。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [安全性控制：滲透測試和 Red Team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指導** 方針：遵循 Microsoft 的 Engagement 規則，以確保您的滲透測試不違反 Microsoft 原則。 針對受 Microsoft 管理的雲端基礎結構、服務和應用程式，使用 Microsoft 的策略和執行的 Red 小組和即時網站滲透測試。

- [滲透測試運作規則](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)
