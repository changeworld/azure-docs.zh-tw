---
title: Azure 服務標記總覽
titlesuffix: Azure Virtual Network
description: 瞭解服務標記。 服務標籤有助於將安全性規則建立的複雜度降到最低。
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 21e35afc72aa9824baa1076abe0e767ce4935c28
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391105"
---
# <a name="virtual-network-service-tags"></a>虛擬網路服務標籤
<a name="network-service-tags"></a>

服務標記代表來自指定 Azure 服務的一組 IP 位址首碼。 Microsoft 會管理服務標籤所包含的位址前置詞，並隨著位址變更自動更新服務標籤，將經常更新網路安全性規則的複雜度降到最低。

您可以使用服務標籤，在 或 [Azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)的[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)上定義網路存取控制。 當您建立安全性規則時，請使用服務標記來取代特定的 IP 位址。 藉由在適當的*來源* 或規則的 [ *目的地* ] 欄位中指定服務標籤名稱（例如， **ApiManagement**），您可以允許或拒絕對應服務的流量。

您可以使用服務標籤來達到網路隔離，並在存取具有公用端點的 Azure 服務時，從一般網際網路保護您的 Azure 資源。 建立輸入/輸出網路安全性群組規則，以拒絕進出**網際網路**的流量，並允許進出**AzureCloud**的流量，或特定 Azure 服務的其他[可用服務](#available-service-tags)標籤。

## <a name="available-service-tags"></a>可用的服務標記
下表包含可以在[網路安全性群組](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)規則中使用的所有服務標籤。

資料行指出標記是否：

- 適用于涵蓋輸入或輸出流量的規則。
- 支援[區域](https://azure.microsoft.com/regions)範圍。
- 可在[Azure 防火牆](https://docs.microsoft.com/azure/firewall/service-tags)規則中使用。

根據預設，服務標記會反映整個雲端的範圍。 某些服務標籤也會藉由將對應的 IP 範圍限制為指定的區域，來提供更細微的控制。 例如，服務標籤**儲存體**代表整個雲端 Azure 儲存體，但**WestUS**會將範圍縮小為只有 WESTUS 區域的儲存體 IP 位址範圍。 下表指出每個服務標記是否支援這類區域範圍。  

| Tag | 目的 | 可以使用輸入或輸出嗎？ | 可以是地區嗎？ | 可以與 Azure 防火牆搭配使用嗎？ |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Azure API 管理-專用部署的管理流量。 <br/><br/>*注意：* 此標記代表每個區域之控制平面的 Azure API 管理服務端點。 這可讓客戶在 API 管理服務上設定的 Api、作業、原則、NamedValues 上執行管理作業。  | 輸入 | 是 | 是 |
| **ApplicationInsightsAvailability** | Application Insights 可用性。 | 輸入 | 否 | 否 |
| **AppService**    | Azure App Service。 對 web 應用程式前端的輸出安全性規則，建議使用此標記。 | 輸出 | 是 | 是 |
| **AppServiceManagement** | 專用於 App Service 環境之部署的管理流量。 | 兩者 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory。 | 輸出 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | 專用於 Azure Active Directory Domain Services 之部署的管理流量。 | 兩者 | 否 | 是 |
| **AzureAdvancedThreatProtection** | Azure 進階威脅防護。 | 輸出 | 否 | 否 |
| **AzureBackup** |Azure 備份。<br/><br/>*注意：* 此標記相依于**儲存體**和**AzureActiveDirectory**標記。 | 輸出 | 否 | 是 |
| **AzureBotService** | Azure Bot 服務。 | 輸出 | 否 | 否 |
| **AzureCloud** | 所有[資料中心公用 IP 位址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 輸出 | 是 | 是 |
| **AzureCognitiveSearch** | Azure 認知搜尋。 <br/><br/>這個標記或此標籤所涵蓋的 IP 位址可用來授與索引子安全存取資料來源。 如需詳細資訊，請參閱[索引子連接檔](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors)。 <br/><br/> *注意*：搜尋服務的 ip 不會包含在此服務標籤的 ip 範圍清單中，而且**也必須新增**至資料來源的 ip 防火牆。 | 輸入 | 否 | 否 |
| **AzureConnectors** | 適用于探查/後端連線的 Azure Logic Apps 連接器。 | 輸入 | 是 | 是 |
| **AzureContainerRegistry** | Azure Container Registry。 | 輸出 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos DB。 | 輸出 | 是 | 是 |
| **AzureDatabricks** | Azure Databricks。 | 兩者 | 否 | 否 |
| **AzureDataExplorerManagement** | Azure 資料總管管理。 | 輸入 | 否 | 否 |
| **AzureDataLake** | Azure Data Lake Storage Gen1。 | 輸出 | 否 | 是 |
| **AzureEventGrid** | Azure 事件格線。 <br/><br/>*注意：* 此標記涵蓋美國中南部、美國東部、美國東部2、美國西部2和美國中部的 Azure 事件方格端點。 | 兩者 | 否 | 否 |
| **AzureFrontDoor** | Azure Front 的大門。 | 兩者 | 否 | 否 |
| **AzureInformationProtection** | Azure 資訊保護。<br/><br/>*注意：* 此標記相依于**AzureActiveDirectory**和**AzureFrontDoor**標記。 也請將下列 Ip 加入白名單（即將移除此相依性）： 13.107.6.181 & 13.107.9.181。 | 輸出 | 否 | 否 |
| **AzureIoTHub** | Azure IoT 中樞。 | 輸出 | 否 | 否 |
| **AzureKeyVault** | Azure Key Vault。<br/><br/>*注意：* 此標記與**AzureActiveDirectory**標記具有相依性。 | 輸出 | 是 | 是 |
| **AzureLoadBalancer** | Azure 基礎結構負載平衡器。 此標籤會轉譯為 Azure 健康狀態探查所源自主機（168.63.129.16）的[虛擬 IP 位址](security-overview.md#azure-platform-considerations)。 這不會包含 Azure Load Balancer 資源的流量。 如果您不是使用 Azure Load Balancer，您可以覆寫此規則。 | 兩者 | 否 | 否 |
| **AzureMachineLearning** | Azure Machine Learning | 兩者 | 否 | 是 |
| **AzureMonitor** | Log Analytics、Application Insights、AzMon 和自訂計量（Gb 端點）。<br/><br/>*注意：* 針對 Log Analytics，此標記與**儲存體**標記相依。 | 輸出 | 否 | 是 |
| **AzurePlatformDNS** | 基本基礎結構（預設） DNS 服務。<br/><br>您可以使用此標記來停用預設 DNS。 當您使用此標記時，請務必小心。 我們建議您閱讀[Azure 平臺考慮](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我們也建議您先執行測試，再使用此標記。 | 輸出 | 否 | 否 |
| **AzurePlatformIMDS** | Azure Instance Metadata Service （IMDS），這是基本的基礎結構服務。<br/><br/>您可以使用此標記來停用預設 IMDS。 當您使用此標記時，請務必小心。 我們建議您閱讀[Azure 平臺考慮](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。 我們也建議您先執行測試，再使用此標記。 | 輸出 | 否 | 否 |
| **AzurePlatformLKM** | Windows 授權或金鑰管理服務。<br/><br/>您可以使用此標記來停用授權的預設值。 當您使用此標記時，請務必小心。 我們建議您閱讀[Azure 平臺考慮](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)。  我們也建議您先執行測試，再使用此標記。 | 輸出 | 否 | 否 |
| **AzureResourceManager** | Azure Resource Manager。 | 輸出 | 否 | 否 |
| **AzureSiteRecovery** | Azure Site Recovery。<br/><br/>*注意：* 此標記相依于**儲存體**、 **AzureActiveDirectory**和**EventHub**標記。 | 輸出 | 否 | 否 |
| **Azuretrafficmanager 僅限** | Azure 流量管理員探查 IP 位址。<br/><br/>如需流量管理員探查 IP 位址的詳細資訊，請參閱[Azure 流量管理員常見問題](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)。 | 輸入 | 否 | 是 |  
| **BatchNodeManagement** | 專用於 Azure Batch 之部署的管理流量。 | 兩者 | 否 | 是 |
| **CognitiveServicesManagement** | Azure 認知服務流量的位址範圍。 | 輸出 | 否 | 否 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 行銷電子郵件服務的位址範圍。 | 輸出 | 是 | 否 |
| **ElasticAFD** | 彈性 Azure Front。 | 兩者 | 否 | 否 |
| **EventHub** | Azure 事件中樞。 | 輸出 | 是 | 是 |
| **GatewayManager** | 適用于 Azure VPN 閘道和應用程式閘道專用之部署的管理流量。 | 輸入 | 否 | 否 |
| **GuestAndHybridManagement** | Azure 自動化和來賓設定。 | 輸出 | 否 | 是 |
| **HDInsight** | Azure HDInsight。 | 輸入 | 是 | 否 |
| **網際網路** | 位於虛擬網路外部且可由公用網際網路連線的 IP 位址空間。<br/><br/>位址範圍包含[Azure 擁有的公用 IP 位址空間](https://www.microsoft.com/download/details.aspx?id=41653)。 | 兩者 | 否 | 否 |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security。 | 輸出 | 否 | 否 |
| **MicrosoftContainerRegistry** | 適用于 Microsoft 容器映射的 container registry。 <br/><br/>*注意：* 也請將下列 IP 列入白名單（即將移除此相依性）：204.79.197.219。 | 輸出 | 是 | 是 |
| **ServiceBus** | Azure 服務匯流排使用 Premium 服務層級的流量。 | 輸出 | 是 | 是 |
| **ServiceFabric** | Azure Service Fabric。<br/><br/>*注意：* 此標記代表每個區域之控制平面的 Service Fabric 服務端點。 這可讓客戶從其 VNET 執行其 Service Fabric 叢集的管理作業（例如 HTTPs://westus.servicefabric.azure.com） | 兩者 | 否 | 否 |
| **Sql** | Azure SQL Database、適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和 Azure SQL 資料倉儲。<br/><br/>*注意：* 此標記代表服務，而不是服務的特定實例。 例如，標籤代表 SQL Database 或伺服器服務，但不代表特定的 Azure SQL Database。 此標記不適用於 SQL 受控實例。 | 輸出 | 是 | 是 |
| **SqlManagement** | 適用于 SQL 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **Storage** | Azure 儲存體。 <br/><br/>*注意：* 此標記代表服務，而不是服務的特定實例。 例如，標籤代表 Azure 儲存體服務，但不代表特定的 Azure 儲存體帳戶。 | 輸出 | 是 | 是 |
| **VirtualNetwork** | 虛擬網路位址空間（針對虛擬網路定義的所有 IP 位址範圍）、所有已連線的內部部署位址空間、[對等互連](virtual-network-peering-overview.md)虛擬網路、連接到[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虛擬網路、[主機的虛擬 IP 位址](security-overview.md#azure-platform-considerations)，以及[使用者定義的路由](virtual-networks-udr-overview.md)所使用的位址首碼。 此標記可能也會包含預設路由。 | 兩者 | 否 | 否 |

>[!NOTE]
>在傳統部署模型中（Azure Resource Manager 之前），會支援上表中所列標記的子集。 這些標記的拼寫方式不同：
>
>| 傳統拼寫 | 對等 Resource Manager 標記 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| 網際網路 | 網際網路 |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服務的服務標記代表所使用之特定雲端中的位址首碼。 例如，對應至 Azure 公用雲端上**Sql**標記值的基礎 IP 範圍，會與 azure 中國雲端上的基礎範圍不同。

> [!NOTE]
> 如果您對服務 (例如 Azure 儲存體或 Azure SQL Database) 實作[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)，Azure 會將[路由](virtual-networks-udr-overview.md#optional-default-routes)新增至服務的虛擬網路子網路。 路由中的位址前置詞與對應服務標籤的位址首碼相同，或 CIDR 範圍。

## <a name="service-tags-on-premises"></a>內部部署服務標記  
您可以取得目前的服務標籤和範圍資訊，以納入內部部署防火牆設定的一部分。 這項資訊是對應到每個服務標籤之 IP 範圍的目前時間點清單。 您可以透過程式設計方式或透過 JSON 檔案下載來取得資訊，如下列各節所述。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服務標記探索 API （公開預覽）
您可以透過程式設計方式，將目前的服務標記清單與 IP 位址範圍詳細資料一起抓取：

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> 當它處於公開預覽狀態時，探索 API 可能會傳回的資訊不如 JSON 下載所傳回的資訊。 (請參閱下節)。


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下載的 JSON 檔案探索服務標記 
您可以下載 JSON 檔案，其中包含目前的服務標籤清單，以及 IP 位址範圍詳細資料。 這些清單會每週更新併發布。 每個雲端的位置如下：

- [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>這項資訊的子集已發行到[Azure 公用](https://www.microsoft.com/download/details.aspx?id=41653)、 [Azure 中國](https://www.microsoft.com/download/details.aspx?id=42064)和[azure 德國](https://www.microsoft.com/download/details.aspx?id=54770)的 XML 檔案中。 這些 XML 下載將于2020年6月30日淘汰，而且在該日期之後將無法再使用。 您應該依照先前章節所述，使用探索 API 或 JSON 檔案下載進行遷移。

### <a name="tips"></a>提示 
- 您可以在 JSON 檔案中注明增加的*changeNumber*值，藉以偵測來自某個發行集的更新。 每個子區段（例如， **WestUS**）都有自己的*changeNumber* ，會隨著變更而遞增。 當任何小節變更時，檔案*changeNumber*的最上層會遞增。
- 如需如何剖析服務標籤資訊的範例（例如，在 WestUS 中取得儲存體的所有位址範圍），請參閱[服務標記探索 API PowerShell](https://aka.ms/discoveryapi_powershell)檔。

## <a name="next-steps"></a>後續步驟
- 瞭解如何[建立網路安全性群組](tutorial-filter-network-traffic.md)。
