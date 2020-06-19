---
title: Azure 私人端點 DNS 設定
description: 了解 Azure 私人端點 DNS 設定
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7c8ff0808ada522dc24ef3c27156f4151832fbcd
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715931"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 私人端點 DNS 設定


使用完整網域名稱 (FQDN) 作為連接字串的一部分來連線到私人連結資源時，請務必正確地設定您的 DNS 設定，以解析為配置的私人 IP 位址。 現有的 Azure 服務在透過公用端點連線時，可能已經有 DNS 設定可供使用。 必須覆寫此設定，才能使用您的私人端點來連線。 
 
與私人端點相關聯的網路介面包含設定 DNS 時所需的一組完整資訊，包括為指定的私人連結資源配置的 FQDN 與私人 IP 位址。 
 
您可以使用下列選項來設定私人端點的 DNS 設定： 
- **使用主機檔案 (僅建議用於測試)** 。 您可以使用虛擬機器上的主機檔案來覆寫 DNS。  
- **使用私人 DNS 區域** 您可以使用[私人 DNS 區域](../dns/private-dns-privatednszone.md)來覆寫指定私人端點的 DNS 解析。 私人 DNS 區域可以連結至您的虛擬網路，以解析特定網域。
- **使用您的 DNS 轉寄站 (選用)** 。 您可以使用 DNS 轉寄站來覆寫指定私人連結資源的 DNS 解析。 如果您的 [DNS 伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)裝載在虛擬網路上，可以建立 DNS 轉送規則來使用私人 DNS 區域，以簡化所有私人連結資源的設定。
 
> [!IMPORTANT]
> 不建議您覆寫會主動用於解析公用端點的區域。 若未將 DNS 轉送至公用 DNS，將無法正確解析資源的連線。 為避免發生問題，請建立不同的網域名稱，或遵循下列每個服務的建議名稱。 

## <a name="azure-services-dns-zone-configuration"></a>Azure 服務 DNS 區域設定
Azure 服務將會在公用 DNS 上建立正式名稱 DNS 記錄 (CNAME)，以將解析重新導向至建議的私人網域名稱。 您可以使用私人端點的私人 IP 位址來覆寫解析。 
 
您的應用程式不需要變更連線 URL。 嘗試使用公用 DNS 解析時，DNS 伺服器現在將會解析為您的私人端點。 此程序不會影響您現有的應用程式。 

針對 Azure 服務，請使用下表中所述的建議區域名稱：

| 私人連結資源類型 / 子資源 |私人 DNS 區域名稱 | 公用 DNS 區域轉寄站 |
|---|---|---|---|
| SQL DB (Microsoft.Sql/servers) / Sql Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft.Sql/servers) / Sql Server  | privatelink.database.windows.net | database.windows.net |
| 儲存體帳戶 (Microsoft.Storage/storageAccounts) / Blob (blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| 儲存體帳戶 (Microsoft.Storage/storageAccounts) / Table (table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| 儲存體帳戶 (Microsoft.Storage/storageAccounts) / 佇列 (queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| 儲存體帳戶 (Microsoft.Storage/storageAccounts) / 檔案e (file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| 儲存體帳戶 (Microsoft.Storage/storageAccounts) / Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake 檔案系統 Gen2 (Microsoft.Storage/storageAccounts) / Data Lake 檔案系統 Gen2 (dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| 適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器 (Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| 適用於 MySQL 的 Azure 資料庫 (Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| 適用於 MariaDB 的 Azure 資料庫 (Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft.KeyVault/vaults) / 保存庫 | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service - Kubernetes API (Microsoft.ContainerService/managedClusters) / managedCluster | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure 搜尋服務 (Microsoft.Search/searchServices) / searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft.ContainerRegistry/registries) / 登錄 | privatelink.azurecr.io | azurecr.io |
| Azure 應用程式組態 (Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure 備份 (Microsoft.RecoveryServices/vaults) / 保存庫 | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure 事件中樞 (Microsoft.EventHub/namespaces) / 命名空間 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 服務匯流排 (Microsoft.ServiceBus/namespaces) / 命名空間 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 轉送 (Microsoft.Relay/namespaces) / 命名空間 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure 事件方格 (Microsoft.EventGrid/topics) / 主題 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure 事件方格 (Microsoft.EventGrid/domains) / 網域 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure WebApps (Microsoft.Web/sites) / site | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning(Microsoft.MachineLearningServices/workspaces) / 工作區 | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>DNS 設定案例

服務的 FQDN 會自動解析為公用 IP 位址，因此若要解析為私人端點的私人 IP 位址，您必須據以變更您的 DNS 設定。

DNS 是一個重要的元件，可透過以正確方式解析私人端點 IP 位址，讓應用程式正確運作。

根據您的喜好設定，下列案例適用於整合 DNS 解析：

- [沒有自訂 DNS 伺服器的虛擬網路工作負載](#virtual-network-workloads-without-custom-dns-server)
- [使用 DNS 轉寄站的內部部署工作負載](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>沒有自訂 DNS 伺服器的虛擬網路工作負載

此設定適用於沒有自訂 DNS 伺服器的虛擬網路工作負載。 在此案例中，用戶端會查詢私人端點 IP 位址到 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)。 Azure DNS 將負責私人 DNS 區域的 DNS 解析。


> [!NOTE]
> 此案例是使用 Azure SQL 資料庫建議的私人 DNS 區域。 針對其他服務，您可以使用下列參考 [Azure 服務 DNS 區域設定](#azure-services-dns-zone-configuration)來調整模型。

您需要下列資源才能正確設定：

- 用戶端虛擬網路

- 私人 DNS 區域 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) (包含[類型 A 記錄](../dns/dns-zones-records.md#record-types))

- 私人端點資訊 (FQDN 記錄名稱與私人 IP 位址)

下圖說明使用私人 DNS 區域之虛擬網路工作負載的 DNS 解析順序

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="單一虛擬網路與 Azure 提供的 DNS":::

此模型可以延伸至與相同私人端點相關聯的多個對等互連虛擬網路。 [新增新的虛擬網路連結](../dns/private-dns-virtual-network-links.md)到所有對等互連虛擬網路的私人 DNS 區域，即可完成此作業。

> [!IMPORTANT]
> 此設定需要單一私人 DNS 區域，為不同的虛擬網路建立多個名稱相同的區域時，需要手動操作以合併 DNS 記錄

在此案例中，有一個[中樞與輪輻](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)網路拓撲，其中包含共用一般私人端點的輪輻網路，而所有輪輻虛擬網路都是連結到相同的私人 DNS 區域。 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="具有 Azure 提供之 DNS 的中樞與輪輻":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>使用 DNS 轉寄站的內部部署工作負載

若要讓內部部署工作負載能夠將私人端點的 FQDN 解析為私人 IP 位址，您必須使用 DNS 轉寄站來解析部署在 Azure 中的 Azure 服務[公用 DNS 區域](#azure-services-dns-zone-configuration)。


下列案例適用於在 Azure 中具有 DNS 轉寄站的內部部署網路，而這會負責透過伺服器層級轉寄站，將所有 DNS 查詢解析為 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> 此案例是使用 Azure SQL 資料庫建議的私人 DNS 區域。 針對其他服務，您可以使用下列參考  [Azure 服務 DNS 區域設定](#azure-services-dns-zone-configuration)來調整模型。

您需要下列資源才能正確設定：

- 內部部署網路
-  [連線到內部部署的](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)虛擬網路
- 部署在 Azure 中的 DNS 轉寄站 
- 私人 DNS 區域  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  (包含 [類型 A 記錄](../dns/dns-zones-records.md#record-types))
- 私人端點資訊 (FQDN 記錄名稱與私人 IP 位址)

下圖說明使用部署在 Azure 中之 DNS 轉寄站的內部部署網路的 DNS 解析順序，其中的解析是由[連結到虛擬網路](../dns/private-dns-virtual-network-links.md)的私人 DNS 區域所執行的。

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="使用 Azure DNS 的內部部署":::

此設定可針對已有 DNS 解決方案的內部部署網路進行擴充。 
內部部署 DNS 解決方案需設定為透過參考部署在 Azure 中之 DNS 轉寄站的 [條件式轉寄站](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)，將 DNS 流量轉送至 Azure DNS。

> [!NOTE]
> 此案例是使用 Azure SQL 資料庫建議的私人 DNS 區域。 針對其他服務，您可以使用下列參考  [Azure 服務 DNS 區域設定](#azure-services-dns-zone-configuration)來調整模型。

您需要下列資源才能正確設定：

- 具備自訂 DNS 解決方案的內部部署網路 
-  [連線到內部部署的](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)虛擬網路
- 部署在 Azure 中的 DNS 轉寄站
- 私人 DNS 區域  [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  ，(包含  [type A  Record](../dns/dns-zones-records.md#record-types))
- 私人端點資訊 (FQDN 記錄名稱與私人 IP 位址)

下圖說明有條件地將 DNS 流量轉送至 Azure 的內部部署網路的 DNS 解析順序，其中的解析是由 [連結到虛擬網路](../dns/private-dns-virtual-network-links.md)的私人 DNS 區域所執行的。

> [!IMPORTANT]
> 條件式轉送必須對建議的[**公用 DNS 區域轉寄站**](#azure-services-dns-zone-configuration)  (例如： `database.windows.net` ) 轉送，而不是  **privatelink**.database.windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="內部部署轉送至 Azure DNS":::


## <a name="next-steps"></a>後續步驟
- [深入了解私人端點](private-endpoint-overview.md)
