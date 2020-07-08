---
title: Azure Cosmos DB Cassandra API 帳戶中編制索引
description: 瞭解次要索引在 Azure Azure Cosmos DB Cassandra API 帳戶中的運作方式。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80758023"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 中的次要索引

Azure Cosmos DB 中的 Cassandra API 會利用基礎的索引基礎結構來公開平臺中所固有的索引強度。 不過，不同于核心 SQL API，Azure Cosmos DB 中的 Cassandra API 預設不會編制所有屬性的索引。 相反地，它支援次要索引編制，以便在某些屬性上建立索引，其行為方式與 Apache Cassandra 相同。  

一般而言，不建議對未分割的資料行執行篩選查詢。 您必須明確使用 [允許篩選語法]，這會導致作業無法正常執行。 在 Azure Cosmos DB 您可以在較低的基數屬性上執行這類查詢，因為它們會在分割區之間展開，以取得結果。

不建議在經常更新的資料行上建立索引。 當您定義資料表時，請謹慎建立索引。 這可確保資料和索引處於一致的狀態。 如果您在現有的資料上建立新的索引，目前您無法追蹤資料表的索引進度變更。 如果您需要追蹤這種作業的進度，您必須透過[支援票證]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來要求進度變更。


> [!NOTE]
> 下列物件不支援次要索引：
> - 資料類型，例如凍結的集合類型、decimal 和 variant 類型。
> - 靜態資料行
> - 叢集索引鍵

## <a name="indexing-example"></a>編制索引範例

首先，在 CQL shell 提示字元上執行下列命令，以建立範例 keyspace 和資料表：

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

然後，使用下列命令插入範例使用者資料：

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

如果您嘗試執行下列語句，將會遇到一個錯誤，要求您使用 `ALLOW FILTERING` ： 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

雖然 Cassandra API 支援如上一節所述的允許篩選，但並不建議您這麼做。 您應該改為在中建立索引，如下列範例所示：

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
在 "lastname" 欄位上建立索引之後，您現在可以成功執行先前的查詢。 使用 Azure Cosmos DB 中的 Cassandra API 時，您不需要提供索引名稱。 使用格式的預設索引 `tablename_columnname_idx` 。 例如， ` t1_lastname_idx` 是上一個資料表的索引名稱。

## <a name="dropping-the-index"></a>卸載索引 
您必須知道要卸載索引的索引名稱。 執行 `desc schema` 命令以取得資料表的描述。 此命令的輸出包含格式的索引名稱 `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` 。 接著，您可以使用索引名稱來卸載索引，如下列範例所示：

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>後續步驟
* 瞭解[自動編制索引](index-overview.md)在 Azure Cosmos DB 中的運作方式
* [Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能](cassandra-support.md)
