---
title: 使用 Kafka Connect 整合 Apache Kafka 和 Azure Cosmos DB Cassandra API
description: 瞭解如何使用 DataStax Apache Kafka 連接器，將資料從 Kafka 內嵌至 Azure Cosmos DB Cassandra API
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: a233845e8f19cc44cd9d00a0392b1341db297fd6
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632639"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>使用 Kafka Connect 將 Apache Kafka 中的資料內嵌至 Azure Cosmos DB Cassandra API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

現有的 Cassandra 應用程式可以輕易地使用 [Azure Cosmos DB Cassandra API](cassandra-introduction.md) ，因為其 [CQLv4 驅動程式相容性](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)。 您可以利用這項功能來與串流平臺（例如 [Apache Kafka](https://kafka.apache.org/) ）整合，並將資料帶入 Azure Cosmos DB。

Apache Kafka (主題) 的資料僅適用于其他應用程式或內嵌到其他系統時。 您可以使用[您選擇的語言和用戶端 SDK](https://cwiki.apache.org/confluence/display/KAFKA/Clients)，建立使用[Kafka 生產者/取用者](https://kafka.apache.org/documentation/#api)api 的解決方案。 Kafka Connect 提供替代的解決方案。 它是以可調整且可靠的方式，在 Apache Kafka 和其他系統之間串流資料的平臺。 因為 Kafka Connect 支援包含 Cassandra 的貨架連接器，所以您不需要撰寫自訂程式碼來整合 Kafka 與 Azure Cosmos DB 的 Cassandra API。 

在本文中，我們將使用開放原始碼 [DataStax Apache Kafka 連接器](https://docs.datastax.com/kafka/doc/kafka/kafkaIntro.html)，此連接器可在 Kafka Connect 架構之上，將 Kafka 主題的記錄內嵌至一個或多個 Cassandra 資料表的資料列中。 此範例會使用 Docker Compose 提供可重複使用的設定。 這相當方便，因為它可讓您使用單一命令，在本機啟動所有必要的元件。 這些元件包括 Kafka、Zookeeper、Kafka Connect worker 和範例資料產生器應用程式。

以下是元件及其服務定義的細目，您可以參考 GitHub 存放庫中的完整 `docker-compose` 檔案[](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)。

- Kafka 和 Zookeeper 會使用 [debezium](https://hub.docker.com/r/debezium/kafka/) 映射。
- 若要以 Docker 容器的形式執行，DataStax Apache Kafka 連接器會內建于現有的 Docker 映射- [debezium/connect 基礎](https://github.com/debezium/docker-images/tree/master/connect-base/1.2)之上。 此映射包含 Kafka 及其 Kafka Connect 程式庫的安裝，因此可讓您輕鬆地新增自訂連接器。 您可以參考 [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile)。
- `data-generator`服務會隨機產生 (JSON) 資料到 `weather-data` Kafka 主題中。 您可以參考程式碼和 `Dockerfile` [GitHub](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)存放庫中的

## <a name="prerequisites"></a>必要條件

* [佈建 Azure Cosmos DB Cassandra API 帳戶](create-cassandra-dotnet.md#create-a-database-account)

* [使用 cqlsh 或 hosted shell 進行驗證](cassandra-support.md#hosted-cql-shell-preview)

* 安裝 [Docker](https://docs.docker.com/get-docker/) 和 [Docker Compose](https://docs.docker.com/compose/install)

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>建立 Keyspace、資料表和啟動整合管線

使用 Azure 入口網站，建立示範應用程式所需的 Cassandra Keyspace 和資料表。

> [!NOTE]
> 使用相同的 Keyspace 和資料表名稱，如下所示

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

複製 GitHub 存放庫：

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

啟動所有服務：

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> 下載並啟動容器可能需要一段時間：這只是一次的處理常式。

若要確認是否已啟動所有容器：

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

資料產生器應用程式會開始將資料提取到 `weather-data` Kafka 中的主題。 您也可以進行快速的健全檢查來確認。 查看執行 Kafka connect worker 的 Docker 容器：


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

一旦您進入容器 shell，只要啟動一般的 Kafka 主控台取用者進程，您應該會看到天氣資料 (以 JSON 格式) 流入。

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra 接收連接器設定

將以下的 JSON 內容複寫到檔案 (您可以將它命名 `cassandra-sink-config.json`) 。 您將需要根據您的設定來更新它，本節的其餘部分將提供本主題的指引。

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

以下是屬性的摘要：

**基本連線能力**

- `contactPoints`：輸入 Cosmos DB Cassandra 的連絡人點
- `loadBalancing.localDc`：輸入 Cosmos DB 帳戶的區域，例如東南亞
- `auth.username`：輸入使用者名稱
- `auth.password`：輸入密碼
- `port`：輸入埠值 (這是 `10350` `9042` 。 讓它保持原狀) 

**SSL 設定**

Azure Cosmos DB 透過 [ssl](database-security.md) 強制執行安全連線，而且 Kafka Connect connector 也支援 ssl。

- `ssl.keystore.path`：容器中的 JDK 金鑰儲存區路徑- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`： JDK 金鑰儲存區 (預設) 密碼
- `ssl.hostnameValidation`：我們會開啟自己的節點主機名稱驗證
- `ssl.provider`： `JDK` 用來做為 SSL 提供者

**泛型參數**

- `key.converter`：我們使用字串轉換器 `org.apache.kafka.connect.storage.StringConverter`
- `value.converter`：由於 Kafka 主題中的資料是 JSON，因此我們利用 `org.apache.kafka.connect.json.JsonConverter`
- `value.converter.schemas.enable`：由於我們的 JSON 承載沒有與其相關聯的架構 (基於示範應用程式) 的目的，我們必須指示 Kafka Connect 不要藉由將此屬性設定為來尋找架構 `false` 。 若未這麼做，將會導致失敗。

### <a name="install-the-connector"></a>安裝連接器

使用 Kafka Connect REST 端點安裝連接器：

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

若要檢查狀態：

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

如果一切順利，連接器應該會開始將其魔術。 它應該進行驗證以 Azure Cosmos DB，並從 Kafka 主題開始擷取資料， (`weather-data`) 至 Cassandra 資料表- `weather.data_by_state` 和 `weather.data_by_station`

您現在可以查詢資料表中的資料。 移至 Azure 入口網站，為您的 Azure Cosmos DB 帳戶啟動 hosted CQL Shell。

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="開啟 CQLSH":::

## <a name="query-data-from-azure-cosmos-db"></a>從 Azure Cosmos DB 查詢資料

檢查 `data_by_state` 和 `data_by_station` 資料表。 以下是一些可協助您開始使用的範例查詢：

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>後續步驟

* [在容器和資料庫中佈建輸送量](set-throughput.md) 
* [分割區索引鍵的最佳作法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB 容量規劃文章來預估 RU/秒](estimate-ru-with-capacity-planner.md)
