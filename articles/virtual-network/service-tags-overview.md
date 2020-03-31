---
title: Azure 服務標記概述
titlesuffix: Azure Virtual Network
description: 瞭解服務標記。 服務標記有助於將安全規則創建的複雜性降至最低。
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 568fc880711d42941fd9aef2ea19b8ac3123793a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384240"
---
# <a name="virtual-network-service-tags"></a>虛擬網路服務標記
<a name="network-service-tags"></a>

服務標記表示給定 Azure 服務的一組 IP 位址首碼。 Microsoft 管理服務標記包含的位址首碼，並在位址更改時自動更新服務標記，從而最大限度地減少頻繁更新網路安全規則的複雜性。

可以使用服務標記在[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) 或 [Azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)上定義網路訪問控制項。 創建安全規則時，使用服務標記代替特定的 IP 位址。 通過在規則的相應*源* 或 *目標* 欄位中指定服務標記名稱（例如**ApiManagement），** 可以允許或拒絕相應服務的流量。

在訪問具有公共終結點的 Azure 服務時，可以使用服務標記實現網路隔離並保護 Azure 資源免受常規 Internet 的監視。 創建入站/出站網路安全性群組規則，以拒絕進出**Internet**的流量，並允許流量進出**AzureCloud**或特定 Azure[服務的其他可用服務標記](#available-service-tags)。

## <a name="available-service-tags"></a>可用的服務標記
下表包括可用於[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)規則的所有服務標記。

列指示標記是否：

- 適用于涵蓋入站或出站流量的規則。
- 支援[區域](https://azure.microsoft.com/regions)範圍。
- 在[Azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)規則中可用。

預設情況下，服務標記反映整個雲的範圍。 某些服務標記還允許通過將相應的 IP 範圍限制為指定區域來進行更精細的控制。 例如，服務標記**存儲**表示整個雲的 Azure 存儲，但**存儲.WestUS**將範圍縮小到僅存儲 IP 位址範圍（來自 WestUS 區域）。 下表指示每個服務標記是否支援此類區域作用域。  

| Tag | 目的 | 可以使用入站或出站嗎？ | 可以是區域性的嗎？ | 可與 Azure 防火牆一起使用？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **行動組** | 操作組。 | 輸入 | 否 | 否 |
| **ApiManagement** | Azure API 管理專用部署的管理流量。 <br/><br/>*注：* 此標記表示每個區域控制平面的 Azure API 管理服務終結點。 這使客戶能夠對 API、操作、策略、在 API 管理服務上配置的命名值執行管理操作。  | 輸入 | 是 | 是 |
| **應用程式洞察可用性** | 應用程式見解可用性。 | 輸入 | 否 | 否 |
| **應用配置** | 應用配置。 | 輸出 | 否 | 否 |
| **應用服務**    | Azure App Service。 此標記建議用於 Web 應用前端的出站安全規則。 | 輸出 | 是 | 是 |
| **AppServiceManagement** | 專用於應用服務環境的部署的管理流量。 | 兩者 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory。 | 輸出 | 否 | 是 |
| **Azure ActiveDirectory 域服務** | 專用於 Azure 活動目錄域服務的部署的管理流量。 | 兩者 | 否 | 是 |
| **Azure 高級威脅保護** | Azure 高級威脅防護。 | 輸出 | 否 | 否 |
| **Azure 備份** |Azure 備份。<br/><br/>*注：* 此標記依賴于**存儲**和**Azure ActiveDirectory**標記。 | 輸出 | 否 | 是 |
| **AzureBot服務** | 建立數位代理程式 | 輸出 | 否 | 否 |
| **AzureCloud** | 所有[資料中心的公共 IP 位址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 輸出 | 是 | 是 |
| **Azure 認知搜索** | Azure 認知搜索。 <br/><br/>此標記或此標記涵蓋的 IP 位址可用於授予索引子安全訪問資料來源的許可權。 有關詳細資訊，請參閱[索引子連接文檔](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors)。 <br/><br/> *注意*：搜索服務的 IP 不包括在此服務標記的 IP 範圍清單中，**還需要添加到**資料來源的 IP 防火牆中。 | 輸入 | 否 | 否 |
| **Azure 連接器** | 用於探測/後端連接的 Azure 邏輯應用連接器。 | 輸入 | 是 | 是 |
| **Azure 容器註冊** | Azure 容器註冊表。 | 輸出 | 是 | 是 |
| **Azure 宇宙資料庫** | Azure 宇宙資料庫。 | 輸出 | 是 | 是 |
| **Azure 資料塊** | Azure 資料塊。 | 兩者 | 否 | 否 |
| **Azure 資料資源管理器管理** | Azure 資料資源管理器管理。 | 輸入 | 否 | 否 |
| **Azure 資料湖** | Azure 資料存儲第 1 代。 | 輸出 | 否 | 是 |
| **Azure 空間** | Azure 開發空間。 | 輸出 | 否 | 否 |
| **Azure 事件網格** | Azure 事件格線。 <br/><br/>*注：* 此標記僅涵蓋美國中南部、美國東部、美國東部 2、美國西部 2 和美國中部中的 Azure 事件網格終結點。 | 兩者 | 否 | 否 |
| **Azure 前門.前端** <br/> **Azure 前門.後端** <br/> **Azure 前門.第一方**  | Azure 前門。 | 兩者 | 否 | 否 |
| **AzureInformationProtection** | Azure 資訊保護。<br/><br/>*注：* 此標記依賴于**Azure ActiveDirectory、Azure** **FrontDoor.前端**和**Azure FrontDoor.第一方**標記。 | 輸出 | 否 | 否 |
| **AzureIoTHub** | Azure IoT 中心。 | 輸出 | 否 | 否 |
| **Azure 金鑰庫** | Azure Key Vault。<br/><br/>*注：* 此標記依賴于**Azure ActiveDirectory**標記。 | 輸出 | 是 | 是 |
| **Azure 負載平衡器** | Azure 基礎結構負載等化器。 該標記轉換為 Azure 運行狀況探測器源自的[主機的虛擬 IP 位址](security-overview.md#azure-platform-considerations)（168.63.129.16）。 這不包括 Azure 負載等化器資源的流量。 如果不使用 Azure 負載等化器，則可以重寫此規則。 | 兩者 | 否 | 否 |
| **Azure 機器學習** | Azure Machine Learning | 兩者 | 否 | 是 |
| **AzureMonitor** | 日誌分析、應用程式見解、AzMon 和自訂指標（GiG 終結點）。<br/><br/>*注：* 對於日誌分析，此標記依賴于**存儲**標記。 | 輸出 | 否 | 是 |
| **Azure 開放資料集** | Azure 打開資料集。<br/><br/>*注：* 此標記依賴于**Azure FrontDoor.前端**和**存儲**標記。 | 輸出 | 否 | 否 |
| **Azure 平臺DNS** | 基本基礎結構（預設）DNS 服務。<br/><br>您可以使用此標記禁用預設 DNS。 使用此標記時要小心。 我們建議您閱讀 Azure[平臺注意事項](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我們還建議您在使用此標記之前執行測試。 | 輸出 | 否 | 否 |
| **Azure 平臺** | Azure 實例中繼資料服務 （IMDS），這是一項基本基礎結構服務。<br/><br/>您可以使用此標記禁用預設的 IMDS。 使用此標記時要小心。 我們建議您閱讀 Azure[平臺注意事項](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我們還建議您在使用此標記之前執行測試。 | 輸出 | 否 | 否 |
| **Azure 平臺** | Windows 許可或金鑰管理服務。<br/><br/>您可以使用此標記禁用許可的預設值。 使用此標記時要小心。 我們建議您閱讀 Azure[平臺注意事項](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。  我們還建議您在使用此標記之前執行測試。 | 輸出 | 否 | 否 |
| **Azure 資源管理器** | Azure 資源管理器。 | 輸出 | 否 | 否 |
| **AzureSignalR** | Azure 信號R。 | 輸出 | 否 | 否 |
| **Azure 網站恢復** | Azure 網站恢復。<br/><br/>*注：* 此標記依賴于**AzureActiveDirectory、AzureKeyVault、****事件Hub、****AzureKeyVault****來賓和混合管理和****存儲**標記。 | 輸出 | 否 | 否 |
| **AzureTrafficManager** | Azure 流量管理器探測 IP 位址。<br/><br/>有關流量管理器探測 IP 位址的詳細資訊，請參閱[Azure 流量管理器常見問題解答](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 輸入 | 否 | 是 |  
| **批次處理節點管理** | 專用於 Azure 批次處理的部署的管理流量。 | 兩者 | 否 | 是 |
| **認知服務管理** | Azure 認知服務流量的位址範圍。 | 輸出 | 否 | 否 |
| **資料工廠**  | Azure Data Factory | 兩者 | 否 | 否 |
| **資料工廠管理** | Azure 資料工廠的管理流量。 | 輸出 | 否 | 否 |
| **Dynamics365 用於行銷電子郵件** | Dynamics 365 行銷電子郵件服務的位址範圍。 | 輸出 | 是 | 否 |
| **彈性AFD** | 彈性 Azure 前門。 | 兩者 | 否 | 否 |
| **事件中心** | Azure 事件中心。 | 輸出 | 是 | 是 |
| **閘道管理器** | 專用於 Azure VPN 閘道和應用程式閘道的部署的管理流量。 | 輸入 | 否 | 否 |
| **賓客和混合管理** | Azure 自動化和來賓配置。 | 輸出 | 否 | 是 |
| **HDInsight** | Azure HDInsight。 | 輸入 | 是 | 否 |
| **網際網路** | 虛擬網路外部且可由公共互聯網覆蓋的 IP 位址空間。<br/><br/>位址範圍包括[Azure 擁有的公共 IP 位址空間](https://www.microsoft.com/download/details.aspx?id=41653)。 | 兩者 | 否 | 否 |
| **LogicApps** | 邏輯應用。 | 兩者 | 否 | 否 |
| **邏輯應用管理** | 邏輯應用的管理流量。 | 輸入 | 否 | 否 |
| **微軟雲應用安全** | Microsoft Cloud App Security。 | 輸出 | 否 | 否 |
| **微軟集裝箱註冊** | 用於 Microsoft 容器映射的容器註冊表。 <br/><br/>*注：* 此標記依賴于**Azure FrontDoor.第一方**標記。 | 輸出 | 是 | 是 |
| **電源查詢線上** | 連線電源查詢。 | 兩者 | 否 | 否 |
| **服務匯流排** | 使用高級服務層的 Azure 服務匯流排流量。 | 輸出 | 是 | 是 |
| **ServiceFabric** | Azure 服務結構。<br/><br/>*注：* 此標記表示每個區域控制平面的服務結構服務終結點。 這使客戶能夠對其服務結構群集執行其 VNET（終結點，例如） 的管理操作。 HTTPs://westus.servicefabric.azure.com） | 兩者 | 否 | 否 |
| **Sql** | Azure SQL 資料庫、MySQL 的 Azure 資料庫、PostgreSQL 的 Azure 資料庫和 Azure SQL 資料倉儲。<br/><br/>*注：* 此標記表示服務，但不表示服務的特定實例。 例如，標籤代表 SQL Database 或伺服器服務，但不代表特定的 Azure SQL Database。 此標記不適用於 SQL 託管實例。 | 輸出 | 是 | 是 |
| **Sql管理** | 用於 SQL 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **儲存空間** | Azure 儲存體。 <br/><br/>*注：* 此標記表示服務，但不表示服務的特定實例。 例如，標籤代表 Azure 儲存體服務，但不代表特定的 Azure 儲存體帳戶。 | 輸出 | 是 | 是 |
| **存儲同步服務** | 存儲同步服務。 | 兩者 | 否 | 否 |
| **視窗虛擬桌面** | Windows 虛擬桌面。 | 兩者 | 否 | 否 |
| **VirtualNetwork** | 虛擬網路位址空間（為虛擬網路定義的所有 IP 位址範圍）、所有連接的本地位址空間、[對等](virtual-network-peering-overview.md)虛擬網路、連接到[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虛擬網路、[主機的虛擬 IP 位址](security-overview.md#azure-platform-considerations)以及[使用者定義的路由](virtual-networks-udr-overview.md)上使用的位址首碼。 此標記可能還包含預設路由。 | 兩者 | 否 | 否 |

>[!NOTE]
>在經典部署模型（在 Azure 資源管理器之前），支援上表中列出的標記的子集。 這些標記的拼寫不同：
>
>| 經典拼寫 | 等效資源管理器標記 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| 網際網路 | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服務的服務標記表示正在使用的特定雲的位址首碼。 例如，與 Azure 公共雲上的**Sql**標記值對應的基礎 IP 範圍將與 Azure 中國雲的基礎範圍不同。

> [!NOTE]
> 如果您對服務 (例如 Azure 儲存體或 Azure SQL Database) 實作[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)，Azure 會將[路由](virtual-networks-udr-overview.md#optional-default-routes)新增至服務的虛擬網路子網路。 路由中的位址首碼與相應服務標記的位址首碼相同，或 CIDR 範圍相同。

## <a name="service-tags-on-premises"></a>本機服務標記  
您可以獲取當前服務標記和範圍資訊，以作為本地防火牆配置的一部分進行包含。 此資訊是對應于每個服務標記的 IP 範圍的目前時間點清單。 您可以以程式設計方式或通過 JSON 檔下載獲取資訊，如以下各節所述。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服務標籤發現 API（公共預覽）
您可以以程式設計方式檢索當前服務標記清單以及 IP 位址範圍詳細資訊：

- [休息](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure 電源外殼](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 當它處於公共預覽版時，發現 API 可能會返回比 JSON 下載返回的資訊更短的當前資訊。 (請參閱下節)。


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下載的 JSON 檔發現服務標記 
您可以下載包含當前服務標記清單的 JSON 檔以及 IP 位址範圍詳細資訊。 這些清單每週更新和發佈一次。 每個雲的位置如下：

- [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure 美國政府](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure 中國](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>此資訊的子集已發佈在[Azure 公共](https://www.microsoft.com/download/details.aspx?id=41653)[、Azure 中國和](https://www.microsoft.com/download/details.aspx?id=42064)Azure[德國的](https://www.microsoft.com/download/details.aspx?id=54770)XML 檔中。 這些 XML 下載將在 2020 年 6 月 30 日前棄用，在此日期之後將不再可用。 您應該遷移到使用發現 API 或 JSON 檔下載，如前幾節所述。

### <a name="tips"></a>提示 
- 您可以通過注意到 JSON 檔中增加*的更改數*值來檢測從一個發佈到下一個發佈的更新。 每個子節（例如 **，Storage.WestUS）** 都有自己的*更改編號*，隨著更改發生而遞增。 更改任何子節時，檔的*更改編號*的頂層將遞增。
- 有關如何分析服務標記資訊的示例（例如，獲取 WestUS 中存儲的所有位址範圍），請參閱[服務標記發現 API PowerShell](https://aka.ms/discoveryapi_powershell)文檔。

## <a name="next-steps"></a>後續步驟
- 瞭解如何[創建網路安全性群組](tutorial-filter-network-traffic.md)。
