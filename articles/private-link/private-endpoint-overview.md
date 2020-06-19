---
title: 什麼是 Azure 私人端點？
description: 了解 Azure 私人端點
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 51afa118be75c7e9ea2cb6e394d27cc39a58de0b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83849649"
---
# <a name="what-is-azure-private-endpoint"></a>什麼是 Azure 私人端點？

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 私人端點會使用您 VNet 中的私人 IP 位址，有效地將服務帶入您的 VNet 中。 服務可以是 Azure 服務，例如 Azure 儲存體、Azure Cosmos DB、SQL 等，或您自己的[私人連結服務](private-link-service-overview.md)。
  
## <a name="private-endpoint-properties"></a>私人端點屬性 
 私人端點指定了下列屬性： 


|屬性  |描述 |
|---------|---------|
|名稱    |    資源群組內的唯一名稱。      |
|子網路    |  要從虛擬網路部署和配置私人 IP 位址的子網路。 如需子網路需求，請參閱此文章的＜限制＞一節。         |
|Private Link 資源    |   從可用類型的清單中使用資源識別碼或別名來連線的私人連結資源。 系統將會為傳送至此資源的所有流量產生唯一的網路識別碼。       |
|目標子資源   |      要連線的子資源。 每個私人連結資源類型都有不同的選項，可根據喜好設定來選取。    |
|連線核准方法    |  自動或手動。 根據角色型存取控制 (RBAC) 權限，可以自動核准您的私人端點。 如果您嘗試連線到不具 RBAC 的私人連結資源，請使用手動方法，讓資源的擁有者核准連線。        |
|要求訊息     |  您可以指定要以手動方式核准要求連線的訊息。 此訊息可用來識別特定的要求。        |
|連線狀態   |   唯讀屬性，可指定私人端點是否作用中。 只有處於核准狀態的私人端點可用來傳送流量。 其他可用的狀態： <br>-**已核准**：已自動或手動核准連線並可供使用。</br><br>-**暫止**：已手動建立連線，且正在等待私人連結資源擁有者核准。</br><br>-**已拒絕**：私人連結資源擁有者已拒絕連線。</br><br>-**已中斷連線**：私人連結資源擁有者已移除連線。 私人端點將提供資訊，應將其刪除以進行清除。 </br>|

以下是私人端點的一些重要詳細資料： 
- 私人端點可讓相同 VNet、區域內對等互連 VNet、全域對等互連 VNet 和內部部署之間的取用者，使用 [VPN](https://azure.microsoft.com/services/vpn-gateway/) 或 [Express Route](https://azure.microsoft.com/services/expressroute/) 和以 Private Link 提供技術支援的服務之間進行連線。
 
- 網路連線只能由連線到私人端點的用戶端起始，服務提供者沒有任何路由設定可向服務取用者起始連線。 只能以單一方向建立連線。

- 建立私人端點時，也會建立資源生命週期的唯讀網路介面。 介面會從對應至私人連結資源的子網路中，指派動態私人 IP 位址。 私人端點的整個生命週期中，私人 IP 位址的值會保持不變。
 
- 私人端點必須部署在與虛擬網路相同的區域中。 
 
- 私人連結資源可以部署在與虛擬網路和私人端點不同的區域中。
 
- 您可以使用相同的私人連結資源來建立多個私人端點。 針對使用一般 DNS 伺服器設定的單一網路，建議的作法是針對指定的私人連結資源使用單一私人端點，以避免 DNS 解析中出現重複的項目或衝突。 
 
- 可以在相同虛擬網路內的相同或不同的子網路上建立多個私人端點。 您可以在訂用帳戶中建立的私人端點數目有所限制。 如需詳細資訊，請參閱  [Azure 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) \(部分機器翻譯\)。


 
## <a name="private-link-resource"></a>Private Link 資源 
私人連結資源是指定私人端點的目的地目標。 以下是可用的私人連結資源類型清單： 
 
|私人連結資源名稱  |資源類型   |子資源  |
|---------|---------|---------|
|**Private Link 服務** (您的服務)   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure 儲存體**  | Microsoft.Storage/storageAccounts    |  Blob (blob、blob_secondary)<BR> 資料表 (table、table_secondary)<BR> 佇列 (queue、queue_secondary)<BR> 檔案 (file、file_secondary)<BR> Web (web、web_secondary)        |
|**Azure Data Lake Storage Gen2** \(部分機器翻譯\)  | Microsoft.Storage/storageAccounts    |  Blob (blob、blob_secondary)<BR> 資料湖檔案系統 Gen2 (dfs、dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql、MongoDB、Cassandra、Gremlin、資料表|
|**適用於 PostgreSQL 的 Azure 資料庫 - 單一伺服器** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**適用於 MySQL 的 Azure 資料庫** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**適用於 MariaDB 的 Azure 資料庫** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure IoT 中心** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure 金鑰保存庫** | Microsoft.KeyVault/vaults    | 保存庫 |
|**Azure Kubernetes Service - Kubernetes API** | Microsoft.ContainerService/managedClusters    | managedCluster |
|**Azure 搜尋服務** | Microsoft.Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registries    | 登錄 |
|**Azure 應用程式組態** | Microsoft.Appconfiguration/configurationStores    | configurationStore |
|**Azure 備份** | Microsoft.RecoveryServices/vaults    | 保存庫 |
|**Azure 事件中樞** | Microsoft.EventHub/namespaces    | 命名空間 |
|**Azure 服務匯流排** | Microsoft.ServiceBus/namespaces | 命名空間 |
|**Azure 轉送** | Microsoft.Relay/namespaces | 命名空間 |
|**事件格線** | Microsoft.EventGrid/topics    | 主題 |
|**事件格線** | Microsoft.EventGrid/domains    | 網域 |
|**Azure WebApps** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | 工作區 |
  
 
## <a name="network-security-of-private-endpoints"></a>私人端點的網路安全性 
當針對 Azure 服務使用私人端點時，會將流量保護到特定的私人連結資源。 該平台會執行存取控制，以驗證僅觸達指定私人連結資源的網路連線。 若要存取相同 Azure 服務中的其他資源，則需要額外的私人端點。 
 
您可以完全鎖定您的工作負載，使其無法存取公用端點，以連線到支援的 Azure 服務。 此控制措施透過提供內建的外流保護，來防止存取相同 Azure 服務上裝載的其他資源，為您的資源提供額外的網路安全性層級。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用核准工作流程存取私人連結資源 
您可以使用下列連線核准方法來連線到私人連結資源：
- **自動**核准，當您擁有或擁有特定私人連結資源的權限時。 所需的權限基於下列格式的私人連結資源類型：Microsoft.\<Provider>/<resource_type>/privateEndpointConnectionApproval/action
- **手動**要求，當您沒有必要的權限，而且想要要求存取權時。 已起始核准工作流程。 私人端點和後續的私人端點連線將會以「擱置」狀態來建立。 私人連結資源的擁有者會負責核准連線。 獲得核准後，私人端點便能正常傳送流量，如下列核准工作流程圖所示。  

![工作流程核准](media/private-endpoint-overview/private-link-paas-workflow.png)
 
私人連結資源擁有者可以透過私人端點連線執行下列動作： 
- 檢閱所有私人端點連線的詳細資料。 
- 核准私人端點連線。 對應的私人端點就能傳送流量至私人連結資源。 
- 拒絕私人端點連線。 對應的私人端點會更新以反映狀態。
- 刪除處於任何狀態的私人端點連線。 對應的私人端點將會更新為 [中斷連線] 狀態以反映動作，私人端點擁有者只能在此時刪除資源。 
 
> [!NOTE]
> 只有處於已核准狀態的私人端點可以將流量傳送到指定的私人連結資源。 

### <a name="connecting-using-alias"></a>使用別名連線
別名是當服務擁有者在標準負載平衡器後方建立私人連結服務時，所產生的唯一 Moniker。 服務擁有者可以離線與取用者共用此別名。 取用者可以使用資源 URI 或別名來要求私人連結服務的連線。 如果您想要使用別名進行連線，您必須使用手動連線核准方法來建立私人端點。 若要使用手動連線核准方法，請在私人端點建立流程期間，將手動要求參數設定為 true。 如需詳細資訊，請參閱 [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) 和 [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)。 

## <a name="dns-configuration"></a>DNS 組態 
使用完整網域名稱 (FQDN) 作為連接字串的一部分來連線到私人連結資源時，請務必正確地設定您的 DNS 設定，以解析為配置的私人 IP 位址。 現有的 Azure 服務在透過公用端點連線時，可能已經有 DNS 設定可供使用。 使用您的私人端點進行連線時，需要覆寫此設定。 
 
與私人端點相關聯的網路介面包含設定 DNS 時所需的一組完整資訊，包括為指定的私人連結資源配置的 FQDN 和私人 IP 位址。 

如需有關為私人端點設定 DNS 的最佳做法和建議的完整詳細資訊，請參閱[私人端點 DNS 設定一文](private-endpoint-dns.md)。



 
## <a name="limitations"></a>限制
 
下表包含使用私人端點時的已知限制清單： 


|限制 |描述 |降低  |
|---------|---------|---------|
|網路安全性群組 (NSG) 規則和使用者定義的路由不適用於私人端點    |私人端點不支援 NSG。 雖然包含私人端點的子網路可以有相關聯的 NSG，但這些規則對私人端點所處理的流量無效。 您必須[停用網路原則強制](disable-private-endpoint-network-policy.md)，才能在子網路中部署私人端點。 在相同子網路上裝載的其他工作負載上，仍會強制執行 NSG。 任何用戶端子網路上的路由都會使用 /32 前置詞，變更預設路由行為需要類似的 UDR  | 針對來源用戶端上的連出流量使用 NSG 規則，以控制流量。 部署具有 /32 前置詞的個別路由，以覆寫私人端點路由。 連出連線的 NSG 流程記錄和監視資訊仍然受支援且可供使用        |


## <a name="next-steps"></a>後續步驟
- [使用入口網站建立適用於 SQL Database 伺服器的私人端點](create-private-endpoint-portal.md)
- [使用 PowerShell 建立適用於 SQL Database 伺服器的私人端點](create-private-endpoint-powershell.md)
- [使用 CLI 建立適用於 SQL Database 伺服器的私人端點](create-private-endpoint-cli.md)
- [使用入口網站建立適用於儲存體帳戶的私人端點](create-private-endpoint-storage-portal.md)
- [使用入口網站建立適用於 Azure Cosmos 帳戶的私人端點](../cosmos-db/how-to-configure-private-endpoints.md)
- [使用 Azure PowerShell 建立您自己的 Private Link 服務](create-private-link-service-powershell.md)
- [針對適用於 PostgreSQL 的 Azure 資料庫建立您自己的 Private Link - 使用入口網站的單一伺服器](../postgresql/howto-configure-privatelink-portal.md)
- [針對適用於 PostgreSQL 的 Azure 資料庫建立您自己的 Private Link - 使用 CLI 的單一伺服器](../postgresql/howto-configure-privatelink-cli.md)
- [使用入口網站針對適用於 MySQL 的 Azure 資料庫建立您自己的 Private Link](../mysql/howto-configure-privatelink-portal.md)
- [使用 CLI 針對適用於 MySQL 的 Azure 資料庫建立您自己的 Private Link](../mysql/howto-configure-privatelink-cli.md)
- [使用入口網站針對適用於 MariaDB 的 Azure 資料庫建立您自己的 Private Link](../mariadb/howto-configure-privatelink-portal.md)
- [使用 CLI 針對適用於 MariaDB 的 Azure 資料庫建立您自己的 Private Link](../mariadb/howto-configure-privatelink-cli.md)
