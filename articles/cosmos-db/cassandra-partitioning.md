---
title: Azure Cosmos DB Cassandra API 中的資料分割
description: 深入瞭解 Azure Cosmos DB Cassandra API 中的資料分割
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 5f159ffcea0aa88f354ae503be96a5c571c10adb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806827"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 中的資料分割

本文說明資料分割在 Azure Cosmos DB Cassandra API 中的運作方式。 

Cassandra API 使用資料分割來調整 keyspace 中的個別資料表，以符合您應用程式的效能需求。 資料分割是根據與資料表中每一筆記錄相關聯的資料分割索引鍵的值來形成。 分割區中的所有記錄都有相同的資料分割索引鍵值。 Azure Cosmos DB 透明並自動管理實體資源的分割區位置，以有效率地滿足資料表的擴充性和效能需求。 隨著應用程式的輸送量和儲存體需求增加，Azure Cosmos DB 會在更多的實體機器之間移動和平衡資料。

從開發人員的觀點來看，資料分割的運作方式與在原生[Apache Cassandra](https://cassandra.apache.org/)中的 Azure Cosmos DB Cassandra API 相同。 不過，幕後會有一些差異。 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Apache Cassandra 與 Azure Cosmos DB 之間的差異

在 Azure Cosmos DB 中，儲存磁碟分割的每一部電腦本身都稱為[實體分割](partition-data.md#physical-partitions)區。 實體分割區類似于虛擬機器;專用的計算單位或實體資源集。 儲存在此計算單位上的每個分割區在 Azure Cosmos DB 中稱為[邏輯分割](partition-data.md#logical-partitions)區。 如果您已經熟悉 Apache Cassandra，您可以將邏輯分割區視為 Cassandra 中一般資料分割的相同方式。 

Apache Cassandra 會針對可儲存在資料分割中的資料大小，建議 100 MB 的限制。 Azure Cosmos DB 的 Cassandra API 允許每個邏輯分割區最多 20 GB，且每個實體分割區最多可30GB 資料。 在 Azure Cosmos DB 中，與 Apache Cassandra 不同的是，實體分割區中可用的計算容量會使用稱為「[要求單位](request-units.md)」的單一計量來表示，這可讓您以每秒的要求（讀取或寫入），而不是核心、記憶體或 IOPS 來思考您的工作負載。 一旦您瞭解每個要求的成本，這可以讓容量規劃更為直接。 每個實體分割區最多可以有 10000 ru 的可用計算。 如需瞭解擴充性選項的詳細資訊，請參閱 Cassandra API 中的[彈性延展](manage-scale-cassandra.md)文章。 

在 Azure Cosmos DB 中，每個實體分割區都是由一組複本（也稱為複本集）所組成，每個資料分割至少有4個複本。 這與 Apache Cassandra 相較之下，可能會將複寫因數設定為1。 不過，如果只有一個具有資料的節點停止運作，這會導致低可用性。 在 Cassandra API 一律會有一個複寫因數4（仲裁3）。 Azure Cosmos DB 會自動管理複本集，而這些都需要使用 Apache Cassandra 中的各種工具來維護。 

Apache Cassandra 具有權杖的概念，這是資料分割索引鍵的雜湊。 權杖是以 murmur3 64 位元組雜湊為基礎，其值範圍介於-2 ^ 63 到-2 ^ 63-1 之間。 此範圍通常稱為 Apache Cassandra 中的「權杖環」。 權杖環形會散發至權杖範圍，而這些範圍會劃分成原生 Apache Cassandra 叢集中存在的節點。 Azure Cosmos DB 的資料分割會以類似的方式執行，但它會使用不同的雜湊演算法，並具有較大的權杖環形。 


## <a name="primary-key"></a>主索引鍵

Cassandra API 中的所有資料表都必須具有 `primary key` 已定義的。 主要金鑰的語法如下所示：

```shell
column_name cql_type_definition PRIMARY KEY
```

假設我們想要建立一個使用者資料表來儲存不同使用者的訊息：

```shell
CREATE TABLE uprofile.user ( 
   id UUID PRIMARY KEY, 
   user text,  
   message text);
```

在此設計中，我們已將 `id` 欄位定義為主要索引鍵。 主鍵會當做資料表中記錄的識別碼，而且在 Azure Cosmos DB 中也會用來做為資料分割索引鍵。 如果主要索引鍵是以先前所述的方式定義，則每個分割區中只會有一筆記錄。 這會在將資料寫入資料庫時產生完全水準和可擴充的散發，這適用于索引鍵/值查閱使用案例。 應用程式應該在每次從資料表讀取資料時提供主要金鑰，以達到最大的讀取效能。 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="資料分割" border="false":::


## <a name="compound-primary-key"></a>複合主鍵

Apache Cassandra 也有的概念 `compound keys` 。 複合是 `primary key` 由一個以上的資料行所組成; 第一個資料行是 `partition key` ，而任何其他資料行都是 `clustering keys` 。 的語法 `compound primary key` 如下所示：

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

假設我們想要變更上述設計，讓您可以有效率地抓取指定使用者的訊息：

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

在此設計中，我們現在會將定義為數據分割索引鍵 `user` ，以及 `id` 做為叢集索引鍵。 您可以視需要定義任意數目的叢集索引鍵，但是叢集索引鍵的每個值（或值的組合）必須是唯一的，才能讓多個記錄新增至相同的分割區，例如：

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

傳回資料時，會依照 Apache Cassandra 中的預期，依照叢集索引鍵來排序：

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="資料分割":::

透過這種方式模型化的資料，可以將多筆記錄指派給每個分割區（依使用者分組）。 因此，我們可以發出一個有效率地路由的查詢 `partition key` （在此案例中為 `user` ），以取得指定使用者的所有訊息。 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="資料分割" border="false":::


## <a name="composite-partition-key"></a>複合資料分割索引鍵

複合資料分割索引鍵的工作方式基本上與複合索引鍵相同，不同之處在于您可以將多個資料行指定為複合資料分割索引鍵。 複合資料分割索引鍵的語法如下所示：

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
例如，您可以使用下列程式，其中和的唯一組合 `firstname` `lastname` 會形成資料分割索引鍵，而是叢集索引 `id` 鍵：

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure Cosmos DB 中的資料分割和水準調整](partition-data.md)。
* 瞭解[Azure Cosmos DB 中布建的輸送量](request-units.md)。
* 深入瞭解[Azure Cosmos DB 中的全域散發](distribute-data-globally.md)。
