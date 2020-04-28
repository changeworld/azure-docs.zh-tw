---
title: PCI DSS 的安全 web 應用程式 |Microsoft Docs
description: 這個範例應用程式會在您于 Azure 上開發時，實行可改善應用程式和組織安全性狀態的安全性最佳作法。
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
ms.openlocfilehash: 4ab3697824ff4a47e7b8f281b531cae610ffdc3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187574"
---
# <a name="develop-a-secure-infrastructure-for-a-pci-app"></a>開發適用于 PCI 應用程式的安全基礎結構

## <a name="overview"></a>概觀

此適用于付款卡產業（PCI）應用程式的安全基礎結構，提供部署付款卡產業平臺即服務（PaaS）環境的指引，適用于收集、儲存和抓取持卡人資料。 此解決方案將一般參考架構的 Azure 資源部署和設定自動化，其中示範的方式讓客戶能符合特定的安全性和合規性需求，並且作為客戶在 Azure 上建置及設定他們自己的解決方案之基礎。 此解決方案會執行一組需求，類似于支付卡產業資料安全標準（PCI DSS 3.2）。

此範例會自動部署 PaaS web 應用程式參考架構與預先設定的安全性控制，以協助客戶達成類似 PCI DSS 3.2 需求的合規性。 解決方案包含引導資源部署與設定的 Azure Resource Manager 範本和 PowerShell 指令碼。

您應遵循本文中所述的步驟，以確保應用程式元件已正確設定。 資料庫、Azure App Service、Azure Key Vault 實例和 Azure 應用程式閘道實例會彼此相依。

部署腳本會設定基礎結構。 執行部署腳本之後，您必須在 Azure 入口網站中進行一些手動設定，以將元件和服務連結在一起。

此範例的目標是 Azure 上有經驗的開發人員，他們在零售業中工作，並想要使用安全的 Azure 基礎結構建立零售應用程式。

> [!NOTE]
> 此架構的目的是作為基礎，讓客戶進行調整以符合其特定需求，不應完全未經修改就用於生產環境中。

將應用程式部署至此環境而不作修改，不足以完全符合 PCI DSS 3.2 的要求。 請注意：

- 此架構提供了一個基準，可協助客戶以符合 PCI DSS 3.2 規範的方式來使用 Azure。
- 客戶須負責為任何使用架構建置的解決方進行置適當的安全性與合規性評量，因為需求可能會隨每個客戶的實作細節而有所不同。

在開發和部署此應用程式時，您將瞭解如何：

- 建立 Azure Key Vault 實例，並從中儲存和取出秘密。
- 部署適用于 Azure SQL 的 Azure 資料庫、設定安全資料，並授權其存取權。
- 使用 App Service 環境來部署 Azure web 應用程式，這是與前端防火牆存取隔離的專用。
- 使用[OWASP 前10大規則集](https://coreruleset.org/)的防火牆建立並設定 Azure 應用程式閘道實例。
- 使用 Azure 服務，啟用傳輸中和待用資料的加密。
- 設定 Azure 原則和 Azure 藍圖以評估相容性。

在您開發並部署此應用程式之後，您將會設定下列範例 web 應用程式，以及所述的設定和安全性量值。

## <a name="architecture-diagram-and-components"></a>架構圖表與元件
應用程式是典型的多層式應用程式，具有三個層級。 整合了監視和密碼管理元件的前端、後端和資料庫層，如下所示：

![應用程式架構](./media/secure-pci-web-app/architecture.png)

架構是由下列元件所組成：

- [App Service 環境 v2](https://docs.microsoft.com/azure/app-service/environment/intro/)。 提供應用程式架構的 App Service 環境和負載平衡器。
- [Azure 應用程式閘道](https://docs.microsoft.com/azure/application-gateway/)。 提供應用程式架構的閘道和防火牆。
- [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 在多個平臺上提供可擴充的應用程式效能管理（APM）服務。
- [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)。 會儲存並加密我們的應用程式密碼，並管理其周圍的存取原則建立。
- [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis/)。提供以雲端為基礎的身分識別和存取管理服務、登入和存取資源。
- [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview)。 提供服務來裝載網域。
- [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview/)。 調整您的應用程式，並為您的服務建立高可用性
- [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-introduction/)。 提供現代化資料儲存案例的解決方案。
- [Azure Web 應用程式](https://docs.microsoft.com/azure/app-service/overview/)。  提供用來裝載 web 應用程式的 HTTP 型服務。
- [適用于 AzureSQL 的 Azure 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview/)。 安全地儲存應用程式的資料。
- [Azure 藍圖](https://docs.microsoft.com/azure/governance/blueprints/overview/)。 提供特定標準和需求的規格和合規性。
- [Azure 資訊安全中心](https://docs.microsoft.com/azure/security-center/)
- [Azure 原則](https://docs.microsoft.com/azure/governance/policy/overview)。 評估您的資源是否符合指派的原則。

## <a name="threat-model"></a>威脅模型
威脅模型化是識別企業和應用程式潛在安全性威脅的程式，然後確保適當的風險降低計畫已就緒。

這個範例使用[Microsoft Threat Modeling Tool](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)來執行安全範例應用程式的威脅模型化。 藉由將元件和資料流程的圖表化，您可以及早識別開發程式中的問題和威脅。 這可節省時間和金錢。

這是範例應用程式的威脅模型：

![威脅模型](./media/secure-pci-web-app/threat-model.png)

下列螢幕擷取畫面顯示威脅模型化工具所產生的一些範例威脅和潛在弱點。 威脅模型概述公開的攻擊面，並提示開發人員思考如何減輕問題。

![威脅模型輸出](./media/secure-web-app/threat-model-output.png)

例如，藉由淨化使用者輸入以及在適用於 PostgreSQL 的 Azure 資料庫中使用預存函式，可減輕前述威脅模型輸出中的 SQL 插入式攻擊。 這項緩和措施可防止在資料讀取和寫入期間執行任意查詢。

開發人員藉由減少威脅模型輸出中的每個威脅，來改善系統的整體安全性。

## <a name="deployment"></a>部署
### <a name="prerequisites"></a>Prerequisites
若要讓應用程式啟動並執行，您需要安裝下列工具：

- 用來修改和查看應用程式程式碼的程式碼編輯器。 [Visual Studio Code](https://code.visualstudio.com/)是一個開放原始碼選項。
- 在您的開發電腦上[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&viewFallbackFrom=azure-cli-latest) 。
- 系統上的[Git](https://git-scm.com/) 。 Git 是用來在本機複製原始程式碼。
- [jq](https://stedolan.github.io/jq/)，這是用來以方便使用的方式查詢 JSON 的 UNIX 工具。

此範例是由 Azure Resource Manager 的 API 服務所處理的 JSON 設定檔和 PowerShell 腳本所組成，可在 Azure 中部署資源。 詳細的部署指示可於[這裡](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM) \(英文\) 取得。

#### <a name="quickstart"></a>快速入門

1.  將[這個](https://github.com/Azure-Samples/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms-AzureRM.git) GitHub 存放庫複製或下載到您的本機工作站。
2.  檢閱 0-Setup-AdministrativeAccountAndPermission.md 並且執行提供的命令。
3.  部署具有 Contoso 範例資料的測試解決方案，或者試驗初始生產環境。

    此腳本會部署 Azure 資源，以示範使用 Contoso 範例資料的 web 存放區。

在此範例中，您會執行部署腳本，將 web 應用程式部署至 App Service 並建立資源。

有許多方式可以在 Azure 上部署應用程式，包括：

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
網路安全性群組（https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)包含允許或拒絕虛擬網路內流量的存取控制清單（acl）。 網路安全性群組可用來保護子網路或個別虛擬機器層級的流量。 有下列網路安全性群組：

- 1 個網路安全性群組用於應用程式閘道
- 1 個網路安全性群組用於 App Service 環境
- 1 個網路安全性群組用於 Azure SQL Database
- 1 個網路安全性群組用於用於堡壘主機

每個網路安全性群組都會開放特定埠和通訊協定，讓解決方案可安全且正確地運作。 此外，下列組態會針對每個網路安全性群組啟用：

- 診斷記錄和事件（https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)已啟用並儲存在儲存體帳戶中）
- Azure 監視器記錄會連線至網路安全性群組的診斷（https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

### <a name="nsg-config"></a>NSG Config
App Service 環境的 NSG 設定應依照下圖所示進行設定。

 ![NSG_Config](./media/secure-pci-web-app/nsg-config.png)

確認每個子網路都與對應的網路安全性群組建立關聯。

### <a name="config"></a>Config
子網的設定如下圖所示。
 ![Config](./media/secure-pci-web-app/config.png)

### <a name="azure-dns"></a>Azure DNS
網域名稱系統（DNS）負責將網站或服務名稱轉譯（或解析）為其 IP 位址。 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) 是 DNS 網域的主機服務，採用 Azure 基礎結構來提供名稱解析。 只要將網域裝載於 Azure，使用者就可以像管理其他 Azure 服務一樣，使用相同的認證、API、工具和計費方式來管理 DNS 記錄。 Azure DNS 也支援私人 DNS 網域。

### <a name="protect-data"></a>保護資料
若要協助保護雲端上的資料，您必須考慮您的資料可能會發生的狀態，以及哪些控制項適用於該狀態。 Azure 資料安全性和加密的最佳做法與下列資料狀態相關：

- 待用︰這包括實體媒體 (磁碟或光碟) 上以靜態方式存在的所有資訊儲存物件、容器和類型。
- 傳輸中：當資料在元件、位置或程式之間傳輸時，就為傳輸中。 範例會透過網路、跨服務匯流排 (從內部部署到雲端，反之亦然，包括諸如 ExpressRoute 的混合式連線)，或輸入/輸出過程期間傳輸。

### <a name="azure-storage"></a>Azure 儲存體
為了符合加密的待用資料需求，所有[Azure 儲存體](https://azure.microsoft.com/services/storage/)都會使用 Azure Key Vault 來維持對存取和加密資料的索引鍵的控制權。 這有助於保護與防衛持卡人資料，以支援組織的安全性承諾及 PCI DSS 3.2 所定義的合規性要求。

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

- Azure Active Directory 是 Microsoft 的多租使用者雲端式目錄和身分識別管理服務。 解決方案的所有使用者都是在 Azure Active Directory 中建立，包括存取 Azure SQL Database 的使用者。
- 應用程式的驗證是使用 Azure Active Directory 執行。 如需詳細資訊，請參閱[整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)。 此外，資料庫資料行加密會使用 Azure Active Directory 向 Azure SQL Database 驗證應用程式。 如需詳細資訊，請參閱[如何保護 Azure SQL Database 中的機密資料](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- Azure 角色型存取控制可讓系統管理員定義微調存取權限，只對使用者授與其執行工作所需的存取權數量。 系統管理員可以只允許存取持卡人資料的特定動作，而不是授與所有使用者不受限制的 Azure 資源使用權限。 只有訂用帳戶管理員可擁有訂用帳戶的存取權。
- Azure Active Directory Privileged Identity Management 可讓客戶將可存取特定資訊 (例如持卡人資料) 的使用者人數降至最低。 系統管理員可以使用 Azure Active Directory Privileged Identity Management 來探索、限制和監視特殊權限的識別和其對資源的存取。 如有需要，這項功能也可用來強制執行隨選 Just-In-Time 系統管理存取權。
- Azure Active Directory Identity Protection 會偵測影響組織身分識別的潛在弱點，並針對偵測到的組織身分識別相關可疑動作設定自動回應，以及調查可疑事件以採取適當動作來解決問題。

### <a name="secrets-management"></a>祕密管理
此解決方案會使用 Azure Key Vault 來管理金鑰和秘密。 Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 下列 Azure Key Vault 功能可協助客戶保護資料及存取這類資料：

- 進階存取原則是視需要設定的。
- Key Vault 存取原則是使用金鑰和祕密的最低必要權限所定義的。
- Key Vault 中的所有金鑰和祕密都有到期日。
- Key Vault 中的所有金鑰都會受到特製化硬體安全模組的保護。 金鑰類型為 HSM 保護的 2048 位元 RSA 金鑰。
- 使用 Key Vault，您可以加密金鑰和密碼（例如驗證金鑰、儲存體帳戶金鑰、資料加密金鑰）。PFX 檔案和密碼），方法是使用受硬體安全模組（Hsm）保護的金鑰
- 使用 RBAC 將許可權指派給特定範圍的使用者、群組和應用程式。
- 使用 Key Vault，透過自動更新來管理 TLS 憑證。
- 金鑰保存庫的診斷記錄的保留期至少 365 天。
- 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目。

### <a name="azure-security-center"></a>Azure 資訊安全中心
客戶可以使用 Azure 資訊安全中心，在工作負載之間集中套用及管理安全性原則、限制暴露於威脅的程度，以及偵測和回應攻擊。 此外，Azure 資訊安全中心會存取 Azure 服務的現有組態，以提供設定和服務建議，協助改善安全性狀態並保護資料。

Azure 資訊安全中心會使用不同的偵測功能，向客戶警示以其環境為目標的潛在攻擊。 這些警示包含觸發警示的項目、鎖定為目標的資源，以及攻擊來源等重要資訊。 Azure 資訊安全中心有一組預先定義的安全性警示，一旦發生威脅或可疑活動時便會觸發這些警示。 Azure 資訊安全中心內的自訂警示規則可讓客戶根據從其環境所收集到的資料，定義新的安全性警示。

Azure 資訊安全中心提供依優先順序排列的安全性警示和事件，讓客戶更容易探索及解決潛在的安全性問題。 針對每個偵測到的威脅會產生威脅情報報告，以協助事件回應小組調查威脅並進行補救。

### <a name="azure-application-gateway"></a>Azure 應用程式閘道
此架構使用已設定 Web 應用程式防火牆和已啟用 OWASP 規則集的 Azure 應用程式閘道，可減少安全性弱點帶來的風險。 其他功能包括：

- 端對端-SSL
- 停用 TLS v1.0 和 v1。1
- 啟用 Tlsv1.1 1。2
- Web 應用程式防火牆 (防止模式)
- 使用 OWASP 3.0 規則集的防止模式
- 啟用診斷記錄
- 自訂健康情況探查
- Azure 資訊安全中心和 Azure Advisor 會提供額外的保護和通知。 Azure 資訊安全中心也會提供評價系統。

### <a name="logging-and-auditing"></a>記錄與稽核
Azure 服務會廣泛記錄系統、使用者活動及系統健康情況：

- [活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)能讓您深入了解在訂用帳戶資源上執行的作業。 活動記錄可協助判斷作業的啟動者、發生時間和狀態。
- [診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)包含每個資源發出的所有記錄。 這些記錄包含 Windows 事件系統記錄、Azure 儲存體記錄、Key Vault audit 記錄檔，以及應用程式閘道存取和防火牆記錄。 所有診斷記錄都會寫入到集中且加密的 Azure 儲存體帳戶進行封存。 保留期是由使用者自訂，視組織特定的保留期需求，最長可達 730 天。

### <a name="azure-monitor-logs"></a>Azure 監視器記錄
這些記錄會合並在[Azure 監視器記錄](https://azure.microsoft.com/services/log-analytics/)中，以供處理、儲存及儀表板報告之用。 所收集的資料會針對 Log Analytics 工作區內的每種資料類型組織成個別資料表，以便一起分析所有的資料 (不論其原始來源為何)。 此外，Azure 資訊安全中心與 Azure 監視器記錄整合，可讓客戶使用 Kusto 查詢來存取其安全性事件資料，並將其與來自其他服務的資料合併。

下列 Azure[監視解決方案](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)包含在此架構中：

- [Active Directory 評定](https://docs.microsoft.com/azure/log-analytics/log-analytics-ad-assessment)： Active Directory 健康情況檢查解決方案會定期評估伺服器環境的風險和健全狀況，並針對已部署的伺服器基礎結構，提供優先的建議清單。
- [SQL 評定](https://docs.microsoft.com/azure/log-analytics/log-analytics-sql-assessment)：SQL 健康情況檢查解決方案會定期評估伺服器環境的風險和健康情況，並專門針對部署的伺服器架構，提供優先的建議清單給客戶。
- [代理程式健全狀況](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-agenthealth)：代理程式健全狀況解決方案會報告部署的代理程式數目和其地理分佈，以及有多少代理程式沒有回應，以及正在提交運算元據的代理程式數目。
- [活動記錄分析](https://docs.microsoft.com/azure/log-analytics/log-analytics-activity)：「活動記錄分析」解決方案可協助您分析客戶所有 Azure 訂用帳戶的 Azure 活動記錄。

### <a name="azure-monitor"></a>Azure 監視器
[Azure 監視器](https://docs.microsoft.com/azure/monitoring-and-diagnostics/)由讓組織稽核、建立警示及封存資料，包括追蹤使用者 Azure 資源中的 API 呼叫，協助使用者追蹤效能、維護安全性和識別趨勢。

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) 是多個平台上的 Web 開發人員所適用的可延伸「應用程式效能管理」服務。 Application Insights 會偵測效能異常，客戶可以用它來監視即時 Web 應用程式。 其中包括強大的分析工具可協助客戶診斷問題，並了解使用者實際上如何運用應用程式。 它是設計來協助客戶持續改善效能和可用性。

### <a name="azure-key-vault"></a>Azure 金鑰保存庫
為要儲存金鑰的組織建立保存庫，並維護作業工作的責任，如下所示：

- 儲存在金鑰保存庫中的資料包括：

   - Application Insights 金鑰
   - 資料儲存體存取金鑰
   - 連接字串
   - 資料表名稱
   - 使用者認證

- 進階存取原則是視需要設定的
- 金鑰保存庫存取原則是使用金鑰和密碼的最低必要權限所定義的
- 金鑰保存庫中的所有金鑰和密碼都有到期日
- Key Vault 中的所有金鑰都受到 HSM 的保護 [金鑰類型 = 受 HSM 保護的2048位 RSA 金鑰]
- 使用以角色為基礎的存取控制（RBAC）授與所有使用者/身分識別所需的最低許可權
- 應用程式不會共用金鑰保存庫，除非它們彼此信任且必須在執行階段存取相同金鑰
- 金鑰保存庫的診斷記錄的保留期至少 365 天。
- 允許的金鑰密碼編譯作業僅限於需要的密碼編譯項目

### <a name="vpn-and-expressroute"></a>VPN 和 ExpressRoute
您需要設定安全的 VPN 通道或 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)，以安全地連線到部署為此 PaaS Web 應用程式參考架構一部分的資源。 透過適當地設定 VPN 或 ExpressRoute，客戶可以在傳輸過程中新增資料保護層。

藉由實作與 Azure 的安全 VPN 通道，即可建立內部部署網路與 Azure 虛擬網路之間的虛擬私人連線。 此連線透過網際網路進行，可讓客戶在客戶網路與 Azure 之間的加密連結內安全地「通道」資訊。 站對站 VPN 是安全成熟的技術，各種規模的企業已部署數十年。 此選項使用 IPsec 通道模式作為加密機制。

由於 VPN 通道內的流量會透過站對站 VPN 周遊網際網路，因此 Microsoft 提供另一個更安全的連線選項。 Azure ExpressRoute 是 Azure 與內部部署位置或 Exchange 主機服務提供者之間專用的 WAN 連結。 ExpressRoute 連線不會經過網際網路，相較於一般網際網路連線，這些連線提供了更可靠、更快速、更低延遲、更安全的連線。 此外，因為這是客戶電信提供者的直接連線，所以資料不會透過網際網路傳輸，因此不會公開給網際網路。

如需實作將內部部署網路擴充至 Azure 之安全混合式網路的最佳做法，請參閱[這裡](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid)。

## <a name="cost-considerations"></a>成本考量
如果您還沒有 Azure 帳戶，可以建立一個免費帳戶。 前往[免費帳戶頁面](https://azure.microsoft.com/free/)以開始使用、瞭解免費 Azure 帳戶可執行檔工作，以及學習哪些產品免費12個月。

若要使用安全性功能來部署範例應用程式中的資源，您必須支付一些 premium 功能。 當應用程式調整規模，且 Azure 所提供的免費層和試用版需要升級以符合應用程式需求時，您的成本可能會增加。 使用 Azure[定價計算機](https://azure.microsoft.com/pricing/calculator/)來預估您的成本。

## <a name="next-steps"></a>後續步驟
下列文章可協助您設計、開發和部署安全的應用程式。

- [設計](secure-design.md)
- [開發](secure-develop.md)
- [部署](secure-deploy.md)
