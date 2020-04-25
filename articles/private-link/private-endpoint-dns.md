---
title: Azure 私人端點 DNS 設定
description: 瞭解 Azure 私人端點 DNS 設定
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 46ecf1bf2f5bd7900677cd115f8571ec3551a874
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134269"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 私人端點 DNS 設定


使用完整功能變數名稱（FQDN）連接到私人連結資源做為連接字串的一部分時，請務必正確地設定您的 DNS 設定，以解析為配置的私人 IP 位址。 現有的 Azure 服務在透過公用端點連線時，可能已經有 DNS 設定可供使用。 這需要覆寫，才能使用您的私用端點進行連接。 
 
與私人端點相關聯的網路介面包含設定 DNS 所需的一組完整資訊，包括為指定的私人連結資源配置的 FQDN 和私人 IP 位址。 
 
您可以使用下列選項來設定私人端點的 DNS 設定： 
- **使用主機檔案（僅建議用於測試）**。 您可以使用虛擬機器上的主機檔案來覆寫 DNS。  
- **使用私人 DNS 區域**。 您可以使用[私人 DNS 區域](../dns/private-dns-privatednszone.md)來覆寫指定私用端點的 DNS 解析。 私人 DNS 區域可以連結至您的虛擬網路，以解析特定網域。
- **使用您的自訂 DNS 伺服器**。 您可以使用自己的 DNS 伺服器來覆寫指定私人連結資源的 DNS 解析。 如果您的[dns 伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)裝載于虛擬網路上，您可以建立 dns 轉送規則來使用私人 DNS 區域，以簡化所有私人連結資源的設定。
 
> [!IMPORTANT]
> 不建議您覆寫主動使用的區域來解析公用端點。 若未將 DNS 轉送至公用 DNS，則無法正確解析資源的連線。 為避免發生問題，請建立不同的功能變數名稱，或遵循下列每項服務的建議名稱。 

## <a name="azure-services-dns-zone-configuration"></a>Azure 服務 DNS 區域設定
Azure 服務會在公用 DNS 上建立正式名稱 DNS 記錄（CNAME），以將解析重新導向至建議的私用功能變數名稱。 您將能夠使用私人端點的私人 IP 位址來覆寫解析。 
 
您的應用程式不需要變更連接 URL。 嘗試使用公用 DNS 進行解析時，DNS 伺服器現在會解析為您的私用端點。 此程式不會影響您現有的應用程式。 

針對 Azure 服務，請使用下表中所述的建議區功能變數名稱稱：

|私人連結資源類型   |子資源  |區功能變數名稱稱  |
|---------|---------|---------|
|SQL DB （Microsoft .Sql/伺服器）    |  Sql Server （sqlServer）        |   privatelink.database.windows.net       |
|Azure Synapse 分析（Microsoft .Sql/伺服器）    |  Sql Server （sqlServer）        | privatelink.database.windows.net |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）    |  Blob （blob、blob_secondary）        |    privatelink.blob.core.windows.net      |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）    |    資料表（資料表、table_secondary）      |   privatelink.table.core.windows.net       |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）    |    佇列（佇列、queue_secondary）     |   privatelink.queue.core.windows.net       |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）   |    File （file，file_secondary）      |    privatelink.file.core.windows.net      |
|儲存體帳戶（Microsoft 儲存體/storageAccounts）     |  Web （web、web_secondary）        |    privatelink.web.core.windows.net      |
|Data Lake 檔案系統 Gen2 （Microsoft. Storage/storageAccounts）  |  Data Lake 檔案系統 Gen2 （dfs，dfs_secondary）        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB （AzureCosmosDB/databaseAccounts）|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB （AzureCosmosDB/databaseAccounts）|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB （AzureCosmosDB/databaseAccounts）|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB （AzureCosmosDB/databaseAccounts）|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB （AzureCosmosDB/databaseAccounts）|Table|privatelink.table.cosmos.azure.com|
|適用於 PostgreSQL 的 Azure 資料庫-單一伺服器（DBforPostgreSQL/servers）|postgresqlServer|privatelink.postgres.database.azure.com|
|適用於 MySQL 的 Azure 資料庫（Microsoft.dbformysql/servers）|mysqlServer|privatelink.mysql.database.azure.com|
|適用於 MariaDB 的 Azure 資料庫（DBforMariaDB/servers）|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault （KeyVault/保存庫）|保存庫|privatelink.vaultcore.azure.net|
|Azure Kubernetes Service Kubernetes API （Microsoft.containerservice/managedClusters）    | managedCluster | {guid}. privatelink。<region>. azmk8s.io|
|Azure 搜尋服務（Microsoft 搜尋/searchServices）|searchService|privatelink.search.windows.net|   
|Azure Container Registry （ContainerRegistry/登錄） | 登錄 | privatelink.azurecr.io |
|Azure 應用程式組態（Appconfiguration/configurationStores）| configurationStore | privatelink.azconfig.io|
|Azure 備份（Azurerm.recoveryservices/保存庫）| 保存庫 |privatelink.{region}. windowsazure.storage .com|
|Azure 事件中樞（Microsoft EventHub/命名空間）| namespace |privatelink.servicebus.windows.net|
|Azure 服務匯流排（Microsoft. 匯流排/命名空間） | namespace |privatelink.servicebus.windows.net|
|Azure 轉送（Microsoft 轉送/命名空間） | namespace |privatelink.servicebus.windows.net|
|Azure 事件方格（Microsoft EventGrid/主題）     | 主題 | 本文.{region}. privatelink. eventgrid. azure .net|
|Azure 事件方格（EventGrid/網域） | 網域 | domain.{region}. privatelink. eventgrid. azure .net |
|Azure WebApps （Microsoft Web/sites）    | site | privatelink.azurewebsites.net |
|Azure Machine Learning （Microsoft.machinelearningservices/工作區）    | 工作區 | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>DNS 設定案例

服務的 FQDN 會解析公用 ip 位址，您必須變更 DNS 設定，以解析私人端點的私人 IP 位址。

DNS 是一項重要的元件，可讓應用程式正確地以私用端點 IP 位址的方式解析，藉此順利運作。

根據您的喜好設定，下列案例適用于整合 DNS 解析：

- [不含自訂 DNS 伺服器的虛擬網路工作負載](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>不含自訂 DNS 伺服器的虛擬網路工作負載

此設定適用于不含自訂 DNS 伺服器的虛擬網路工作負載。 在此案例中，用戶端會查詢私人端點 IP 位址至 Azure 提供的 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)。 Azure DNS 將負責私人 DNS 區域的 DNS 解析。


 > [!NOTE]
> 此案例使用 Azure SQL database 建議的私人 DNS 區域。 針對其他服務，您可以使用下列參考[Azure 服務 DNS 區域](#azure-services-dns-zone-configuration)設定來調整模型。

若要正確設定，您需要下列資源：

- 用戶端虛擬網路

- 具有[類型 A 記錄](../dns/dns-zones-records.md#record-types)的私人 DNS 區域[privatelink.database.windows.net](../dns/private-dns-privatednszone.md)

- 私人端點資訊（FQDN 記錄名稱和私人 IP 位址）

下圖說明使用私人 DNS 區域之虛擬網路工作負載的 DNS 解析順序

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="單一虛擬網路和 azure 提供的 dns":::

此模型可以延伸至與相同私人端點相關聯的多個對等互連虛擬網路。 這可以藉由[將新的虛擬網路連結新增](../dns/private-dns-virtual-network-links.md)至所有對等互連虛擬網路的私人 DNS 區域來完成。

 > [!IMPORTANT]
>  此設定需要單一私人 DNS 區域，為不同的虛擬網路建立多個具有相同名稱的區域，需要手動操作以合併 DNS 記錄

在此案例中，有一個[中樞 & 輪輻](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)網路拓朴，其中包含共用一般私人端點的輪輻網路，而所有輪輻虛擬網路都連結到相同的私人 dns 區域。 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="具有 azure 提供 dns 的中樞和輪輻":::


## <a name="next-steps"></a>後續步驟
- [深入瞭解私人端點](private-endpoint-overview.md)
