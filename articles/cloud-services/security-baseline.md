---
title: 適用于 Azure 雲端服務的 Azure 安全性基準
description: Azure 雲端服務安全性基準提供的程式指引和資源，可讓您執行 Azure 安全性基準測試中所指定的安全性建議。
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 100871ab2188aa638c4c9f2b058021706c7741bb
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531899"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>適用于 Azure 雲端服務的 Azure 安全性基準

此安全性基準會將 [Azure 安全性基準測試版本 1.0](../security/benchmarks/overview-v1.md) 的指引套用至 Microsoft Azure 雲端服務。 Azure 安全性基準提供如何在 Azure 上保護雲端解決方案的建議。
內容會依 Azure 安全性基準測試所定義的 **安全性控制** ，以及適用于雲端服務的相關指引來分組。 已排除不適用雲端服務的 **控制項**。

 
若要瞭解雲端服務如何完全對應至 Azure 安全性基準測試，請參閱 [完整的雲端服務安全性基準對應](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)檔案。

## <a name="network-security"></a>網路安全性

*如需詳細資訊，請參閱 [Azure 安全性基準測試：網路安全性](../security/benchmarks/security-control-network-security.md)。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1：保護虛擬網路內的 Azure 資源

**指導** 方針：建立具有個別公用和私人子網的傳統 Azure 虛擬網路，以根據受信任的埠和 IP 範圍強制進行隔離。 這些虛擬網路和子網必須是傳統虛擬網路， (傳統部署) 資源，而不是目前的 Azure Resource Manager 資源。  

使用網路安全性群組來允許或拒絕流量，此群組包含以流量方向、通訊協定、來源位址和埠，以及目的地位址和埠為基礎的存取控制規則。 您可以隨時變更網路安全性群組的規則，並將變更套用至所有相關聯的實例。

Microsoft Azure 雲端服務 (傳統) 不能放在 Azure Resource Manager 的虛擬網路中。 不過，以 Resource Manager 為基礎的虛擬網路和傳統部署型虛擬網路可以透過對等互連來連線。 

- [網路安全性群組概觀](../virtual-network/network-security-groups-overview.md)

- [虛擬網路對等互連](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2：監視和記錄虛擬網路、子網和 Nic 的設定和流量

**指導** 方針：記錄您的 Azure 雲端服務設定，並監視其變更。 使用服務的設定檔來指定要為服務中每個角色部署的角色實例數目、任何設定設定的值，以及與角色相關聯之任何憑證的指紋。 

如果服務是虛擬網路的一部分，則必須在服務設定檔和虛擬網路設定檔中提供網路的設定資訊。 服務組態檔的預設副檔名為 .cscfg。 請注意，不支援將 Azure 原則用於設定強制執行的傳統部署。

在服務設定檔中設定雲端服務的設定值 ( .cscfg) 以及服務定義中的定義 ( 的已配置) 檔案。 使用服務定義檔定義應用程式的服務模型。 定義可供雲端服務使用的角色，同時也會指定服務端點。 記錄服務設定檔 Azure 雲端服務的設定。 任何重新設定都可以透過 ServiceConfig .cscfg 檔案來完成。 

監視選用的 NetworkTrafficRules 元素服務定義，以限制哪些角色可以與指定的內部端點通訊。 設定 NetworkTrafficRules 節點（服務定義檔中的選擇性專案），以指定角色應該如何彼此通訊。 限制哪些角色可以存取特定角色的內部端點。  請注意，無法更改服務定義。 

啟用網路安全性群組流量記錄，並將記錄傳送至 Azure 儲存體帳戶進行審核。 將流量記錄傳送至 Log Analytics 工作區，並使用流量分析來提供 Azure 租使用者中流量模式的深入解析。 流量分析的一些優點是能夠將網路活動視覺化、找出熱點和安全性威脅、瞭解流量流程模式，以及找出網路錯誤配置。

- [Azure Resource Manager 與傳統部署-瞭解部署模型和資源的狀態](../azure-resource-manager/management/deployment-models.md)

- [雲端服務設定檔案](schema-cscfg-file.md)

- [Azure 原則支援的服務清單](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="13-protect-critical-web-applications"></a>1.3：保護重要的 Web 應用程式

**指導** 方針： Microsoft 會使用傳輸層安全性 (TLS) 通訊協定 v 1.2，以在 Azure 雲端服務與客戶之間流動時保護資料。 Microsoft 資料中心會與連線到 Azure 服務的用戶端系統進行交涉以達成 TLS 連線。 TLS 提供增強式驗證、訊息隱私權、完整性 (可偵測訊息竄改、攔截和偽造)、互通性、演算法彈性，以及方便部署和使用。

- [加密基本概念](../security/fundamentals/encryption-overview.md)

- [設定 TLS/SSL 憑證](cloud-services-configure-ssl-certificate-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4：拒絕與已知惡意 IP 位址的通訊

**指導** 方針： Azure Cloud 會執行多層式網路安全性，以保護其平臺服務免于分散式阻斷服務 (DDoS) 攻擊。 Azure DDoS 保護是 Azure 雲端持續監視流程的一部分，可透過滲透測試持續改善。 此 DDoS 保護的設計目的是要防止來自外部的攻擊，但也不能從其他 Azure 租使用者。 

除了 Azure 雲端服務內的平台層級保護之外，有幾種不同的方式可以封鎖或拒絕通訊。 這些警告是： 

-  建立啟動工作，選擇性地封鎖一些特定的 IP 位址
-  藉由修改 IIS web.config 檔案，將 Azure web 角色存取許可權制為一組指定的 IP 位址

防止連入流量指向預設 URL 或您的雲端服務名稱，例如， *cloudapp.net。在雲端服務定義中的 [網站系結設定] 下，將主機標頭設定為自訂 DNS 名稱， (*]) 檔案。

將拒絕套用規則設定為傳統訂用帳戶系統管理員指派。 根據預設，定義內部端點之後，來自任何角色的通訊都可以在沒有任何限制的情況下流向角色的內部端點。 若要限制通訊，您必須在服務定義檔中將 NetworkTrafficRules 元素加入至 ServiceDefinition 元素。

- [如何封鎖/停用我雲端服務的預設 URL 的連入流量](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Azure DDOS 保護](./cloud-services-connectivity-and-networking-faq.md?preserve-view=true#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [封鎖特定的 IP 位址](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="15-record-network-packets"></a>1.5：記錄網路封包

**指導** 方針：使用 azure 網路監看員、網路效能監視、診斷和分析服務，以允許監視 Azure 網路。 網路監看員代理程式虛擬機器擴充功能是在需要時，需要在 Azure 虛擬機器上捕獲網路流量和其他先進功能的需求。 安裝網路監看員代理程式虛擬機器擴充功能，並開啟網路安全性群組流量記錄。

設定網路安全性群組的流量記錄。 查看如何將網路監看員虛擬機器擴充功能部署至透過傳統部署模型部署的現有虛擬機器的詳細資料。

- [設定網路安全性群組的流量記錄](../virtual-machines/extensions/network-watcher-linux.md)

- [如需設定流程記錄的詳細資訊，請造訪](/cli/azure/azure-services-the-azure-cli-can-manage?preserve-view=)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6：部署以網路為基礎的入侵偵測/入侵防護系統 (IDS/IPS) 

**指導** 方針： Azure 雲端服務沒有內建的識別碼或 ip 功能。 客戶可以根據組織需求，從 Azure Marketplace 選取並部署補充的網路型識別碼或 IP 解決方案。 使用協力廠商解決方案時，請務必使用 Azure 雲端服務徹底測試您選取的識別碼或 IP 解決方案，以確保適當的作業和功能。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="17-manage-traffic-to-web-applications"></a>1.7：管理 Web 應用程式的流量

**指導** 方針：連接至 Azure 雲端服務的服務憑證，可啟用與服務的安全通訊。 這些憑證是在服務定義中定義，並且會自動部署到執行 web 角色實例的虛擬機器。 例如，針對 web 角色，您可以使用可驗證公開 HTTPS 端點的服務憑證。 

若要更新憑證，只需要上傳新的憑證，並變更服務設定檔中的憑證指紋值即可。

使用 TLS 1.2 通訊協定，這是最常用來保護資料安全的方法，以提供機密性和完整性保護。 

一般而言，若要保護 web 應用程式並保護它們免于遭受像是 OWASP 前10個攻擊的攻擊，您可以部署已啟用 Azure Web 應用程式防火牆的 Azure 應用程式閘道來保護 web 應用程式。 

- [服務憑證](cloud-services-certs-create.md)

- [在 Azure 中設定應用程式的 TLS](cloud-services-configure-ssl-certificate-portal.md)

- [如何部署應用程式閘道](../application-gateway/quick-create-portal.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9：維護網路裝置的標準安全性設定

**指導** 方針：強化您的 Azure 雲端服務設定，並監視其是否有變更。 服務組態檔會指定要為服務中每個角色部署的角色執行個體數目、任何組態設定的值，以及與角色相關聯之任何憑證的指紋。 

如果您的服務是虛擬網路的一部分，則必須在服務設定檔和虛擬網路設定檔中提供網路的設定資訊。 服務組態檔的預設副檔名為 .cscfg。

請注意，設定強制執行 Azure 雲端服務不支援 Azure 原則。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="110-document-traffic-configuration-rules"></a>1.10：文件流量設定規則

**指導** 方針： azure 網路安全性群組可以用來篩選 Azure 虛擬網路中的 azure 資源的網路流量。 網路安全性群組包含安全性規則，可允許或拒絕來自數種 Azure 資源類型的連入網路流量或輸出網路流量。 您可以為每個規則指定來源和目的地、連接埠及通訊協定。

針對 Azure 雲端服務內的個別網路安全性群組規則使用 [描述] 欄位，以記錄允許流量或從網路傳送的規則。

- [如何使用網路安全性群組規則來篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11：使用自動化工具來監視網路資源設定並偵測變更

**指導** 方針：使用 Azure 流量管理員的內建端點監視和自動端點容錯移轉功能。 這些應用程式可協助您提供高可用性應用程式，這些應用程式可在端點和 Azure 區域失敗時復原。 若要設定端點監視，您必須在流量管理員設定檔上指定特定設定。

從活動記錄檔（Azure 中的平臺記錄檔）收集至訂用帳戶層級事件的見解。 它包含資源修改或虛擬機器啟動時的資訊。 在 Azure 入口網站中查看活動記錄，或使用 PowerShell 和 CLI 來取得專案。 

建立診斷設定，以將活動記錄傳送至 Azure 監視器、Azure 事件中樞轉送至 Azure 外部，或用於封存的 Azure 儲存體。 設定 Azure 雲端服務中的重要資源變更時通知警示的 Azure 監視器。 

- [Azure 活動記錄](../azure-monitor/platform/activity-log.md)

- [使用 Azure 監視器中建立、檢視及管理活動記錄警示](../azure-monitor/platform/alerts-activity-log.md)

- [流量管理員監視](../traffic-manager/traffic-manager-monitoring.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="logging-and-monitoring"></a>記錄和監視

*如需詳細資訊，請參閱 [Azure 安全性基準測試：記錄和監視](../security/benchmarks/security-control-logging-monitoring.md)。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1：使用已核准的時間同步處理來源

**指導** 方針： Microsoft 會維護適用于 Azure 雲端服務的 Azure 資源的時間來源。 客戶可能需要建立網路規則，以允許存取其環境中使用的時間伺服器，透過 UDP 通訊協定的埠123。

- [NTP 伺服器存取](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Azure 資訊安全中心監視**：是

**責任**：共用

### <a name="22-configure-central-security-log-management"></a>2.2：設定中央安全性記錄管理

**指導** 方針：使用 Azure 事件中樞以程式設計方式取用您的雲端服務串流資料。 將這些資料整合並傳送給 Azure Sentinel，以監視和檢查您的記錄，或使用協力廠商 SIEM。 針對中央安全性記錄檔管理，請將所選 Azure 資訊安全中心資料的連續匯出設定為 Azure 事件中樞，並為您的 SIEM 設定適當的連接器。 以下是一些 Azure Sentinel 的選項，包括協力廠商工具：

- Azure Sentinel-使用原生的安全性中心警示資料連線器
- Splunk-使用適用于 Splunk 的 Azure 監視器附加元件
- IBM QRadar-使用手動設定的記錄來源
- ArcSight –使用 SmartConnector

請參閱 Azure Sentinel 檔，以取得使用 Azure Sentinel 之可用連接器的其他詳細資料。 

- [連線資料來源](../sentinel/connect-data-sources.md)

- [與 SIEM 整合](../security-center/continuous-export.md)

- [儲存診斷資料](diagnostics-extension-to-storage.md)

- [透過 Azure 事件中樞設定 SIEM 整合](../security-center/continuous-export.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3：啟用 Azure 資源的稽核記錄

**指導** 方針：設定 Visual Studio 設定 Azure 診斷以進行疑難排解 Azure 雲端服務這會在虛擬機器上捕捉系統和記錄資料，包括執行 Azure 雲端服務的虛擬機器實例。 診斷資料會傳輸到您選擇的儲存體帳戶。 在部署之前，先在 Azure 雲端服務專案中開啟診斷。

 
在 Azure 監視器的活動記錄中，查看某些事件的變更歷程記錄。 Audit 事件期間發生的變更。 從活動記錄中選擇事件，以利用變更記錄 (預覽) 索引標籤進行更深入的檢查。當您從 Visual Studio 發行 Azure 雲端服務時，將診斷資料傳送至 Application Insights。 在該時間建立 Application Insights 的 Azure 資源，或將資料傳送至現有的 Azure 資源。 

Azure 雲端服務可以透過 Application Insights 來監視可用性、效能、失敗和使用方式。 自訂圖表可以新增至 Application Insights，因此您可以看到最重要的資料。 您可以使用 Azure 雲端服務專案中的 Application Insights SDK 來收集角色實例資料。 

- [在部署之前開啟 Visual Studio 中的診斷](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [檢視變更歷程記錄](../azure-monitor/platform/activity-log.md#view-change-history)

- [適用于 Azure 雲端服務 (傳統) 的 Application Insights ](../azure-monitor/app/cloudservices.md)

- [設定 Azure 雲端服務 (傳統) 和虛擬機器的診斷](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?preserve-view=true&toc=%2fazure%2fcloud-services%2ftoc.json)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="25-configure-security-log-storage-retention"></a>2.5：設定安全性記錄儲存體保留期

**指導** 方針：您可以使用 advanced monitoring 搭配 Azure 雲端服務，以在5分鐘、1小時和12小時的間隔內取樣和收集其他計量。 匯總的資料會儲存在儲存體帳戶的資料表中，並在10天后清除。 不過，使用的儲存體帳戶是由角色設定，而您可以針對不同的角色使用不同的儲存體帳戶。 這是使用 .csdef 和 .cscfg 檔案中的連接字串所設定。

請注意，Advanced monitoring 需要使用 Azure 診斷擴充功能 (Application Insights SDK 是您想要監視之角色的選擇性) 。 診斷延伸模組會使用名為 diagnostics.wadcfgx 的設定檔 (每個角色)，來設定所監視的診斷計量。 Azure 診斷延伸模組會收集資料並且儲存在 Azure 儲存體帳戶。 這些設定是在 .wadcfgx、.csdef 和 .cscfg 檔案中設定。

- [雲端服務監視簡介](cloud-services-how-to-monitor.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="26-monitor-and-review-logs"></a>2.6：監視和檢閱記錄

**指導** 方針：適用于 Azure 雲端服務的基本或 advanced 監視模式。 Azure 雲端服務會自動從主機虛擬機器收集基本監視資料 (CPU 百分比、網路進出/輸出，以及磁片讀取/寫入) 。 在 Azure 入口網站的雲端服務的 [總覽] 和 [計量] 頁面上，查看收集到的監視資料。 

使用 Azure 診斷擴充功能時，在 Azure 雲端服務中啟用診斷以收集診斷資料（例如應用程式記錄、效能計數器等）。 啟用或更新已在 Set-AzureServiceDiagnosticsExtension Cmdlet 執行的雲端服務上的診斷設定，或自動部署具有診斷擴充功能的雲端服務。 （選擇性）安裝 Application Insights SDK。 將效能計數器傳送至 Azure 監視器。

Azure 診斷延伸模組會收集資料並且儲存在 Azure 儲存體帳戶。 將診斷資料傳輸至 Microsoft Azure 儲存體模擬器或 Azure 儲存體，因為它不會永久儲存。 一旦儲存之後，您可以使用數個可用工具的其中一種來查看，例如 Visual Studio Microsoft Azure 儲存體總管、Azure Management Studio 中的伺服器總管。 設定診斷計量，以使用每個角色)  (的設定檔來監視診斷延伸模組中 diagnostics.wadcfgx 的名稱。 

- [雲端服務監視簡介](cloud-services-how-to-monitor.md)

- [如何在背景工作角色中啟用診斷-與 SIEM 整合](../security-center/continuous-export.md)

- [使用 PowerShell 在 Azure 雲端服務中啟用診斷](cloud-services-diagnostics-powershell.md)

- [在 Azure 儲存體中儲存和檢視診斷資料](diagnostics-extension-to-storage.md?&amp;preserve-view=true)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7：啟用異常活動的警示

**指導** 方針：您可以啟用異常活動的警示，藉由與 Azure Sentinel 整合，或使用協力廠商 SIEM 來監視 Azure 雲端服務記錄資料。

- [與 SIEM 整合](../security-center/continuous-export.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="28-centralize-anti-malware-logging"></a>2.8：集中化反惡意程式碼記錄

**指導** 方針：適用于 Azure 的 Microsoft Antimalware 可保護 Azure 雲端服務和虛擬機器。 您可以選擇部署協力廠商安全性解決方案，例如 web 應用程式的火警、網路防火牆、反惡意程式碼、入侵偵測和防護系統 (識別碼或 IP) 等等。

- [Azure 基本 IP/識別碼和 DDOS 提供的特性和功能有哪些](./cloud-services-configuration-and-management-faq.md?preserve-view=true#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="identity-and-access-control"></a>身分識別與存取控制

*如需詳細資訊，請參閱 [Azure 安全性基準測試：身分識別與存取控制](../security/benchmarks/security-control-identity-access-control.md)。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1：維護系統管理帳戶的清查

**指導** 方針： Microsoft 建議您使用 azure RBAC)  (azure 角色型存取控制來管理對 azure 資源的存取。 不過 Azure 雲端服務並不支援 Azure RBAC 模型，因為它不是以 Azure Resource Manager 為基礎的服務，而您必須使用傳統訂用帳戶

依預設，帳戶管理員、服務管理員和 Co-Administrator 是 Azure 中的三個傳統訂用帳戶管理員角色。 

傳統訂用帳戶管理員具有 Azure 訂用帳戶的完整存取權。 他們可以使用 Azure 入口網站、Azure Resource Manager API 和傳統部署模型 API 來管理資源。 用來註冊 Azure 帳戶會自動設定為帳戶管理員和服務管理員。 額外的 Co-Administrators 可以稍後再新增。 

服務管理員和 Co-Administrators 對於已獲指派「擁有者」角色的使用者，在訂用帳戶範圍 (Azure 角色) 的許可權。 使用 Azure 入口網站的 [傳統系統管理員] 索引標籤來管理 Co-Administrators 或查看服務系統管理員。 

使用下列命令，列出傳統服務管理員和共同管理員搭配 PowerShell 的角色指派：

Get-AzRoleAssignment-IncludeClassicAdministrators

請參閱傳統訂用帳戶系統管理角色之間的差異。 

- [三個傳統訂用帳戶系統管理角色之間的差異](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="33-use-dedicated-administrative-accounts"></a>3.3：使用專用的系統管理帳戶

**指導** 方針：建議您根據可用的角色和操作及管理 Azure 雲端服務資源所需的許可權，建立有關使用專用系統管理帳戶的標準作業程式。

- [傳統訂用帳戶系統管理角色之間的差異](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4：使用單一登入 (SSO) 搭配 Azure Active Directory

**指導** 方針：避免針對在 Azure 雲端服務上執行的應用程式管理個別的身分識別。 執行單一登入，以避免要求使用者管理多個身分識別和認證。

- [什麼是單一登入 (SSO) ](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6：使用專用電腦 (特殊權限存取工作站) 進行所有系統管理工作

**指導** 方針：建議使用安全且受 Azure 管理的工作站 (也稱為特殊許可權存取工作站) 管理工作，其需要較高的許可權。

- [瞭解安全、受 Azure 管理的工作站](../active-directory/devices/concept-azure-managed-workstation.md)

- [如何啟用 Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="data-protection"></a>資料保護

*如需詳細資訊，請參閱 [Azure 安全性基準測試：資料保護](../security/benchmarks/security-control-data-protection.md)。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1：維護敏感性資訊的清查

**指導** 方針：使用 Azure 雲端服務 REST api 來清查您的 Azure 雲端服務資源，以取得機密資訊。 輪詢已部署的雲端服務資源，以取得設定和 pkg 資源。

 例如，以下列出幾個 Api：

- 取得部署：取得部署作業會傳回部署的設定資訊、狀態和系統屬性。
- 取得套件-取得封裝作業會抓取部署的雲端服務封裝，並將封裝檔案儲存在 Microsoft Azure Blob 儲存體中
- 取得雲端服務屬性-取得雲端服務屬性作業可取得指定之雲端服務的屬性

請參閱 Azure 雲端服務 REST Api 檔，並根據您的組織需求建立機密資訊之資料保護的處理常式。

- [取得部署](/rest/api/compute/cloudservices/rest-get-deployment)

- [取得雲端服務屬性](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [取得封裝](/rest/api/compute/cloudservices/rest-get-package)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2：隔離儲存或處理敏感性資訊的系統

**指導** 方針：針對個別的安全性網域（例如 Azure 雲端服務的環境類型和資料敏感度層級），使用個別的訂用帳戶和管理群組來執行隔離。

您也可以在 Azure 雲端服務的憑證元素中編輯 "permissionLevel"，以指定提供給角色進程的存取權限。 如果您只希望提高許可權的進程能夠存取私密金鑰，請指定較高的許可權。 limitedOrElevated 許可權可讓所有角色進程存取私密金鑰。 可能的值為 limitedOrElevated 或提高許可權。 預設值為 limitedOrElevated。

- [如何建立額外的 Azure 訂閱](../cost-management-billing/manage/create-subscription.md)

- [如何建立管理群組](../governance/management-groups/create-management-group-portal.md)

- [WebRole 結構描述](schema-csdef-webrole.md#Certificate)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3：監視並封鎖未經授權的敏感性資訊傳輸

**指導** 方針：建議您從網路周邊的 Azure Marketplace 使用協力廠商解決方案，以監視是否有未經授權的機密資訊傳輸，並在警示資訊安全專業人員時封鎖這類傳輸。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4：加密傳輸中的所有敏感性資訊

**指導** 方針：設定 AZURE 雲端服務的 TLS v2。 使用 Azure 入口網站將憑證新增至您的暫存 Azure 雲端服務部署，然後將憑證資訊新增至服務的配置和 .CSCFG 檔案。 重新封裝您的應用程式，並更新您的預備部署以使用新的套件。 

在 Azure 中使用附加至 Azure 雲端服務的服務憑證，以啟用與服務之間的安全通訊。 提供可驗證公開的 HTTPS 端點的憑證。 在雲端服務的服務定義中定義服務憑證，並將其自動部署到虛擬機器，以執行您的角色實例。

使用管理憑證來向管理 API 進行驗證) 管理憑證可讓您使用傳統部署模型進行驗證。 許多程式和工具 (例如 Visual Studio 或 Azure SDK) 都會使用這些憑證，將各種 Azure 服務的設定與部署自動化。 

如需其他參考，Azure 服務管理 API 可讓您以程式設計方式存取透過 Azure 入口網站提供的服務管理功能。 適用于 Python 的 Azure SDK 可以用來管理 Azure 雲端服務和 Azure 儲存體帳戶。 適用于 Python 的 Azure SDK 會包裝服務管理 API，REST API。 所有 API 作業都是透過 TLS 執行，並使用 x.509 v3 憑證相互驗證。 您可以從在 Azure 中執行的服務內存取此管理服務。 此外，也可以透過網際網路，從任何可傳送 HTTPS 要求和接收 HTTPS 回應的應用程式，直接存取此管理服務。

- [在 Azure 中設定應用程式的 TLS](cloud-services-configure-ssl-certificate-portal.md)

- [從 Python 使用服務管理](cloud-services-python-how-to-use-service-management.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5：使用作用中探索工具來識別敏感性資料

**指導** 方針：建議使用協力廠商主動式探索工具來識別組織技術系統儲存、處理或傳輸的所有機密資訊，包括位於現場或遠端服務提供者的機密資訊，然後更新組織的機密資訊清查。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7：使用主機型資料外洩防護來強制執行存取控制

**指導** 方針：不適用於雲端服務 (傳統) 。 它不會強制防止資料遺失。

建議您執行協力廠商工具（例如自動化的主機型資料遺失防護解決方案），以對資料強制執行存取控制，即使是從系統複製資料時也一樣。

針對 Microsoft 管理的基礎平台，Microsoft 會將所有客戶內容視為敏感性資訊，並竭盡全力防範客戶資料外洩和暴露。 為了確保 Azure 中的客戶資料保持安全，Microsoft 已實行並維護一套強大的資料保護控制項和功能。

- [瞭解 Azure 中的客戶資料保護](../security/fundamentals/protection-customer-data.md)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8：加密待用的敏感性資訊

**指導** 方針： Azure 雲端服務不支援靜態加密。 這是因為 Azure 雲端服務是設計成無狀態的。 Azure 雲端服務支援外部儲存體，例如，Azure 儲存體，預設會在待用時加密。  

暫存磁片中儲存的應用程式資料不會加密。 客戶必須視需要負責管理和加密此資料。  

- [了解 Azure 中的待用加密](../security/fundamentals/encryption-atrest.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9：針對重要 Azure 資源的變更留下記錄和發出警示

**指導** 方針：您可以在 Azure 監視器中使用傳統計量警示，以在您的其中一個計量套用至超過閾值的重要資源時收到通知。 傳統計量警示是一種較舊的功能，只允許在非維度計量上發出警示。 現有的較新功能稱為計量警示，和傳統計量警示相比，功能更強。 

此外，Application Insights 可以監視可用性、效能、失敗和使用方式的 Azure 雲端服務應用程式。 這會使用來自 Application Insights Sdk 的合併資料與 Azure 雲端服務的 Azure 診斷資料。

- [使用 Azure 監視器建立、查看及管理傳統計量警示](../azure-monitor/platform/alerts-classic-portal.md)

- [計量警示總覽](../azure-monitor/platform/alerts-metric-overview.md) 

- [適用于 Azure 雲端服務 (傳統) 的 Application Insights ](../azure-monitor/app/cloudservices.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="vulnerability-management"></a>弱點管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：弱點管理](../security/benchmarks/security-control-vulnerability-management.md)。*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2：部署自動化的作業系統修補程式管理解決方案

**指導** 方針：請注意，此資訊與使用「平臺即服務」 (PaaS) 的 Azure 雲端服務背景工作角色和 web 角色的 Azure 客體作業系統相關。 但是，它並不適用于基礎結構即服務 (IaaS) 的虛擬機器。

根據預設，Azure 會定期將客戶的客體作業系統更新為作業系統系列內的最新支援映射（這些映射已在其服務設定中指定 ( .cscfg) ，例如，Windows Server 2016）。

當客戶為其 Azure 雲端服務部署選擇特定的作業系統版本時，它會停用自動作業系統更新，並修補其責任。 客戶必須確定其角色實例正在接收更新，或可能會將其應用程式公開至安全性弱點。

- [Azure 客體作業系統](cloud-services-guestos-msrc-releases.md)

- [Azure 客體作業系統可支援性和淘汰原則](cloud-services-guestos-retirement-policy.md)

- [如何設定雲端服務 (傳統) ](cloud-services-how-to-configure-portal.md)

- [管理客體作業系統版本](cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3：為協力廠商軟體專案部署自動化的修補程式管理解決方案

**指導** 方針：使用協力廠商修補程式管理解決方案。 已在環境中使用 Configuration Manager 的客戶也可以使用 System Center Updates Publisher，讓他們能夠將自訂更新發佈到 Windows Server Update 服務。 

這可讓更新管理修補使用 Configuration Manager 作為其更新存放庫（具有協力廠商軟體）的機器。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5：使用風險評等程序來排定所發現弱點的補救優先順序

**指導** 方針：建議客戶定期從 DDoS 攻擊瞭解其風險的範圍。 

我們建議您考慮下列案例：

- 有哪些公開使用的 Azure 新資源需要保護？
- 服務中有單一失敗點嗎？
- 如何將服務隔離以限制攻擊所產生的影響，同時仍能讓有效的客戶可以使用服務？
- 是否有虛擬網路應啟用標準 DDoS 保護，但尚未啟用？
- 我的服務是否會在跨多個區域進行容錯移轉時處於主動/主動狀態？

支援檔：

- [Azure 資源的風險評估](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="inventory-and-asset-management"></a>清查和資產管理

*如需詳細資訊，請參閱 [Azure 安全性基準測試：清查和資產管理](../security/benchmarks/security-control-inventory-asset-management.md)。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1：使用自動化資產探索解決方案

**指導** 方針：不適用 Azure 雲端服務。 這項建議適用于 IaaS 計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="63-delete-unauthorized-azure-resources"></a>6.3：刪除未經授權的 Azure 資源

**指導** 方針：建議定期協調清查，並確保及時從訂用帳戶中刪除未經授權的資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4：定義和維護已核准 Azure 資源的清查

**指導** 方針：客戶應為計算資源定義核准的 Azure 資源和已核准的軟體。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5：監視未經核准的 Azure 資源

**指導** 方針：使用 Azure 資訊安全中心中提供的適應性應用程式控制功能。 它是資訊安全中心的智慧型自動化端對端解決方案，可協助您控制哪些應用程式可以在您的 Windows 和 Linux、Azure 和非 Azure 電腦上執行。 它也有助於強化您的機器免于惡意程式碼的侵害。 

這項功能適用于 Azure 和非 Azure Windows (所有版本、傳統或 Azure Resource Manager) 和 Linux 機器。

資訊安全中心會使用機器學習來分析在您電腦上執行的應用程式，並從這個智慧建立允許清單。 這項功能可大幅簡化設定和維護應用程式允許清單原則的流程，讓您能夠：
- 封鎖執行惡意應用程式的嘗試或提出警示，包括反惡意程式碼解決方案可能遺漏的嘗試。

- 符合您組織規定只能使用授權軟體的安全性原則。
- 避免在您的環境中使用不必要的軟體。
- 避免執行舊版和不支援的應用程式。
- 阻止您的組織不允許的特定軟體工具。
- 讓 IT 能夠透過應用程式使用量來控制敏感性資料的存取。

您可以在參考的連結中找到更多詳細資料。

- [自適性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6：監視計算資源內未經核准的軟體應用程式

**指導** 方針：使用 Azure 資訊安全中心中提供的適應性應用程式控制功能。 它是資訊安全中心的智慧型自動化端對端解決方案，可協助您控制哪些應用程式可以在您的 Windows 和 Linux、Azure 和非 Azure 電腦上執行。 它也有助於強化您的機器免于惡意程式碼的侵害。 

這項功能適用于 Azure 和非 Azure Windows (所有版本、傳統或 Azure Resource Manager) 和 Linux 機器。

資訊安全中心會使用機器學習來分析在您電腦上執行的應用程式，並從這個智慧建立允許清單。 這項功能可大幅簡化設定和維護應用程式允許清單原則的流程，讓您能夠：

- 封鎖執行惡意應用程式的嘗試或提出警示，包括反惡意程式碼解決方案可能遺漏的嘗試。

- 符合您組織規定只能使用授權軟體的安全性原則。

- 避免在您的環境中使用不必要的軟體。

- 避免執行舊版和不支援的應用程式。

- 阻止您的組織不允許的特定軟體工具。

- 讓 IT 能夠透過應用程式使用量來控制敏感性資料的存取。

您可以在參考的連結中找到更多詳細資料。

- [自適性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7：移除未經核准的 Azure 資源和軟體應用程式

**指導** 方針：使用 Azure 資訊安全中心中提供的適應性應用程式控制功能。 它是資訊安全中心的智慧型自動化端對端解決方案，可協助您控制哪些應用程式可以在您的 Windows 和 Linux、Azure 和非 Azure 電腦上執行。 它也有助於強化您的機器免于惡意程式碼的侵害。 

這項功能適用于 Azure 和非 Azure Windows (所有版本、傳統或 Azure Resource Manager) 和 Linux 機器。

資訊安全中心會使用機器學習來分析在您電腦上執行的應用程式，並從這個智慧建立允許清單。 這項功能可大幅簡化設定和維護應用程式允許清單原則的流程，讓您能夠：

- 封鎖執行惡意應用程式的嘗試或提出警示，包括反惡意程式碼解決方案可能遺漏的嘗試。

- 符合您組織規定只能使用授權軟體的安全性原則。

- 避免在您的環境中使用不必要的軟體。

- 避免執行舊版和不支援的應用程式。

- 阻止您的組織不允許的特定軟體工具。

- 讓 IT 能夠透過應用程式使用量來控制敏感性資料的存取。

您可以在參考的連結中找到更多詳細資料。

- [自適性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="68-use-only-approved-applications"></a>6.8：僅使用已核准的應用程式

**指導** 方針：使用 Azure 資訊安全中心中提供的適應性應用程式控制功能。 它是資訊安全中心的智慧型自動化端對端解決方案，可協助您控制哪些應用程式可以在您的 Windows 和 Linux、Azure 和非 Azure 電腦上執行。 它也有助於強化您的機器免于惡意程式碼的侵害。 

這項功能適用于 Azure 和非 Azure Windows (所有版本、傳統或 Azure Resource Manager) 和 Linux 機器。

資訊安全中心會使用機器學習來分析在您電腦上執行的應用程式，並從這個智慧建立允許清單。 這項功能可大幅簡化設定和維護應用程式允許清單原則的流程，讓您能夠：

- 封鎖執行惡意應用程式的嘗試或提出警示，包括反惡意程式碼解決方案可能遺漏的嘗試。

- 符合您組織規定只能使用授權軟體的安全性原則。

- 避免在您的環境中使用不必要的軟體。

- 避免執行舊版和不支援的應用程式。

- 阻止您的組織不允許的特定軟體工具。

- 讓 IT 能夠透過應用程式使用量來控制敏感性資料的存取。

您可以在參考的連結中找到更多詳細資料。

- [自適性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10：維護已核准軟體標題的清查

**指導** 方針：使用 Azure 資訊安全中心中提供的適應性應用程式控制功能。 它是資訊安全中心的智慧型自動化端對端解決方案，可協助您控制哪些應用程式可以在您的 Windows 和 Linux、Azure 和非 Azure 電腦上執行。 它也有助於強化您的機器免于惡意程式碼的侵害。 

這項功能適用于 Azure 和非 Azure Windows (所有版本、傳統或 Azure Resource Manager) 和 Linux 機器。

資訊安全中心會使用機器學習來分析在您電腦上執行的應用程式，並從這個智慧建立允許清單。 這項功能可大幅簡化設定和維護應用程式允許清單原則的流程，讓您能夠：
- 封鎖執行惡意應用程式的嘗試或提出警示，包括反惡意程式碼解決方案可能遺漏的嘗試。

- 符合您組織規定只能使用授權軟體的安全性原則。
- 避免在您的環境中使用不必要的軟體。
- 避免執行舊版和不支援的應用程式。
- 阻止您的組織不允許的特定軟體工具。
- 讓 IT 能夠透過應用程式使用量來控制敏感性資料的存取。

您可以在參考的連結中找到更多詳細資料。

- [自適性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12：限制使用者在計算資源中執行腳本的能力

**指導** 方針：使用 Azure 資訊安全中心中提供的適應性應用程式控制功能。 它是資訊安全中心的智慧型自動化端對端解決方案，可協助您控制哪些應用程式可以在您的 Windows 和 Linux、Azure 和非 Azure 電腦上執行。 它也有助於強化您的機器免于惡意程式碼的侵害。 

這項功能適用于 Azure 和非 Azure Windows (所有版本、傳統或 Azure Resource Manager) 和 Linux 機器。

資訊安全中心會使用機器學習來分析在您電腦上執行的應用程式，並從這個智慧建立允許清單。 這項功能可大幅簡化設定和維護應用程式允許清單原則的流程，讓您能夠：

- 封鎖執行惡意應用程式的嘗試或提出警示，包括反惡意程式碼解決方案可能遺漏的嘗試。

- 符合您組織規定只能使用授權軟體的安全性原則。

- 避免在您的環境中使用不必要的軟體。

- 避免執行舊版和不支援的應用程式。

- 阻止您的組織不允許的特定軟體工具。

- 讓 IT 能夠透過應用程式使用量來控制敏感性資料的存取。

您可以在參考的連結中找到更多詳細資料。

- [自適性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13：以實體或邏輯方式隔離高風險的應用程式

**指導** 方針：適用于具有 Azure 雲端服務的敏感性或高風險應用程式、執行個別訂用帳戶或管理群組以提供隔離。

使用網路安全性群組、建立輸入安全性規則、選擇服務（例如 HTTP）、選擇自訂埠，並為其提供優先順序和名稱。 優先順序會影響規則的套用順序，數值越低，規則就會套用到較早的部分。 您必須將您的網路安全性群組與子網或特定網路介面建立關聯，以根據您的業務需求來隔離或分割網路流量。

您可以在參考的連結中找到更多詳細資料。

- [教學課程-使用 Azure 入口網站的網路安全性群組來篩選網路流量](../virtual-network/tutorial-filter-network-traffic.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="secure-configuration"></a>安全設定

*如需詳細資訊，請參閱 [Azure 安全性基準測試：安全](../security/benchmarks/security-control-secure-configuration.md)設定。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1：為所有 Azure 資源建立安全設定

**指導** 方針：使用 Azure 資訊安全中心中的建議做為您 Azure 雲端服務資源的安全設定基準。 

在 Azure 入口網站上，依序選擇 [資訊安全中心]、[計算 &amp; 應用程式] 和 [Azure 雲端服務]，以查看適用于您服務資源的建議。

- [安全性建議 - 參考指南](../security-center/recommendations-reference.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3：維護安全的 Azure 資源設定

**指導** 方針：不適用 Azure 雲端服務。 它是以傳統部署模型為基礎。 建議使用協力廠商解決方案來維護安全的 Azure 資源設定

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5：安全地儲存 Azure 資源的設定

**指導** 方針： Azure 雲端服務的設定檔會儲存資源的操作屬性。 您可以將設定檔的複本儲存至安全的儲存體帳戶。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7：部署適用于 Azure 資源的設定管理工具

**指導** 方針：不適用 Azure 雲端服務。 它是以傳統部署模型為基礎，而且無法透過 Azure Resource Manager 部署式設定工具來管理。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8：部署作業系統的設定管理工具

**指導** 方針：不適用 Azure 雲端服務。 這項建議適用于基礎結構即服務 (IaaS) 為基礎的計算資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9：執行 Azure 資源的自動化設定監視

**指導** 方針：使用 Azure 資訊安全中心針對您的 Azure 資源執行基準掃描。  

- [如何修復 Azure 資訊安全中心中的建議](../security-center/security-center-remediate-recommendations.md)

**Azure 資訊安全中心監視**：是

**責任**：客戶

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10：為作業系統實作自動化的設定監視

**指導** 方針：在 Azure 資訊安全中心中，選擇 [計算 &amp; 應用程式] 功能，並遵循虛擬機器、伺服器和容器的建議。

- [了解 Azure 資訊安全中心容器建議](../security-center/container-security.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="711-manage-azure-secrets-securely"></a>7.11：安全地管理 Azure 秘密

**指導** 方針： Azure 雲端服務是以傳統部署模型為基礎，且不會與 Azure Key Vault 整合。

您可以保護秘密，例如 Azure 雲端服務中使用的認證，因此您不需要每次都輸入密碼。 首先，指定純文字密碼，使用 ConvertTo-SecureString，PowerShell 命令將它轉換成安全字串。 接下來，使用 Convertfrom-string-SecureString 將此安全字串轉換成加密的標準字串。  您現在可以使用集合內容將此加密標準字串儲存至檔案。

此外，建議將 Azure 雲端服務中使用的憑證私密金鑰儲存至安全的儲存體。

- [從 PowerShell 設定遠端桌面](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13：消除非預期的認證公開

**指導** 方針：安全的密碼（例如 Azure 雲端服務中使用的認證），因此您不需要每次都輸入密碼。 
 

若要開始，請指定純文字密碼，並使用 ConvertTo-SecureString，PowerShell 命令將其變更為安全字串。 接下來，使用 Convertfrom-string-SecureString 將此安全字串轉換成加密的標準字串。 現在使用 Set-Content 命令將此加密標準字串儲存至檔案。

將 Azure 雲端服務中使用的憑證私密金鑰儲存至安全的儲存位置。

- [從 PowerShell 設定遠端桌面](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="malware-defense"></a>惡意程式碼防禦

*如需詳細資訊，請參閱 [Azure 安全性基準測試：惡意程式碼防護](../security/benchmarks/security-control-malware-defense.md)。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1：使用集中管理的反惡意程式碼軟體

**指導** 方針：適用于 Azure 的 Microsoft Antimalware 適用于 Azure 雲端服務和虛擬機器。 這是一項免費的即時保護，可協助識別和移除病毒、間諜軟體和其他惡意軟體。 它會在已知的惡意或垃圾軟體嘗試自行安裝或在 Azure 系統上執行時產生警示。 

使用以 PowerShell 為基礎的反惡意程式碼 Cmdlet 來取得反惡意程式碼設定，並使用 "Get-azureserviceantimalwareconfig"。

在 Azure 雲端服務的啟動工作中，使用 PowerShell 腳本啟用反惡意程式碼擴充功能。

選擇 Azure 資訊安全中心中的適應性應用程式控制功能，這是一個智慧型、自動化的端對端解決方案。 它有助於強化您的機器免于惡意軟體的攻擊，並可讓您封鎖或警示執行惡意應用程式的嘗試，包括反惡意程式碼解決方案可能遺漏的應用程式。

- [如何以自動化的方式為我的 Azure 雲端服務新增反惡意程式碼擴充功能](./cloud-services-configuration-and-management-faq.md?preserve-view=true#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [反惡意程式碼部署案例](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [自適性應用程式控制](../security-center/security-center-adaptive-application.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="incident-response"></a>事件回應

*如需詳細資訊，請參閱 [Azure 安全性基準測試：事件回應](../security/benchmarks/security-control-incident-response.md)。*

### <a name="101-create-an-incident-response-guide"></a>10.1：建立事件回應指南

**指引**：為組織製作事件回應指南。 請確定有書面的事件回應計畫，其中定義人員的所有角色，以及從偵測到事件後檢討的事件處理/管理階段。

- [如何設定 Azure 資訊安全中心內的工作流程自動化](../security-center/security-center-planning-and-operations-guide.md)

- [建立自有安全性事件回應程序的指引](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Microsoft 安全性回應中心的事件剖析](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [客戶也可以利用 NIST 的電腦安全性性事件處理指南來協助建立自己的事件回應計畫](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2：建立事件評分和優先順序程序

**指導** 方針： Azure 資訊安全中心將嚴重性指派給每個警示，以協助您排列應先調查哪些警示的優先順序。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。 

清楚地將訂用帳戶標示 (例如生產、非生產) ，以及建立命名系統來清楚識別和分類 Azure 資源。

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="103-test-security-response-procedures"></a>10.3：測試安全性回應程序

**指引**：進行練習以定期測試系統的事件回應功能。 找出弱點和落差，並視需要修訂計畫。 

- [請參閱 NIST 的發行集：Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4：提供安全性事件連絡人詳細資料，並設定安全性事件的警示通知

**指引**：如果 Microsoft 安全性回應中心 (MSRC) 發現客戶的資料遭到非法或未經授權的對象存取，Microsoft 將使用安全性事件連絡人資訊來連絡您。 事後檢討事件，確保問題已解決。 

- [如何設定 Azure 資訊安全中心的安全性連絡人](../security-center/security-center-provide-security-contact-details.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5：將安全性警示併入事件回應系統

**指引**：使用「連續匯出」功能來匯出 Azure 資訊安全中心的警示和建議。 「連續匯出」可供以手動或持續不斷的方式來匯出警示和建議。 您可以使用「安全性中心」資料連線器，將警示串流至 Azure Sentinel。 

- [如何設定連續匯出](../security-center/continuous-export.md) 

- [如何將警示串流至 Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

### <a name="106-automate-the-response-to-security-alerts"></a>10.6：自動回應安全性警示

**指引**：利用 Azure 資訊安全中心的工作流程自動化功能，透過「Logic Apps」自動觸發對安全性警示和建議的回應。

- [如何設定工作流程自動化和 Logic Apps](../security-center/workflow-automation.md)

**Azure 資訊安全中心監視**：不適用

**責任**：客戶

## <a name="penetration-tests-and-red-team-exercises"></a>滲透測試和 Red Team 練習

*如需詳細資訊，請參閱 [Azure 安全性基準測試：滲透測試和 red team 練習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1：進行 Azure 資源的定期滲透測試，並確保修復所有重要的安全性結果

**指引**：遵循 Microsoft 參與規則，以確保滲透測試不會違反 Microsoft 原則： https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [對於 Microsoft 管理的雲端基礎結構、服務和應用程式，您可在此找到 Microsoft 對於 Red Teaming 和即時網站滲透測試的策略與執行詳細資訊：](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure 資訊安全中心監視**：不適用

**責任**：共用

## <a name="next-steps"></a>後續步驟

- 請參閱 [Azure 安全性效能評定](../security/benchmarks/overview.md)
- 深入了解 [Azure 資訊安全性基準](../security/benchmarks/security-baselines-overview.md)