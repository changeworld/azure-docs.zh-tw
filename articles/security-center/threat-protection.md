---
title: Azure 資訊安全中心中的威脅防護
description: 本主題描述受 Azure 資訊安全中心威脅防護功能保護的資源
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 7d078fb93f933320d13bfeb768b27923748b1262
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623942"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure 資訊安全中心中的威脅防護

此頁面簡要說明標準定價層上的使用者 Azure 資訊安全中心所顯示的安全性警示來源。

當資訊安全中心在下列環境的任何區域中偵測到威脅時，它會產生警示。 這些警示會描述受影響資源的詳細資料、建議的補救步驟，以及在某些情況下觸發邏輯應用程式以回應的選項。

無論是由資訊安全中心產生警示，還是由不同的安全性產品資訊安全中心接收，您都可以將它匯出。 若要將您的警示匯出至 Azure Sentinel （或協力廠商 SIEM）或任何其他外部位置，請遵循[匯出警示和建議](continuous-export.md)中的指示。 




## Windows 電腦的威脅防護<a name="windows-machines"></a>

Azure 資訊安全中心與 Azure 服務整合，以監視及保護您的 Windows 電腦。 資訊安全中心以便於使用的格式，提供所有這些服務的警示和補救建議。

* **Microsoft defender ATP** <a name="windows-atp"></a> -藉由與 microsoft defender Advanced 威脅防護（ATP）整合，資訊安全中心擴充其雲端工作負載保護平臺。 它們一起提供完整的端點偵測和回應（EDR）功能。

    > [!IMPORTANT]
    > Microsoft Defender ATP 感應器會在使用資訊安全中心的 Windows 伺服器上自動啟用。

    當 Microsoft Defender ATP 偵測到威脅時，它會觸發警示。 警示會顯示在 [資訊安全中心] 儀表板上。 從儀表板，您可以將資料透視到 Microsoft Defender ATP 主控台，並執行詳細的調查，以找出攻擊的範圍。 如需 Microsoft Defender ATP 的詳細資訊，請參閱[將伺服器上架到 Microsoft DEFENDER atp 服務](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* 損毀傾印**分析** <a name="windows-dump"></a> -當軟體當機時，損毀傾印會在當機時，捕捉記憶體的一部分。

    損毀可能是惡意軟體或包含惡意程式碼所造成。 為了避免安全性產品偵測到，各種形式的惡意程式碼都會使用 fileless 攻擊，以避免寫入磁片或加密寫入磁片的軟體元件。 使用傳統的磁片型方法，很容易偵測到這種類型的攻擊。

    不過，藉由使用記憶體分析，您可以偵測到這類攻擊。 藉由分析損毀傾印中的記憶體，資訊安全中心可以偵測到攻擊所使用的技術。 例如，攻擊可能會嘗試入侵軟體中的弱點、存取機密資料，以及暗中保存在遭入侵的電腦中。 資訊安全中心會執行這種操作，對主機的效能影響最小。

    如需損毀傾印分析警示的詳細資訊，請參閱[警示的參考資料表](alerts-reference.md#alerts-windows)。

* **Fileless 攻擊偵測** <a name="windows-fileless"></a> -以您的端點為目標的 Fileless 攻擊是常見的。 為了避免偵測，fileless 攻擊會將惡意承載插入記憶體中。 攻擊者承載會保存在遭入侵的進程記憶體中，並執行各式各樣的惡意活動。

    透過 fileless 攻擊偵測，自動化記憶體辨識技術可識別 fileless 攻擊工具組、技術和行為。 此解決方案會在執行時間定期掃描您的電腦，並直接從安全性關鍵進程的記憶體中抽取深入解析。

    它會尋找入侵、程式碼插入和執行惡意承載的證據。 Fileless 攻擊偵測會產生詳細的安全性警示，以加速警示分級、相互關聯和下游回應時間。 這個方法會補充以事件為基礎的 EDR 解決方案，以提供更高的偵測涵蓋範圍。

    如需 fileless 攻擊偵測警示的詳細資訊，請參閱[警示的參考資料表](alerts-reference.md#alerts-windows)。

> [!TIP]
> 您可以下載[Azure 資訊安全中心腳本：安全性警示](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)來模擬 Windows 警示。






## Linux 機器的威脅防護<a name="linux-machines"></a>

資訊安全中心會使用**auditd**（其中一個最常見的 linux 審核架構），從 Linux 機器收集審核記錄。 auditd 居住在主線核心中。 

* **Linux auditd 警示和 Microsoft Monitoring Agent （MMA）整合** <a name="linux-auditd"></a> -auditd 系統是由核心層級子系統所組成，負責監視系統呼叫。 它會依指定的規則集篩選它們，並將訊息寫入至通訊端。 資訊安全中心會從 Microsoft Monitoring Agent （MMA）內的 auditd 套件整合功能。 這項整合可讓您在所有支援的 Linux 發行版本中收集 auditd 事件，而不需要任何必要條件。  

    auditd 記錄會使用 Linux MMA 代理程式來收集、擴充及匯總至事件。 資訊安全中心持續新增使用 Linux 信號的新分析，以偵測雲端和內部部署 Linux 電腦上的惡意行為。 與 Windows 功能類似，這些分析會跨越可疑的程式、可疑登入嘗試、核心模組載入和其他活動。 這些活動可能表示電腦受到攻擊，或已被入侵。  

    如需 Linux 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-linux)。

> [!TIP]
> 您可以藉由下載 Azure 資訊安全中心腳本[： linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)偵測來模擬 Linux 警示。





## Azure App Service 的威脅防護<a name="app-services"></a>

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

資訊安全中心使用雲端的規模來識別以 App Service 執行之應用程式為目標的攻擊。 攻擊者會探查 web 應用程式，以找出併入侵弱點。 在路由傳送至特定環境之前，對 Azure 中執行之應用程式的要求會經歷數個閘道，並在其中進行檢查和記錄。 接著會使用此資料來識別入侵和攻擊者，並瞭解稍後將使用的新模式。

藉由使用 Azure 做為雲端提供者的可見度，資訊安全中心分析 App Service 內部記錄，以識別多個目標的攻擊方法。 例如，方法包含廣泛的掃描和分散式攻擊。 這種類型的攻擊通常來自于一小部分的 Ip，並顯示在多部主機上編目至類似端點的模式。 攻擊會搜尋易受攻擊的頁面或外掛程式，而無法從單一主機的觀點來識別。

如果您執行的是以 Windows 為基礎的 App Service 方案，資訊安全中心也可以存取基礎沙箱和 Vm。 除了上述的記錄資料，基礎結構也可以告訴故事，從攻擊者的新攻擊，到客戶電腦的危害。 因此，即使在惡意探索 web 應用程式之後部署資訊安全中心，它還是可能能夠偵測到進行中的攻擊。

如需 Azure App Service 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureappserv)。

如需 App Service 計畫的詳細資訊，請參閱[App Service 方案](https://azure.microsoft.com/pricing/details/app-service/plans/)。





## Azure 容器的威脅防護<a name="azure-containers"></a>

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

資訊安全中心為您的容器化環境提供即時威脅防護，並產生可疑活動的警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

資訊安全中心提供不同層級的威脅防護： 

* **主機層級**-資訊安全中心的代理程式（適用于標準層，如需詳細資訊，請參閱[定價](security-center-pricing.md)）監視 Linux 是否有可疑的活動。 代理程式會觸發源自節點或在其上執行之容器的可疑活動警示。 這類活動的範例包括 web 命令介面偵測，以及與已知可疑 IP 位址的連線。

    若要深入瞭解容器化環境的安全性，代理程式會監視容器特有的分析。 它會觸發事件的警示，例如建立許可權的容器、對 API 伺服器的可疑存取，以及在 Docker 容器內執行的安全殼層（SSH）伺服器。

    >[!IMPORTANT]
    > 如果您選擇不在您的主機上安裝代理程式，您只會收到威脅偵測權益和警示的子集。 您仍然會收到與網路分析以及與惡意伺服器通訊相關的警示。

    如需主機層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-containerhost)。


* 在**AKS 叢集層級**，威脅防護是以分析 Kubernetes 的「audit 記錄」為基礎。 若要啟用此**無代理**程式監視，請從 [**定價 & 設定**] 頁面將 [Kubernetes] 選項新增至您的訂用帳戶（請參閱[定價](security-center-pricing.md)）。 若要在此層級產生警示，資訊安全中心使用 AKS 所取出的記錄來監視 AKS 管理的服務。 此層級的事件範例包括公開的 Kubernetes 儀表板、建立高特殊許可權的角色，以及建立敏感性裝載。

    >[!NOTE]
    > 資訊安全中心會針對在訂用帳戶設定上啟用 Kubernetes 選項後發生的 Azure Kubernetes Service 動作和部署產生安全性警示。 

    如需 AKS 叢集層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-akscluster)。

此外，我們的全球安全性研究人員小組會持續監視威脅的範圍。 他們會在探索到容器特有的警示和弱點時加以新增。






## Azure 網路層的威脅防護<a name="network-layer"></a>

資訊安全中心的網路層分析是以範例[IPFIX 資料](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)為基礎，這是 Azure 核心路由器所收集的封包標頭。 根據此資料摘要，資訊安全中心會使用機器學習模型來識別並標示惡意的流量活動。 資訊安全中心也會使用 Microsoft 威脅情報資料庫來充實 IP 位址。

某些網路設定可能會限制資訊安全中心產生可疑網路活動的警示。 如需產生網路警示的資訊安全中心，請確定：

- 您的虛擬機器具有公用 IP 位址（或位於具有公用 IP 位址的負載平衡器上）。

- 外部識別碼解決方案不會封鎖您虛擬機器的網路輸出流量。

- 您的虛擬機器在發生可疑通訊的整個一小時，已指派相同的 IP 位址。 這也適用于在受控服務中建立的 Vm （例如，AKS、Databricks）。

如需 Azure 網路層警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azurenetlayer)。

如需資訊安全中心如何使用網路相關信號來套用威脅防護的詳細資訊，請參閱[資訊安全中心中的啟發式 DNS](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)偵測。







## Azure Key Vault 的威脅防護（預覽）<a name="azure-keyvault"></a>

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

Azure 資訊安全中心包含適用于 Azure Key Vault 的 Azure 原生、先進的威脅防護，提供額外一層的安全性情報。 資訊安全中心偵測到不尋常且可能有害的嘗試存取或惡意探索 Key Vault 的帳戶。 這一層保護可讓您在不是安全性專家的情況下處理威脅，而不需要管理協力廠商的安全性監視系統。  

發生異常活動時，資訊安全中心會顯示警示，並選擇性地透過電子郵件傳送給訂用帳戶管理員。 這些警示包括可疑活動的詳細資料，以及如何調查和修復威脅的建議。 

如需 Azure Key Vault 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azurekv)。








## SQL Database 和 SQL 資料倉儲的威脅防護<a name="data-sql"></a>

Azure SQL Database 的先進威脅防護會偵測異常活動，指出不尋常且可能有害的嘗試存取或惡意探索資料庫。

當有可疑的資料庫活動、潛在弱點或 SQL 插入式攻擊，以及異常的資料庫存取和查詢模式時，您會看到警示。

適用于 Azure SQL Database 和 SQL 的先進威脅防護屬於先進的 SQL 安全性功能的[先進資料安全性（ADS）](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)整合套件，涵蓋 Azure SQL 資料庫、Azure SQL Database 受控實例、Azure SQL 資料倉儲資料庫和 azure 虛擬機器上的 SQL server。

如需詳細資訊，請參閱

* [如何為 Azure SQL Database 啟用先進的威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何為 Azure 上的 SQL server 啟用先進的威脅防護虛擬機器](security-center-iaas-advanced-data.md)
* [SQL Database 和 SQL 資料倉儲的威脅防護警示清單](alerts-reference.md#alerts-sql-db-and-warehouse)









## Azure 儲存體的威脅防護<a name="azure-storage"></a>

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

儲存體的先進威脅防護（目前僅適用于 Blob 儲存體）會偵測到不尋常且可能有害的嘗試存取或惡意探索儲存體帳戶。 這一層保護可讓您解決威脅，而不需要您成為安全性專家，並協助您管理安全性監視系統。

如需詳細資訊，請參閱

* [如何為 Azure 儲存體啟用先進的威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 儲存體的威脅防護警示清單](alerts-reference.md#alerts-azurestorage)







## Azure Cosmos DB 的威脅防護<a name="cosmos-db"></a>

Azure Cosmos DB 警示是由不尋常且可能有害的嘗試存取或惡意探索 Azure Cosmos DB 帳戶所產生。

如需詳細資訊，請參閱

* [Azure Cosmos DB 的先進威脅防護（預覽）](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 的威脅防護警示清單（預覽）](alerts-reference.md#alerts-azurecosmos)







## Azure 管理層（Azure Resource Manager）的威脅防護（預覽）<a name ="management-layer"></a>

以 Azure Resource Manager 為基礎的資訊安全中心保護層目前為預覽狀態。

資訊安全中心藉由使用 Azure Resource Manager 事件來提供額外的保護層，這會被視為 Azure 的控制平面。 藉由分析 Azure Resource Manager 記錄，資訊安全中心在 Azure 訂用帳戶環境中偵測到不尋常或可能有害的作業。

如需 Azure Resource Manager （預覽）警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 其中幾個先前的分析是由 Microsoft Cloud App Security 提供技術支援。 若要受益于這些分析，您必須啟用 Cloud App Security 授權。 如果您有 Cloud App Security 授權，則預設會啟用這些警示。 若要停用它們：
>
> 1. 在 [**資訊安全中心**] 分頁中，選取 [**安全性原則**]。 針對您想要變更的訂用帳戶，選取 [**編輯設定**]。
> 2. 選取 [**威脅偵測**]。
> 3. 在 [**啟用**整合] 下，清除 [**允許 Microsoft Cloud App Security 存取我的資料**]，然後選取 [**儲存**]。

>[!NOTE]
>資訊安全中心會將安全性相關的客戶資料儲存在與其資源相同的地理位置。 如果 Microsoft 尚未在資源的地理位置部署資訊安全中心，則會將資料儲存在美國。 啟用 Cloud App Security 時，會根據 Cloud App Security 的地理位置規則來儲存這項資訊。 如需詳細資訊，請參閱[非區域服務的資料儲存體](https://azuredatacentermap.azurewebsites.net/)。



## 來自其他 Microsoft 服務的安全性警示<a name="alerts-other"></a>

### Azure WAF 的威脅防護<a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

Web 應用程式已逐漸成為利用常見已知弱點進行惡意攻擊的目標。 應用程式閘道 WAF 是根據「開啟 Web 應用程式安全性」專案中的核心規則集3.0 或2.2.9。 系統會自動更新 WAF，以防止新的弱點。 

如果您有 Azure WAF 的授權，您的 WAF 警示會串流處理到資訊安全中心，而不需要額外的設定。 如需 WAF 所產生警示的詳細資訊，請參閱[Web 應用程式防火牆 CRS 規則群組與規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### Azure DDoS 保護的威脅防護<a name="azure-ddos"></a>

分散式阻斷服務（DDoS）攻擊已知容易執行。 它們已成為絕佳的安全性考慮，特別是當您將應用程式移至雲端時。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以鎖定可透過網際網路觸達的任何端點。

若要防範 DDoS 攻擊，請購買 Azure DDoS 保護的授權，並確保您遵循應用程式設計的最佳作法。 DDoS 保護提供不同的服務層級。 如需詳細資訊，請參閱[Azure DDoS 保護總覽](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

如需 Azure DDoS 保護警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>後續步驟
若要深入瞭解這些威脅防護功能的安全性警示，請參閱下列文章：

* [所有 Azure 資訊安全中心警示的參考資料表](alerts-reference.md)
* [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
* [在 Azure 資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)
* [匯出安全性警示和建議（預覽）](continuous-export.md)