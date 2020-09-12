---
title: Azure 資訊安全中心內的威脅防護
description: 本主題說明受 Azure 資訊安全中心的威脅防護功能保護的資源
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: e2f72cc0ea6851caaf5d0db2f17f8e16473d420e
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667550"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure 資訊安全中心內的威脅防護

資訊安全中心在您環境的任何區域中偵測到威脅時，會產生警示。 這些警示會說明受影響資源的詳細資料、建議的補救步驟，以及在某些情況下會說明觸發邏輯應用程式以進行回應的選項。

Azure 資訊安全中心的威脅防護可為您的環境提供全面的防護：

* **Azure 計算資源的威脅防護**：Windows 電腦、Linux 機器、Azure App Service 和 Azure 容器

* **Azure 資料資源的威脅防護**： SQL Database 和 Azure Synapse Analytics (先前的 SQL 資料倉儲) 、Azure 儲存體和 Azure Cosmos DB

* **Azure 服務層級的威脅防護**：Azure 網路層、Azure 管理層 (Azure Resource Manager) (預覽) 和 Azure Key Vault (預覽)

無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收的警示，您都可以將其匯出。 若要將您的警示匯出至 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，請依照[將警示匯出至 SIEM](continuous-export.md) 中的指示操作。 

> [!NOTE]
> 來自不同來源的警示顯示的所需時間可能不相同。 例如，需要分析網路流量的警示在顯示時，可能會比在虛擬機器上執行的可疑程序所產生的相關警示耗時。

> [!TIP]
> 若要啟用資訊安全中心的威脅防護功能，您必須將標準定價層套用至包含適用工作負載的訂用帳戶。
>
> 您可以在訂用帳戶層級或資源層級為 **Azure 儲存體帳戶**啟用威脅防護。
> 您可以在訂用帳戶層級或資源層級為 **Azure SQL Database SQL 伺服器**啟用威脅防護。
> 您只能在資源層級為**適用於 MariaDB/MySQL/PostgreSQL 的 Azure 資料庫**啟用威脅防護。



## <a name="threat-protection-for-windows-machines"></a>Windows 機器的威脅防護 <a name="windows-machines"></a>

Azure 資訊安全中心可與 Azure 服務整合，用以監視及保護您的 Windows 機器。 資訊安全中心會以便於使用的格式，提供來自這些服務的警示和補救建議。

* **Microsoft Defender 進階威脅防護 (ATP)** <a name="windows-atp"></a> - 資訊安全中心可藉由與 Microsoft Defender 進階威脅防護 (ATP) 整合來延伸其雲端工作負載保護平台。 兩者搭配運作下，可提供完整的端點偵測和回應 (EDR) 功能。

    > [!IMPORTANT]
    > 在使用資訊安全中心的 Windows 伺服器上，會自動啟用 Microsoft Defender ATP 感應器。

    Microsoft Defender ATP 在偵測到威脅時，會觸發警示。 警示會顯示在 [資訊安全中心] 儀表板上。 在儀表板中，您可以切換至 Microsoft Defender ATP 主控台並執行詳細的調查，以找出攻擊的範圍。 如需 Microsoft Defender ATP 的詳細資訊，請參閱[將伺服器上線至 Microsoft Defender ATP 服務](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* **無檔案攻擊偵測** <a name="windows-fileless"></a> -無檔案攻擊會將惡意承載插入記憶體中，以避免以磁片為基礎的掃描技術偵測。 攻擊者的承載接著會在遭入侵的進程記憶體中保存，並執行各式各樣的惡意活動。

    透過無檔案攻擊偵測，自動化記憶體鑑識技術可識別無檔案攻擊的工具組、技術和行為。 此解決方案會在執行時間定期掃描您的機器，並直接從進程的記憶體中將見解解壓縮。 Linux 的特定深入解析包含下列各項的識別： 

    - 知名的工具組和加密的挖掘軟體 
    - 程式碼外殼，這是一小段程式碼，通常是用來做為惡意探索軟體弱點的弱點。
    - 在進程記憶體中插入惡意可執行檔

    無檔案攻擊偵測會產生包含其他進程中繼資料（例如網路活動）描述的詳細安全性警示。 這會加速警示分級、相互關聯和下游回應時間。 這種方法可補充以事件為基礎的 EDR 解決方案，並提供更高的偵測涵蓋範圍。

    如需無檔案攻擊偵測警示的詳細資訊，請參閱[警示的參考資料表](alerts-reference.md#alerts-windows)。

> [!TIP]
> 您可以模擬 Windows 警示，方法是下載 [Azure 資訊安全中心劇本：安全性警示](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)。






## <a name="threat-protection-for-linux-machines"></a>Linux 機器的威脅防護 <a name="linux-machines"></a>

資訊安全中心會使用 **auditd** (最常見的 Linux 稽核架構之一)，從 Linux 機器收集稽核記錄。 auditd 位於主線核心中。 

* **Linux auditd 警示與 Log Analytics 代理程式的整合** <a name="linux-auditd"></a> - auditd 系統由核心層級子系統所組成，負責監視系統呼叫。 此系統會依指定的規則集篩選這些呼叫，並將其訊息寫入至通訊端。 資訊安全中心會整合 Log Analytics 代理程式內的 auditd 套件所包含的功能。 這項整合可讓您在所有支援的 Linux 發行版本中收集 auditd 事件，而不需要任何必要條件。

    auditd 記錄在收集之後，會使用適用於 Linux 的 Log Analytics 代理程式進行擴充，並彙總至事件中。 資訊安全中心會持續新增新的分析，使用 Linux 信號偵測雲端和內部部署 Linux 機器上的惡意行為。 與 Windows 功能類似，這些分析的執行範圍遍及可疑的程序、可疑的登入嘗試、核心模組載入和其他活動。 出現這些活動時，表示機器可能受到攻擊或已遭入侵。  

    如需 Linux 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-linux)。

> [!TIP]
> 您可以模擬 Linux 警示，方法是下載 [Azure 資訊安全中心劇本：Linux 偵測](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)。





## <a name="threat-protection-for-azure-app-service"></a>Azure App Service 的威脅防護 <a name="app-services"></a>

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

資訊安全中心使用雲端規模，來識別將透過 App Service 執行的應用程式視為目標的攻擊。 攻擊者會探查 Web 應用程式，以找出弱點並加以入侵。 在路由到特定環境之前，對執行於 Azure 中的應用程式提出的要求會經過數個閘道，並在其中進行檢查和記錄。 接著，此資料會用來識別入侵和攻擊者，並了解後續將使用的新模式。

利用 Azure 作為雲端提供者所具備的可見度，資訊安全中心可分析 App Service 內部記錄，以識別多個目標的攻擊方法。 例如，這些方法包括廣泛的掃描和分散式攻擊。 這種類型的攻擊通常來自於一小部分的 IP，且其模式為在多部主機上搜耙至類似端點。 攻擊會搜尋易受攻擊的頁面或外掛程式，且無法從單一主機的觀點來識別。

如果您執行以 Windows 為基礎的 App Service 方案，資訊安全中心也可存取基礎沙箱和 VM。 搭配前述的記錄資料，基礎結構也可勾勒出攻擊的概況，包括到處肆虐的新式攻擊，以及對客戶電腦的入侵。 因此，即使是在 Web 應用程式遭到惡意探索後才部署資訊安全中心，還是有可能偵測到進行中的攻擊。

如需 Azure App Service 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureappserv)。

如需 App Service 方案的詳細資訊，請參閱 [App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。





## <a name="threat-protection-for-containers"></a>容器的威脅防護 <a name="azure-containers"></a>

### <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|正式推出|
|定價：|標準層|
|必要的角色和許可權：|**安全性系統管理員** 可以關閉警示。<br>**安全性讀取者**可以查看結果。|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![否](./media/icons/no-icon.png) 全國/主權 (US Gov、中國 Gov、其他 Gov) |
|||

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]








## <a name="threat-protection-for-sql-database-and-azure-synapse-analytics-formerly-sql-data-warehouse"></a>SQL Database 的威脅防護，以及先前的 SQL 資料倉儲 Azure Synapse Analytics () <a name="data-sql"></a>

適用於 Azure SQL Database 的進階威脅防護會偵測異常活動，這些活動代表有不尋常及可能有害的活動試圖存取或惡意探索資料庫。

有可疑的資料庫活動、潛在弱點或 SQL 插入式攻擊，以及異常的資料庫存取和查詢模式時，您將會看到警示。

Azure SQL Database 和 SQL 的 advanced 威脅防護是 advanced [Data Security (ADS ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 的一部分，) 統一的 SQL 安全性功能的整合套件，涵蓋 Azure SQL Database、Azure SQL 受控實例、Azure Synapse Analytics (先前的 Sql 資料倉儲) 資料庫，以及 Azure 虛擬機器上的 sql server。

如需詳細資訊，請參閱

* [如何啟用 Azure SQL 資料庫的進階威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何為 Azure 虛擬機器上的 SQL 伺服器啟用進階威脅防護](security-center-iaas-advanced-data.md)
* [SQL Database 的威脅防護警示清單和先前的 SQL 資料倉儲 Azure Synapse Analytics () ](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure 儲存體的威脅防護 <a name="azure-storage"></a>

### <a name="availability"></a>可用性

|層面|詳細資料|
|----|:----|
|釋放狀態：|[Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/) (公開上市) <br>[Azure 檔案儲存體](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (preview) <br>[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) (preview) |
|定價：|標準層|
|雲端：|![是](./media/icons/yes-icon.png) 商業雲端<br>![是](./media/icons/yes-icon.png) US Gov<br>![否](./media/icons/no-icon.png) 中國 Gov，其他 Gov|
|||


### <a name="whats-protected"></a>受保護的專案

Azure 儲存體的威脅防護會偵測 Azure 儲存體帳戶可能有害的活動。 無論您的資料是儲存為 blob 容器、檔案共用或資料 lake，都可以受到保護。

這一層保護可讓您在不需要成為安全性專家的 *情況* 下解決威脅，並協助您管理安全性監視系統。

### <a name="what-kind-of-alerts-does-threat-protection-for-azure-storage-provide"></a>Azure 儲存體提供威脅防護的警示類型有哪些？

當發生下列情況時，就會觸發安全性警示：

- **可疑的活動** -例如，已成功存取儲存體帳戶的 IP 位址，該 IP 位址稱為 Tor 的 active exit 節點
- **異常行為** -例如，對儲存體帳戶的存取模式變更
- **可能上傳的惡意** 代碼-雜湊信譽分析指出上傳的檔案包含惡意程式碼

警示包含觸發警示的事件詳細資料，以及如何調查和修復威脅的建議。

### <a name="what-is-hash-reputation-analysis-for-malware"></a>惡意程式碼的雜湊信譽分析是什麼？

若要判斷上傳的檔案是否可疑，Azure 儲存體的威脅防護會使用 [Microsoft 威脅情報](https://go.microsoft.com/fwlink/?linkid=2128684)所支援的雜湊信譽分析。 威脅防護工具不會掃描上傳的檔案，而是會檢查儲存體記錄，並將新上傳檔案的雜湊與已知病毒、特洛伊木馬程式、間諜軟體及勒索軟體的雜湊進行比較。 

當檔案被懷疑包含惡意程式碼時，[安全性中心] 會顯示警示，並可選擇性地以電子郵件傳送儲存體擁有者，以核准刪除可疑的檔案。 若要設定自動移除雜湊信譽分析指出包含惡意程式碼的檔案，請部署 [工作流程自動化，以在包含「可能已上傳至儲存體帳戶的惡意程式碼」的警示上觸發](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-respond-to-potential-malware-uploaded-to-azure-storage/ba-p/1452005)。



### <a name="next-steps"></a>接下來的步驟 

如需定價詳細資料 (包含 30 天免費試用)，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

如需詳細資訊，請參閱

* [如何啟用 Azure 儲存體的進階威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 儲存體的威脅防護警示清單](alerts-reference.md#alerts-azurestorage)
* [Microsoft 的威脅情報功能](https://go.microsoft.com/fwlink/?linkid=2128684)

> [!TIP]
> 您可以遵循 [此 blog 文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的指示來模擬儲存體警示。







## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB (Preview) 的威脅防護 <a name="cosmos-db"></a>

Azure Cosmos DB 警示是因為以不尋常且有危害意圖的方式嘗試存取或惡意探索 Azure Cosmos DB 帳戶而產生的。

如需詳細資訊，請參閱

* [Azure Cosmos DB 的進階威脅防護 (預覽)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 的威脅防護警示清單 (預覽)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure 網路層的威脅防護 <a name="network-layer"></a>

資訊安全中心的網路層分析立基於範例 [IPFIX 資料](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)，這是由 Azure 核心路由器收集到的封包標頭。 根據此資料摘要，資訊安全中心會使用機器學習模型來識別並標示惡意的流量活動。 資訊安全中心也會使用 Microsoft 威脅情報資料庫來擴充 IP 位址。

某些網路設定可能會妨礙資訊安全中心產生可疑網路活動的警示。 若要讓資訊安全中心產生網路警示，請確定：

- 您的虛擬機器具有公用 IP 位址 (或位於具有公用 IP 位址的負載平衡器上)。

- 虛擬機器的網路輸出流量不會遭到外部 IDS 解決方案封鎖。

- 您的虛擬機器在發生可疑通訊的整個期間被指派的 IP 位址是相同的。 這也適用於在受控服務中建立的 VM (例如 AKS、Databricks)。

如需 Azure 網路層警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azurenetlayer)。




## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理層 (Azure Resource Manager) 的威脅防護 (預覽)<a name ="management-layer"></a>

以 Azure Resource Manager 為基礎的資訊安全中心保護層目前處於預覽階段。

資訊安全中心會利用 Azure Resource Manager 事件來提供額外的保護層，我們將其視為 Azure 的控制平面。 藉由分析 Azure Resource Manager 記錄，資訊安全中心會在 Azure 訂用帳戶環境中偵測不尋常或可能有害的作業。

如需 Azure Resource Manager (預覽) 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 前述的幾項分析由 Microsoft Cloud App Security 提供技術支援。 若要受益於這些分析，您必須啟用 Cloud App Security 授權。 如果您有 Cloud App Security 授權，則依預設會啟用這些警示。 若要停用警示：
>
> 1. 在 [安全性中心] 功能表中，選取 [ **定價 & 設定**]。
> 1. 選取您想變更的訂用帳戶。
> 1. 選取 [威脅偵測]。
> 1. 清除 [ **允許 Microsoft Cloud App Security 存取我的資料**]，然後選取 [ **儲存**]。

>[!NOTE]
>資訊安全中心會將關乎安全性的客戶資料儲存在與其資源相同的地理位置。 如果 Microsoft 尚未在資源的地理位置部署資訊安全中心，則會將資料儲存在美國。 啟用 Cloud App Security 時，會根據 Cloud App Security 的地理位置規則來儲存這項資訊。 如需詳細資訊，請參閱[非區域服務的資料儲存體](https://azuredatacentermap.azurewebsites.net/)。

1. 設定您要在其上安裝代理程式的工作區。 請確定工作區位於您在資訊安全中心使用的相同訂用帳戶中，而且您具備工作區的讀取/寫入權限。

1. 設定標準定價層，然後選取 [ **儲存**]。






## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure Key Vault 的威脅防護 (預覽)<a name="azure-keyvault"></a>

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

Azure 資訊安全中心包含適用於 Azure Key Vault 的 Azure 原生進階威脅防護，可額外提供多一層的安全情報。 資訊安全中心會偵測是否有人試圖以不尋常且可能有害的方式存取或惡意探索 Key Vault 帳戶。 這一層保護可讓您直接解決威脅，而無須成為安全性專家，且無須管理第三方安全性監視系統。  

發生異常活動時，資訊安全中心會顯示警示，並選擇性地透過電子郵件將其傳送給訂用帳戶管理員。 這些警示包含可疑活動的詳細資料，以及如何調查和補救威脅的建議。 

如需 Azure Key Vault 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azurekv)。





## <a name="threat-protection-for-other-microsoft-services"></a>其他 Microsoft 服務的威脅防護 <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF 的威脅防護 <a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

Web 應用程式已逐漸成為利用常見已知弱點進行惡意攻擊的目標。 應用程式閘道 WAF 會以 Open Web Application Security Project 中的核心規則集 3.0 或 2.2.9 為基礎。 系統會自動更新 WAF 以防護新的弱點。 

如果您有 Azure WAF 的授權，您的 WAF 警示將會串流到資訊安全中心，而不需要額外的設定。 若要進一步了解 WAF 所產生的警示，請參閱 [Web 應用程式防火牆 CRS 規則群組與規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS 保護的威脅防護 <a name="azure-ddos"></a>

目前已知分散式阻斷服務 (DDoS) 攻擊是很容易執行的。 這類攻擊已成為很大的安全顧慮，特別是當您將應用程式移轉至雲端時。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定任何可透過網際網路連線的任何端點。

若要防範 DDoS 攻擊，請購買 Azure DDoS 保護的授權，並確實遵循應用程式設計的最佳做法。 DDoS 保護提供不同的服務層級。 如需詳細資訊，請參閱 [Azure DDoS 保護概觀](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

如需 Azure DDoS 保護警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>後續步驟
若要深入了解這些威脅防護功能的安全性警示，請參閱下列文章：

* [所有 Azure 資訊安全中心警示的參考資料表](alerts-reference.md)
* [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
* [在 Azure 資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)
* [匯出安全性警示和建議 (預覽)](continuous-export.md)