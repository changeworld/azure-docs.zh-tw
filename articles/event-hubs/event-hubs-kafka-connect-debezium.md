---
title: '使用 Debezium for Change Data Capture 整合 Apache Kafka Connect on Azure 事件中樞 (Preview) '
description: 本文提供有關如何使用 Debezium 搭配適用于 Kafka Azure 事件中樞的資訊。
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: a13713f01a6bdb0ffcd787ef9c1d2f9a0336f63c
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369551"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>使用 Debezium for Change Data Capture 整合 Azure 事件中樞 (Preview) 上的 Apache Kafka Connect 支援

**變更資料捕獲 (CDC) ** 是一種技術，用來追蹤資料庫資料表中的資料列層級變更，以回應建立、更新和刪除作業。 [Debezium](https://debezium.io/) 是以不同資料庫中可用的變更資料捕獲功能為基礎的分散式平臺 (例如，于 postgresql) [中的邏輯解碼](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html) 。 它提供一組 [Kafka Connect 連接器](https://debezium.io/documentation/reference/1.2/connectors/index.html) ，可在資料庫資料表中的資料列層級變更 (s) ，然後將它們轉換成事件串流，然後再傳送至 [Apache Kafka](https://kafka.apache.org/)。

本教學課程會逐步引導您瞭解如何使用適用于 Kafka) 、 [AZURE DB For 于 postgresql](../postgresql/overview.md)和 Debezium 的[Azure 事件中樞](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (，在 azure 上設定變更資料捕獲型系統。 它將使用 [Debezium 于 postgresql 連接器](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html) ，從于 postgresql 將資料庫修改串流至 Azure 事件中樞中的 Kafka 主題

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立事件中樞命名空間
> * 設定和設定適用於 PostgreSQL 的 Azure 資料庫
> * 使用 Debezium 于 postgresql 連接器設定和執行 Kafka Connect
> * 測試變更資料捕獲
> *  (選擇性) 使用連接器來取用變更資料事件 `FileStreamSink`

## <a name="pre-requisites"></a>必要條件
若要完成這個逐步解說，您需要：

- Azure 訂用帳戶。 如果您沒有訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。
- Linux/MacOS
- Kafka 版本 (1.1.1 版，Scala 2.11 版)，取得來源為 [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- 請參閱[適用於 Apache Kafka 的事件中樞](./event-hubs-for-kafka-ecosystem-overview.md)簡介文章

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
您需要事件中樞命名空間，才能從任何事件中樞服務傳送和接收。 請參閱 [建立事件中樞](event-hubs-create.md) ，以取得建立命名空間和事件中樞的指示。 請取得事件中樞連接字串和完整網域名稱 (FQDN) 以供稍後使用。 如需相關指示，請參閱[取得事件中樞連接字串](event-hubs-get-connection-string.md)。 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>設定和設定適用於 PostgreSQL 的 Azure 資料庫
[適用於 PostgreSQL 的 Azure 資料庫](../postgresql/overview.md) 是以開放原始碼于 postgresql 資料庫引擎的社區版本為基礎的關係資料庫服務，並提供兩種部署選項：單一伺服器和超大規模 (Citus) 。 [請遵循下列指示](../postgresql/quickstart-create-server-database-portal.md) ，使用 Azure 入口網站來建立適用於 PostgreSQL 的 Azure 資料庫的伺服器。 

## <a name="setup-and-run-kafka-connect"></a>設定和執行 Kafka Connect
本節將討論下列主題：

- Debezium 連接器安裝
- 設定事件中樞的 Kafka Connect
- 使用 Debezium 連接器開始 Kafka Connect cluster

### <a name="download-and-setup-debezium-connector"></a>下載並設定 Debezium 連接器
請依照 [Debezium 檔](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) 中的最新指示，下載並設定連接器。

- 下載連接器的外掛程式封存。 例如，若要下載連接器的版本 `1.2.0` ，請使用下列連結： https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- 將 JAR 檔案解壓縮，並將它們複製到 [Kafka Connect 外掛程式。 path](https://kafka.apache.org/documentation/#connectconfigs)。


### <a name="configure-kafka-connect-for-event-hubs"></a>設定事件中樞的 Kafka Connect
將 Kafka Connect 輸送量從 Kafka 重新導向至事件中樞時，需要稍微重新設定。  下列 `connect-distributed.properties` 範例說明如何設定 Connect，以向事件中樞上的 Kafka 端點進行驗證與通訊：

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> 將 `{YOUR.EVENTHUBS.CONNECTION.STRING}` 取代為事件中樞命名空間的連接字串。 如需取得連接字串的指示，請參閱 [取得事件中樞連接字串](event-hubs-get-connection-string.md)。 以下是範例設定： `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-kafka-connect"></a>執行 Kafka Connect
在此步驟中，Kafka Connect 背景工作角色會以分散模式在本機啟動，使用事件中樞來維持叢集的狀態。

1. 將上述 `connect-distributed.properties` 檔案儲存在本機。  請務必取代括號中的所有值。
2. 瀏覽至機器上的 Kafka 版本位置。
3. 執行 `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` 並等候叢集啟動。

> [!NOTE]
> Kafka Connect 會使用 Kafka AdminClient API，以使用建議的設定 (包括壓縮) 自動建立主題。 在 Azure 入口網站中快速檢查命名空間，會顯露出 Connect 背景工作角色的內部主題已自動建立。
>
> Kafka Connect 內部主題**必須使用壓縮**。  若未正確設定 [內部連線] 主題，事件中樞小組將不會負責修正不當的設定。

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>設定並啟動 Debezium 于 postgresql 來源連接器

建立于 postgresql 來源連接器的設定檔 (`pg-source-connector.json`) -依據您的 Azure 于 postgresql 實例取代這些值。

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` 屬性是識別並提供所監視之特定于 postgresql 資料庫伺服器/叢集命名空間的邏輯名稱。 如需詳細資訊，請參閱 [Debezium 檔](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

若要建立連接器的實例，請使用 Kafka Connect REST API 端點：

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

若要檢查連接器的狀態：

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>測試變更資料捕獲
若要查看變更資料捕獲的實際運作，您必須建立/更新/刪除 Azure 于 postgresql 資料庫中的記錄。

從連接到您的 Azure 于 postgresql 資料庫開始， (下列範例使用 [psql](https://www.postgresql.org/docs/12/app-psql.html)) 

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**建立資料表並插入記錄**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

連接器現在應該會進入動作中，並將變更資料事件傳送到事件中樞主題，並以下列 na 為 e `my-server.public.todos` ，假設您擁有 `my-server` 作為的值 `database.server.name` ，而且 `public.todos` 是您要追蹤其變更的依據設定的資料表 (`table.whitelist`) 

**檢查事件中樞主題**

讓我們 introspect 主題的內容，以確定一切都如預期般運作。 下列範例會使用 [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) ，但您也可以 [使用此處所列的任何選項來建立取用者](apache-kafka-developer-guide.md)

使用下列內容建立名為的檔案 `kafkacat.conf` ：

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> `metadata.broker.list` `sasl.password` 依據事件中樞資訊，更新和中的屬性 `kafkacat.conf` 。 

在不同的終端機中，啟動取用者：

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

您應該會看到 JSON 承載，代表在於 postgresql 中產生的變更資料事件，以回應您剛剛新增至資料表的資料列 `todos` 。 以下是承載的程式碼片段：


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

事件是由 `payload` 和其 `schema` (省略，以求簡潔) 。 在 `payload` 區段中，請注意建立作業 (`"op": "c"`) 如何表示 `"before": null` ，這表示它是新的 `INSERT` ed `after` 資料列、提供資料列中資料行的值，並 `source` 提供從中挑選這個事件的于 postgresql 實例中繼資料等等。

您也可以使用 update 或 delete 作業來嘗試相同的作業，並 introspect 變更資料事件。 例如，若要更新 (的工作狀態， `configure and install connector` 假設其 `id` 為 `3`) ：

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a> (選用) 安裝 FileStreamSink 連接器
既然所有 `todos` 資料表變更都是在事件中樞主題中取得，我們將會使用 Kafka Connect) 預設提供的 FileStreamSink 連接器 (來使用這些事件。

建立連接器的設定檔 (`file-sink-connector.json`) - `file` 依據您的檔案系統取代屬性

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

若要建立連接器並檢查其狀態：

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

插入/更新/刪除資料庫記錄，並監視已設定之輸出接收檔案中的記錄：

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>清理
Kafka Connect 會建立事件中樞主題，以儲存即使在 Connect 叢集關閉後仍會保存下來的設定、位移及狀態。 除非需要此持續性，否則建議您刪除這些主題。 您也可能想要刪除在 `my-server.public.todos` 此逐步解說過程中所建立的事件中樞。

## <a name="next-steps"></a>後續步驟

若要深入瞭解適用于 Kafka 的事件中樞，請參閱下列文章：  

- [在事件中樞中鏡像 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md)
- [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md)
- [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md)
- [在 GitHub 上探索範例](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)
- [將 Akka 串流連線到事件中樞](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)