---
title: 如何將 Azure API 管理與虛擬網路搭配使用
description: 了解如何在「Azure API 管理」中設定虛擬網路連線，然後透過它存取 Web 服務。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/09/2020
ms.author: apimpm
ms.openlocfilehash: 462a44f7766e0ec52ba7156d6de5ae5261e21376
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547372"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何將 Azure API 管理與虛擬網路搭配使用
「Azure 虛擬網路」(VNET) 可讓您將任何 Azure 資源，放在您控制存取權的非網際網路可路由網路中。 然後，可以使用各種 VPN 技術，將這些網路連線到您的內部部署網路。 若要深入了解「Azure 虛擬網路」，請從以下資訊著手：[Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

Azure API 管理可以部署在虛擬網路 (VNET) 內，因此它可以存取網路內的後端服務。 開發人員入口網站與 API 閘道，可設定為從網際網路存取或只從虛擬網路內存取。

> [!NOTE]
> API 匯入文件 URL 必須託管在可公開存取的網際網路位址上。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisites

若要執行本文所述的步驟，您必須具有：

+ 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>啟用 VNET 連線

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>使用 Azure 入口網站啟用 VNET 連線能力

1. 轉到[Azure 門戶](https://portal.azure.com)以查找 API 管理實體。 搜尋並選擇**API 管理服務**。

2. 選擇 API 管理實體。

3. 選擇**虛擬網路**。
4. 將 API 管理執行個體設定為在虛擬網路內部署。

    ![API 管理的虛擬網路功能表][api-management-using-vnet-menu]
5. 選取所需的存取類型：

    * **關閉**:這是預設值。 API 管理未部署到虛擬網路中。

    * **外部**:API 管理閘道和開發人員門戶可通過外部負載均衡器從公共 Internet 訪問。 閘道可以存取虛擬網路內的資源。

        ![公用對等互連][api-management-vnet-public]

    * **內部**:API 管理閘道和開發人員門戶只能通過內部負載均衡器從虛擬網路內訪問。 閘道可以存取虛擬網路內的資源。

        ![私人對等互連][api-management-vnet-private]

6. 如果選擇 **「外部**」或「**內部**」,您將看到預配 API 管理服務的所有區域的清單。 選擇**位置**,然後選擇虛擬**網路與****子網路**。 虛擬網路清單填充了在要配置的區域中設置的 Azure 訂閱中可用的經典和資源管理器虛擬網路。

    > [!IMPORTANT]
    > 將 Azure API 管理執行個體部署至 Resource Manager VNET 時，服務必須在除了 Azure API 管理執行個體之外不包含其他資源的專用子網路中。 如果嘗試將 Azure API 管理執行個體部署到含有其他資源的 Resource Manager VNET 子網路，則部署將會失敗。

    然後，選取 [套用]****。 API 管理實體的**虛擬網路**頁面將隨新的虛擬網路和子網選項進行更新。

    ![選取 VPN][api-management-setup-vpn-select]

7. 在頂部導航列中,選擇 **"保存**",然後選擇 **"應用網路配置**"。

> [!NOTE]
> 「API 管理」執行個體的 VIP 位址在每次啟用或停用 VNET 時都會變更。
> 當 API 管理從**外部**移動到**內部**時,VIP 位址也會更改,反之亦然。
>

> [!IMPORTANT]
> 如果從 VNET 中刪除 API 管理或更改其部署中的 API 管理,則以前使用的 VNET 可以保持鎖定長達六個小時。 在這段期間，將無法刪除該 VNET 或在其中部署新的資源。 對於使用 api 版本 2018-01-01 及更早版本的用戶端,此行為是正確的。 使用 api 版本 2019-01-01 及更高版本的用戶端,一旦刪除關聯的 API 管理服務,VNET 就會被釋放。

## <a name="enable-vnet-connection-using-powershell-cmdlets"></a><a name="enable-vnet-powershell"> </a>使用 PowerShell cmdlet 來啟用 VNET 連線
您也可以使用 PowerShell 來mdlet 來啟用 VNET 連線。

* **在 VNET 中建立 API 管理服務**:使用 cmdlet [New-AzApi 管理](/powershell/module/az.apimanagement/new-azapimanagement)在 VNET 中創建 Azure API 管理服務。

* **在 VNET 中部署現有的 API 管理服務**:使用 cmdlet[更新-AzApi管理區域](/powershell/module/az.apimanagement/update-azapimanagementregion)在虛擬網路內移動現有的 Azure API 管理服務。

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>連接到裝載於虛擬網路內的 Web 服務
在您的「API 管理」服務連接到 VNET 之後，存取 VNET 內的後端服務與存取公用服務便沒有差別。 只要在建立新 API 或編輯現有 API 時，於 [Web 服務 URL]**** 欄位中輸入您 Web 服務的本機 IP 位址或主機名稱 (如果為 VNET 設定了 DNS 伺服器) 即可。

![透過 VPN 加入 API][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>常見的網路組態問題
以下是將 API 管理服務部署到虛擬網路時可能發生的常見錯誤設定問題清單。

* **自訂 DNS 伺服器安裝**：API 管理服務相依於數個 Azure 服務。 當「API 管理」是裝載於具有自訂 DNS 伺服器的 VNET 中時，它必須要解析這些 Azure 服務的主機名稱。 請遵循 [這份](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 有關自訂 DNS 設定的指引。 請參閱下方的連接埠資料表和參考的其他網路需求。

> [!IMPORTANT]
> 如果您打算針對 VNET 使用「自訂 DNS 伺服器」，在將 API 管理服務部署到該伺服器**之前**，應該先將該伺服器設定妥當。 否則，每次變更 DNS 伺服器時，您都必須執行[套用網路設定作業](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/ApiManagementService/ApplyNetworkConfigurationUpdates)來更新 API 管理服務

* **API 管理所需的連接埠**︰使用[網路安全性群組][Network Security Group]可以控制到 API 管理部署於其中的子網路之輸入和輸出流量。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。 搭配 VNET 使用 API 管理時，封鎖這其中一或多個連接埠是另一個常見的錯誤組態問題。

<a name="required-ports"> </a>當 API 管理服務實例託管在 VNET 中時,將使用下表中的埠。

| 來源 / 目的地連接埠 | 方向          | 傳輸通訊協定 |   [服務標籤](../virtual-network/security-overview.md#service-tags) <br> 來源 / 目的地   | 目的 (*)                                                 | 虛擬網路類型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | 輸入            | TCP                | INTERNET / VIRTUAL_NETWORK            | 與 API 管理的用戶端通訊                      | 外部             |
| * / 3443                     | 輸入            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 入口網站和 PowerShell 的管理端點         | 外部和內部  |
| * / 443                  | 輸出           | TCP                | VIRTUAL_NETWORK / Storage             | **與 Azure 儲存體的相依性**                             | 外部和內部  |
| * / 443                  | 輸出           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure 的活動目錄](api-management-howto-aad.md)(如適用)                   | 外部和內部  |
| * / 1433                     | 輸出           | TCP                | VIRTUAL_NETWORK / SQL                 | **存取 Azure SQL 端點**                           | 外部和內部  |
| * / 5671, 5672, 443          | 輸出           | TCP                | VIRTUAL_NETWORK / EventHub            | [紀錄到事件中心策略](api-management-howto-log-event-hubs.md)和監視代理的依賴項 | 外部和內部  |
| * / 445                      | 輸出           | TCP                | VIRTUAL_NETWORK / Storage             | [對 GIT](api-management-configuration-repository-git.md)的 Azure 檔案分享的相依性                      | 外部和內部  |
| * / 1886                     | 輸出           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 將健康情況狀態發佈至 [資源健康狀態] 時所需          | 外部和內部  |
| * / 443                     | 輸出           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 發佈[診斷紀錄和指標](api-management-howto-use-azure-monitor.md)                       | 外部和內部  |
| * / 25                       | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 連線到 SMTP 轉送以便傳送電子郵件                    | 外部和內部  |
| * / 587                      | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 連線到 SMTP 轉送以便傳送電子郵件                    | 外部和內部  |
| * / 25028                    | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 連線到 SMTP 轉送以便傳送電子郵件                    | 外部和內部  |
| * / 6381 - 6383              | 輸入和輸出 | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | 存取 Redis 服務,用於電腦之間的[速率限制](api-management-access-restriction-policies.md#LimitCallRateByKey)政策         | 外部和內部  |
| * / *                        | 輸入            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure 基礎結構負載平衡器                          | 外部和內部  |

>[!IMPORTANT]
> 要成功部署 API 管理服務，就必須有以**粗體**表示其「目的」** 的連接埠。 不過，封鎖其他連接埠將會降低使用和監視執行中服務的能力。

+ **TLS 功能**:要啟用 TLS/SSL 憑證鏈建構和驗證,API 管理服務需要出站網路連接到ocsp.msocsp.com、mscrl.microsoft.com和crl.microsoft.com。 如果您上傳至 API 管理的任何憑證包含 CA 根的完整鏈結，則不需要此相依性。

+ **DNS 存取**：需要有連接埠 53 的輸出存取，才能與 DNS 伺服器通訊。 如果 VPN 閘道的另一端有自訂 DNS 伺服器存在，則必須可從裝載 API 管理的子網路連接該 DNS 伺服器。

+ **計量和健康情況監視**︰對 Azure 監視端點 (解析為屬於下列網域) 的輸出網路連線能力︰

+ **區域服務標記**「:允許向外站連接到存儲、SQL 和 EventHubs 服務標記的 NSG 規則可以使用與包含 API 管理實例的區域(例如,美國西部區域 API 管理實例的 Storage.WestUS)對應的區域版本。 在多區域部署中,每個區域中的 NSG 應允許對該區域的服務標記的流量。

    | Azure 環境 | 端點                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 公用      | <ul><li>gcs.prod.monitoring.core.windows.net(**新**)</li><li>prod.warmpath.msftcloudes.com(**棄用**)</li><li>shoebox2.metrics.microsoftmetrics.com(**新**)</li><li>shoebox2.metrics.nsatc.net(**被棄用**)</li><li>prod3.metrics.microsoftmetrics.com(**新**)</li><li>prod3.metrics.nsatc.net(**被棄用**)</li><li>prod3-black.prod3.metrics.microsoftmetrics.com(**新**)</li><li>prod3-black.prod3.metrics.nsatc.net(**被棄用**)</li><li>prod3-red.prod3.metrics.microsoftmetrics.com(**新**)</li><li>prod3-red.prod3.metrics.nsatc.net(**被棄用**)</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com，其中 `East US 2` 是 eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.microsoftmetrics.com(**新**)</li><li>shoebox2.metrics.nsatc.net(**被棄用**)</li><li>prod3.metrics.microsoftmetrics.com(**新**)</li><li>prod3.metrics.nsatc.net(**被棄用**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.microsoftmetrics.com(**新**)</li><li>shoebox2.metrics.nsatc.net(**被棄用**)</li><li>prod3.metrics.microsoftmetrics.com(**新**)</li><li>prod3.metrics.nsatc.net(**被棄用**)</li><li>prod5.prod.microsoftmetrics.com</li></ul>                                                                                                                                                                                                                                                |

>[!IMPORTANT]
> 上面帶有 dns 區域 **.nsatc.net**的群集更改為 **.microsoftmetrics.com**的群集主要是 DNS 更改。 叢集的 IP 位址不會更改。

+ **SMTP 中繼**`smtpi-co1.msn.com`:SMTP 中繼的出站網路連接,在主`smtpi-ch1.msn.com`機下`smtpi-db3.msn.com``smtpi-sin.msn.com`解析 , 和`ies.global.microsoft.com`

+ **開發人員門戶 CAPTCHA**:開發人員門戶的 CAPTCHA 的出站網路連接`client.hip.live.com`,`partner.hip.live.com`在主機 和下解析。

+ **Azure 入口網站診斷**：從虛擬網路內部使用 API 管理延伸模組時，若要從 Azure 入口網站啟用診斷記錄的流程，則需要在連接埠 443 上有 `dc.services.visualstudio.com` 的輸出存取權。 這有助於針對您在使用延伸模組時所可能面臨的問題進行疑難排解。

+ **使用快速路由或網路虛擬設備強制將流量隧道到預處理防火牆**:常見的客戶配置是定義自己的預設路由 (0.0.0.0/0),該路由強制 API 管理委派子網中的所有流量流經本地防火牆或網路虛擬設備。 此流量流程一定會中斷與 Azure API 管理的連線，因為已在內部部署封鎖輸出流量，或者 NAT 至無法再使用各種 Azure 端點的一組無法辨識位址。 該解決方案要求您執行以下幾項操作:

  * 在部署 API 管理服務的子網上啟用服務終結點。 需要為 Azure Sql、Azure 儲存、Azure 事件 Hub 和 Azure 服務總線啟用[服務終結點][ServiceEndpoints]。 將終結點直接從 API 管理委派子網委派到這些服務,使他們能夠使用 Microsoft Azure 骨幹網,為服務流量提供最佳路由。 如果將服務終結點與強制隧道 Api 管理一起使用,則上述 Azure 服務流量不會強制隧道化。 其他 API 管理服務依賴項流量被強制隧道化,不能丟失或 API 管理服務無法正常運行。
    
  * 從 Internet 到 API 管理服務管理終結點的所有控制平面流量都透過 API 管理託管的特定入站 IP 集路由。 當流量被強制隧道時,回應將不會對稱地映射回這些入站源 IP。 為了克服這一限制,我們需要添加以下使用者定義的路由[(UDR),][UDRs]通過將這些主機路由的目標設置為"Internet"來引導流量返回 Azure。 控制平面流量的入站 IP 記錄[控制平面 IP 位址](#control-plane-ips)

  * 對於強制隧道的其他 API 管理服務依賴項,應該有一種方法來解析主機名並到達終結點。 包括：
      - 指標和健康監測
      - Azure 門戶診斷
      - SMTP 繼電器
      - 開發人員門戶 CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>疑難排解
* **初始安裝**：若未能成功地將 API 管理服務初始部署到子網路，建議您先將虛擬機器部署到相同的子網路。 接下來，再將桌面遠端連線到虛擬機器，並驗證您可以連線到 Azure 訂用帳戶中的下列其中一個資源
    * Azure 儲存體 Blob
    * Azure SQL Database
    * Azure 儲存體資料表

  > [!IMPORTANT]
  > 驗證過連線能力後，請務必先移除子網路中部署的所有資源，再將 API 管理部署至子網路。

* **增量更新**:對網路進行更改時,請參閱[NetworkStatus API,](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/networkstatus)以驗證 API 管理服務是否未丟失對它所依賴的任何關鍵資源的訪問許可權。 連線狀態應該每隔 15 分鐘更新一次。

* **資源導覽連結**：在部署到 Resource Manager 樣式 VNet 子網路時，API 管理會藉由建立資源導覽連結來保留子網路。 如果子網路已包含來自不同提供者的資源，部署將會**失敗**。 同樣地，當您將 API 管理服務移至不同子網路或將它刪除時，我們也會移除該資源導覽連結。

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> 子網路大小需求
Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基礎結構使用的 IP 位址之外，子網路中的每個 API 管理執行個體都會為進階 SKU 的每個單位使用兩個 IP 位址，或為開發人員 SKU 使用一個 IP 位址。 每個執行個體都會保留一個額外 IP 位址作為外部負載平衡器。 部署到內部虛擬網路時,需要內部負載均衡器的附加 IP 位址。

給定上述子網的最小大小計算,其中可以部署 API 管理為 /29,提供三個可用的 IP 位址。

API 管理的每個附加擴展單元都需要兩個 IP 位址。

## <a name="routing"></a><a name="routing"> </a>路由
+ 負載平衡的公用 IP 位址 (VIP) 會保留下來，以供存取所有服務端點。
+ 子網路 IP 範圍的 IP (DIP) 位址會用來存取 VNET 中的資源，而公用 IP 位址 (VIP) 會用來存取 VNET 之外的資源。
+ 您可以在 Azure 入口網站的 [概觀/基本資訊] 刀鋒視窗上找到負載平衡的公用 IP 位址。

## <a name="limitations"></a><a name="limitations"> </a>限制
* 包含「API 管理」執行個體的子網路無法包含任何其他 Azure 資源類型。
* 子網路和「API 管理」服務必須位於同一個訂用帳戶中。
* 包含「API 管理」執行個體的子網路無法跨訂用帳戶移動。
* 針對設定為內部虛擬網路模式的多區域 API 管理部署，使用者需負責管理跨多個區域的負載平衡，因為他們擁有路由。
* 由於平台限制，無法從另一個區域中全域對等互連 VNET 中的資源連線到內部模式中的 API 管理服務。 如需詳細資訊，請參閱[一個虛擬網路中的資源無法與對等互連虛擬網路中的 Azure 內部負載平衡器通訊](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a>控制平面 IP 位址

IP 位址按 Azure**環境**劃分。 允許使用**Global**標記的入站請求時,必須與**區域**特定的 IP 位址一起列入白名單。

| **Azure 環境**|   **區域**|  **IP 位址**|
|-----------------|-------------------------|---------------|
| Azure 公用| 美國中南部(全球)| 104.214.19.224|
| Azure 公用| 美國中北部(全球)| 52.162.110.80|
| Azure 公用| 美國中西部| 52.253.135.58|
| Azure 公用| 南韓中部| 40.82.157.167|
| Azure 公用| 英國西部| 51.137.136.0|
| Azure 公用| 日本西部| 40.81.185.8|
| Azure 公用| 美國中北部| 40.81.47.216|
| Azure 公用| 英國南部| 51.145.56.125|
| Azure 公用| 印度西部| 40.81.89.24|
| Azure 公用| 美國東部| 52.224.186.99|
| Azure 公用| 西歐| 51.145.179.78|
| Azure 公用| 日本東部| 52.140.238.179|
| Azure 公用| 法國中部| 40.66.60.111|
| Azure 公用| 加拿大東部| 52.139.80.117|
| Azure 公用| 阿拉伯聯合大公國北部| 20.46.144.85|
| Azure 公用| 巴西南部| 191.233.24.179|
| Azure 公用| 東南亞| 40.90.185.46|
| Azure 公用| 南非北部| 102.133.130.197|
| Azure 公用| 加拿大中部| 52.139.20.34|
| Azure 公用| 南韓南部| 40.80.232.185|
| Azure 公用| 印度中部| 13.71.49.1|
| Azure 公用| 美國西部| 13.64.39.16|
| Azure 公用| 澳大利亞東南部| 20.40.160.107|
| Azure 公用| 澳大利亞中部| 20.37.52.67|
| Azure 公用| 印度南部| 20.44.33.246|
| Azure 公用| 美國中部| 13.86.102.66|
| Azure 公用| 澳大利亞東部| 20.40.125.155|
| Azure 公用| 美國西部 2| 51.143.127.203|
| Azure 公用| 美國東部 2 EUAP| 52.253.229.253|
| Azure 公用| 美國中部 EUAP| 52.253.159.160|
| Azure 公用| 美國中南部| 20.188.77.119|
| Azure 公用| 美國東部 2| 20.44.72.3|
| Azure 公用| 北歐| 52.142.95.35|
| Azure 公用| 東亞| 52.139.152.27|
| Azure 公用| 法國南部| 20.39.80.2|
| Azure 公用| 瑞士西部| 51.107.96.8|
| Azure 公用| 澳大利亞中部 2| 20.39.99.81|
| Azure 公用| 阿拉伯聯合大公國中部| 20.37.81.41|
| Azure 公用| 瑞士北部| 51.107.0.91|
| Azure 公用| 南非西部| 102.133.0.79|
| Azure 公用| 德國中西部| 51.116.96.0|
| Azure 公用| 德國北部| 51.116.0.0|
| Azure 公用| 挪威東部| 51.120.2.185|
| Azure 公用| 挪威西部| 51.120.130.134|
| Azure China 21Vianet| 中國北部(全球)| 139.217.51.16|
| Azure China 21Vianet| 華東(全球)| 139.217.171.176|
| Azure China 21Vianet| 中國北部| 40.125.137.220|
| Azure China 21Vianet| 中國東部| 40.126.120.30|
| Azure China 21Vianet| 中國北部 2| 40.73.41.178|
| Azure China 21Vianet| 中國東部 2| 40.73.104.4|
| Azure Government| 美國弗吉尼亞州州長(全球)| 52.127.42.160|
| Azure Government| 美國德克薩斯州(全球)| 52.127.34.192|
| Azure Government| 美國政府維吉尼亞州| 52.227.222.92|
| Azure Government| 美國政府愛荷華州| 13.73.72.21|
| Azure Government| 美國政府亞利桑那州| 52.244.32.39|
| Azure Government| 美國政府德克薩斯州| 52.243.154.118|
| Azure Government| USDoD 中心| 52.182.32.132|
| Azure Government| 美國國防部東| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>相關內容
* [使用 VPN 閘道將虛擬網路連線到後端](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [從不同的部署模型連接虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫](api-management-howto-api-inspector.md)
* [虛擬網路 常見問題](../virtual-network/virtual-networks-faq.md)
* [服務標籤](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
