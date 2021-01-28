---
title: Azure 服務標籤概觀
titlesuffix: Azure Virtual Network
description: 了解服務標籤。 服務標籤有助於將建立安全性規則的複雜度降到最低。
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 39645e8bd370dfd518d570c3088c56a22aa77748
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927041"
---
# <a name="virtual-network-service-tags"></a>虛擬網路服務標籤
<a name="network-service-tags"></a>

服務標籤代表來自指定 Azure 服務的一組 IP 位址前置詞。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤，而盡可能簡化網路安全性規則頻繁的更新。

您可以使用服務標記來定義 [網路安全性群組](./network-security-groups-overview.md#security-rules) 或 [Azure 防火牆](../firewall/service-tags.md)上的網路存取控制。 建立安全性規則時，請以服務標籤取代特定的 IP 位址。 藉由在規則的適當 *來源* 或 *目的地* 欄位中指定服務標籤名稱（例如 **ApiManagement**），您可以允許或拒絕對應服務的流量。

您可以使用服務標籤進行網路隔離，以及在存取具有公用端點的 Azure 服務時，防止一般網際網路存取您的 Azure 資源。 建立輸入/輸出網路安全性群組規則，可拒絕進出於 **網際網路** 的流量，並允許 **AzureCloud** 或特定 Azure 服務的其他 [可用服務標籤](#available-service-tags)的輸入/輸出流量。

![使用服務標籤來隔離 Azure 服務的網路](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>可用的服務標籤
下表包含可在[網路安全性群組](./network-security-groups-overview.md#security-rules)規則中使用的所有服務標籤。

資料行表示標籤是否：

- 適用於說明輸入或輸出流量的規則。
- 支援[區域](https://azure.microsoft.com/regions)範圍。
- 可在 [Azure 防火牆](../firewall/service-tags.md)規則中使用。

根據預設，服務標籤會反映整個雲端的範圍。 某些服務標籤也會將對應的 IP 範圍限定為指定的區域，藉以提供更精細的控制。 例如，服務標籤 **Storage** 代表整個雲端的 Azure 儲存體，而 **Storage.WestUS** 則會將範圍縮小為僅限來自美國西部區域的儲存體 IP 位址範圍。 下表指出各個服務標籤是否支援這類區域範圍。  

| Tag | 目的 | 可以使用輸入還是輸出？ | 是否可為區域性？ | 是否可與 Azure 防火牆搭配使用？ |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | 動作群組。 | 輸入 | 否 | 否 |
| **ApiManagement** | Azure API 管理專用部署的管理流量。 <br/><br/>*注意：* 此標籤代表各區域控制平面的 Azure API 管理服務端點。 這可讓客戶對設定於 API 管理服務上的 API、作業、原則、NamedValues 執行管理作業。  | 輸入 | 是 | 是 |
| **ApplicationInsightsAvailability** | Application Insights 可用性。 | 輸入 | 否 | 否 |
| **AppConfiguration** | 應用程式組態。 | 輸出 | 否 | 否 |
| **AppService**    | Azure App Service。 針對 web 應用程式和函式應用程式，建議將此標記用於輸出安全性規則。  | 輸出 | 是 | 是 |
| **AppServiceManagement** | App Service 環境專用部署的管理流量。 | 兩者 | 否 | 是 |
| **AzureActiveDirectory** | Azure Active Directory。 | 輸出 | 否 | 是 |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory Domain Services 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **AzureAdvancedThreatProtection** | Azure 進階威脅防護。 | 輸出 | 否 | 否 |
| **AzureArcInfrastructure** | Azure Arc 啟用的伺服器、Azure Arc 啟用的 Kubernetes 和來賓設定流量。<br/><br/>*注意：* 此標記相依于 **AzureActiveDirectory**、**AzureTrafficManager** 和 **AzureResourceManager** 標記。 *此標記目前無法透過 Azure 入口網站進行* 設定。| 輸出 | 否 | 是 |
| **AzureBackup** |Azure 備份。<br/><br/>*注意：* 此標籤相依於 **Storage** 和 **AzureActiveDirectory** 標籤。 | 輸出 | 否 | 是 |
| **AzureBotService** | Azure Bot Service。 | 輸出 | 否 | 否 |
| **AzureCloud** | 所有的[資料中心公用 IP 位址](https://www.microsoft.com/download/details.aspx?id=56519)。 | 輸出 | 是 | 是 |
| **AzureCognitiveSearch** | Azue 認知搜尋。 <br/><br/>此標籤或其涵蓋的 IP 位址可用來授與索引子對資料來源的安全存取。 如需詳細資訊，請參閱 [索引子連接檔](../search/search-indexer-troubleshooting.md#connection-errors) 。 <br/><br/> *注意*：搜尋服務的 IP 未包含在此服務標籤的 IP 範圍清單中，且 **還必須新增至** 資料來源的 IP 防火牆。 | 輸入 | 否 | 否 |
| **AzureConnectors** | 用於探查/後端連線的 Azure Logic Apps 連接器。 | 輸入 | 是 | 是 |
| **AzureContainerRegistry** | Azure Container Registry。 | 輸出 | 是 | 是 |
| **AzureCosmosDB** | Azure Cosmos DB。 | 輸出 | 是 | 是 |
| **AzureDatabricks** | Azure Databricks。 | 兩者 | 否 | 否 |
| **AzureDataExplorerManagement** | Azure 資料總管管理。 | 輸入 | 否 | 否 |
| **AzureDataLake** | Azure Data Lake Storage Gen1。 | 輸出 | 否 | 是 |
| **AzureDevSpaces** | Azure Dev Spaces。 | 輸出 | 否 | 否 |
| **AzureDevOps** | Azure Dev Ops。<br/><br/>*注意：此標記目前無法透過 Azure 入口網站設定*| 輸入 | 否 | 是 |
| **AzureDigitalTwins** | Azure 數位 Twins。<br/><br/>*注意：* 此標籤或此標記所涵蓋的 IP 位址，可用來限制對為事件路由設定之端點的存取權。 *此標記目前無法透過 Azure 入口網站設定* | 輸入 | 否 | 是 |
| **AzureEventGrid** | Azure 事件格線。 | 兩者 | 否 | 否 |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door。 | 兩者 | 否 | 否 |
| **AzureInformationProtection** | Azure 資訊保護。<br/><br/>*注意：* 此標籤相依於 **AzureActiveDirectory**、**AzureFrontDoor.Frontend** 和 **AzureFrontDoor.FirstParty** 標籤。 | 輸出 | 否 | 否 |
| **AzureIoTHub** | Azure IoT 中樞。 | 輸出 | 否 | 否 |
| **AzureKeyVault** | Azure Key Vault。<br/><br/>*注意：* 此標籤相依於 **AzureActiveDirectory** 標籤。 | 輸出 | 是 | 是 |
| **AzureLoadBalancer** | Azure 基礎結構負載平衡器。 此標籤會轉譯成作為 Azure 健康情況探查來源的[主機虛擬 IP 位址](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16)。 這只包括探查流量，而不是對您後端資源的實際流量。 如果您未使用 Azure Load Balancer，則可以覆寫此規則。 | 兩者 | 否 | 否 |
| **AzureMachineLearning** | Azure Machine Learning | 兩者 | 否 | 是 |
| **AzureMonitor** | Log Analytics、Application Insights、AzMon 和自訂計量 (GiG 端點)。<br/><br/>*注意：* 針對 Log Analytics，此標籤相依於 **Storage** 標籤。 | 輸出 | 否 | 是 |
| **AzureOpenDatasets** | Azure 開放資料集。<br/><br/>*注意：* 此標籤相依於 **AzureFrontDoor.Frontend** 和 **Storage** 標籤。 | 輸出 | 否 | 否 |
| **AzurePlatformDNS** | 基本基礎結構 (預設) DNS 服務。<br/><br>您可以使用此標籤來停用預設 DNS。 使用此標籤時請多加留意。 建議您閱讀 [Azure 平台的考量](./network-security-groups-overview.md#azure-platform-considerations)。 此外也建議您在使用此標籤前先執行測試。 | 輸出 | 否 | 否 |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS)，這是基本的基礎結構服務。<br/><br/>您可以使用此標籤來停用預設 IMDS。 使用此標籤時請多加留意。 建議您閱讀 [Azure 平台的考量](./network-security-groups-overview.md#azure-platform-considerations)。 此外也建議您在使用此標籤前先執行測試。 | 輸出 | 否 | 否 |
| **AzurePlatformLKM** | Windows 授權或金鑰管理服務。<br/><br/>您可以使用此標籤來停用授權的預設值。 使用此標籤時請多加留意。 建議您閱讀 [Azure 平台的考量](./network-security-groups-overview.md#azure-platform-considerations)。  此外也建議您在使用此標籤前先執行測試。 | 輸出 | 否 | 否 |
| **AzureResourceManager** | Azure Resource Manager。 | 輸出 | 否 | 否 |
| **AzureSignalR** | Azure SignalR。 | 輸出 | 否 | 否 |
| **AzureSiteRecovery** | Azure Site Recovery。<br/><br/>*注意：* 此標籤相依於 **AzureActiveDirectory**、**AzureKeyVault**、**EventHub**、**GuestAndHybridManagement** 和 **Storage** 標籤。 | 輸出 | 否 | 否 |
| **AzureTrafficManager** | Azure 流量管理員探查 IP 位址。<br/><br/>如需流量管理員探查 IP 位址的詳細資訊，請參閱 [Azure 流量管理員常見問題集](../traffic-manager/traffic-manager-faqs.md)。 | 輸入 | 否 | 是 |  
| **BatchNodeManagement** | Azure Batch 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **CognitiveServicesManagement** | Azure 認知服務流量的位址範圍。 | 兩者 | 否 | 否 |
| **DataFactory**  | Azure Data Factory | 兩者 | 否 | 否 |
| **DataFactoryManagement** | Azure Data Factory 的管理流量。 | 輸出 | 否 | 否 |
| **Dynamics365ForMarketingEmail** | Dynamics 365 行銷電子郵件服務的位址範圍。 | 輸出 | 是 | 否 |
| **EventHub** | Azure 事件中樞上的事件中樞功能比較。 | 輸出 | 是 | 是 |
| **GatewayManager** | Azure VPN 閘道和應用程式閘道專用部署的管理流量。 | 輸入 | 否 | 否 |
| **GuestAndHybridManagement** | Azure 自動化和來賓設定。 | 輸出 | 否 | 是 |
| **HDInsight** | Azure HDInsight。 | 輸入 | 是 | 否 |
| **網際網路** | 位於虛擬網路以外、可透過公用網際網路存取的 IP 位址空間。<br/><br/>此位址範圍也包括 [Azure 擁有的公用 IP 位址空間](https://www.microsoft.com/download/details.aspx?id=41653)。 | 兩者 | 否 | 否 |
| **LogicApps** | Logic Apps。 | 兩者 | 否 | 否 |
| **LogicAppsManagement** | Logic Apps 的管理流量。 | 輸入 | 否 | 否 |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security。 | 輸出 | 否 | 否 |
| **MicrosoftContainerRegistry** | Microsoft 容器映像的容器登錄。 <br/><br/>*注意：* 此標籤相依於 **AzureFrontDoor.FirstParty** 標籤。 | 輸出 | 是 | 是 |
| **PowerQueryOnline** | Power Query Online。 | 兩者 | 否 | 否 |
| **ServiceBus** | 使用進階服務層級的 Azure 服務匯流排流量。 | 輸出 | 是 | 是 |
| **ServiceFabric** | Azure Service Fabric。<br/><br/>*注意：* 此標籤代表各區域控制平面的 Service Fabric 服務端點。 此標籤可讓客戶從其 VNET 執行 Service Fabric 叢集的管理作業 (端點，例如 https:// westus.servicefabric.azure.com) | 兩者 | 否 | 否 |
| **Sql** | Azure SQL Database、適用於 MySQL 的 Azure 資料庫、適用於 PostgreSQL 的 Azure 資料庫和 Azure Synapse Analytics。<br/><br/>*注意：* 此標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 SQL Database 或伺服器服務，但不代表特定的 Azure SQL Database。 此標籤不適用於 SQL 受控執行個體。 | 輸出 | 是 | 是 |
| **SqlManagement** | SQL 專用部署的管理流量。 | 兩者 | 否 | 是 |
| **Storage** | Azure 儲存體。 <br/><br/>*注意：* 此標籤代表服務，但不代表服務的特定執行個體。 例如，標籤代表 Azure 儲存體服務，但不代表特定的 Azure 儲存體帳戶。 | 輸出 | 是 | 是 |
| **StorageSyncService** | 儲存體同步服務。 | 兩者 | 否 | 否 |
| **WindowsVirtualDesktop** | Windows 虛擬桌面。 | 兩者 | 否 | 是 |
| **VirtualNetwork** | 虛擬網路位址空間 (針對虛擬網路定義的所有 IP 位址範圍)、所有已連線的內部部署位址空間、[對等互連](virtual-network-peering-overview.md)的虛擬網路、已連線至[虛擬網路閘道](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)的虛擬網路、[主機的虛擬 IP 位址](./network-security-groups-overview.md#azure-platform-considerations)，以及在[使用者定義的路由](virtual-networks-udr-overview.md)上使用的位址前置詞。 此標籤可能也會包含預設路由。 | 兩者 | 否 | 否 |

>[!NOTE]
>在傳統部署模型中 (Azure Resource Manager 之前)，可支援上表所列標籤的子集。 這些標籤的拼寫方式不同：
>
>| 傳統拼字 | 對等 Resource Manager 標籤 |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| 網際網路 | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure 服務的服務標籤代表所使用之特定雲端中的位址前置詞。 例如，與 Azure 公用雲端上的 **Sql** 標籤值相對應的基礎 IP 範圍，會與 Azure 中國雲端上的基礎範圍不同。

> [!NOTE]
> 如果您對服務 (例如 Azure 儲存體或 Azure SQL Database) 實作[虛擬網路服務端點](virtual-network-service-endpoints-overview.md)，Azure 會將[路由](virtual-networks-udr-overview.md#optional-default-routes)新增至服務的虛擬網路子網路。 路由中的位址前置詞是與對應的服務標籤相同的位址前置詞或 CIDR 範圍。

## <a name="service-tags-on-premises"></a>內部部署服務標籤  
您可以取得目前的服務標籤和範圍資訊，並將其納入為內部部署防火牆設定的一部分。 這項資訊是與每個服務標籤相對應之 IP 範圍的目前時間點清單。 您可以透過程式設計方式或 JSON 檔案下載來取得資訊，如以下幾節所說明。

### <a name="use-the-service-tag-discovery-api-public-preview"></a>使用服務標籤探索 API (公開預覽)
您可以透過程式設計方式，將目前的服務標籤清單連同 IP 位址範圍詳細資料一起擷取：

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag?viewFallbackFrom=azps-2.3.2)
- [Azure CLI](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> 在公開預覽階段，探索 API 所傳回的資訊可能會比 JSON 下載傳回的資訊舊一些。 (請參閱下一節。)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>使用可下載的 JSON 檔案探索服務標籤 
您可以下載 JSON 檔案，其中包含目前的服務標籤清單以及 IP 位址範圍詳細資料。 這些清單會每週更新並發佈。 每個雲端的位置如下：

- [Azure 公用](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Gov](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

這些檔案中的 IP 位址範圍是以 CIDR 標記法表示。 

> [!NOTE]
>這項資訊的子集已發佈在 XML 檔案中，適用於 [Azure 公用](https://www.microsoft.com/download/details.aspx?id=41653)、[Azure 中國](https://www.microsoft.com/download/details.aspx?id=42064)和 [Azure 德國](https://www.microsoft.com/download/details.aspx?id=54770)。 這些 XML 下載將於 2020 年 6 月 30 日淘汰，且在該日期後即無法使用。 您應依照先前幾節的說明進行遷移，以使用探索 API 或 JSON 檔案下載。

### <a name="tips"></a>提示 
- 您可以記下 JSON 檔案中增長的 *changeNumber* 值，藉以偵測某個發行集的更新。 每個子區段 (例如 **Storage.WestUS**) 都會其本身的 *changeNumber*，會隨著變更而遞增。 當任何子區段變更時，檔案的 *changeNumber* 的最上層就會遞增。
- 如需如何剖析服務標籤資訊的範例 (例如，取得美國西部的儲存體所有的位址範圍)，請參閱[服務標籤探索 API PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag?viewFallbackFrom=azps-2.3.2) 文件。

## <a name="next-steps"></a>後續步驟
- 了解如何[建立網路安全性群組](tutorial-filter-network-traffic.md)。
