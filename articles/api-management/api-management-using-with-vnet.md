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
ms.date: 12/10/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: e36f7c6085908630d5e7aa2593fe4d57202d6ee7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107646"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>如何將 Azure API 管理與虛擬網路搭配使用
「Azure 虛擬網路」(VNET) 可讓您將任何 Azure 資源，放在您控制存取權的非網際網路可路由網路中。 然後，可以使用各種 VPN 技術，將這些網路連線到您的內部部署網路。 若要深入了解「Azure 虛擬網路」，請從以下資訊著手：[Azure 虛擬網路概觀](../virtual-network/virtual-networks-overview.md)。

Azure API 管理可以部署在虛擬網路 (VNET) 內，因此它可以存取網路內的後端服務。 開發人員入口網站與 API 閘道，可設定為從網際網路存取或只從虛擬網路內存取。

> [!NOTE]
> API 匯入文件 URL 必須裝載在可公開存取的網際網路位址上。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>必要條件

若要執行本文所述的步驟，您必須具有：

+ 有效的 Azure 訂用帳戶。

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 執行個體。 如需詳細資訊，請參閱[建立 Azure API 管理執行個體](get-started-create-service-instance.md)。

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>啟用 VNET 連線

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>使用 Azure 入口網站啟用 VNET 連線能力

1. 移至 [Azure 入口網站](https://portal.azure.com)，以尋找您的 API 管理執行個體。 搜尋並選取 [API 管理服務]。

2. 選擇您的 API 管理執行個體。

3. 選取 [虛擬網路]。
4. 將 API 管理執行個體設定為在虛擬網路內部署。

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="在 Azure 入口網站中選取 [虛擬網路]。":::
    
5. 選取所需的存取類型：

    * **Off**：這是預設值。 API 管理不會部署到虛擬網路中。

    * **外部**：可透過外部負載平衡器，從公用網際網路存取 API 管理閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

        ![公用對等互連][api-management-vnet-public]

    * **內部**：僅能透過內部負載平衡器，從虛擬網路內存取 API 管理閘道和開發人員入口網站。 閘道可以存取虛擬網路內的資源。

        ![私人對等互連][api-management-vnet-private]

6. 如果您選取 [外部] 或 [內部]，您會看見佈建 API 管理服務所在的所有區域清單。 選擇 [位置]，然後挑選其 [虛擬網路] 和 [子網路]。 虛擬網路清單中會同時填入已在您要設定之區域中設定的 Azure 訂用帳戶中可用的傳統和 Resource Manager 虛擬網路。

    > [!IMPORTANT]
    > 將 Azure API 管理執行個體部署至 Resource Manager VNET 時，服務必須在除了 Azure API 管理執行個體之外不包含其他資源的專用子網路中。 如果嘗試將 Azure API 管理執行個體部署到含有其他資源的 Resource Manager VNET 子網路，則部署將會失敗。

    然後，選取 [套用]。 您的 API 管理執行個體的 [虛擬網路] 頁面會更新為新的虛擬網路和子網路選項。

    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="入口網站中的虛擬網路設定。":::

7. 在頂端導覽列中，選取 [儲存]，然後選取 [套用網路設定]。

> [!NOTE]
> 「API 管理」執行個體的 VIP 位址在每次啟用或停用 VNET 時都會變更。
> 當 API 管理從 [外部] 移到 [內部] 時，VIP 位址也會變更，反之亦然。
>

> [!IMPORTANT]
> 如果您將 API 管理從 VNET 中移除或變更其部署所在的 VNET，則先前使用的 VNET 將保持鎖定狀態最長達六個小時。 在這段期間，將無法刪除該 VNET 或在其中部署新的資源。 此行為適用於使用 api-version 2018-01-01 和更早版本的用戶端。 使用 api-version 2019-01-01 和更新版本的用戶端，會在相關聯的 API 管理服務刪除時立即釋放 VNET。

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"></a>將 API 管理部署到外部 VNET

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **在 VNET 內建立 APIM 服務**：使用 [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) Cmdlet 在 VNET 內建立 Azure API 管理服務。

* **在 VNET 內部署現有 APIM 服務**：使用 [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) Cmdlet 移動虛擬網路內的現有 Azure API 管理服務。

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>連線至裝載於虛擬網路內的 Web 服務
在您的「API 管理」服務連接到 VNET 之後，存取 VNET 內的後端服務與存取公用服務便沒有差別。 只要在建立新 API 或編輯現有 API 時，於 [Web 服務 URL] 欄位中輸入您 Web 服務的本機 IP 位址或主機名稱 (如果為 VNET 設定了 DNS 伺服器) 即可。

![透過 VPN 加入 API][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>常見的網路設定問題
以下是將 API 管理服務部署到虛擬網路時可能發生的常見錯誤設定問題清單。

* **自訂 DNS 伺服器設定**：APIM 服務相依於數個 Azure 服務。 當「API 管理」是裝載於具有自訂 DNS 伺服器的 VNET 中時，它必須要解析這些 Azure 服務的主機名稱。 請遵循 [這份](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 有關自訂 DNS 設定的指引。 請參閱下方的連接埠資料表和參考的其他網路需求。

> [!IMPORTANT]
> 如果您打算針對 VNET 使用「自訂 DNS 伺服器」，在將 API 管理服務部署到該伺服器 **之前**，應該先將該伺服器設定妥當。 否則，每次變更 DNS 伺服器時，您都必須執行[套用網路設定作業](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)來更新 API 管理服務

* **APIM 所需的連接埠**︰使用 [網路安全性群組][Network Security Group]可以控制針對 API 管理部署所在之子網路的輸入與輸出流量。 如果這些連接埠中有任何一個無法使用，「API 管理」可能就無法正常運作而可能變成無法存取。 搭配 VNET 使用 API 管理時，封鎖這其中一或多個連接埠是另一個常見的錯誤組態問題。

<a name="required-ports"> </a> 當 API 管理服務執行個體裝載於 VNET 時，會使用下表中的連接埠。

| 來源 / 目的地連接埠 | 方向          | 傳輸通訊協定 |   [服務標記](../virtual-network/network-security-groups-overview.md#service-tags) <br> 來源 / 目的地   | 目的 (\*)                                                 | 虛擬網路類型 |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80]、443                  | 輸入            | TCP                | INTERNET / VIRTUAL_NETWORK            | 與 API 管理的用戶端通訊                      | 外部             |
| * / 3443                     | 輸入            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Azure 入口網站和 PowerShell 的管理端點         | 外部和內部  |
| * / 443                  | 輸出           | TCP                | VIRTUAL_NETWORK / Storage             | **與 Azure 儲存體的相依性**                             | 外部和內部  |
| * / 443                  | 輸出           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) 和 Azure KeyVault 相依性                  | 外部和內部  |
| * / 1433                     | 輸出           | TCP                | VIRTUAL_NETWORK / SQL                 | **存取 Azure SQL 端點**                           | 外部和內部  |
| */433                     | 輸出           | TCP                | VIRTUAL_NETWORK/AzureKeyVault                 | **存取 Azure KeyVault**                           | 外部和內部  |
| * / 5671、5672、443          | 輸出           | TCP                | VIRTUAL_NETWORK / EventHub            | [記錄到事件中樞原則](api-management-howto-log-event-hubs.md)和監視代理程式的相依性 | 外部和內部  |
| * / 445                      | 輸出           | TCP                | VIRTUAL_NETWORK / Storage             | 適用於 [GIT](api-management-configuration-repository-git.md) 之 Azure 檔案共用的相依性                      | 外部和內部  |
| */443、12000                     | 輸出           | TCP                | VIRTUAL_NETWORK / AzureCloud            | 健康情況與監視延伸模組         | 外部和內部  |
| */1886、443                     | 輸出           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | 發佈 [診斷記錄和計量](api-management-howto-use-azure-monitor.md)、 [資源健康狀態](../service-health/resource-health-overview.md) 和 [Application Insights](api-management-howto-app-insights.md)                   | 外部和內部  |
| */25、587、25028                       | 輸出           | TCP                | VIRTUAL_NETWORK / INTERNET            | 連線到 SMTP 轉送以便傳送電子郵件                    | 外部和內部  |
| * / 6381 - 6383              | 輸入和輸出 | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | 在電腦之間存取快 [取原則的](api-management-caching-policies.md) Redis 服務         | 外部和內部  |
| */4290              | 輸入和輸出 | UDP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | 電腦之間的 [速率限制](api-management-access-restriction-policies.md#LimitCallRateByKey) 原則的同步計數器         | 外部和內部  |
| * / \*                        | 輸入            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Azure 基礎結構負載平衡器                          | 外部和內部  |

>[!IMPORTANT]
> 要成功部署 API 管理服務，就必須有以 **粗體** 表示其「目的」的連接埠。 但封鎖其他埠會導致使用和監視執行中服務的能力 **降低** **，並提供已認可的 SLA**。

+ **TLS 功能**：若要啟用 TLS/SSL 憑證鏈結建置和驗證，API 管理服務需要 ocsp.msocsp.com、mscrl.microsoft.com 和 crl.microsoft.com 的輸出網路連線能力。 如果您上傳至 API 管理的任何憑證包含 CA 根的完整鏈結，則不需要此相依性。

+ **DNS 存取**：需要有連接埠 53 的輸出存取，才能與 DNS 伺服器通訊。 如果 VPN 閘道的另一端有自訂 DNS 伺服器存在，則必須可從裝載 API 管理的子網路連接該 DNS 伺服器。

+ **計量和健康情況監視**：對 Azure 監視端點的輸出網路連線能力，其會在下列網域下解析。 如下表所示，這些 URL 會以 AzureMonitor 服務標籤表示，以搭配「網路安全性群組」使用。

    | Azure 環境 | 端點                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure 公用      | <ul><li>gcs.prod.monitoring.core.windows.net(**新增**)</li><li>prod.warmpath.msftcloudes.com(**即將淘汰**)</li><li>global.prod.microsoftmetrics.com(**新增**)</li><li>global.metrics.nsatc.net(**即將淘汰**)</li><li>shoebox2.prod.microsoftmetrics.com(**新增**)</li><li>shoebox2.metrics.nsatc.net(**即將淘汰**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**新增**)</li><li>prod3.metrics.nsatc.net(**即將淘汰**)</li><li>prod3-black.prod.microsoftmetrics.com(**新增**)</li><li>prod3-black.prod3.metrics.nsatc.net(**即將淘汰**)</li><li>prod3-red.prod.microsoftmetrics.com(**新增**)</li><li>prod3-red.prod3.metrics.nsatc.net(**即將淘汰**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**新增**)</li><li>global.metrics.nsatc.net(**即將淘汰**)</li><li>shoebox2.prod.microsoftmetrics.com(**新增**)</li><li>shoebox2.metrics.nsatc.net(**即將淘汰**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**新增**)</li><li>prod3.metrics.nsatc.net(**即將淘汰**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**新增**)</li><li>global.metrics.nsatc.net(**即將淘汰**)</li><li>shoebox2.prod.microsoftmetrics.com(**新增**)</li><li>shoebox2.metrics.nsatc.net(**即將淘汰**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**新增**)</li><li>prod3.metrics.nsatc.net(**即將淘汰**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > 上述叢集的變更 (DNS 區域 **.nsatc.net** 變更為 **.microsoftmetrics.com**) 大多是 DNS 變更。 叢集的 IP 位址不會變更。

+ **區域服務標籤**：允許 Storage、SQL 和事件中樞服務標籤輸出連線的 NSG 規則，可以使用那些標籤 (與包含 API 管理執行個體的區域相對應) 的區域版本 (例如，適用於美國西部地區之 API 管理執行個體的 Storage.WestUS)。 在多區域部署中，每個區域中的 NSG 應該允許流量到達該區域和主要區域的服務標籤。

+ **SMTP 轉送**：SMTP 轉送的輸出網路連線能力，其會在主機 `smtpi-co1.msn.com`、`smtpi-ch1.msn.com`、`smtpi-db3.msn.com`、`smtpi-sin.msn.com` 和 `ies.global.microsoft.com` 下解析

+ **開發人員入口網站 CAPTCHA**：開發人員入口網站 CAPTCHA 的輸出網路連線能力，其會在主機 `client.hip.live.com` 和 `partner.hip.live.com` 下解析。

+ **Azure 入口網站診斷**：從虛擬網路內部使用 APIM 延伸模組時，若要從 Azure 入口網站啟用診斷記錄的流程，則需要在連接埠 443 上有 `dc.services.visualstudio.com` 的輸出存取權。 這有助於針對您在使用延伸模組時所可能面臨的問題進行疑難排解。

+ **Azure Load Balancer**：`Developer` SKU 不需要允許來自服務標籤 `AZURE_LOAD_BALANCER` 的輸入要求，因為我們只會在其後面部署一個計算單位。 但是在調整為較高的 SKU (例如 `Premium`) 時，來自 [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 的輸入會變得很重要，因為來自負載平衡器的健康情況探查失敗會導致部署失敗。

+ **Application Insights**：如果已在 API 管理上啟用 [Azure 應用程式 Insights](api-management-howto-app-insights.md) 監視，則需要允許從虛擬網路到 [遙測端點](../azure-monitor/app/ip-addresses.md#outgoing-ports) 的輸出連線。 

+ **使用 Express Route 或網路虛擬設備，以強制通道將流量傳送至內部部署防火牆**：常見的客戶設定是定義自己的預設路由 (0.0.0.0/0)，以強制所有來自 API 管理委派子網路的流量流經內部部署防火牆或網路虛擬設備。 此流量流程一定會中斷與 Azure API 管理的連線，因為已在內部部署封鎖輸出流量，或者 NAT 至無法再使用各種 Azure 端點的一組無法辨識位址。 解決方案會要求您執行幾項工作：

  * 在 API 管理服務部署所在的子網路上啟用服務端點。 需要為 Azure SQL、Azure 儲存體、Azure 事件中樞和 Azure 服務匯流排啟用[服務端點][ServiceEndpoints]。 從 API 管理委派子網路直接啟用端點至這些服務，可讓其使用 Microsoft Azure 骨幹網路，以提供最佳的服務流量路由。 如果您使用服務端點搭配強制通道 API 管理，上述 Azure 服務流量不會使用強制通道。 其他 API 管理服務相依性流量會使用強制通道且不能遺失，否則 API 管理服務會無法正常運作。
    
  * 從網際網路到 API 管理服務管理端點的所有控制平面流量，都是透過 API 管理所裝載的一組特定輸入 IP 來路由傳送。 當流量使用強制通道時，回應將不會對稱地對應回這些輸入來源 IP。 若要克服這項限制，我們必須新增下列使用者定義的路由 ([UDR][UDRs])，藉由將這些主機路由的目的地設定為「網際網路」，將流量引導回 Azure。 控制平面流量的一組輸入 IP 會記錄為[控制平面 IP 位址](#control-plane-ips)

  * 對於其他以強制通道處理的 API 管理服務相依性，應該有方法可以解析主機名稱並觸達端點。 其中包括
      - 計量和健康情況監視
      - Azure 入口網站診斷
      - SMTP 轉送
      - 開發人員入口網站 CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>疑難排解
* **初始設定**：若未能成功地將 APIM 服務初始部署到子網路，建議您先將虛擬機器部署到相同的子網路。 下一次從遠端桌面連線到虛擬機器，並驗證您的 Azure 訂用帳戶中的每個資源都有連線能力
    * Azure 儲存體 Blob
    * Azure SQL Database
    * Azure 儲存體資料表

  > [!IMPORTANT]
  > 驗證過連線能力後，請務必先移除子網路中部署的所有資源，再將 API 管理部署至子網路。

* **確認網路線上狀態**：將 API 管理部署至子網之後，請使用入口網站來檢查實例與相依性（例如 Azure 儲存體）的連線。 在入口網站的左側功能表中，選取 [ **部署和基礎結構**] 下的 [ **網路線上狀態**]。

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="在入口網站中確認網路線上狀態":::

    * 請選取 [ **必要** ]，以查看 API 管理所需之 Azure 服務的連線能力。 失敗表示實例無法執行核心作業來管理 Api。
    * 選取 [ **選擇性** ] 以檢查選用服務的連線。 任何失敗都只表示特定功能無法運作 (例如 SMTP) 。 失敗可能會導致使用和監視 API 管理實例的能力降低，並提供已認可的 SLA。

若要解決連線問題，請參閱 [常見的網路設定問題](#network-configuration-issues) ，並修正必要的網路設定。

* **累加式更新**：對您的網路進行變更時，請參閱 [NetworkStatus API](/rest/api/apimanagement/2019-12-01/networkstatus) \(部分機器翻譯\)，以確認 API 管理服務未遺失其所相依之任何關鍵資源的存取權。 連線狀態應該每隔 15 分鐘更新一次。

* **資源導覽連結**：在部署到 Resource Manager 樣式 VNet 子網路時，APIM 會藉由建立資源導覽連結來保留子網路。 如果子網路已包含來自不同提供者的資源，部署將會 **失敗**。 同樣地，當您將 API 管理服務移至不同子網路或將它刪除時，我們也會移除該資源導覽連結。

## <a name="subnet-size-requirement"></a><a name="subnet-size"> </a> 子網路大小需求
Azure 會在每個子網路中保留一些 IP 位址，但這些位址無法使用。 子網路的第一個和最後一個 IP 位址會保留給相容的通訊協定，以及用於 Azure 服務的額外 3 個位址。 如需詳細資訊，請參閱 [在這些子網路內使用 IP 位址是否有任何限制？](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

除了 Azure VNET 基礎結構使用的 IP 位址之外，子網路中的每個 API 管理執行個體都會為進階 SKU 的每個單位使用兩個 IP 位址，或為開發人員 SKU 使用一個 IP 位址。 每個執行個體都會保留一個額外 IP 位址作為外部負載平衡器。 當您部署到內部虛擬網路時，內部負載平衡器需要其他的 IP 位址。

基於上述的計算方式，子網路中可部署 API 管理的大小下限為 /29，這能提供三個可用的 IP 位址。

API 管理的每個額外縮放單位，都需要兩個額外的 IP 位址。

## <a name="routing"></a><a name="routing"> </a> 路由
+ 負載平衡的公用 IP 位址 (VIP) 會保留下來，以供存取所有服務端點。
+ 子網路 IP 範圍的 IP (DIP) 位址會用來存取 VNET 中的資源，而公用 IP 位址 (VIP) 會用來存取 VNET 之外的資源。
+ 您可以在 Azure 入口網站的 [概觀/基本資訊] 刀鋒視窗上找到負載平衡的公用 IP 位址。

## <a name="limitations"></a><a name="limitations"> </a>限制
* 包含「API 管理」執行個體的子網路無法包含任何其他 Azure 資源類型。
* 子網路和「API 管理」服務必須位於同一個訂用帳戶中。
* 包含「API 管理」執行個體的子網路無法跨訂用帳戶移動。
* 針對設定為內部虛擬網路模式的多區域 API 管理部署，使用者需負責管理跨多個區域的負載平衡，因為他們擁有路由。
* 由於平台限制，無法從另一個區域中全域對等互連 VNET 中的資源連線到內部模式中的 API 管理服務。 如需詳細資訊，請參閱[一個虛擬網路中的資源無法與對等互連虛擬網路中的 Azure 內部負載平衡器通訊](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)。

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"> </a> 控制平面 IP 位址

IP 位址是以 **Azure 環境** 分割。 當允許輸入要求以 **Global** 標記的 ip 位址時，必須與 **區域** 特定的 ip 位址一起使用。

| **Azure 環境**|   **區域**|  **IP 位址**|
|-----------------|-------------------------|---------------|
| Azure 公用| 美國中南部 (全域)| 104.214.19.224|
| Azure 公用| 美國中北部 (全域)| 52.162.110.80|
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
| Azure 公用| 巴西東南部| 191.232.18.181|
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
| Azure China 21Vianet| 中國北部 (全域)| 139.217.51.16|
| Azure China 21Vianet| 中國東部 (全域)| 139.217.171.176|
| Azure China 21Vianet| 中國北部| 40.125.137.220|
| Azure China 21Vianet| 中國東部| 40.126.120.30|
| Azure China 21Vianet| 中國北部 2| 40.73.41.178|
| Azure China 21Vianet| 中國東部 2| 40.73.104.4|
| Azure Government| US Gov 維吉尼亞州 (全域)| 52.127.42.160|
| Azure Government| US Gov 德克薩斯州 (全域)| 52.127.34.192|
| Azure Government| 美國政府維吉尼亞州| 52.227.222.92|
| Azure Government| 美國政府愛荷華州| 13.73.72.21|
| Azure Government| 美國政府亞利桑那州| 52.244.32.39|
| Azure Government| 美國政府德克薩斯州| 52.243.154.118|
| Azure Government| US DoD 中部| 52.182.32.132|
| Azure Government| US DoD 東部| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>相關內容
* [使用 VPN 閘道將虛擬網路連線到後端](../vpn-gateway/design.md#s2smulti)
* [從不同的部署模型連接虛擬網路](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [如何在 Azure API 管理中使用 API 偵測器來追蹤呼叫](api-management-howto-api-inspector.md)
* [虛擬網路常見問題集](../virtual-network/virtual-networks-faq.md)
* [服務標籤](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
