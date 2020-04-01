---
title: Azure 安全中心中的威脅保護
description: 本主題介紹受 Azure 安全中心威脅保護功能保護的資源
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 395f4b5481fcf2028d6bfe736e58c3174a0c80b2
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435338"
---
# <a name="threat-protection-in-azure-security-center"></a>Azure 安全中心中的威脅保護

當安全中心檢測到環境的任何區域中的威脅時,它會生成警報。 這些警報描述受影響資源的詳細資訊、建議的補救步驟,在某些情況下還描述了在回應中觸發邏輯應用的選項。

Azure 安全中心的威脅防護為您的環境提供了全面的防禦:

* **Azure 計算資源的威脅保護**:Windows 計算機、Linux 計算機、Azure 應用服務和 Azure 容器

* **Azure 資料資源的威脅保護**:SQL 資料庫和 SQL 資料倉儲、Azure 儲存和 Azure 宇宙資料庫

* **Azure 服務層的威脅保護**:Azure 網路層、Azure 管理層(Azure 資源管理員)(預覽)和 Azure 金鑰保管庫(預覽)

無論是安全中心生成警報,還是安全中心從其他安全產品接收警報,您都可以匯出警報。 要將警報導出到 Azure Sentinel(或第三方 SIEM)或任何其他外部工具,請按照[將警報導出到 SIEM](continuous-export.md)中的說明進行操作。 




## <a name="threat-protection-for-windows-machines"></a>Windows 電腦的威脅保護<a name="windows-machines"></a>

Azure 安全中心與 Azure 服務整合,以監視和保護基於 Windows 的電腦。 安全中心以易於使用的格式提供所有這些服務的警報和補救建議。

* **微軟防禦者ATP** <a name="windows-atp"></a> - 安全中心通過與微軟防禦者高級威脅保護 (ATP) 整合來擴展其雲工作負載保護平臺。 它們共同提供全面的端點檢測和回應 (EDR) 功能。

    > [!IMPORTANT]
    > 使用安全中心的 Windows 伺服器上會自動啟用 Microsoft Defender ATP 感測器。

    當微軟防禦者 ATP 檢測到威脅時,它會觸發警報。 警報顯示在安全中心儀錶板上。 從儀錶板,您可以透視到 Microsoft Defender ATP 主控台,並執行詳細調查以發現攻擊的範圍。 有關微軟後衛 ATP 的更多資訊,請參閱[微軟防禦者 ATP 服務的板載伺服器](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)。

* **崩潰傾印分析**<a name="windows-dump"></a>- 當軟體崩潰時,崩潰轉儲在崩潰時捕獲部分記憶體。

    崩潰可能是由惡意軟體引起的或包含惡意軟體。 為了避免被安全產品檢測到,各種形式的惡意軟體使用無文件攻擊,避免寫入磁碟或加密寫入磁碟的軟體元件。 使用傳統的基於磁碟的方法很難檢測到這種類型的攻擊。

    但是,通過使用記憶體分析,您可以檢測到這種攻擊。 通過分析崩潰轉儲中的記憶體,安全中心可以檢測攻擊正在使用的技術。 例如,攻擊可能試圖利用軟體中的漏洞,訪問機密數據,並秘密地在受攻擊的機器中持續存在。 安全中心以對主機的性能影響最小來完成此工作。

    有關崩潰轉儲分析警報的詳細資訊,請參閱[警報的參考表](alerts-reference.md#alerts-windows)。

* **無文件攻擊檢測**<a name="windows-fileless"></a>- 針對端點的無文件攻擊很常見。 為了避免檢測,無文件攻擊將惡意負載注入記憶體。 攻擊者負載保留在受破壞進程的記憶體中,並執行各種惡意活動。

    通過無文件攻擊檢測,自動記憶體取證技術可識別無文件攻擊工具組、技術和行為。 此解決方案會在運行時定期掃描計算機,並直接從安全關鍵型進程的記憶體中提取見解。

    它找到惡意負載的利用、代碼注入和執行的證據。 無文件攻擊檢測生成詳細的安全警報,以加快警報會審、關聯和下游響應時間。 此方法補充了基於事件的 EDR 解決方案,提供了更大的檢測覆蓋率。

    有關無文件攻擊檢測警報的詳細資訊,請參閱[警報的參考表](alerts-reference.md#alerts-windows)。

> [!TIP]
> 您可以通過下載[Azure 安全中心行動手冊:安全警報](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)來類比 Windows 警報。






## <a name="threat-protection-for-linux-machines"></a>Linux 電腦的威脅保護<a name="linux-machines"></a>

安全中心使用最常見的Linux審核框架之一的**審核**框架,從Linux計算機收集審計記錄。 審核生活在主線內核中。 

* **Linux 審核警報和日誌分析代理集成**<a name="linux-auditd"></a>- 審核的系統由一個內核級子系統組成,負責監視系統調用。 它按指定的規則集篩選它們,並將它們的消息寫入套接字。 安全中心將審核包的功能集成到日誌分析代理中。 此整合支援在所有支援的 Linux 發行版中收集審核的事件,而無需任何先決條件。

    使用 Linux 代理的日誌分析代理收集、豐富和聚合到事件中審核的記錄。 安全中心不斷添加新的分析,使用 Linux 信號檢測雲和本地 Linux 電腦上的惡意行為。 與 Windows 功能類似,這些分析跨越可疑進程、可疑的登錄嘗試、內核模組載入和其他活動。 這些活動可能表明機器受到攻擊或已被入侵。  

    有關 Linux 警報的清單,請參閱[警報的參考表](alerts-reference.md#alerts-linux)。

> [!TIP]
> 您可以通過下載[Azure 安全中心行動手冊:Linux 檢測](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)來類比 Linux 警報。





## <a name="threat-protection-for-azure-app-service"></a>Azure 應用服務的威脅保護<a name="app-services"></a>

> [!NOTE]
> 此服務當前不在 Azure 政府和主權雲區域中。

安全中心使用雲的規模來識別針對通過應用服務運行的應用程式的攻擊。 攻擊者將調查 Web 應用程式以查找和利用弱點。 在路由到特定環境之前,對 Azure 中運行的應用程式的請求將經過多個閘道,其中將對其進行檢查和記錄。 然後,此數據用於識別漏洞利用和攻擊者,並瞭解稍後將使用的新模式。

通過使用 Azure 作為雲端供應商的可見性,安全中心分析應用服務內部日誌,以確定對多個目標的攻擊方法。 例如,方法包括廣泛的掃描和分散式攻擊。 這種類型的攻擊通常來自 IP 的一小部分,並顯示爬網到多個主機上的類似終結點的模式。 攻擊正在搜索易受攻擊的頁面或外掛程式,無法從單個主機的角度進行識別。

如果您正在運行基於 Windows 的應用服務計畫,安全中心還可以訪問基礎沙箱和 VM。 與上述日誌數據一起,基礎結構可以講述故事,從在野外迴圈的新攻擊到客戶機器中的危害。 因此,即使安全中心是在 Web 應用被利用後部署的,它也可能檢測到正在進行的攻擊。

有關 Azure 應用服務警報的清單,請參閱[警報的參考表](alerts-reference.md#alerts-azureappserv)。

有關應用服務計劃的詳細資訊,請參閱[應用服務計畫](https://azure.microsoft.com/pricing/details/app-service/plans/)。





## <a name="threat-protection-for-azure-containers"></a>Azure 容器的威脅保護<a name="azure-containers"></a>

> [!NOTE]
> 此服務當前不在 Azure 政府和主權雲區域中。

安全中心為您的容器化環境提供即時威脅保護,並針對可疑活動生成警報。 您可以使用這項資訊來快速修復安全性問題，並改善您容器的安全性。

安全中心在不同級別提供威脅保護: 

* **主機級**- 安全中心的代理(在標準層上提供,請參閱[定價](security-center-pricing.md)的詳細資訊)監視 Linux 中的可疑活動。 代理會觸發來自節點或運行在節點上的容器的可疑活動的警報。 此類活動的範例包括 Web 外殼檢測和與已知可疑 IP 位址的連接。

    為了更深入地瞭解容器化環境的安全性,代理監視特定於容器的分析。 它將觸發對事件警報,例如特權容器創建、對 API 伺服器的可疑存取以及 Docker 容器內執行的安全外殼 (SSH) 伺服器。

    >[!IMPORTANT]
    > 如果選擇不在主機上安裝代理,則只會收到威脅保護權益和安全警報的子集。 您仍將收到與網路分析和與惡意伺服器通信相關的警報。

    有關主機級警報的清單,請參閱[警報的參考表](alerts-reference.md#alerts-containerhost)。


* 在**AKS集群級別**,威脅保護基於分析庫伯內斯的審計日誌。 要啟用此**無代理**監視,請從**定價&設置**頁將 Kubernetes 選項添加到訂閱中(請參閱[定價](security-center-pricing.md))。 若要在此級別生成警報,安全中心使用 AKS 檢索的日誌監視 AKS 託管的服務。 此級別的事件示例包括公開的 Kubernetes 儀表板、創建高特權角色以及創建敏感裝載。

    >[!NOTE]
    > 安全中心為在訂閱設置上啟用 Kubernetes 選項後發生的 Azure 庫伯內特服務操作和部署生成安全警報。 

    有關 AKS 叢集等級警示的清單,請參閱[警報的參考表](alerts-reference.md#alerts-akscluster)。

此外,我們的全球安全研究人員團隊不斷監控威脅形勢。 在發現容器時,它們會添加特定於容器的警報和漏洞。

> [!TIP]
> 您可以按照[本博客文章](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)中的說明來類比容器警報。








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>SQL 資料庫與 SQL 資料倉儲的威脅保護<a name="data-sql"></a>

Azure SQL 資料庫的高級威脅保護可檢測異常活動,指示訪問或利用資料庫的異常和潛在有害嘗試。

當存在可疑的資料庫活動、潛在漏洞或 SQL 注入攻擊以及異常的資料庫訪問和查詢模式時,您將看到警報。

Azure SQL 資料庫和 SQL 的進階威脅防護是[進階 SQL 安全功能的進階資料安全 (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)統一包的一部分,涵蓋 Azure SQL 資料庫、Azure SQL 資料庫託管實例、Azure SQL 資料倉儲資料庫和 Azure 虛擬機上的 SQL 伺服器。

如需詳細資訊，請參閱

* [如何為 Azure SQL 資料庫啟用進階威脅保護](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [如何為 Azure 虛擬機器上的 SQL 伺服器啟用進階威脅保護](security-center-iaas-advanced-data.md)
* [SQL 資料庫與 SQL 資料倉儲的威脅防護警示清單](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Azure 儲存的威脅保護<a name="azure-storage"></a>

> [!NOTE]
> 此服務在美國政府雲中可用,但沒有其他主權或 Azure 政府雲區域。

進階儲存威脅保護(目前僅適用於 Blob 儲存)可檢測存取或利用儲存帳戶的異常和潛在有害嘗試。 此保護層允許您解決威脅,而無需您成為安全專家,並説明您管理安全監控系統。

如需詳細資訊，請參閱

* [如何為 Azure 儲存啟用進階威脅防護](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [Azure 儲存的威脅保護警報清單](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> 您可以按照[本部落格文章](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131)中的說明來類比 Azure 儲存警報。




## <a name="threat-protection-for-azure-cosmos-db"></a>Azure 宇宙 DB 的威脅保護<a name="cosmos-db"></a>

Azure 宇宙資料庫警報由訪問或利用 Azure Cosmos DB 帳戶的異常且可能有害的嘗試生成。

如需詳細資訊，請參閱

* [Azure 宇宙 DB 的進階威脅保護(預覽版)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure 宇宙 DB 的威脅保護警報列表(預覽版)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Azure 網路層的威脅保護<a name="network-layer"></a>

安全中心網路層分析基於[示例 IPFIX 數據](https://en.wikipedia.org/wiki/IP_Flow_Information_Export),這些數據是由 Azure 核心路由器收集的數據包標頭。 基於此數據饋送,安全中心使用機器學習模型來識別和標記惡意流量活動。 安全中心還使用 Microsoft 威脅情報資料庫來豐富 IP 位址。

某些網路配置可能會限制安全中心生成可疑網路活動的警報。 對於安全中心生成網路警報,請確保:

- 您的虛擬機具有公共 IP 位址(或位於具有公共 IP 位址的負載均衡器上)。

- 外部 IDS 解決方案不會阻止虛擬機器的網路出口流量。

- 在發生可疑通信的整個小時內,虛擬機都分配了相同的 IP 位址。 這也適用於作為託管服務的一部分創建的 VM(例如 AKS、數據磚)。

有關 Azure 網路圖層警報的清單,請參閱[警報的參考表](alerts-reference.md#alerts-azurenetlayer)。

有關安全中心如何使用網路相關信號應用威脅保護的詳細資訊,請參閱[安全中心中的啟發式 DNS 檢測](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)。



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Azure 管理層(Azure 資源管理員)的威脅保護(預覽)<a name ="management-layer"></a>

基於 Azure 資源管理器的安全中心保護層當前處於預覽狀態。

安全中心通過使用 Azure 資源管理器事件提供額外的保護層,該事件被視為 Azure 的控制平面。 通過分析 Azure 資源管理器記錄,安全中心檢測 Azure 訂閱環境中的異常或潛在有害操作。

有關 Azure 資源管理員(預覽)警示的清單,請參閱[警報的參考表](alerts-reference.md#alerts-azureresourceman)。



>[!NOTE]
> 前面的幾個分析由 Microsoft 雲應用安全支援。 要從這些分析中獲益,您必須啟動雲應用安全許可證。 如果您有雲端應用安全許可證,則默認情況下將啟用這些警報。 要關閉警報:
>
> 1. 在 **「安全中心**」邊欄選項卡中,選擇**安全原則**。 對於要更改的訂閱,請選擇 **「編輯設置**」。
> 2. 選擇**威脅偵測**。
> 3. 在**啟用整合**下,清除**允許 Microsoft 雲應用安全訪問我的數據**,然後選擇「**保存**」 。。

>[!NOTE]
>安全中心將安全相關的客戶數據存儲在與其資源相同的地理位置中。 如果 Microsoft 尚未在資源的地理部署安全中心,則它將數據存儲在美國。 啟用雲應用安全性后,此資訊將按照雲應用安全性的地理位置規則進行存儲。 有關詳細資訊,請參閱[非區域服務的資料儲存](https://azuredatacentermap.azurewebsites.net/)。








## <a name="threat-protection-for-azure-key-vault-preview"></a>Azure 金鑰保管庫的威脅保護(預覽版)<a name="azure-keyvault"></a>

> [!NOTE]
> 此服務當前不在 Azure 政府和主權雲區域中。

Azure Key Vault 是用來保護加密金鑰和祕密 (例如憑證、連接字串和密碼) 的雲端服務。 

Azure 安全中心包括 Azure 密鑰保管庫的 Azure 本機高級威脅保護,提供了額外的安全智慧層。 安全中心檢測訪問或利用密鑰保管庫帳戶的異常且可能有害的嘗試。 此保護層允許您無需成為安全專家即可應對威脅,而無需管理第三方安全監控系統。  

當異常活動發生時,安全中心會顯示警報,並選擇性地通過電子郵件將其發送給訂閱管理員。 這些警報包括可疑活動的詳細資訊以及如何調查和補救威脅的建議。 

有關 Azure 金鑰保管庫警報的清單,請參閱[警報的參考表](alerts-reference.md#alerts-azurekv)。





## <a name="threat-protection-for-other-microsoft-services"></a>其他 Microsoft 服務的威脅保護<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Azure WAF 的威脅保護<a name="azure-waf"></a>

Azure 應用程式閘道提供了 Web 應用程式防火牆 (WAF)，可為 Web 應用程式提供集中式保護，免於遭遇常見的攻擊和弱點。

Web 應用程式已逐漸成為利用常見已知弱點進行惡意攻擊的目標。 應用程式閘道 WAF 基於開啟 Web 應用程式安全專案的核心規則集 3.0 或 2.2.9。 WAF 會自動更新,以防止新的漏洞。 

如果您有 Azure WAF 的授權,則 WAF 警報將流式傳輸到安全中心,無需其他配置。 有關 WAF 產生的警示的詳細資訊,請參閱 Web[應用程式防火牆 CRS 規則組與規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)。


### <a name="threat-protection-for-azure-ddos-protection"></a>Azure DDoS 保護的威脅保護<a name="azure-ddos"></a>

眾所周知,分散式拒絕服務 (DDoS) 攻擊很容易執行。 它們已成為一個很大的安全問題,尤其是在您將應用程式遷移到雲時。 

DDoS 攻擊會嘗試耗盡應用程式的資源，讓合法使用者無法使用該應用程式。 DDoS 攻擊可以針對任何可以通過 Internet 到達的終結點。

要抵禦 DDoS 攻擊,請購買 Azure DDoS 保護許可證,並確保遵循應用程式設計最佳實踐。 DDoS 保護提供不同的服務層。 有關詳細資訊,請參閱[Azure DDoS 保護概述](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)。

有關 Azure DDoS 保護警報的清單,請參閱[警報的參考表](alerts-reference.md#alerts-azureddos)。


## <a name="next-steps"></a>後續步驟
要瞭解有關這些威脅防護功能的安全警報的更多詳細資訊,請參閱以下文章:

* [所有 Azure 安全中心警報的參考表](alerts-reference.md)
* [Azure 資訊安全中心的安全性警示](security-center-alerts-overview.md)
* [在 Azure 資訊安全中心管理和回應安全性警示](security-center-managing-and-responding-alerts.md)
* [匯出安全警報和建議(預覽版)](continuous-export.md)