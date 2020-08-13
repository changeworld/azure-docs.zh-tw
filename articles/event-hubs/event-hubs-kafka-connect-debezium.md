---
title: 在 Azure 事件中樞 (Preview) 上整合 Apache Kafka Connect 與變更資料捕獲的 Debezium
description: 本文提供有關如何將 Apache Spark 與「適用於 Kafka 的 Azure 事件中樞」搭配使用的資訊。
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 08/11/2020
ms.openlocfilehash: a11ec882a50d051a34758562ac84dcef5b799f5f
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136799"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview-with-debezium-for-change-data-capture"></a>整合 Azure 事件中樞 (Preview) 上的 Apache Kafka Connect 支援與變更資料捕獲的 Debezium

**Change Data Capture (CDC) **是一種用來追蹤資料庫資料表中資料列層級變更的技術，以回應建立、更新和刪除作業。 [Debezium](https://debezium.io/)是以不同資料庫中可用的變更資料捕獲功能為基礎的分散式平臺 (例如[于 postgresql) 中的邏輯解碼](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)。 它提供了一組[Kafka Connect 連接器](https://debezium.io/documentation/reference/1.2/connectors/index.html)，可在資料庫資料表 () s 中，使用資料列層級的變更，並將它們轉換成事件資料流程，然後再傳送至[Apache Kafka](https://kafka.apache.org/)。

本教學課程將逐步引導您使用適用于 Kafka) 、 [AZURE DB For 于 postgresql](../postgresql/overview.md)和 Debezium 的[Azure 事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-about?WT.mc_id=devto-blog-abhishgu) (，在 Azure 上設定變更資料捕獲型系統。 它會使用[Debezium 于 postgresql 連接器](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)，將資料庫修改從于 postgresql 串流至中的 Kafka 主題 Azure 事件中樞

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立事件中樞命名空間
> * 安裝和設定適用於 PostgreSQL 的 Azure 資料庫
> * 使用 Debezium 于 postgresql 連接器設定並執行 Kafka Connect
> * 測試變更資料捕獲
> *  (選擇性) 使用連接器來取用變更資料事件 `FileStreamSink`

## <a name="pre-requisites"></a>必要條件
若要完成此逐步解說，您將需要：

- Azure 訂用帳戶。 如果您沒有訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。
- Linux/MacOS
- Kafka 版本 (1.1.1 版，Scala 2.11 版)，取得來源為 [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- 請參閱[適用於 Apache Kafka 的事件中樞](./event-hubs-for-kafka-ecosystem-overview.md)簡介文章

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
您需要事件中樞命名空間，才能從任何事件中樞服務傳送和接收。 如需建立命名空間和事件中樞的指示，請參閱[建立事件中樞](event-hubs-create.md)。 請取得事件中樞連接字串和完整網域名稱 (FQDN) 以供稍後使用。 如需相關指示，請參閱[取得事件中樞連接字串](event-hubs-get-connection-string.md)。 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>安裝和設定適用於 PostgreSQL 的 Azure 資料庫
[適用於 PostgreSQL 的 Azure 資料庫](../postgresql/overview.md)是以開放原始碼于 postgresql 資料庫引擎的社區版本為基礎的關係資料庫服務，而且有兩種部署選項可供使用：單一伺服器和超大規模資料庫 (Citus) 。 [遵循這些指示](../postgresql/quickstart-create-server-database-portal.md)，使用 Azure 入口網站建立適用於 PostgreSQL 的 Azure 資料庫伺服器。 

## <a name="setup-and-run-kafka-connect"></a>設定和執行 Kafka Connect
本節將涵蓋下列主題：

- Debezium 連接器安裝
- 設定事件中樞的 Kafka Connect
- 使用 Debezium 連接器啟動 Kafka Connect cluster

### <a name="download-and-setup-debezium-connector"></a>下載並設定 Debezium 連接器
請遵循[Debezium 檔](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector)中的最新指示，下載並設定連接器。

- 下載連接器的外掛程式封存。 例如，若要下載連接器的版本 `1.2.0` ，請使用此連結-https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz
- 將 JAR 檔案解壓縮，並將其複製到[Kafka Connect 外掛程式。路徑](https://kafka.apache.org/documentation/#connectconfigs)。


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

建立于 postgresql 來源連接器的設定檔 (`pg-source-connector.json`) -根據您的 Azure 于 postgresql 實例取代值。

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
> `database.server.name`「屬性」（attribute）是識別並提供受監視之特定于 postgresql 資料庫伺服器/叢集之命名空間的邏輯名稱。 如需詳細資訊，請參閱[Debezium 檔](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)

若要建立連接器的實例，請使用 Kafka Connect REST API 端點：

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

若要檢查連接器的狀態：

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>測試變更資料捕獲
若要查看作用中的變更資料捕獲，您將需要在 Azure 于 postgresql 資料庫中建立/更新/刪除記錄。

一開始請先連接到您的 Azure 于 postgresql 資料庫， (下列範例會使用[psql](https://www.postgresql.org/docs/12/app-psql.html)) 

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

連接器現在應該會進入動作，並將變更資料事件傳送至事件中樞主題，並以下列 na 為 e `my-server.public.todos` ，假設您有做為的 `my-server` 值 `database.server.name` ，而且 `public.todos` 是您要追蹤其變更的資料表 (依據設定 `table.whitelist`) 

**檢查事件中樞主題**

讓我們 introspect 主題的內容，以確定一切如預期般運作。 下列範例使用 [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) ，但您也可以[使用此處所列的任何選項來建立取用者](apache-kafka-developer-guide.md)

使用下列內容建立名為的檔案 `kafkacat.conf` ：

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> `metadata.broker.list` `sasl.password` 依據事件中樞資訊，更新中的和屬性 `kafkacat.conf` 。 

在不同的終端機中，啟動取用者：

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

您應該會看到 JSON 承載，代表在於 postgresql 中產生的變更資料事件，以回應您剛加入至資料表的資料列 `todos` 。 以下是裝載的程式碼片段：


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

事件包含，並 `payload` `schema` 省略其 (以求簡潔) 。 在 `payload` 區段中，請注意建立作業 (`"op": "c"`) 是如何表示的 `"before": null` ，這表示它是新的 `INSERT` `after` 資料列，提供資料列中資料行的值， `source` 提供從這個事件拾取的于 postgresql 實例中繼資料等等。

您也可以使用 update 或 delete 作業來嘗試相同，並 introspect 變更資料事件。 例如，若要更新 (的工作狀態， `configure and install connector` 假設其 `id` 為 `3`) ：

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a> (選擇性) 安裝 FileStreamSink 連接器
既然所有 `todos` 資料表變更都是在事件中樞主題中取得，我們將會使用 Kafka Connect) 預設提供的 FileStreamSink 連接器 (來取用這些事件。

建立連接器的設定檔 (`file-sink-connector.json`) - `file` 根據您的檔案系統取代屬性

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

在設定的輸出接收檔案中插入/更新/刪除資料庫記錄和監視記錄：

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>清除
Kafka Connect 會建立事件中樞主題，以儲存即使在 Connect 叢集關閉後仍會保存下來的設定、位移及狀態。 除非需要此持續性，否則建議您刪除這些主題。 您可能也會想要刪除在 `my-server.public.todos` 此逐步解說期間所建立的事件中樞。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Kafka 的事件中樞，請參閱下列文章：  

- [在事件中樞中鏡像 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md)
- [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md)
- [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md)
- [在 GitHub 上探索範例](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)
- [將 Akka 串流連線到事件中樞](event-hubs-kafka-akka-streams-tutorial.md)
- [適用于 Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
