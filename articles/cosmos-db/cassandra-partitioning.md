---
title: Azure Cosmos DB Cassandra API 中的資料分割
description: 瞭解 Azure Cosmos DB Cassandra API 中的資料分割
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: f12919cd35441c6c198269e2f79c705c1d304acd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92278817"
---
# <a name="partitioning-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 中的資料分割

本文說明資料分割在 Azure Cosmos DB Cassandra API 中的運作方式。 

Cassandra API 使用資料分割來調整 keyspace 中的個別資料表，以符合應用程式的效能需求。 分割區是根據與資料表中每一筆記錄相關聯的資料分割索引鍵的值所組成。 分割區中的所有記錄都有相同的資料分割索引鍵值。 Azure Cosmos DB 透明且自動管理跨實體資源的資料分割位置，以有效率地滿足資料表的擴充性和效能需求。 隨著應用程式的輸送量和儲存體需求增加，Azure Cosmos DB 會在多個實體機器之間移動和平衡資料。

從開發人員的觀點來看，資料分割的運作方式與在原生 [Apache Cassandra](https://cassandra.apache.org/)中 Azure Cosmos DB Cassandra API 相同。 不過，幕後有一些差異。 


## <a name="differences-between-apache-cassandra-and-azure-cosmos-db"></a>Apache Cassandra 與 Azure Cosmos DB 之間的差異

在 Azure Cosmos DB 中，儲存磁碟分割的每一部機器本身都稱為 [實體分割](partitioning-overview.md#physical-partitions)區。 實體分割區類似于虛擬機器;專用的計算單位或一組實體資源。 此計算單位上儲存的每個分割區都稱為 Azure Cosmos DB 中的 [邏輯分割](partitioning-overview.md#logical-partitions) 區。 如果您已經熟悉 Apache Cassandra，您可以像是在 Cassandra 中想像一般資料分割一樣的方式來考慮邏輯分割區。 

Apache Cassandra 針對可儲存在分割區中的資料大小，建議 100-MB 的限制。 Azure Cosmos DB 的 Cassandra API 允許每個邏輯分割區最多 20 GB，每個實體分割區最多可30GB 資料。 在 Azure Cosmos DB 中，與 Apache Cassandra 不同的是，實體分割區中可用的計算容量是以單一計量（稱為 [要求單位](request-units.md)）來表示，而這可讓您將工作負載視為每秒) 的要求 (讀取或寫入，而不是核心、記憶體或 IOPS。 當您瞭解每個要求的成本時，這樣做可以更快地進行容量規劃。 每個實體分割區最多可以有10000個 ru 的可用計算。 若要深入瞭解擴充性選項，請閱讀我們的 Cassandra API 的 [彈性延展](manage-scale-cassandra.md) 文章。 

在 Azure Cosmos DB 中，每個實體分割區都包含一組複本（也稱為「複本集」），每個資料分割至少有4個複本。 這與 Apache Cassandra 相反，因為可能會將複寫因數設定為1。 但是，如果有資料的唯一節點停止運作，這會導致低可用性。 在 Cassandra API 中，一律會有4個 (仲裁的複寫因數 3) 。 Azure Cosmos DB 會自動管理複本集，而這些都必須使用 Apache Cassandra 中的各種工具進行維護。 

Apache Cassandra 具有權杖的概念，也就是資料分割索引鍵的雜湊。 這些權杖是以 murmur3 64 位元組雜湊為基礎，其值範圍從-2 ^ 63 到-2 ^ 63-1。 此範圍在 Apache Cassandra 中通常稱為「權杖環形」。 權杖通道會分散至權杖範圍，而這些範圍會在原生 Apache Cassandra 叢集中的節點之間分割。 Azure Cosmos DB 的資料分割會以類似的方式執行，但它會使用不同的雜湊演算法，且具有較大的內部 token 環形。 不過，在外部，我們會公開與 Apache Cassandra 相同的權杖範圍，也就是-2 ^ 63 到-2 ^ 63-1。


## <a name="primary-key"></a>主索引鍵

Cassandra API 中的所有資料表都必須 `primary key` 定義。 主要索引鍵的語法如下所示：

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

在此設計中，我們已將 `id` 欄位定義為主鍵。 主鍵會當做資料表中記錄的識別碼，而且也會當做 Azure Cosmos DB 中的資料分割索引鍵使用。 如果主鍵是以先前描述的方式定義，則每個分割區中只會有一筆記錄。 這會在將資料寫入資料庫時產生完全水準和可調整的散發，而且非常適合用於索引鍵/值查閱使用案例。 應用程式應該會在每次讀取資料表中的資料時提供主鍵，以將讀取效能最大化。 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning.png" alt-text="分區" border="false":::


## <a name="compound-primary-key"></a>複合主鍵

Apache Cassandra 也有的概念  `compound keys` 。 複合是 `primary key` 由一個以上的資料行所組成; 第一個資料行是 `partition key` ，而任何其他資料行則為 `clustering keys` 。 的語法如下 `compound primary key` 所示：

```shell
PRIMARY KEY (partition_key_column_name, clustering_column_name [, ...])
```

假設我們想要變更上述設計，使其能夠有效率地取出指定使用者的訊息：

```shell
CREATE TABLE uprofile.user (
   user text,  
   id int, 
   message text, 
   PRIMARY KEY (user, id));
```

在此設計中，我們現在會將定義 `user` 為數據分割索引鍵，以及 `id` 做為叢集索引鍵。 您可以視需要定義任意數目的叢集索引鍵，但每個值 (或叢集索引鍵) 的值組合，都必須是唯一的，才能導致將多筆記錄新增至相同的分割區，例如：

```shell
insert into uprofile.user (user, id, message) values ('theo', 1, 'hello');
insert into uprofile.user (user, id, message) values ('theo', 2, 'hello again');
```

傳回資料時，會依照下列方式，在 Apache Cassandra 中依照叢集索引鍵來排序：

:::image type="content" source="./media/cassandra-partitioning/select-from-pk.png" alt-text="分區":::

使用以這種方式建立模型的資料時，可以將多筆記錄指派給每個分割區（依使用者分組）。 如此一來，我們就可以在此情況下發出由 (有效率地路由傳送的查詢 `partition key` ， `user`) 取得指定使用者的所有訊息。 

:::image type="content" source="./media/cassandra-partitioning/cassandra-partitioning2.png" alt-text="分區" border="false":::


## <a name="composite-partition-key"></a>複合分割區索引鍵

複合資料分割索引鍵的運作方式基本上與複合索引鍵相同，不同之處在于您可以將多個資料行指定為複合資料分割索引鍵。 複合分割區索引鍵的語法如下所示：

```shell
PRIMARY KEY (
   (partition_key_column_name[, ...]), 
    clustering_column_name [, ...]);
```
例如，您可以使用下列程式，其中的唯一組合 `firstname` `lastname` 會形成分割區索引鍵，而是叢集索引 `id` 鍵：

```shell
CREATE TABLE uprofile.user ( 
   firstname text, 
   lastname text,
   id int,  
   message text, 
   PRIMARY KEY ((firstname, lastname), id) );
```

## <a name="next-steps"></a>後續步驟

* 瞭解 [Azure Cosmos DB 中的資料分割和水準調整](partitioning-overview.md)。
* 瞭解 [Azure Cosmos DB 中布建的輸送量](request-units.md)。
* 瞭解 [Azure Cosmos DB 中的全域散發](distribute-data-globally.md)。
