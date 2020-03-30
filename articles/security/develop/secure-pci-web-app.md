---
title: PCI DSS 的安全 Web 應用 |微軟文檔
description: 此示例應用實現了安全最佳實踐，在 Azure 上開發時可改進應用程式和組織的安全狀況。
keywords: NA
services: security
documentationcenter: na
author: fehase
manager: rkarlin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: terrylan
ms.openlocfilehash: 4fe612db65d985be2f1f1c81d03c3ee735c03889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992610"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>為 PCI 應用開發安全基礎結構

## <a name="overview"></a>總覽

此支付卡行業 （PCI） 應用的安全基礎結構為部署支付卡行業平臺提供了指導，即適合收集、存儲和檢索持卡人資料的服務 （PaaS） 環境。 此解決方案將一般參考架構的 Azure 資源部署和設定自動化，其中示範的方式讓客戶能符合特定的安全性和合規性需求，並且作為客戶在 Azure 上建置及設定他們自己的解決方案之基礎。 該解決方案實現了類似于支付卡行業資料安全標準 （PCI DSS 3.2） 的要求子集。

此示例自動部署帶有預配置安全控制的 PaaS Web 應用程式參考體系結構，説明客戶實現類似于 PCI DSS 3.2 要求的合規性。 解決方案包含引導資源部署與設定的 Azure Resource Manager 範本和 PowerShell 指令碼。

您應該按順序按照本文中描述的步驟操作，以確保正確配置應用程式元件。 資料庫、Azure 應用服務、Azure 金鑰保存庫實例和 Azure 應用程式閘道實例彼此依賴。

部署腳本設置基礎結構。 運行部署腳本後，需要在 Azure 門戶中執行一些手動設定，以將元件和服務連結在一起。

此示例面向 Azure 上經驗豐富的開發人員，他們在零售行業中工作，並希望使用安全的 Azure 基礎結構構建零售應用。

> [!NOTE]
> 此架構的目的是作為基礎，讓客戶進行調整以符合其特定需求，不應完全未經修改就用於生產環境中。

將應用程式部署至此環境而不作修改，不足以完全符合 PCI DSS 3.2 的要求。 請注意：

- 此架構提供了一個基準，可協助客戶以符合 PCI DSS 3.2 規範的方式來使用 Azure。
- 客戶須負責為任何使用架構建置的解決方進行置適當的安全性與合規性評量，因為需求可能會隨每個客戶的實作細節而有所不同。

在開發和部署此應用程式時，您將瞭解如何：

- 創建 Azure 金鑰保存庫實例並從中存儲和檢索機密。
- 為 Azure SQL 部署 Azure 資料庫，設置安全資料，並授權訪問它。
- 使用應用服務環境部署 Azure Web 應用，該環境與前端防火牆 aEcess 是專用隔離的。
- 使用使用[OWASP 前 10 個規則集](https://coreruleset.org/)的防火牆創建和配置 Azure 應用程式閘道實例。
- 使用 Azure 服務對傳輸中和靜態的資料進行加密。
- 設置 Azure 策略和藍色列印件以評估合規性

開發和部署此應用後，您將設置以下示例 Web 應用以及描述的配置和安全措施。

## <a name="architecture-diagram-and-components"></a>架構圖表與元件
該應用程式是一個典型的n層應用程式，具有三層。 此處顯示了集成了監視和金鑰管理元件的前端、後端和資料庫層：

![應用程式架構](./media/secure-pci-web-app/architecture.png)

該體系結構由以下元件組成：

- [應用服務環境 v2](https://docs.microsoft.com/azure/app-service/environment/intro/). 為我們的應用程式體系結構提供應用服務環境和負載等化器。
- [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)。 為我們的應用程式體系結構提供閘道和防火牆。
- [應用程式見解](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 在多個平臺上提供可擴展的應用程式性能管理 （APM） 服務。
- [Azure 金鑰保存庫](https://docs.microsoft.com/azure/key-vault/)。 存儲和加密應用的機密，並管理圍繞其創建訪問策略。
- [Azure 活動目錄](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/)。提供基於雲的標識和訪問管理服務、登錄和訪問資源。
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)。 提供託管域的服務。
- [Azure 負載等化器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/)。 擴展應用程式並為服務創建高可用性
- [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-introduction/)。 為現代資料存儲方案提供解決方案。
- [Azure Web 應用程式](https://docs.microsoft.com/azure/app-service/overview/)。  提供基於 HTTP 的服務來託管 Web 應用程式。
- [Azure 資料庫為 AzureSQL](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/)。 安全地存儲我們應用的資料。
- [Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/overview/)。 提供規範和符合某些標準和要求。
- [Azure 安全中心](https://docs.microsoft.com/azure/security-center/)
- [Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview). 評估資源是否符合分配的策略。

## <a name="threat-model"></a>威脅模型
威脅建模是識別業務和應用程式的潛在安全威脅，然後確保制定適當的緩解計畫的過程。

此示例使用[Microsoft 威脅建模工具](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)為安全示例應用實現威脅建模。 通過繪製元件和資料流程圖，可以在開發過程的早期識別問題和威脅。 這樣可以節省時間和金錢。

這是示例應用的威脅模型：

![威脅模型](./media/secure-pci-web-app/threat-model.png)

以下螢幕截圖中顯示了威脅建模工具生成的一些示例威脅和潛在漏洞。 威脅模型概述了暴露的攻擊面，並提示開發人員考慮如何緩解問題。

![威脅模型輸出](./media/secure-web-app/threat-model-output.png)

例如，通過清理使用者輸入和使用 Azure 資料庫中的存儲函數進行 PostgreSQL 來緩解上述威脅模型輸出中的 SQL 注入。 此緩解措施可防止在資料讀取和寫入期間任意執行查詢。

開發人員通過緩解威脅模型輸出中的每個威脅來提高系統的整體安全性。

## <a name="deployment"></a>部署
### <a name="prerequisites"></a>Prerequisites
要啟動和運行應用程式，您需要安裝這些工具：

- 用於修改和查看應用程式代碼的代碼編輯器。 [視覺化工作室代碼](https://code.visualstudio.com/)是一個開源選項。
- [開發電腦上的 Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest)
- [Git](https://git-scm.com/)在你的系統上。 Git 用於在本地克隆原始程式碼。
- [jq，](https://stedolan.github.io/jq/)一種以方便使用方式查詢JSON的UNIX工具。

此示例由 JSON 設定檔和 PowerShell 腳本組成，這些腳本由 Azure 資源管理器的 API 服務處理，用於在 Azure 中部署資源。 詳細的部署指示可於[這裡](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM) \(英文\) 取得。

#### <a name="quickstart"></a>快速入門

1.  將[這個](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub 存放庫複製或下載到您的本機工作站。
2.  檢閱 0-Setup-AdministrativeAccountAndPermission.md 並且執行提供的命令。
3.  部署具有 Contoso 範例資料的測試解決方案，或者試驗初始生產環境。

    此腳本部署 Azure 資源，以便使用 Contoso 示例資料演示 Web 存儲。

在此示例中，您將運行將 Web 應用部署到應用服務並創建資源的部署腳本。

在 Azure 上部署應用的方法有很多種，包括：

- Azure 資源管理員範本
- PowerShell
- Azure CLI
- Azure 入口網站
- Azure DevOps

## <a name="guidance-and-recommendations"></a>指引與建議

### <a name="network"></a>網路
此架構會定義位址空間為 10.200.0.0/16 的私人虛擬網路。
 ![Virtual_Network](./media/secure-pci-web-app/virtual-network.png)

### <a name="network-security-groups"></a>網路安全性群組
網路安全性群組（https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)包含允許或拒絕虛擬網路中的流量的存取控制清單 （ACL）。 網路安全性群組可用來保護子網路或個別虛擬機器層級的流量。 有下列網路安全性群組：

- 1 個網路安全性群組用於應用程式閘道
- 1 個網路安全性群組用於 App Service 環境
- 1 個網路安全性群組用於 Azure SQL Database
- 1 個網路安全性群組用於用於堡壘主機

每個網路安全性群組都打開特定的埠和協定，以便解決方案可以安全、正確地工作。 此外，下列組態會針對每個網路安全性群組啟用：

- 診斷日誌和事件（https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已啟用並存儲在存儲帳戶中）
- Azure 監視器日誌已連接到網路安全性群組的診斷程式（https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG 配置
應用服務環境的 NSG 配置應配置為下圖所示。

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

確認每個子網路都與對應的網路安全性群組建立關聯。

### <a name="config"></a>Config
子網配置如下圖所示。
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
網域名稱系統 （DNS） 負責將網站或服務名稱翻譯（或解析）到其 IP 位址。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) 是 DNS 網域的主機服務，採用 Azure 基礎結構來提供名稱解析。 只要將網域裝載於 Azure，使用者就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。 Azure DNS 也支援私人 DNS 網域。

### <a name="protect-data"></a>保護資料
若要協助保護雲端上的資料，您必須考慮您的資料可能會發生的狀態，以及哪些控制項適用於該狀態。 Azure 資料安全性和加密的最佳做法與下列資料狀態相關：

- 待用︰這包括實體媒體 (磁碟或光碟) 上以靜態方式存在的所有資訊儲存物件、容器和類型。
- 傳輸中：當資料在元件、位置或程式之間傳輸時，就為傳輸中。 範例會透過網路、跨服務匯流排 (從內部部署到雲端，反之亦然，包括諸如 ExpressRoute 的混合式連線)，或輸入/輸出過程期間傳輸。

### <a name="azure-storage"></a>Azure 儲存體
為了滿足靜態的加密資料要求，所有[Azure 存儲](https://azure.microsoft.com/services/storage/)都使用 Azure 金鑰保存庫來維護對訪問和加密資料的金鑰的控制。 這有助於保護與防衛持卡人資料，以支援組織的安全性承諾及 PCI DSS 3.2 所定義的合規性要求。

### <a name="azure-disk-encryption"></a>Azure 磁碟加密
Azure 磁碟加密利用 Windows 的 BitLocker 功能來提供資料磁碟的磁碟區加密。 此解決方案與 Azure Key Vault 整合，以協助控制和管理磁碟加密金鑰。

### <a name="azure-sql-database"></a>Azure SQL Database
Azure SQL Database 執行個體會使用下列資料庫安全性量值：

- [Active Directory 驗證與授權](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)可讓您在一個中央位置，管理資料庫使用者及其他 Microsoft 服務的身分識別。
- [SQL 資料庫稽核](https://docs.microsoft.com/azure/sql-database/sql-database-auditing-get-started/)會追蹤資料庫事件並將事件寫入 Azure 儲存體帳戶中的稽核記錄。
- Azure SQL Database 設定成使用[透明資料加密](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)，會執行資料庫、相關備份及交易記錄檔的即時加密和解密，以保護待用資訊。 透明資料加密能保證已儲存的資料不會遭到未經授權的存取。
- [防火牆規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)可在授與適當權限之前，防止所有對資料庫伺服器的存取。 此防火牆會根據每一個要求的來源 IP 位址來授與資料庫存取權。
- [SQL 威脅偵測](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-get-started)可針對可疑的資料庫活動、潛在弱點、SQL 插入式攻擊，以及異常的資料庫存取模式，提供安全性警示，藉以在發生潛在威脅時啟用偵測及回應。
- [加密資料行](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault)可確保敏感性資料在資料庫系統內一律不會以純文字顯示。 啟用資料加密之後，只有具備金鑰存取權的用戶端應用程式或應用程式伺服器才可以存取純文字資料。
- [SQL Database 動態資料遮罩](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)可藉由遮避資料，使不具權限的使用者或應用程式無法看見敏感性資料。 動態資料遮罩可以自動探索潛在的敏感性資料，並建議套用適當的遮罩。 這有助於找出並減少資料的存取，使其不會透過未經授權的存取而離開資料庫。 客戶須負責調整動態資料遮罩設定，以符合其資料庫結構描述。

### <a name="identity-management"></a>身分識別管理
下列技術可在 Azure 環境中提供管理持卡人資料存取的功能：

- Azure 活動目錄是 Microsoft 的多租戶基於雲的目錄和標識管理服務。 解決方案的所有使用者都是在 Azure Active Directory 中建立，包括存取 Azure SQL Database 的使用者。
- 應用程式的驗證是使用 Azure Active Directory 執行。 有關詳細資訊，請參閱[將應用程式與 Azure 活動目錄集成](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)。 此外，資料庫資料行加密會使用 Azure Active Directory 向 Azure SQL Database 驗證應用程式。 有關詳細資訊，請參閱[如何保護 Azure SQL 資料庫中的敏感性資料](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- Azure 角色型存取控制可讓系統管理員定義微調存取權限，只對使用者授與其執行工作所需的存取權數量。 系統管理員可以只允許存取持卡人資料的特定動作，而不是授與所有使用者不受限制的 Azure 資源使用權限。 只有訂用帳戶管理員可擁有訂用帳戶的存取權。
- Azure Active Directory Privileged Identity Management 可讓客戶將可存取特定資訊 (例如持卡人資料) 的使用者人數降至最低。 系統管理員可以使用 Azure Active Directory Privileged Identity Management 來探索、限制和監視特殊權限的識別和其對資源的存取。 如有需要，這項功能也可用來強制執行隨選 Just-In-Time 系統管理存取權。
- Azure 活動目錄標識保護檢測影響組織身份的潛在漏洞，配置自動回應，以檢測到與組織標識相關的可疑操作，並調查可疑事件採取適當的措施來解決它們。

### <a name="secrets-management"></a>祕密管理
此解決方案會使用 Azure Key Vault 來管理金鑰和秘密。 Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 下列 Azure Key Vault 功能可協助客戶保護資料及存取這類資料：

- 進階存取原則是視需要設定的。
- Key Vault 存取原則是使用金鑰和祕密的最低必要權限所定義的。
- Key Vault 中的所有金鑰和祕密都有到期日。
- Key Vault 中的所有金鑰都會受到特製化硬體安全模組的保護。 金鑰類型為 HSM 保護的 2048 位元 RSA 金鑰。
- 使用金鑰保存庫，您可以加密金鑰和機密（如身份驗證金鑰、存儲帳戶金鑰、資料加密金鑰、通過使用受硬體安全模組 （HSM） 保護的金鑰，使用 PFX 檔和密碼
- 使用 RBAC 將許可權分配給特定範圍內的使用者、組和應用程式。
- 使用金鑰保存庫通過自動續訂管理 TLS 證書。
- 金鑰保存庫的診斷記錄的保留期至少 365 天。
- 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目。

### <a name="azure-security-center"></a>Azure 資訊安全中心
客戶可以使用 Azure 資訊安全中心，在工作負載之間集中套用及管理安全性原則、限制暴露於威脅的程度，以及偵測和回應攻擊。 此外，Azure 資訊安全中心會存取 Azure 服務的現有組態，以提供設定和服務建議，協助改善安全性狀態並保護資料。

Azure 資訊安全中心會使用不同的偵測功能，向客戶警示以其環境為目標的潛在攻擊。 這些警示包含觸發警示的項目、鎖定為目標的資源，以及攻擊來源等重要資訊。 Azure 資訊安全中心有一組預先定義的安全性警示，一旦發生威脅或可疑活動時便會觸發這些警示。 Azure 資訊安全中心內的自訂警示規則可讓客戶根據從其環境所收集到的資料，定義新的安全性警示。

Azure 資訊安全中心提供依優先順序排列的安全性警示和事件，讓客戶更容易探索及解決潛在的安全性問題。 針對每個偵測到的威脅會產生威脅情報報告，以協助事件回應小組調查威脅並進行補救。

### <a name="azure-application-gateway"></a>Azure 應用程式閘道
此架構使用已設定 Web 應用程式防火牆和已啟用 OWASP 規則集的 Azure 應用程式閘道，可減少安全性弱點帶來的風險。 其他功能包括：

- 端到端 SSL
- 禁用 TLS v1.0 和 v1.1
- 啟用 TLSv1.2
- Web 應用程式防火牆 (防止模式)
- 使用 OWASP 3.0 規則集的防止模式
- 啟用診斷記錄
- 自訂健康情況探查
- Azure 資訊安全中心和 Azure Advisor 會提供額外的保護和通知。 Azure 資訊安全中心也會提供評價系統。

### <a name="logging-and-auditing"></a>記錄與稽核
Azure 服務會廣泛記錄系統、使用者活動及系統健康情況：

- [活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)能讓您深入了解在訂用帳戶資源上執行的作業。 活動記錄可協助判斷作業的啟動者、發生時間和狀態。
- [診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包含每個資源發出的所有記錄。 這些日誌包括 Windows 事件系統日誌、Azure 存儲日誌、金鑰保存庫稽核記錄以及應用程式閘道訪問和防火牆日誌。 所有診斷記錄都會寫入到集中且加密的 Azure 儲存體帳戶進行封存。 保留期是由使用者自訂，視組織特定的保留期需求，最長可達 730 天。

### <a name="azure-monitor-logs"></a>Azure 監視器記錄
這些日誌合併在[Azure 監視器日誌](https://azure.microsoft.com/services/log-analytics/)中，用於處理、存儲和儀表板報告。 所收集的資料會針對 Log Analytics 工作區內的每種資料類型組織成個別資料表，以便一起分析所有的資料 (不論其原始來源為何)。 此外，Azure 安全中心與 Azure 監視器日誌集成，允許客戶使用 Kusto 查詢訪問其安全事件資料並將其與其他服務的資料合併。

以下 Azure[監視解決方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)作為此體系結構的一部分包含在內：

- [活動目錄評估](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)：活動目錄運行狀況檢查解決方案在常規時間間隔內評估伺服器環境的風險和運行狀況，並提供特定于已部署伺服器基礎結構的建議的優先順序清單。
- [SQL 評定](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 健康情況檢查解決方案會定期評估伺服器環境的風險和健康情況，並專門針對部署的伺服器架構，提供優先的建議清單給客戶。
- [代理運行狀況](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理運行狀況解決方案報告部署的代理數量及其地理分佈，以及回應的代理數以及提交運算元據的代理數。
- [活動記錄分析](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：「活動記錄分析」解決方案可協助您分析客戶所有 Azure 訂用帳戶的 Azure 活動記錄。

### <a name="azure-monitor"></a>Azure 監視器
[Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)由讓組織稽核、建立警示及封存資料，包括追蹤使用者 Azure 資源中的 API 呼叫，協助使用者追蹤效能、維護安全性和識別趨勢。

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」服務。 Application Insights 會偵測效能異常，客戶可以用它來監視即時 Web 應用程式。 其中包括強大的分析工具可協助客戶診斷問題，並了解使用者實際上如何運用應用程式。 它是設計來協助客戶持續改善效能和可用性。

### <a name="azure-key-vault"></a>Azure 金鑰保存庫
為存儲金鑰的組織創建保存庫，並維護操作任務的問責制，如下所示：

- 儲存在金鑰保存庫中的資料包括：

   - Application Insights 金鑰
   - 資料存儲訪問金鑰
   - 連接字串
   - 資料表名稱
   - 使用者認證

- 進階存取原則是視需要設定的
- 金鑰保存庫存取原則是使用金鑰和密碼的最低必要權限所定義的
- 金鑰保存庫中的所有金鑰和密碼都有到期日
- 金鑰保存庫中的所有金鑰都受 HSM [金鑰類型 + HSM 保護 2048 位 RSA 金鑰] 保護。
- 使用基於角色的存取控制 （RBAC） 授予所有使用者/標識所需的最低許可權
- 應用程式不會共用金鑰保存庫，除非它們彼此信任且必須在執行階段存取相同金鑰
- 金鑰保存庫的診斷記錄的保留期至少 365 天。
- 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
您需要設定安全的 VPN 通道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地連線到部署為此 PaaS Web 應用程式參考架構一部分的資源。 透過適當地設定 VPN 或 ExpressRoute，客戶可以在傳輸過程中新增資料保護層。

藉由實作與 Azure 的安全 VPN 通道，即可建立內部部署網路與 Azure 虛擬網路之間的虛擬私人連線。 此連接通過 Internet 進行，並允許客戶安全地在客戶網路和 Azure 之間的加密鏈路內"隧道"資訊。 站對站 VPN 是安全成熟的技術，各種規模的企業已部署數十年。 此選項使用 IPsec 通道模式作為加密機制。

由於 VPN 通道內的流量會透過站對站 VPN 周遊網際網路，因此 Microsoft 提供另一個更安全的連線選項。 Azure ExpressRoute 是 Azure 與內部部署位置或 Exchange 主機服務提供者之間專用的 WAN 連結。 ExpressRoute 連線不會經過網際網路，相較於一般網際網路連線，這些連線提供了更可靠、更快速、更低延遲、更安全的連線。 此外，因為這是客戶電信提供者的直接連線，所以資料不會透過網際網路傳輸，因此不會公開給網際網路。

如需實作將內部部署網路擴充至 Azure 之安全混合式網路的最佳做法，請參閱[這裡](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="cost-considerations"></a>成本考量
如果還沒有 Azure 帳戶，則可以創建一個免費帳戶。 轉到[免費帳戶頁面](https://azure.microsoft.com/free/)即可開始，瞭解可以使用免費 Azure 帳戶做什麼，並瞭解哪些產品在 12 個月內是免費的。

要使用安全功能部署示例應用中的資源，您需要為某些高級功能付費。 隨著應用的擴展以及 Azure 提供的免費層和試用版需要升級以滿足應用程式要求，您的成本可能會增加。 使用 Azure[定價計算機](https://azure.microsoft.com/pricing/calculator/)估計成本。

## <a name="next-steps"></a>後續步驟
以下文章可説明您設計、開發和部署安全應用程式。

- [設計](secure-design.md)
- [發展](secure-develop.md)
- [部署](secure-deploy.md)
