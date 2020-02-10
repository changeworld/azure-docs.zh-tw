---
title: 如何將 Azure API 管理與虛擬網路搭配使用
description: 了解如何在「Azure API 管理」中設定虛擬網路連線，然後透過它存取 Web 服務。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/05/2020
ms.author: apimpm
ms.openlocfilehash: c5a1aaac0edea1e5ab2e6cdf35f91f61eed23db5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047489"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何將 Azure API 管理與虛擬網路搭配使用
「Azure 虛擬網路」(VNET) 可讓您將任何 Azure 資源，放在您控制存取權的非網際網路可路由網路中。 然後，可以使用各種 VPN 技術，將這些網路連線到您的內部部署網路。 若要深入了解「Azure 虛擬網路」，請從以下資訊著手：[Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

Azure API 管理可以部署在虛擬網路 (VNET) 內，因此它可以存取網路內的後端服務。 開發人員入口網站與 API 閘道，可設定為從網際網路存取或只從虛擬網路內存取。

> [!NOTE]
> API 匯入檔 URL 必須裝載在可公開存取的網際網路位址上。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisites

若要執行本文所述的步驟，您必須具有：

+ 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。

## <a name="enable-vpn"></a>啟用 VNET 連線

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>使用 Azure 入口網站啟用 VNET 連線能力

1. 請移至[Azure 入口網站](https://portal.azure.com)來尋找您的 API 管理實例。 搜尋並選取 [ **API 管理服務**]。

2. 選擇您的 API 管理實例。

3. 選取 [虛擬網路]。
4. 將 API 管理執行個體設定為在虛擬網路內部署。

    ![API 管理的虛擬網路功能表][api-management-using-vnet-menu]
5. 選取所需的存取類型：

    * **Off**：這是預設值。 API 管理不會部署到虛擬網路中。

    * **外部**：可透過外部負載平衡器從公用網際網路存取「API 管理」閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

        ![公用對等互連][api-management-vnet-public]

    * **內部**：只能透過內部負載平衡器，從虛擬網路中存取 API 管理閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

        ![私人對等互連][api-management-vnet-private]

6. 如果您選取 [**外部**] 或 [**內部**]，您會看到布建您的 API 管理服務的所有區域清單。 選擇 [**位置**]，然後挑選其 [**虛擬網路**] 和 [**子網**]。 [虛擬網路] 清單會在您要設定的區域中，填入您的 Azure 訂用帳戶中所提供的傳統和 Resource Manager 虛擬網路。

    > [!IMPORTANT]
    > 將 Azure API 管理執行個體部署至 Resource Manager VNET 時，服務必須在除了 Azure API 管理執行個體之外不包含其他資源的專用子網路中。 如果嘗試將 Azure API 管理執行個體部署到含有其他資源的 Resource Manager VNET 子網路，則部署將會失敗。

    然後，選取 [套用]。 您的 API 管理實例的 [**虛擬網路**] 頁面會以新的虛擬網路和子網選擇更新。

    ![選取 VPN][api-management-setup-vpn-select]

7. 在上方導覽列中，選取 [**儲存**]，然後選取 [套用**網路**設定]。

> [!NOTE]
> 「API 管理」執行個體的 VIP 位址在每次啟用或停用 VNET 時都會變更。
> 當 API 管理從**外部**移到**內部**，或反之亦然時，VIP 位址也會變更。
>

> [!IMPORTANT]
> 如果您從 VNET 移除 API 管理，或變更其部署所在的應用程式，則先前使用的 VNET 最多可以保持鎖定達六個小時。 在這段期間，將無法刪除該 VNET 或在其中部署新的資源。 此行為適用于使用 api 版本2018-01-01 和更早版本的用戶端。 使用 api 版本2019-01-01 和更新版本的用戶端，會在相關聯的 API 管理服務刪除時立即釋放 VNET。

## <a name="enable-vnet-powershell"></a>使用 PowerShell CMDLET 啟用 VNET 連線
您也可以使用 PowerShell 來mdlet 來啟用 VNET 連線。

* 在**vnet 內建立 API 管理服務**：使用 Cmdlet [AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement)在 VNET 內建立 Azure api 管理服務。

* 在**VNET 內部署現有的 Api 管理服務**：使用 Cmdlet[更新-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) ，將現有的 Azure api 管理服務移至虛擬網路內。

## <a name="connect-vnet"></a>連接到裝載于虛擬網路內的 web 服務
在您的「API 管理」服務連接到 VNET 之後，存取 VNET 內的後端服務與存取公用服務便沒有差別。 只要在建立新 API 或編輯現有 API 時，於 [Web 服務 URL] 欄位中輸入您 Web 服務的本機 IP 位址或主機名稱 (如果為 VNET 設定了 DNS 伺服器) 即可。

![透過 VPN 加入 API][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"></a>常見的網路設定問題
以下是將 API 管理服務部署到虛擬網路時可能發生的常見錯誤設定問題清單。

* **自訂 DNS 伺服器安裝**：API 管理服務相依於數個 Azure 服務。 當「API 管理」是裝載於具有自訂 DNS 伺服器的 VNET 中時，它必須要解析這些 Azure 服務的主機名稱。 請遵循 [這份](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 有關自訂 DNS 設定的指引。 請參閱下方的連接埠資料表和參考的其他網路需求。

> [!IMPORTANT]
> 如果您打算針對 VNET 使用「自訂 DNS 伺服器」，在將 API 管理服務部署到該伺服器**之前**，應該先將該伺服器設定妥當。 否則，每次變更 DNS 伺服器時，您都必須執行[套用網路設定作業](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates)來更新 API 管理服務

* **Api 管理所需的埠**：在部署 api 管理的子網中，輸入和輸出流量可以使用[網路安全性群組][Network Security Group]來控制。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。 搭配 VNET 使用 API 管理時，封鎖這其中一或多個連接埠是另一個常見的錯誤組態問題。

<a name="required-ports"></a>當「API 管理」服務實例裝載于 VNET 時，會使用下表中的埠。

| 來源 / 目的地連接埠 | 方向          | 傳輸通訊協定 |   [服務標記](../virtual-network/security-overview.md#service-tags) <br> 來源 / 目的地   | 目的 (*)                                                 | 虛擬網路類型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | 輸入            | TCP                | INTERNET / VIRTUAL_NETWORK            | 與 API 管理的用戶端通訊                      | 外部             |
| * / 3443                     | 輸入            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 入口網站和 PowerShell 的管理端點         | 外部和內部  |
| * / 80, 443                  | 輸出           | TCP                | VIRTUAL_NETWORK / Storage             | **與 Azure 儲存體的相依性**                             | 外部和內部  |
| * / 80, 443                  | 輸出           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Azure Active Directory (如果適用)                   | 外部和內部  |
| * / 1433                     | 輸出           | TCP                | VIRTUAL_NETWORK / SQL                 | **存取 Azure SQL 端點**                           | 外部和內部  |
| */5671、5672、443          | 輸出           | TCP                | VIRTUAL_NETWORK / EventHub            | 「記錄到事件中樞」原則和監視代理程式的相依性 | 外部和內部  |
| * / 445                      | 輸出           | TCP                | VIRTUAL_NETWORK / Storage             | 與「適用於 GIT 的 Azure 檔案共用」的相依性                      | 外部和內部  |
| * / 1886                     | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 將健康情況狀態發佈至 [資源健康狀態] 時所需          | 外部和內部  |
| * / 443                     | 輸出           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 發佈診斷記錄和計量                        | 外部和內部  |
| * / 25                       | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 連線到 SMTP 轉送以便傳送電子郵件                    | 外部和內部  |
| * / 587                      | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 連線到 SMTP 轉送以便傳送電子郵件                    | 外部和內部  |
| * / 25028                    | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 連線到 SMTP 轉送以便傳送電子郵件                    | 外部和內部  |
| * / 6381 - 6383              | 輸入和輸出 | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | 針對 RoleInstances 之間的 Redis 執行個體存取 Azure 快取          | 外部和內部  |
| * / \*                        | 輸入            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure 基礎結構負載平衡器                          | 外部和內部  |

>[!IMPORTANT]
> 要成功部署 API 管理服務，就必須有以*粗體*表示其「目的」的連接埠。 不過，封鎖其他連接埠將會降低使用和監視執行中服務的能力。

+ **SSL 功能**︰若要啟用 SSL 憑證鏈結建立和驗證，API 管理服務需要 ocsp.msocsp.com、mscrl.microsoft.com 和 crl.microsoft.com 的輸出網路連線。 如果您上傳至 API 管理的任何憑證包含 CA 根的完整鏈結，則不需要此相依性。

+ **DNS 存取**：需要有連接埠 53 的輸出存取，才能與 DNS 伺服器通訊。 如果 VPN 閘道的另一端有自訂 DNS 伺服器存在，則必須可從裝載 API 管理的子網路連接該 DNS 伺服器。

+ **計量和健康情況監視**︰對 Azure 監視端點 (解析為屬於下列網域) 的輸出網路連線能力︰

    | Azure 環境 | 端點                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 公用      | <ul><li>gcs.prod.monitoring.core.windows.net （**新增**）</li><li>prod.warmpath.msftcloudes.com （**即將淘汰**）</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`.warm.ingestion.msftcloudes.com，其中 `East US 2` 是 eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **Smtp 轉送**： smtp 轉送的輸出網路連線能力，它會在主機 `smtpi-co1.msn.com`、`smtpi-ch1.msn.com`、`smtpi-db3.msn.com`、`smtpi-sin.msn.com` 和 `ies.global.microsoft.com` 底下解析

+ **開發人員入口網站 CAPTCHA**：開發人員入口網站的 CAPTCHA 的輸出網路連線能力，它會在 `client.hip.live.com` 和 `partner.hip.live.com`的主機底下解析。

+ **Azure 入口網站診斷**：從虛擬網路內部使用 API 管理延伸模組時，若要從 Azure 入口網站啟用診斷記錄的流程，則需要在連接埠 443 上有 `dc.services.visualstudio.com` 的輸出存取權。 這有助於針對您在使用延伸模組時所可能面臨的問題進行疑難排解。

+ **使用 Express Route 或網路虛擬裝置強制通道流量至內部內部部署防火牆**：常見的客戶設定是定義自己的預設路由（0.0.0.0/0），以強制所有來自 API 管理委派子網的流量都能流經內部部署防火牆或網路虛擬裝置。 此流量流程一定會中斷與 Azure API 管理的連線，因為已在內部部署封鎖輸出流量，或者 NAT 至無法再使用各種 Azure 端點的一組無法辨識位址。 解決方案會要求您執行幾項工作：

  * 在 API 管理服務部署所在的子網上啟用服務端點。 您必須為 Azure Sql、Azure 儲存體、Azure EventHub 和 Azure 服務匯流排啟用[服務端點][ServiceEndpoints]。 從 API 管理委派子網直接啟用端點至這些服務，可讓他們使用 Microsoft Azure 骨幹網路，以提供最佳的服務流量路由。 如果您使用具有強制通道 Api 管理的服務端點，上述 Azure 服務流量不會強制通道處理。 另一個 API 管理服務相依性流量會被強制通道處理，且不能遺失，或 API 管理服務無法正常運作。
    
  * 從網際網路到 API 管理服務的管理端點的所有控制平面流量，都是透過 API 管理所裝載的一組特定輸入 Ip 來路由傳送。 當流量強制通道時，回應將不會對稱地對應回這些輸入來源 Ip。 若要克服此限制，我們必須新增下列使用者定義的路由（[udr][UDRs]），藉由將這些主機路由的目的地設定為「網際網路」，將流量引導回到 Azure。 控制平面流量的一組輸入 ip 是記載的[控制平面 IP 位址](#control-plane-ips)

  * 對於其他以強制通道處理的 API 管理服務相依性，應該有方法可以解析主機名稱，並連到端點。 其中包括
      - 計量和健康情況監視
      - Azure 入口網站診斷
      - SMTP 轉送
      - 開發人員入口網站 CAPTCHA

## <a name="troubleshooting"></a>疑難排解
* **初始安裝**：若未能成功地將 API 管理服務初始部署到子網路，建議您先將虛擬機器部署到相同的子網路。 接下來，再將桌面遠端連線到虛擬機器，並驗證您可以連線到 Azure 訂用帳戶中的下列其中一個資源
    * Azure 儲存體 Blob
    * Azure SQL Database
    * Azure 儲存體資料表

  > [!IMPORTANT]
  > 驗證過連線能力後，請務必先移除子網路中部署的所有資源，再將 API 管理部署至子網路。

* 累加**式更新**：對您的網路進行變更時，請參閱[NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus)，以確認 API 管理服務並未遺失任何重要資源的存取權。 連線狀態應該每隔 15 分鐘更新一次。

* **資源導覽連結**：在部署到 Resource Manager 樣式 VNet 子網路時，API 管理會藉由建立資源導覽連結來保留子網路。 如果子網路已包含來自不同提供者的資源，部署將會**失敗**。 同樣地，當您將 API 管理服務移至不同子網路或將它刪除時，我們也會移除該資源導覽連結。

## <a name="subnet-size"></a>子網大小需求
Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基礎結構使用的 IP 位址之外，子網路中的每個 API 管理執行個體都會為進階 SKU 的每個單位使用兩個 IP 位址，或為開發人員 SKU 使用一個 IP 位址。 每個執行個體都會保留一個額外 IP 位址作為外部負載平衡器。 當您部署到內部 vnet 時，內部負載平衡器需要其他的 IP 位址。

假設計算超過子網的大小下限，其中可部署 API 管理為/29，這會提供三個可用的 IP 位址。

## <a name="routing"></a>路由
+ 負載平衡的公用 IP 位址 (VIP) 會保留下來，以供存取所有服務端點。
+ 子網路 IP 範圍的 IP (DIP) 位址會用來存取 VNET 中的資源，而公用 IP 位址 (VIP) 會用來存取 VNET 之外的資源。
+ 您可以在 Azure 入口網站的 [概觀/基本資訊] 刀鋒視窗上找到負載平衡的公用 IP 位址。

## <a name="limitations"></a>限制
* 包含「API 管理」執行個體的子網路無法包含任何其他 Azure 資源類型。
* 子網路和「API 管理」服務必須位於同一個訂用帳戶中。
* 包含「API 管理」執行個體的子網路無法跨訂用帳戶移動。
* 針對設定為內部虛擬網路模式的多區域 API 管理部署，使用者需負責管理跨多個區域的負載平衡，因為他們擁有路由。
* 由於平台限制，無法從另一個區域中全域對等互連 VNET 中的資源連線到內部模式中的 API 管理服務。 如需詳細資訊，請參閱[一個虛擬網路中的資源無法與對等互連虛擬網路中的 Azure 內部負載平衡器通訊](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。

## <a name="control-plane-ips"></a>控制平面 IP 位址

IP 位址會被**Azure 環境**分割。 允許以**Global**標記的輸入要求的 ip 位址，必須與**區域**特定的 ip 位址一起列入白名單。

| **Azure 環境**|   **區域**|  **IP 位址**|
|-----------------|-------------------------|---------------|
| Azure 公用| 美國中南部（全球）| 104.214.19.224|
| Azure 公用| 美國中北部（全球）| 52.162.110.80|
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
| Azure 公用| 歐洲北部| 52.142.95.35|
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
| Azure China 21Vianet| 中國北部（全域）| 139.217.51.16|
| Azure China 21Vianet| 中國東部（全域）| 139.217.171.176|
| Azure China 21Vianet| 中國北部| 40.125.137.220|
| Azure China 21Vianet| 中國東部| 40.126.120.30|
| Azure China 21Vianet| 中國北部 2| 40.73.41.178|
| Azure China 21Vianet| 中國東部 2| 40.73.104.4|
| Azure Government| USGov 弗吉尼亞州（全球）| 52.127.42.160|
| Azure Government| USGov 德克薩斯州（全球）| 52.127.34.192|
| Azure Government| 美國政府維吉尼亞州| 52.227.222.92|
| Azure Government| 美國政府愛荷華州| 13.73.72.21|
| Azure Government| 美國政府亞利桑那州| 52.244.32.39|
| Azure Government| 美國政府德克薩斯州| 52.243.154.118|
| Azure Government| USDoD 中部| 52.182.32.132|
| Azure Government| USDoD 東部| 52.181.32.192|

## <a name="related-content"></a>相關內容
* [使用 VPN 閘道將虛擬網路連線到後端](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [從不同的部署模型連接虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫](api-management-howto-api-inspector.md)
* [虛擬網路常見問題](../virtual-network/virtual-networks-faq.md)
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
