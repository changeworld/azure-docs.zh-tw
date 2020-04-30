---
title: Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能
description: 了解 Azure Cosmos DB Cassandra API 中的 Apache Cassandra 功能
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 223544f7ceddce6bc2071d561da1cff1c0d4b53b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80420160"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能 

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 您可以透過 Cassandra 查詢語言 (CQL) v4 [有線通訊協定](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec)合規性開放原始碼 Cassandra 用戶端[驅動程式](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)與 Azure Cosmos DB Cassandra API 通訊。 

透過使用 Azure Cosmos DB Cassandra API，您可以享受 Apache Cassandra API 的好處，以及 Azure Cosmos DB 所提供的企業功能。 企業功能包括[全域散發](distribute-data-globally.md)、[自動擴增分割處理](partition-data.md)、可用性與延遲保證、靜態資料加密、備份等。

## <a name="cassandra-protocol"></a>Cassandra 通訊協定 

Azure Cosmos DB Cassandra API 與 CQL 版本 **v4** 相容。 支援的 CQL 命令、工具、限制與例外，如下所示。 能解析這些通訊協定的任何用戶端驅動程式，都應該能夠使用連線到 Azure Cosmos DB Cassandra API。

## <a name="cassandra-driver"></a>Cassandra 驅動程式

Azure Cosmos DB Cassandra API 支援下列 Cassandra 驅動程式：

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CQL 資料類型 

Azure Cosmos DB Cassandra API 支援下列 CQL 資料類型：

* ascii  
* BIGINT  
* blob  
* boolean  
* counter  
* date  
* decimal  
* double  
* FLOAT  
* 冰雪奇緣  
* inet  
* int  
* list  
* set  
* SMALLINT  
* text  
* time  
* timestamp  
* timeuuid  
* TINYINT  
* Tuple  
* uuid  
* varchar  
* varint  
* Tuple  
* udts  
* map  

## <a name="cql-functions"></a>CQL 功能

Azure Cosmos DB Cassandra API 支援下列 CQL 函式：

* Token  
* 彙總函式
  * min、max、avg、count
* Blob 轉換函式 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID 與 timeuuid 功能 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-api-limits"></a>Cassandra API 限制

Azure Cosmos DB Cassandra API 在資料表中存放的資料大小沒限制。 可以儲存數百 TB 或 PB 的資料，同時確保遵守資料分割金鑰。 同樣地，每個實體或資料列對等項在資料行數目上沒有任何限制。 不過，實體的總大小不應該超過 2 MB。 就像所有其他 API 一樣，每個分割區索引鍵的資料不能超過 20 GB。

## <a name="tools"></a>工具 

Azure Cosmos DB Cassandra API 是受控服務平台。 它不會造成任何管理額外負荷，也不會使用記憶體回收行程、Java 虛擬機器 (JVM) 與 nodetool 等公用程式來管理叢集。 它支援 cqlsh 之類的工具，cqlsh 利用 Binary CQLv4 相容性。 

* Azure 入口網站的資料總管、計量、記錄診斷、PowerShell 及 CLI 均為其他支援管理帳戶的機制。

## <a name="cql-shell"></a>CQL Shell  

CQLSH 命令列公用程式隨附 Apache Cassandra 3.1.1，而且只需設定一些環境變數即可立即運作。

**Windows：**

如果使用 Windows，建議您啟用[適用於 Linux 的 Windows 檔案系統](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux)。 您接著可遵循以下的 Linux 命令。

**Unix/Linux/Mac：**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>CQL 命令

Azure Cosmos DB 支援在 Cassandra API 帳戶上使用下列資料庫命令。

* CREATE KEYSPACE (已忽略此命令的複寫設定)
* CREATE TABLE 
* CREATE INDEX (不指定索引名稱，以及尚未支援完整凍結索引)
* 允許篩選
* ALTER TABLE 
* USE 
* Insert 
* SELECT 
* UPDATE 
* BATCH - 只支援未記錄的命令 
* 刪除

透過 CQL v4 相容 SDK 執行的所有 CRUD 作業，將傳回有關錯誤及已取用的要求單位等額外資訊。 DELETE 和 UPDATE 命令應使用納入考慮的資源管理來處理，確保會以最有效率的方式使用佈建的輸送量。

* 請注意，所指定的 gc_grace_seconds 值必須是零。

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>一致性對應 

Azure Cosmos DB Cassandra API 提供讀取作業的一致性選擇。  一致性對應[在此](consistency-levels-across-apis.md#cassandra-mapping)有詳細說明。

## <a name="permission-and-role-management"></a>權限與角色管理

Azure Cosmos DB 支援角色型存取控制 (RBAC) 來佈建、輪替金鑰、檢視計量及讀寫和唯讀密碼/金鑰，其可透過 [Azure 入口網站](https://portal.azure.com)取得。 Azure Cosmos DB 不支援 CRUD 活動的角色。

## <a name="keyspace-and-table-options"></a>Keyspace 和資料表選項

目前已忽略 "Create Keyspace" 命令中區域名稱、類別、replication_factor 和資料中心的選項。 系統會使用基礎 Azure Cosmos DB 的[全域散發](global-dist-under-the-hood.md)複寫方法來新增區域。 如果您需要資料跨區域存在，可以使用 PowerShell、CLI 或入口網站在帳戶層級加以啟用。若要深入了解，請參閱[如何新增區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)一文。 無法停用 Durable_writes，因為 Azure Cosmos DB 可確保每次寫入都是持久的。 在每個區域中，Azure Cosmos DB 會將資料複寫到由四個複本組成的複本集，而此複本集[設定](global-dist-under-the-hood.md)無法加以修改。
 
建立資料表時會忽略所有選項，但應設定為零的 gc_grace_seconds 除外。
Keyspace 和資料表有一個名為 "cosmosdb_provisioned_throughput" 的額外選項，其最小值為 400 RU/秒。 Keyspace 輸送量可讓您跨多個資料表共用輸送量，而當所有資料表均未使用佈建的輸送量時就很有用。 Alter Table 命令可讓您變更跨區域佈建的輸送量。 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>使用 Cassandra 重試連線原則

Azure Cosmos DB 是資源控管系統。 這表示您可以根據作業所取用的要求單位，在規定的秒數內執行特定數量的作業。 如果應用程式在規定的秒數內超過該限制，則要求會受到速率限制，並且會擲回例外狀況。 Azure Cosmos DB 中的 Cassandra API 會將這些例外狀況轉譯成 Cassandra 原生通訊協定上的多載錯誤。 為了確保您的應用程式可以在發生速率限制時攔截並重試要求，因而提供 [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) 和 [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) 擴充功能。 如果您使用其他 SDK 來存取 Azure Cosmos DB 中的 Cassandra API，請建立連線原則以在這些例外狀況重試。

## <a name="next-steps"></a>後續步驟

- 開始使用 Java 應用程式來[建立 Cassandra API 帳戶、資料庫及資料表](create-cassandra-api-account-java.md)。

