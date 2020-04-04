---
title: 什麼是 Azure 專用終結點?
description: 瞭解 Azure 專用終結點
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 6782d745bfced576fe06019b0d41af86c8c63ed4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656263"
---
# <a name="what-is-azure-private-endpoint"></a>什麼是 Azure 專用終結點?

Azure 私人端點是一種網路介面，可讓您以私人且安全地方式連線至 Azure Private Link 所支援的服務。 專用終結點使用來自 VNet 的專用 IP 位址,有效地將服務引入 VNet。 該服務可以是 Azure 服務,如 Azure 儲存、Azure 宇宙資料庫、SQL 等,也可以是您自己的[專用連結服務](private-link-service-overview.md)。
  
## <a name="private-endpoint-properties"></a>專用終結點屬性 
 專用終結點指定以下屬性: 


|屬性  |描述 |
|---------|---------|
|名稱    |    資源組中的唯一名稱。      |
|子網路    |  從虛擬網路部署和分配專用 IP 位址的子網路。 有關子網要求,請參閱本文中的"限制"部分。         |
|專用連結資源    |   使用資源識別碼或別名從可用類型清單中連接的專用連結資源。 將為發送到此資源的所有流量生成唯一的網路標識碼。       |
|目標子資源   |      要連接的子資源。 每個專用連結資源類型都有不同的選項可供選擇,具體取決於首選項。    |
|連線核准方法    |  自動或手動。 根據基於角色的訪問控制 (RBAC) 許可權,可以自動批准專用終結點。 如果嘗試連接到沒有 RBAC 的專用鏈路資源,請使用手動方法允許資源的擁有者批准連接。        |
|要求訊息     |  您可以指定一條訊息,以便手動批准請求的連接。 此消息可用於標識特定請求。        |
|連線狀態   |   指定專用終結點是否處於活動狀態的唯讀屬性。 只有處於已批准狀態的專用終結點才能用於發送流量。 其他可用狀態: <br>-**已批准**:連接已自動或手動批准,可供使用。</br><br>-**掛起**: 連接是手動創建的,正在等待專用連結資源擁有者的批准。</br><br>-**已拒絕**:連接被專用連結資源所有者拒絕。</br><br>-**已斷線連接**連接連接已刪除,由專用連結資源擁有者刪除。 專用終結點信息豐富,應刪除以進行清理。 </br>|

以下是有關私有終結點的一些關鍵詳細資訊: 
- 專用終結點允許來自同一 VNet、區域對等 VNet、全域對等 VNet 和內部消費者之間的連接,使用[VPN](https://azure.microsoft.com/services/vpn-gateway/)或[快速路由](https://azure.microsoft.com/services/expressroute/)以及由專用鏈路提供支援的服務。
 
- 創建專用終結點時,還會為資源的生命周期創建網路介面。 介面從映射到專用連結服務的子網中分配了專用 IP 位址。
 
- 專用終結點必須部署在同一區域中的虛擬網路。 
 
- 專用鏈路資源可以部署到與虛擬網路和專用終結點不同的區域中。
 
- 可以使用同一專用連結資源創建多個專用終結點。 對於使用公共 DNS 伺服器配置的單個網路,建議的做法是為給定的專用鏈路資源使用單個專用終結點,以避免重複條目或 DNS 解析中的衝突。 
 
- 可以在同一虛擬網路中的同一子網或不同子網上創建多個專用終結點。 您可以在訂閱中創建的專用終結點數有限制。 有關詳細資訊,請參閱 [Azure 限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)。


 
## <a name="private-link-resource"></a>專用連結資源 
專用鏈接資源是給定專用終結點的目標。 以下是可用專用連結資源類型的清單: 
 
|私人連結資源名稱  |資源類型   |子資源  |
|---------|---------|---------|
|**專用連結服務**(您自己的服務)   |  微軟.網路/私人連結服務       | empty |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql 伺服器 (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql 伺服器 (sqlServer)        | 
|**Azure 儲存體**  | Microsoft.Storage/storageAccounts    |  Blob(blob,blob_secondary)<BR> 表(表,table_secondary)<BR> 佇列(佇列、queue_secondary)<BR> 檔案(檔案,file_secondary)<BR> 網路(網路,web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob(blob,blob_secondary)<BR> 資料系統第二代(dfs,dfs_secondary)       |
|**Azure Cosmos DB** | 微軟.AzureCosmosDB/資料庫帳戶 | Sql, 蒙戈DB, 卡桑德拉, 格雷姆林, 表|
|**PostgreSQL - 單一伺服器的 Azure 資料庫** | Microsoft.DBforPostgreSQL/servers   | 後格雷sqlServer |
|**適用於 MySQL 的 Azure 資料庫** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**適用於 MariaDB 的 Azure 資料庫** | Microsoft.DBforMariaDB/servers    | 馬里亞德布拉塞 |
|**Azure 金鑰保存庫** | Microsoft.KeyVault/vaults    | 保存庫 |
|**Azure 庫伯奈斯服務 - 庫伯內斯 API** | Microsoft.ContainerService/managedClusters | 託管叢集 |
|**Azure 搜尋服務** | 微軟.搜尋/搜尋服務| 搜尋服務|  
|**Azure 容器註冊表** | Microsoft.ContainerRegistry/registries  | 登錄 |
|**Azure 應用程式組態** | 微軟.應用程式配置/配置商店   | 設定儲存 |
|**Azure 備份** | Microsoft.RecoveryServices/vaults   | 保存庫 |
|**Azure 事件中心** | Microsoft.EventHub/namespaces    | namespace |
|**Azure 服務匯流排** | Microsoft.ServiceBus/namespaces | namespace |
|**Azure 轉送** | Microsoft.Relay/namespaces | namespace |
|**Azure 事件網格** | Microsoft.EventGrid/topics  | 主題 |
|**Azure 事件網格** | 微軟.事件網格/域 | 網域 |
|**Azure WebApps** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces  | 工作區 |
  
 
## <a name="network-security-of-private-endpoints"></a>專用終結點的網路安全 
將專用終結點用於 Azure 服務時,流量將保護到特定的專用連結資源。 平臺執行訪問控制,以驗證僅到達指定專用鏈路資源的網路連接。 要訪問同一 Azure 服務中的其他資源,需要額外的專用終結點。 
 
您可以完全鎖定工作負荷,禁止訪問公共終結點以連接到受支援的 Azure 服務。 此控件通過提供內置的外滲保護,防止訪問同一 Azure 服務上託管的其他資源,從而為資源提供額外的網路安全層。 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>使用審批工作流存取專用連結資源 
您可以使用以下連線核准方法連線到專用連結資源:
- 當您擁有或擁有特定專用連結資源的許可權時**自動**批准。 所需的許可權基於以下格式的專用連結資源類型:Microsoft。\<供應商>/<resource_type>/專用端接連接審批/操作
- 當您沒有所需的許可權並且希望請求訪問許可權時,請**手動**請求。 將啟動審批工作流。 私人端點和後續的私人端點連線將會以「擱置」狀態來建立。 私人連結資源的擁有者會負責核准連線。 批准后,私有終結點將啟用正常發送流量,如下讚工作流圖所示。  

![工作流審批](media/private-endpoint-overview/private-link-paas-workflow.png)
 
專用連結資源擁有者可以通過專用終結點連接執行以下操作: 
- 查看所有專用終結點連接詳細資訊。 
- 批准專用終結點連接。 將啟用相應的專用終結點,以便將流量發送到專用連結資源。 
- 拒絕專用終結點連接。 相應的私有終結點將更新以反映狀態。
- 刪除處於任何狀態的專用終結點連接。 相應的私有終結點將更新與斷開連接的狀態以反映操作,專用終結點擁有者此時只能刪除資源。 
 
> [!NOTE]
> 只有處於已批准狀態的專用終結點才能將流量發送到給定的專用鏈路資源。 

### <a name="connecting-using-alias"></a>使用別名連線
Alias 是服務擁有者在標準負載均衡器後面創建專用鏈路服務時生成的唯一名字。 服務擁有者可以離線與其使用者共用此別名。 消費者可以使用資源URI或別名請求連接到專用連結服務。 如果要使用別名連接,則必須使用手動連接審批方法創建專用終結點。 對於使用手動連接審批方法,在專用終結點創建流期間將手動請求參數設置為 true。 有關詳細資訊,請查看[New-AzPrivate 終結點](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0)和[az 網路專用終結點建立](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create)。 

## <a name="dns-configuration"></a>DNS 組態 
當使用完全限定的功能變數名稱 (FQDN) 作為連接字串的一部分連接到專用連結資源時,正確配置 DNS 設置以解析為分配的專用 IP 位址非常重要。 通過公共終結點連接時,現有 Azure 服務可能已經具有要使用的 DNS 配置。 需要重寫此功能才能使用專用終結點進行連接。 
 
與專用終結點關聯的網路介面包含配置 DNS 所需的完整資訊集,包括為給定專用鏈路資源分配的 FQDN 和專用 IP 位址。 
 
您可以使用以下選項為專用的終結點設定 DNS 設定: 
- **使用主機檔(僅建議進行測試)。** 您可以使用虛擬機器上的主機檔覆蓋 DNS。  
- **使用私人 DNS 區域**。 可以使用專用 DNS 區域覆蓋給定專用終結點的 DNS 解析。 專用 DNS 區域可以連結到虛擬網路以解決特定域。
- **使用自訂 DNS 伺服器**。 您可以使用自己的 DNS 伺服器來覆蓋給定專用連結資源的 DNS 解析。 如果您的[DNS 伺服器](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)託管在虛擬網路上,則可以創建 DNS 轉發規則以使用專用 DNS 區域來簡化所有專用鏈路資源的配置。
 
> [!IMPORTANT]
> 不建議覆蓋主動用於解析公共終結點的區域。 如果沒有 DNS 轉發到公共 DNS,則無法正確解析與資源的連接。 為避免出現問題,請創建不同的功能變數名稱或按照以下每個服務的建議名稱進行操作。 
 
對於 Azure 服務,請使用下表中所述的建議區域名稱:

|私人連結資源類型   |子資源  |區域名稱  |
|---------|---------|---------|
|SQL DB(微軟.Sql/伺服器)    |  Sql 伺服器 (sqlServer)        |   privatelink.database.windows.net       |
|Azure 突觸分析(微軟.Sql/伺服器)    |  Sql 伺服器 (sqlServer)        | privatelink.database.windows.net |
|儲存帳戶(微軟.儲存/儲存帳戶)    |  Blob(blob,blob_secondary)        |    privatelink.blob.core.windows.net      |
|儲存帳戶(微軟.儲存/儲存帳戶)    |    表(表,table_secondary)      |   privatelink.table.core.windows.net       |
|儲存帳戶(微軟.儲存/儲存帳戶)    |    佇列(佇列、queue_secondary)     |   privatelink.queue.core.windows.net       |
|儲存帳戶(微軟.儲存/儲存帳戶)   |    檔案(檔案,file_secondary)      |    privatelink.file.core.windows.net      |
|儲存帳戶(微軟.儲存/儲存帳戶)     |  網路(網路,web_secondary)        |    privatelink.web.core.windows.net      |
|資料湖檔案系統第2代(微軟.儲存/儲存帳戶)  |  資料系統第二代(dfs,dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure 宇宙資料庫(微軟.AzureCosmosDB/資料庫帳戶)|SQL |privatelink.documents.azure.com|
|Azure 宇宙資料庫(微軟.AzureCosmosDB/資料庫帳戶)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure 宇宙資料庫(微軟.AzureCosmosDB/資料庫帳戶)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure 宇宙資料庫(微軟.AzureCosmosDB/資料庫帳戶)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure 宇宙資料庫(微軟.AzureCosmosDB/資料庫帳戶)|Table|privatelink.table.cosmos.azure.com|
|後資料庫的 Azure 資料庫 - 單一伺服器(微軟.DBforpostsql/伺服器)|後格雷sqlServer|privatelink.postgres.database.azure.com|
|MySQL 的 Azure 資料庫(微軟.DBforMySQL/伺服器)|mysqlServer|privatelink.mysql.database.azure.com|
|MariaDB 的 Azure 資料庫(微軟.DBforMariaDB/伺服器)|馬里亞德布拉塞|privatelink.mariadb.database.azure.com|
|Azure 金鑰保管庫(微軟.金鑰庫/保管庫)|保存庫|privatelink.vaultcore.azure.net|
|Azure 庫伯奈斯服務 - 庫伯奈斯 API(微軟.容器服務/託管群集) | 託管叢集 | [guid].私人連結。<region>.azmk8s.io|
|Azure 搜尋(微軟.搜尋/搜尋服務)|搜尋服務|privatelink.search.windows.net|   
|Azure 容器註冊錶(微軟.集裝箱註冊/註冊表) | 登錄 | privatelink.azurecr.io |
|Azure 應用設定 (微軟.應用配置/配置儲存)| 設定儲存 | privatelink.azconfig.io|
|Azure 備份(微軟.恢復服務/保管庫)| 保存庫 |私人連結。[地區].備份.windowsazure.com|
|Azure 事件中心(微軟.事件中心/命名空間)| namespace |privatelink.servicebus.windows.net|
|Azure 服務總線(微軟.服務總線/命名空間) | namespace |privatelink.servicebus.windows.net|
|Azure 轉送(微軟.中繼/命名空間) | namespace |privatelink.servicebus.windows.net|
|Azure 事件網格(微軟.事件網格/主題)   | 主題 | 主題。[區域].私人連結.事件網格.azure.net|
|Azure 事件網格(微軟.事件網格/域) | 網域 | 域。[區域].私人連結.事件網格.azure.net |
|Azure WebApps(微軟.網站/網站) | site | privatelink.azurewebsites.net |
|Azure 機器學習(微軟.機器學習服務/工作區)   | 工作區 | privatelink.api.azureml.ms |
 
Azure 將在公共 DNS 上建立規範名稱 DNS 記錄 (CNAME), 以將解析重定向到建議的網功能變數名稱。 您可以使用專用終結點的專用 IP 位址覆蓋解析度。 
 
應用程式不需要更改連接 URL。 嘗試使用公共 DNS 解析時,DNS 伺服器現在將解析為專用終結點。 該過程不會影響您的應用程式。 
 
## <a name="limitations"></a>限制
 
下表包括使用專用終結點時的已知限制清單: 


|限制 |描述 |降低  |
|---------|---------|---------|
|網路安全群組 (NSG) 規則與使用者定義路由不適用於專用終結點    |私有終結點不支援 NSG。 雖然包含專用終結點的子網可以具有與它關聯的 NSG,但規則對專用終結點處理的流量無效。 必須[禁用網路策略強制才能](disable-private-endpoint-network-policy.md)在子網中部署專用終結點。 NSG仍對託管在同一子網上的其他工作負載強制執行。 任何用戶端子網上的路由都將使用 /32 首元,更改預設路由行為需要類似的 UDR  | 使用 NSG 規則控制源用戶端上的出站流量。 使用 /32 首碼部署單個路由以覆蓋專用終結點路由。 NSG 串流紀錄和出站連接監視資訊仍然受支援,可以使用        |


## <a name="next-steps"></a>後續步驟
- [使用入口網站建立適用於 SQL Database 伺服器的私人端點](create-private-endpoint-portal.md)
- [使用 PowerShell 建立適用於 SQL Database 伺服器的私人端點](create-private-endpoint-powershell.md)
- [使用 CLI 建立適用於 SQL Database 伺服器的私人端點](create-private-endpoint-cli.md)
- [使用入口網站建立適用於儲存體帳戶的私人端點](create-private-endpoint-storage-portal.md)
- [使用入口網站建立適用於 Azure Cosmos 帳戶的私人端點](../cosmos-db/how-to-configure-private-endpoints.md)
- [使用 Azure PowerShell 建立您自己的 Private Link 服務](create-private-link-service-powershell.md)
- [為 PostgreSQL 的 Azure 資料庫建立您自己的專用連結 - 使用門戶建立單個伺服器](../postgresql/howto-configure-privatelink-portal.md)
- [為後格雷SQL 的 Azure 資料庫建立您自己的專用連結 - 使用 CLI 建立單一伺服器](../postgresql/howto-configure-privatelink-cli.md)
- [使用門戶為 MySQL 為 Azure 資料庫建立您自己的專用連結](../mysql/howto-configure-privatelink-portal.md)
- [使用 CLI 為 MySQL 為 Azure 資料庫建立您自己的專用連結](../mysql/howto-configure-privatelink-cli.md)
- [使用門戶為 MariaDB 為 Azure 資料庫建立自己的專用連結](../mariadb/howto-configure-privatelink-portal.md)
- [使用 CLI 為 MariaDB 為 Azure 資料庫建立自己的專用連結](../mariadb/howto-configure-privatelink-cli.md)
