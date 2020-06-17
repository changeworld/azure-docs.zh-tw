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
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 850b06153a25020f36a4c7df1863e5a576495f3b
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744166"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure 資訊安全中心內的威脅防護

資訊安全中心在您環境的任何區域中偵測到威脅時，會產生警示。 這些警示會說明受影響資源的詳細資料、建議的補救步驟，以及在某些情況下會說明觸發邏輯應用程式以進行回應的選項。

Azure 資訊安全中心的威脅防護可為您的環境提供全面的防護：

* **Azure 計算資源的威脅防護**：Windows 電腦、Linux 機器、Azure App Service 和 Azure 容器

* **Azure 資料資源的威脅防護**：SQL Database 和 SQL 資料倉儲、Azure 儲存體，以及 Azure Cosmos DB

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

* **無檔案攻擊偵測** <a name="windows-fileless"></a> - 以您的端點為目標的無檔案攻擊，是很常見的。 為了規避偵測，無檔案攻擊會將惡意承載插入記憶體中。 攻擊者承載會保存在遭入侵之程序的記憶體中，並執行各式各樣的惡意活動。

    透過無檔案攻擊偵測，自動化記憶體鑑識技術可識別無檔案攻擊的工具組、技術和行為。 此解決方案會在執行階段定期掃描您的機器，並直接從安全性關鍵程序的記憶體中擷取深入解析。

    其會尋找惡意探索、程式碼插入和執行惡意承載的證據。 無檔案攻擊偵測會產生詳細的安全性警示，以加速警示分級、相互關聯和下游回應時間。 此方法可補強以事件為基礎的 EDR 解決方案，以提供更大的偵測涵蓋範圍。

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





## <a name="threat-protection-for-azure-containers"></a>Azure 容器的威脅防護 <a name="azure-containers"></a>

> [!NOTE]
> 此服務目前無法在 Azure 政府和主權雲端區域中使用。

資訊安全中心可為您的容器化環境提供即時威脅防護，並產生可疑活動的警示。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

資訊安全中心提供不同層級的威脅防護： 

* **主機層級** - 資訊安全中心的代理程式 (適用於標準層，請參閱[定價](security-center-pricing.md)以取得詳細資訊) 可監視 Linux 是否有可疑活動。 此代理程式會針對源自節點或執行於其上之容器的可疑活動觸發警示。 這類活動的範例包括 Web 殼層偵測，以及與已知可疑 IP 位址的連線。

    為了深入了解容器化環境的安全性，代理程式會監視容器特有的分析。 其會針對多種事件觸發警示，例如，建立具特殊權限的容器、對 API 伺服器進行可疑的存取，以及在 Docker 容器內執行安全殼層 (SSH) 伺服器。

    >[!IMPORTANT]
    > 如果您選擇不在主機上安裝此代理程式，則只會收到部分的威脅防護權益和安全性警示。 您仍會收到與網路分析以及與惡意伺服器通訊有關的警示。

    如需主機層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-containerhost)。


* 在 **AKS 叢集層級**，威脅防護立基於對 Kubernetes 稽核記錄的分析。 若要啟用此**無代理程式**監視，請從 [定價和設定] 頁面將 Kubernetes 選項新增至您的訂用帳戶 (請參閱[定價](security-center-pricing.md))。 為了在此層級產生警示，資訊安全中心會使用 AKS 所擷取的記錄來監視受 AKS 管理的服務。 此層級的事件範例包括公開 Kubernetes 儀表板、建立高特殊權限的角色，以及建立敏感性裝載。

    >[!NOTE]
    > 資訊安全中心會針對在訂用帳戶設定上啟用 Kubernetes 選項後所發生的 Azure Kubernetes Service 動作和部署產生安全性警示。 

    如需 AKS 叢集層級警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-akscluster)。

此外，我們的全球安全性研究小組會持續監視威脅的態勢。 他們會在發現容器特有的警示和弱點時加以新增。

> [!TIP]
> 您可以依照[此部落格文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的指示來模擬容器警示。








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL Database 和 SQL 資料倉儲的威脅防護 <a name="data-sql"></a>

適用於 Azure SQL Database 的進階威脅防護會偵測異常活動，這些活動代表有不尋常及可能有害的活動試圖存取或惡意探索資料庫。

有可疑的資料庫活動、潛在弱點或 SQL 插入式攻擊，以及異常的資料庫存取和查詢模式時，您將會看到警示。

Azure SQL Database 和 SQL 的進階威脅防護是[進階資料安全性 (ADS) ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) 的 SQL 安全性功能整合套件的一部分，其範圍涵蓋 Azure SQL Database、Azure SQL Database 受控執行個體、Azure SQL 資料倉儲資料庫，和 Azure 虛擬機器上的 SQL 伺服器。

如需詳細資訊，請參閱

* [如何啟用 Azure SQL 資料庫的進階威脅防護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何為 Azure 虛擬機器上的 SQL 伺服器啟用進階威脅防護](security-center-iaas-advanced-data.md)
* [SQL Database 和 SQL 資料倉儲的威脅防護警示清單](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Azure 儲存體的威脅防護 <a name="azure-storage"></a>

儲存體的進階威脅防護可偵測儲存體帳戶中異常而且可能有害的存取或攻擊意圖。 這一層保護可讓您直接解決威脅，而無須成為安全性專家，並且可協助您管理安全性監視系統。

Azure 儲存體的進階威脅防護目前僅適用於 [Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)。 

這項服務適用於所有的公用雲端和美國政府雲端，但不是用於其他主權或 Azure Government 雲端區域。

如需定價詳細資料 (包含 30 天免費試用)，請參閱 [Azure 資訊安全中心定價頁面](https://azure.microsoft.com/pricing/details/security-center/)。

如需詳細資訊，請參閱

* [如何啟用 Azure 儲存體的進階威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 儲存體的威脅防護警示清單](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> 您可以依照[此部落格文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的指示來模擬 Azure 儲存體警示。




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure Cosmos DB 的威脅防護 <a name="cosmos-db"></a>

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

如需資訊安全中心如何使用網路相關訊號來套用威脅防護的詳細資訊，請參閱[資訊安全中心的啟發式 DNS 偵測](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理層 (Azure Resource Manager) 的威脅防護 (預覽)<a name ="management-layer"></a>

以 Azure Resource Manager 為基礎的資訊安全中心保護層目前處於預覽階段。

資訊安全中心會利用 Azure Resource Manager 事件來提供額外的保護層，我們將其視為 Azure 的控制平面。 藉由分析 Azure Resource Manager 記錄，資訊安全中心會在 Azure 訂用帳戶環境中偵測不尋常或可能有害的作業。

如需 Azure Resource Manager (預覽) 警示的清單，請參閱[警示的參考資料表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 前述的幾項分析由 Microsoft Cloud App Security 提供技術支援。 若要受益於這些分析，您必須啟用 Cloud App Security 授權。 如果您有 Cloud App Security 授權，則依預設會啟用這些警示。 若要停用警示：
>
> 1. 在 [資訊安全中心] 刀鋒視窗中，選取 [安全性原則]。 針對您要變更的訂用帳戶，選取 [編輯設定]。
> 2. 選取 [威脅偵測]。
> 3. 在 [啟用整合] 下方，清除 [允許 Microsoft Cloud App Security 存取我的資料]，然後選取 [儲存]。

>[!NOTE]
>資訊安全中心會將關乎安全性的客戶資料儲存在與其資源相同的地理位置。 如果 Microsoft 尚未在資源的地理位置部署資訊安全中心，則會將資料儲存在美國。 啟用 Cloud App Security 時，會根據 Cloud App Security 的地理位置規則來儲存這項資訊。 如需詳細資訊，請參閱[非區域服務的資料儲存體](https://azuredatacentermap.azurewebsites.net/)。








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