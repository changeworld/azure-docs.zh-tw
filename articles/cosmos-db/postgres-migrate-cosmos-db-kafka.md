---
title: 使用 Apache Kafka 將資料從于 postgresql 遷移至 Azure Cosmos DB Cassandra API 帳戶
description: 瞭解如何使用 Kafka Connect 將即時的資料從于 postgresql 同步處理至 Azure Cosmos DB Cassandra API。
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 24714b185b0f666770b306a7e80a97a3f8f868a3
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052618"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>使用 Apache Kafka 將資料從于 postgresql 遷移至 Azure Cosmos DB Cassandra API 帳戶
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 已成為在 Apache Cassandra 上執行之企業工作負載的絕佳選擇，原因如下：

* **大幅節省成本：** 您可以使用 Azure Cosmos DB 來節省成本，包括 VM 的成本、頻寬和任何適用的 Oracle 授權。 此外，您不需要管理資料中心、伺服器、SSD 儲存體、網路和電力成本。

* **更好的擴充性和可用性：** 它可消除單一失敗點、更好的擴充性，以及應用程式的可用性。

* **管理和監視沒有額外負荷：** 做為完全受控的雲端服務，Azure Cosmos DB 消除了管理和監視無數設定的額外負荷。

[Kafka Connect](https://kafka.apache.org/documentation/#connect) 是以可調整且可靠的方式，在 [Apache Kafka](https://kafka.apache.org/) 和其他系統之間串流資料的平臺。 它支援數個現成的連接器，這表示您不需要自訂程式碼，就能將外部系統與 Apache Kafka 整合。

本文將示範如何使用 Kafka 連接器的組合來設定資料管線，以持續將關係資料庫（例如 [于 postgresql](https://www.postgresql.org/) ）的記錄同步處理至 [Azure Cosmos DB Cassandra API](cassandra-introduction.md)。

## <a name="overview"></a>概觀

以下是本文中提供的端對端流程概要說明。

于 postgresql 資料表中的資料將會使用 [Debezium 于 postgresql 連接器](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)（這是 Kafka Connect **來源** 連接器）推送至 Apache Kafka。 在於 postgresql 資料表中，插入、更新或刪除記錄，將會被視為事件來捕捉， `change data` 並傳送至 Kafka 主題 (s) 。 [DataStax Apache Kafka 連接器](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (Kafka Connect **接收** 連接器) ，形成管線的第二個部分。 它會將 Kafka 主題的變更資料事件同步處理至 Azure Cosmos DB Cassandra API 資料表。

> [!NOTE]
> 使用 DataStax Apache Kafka 連接器的特定功能，可讓我們將資料推送至多個資料表。 在此範例中，連接器將協助我們將變更資料記錄保存到兩個可支援不同查詢需求的 Cassandra 資料表。

## <a name="prerequisites"></a>先決條件

* [佈建 Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account)
* [使用 cqlsh 或 hosted shell 進行驗證](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 或更新版本
* [Docker](https://www.docker.com/) (選用) 

## <a name="base-setup"></a>基本設定

### <a name="set-up-postgresql-database-if-you-havent-already"></a>設定于 postgresql 資料庫（如果尚未安裝）。 

這可以是現有的內部部署資料庫，也可以在本機電腦上 [下載並安裝](https://www.postgresql.org/download/) 。 也可以使用 [Docker 容器](https://hub.docker.com/_/postgres)。

若要啟動容器：

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

使用用戶端連接到您的于 postgresql 實例 [`psql`](https://www.postgresql.org/docs/current/app-psql.html) ：

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

建立資料表來儲存範例訂單資訊：

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>使用 Azure 入口網站，建立示範應用程式所需的 Cassandra Keyspace 和資料表。

> [!NOTE]
> 使用相同的 Keyspace 和資料表名稱，如下所示

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>設定 Apache Kafka 

本文使用本機叢集，但您可以選擇任何其他選項。 [下載 Kafka](https://kafka.apache.org/downloads)、將它解壓縮，然後啟動 Zookeeper 和 Kafka 叢集。

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>設定連接器

安裝 Debezium 于 postgresql 和 DataStax Apache Kafka 連接器。 下載 Debezium 于 postgresql connector 外掛程式封存。 例如，若要下載連接器的版本 1.3.0 (最新的) ，請使用 [此連結](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz)。 從此 [連結](https://downloads.datastax.com/#akc)下載 DataStax Apache Kafka 連接器。

將連接器封存解壓縮，並將 JAR 檔案複製到 [Kafka Connect 外掛程式。路徑](https://kafka.apache.org/documentation/#connectconfigs)。


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> 如需詳細資訊，請參閱 [Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) 和 [DataStax](https://docs.datastax.com/en/kafka/doc/) 檔。

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>設定 Kafka Connect 和啟動資料管線

### <a name="start-kafka-connect-cluster"></a>啟動 Kafka Connect 叢集

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>啟動于 postgresql 連接器實例

將連接器設定 (JSON) 儲存至檔案範例 `pg-source-config.json`

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

若要啟動于 postgresql 連接器實例：

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> 若要刪除，您可以使用： `curl -X DELETE http://localhost:8083/connectors/pg-orders-source`


### <a name="insert-data"></a>插入資料

`orders_info`資料表包含訂單識別碼、客戶識別碼、城市等的訂單詳細資料。使用下列 SQL，在資料表中填入亂數據。

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

它應該會將10筆記錄插入資料表中。 請務必依您的需求範例來更新以下的記錄數目 `generate_series(1, 10)` ，以插入 `100` 記錄，請使用 `generate_series(1, 100)`

若要確認：

```bash
select * from retail.orders_info;
```

查看 Kafka 主題中的變更資料捕獲事件

> [!NOTE]
> 請注意，主題名稱是 `myserver.retail.orders_info` 根據 [連接器慣例](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

您應該會看到變更資料事件的 JSON 格式。

### <a name="start-datastax-apache-kafka-connector-instance"></a>開始 DataStax Apache Kafka 連接器實例

將連接器設定 (JSON) 儲存至檔案範例， `cassandra-sink-config.json` 並依據您的環境更新屬性。

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

若要啟動連接器實例：

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

連接器應該會進入動作，而從于 postgresql 到 Azure Cosmos DB 的端對端管線將可運作。

### <a name="query-azure-cosmos-db"></a>查詢 Azure Cosmos DB

檢查 Azure Cosmos DB 中的 Cassandra 資料表。 以下是一些您可以嘗試的查詢：

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

您可以繼續在於 postgresql 中插入更多資料，並確認記錄已同步處理至 Azure Cosmos DB。

## <a name="next-steps"></a>下一步

* [使用 Kafka Connect 整合 Apache Kafka 和 Azure Cosmos DB Cassandra API](cassandra-kafka-connect.md)
* [使用 Debezium for Change Data Capture 整合 Apache Kafka Connect on Azure 事件中樞 (Preview) ](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [使用 Blitzz 將將資料從 Oracle 遷移至 Azure Cosmos DB Cassandra API](oracle-migrate-cosmos-db-blitzz.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md) 
* [分割區索引鍵的最佳作法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB 容量規劃文章來預估 RU/秒](estimate-ru-with-capacity-planner.md)

