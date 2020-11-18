---
title: 適用于 App Service 的 Azure 安全性基準
description: App Service 安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: app-service
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 640d51de6dac1d175f9b46f327b72f293cf025cb
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94849166"
---
# <a name="azure-security-baseline-for-app-service"></a>適用于 App Service 的 Azure 安全性基準

適用于 App Service 的 Azure 安全性基準包含可協助您改善部署安全性狀態的建議。 此服務的基準取自 [Azure 安全性效能評定 1.0 版](../security/benchmarks/overview-v1.md)，其會提供如何在 Azure 上使用最佳做法指引來保護雲端解決方案的建議。 內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于 App Service 的相關指引來分組。 已排除不適用 App Service 的 **控制項**。

若要查看 App Service 如何完全對應至 Azure 安全性基準測試，請參閱 [完整的 App Service 安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：當您在隔離的定價層中使用 App Service 時，也稱為 APP SERVICE 環境 (ASE) 您可以直接部署到 Azure 虛擬網路內的子網中。 使用網路安全性群組來保護您的 Azure App Service 環境，方法是封鎖對您虛擬網路中資源的輸入和輸出流量，或限制 App Service 環境中的應用程式存取。

依預設，網路安全性群組包含最低優先順序的隱含拒絕規則，需要您明確新增允許規則。 根據最低許可權的網路方法，新增網路安全性群組的允許規則。 用來裝載 App Service 環境的基礎虛擬機器無法直接存取，因為它們位於受 Microsoft 管理的訂用帳戶中。

透過 Web 應用程式防火牆來路由傳送流量， (WAF) 啟用 Azure 應用程式閘道，以保護 App Service 環境。 將服務端點與應用程式閘道搭配使用，以保護對您應用程式的輸入發佈流量。  

在多租使用者 App Service (不在隔離層) 的應用程式，請使用網路安全性群組來封鎖來自您應用程式的輸出流量。 使用虛擬網路整合功能，讓您的應用程式可透過虛擬網路存取中的資源。 這項功能也可用來封鎖從應用程式到公用位址的輸出流量。 虛擬網路整合無法用來提供應用程式的輸入存取。  

使用下列內容保護應用程式的輸入流量：
- 存取限制-控制輸入存取的一系列允許或拒絕規則
- 服務端點-可以拒絕來自指定虛擬網路或子網外部的輸入流量
- 私人端點-使用私人 IP 位址向您的虛擬網路公開您的應用程式。 在您的應用程式上啟用私人端點後，即無法再透過網際網路存取

使用虛擬網路整合功能搭配相同區域中的虛擬網路時，請使用網路安全性群組和路由表搭配使用者定義路由。 您可以將使用者定義的路由放在整合子網上，以傳送預期的輸出流量。  

請考慮採用 Azure 防火牆，在您的訂用帳戶和虛擬網路集中建立、強制執行及記錄應用程式和網路連線原則。 Azure 防火牆會針對虛擬網路資源使用靜態公用 IP 位址，允許外部防火牆識別源自您虛擬網路的流量。 

- [鎖定 App Service 環境](environment/firewall-integration.md)

- [開啟 Web 應用程式安全性專案 (OWASP) 前10個弱點保護](https://owasp.org/www-project-top-ten/)

- [網路安全性群組](/azure/virtual-network/security-overview)

- [將您的應用程式與 Azure 虛擬網路整合](web-sites-integrate-with-vnet.md)

- [App Service 環境的網路考量](environment/network-info.md)

- [如何建立外部 ASE](environment/create-external-ase.md)

- [如何建立內部 ASE](environment/create-ilb-ase.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2：監視和記錄虛擬網路、子網和網路介面的設定和流量

**指導** 方針：從 Azure 資訊安全中心執行網路保護建議，以保護與 App Service 應用程式和 api 相關的網路資源和設定。

使用 Azure 防火牆來傳送流量，並跨訂用帳戶和虛擬網路集中建立、強制執行及記錄應用程式和網路連線原則。 Azure 防火牆會針對您的虛擬網路資源使用靜態公用 IP 位址，允許外部防火牆識別源自您虛擬網路的流量。 Azure 防火牆服務也會與 Azure 監視器完全整合，以進行記錄和分析。

- [Azure 防火牆總覽](../firewall/overview.md)

- [瞭解 Azure 資訊安全中心所提供的網路安全性](../security-center/security-center-network-recommendations.md)

- [如何啟用 App Service 的監視和保護](/azure/security-center/security-center-app-services)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針：透過下列方式保護 APP SERVICE 環境 (ASE) 中的網際網路可存取應用程式：
- 在網際網路對向應用程式前面使用 Azure 應用程式閘道部署 Web 應用程式防火牆 (WAF) 
- 使用存取限制來保護對應用程式閘道的輸入流量
- 使用 Azure Active Directory (Azure AD) 保護應用程式，以確保驗證
- 將最小 TLS 版本設定為1。2
- 將應用程式設定為僅限 HTTPS

驅動透過 Azure 防火牆裝置輸出的所有應用程式流量，並監視記錄。 

若要保護多租使用者 App Service 中可存取網際網路的應用程式， (例如，而不是在隔離層) 
- 在應用程式前方部署啟用 Web 應用程式防火牆的裝置
- 使用存取限制或服務端點來保護 Web 應用程式防火牆 (WAF) 裝置的輸入流量
- 使用 Azure AD 保護應用程式，以確保驗證
- 將最小 TLS 版本設定為1。2
- 將應用程式設定為僅限 HTTPS
- 使用虛擬網路整合和應用程式設定 WEBSITE_VIRTUAL NETWORK_ROUTE_ALL，讓所有輸出流量受限於網路安全性群組和整合子網上的使用者定義路由。

類似于應用程式服務環境應用程式，透過 Azure 防火牆裝置輸出輸出的所有應用程式流量，並監視應用程式中的記錄。

此外，請參閱鎖定 App Service 環境檔，並遵循中的建議。

- [鎖定 App Service 環境](environment/firewall-integration.md)

- [Azure 應用程式閘道上的 Azure Web 應用程式防火牆](../web-application-firewall/ag/ag-overview.md)

- [Azure App Service 存取限制](app-service-ip-restrictions.md)

- [使用 Azure 監視器來追蹤 WAF 警示並輕鬆監視趨勢 ](../azure-monitor/overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針：保護 App Service 環境，如鎖定 App Service 環境檔中所述。 將整合式威脅情報功能套用到 Azure 資訊安全中心，以拒絕與已知惡意或未使用的公用 IP 位址的通訊。 使用存取限制來保護對應用程式閘道的輸入流量。 

保護多租使用者 App Service (不在隔離層) 的應用程式，以及公用網際網路面向端點。 它只允許來自您虛擬網路內特定子網的流量，並封鎖其他所有專案。 使用存取限制來設定網路存取控制清單 (IP 限制) 來鎖定允許的輸入流量。

在已排序的 [允許] 或 [拒絕] 清單中定義優先順序，以管理對您應用程式的網路存取。 這份清單可以包含 IP 位址或虛擬網路子網。 當清單中包含一或多個專案時，就會在清單結尾處存在隱含的「全部拒絕」規則。 這項功能適用于所有 App Service 裝載的工作負載，包括、Web Apps、API Apps、Linux 應用程式、Linux 容器應用程式和功能。 

使用服務端點來限制從 Azure 虛擬網路存取您的 web 應用程式。 限制存取多租使用者 App Service (不在隔離層中的應用程式) ，從選取的子網與服務端點。 

- [Azure App Service 靜態 IP 限制](app-service-ip-restrictions.md)

- [Azure 應用程式閘道上的 Azure Web 應用程式防火牆](../web-application-firewall/ag/ag-overview.md)

- [如何設定 Web 應用程式防火牆 (WAF) 以進行 App Service 環境](environment/app-service-app-service-environment-web-application-firewall.md)

- [保護 ASE 的安全，如鎖定 App Service 環境所述](environment/firewall-integration.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：監視透過「安全性中心」 App Service 應用程式傳送的要求和回應。 您可以使用具有 Web 應用程式防火牆的即時應用程式閘道來監視 web 應用程式的攻擊，此閘道已啟用 Azure 監視器的整合式記錄來追蹤 Web 應用程式防火牆警示，並可輕鬆地監視趨勢。

- [Azure 應用程式閘道上的 Web 應用程式防火牆](../web-application-firewall/ag/ag-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：管理 App Service 環境中的應用程式流量：

- 保護 App Service 環境，如鎖定 App Service 環境所述
- 在您的網際網路對向應用程式前面部署具有 Azure Web 應用程式防火牆的應用程式閘道
- 將應用程式設定為只能透過 HTTPS 存取

管理多租使用者 App Service (不在隔離層) 中的網際網路可存取應用程式流量： 

- 部署應用程式閘道，該應用程式閘道已在網際網路對應的應用程式前面啟用 Azure Web 應用程式防火牆
- 使用存取限制或服務端點來保護 Web 應用程式防火牆的輸入流量。 存取限制功能適用于所有 App Service 裝載的工作負載，包括 Web Apps、API Apps、Linux 應用程式、Linux 容器應用程式和功能。

- 將應用程式設定為只能透過 HTTPS 存取
- 使用靜態 IP 限制來限制對您的 App Service 應用程式的存取，讓它只會接收來自應用程式閘道上 VIP 的流量，作為存取權的唯一位址。

如需其他資訊，請參閱參考的連結。

- [Azure App Service 靜態 IP 限制](app-service-ip-restrictions.md)

- [Azure 應用程式閘道上的 Azure Web 應用程式防火牆](../web-application-firewall/ag/ag-overview.md)

- [如何透過入口網站使用應用程式閘道設定端對端 TLS](../application-gateway/end-to-end-ssl-portal.md)

- [保護 ASE 的安全，如鎖定 App Service 所述](https://review.docs.microsoft.com/azure/app-service/environment/firewall-integrationEnvironment:)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8：將網路安全性規則的複雜性和系統管理負荷降至最低

**指導** 方針： App Service 有許多用來管理服務的端點。 這些端點位址也會包含在 AppServiceManagement IP 服務標記中。 AppServiceManagement 標記只會搭配 App Service 環境使用，以允許這類流量。 

您可以在規則的適當來源或目的地欄位中指定服務標記名稱，以允許或拒絕對應服務的流量。 App Service 的輸入位址會在 AppService IP 服務標記中追蹤。 沒有 IP 服務標記包含 App Service 使用的輸出位址。

Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤。

- [虛擬網路服務標籤](../virtual-network/service-tags-overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：定義和執行與您的 App Service 應用程式相關之網路設定的標準安全性設定。 

使用 "Microsoft. Web" 和 "Microsoft. Network" 命名空間中的 Azure 原則別名來維護安全性設定。 建立自訂原則來審核或強制執行 App Service 應用程式的網路設定。 

針對 App Service 使用內建原則定義，例如：
- 應用程式應該使用虛擬網路服務端點
- 應用程式應該只能透過 HTTPS 存取
- 將最小 TLS 版本設定為最新版本

如需其他資訊，請參閱參考的連結。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何透過入口網站使用應用程式閘道設定端對端 TLS](../application-gateway/end-to-end-ssl-portal.md)

- [保護 ASE 的安全，如鎖定 App Service 所述](https://review.docs.microsoft.com/azure/app-service/environment/firewall-integrationEnvironment:)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針：針對網路安全性群組和其他相關資源使用標籤，包括 App Service 中的流量流程。

使用任何規則的 [描述] 欄位指定商務需求、持續時間等等，以允許對個別網路安全性群組規則的網路流量。

套用任何與標記效果相關的內建 Azure 原則定義，例如「需要標籤和其值」，以確保所有資源都是使用標籤建立的，並會通知您任何現有未標記的資源。 使用 Azure PowerShell 或 Azure CLI，根據其標記查閱或執行資源的動作。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

- [Azure App Service 存取限制](/azure/app-service/app-service-ip-restriction)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 活動記錄監視網路資源設定，以及偵測對網路設定的變更，以及與 App Service 相關的任何資源。 

套用 App Service 的數個 Azure 原則內建定義之一，例如審核應用程式以使用虛擬網路端點服務的原則。 在 Azure 監視器中建立警示，以在重大網路設定或資源的變更發生時觸發。 

在入口網站中或透過程式設計工具，查看資訊安全中心的詳細安全性警示和建議。 匯出此資訊，或將其傳送至您環境中的其他監視工具。 您可以手動或持續持續的方式，使用工具來匯出警示和建議。 使用這些工具，您可以：
 
- 持續匯出至 Log Analytics 工作區
- 持續匯出至 Azure 事件中樞 (，以與協力廠商 Siem) 整合
- 匯出至 CSV 檔案 (一次) 

建議您建立具有自動化工具的進程，以監視網路資源設定並快速偵測變更。

- [如何檢視及擷取 Azure 活動記錄事件](/azure/azure-monitor/platform/activity-log-view)

- [如何在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-activity-log.md)

- [匯出安全性警訊和建議](../security-center/continuous-export.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：整合 APP SERVICE 環境 (ASE) 與 Azure 監視器，以將記錄傳送至 Azure 儲存體、Azure 事件中樞或 Log Analytics。 啟用控制平面審核記錄的 Azure 活動記錄診斷設定。 來自安全中心的安全性警示會發佈至 Azure 活動記錄。 審核 Azure 活動記錄資料，讓您決定任何寫入作業的「內容、物件和時間」 (PUT、POST、DELETE) 在控制平面層級為 Azure App Service 和其他 Azure 資源所執行。 儲存您的查詢以供未來使用、將查詢結果釘選到 Azure 儀表板，以及建立記錄警示。 此外，您也可以使用 Application Insights 中的資料存取 REST API，以程式設計方式存取遙測資料。

使用 Microsoft Azure Sentinel，可根據您的業務需求，使用可調整的雲端原生安全性資訊事件管理 (SIEM) 來連線至各種資料來源和連接器。 您也可以啟用和內部資料至協力廠商安全性資訊事件管理 (SIEM) 系統，例如 Azure Marketplace 中的 Barracuda。

- [記錄 ASE 活動](environment/using-an-ase.md#logging)

- [如何啟用 Azure App Service 的診斷設定](troubleshoot-diagnostic-logs.md)

- [如何啟用 Application Insights](../azure-monitor/app/app-insights-overview.md)

- [從 Application Insights 匯出遙測](../azure-monitor/app/export-telemetry.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：啟用 App Service 的控制平面審核記錄的 Azure 活動記錄診斷設定。 將記錄傳送至 Log Analytics 工作區、Azure 事件中樞或 Azure 儲存體帳戶。
在控制平面層級 (PUT、POST、DELETE) 所執行之任何寫入作業的「內容、物件和時間」，可使用適用于 App Service 和其他 Azure 資源的 Azure 活動記錄資料來決定。

此外，Azure Key Vault 可透過存取原則和審核歷程記錄來提供集中式秘密管理。 

- [如何啟用 Azure 活動記錄的診斷設定](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何啟用 Azure App Service 的診斷設定](troubleshoot-diagnostic-logs.md)

- [Resource Manager 作業](../role-based-access-control/resource-provider-operations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：在 Azure 監視器中，根據您組織的合規性法規，設定與您 App Service 資源相關聯的 log Analytics 工作區記錄保留期限。
- [如何設定記錄保留參數](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和審核記錄

**指導** 方針：檢查您 App Service 資源中的 Azure 活動記錄診斷設定，並將記錄傳送至 log Analytics 工作區。 在 Log Analytics 中執行查詢以搜尋字詞、找出趨勢、分析模式，以及提供許多其他以所收集資料為基礎的見解。

使用 Application Insights App Service 應用程式，並收集記錄、效能和錯誤資料。 查看 Azure 入口網站內 Application Insights 所收集的遙測資料。

如果您已將 Web 應用程式防火牆部署 (WAF) ，您可以使用即時的 Web 應用程式防火牆記錄來監視對 App Service 應用程式的攻擊。 此記錄檔會與 Azure 監視器整合，以追蹤 Web 應用程式防火牆警示並輕鬆地監視趨勢。

使用 Azure Sentinel 是可調整的雲端原生安全性資訊事件管理 (SIEM) ，以根據需求與各種資料來源和連接器整合。 （選擇性）啟用 Azure Marketplace 中的協力廠商安全性資訊事件管理解決方案的和內部資料。

- [如何啟用 Azure 活動記錄的診斷設定](/azure/azure-monitor/platform/diagnostic-settings-legacy)

- [如何啟用 Application Insights](../azure-monitor/app/app-insights-overview.md)

- [如何整合您的 App Service 環境與 Azure 應用程式閘道 (WAF) ](environment/integrate-with-application-gateway.md)

- [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：在您的 Azure 訂用帳戶中設定「安全性中心」，並檢查產生的警示。 您可以使用 Azure 監視器將活動記錄資料取得至事件中樞，以供安全性資訊事件管理 (SIEM) 解決方案（例如 Azure Sentinel）讀取。 

使用與已部署 Azure Web 應用程式防火牆 (WAF) 的即時 Web 應用程式防火牆記錄檔，監視對您的 App Service 應用程式的攻擊。 此記錄檔會與 Azure 監視器整合，以追蹤 Web 應用程式防火牆 (WAF) 警示並輕鬆地監視趨勢。

- [如何整合您的 App Service 環境與 Azure 應用程式閘道 (WAF) ](environment/integrate-with-application-gateway.md)

- [匯出安全性警訊和建議](../security-center/continuous-export.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： Azure Active Directory (Azure AD) 有必須明確指派和查詢能力的內建角色。 使用 Azure AD PowerShell 模組執行臨機操作查詢，以探索屬於系統管理群組成員的帳戶。

- [如何使用 PowerShell 在 Azure AD 中取得目錄角色的成員](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

- [如何使用 App Service 和 Azure Functions 的受控身分識別](overview-managed-identity.md?context=azure%2Factive-directory%2Fmanaged-identities-azure-resources%2Fcontext%2Fmsi-context&amp;tabs=dotnet)

- [使用 Azure 入口網站新增或移除 Azure 角色指派](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="32-change-default-passwords-where-applicable"></a>3.2：在適用的情況下變更預設密碼

**指導** 方針： Azure Active Directory (Azure AD) 沒有預設密碼的概念。 它會提供 App Service 的控制平面存取。

一般來說，在建立您自己的應用程式時，請避免執行使用者存取的預設密碼。 針對 App Service （例如 Azure AD、Microsoft 帳戶、Facebook、Google 或 Twitter）使用預設提供的其中一個身分識別提供者。

除非您需要支援，否則請停用匿名存取。 

- [預設會在 Azure App Service 中提供識別提供者](overview-authentication-authorization.md#identity-providers)

- [Azure App Service 和 Azure Functions 中的驗證和授權](overview-authentication-authorization.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指引**：建立使用專用系統管理帳戶的標準作業程序。 您可以使用資訊安全中心的身分識別和存取管理功能，來監視和追蹤系統管理帳戶的數目。 

使用來自「安全性中心」或內建 Azure 原則的建議，例如：

- 應將一個以上的擁有者指派給您的訂用帳戶。 
- 具有擁有者權限的已取代帳戶應該從您的訂用帳戶中移除
- 具有擁有者權限的外部帳戶應該從您的訂用帳戶中移除

建立監視網路資源設定的進程，並偵測系統管理帳戶的變更。

- [如何使用 Azure 資訊安全中心來監視身分識別和存取](../security-center/security-center-identity-access.md)

- [如何使用 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [深入瞭解如何將應用程式的存取權授與使用者](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4：使用 Azure Active Directory 單一登入 (SSO) 

**指導** 方針：透過 Azure Active Directory (Azure AD) 驗證 App Service。 它會為您的身分識別提供者提供 OAuth 2.0 服務，並啟用對行動和 web 應用程式的授權存取。 

App Service apps 會使用同盟身分識別，而協力廠商身分識別提供者會為您管理使用者身分識別和驗證流程。 預設會提供這些識別提供者：

- Azure AD
- Microsoft 帳戶

- Facebook

- Google

- Twitter

當您使用其中一個提供者來啟用驗證和授權時，其登入端點可供使用者驗證，並可驗證來自提供者的驗證權杖。

- [瞭解 Azure App Service 中的驗證和授權](overview-authentication-authorization.md#identity-providers)

- [瞭解 Azure App Service 中的驗證和授權](overview-authentication-authorization.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5：所有以 Azure Active Directory 為基礎的存取都使用多重要素驗證

**指導** 方針：啟用 Azure Active Directory (Azure AD) 中的多重要素驗證功能，並遵循資訊安全中心的身分識別和存取管理建議。

針對 Azure AD 執行多重要素驗證。 系統管理員必須確保入口網站中的訂用帳戶帳戶受到保護。 訂用帳戶很容易受到攻擊，因為它會管理您所建立的資源。 

- [Azure 安全性 MFA](/azure/security/develop/secure-aad-app)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

- [如何在 Azure 資訊安全中心監視身分識別和存取](../security-center/security-center-identity-access.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6：使用安全、受 Azure 管理的工作站進行系統管理工作

**指導** 方針：使用特殊許可權的存取工作站 (PAW) ，並設定多重要素驗證以登入和設定 Azure 資源。

- [瞭解特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [如何在 Azure 中啟用 MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7：來自系統管理帳戶的可疑活動記錄和警示

**指導** 方針：當環境中發生可疑或不安全的活動時，請使用 Azure Active Directory (Azure AD 中的 PRIVILEGED IDENTITY MANAGEMENT (PIM) 來產生記錄和警示。

此外，使用 Azure AD 風險偵測來檢視有風險的使用者行為相關警示和報告。

安全中心的威脅防護可為您的環境提供完整的防禦措施，包括 Azure 計算資源（例如 Windows 機器、Linux 機器、App Service 和 Azure 容器）的威脅防護。

- [如何部署 Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [了解 Azure AD 風險偵測](/azure/active-directory/reports-monitoring/concept-risk-events)

- [Azure 計算資源的威脅防護](/azure/security-center/threat-protection)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8：僅從核准的位置管理 Azure 資源

**指導** 方針：使用名為「位置」的條件式存取，只允許從 IP 位址範圍、國家或地區的特定邏輯群組存取 Azure 入口網站。

- [如何在 Azure 中設定具名位置](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="39-use-azure-active-directory"></a>3.9：使用 Azure Active Directory

**指導** 方針：使用 Azure Active Directory (Azure AD) 做為您 App Service 應用程式的中央驗證和授權系統。 Azure AD 使用強式加密來保護待用和傳輸中的資料，以及 salts、雜湊及安全地儲存使用者認證，藉此保護資料。

- [如何設定 Azure App Service apps 以使用 Azure AD 登入](configure-authentication-provider-aad.md)

- [如何建立和設定 Azure AD 實例](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10：定期檢閱並協調使用者存取

**指導** 方針：使用 Azure Active Directory (Azure AD) 所提供的記錄來探索過時的帳戶。 使用 Azure 身分識別存取審核來有效率地管理群組成員資格和企業應用程式的存取權，以及角色指派。 定期檢查使用者存取權，以確保只有預期的使用者能夠繼續存取。 

- [瞭解 Azure AD 報告](/azure/active-directory/reports-monitoring)

- [如何使用 Azure 身分識別存取權檢閱](../active-directory/governance/access-reviews-overview.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11：監視嘗試存取已停用的認證

**指導** 方針：使用 Azure Active Directory (Azure AD) 做為您 App Service 應用程式的中央驗證和授權系統。 Azure AD 使用強式加密來保護資料，以及傳輸中、salts、雜湊，以及安全地儲存使用者認證，藉此保護資料。

存取 Azure AD 的「登入」活動、「審核」和「風險事件記錄檔」來源，可讓您與 Azure Sentinel 或協力廠商安全性資訊事件管理 (SIEM) 解決方案整合。 針對 Azure AD 使用者帳戶建立診斷設定，並將 audit 和登入記錄傳送至 Log Analytics 工作區，以簡化此程式。 您可以在 Log Analytics 中設定所需的記錄警示。

- [如何設定 Azure App Service apps 以使用 Azure AD 登入](configure-authentication-provider-aad.md)

- [如何將 Azure 活動記錄整合到 Azure 監視器中](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [如何讓 Azure Sentinel 上線](../sentinel/quickstart-onboard.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12：帳戶登入行為偏差的警示

**指導** 方針：使用 Azure Active Directory (Azure AD) 做為您 App Service 應用程式的中央驗證和授權系統。 

您可以使用 Azure AD Identity Protection 來設定偵測到與使用者身分識別相關的可疑動作的自動回應，例如使用 Azure 入口網站的控制平面上的帳戶登入行為偏差。 您也可將資料內嵌到 Azure Sentinel 中，以便進一步調查。 

- [如何設定您的 Azure App Service 應用程式以使用 Azure AD 登入](configure-authentication-provider-aad.md)

- [如何檢視有風險的 Azure AD 登入](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [如何設定和啟用身分識別保護風險原則](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13：在支援案例期間為 Microsoft 提供相關客戶資料的存取權

**指導** 方針： App Service 無法使用。 Azure App Service 不支援客戶加密箱。

- [客戶加密箱支援的服務清單](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導** 方針：使用標記來協助追蹤儲存或處理機密資訊 App Service 資源。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：針對 App Service 環境，在開發、測試和生產環境中執行不同的訂用帳戶、管理群組或兩者。 您可以用相同的方式，隔離處理來自其他應用程式之機密資訊的應用程式。 將您的 App Service 應用程式部署至虛擬網路。 使用網路安全性群組和子網進行進一步的應用程式隔離。 

App Service 環境 (ASE) 有兩種部署類型。 兩者都可讓您根據業務需求來隔離流量。

- 外部應用程式服務環境-在可存取網際網路的 IP 位址上公開 App Service 環境託管應用程式。

-  內部負載平衡器 (ILB) 應用程式服務環境-會在您虛擬網路內的 IP 位址上公開 App Service 環境的託管應用程式。 內部端點是 (ILB) 的內部負載平衡器，這也是為什麼它稱為 ILB ASE 的原因。 

若為多租使用者 App Service (不在隔離層) 中的應用程式，請使用虛擬網路整合，讓您的應用程式存取虛擬網路中的資源。  使用私人網站存取權，讓應用程式只能從私人網路（例如 Azure 虛擬網路內的私人網路）存取。 虛擬網路整合只會用來從您的應用程式對您的虛擬網路進行輸出呼叫。 虛擬網路整合功能與相同區域中的虛擬網路和其他區域中的虛擬網路搭配使用時，會有不同的行為。 
 
- [App Service 環境的網路考量](environment/network-info.md)

- [如何建立外部 ASE](environment/create-external-ase.md)

- [如何建立內部 ASE](environment/create-ilb-ase.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：雖然 App Service 的資料識別、分類和遺失防護功能尚無法使用，但您可以藉由移除目的地針對網際網路或 Azure 服務使用「標籤」的所有規則，來減少虛擬網路的資料遭到外泄風險。 

Microsoft 管理 App Service 的基礎結構，並已實行嚴格的控制，以防止資料遺失或洩漏。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：使用 tls 1.2 的預設最小版本（設定于 TLS/SSL 設定）來加密傳輸中的所有資訊。 也請確定所有 HTTP 連線要求都會重新導向至 HTTPS。

- [瞭解 Azure App Service web 應用程式傳輸中的加密](security-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：目前無法使用。 App Service 尚無法使用資料識別、分類和遺失防護功能。 

標記 App Service 可能正在處理敏感性資訊的應用程式。 視需要執行協力廠商解決方案，以符合合規性目的。

Microsoft 會管理基礎平臺，並將所有客戶資料視為機密資料，並將其移至絕佳的長度，以防止客戶資料遺失和暴露。 為了確保 Azure 中的客戶資料安全無虞，Microsoft 已實作並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：共用

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6：使用角色型存取控制來控制資源的存取權

**指導** 方針：使用角色型存取控制 (Azure Active Directory (Azure AD) 中的 Azure RBAC) 來控制 App Service 控制平面的存取。

- [如何在 Azure 中設定 RBAC](../role-based-access-control/role-assignments-portal.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針： App Service 應用程式中的網站內容（例如檔案）儲存在 Azure 儲存體中，這會自動加密待用內容。 選擇將應用程式秘密儲存在 Key Vault 中，並在執行時間加以取出。

客戶提供的秘密會在儲存于 App Service 設定資料庫時待用加密。

請注意，網站可以選擇性地使用本機連接的磁片作為暫存儲存體， (例如 D:\local 和% TMP% ) ，則不會在待用時加密。

- [瞭解 Azure App Service 的資料保護控制項](app-service-security-controls.md)

- [瞭解靜態加密 Azure 儲存體](../storage/common/storage-service-encryption.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：使用 Azure 監視器搭配 Azure 活動記錄，以在生產 App Service 應用程式和其他重要或相關資源的任何變更時建立警示。

- [如何建立 Azure 活動記錄事件的警示](../azure-monitor/platform/alerts-activity-log.md)

**Azure 資訊安全中心監視**：目前無法使用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1：執行自動化弱點掃描工具

**指導** 方針：採用 DevSecOps 實務，以確保您的 App Service 應用程式安全，並在其生命週期的持續期間保持安全。 DevSecOps 將您組織的安全性小組及其功能納入您的 DevOps 實務中，讓小組的每個人都能承擔安全性。

複習並遵循安全性中心的建議，以保護您的 App Service 應用程式。

- [如何將持續安全性驗證新增至 CI/CD 管線](https://docs.microsoft.com/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops&amp;preserve-view=true)

- [如何實行 Azure 資訊安全中心弱點評定建議](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針： Microsoft 會在支援 App Service 的基礎系統上執行弱點管理。 不過，您可以使用「安全性中心」內的建議嚴重性以及安全分數來測量環境內的風險。 您的安全分數取決於您已緩和的資訊安全中心建議數量。 若要優先解決建議，請考慮每一個建議的嚴重性。

- [安全性建議參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：使用 Azure Resource Graph 來查詢或探索 (的所有資源，例如計算、儲存體、網路、埠、通訊協定等，以及您訂用帳戶內的) 。 確定已將適當的許可權套用至您的租使用者，而且您可以列舉訂用帳戶中的所有 Azure 訂用帳戶及資源。

雖然可透過 Resource Graph 探索傳統 Azure 資源，但強烈建議您從現在開始建立並使用 Azure Resource Manager 資源。

- [如何使用 Azure Resource Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

- [如何檢視您的 Azure 訂用帳戶](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [了解 Azure RBAC](../role-based-access-control/overview.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="62-maintain-asset-metadata"></a>6.2：維護資產中繼資料

**指導** 方針：使用中繼資料將標記套用至 Azure 資源，以邏輯方式將它們組織成分類法。

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：依適當情況使用標記、管理群組和個別訂用帳戶，來組織及追蹤 Azure 資源。 定期協調清查，並確保您的訂用帳戶會在此過程中移除未經授權的資源。

選擇 [Azure 原則]，以使用下列內建原則定義，對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型
- 允許的資源類型

如需其他資訊，請參閱參考的連結。

- [如何建立額外的 Azure 訂閱](/azure/billing/billing-create-subscription)

- [如何建立管理群組](/azure/governance/management-groups/create)

- [如何建立和使用標籤](/azure/azure-resource-manager/resource-group-using-tags)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：根據您的組織需求，建立已核准 Azure 資源的清查，以及適用于計算資源的核准軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 原則對可在訂用帳戶中建立的資源類型施加限制。

使用 Azure Resource Graph 來查詢或探索其訂用帳戶內的資源。  確保已核准環境中的所有 Azure 資源。 

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md) 

- [如何使用 Azure Graph 建立查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：使用 Azure Resource Graph 來查詢或探索訂用帳戶內的資源，並確保已根據您的組織原則核准探索到的 Azure 資源。

使用 App Service 中的 Webjob 來監視在計算資源內部署的未核准軟體應用程式。 使用 Webjob 在與 web 應用程式、API 應用程式或行動應用程式相同的實例中執行程式或腳本。 使用記錄來定義 WebJob 設定和監視。 在 [WebJob 執行詳細資料] 分頁上，選取 [切換輸出] 以查看記錄內容的文字。 請注意，尚未支援 Linux 上的 App Service 的 Webjob。

- [在 Azure App Service 中使用 Webjob 執行背景工作](webjobs-create.md)

- [教學課程-建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [快速入門-使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：確認環境中的所有 Azure 資源都已獲核准。 使用 Azure 原則來限制可在訂用帳戶中建立的資源類型。 移除尚未依您的組織原則核准的任何已部署軟體應用程式。

- [在 Azure App Service 中使用 Webjob 執行背景工作](webjobs-create.md)

- [教學課程-建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [快速入門-使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針：確認環境中的所有 Azure 資源都已獲核准。 使用 Azure 原則來限制可在訂用帳戶中建立的資源類型。 移除尚未依您的組織原則核准的任何已部署軟體應用程式。 

- [在 Azure App Service 中使用 Webjob 執行背景工作](webjobs-create.md)

- [教學課程-建立和管理原則以強制執行合規性](../governance/policy/tutorials/create-and-manage.md)

- [快速入門-使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](../governance/resource-graph/first-query-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="69-use-only-approved-azure-services"></a>6.9：僅使用已核准的 Azure 服務

**指導** 方針：建立一個程式以定期檢查未經授權的 azure 服務，以確保您的訂用帳戶中只會使用已授權的 azure 服務。

在此程式中使用 Azure Resource Graph，以查詢或探索其訂用帳戶內的資源。 確保已核准環境中的所有 Azure 資源。

使用下列內建原則定義，設定 Azure 原則以限制可在訂用帳戶中建立的資源類型：

- 不允許的資源類型

- 允許的資源類型

使用 App Service 中的 Webjob 來監視電腦資源內部署的未核准軟體應用程式。 使用 Webjob 在與 web 應用程式、API 應用程式或行動應用程式相同的實例中執行程式或腳本。 使用記錄來定義 WebJob 設定和監視。 在 [WebJob 執行詳細資料] 分頁上，選取 [切換輸出] 以查看記錄內容的文字。 請注意，尚未支援 Linux 上的 App Service 的 Webjob。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/built-in-policies.md#general)

- [在 Azure App Service 中使用 Webjob 執行背景工作](webjobs-create.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導** 方針：在您的訂用帳戶中定期執行清查和審核軟體專案的程式，以確保您的訂用帳戶中只會使用已授權的 Azure 服務。

在此程式中使用 Azure Resource Graph，以查詢或探索訂用帳戶內的資源。 確定已核准在環境中探索到的所有 Azure 資源。

將 Azure 原則設定為使用下列內建原則定義，對可在客戶訂用帳戶中建立的資源類型施加限制：

- 不允許的資源類型

- 允許的資源類型

同樣地，您也可以使用 App Service 中的 Webjob，清查電腦資源內部署的未核准軟體應用程式。 使用記錄定義其設定和監視。 在 [WebJob 執行詳細資料] 分頁上，選取 [切換輸出] 以查看記錄內容的文字。 請注意，尚未支援 Linux 上的 App Service 的 Webjob。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [如何使用 Azure 原則拒絕特定的資源類型](../governance/policy/samples/built-in-policies.md#general)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11：限制使用者與 Azure Resource Manager 互動的能力

**指導** 方針：設定「Microsoft Azure 管理」應用程式的「封鎖存取」，設定 Azure 條件式存取以限制使用者與 Azure Resource Manager 互動的能力。

- [如何設定條件式存取以封鎖 Azure Resource Manager 的存取](../role-based-access-control/conditional-access-azure-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12：限制使用者在計算資源內執行指令碼的能力

**指導** 方針： App Service 中的 webjob 可讓客戶在與 web 應用程式、API 應用程式或行動應用程式相同的實例中執行程式或腳本。 您必須負責定義您的設定，以限制或限制組織不允許的任何腳本。 App Service 不提供以原生方式限制腳本執行的機制。 請注意，尚未支援 Linux 上的 App Service 的 Webjob。

- [在 Azure App Service 中使用 Webjob 執行背景工作](webjobs-create.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：執行個別的訂用帳戶或管理群組，以針對高風險 App Service 應用程式提供隔離。 將更高風險的應用程式部署到自己的虛擬網路，因為 App Service 的周邊安全性是透過使用虛擬網路來達成。 App Service 環境是將 App Service 部署至 Azure 虛擬網路中子網的部署。 

有兩種類型的應用程式服務環境、外部應用程式服務環境，以及 ILB (內部 Load Balancer) 應用程式服務環境。 根據您的需求選擇最佳架構。

- [App Service 環境的網路考量](environment/network-info.md)

- [建立外部 App Service 環境](environment/create-external-ase.md)

- [建立及使用內部負載平衡器 App Service 環境](environment/create-ilb-ase.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 原則為您 App Service 部署的應用程式定義和執行標準安全性設定。

使用 "Web.config" 命名空間中 Azure 原則別名來建立自訂原則，以審核或強制執行 App Service Web Apps 的設定。

套用內建原則定義，例如：
- App Service 應該使用虛擬網路服務端點
- Web 應用程式應該只能透過 HTTPS 存取

- 在您的應用程式中使用最新的 TLS 版本

建議您記載程式以套用內建原則定義，以用於標準化的使用方式。   

- [如何檢視可用的 Azure 原則別名](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：使用 Azure 原則 [拒絕] 和 [部署是否不存在] 效果，在您的 Azure App Service 應用程式中強制執行安全設定。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

- [瞭解 Azure 原則效果](../governance/policy/concepts/effects.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針：當您使用自訂 Azure 原則定義時，請選擇 Azure DevOps 或 Azure Repos 安全地儲存和管理您的程式碼。

使用您現有的持續整合 (CI) 和持續傳遞 (CD) 管線來部署已知安全的設定。

- [如何在 Azure DevOps 中儲存程式碼](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos 文件](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：使用內建的 Azure 原則定義以及 "Microsoft. Web" 命名空間中 Azure 原則的別名，建立自訂原則來警示、審核和強制執行系統設定。 開發用於管理原則例外狀況的處理常式和管線。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用內建的 Azure 原則定義以及 "Microsoft. Web" 命名空間中 Azure 原則的別名，建立自訂原則來警示、審核和強制執行系統設定。 

套用 Azure 原則 [audit]、[拒絕] 和 [部署（如果不存在）]，會自動強制執行 Azure 資源設定的效果。

- [如何設定和管理 Azure 原則](../governance/policy/tutorials/create-and-manage.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針：使用受控識別，在 Azure Active Directory (Azure AD) 中，為您的 App Service 應用程式提供自動管理的身分識別。 受控識別可讓您的應用程式向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不需要在您的程式碼中提供任何認證。 確定已在 Azure Key Vault 中啟用虛刪除。

- [如何在 Azure Key Vault 中啟用虛刪除](/azure/key-vault/key-vault-soft-delete-powershell)

- [如何使用受控識別進行 App Service](overview-managed-identity.md)

- [如何使用受控識別來提供 Key Vault authentication](/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="712-manage-identities-securely-and-automatically"></a>7.12：安全且自動地管理身分識別

**指導** 方針：使用受控識別，以 Azure Active Directory (Azure AD) 中的自動受控身分識別來提供您的 App Service 部署應用程式。 受控識別可讓您的應用程式向任何支援 Azure AD 驗證的服務進行驗證，包括 Key Vault，而不需要您程式碼中的任何認證。

- [如何使用受控識別進行 App Service](overview-managed-identity.md)

- [如何使用受控識別來提供 Key Vault authentication](/azure/key-vault/managed-identity)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指引**：實作認證掃描器來識別程式碼中的認證。 認證掃描器也有助於將探索到的認證移至更安全的位置，例如 Azure Key Vault。

- [如何設定認證掃描器](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-recovery"></a>資料復原

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料](../security/benchmarks/security-control-data-recovery.md)復原。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1：確定定期自動備份

**指導** 方針： App Service 中的備份和還原功能，可讓您以手動或依排程的方式輕鬆建立應用程式備份。 您可以將備份設定為無限期保留。 您可以透過覆寫現有的應用程式或還原到另一個應用程式，將應用程式還原到先前狀態的快照。

App Service 可以將下列資訊備份到您已設定應用程式使用的 Azure 儲存體帳戶和容器：
- 應用程式設定
- 檔案內容
- 已連線到您應用程式的資料庫

確定定期和自動備份會依照您的組織原則所定義的頻率發生。

- [瞭解 Azure App Service 備份功能](manage-backup.md)

- [Azure 儲存體加密的客戶管理金鑰](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-overview?toc=/azure/storage/blobs/toc.json)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2：執行完整的系統備份並備份任何客戶管理的金鑰

**指導** 方針：使用 App Service 的備份和還原功能來備份您的應用程式。 備份功能需要 Azure 儲存體帳戶才能儲存您應用程式的備份資訊。

- Azure 儲存體提供待用加密-使用系統提供的金鑰，或您自己的客戶管理金鑰。 當您的應用程式資料不是在 Azure 中的 web 應用程式中執行時，就會儲存應用程式資料。
- 從部署套件執行是 App Service 的部署功能。 它可讓您使用共用存取簽章 (SAS) URL，從 Azure 儲存體帳戶部署網站內容。

- Key Vault 參考是 App Service 的安全性功能。 它可讓您在執行時間將秘密匯入為應用程式設定。 使用此項來加密 Azure 儲存體帳戶的 SAS URL。

您可以在參考的連結取得詳細資訊。

- [在 Azure 中備份應用程式](manage-backup.md)

- [還原在 Azure App Service 中執行的應用程式](web-sites-restore.md)

- [瞭解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md#encryption-at-rest-in-microsoft-cloud-services) 

- [加密模型和金鑰管理表](../security/fundamentals/encryption-atrest.md)

- [使用客戶管理的金鑰進行待用加密](configure-encrypt-at-rest-using-cmk.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3：驗證所有備份，包括客戶管理的金鑰

**指導** 方針：定期測試 App Service 應用程式的任何備份的還原程式。

- [在 Azure 中備份應用程式](manage-backup.md)

- [如何還原 Azure App Service 的 web 應用程式](web-sites-restore.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4：確保備份和客戶管理的金鑰的保護

**指導** 方針： App Service 備份會儲存在 Azure 儲存體帳戶內。 Azure 儲存體中的資料會以透明的方式使用256位 AES 加密（可用的最強區塊編碼器之一）進行加密和解密，並符合 FIPS 140-2 規範。 Azure 儲存體加密類似于 Windows 上的 BitLocker 加密。

已針對所有儲存體帳戶啟用 Azure 儲存體加密，包括 Resource Manager 和傳統儲存體帳戶。 無法停用 Azure 儲存體加密。 由於預設會保護您的資料，因此您不需要修改您的程式碼或應用程式，即可利用 Azure 儲存體加密。

根據預設，儲存體帳戶中的資料會使用 Microsoft 管理的金鑰進行加密。 您可以依賴 Microsoft 管理的金鑰來加密您的資料，也可以使用您自己的金鑰管理加密。 確定已在 Azure Key Vault 中啟用虛刪除。

- [瞭解靜態資料的 Azure 儲存體加密](../storage/common/storage-service-encryption.md)

- [如何在 Azure Key Vault 中啟用虛刪除](/azure/key-vault/key-vault-soft-delete-powershell)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

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

此外，清楚地將訂用帳戶標示 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指導** 方針：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。

- [請參閱 NIST 的發行指南，以針對 IT 方案和功能進行測試、訓練和練習程式](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。  事後檢討事件，確保問題已解決。

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指導** 方針：使用連續匯出功能來匯出您的安全性中心警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 使用「安全性中心」資料連線器，根據商務需求來串流警示 Sentinel。

- [如何設定連續匯出](../security-center/continuous-export.md)

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指導** 方針：使用資訊安全中心的工作流程自動化功能，透過安全性警示和建議的「Logic Apps」來自動觸發回應。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指引**：遵循 Microsoft 參與規則，以確保滲透測試不會違反 Microsoft 原則： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

您可以在 microsoft 管理的雲端基礎結構、服務和應用程式中，找到關於 Microsoft 的策略和執行紅色小組和即時網站滲透測試的詳細資訊。

- [Microsoft Cloud Red 小組](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](/azure/security/benchmarks/overview)
- 深入了解 [Azure 資訊安全性基準](/azure/security/benchmarks/security-baselines-overview)
