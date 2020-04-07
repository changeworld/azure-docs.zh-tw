---
title: Azure 宇宙 DB Cassandra API 帳戶中的索引
description: 瞭解輔助索引在 Azure Azure Cosmos DB Cassandra API 帳戶中的工作原理。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758023"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure 宇宙 DB Cassandra API 中的輔助索引

Azure Cosmos DB 中的 Cassandra API 利用基礎索引基礎結構來公開平臺中固有的索引強度。 但是,與核心 SQL API 不同,Azure Cosmos DB 中的 Cassandra API 預設情況下不會索引所有屬性。 相反,它支援輔助索引來創建對某些屬性的索引,這些屬性的工作方式與 Apache Cassandra 相同。  

通常,不建議對未分區的列執行篩選器查詢。 您必須顯式使用 ALLOW 篩選語法,這將導致操作可能無法很好地執行。 在 Azure Cosmos DB 中,您可以在低基數屬性上運行此類查詢,因為它們跨分區扇出以檢索結果。

不建議在頻繁更新的列上創建索引。 在定義表時,最好創建索引。 這可確保數據和索引處於一致狀態。 如果在現有數據上創建新索引,則當前無法跟蹤表的索引進度更改。 如果需要跟蹤此操作的進度,必須通過[支援票證]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)請求進度更改。


> [!NOTE]
> 以下物件不支援輔助索引:
> - 數據類型,如凍結集合類型、小數次和變體類型。
> - 靜態列
> - 叢集鍵

## <a name="indexing-example"></a>索引範例

首先,透過 CQL shell 提示符上執行以下指令來建立一個範例鍵空間和表:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

然後,使用以下指令插入範例使用者資料:

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

如果嘗試執行以下文句,則會遇到錯誤,要求您使用`ALLOW FILTERING`: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

儘管卡桑德拉 API 支援 ALLOW 篩選(如上一節所述),但不建議這樣做。 您應該在 中建立索引,如以下範例的範例 :

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
在"姓氏"欄位上創建索引後,現在可以成功運行上一個查詢。 使用 Azure Cosmos DB 中的 Cassandra API,您不必提供索引名稱。 使用具有格式`tablename_columnname_idx`的預設索引。 例如,` t1_lastname_idx`是上一表的索引名稱。

## <a name="dropping-the-index"></a>刪除索引 
您需要知道索引名稱是刪除索引的。 運行該`desc schema`指令以獲取表的說明。 此命令的輸出包括格式`CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`中的索引名稱。 然後可以使用索引名稱刪除索引,如以下範例所示:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>後續步驟
* 瞭解[自動索引](index-overview.md)在 Azure Cosmos DB 中的工作原理
* [Azure 宇宙 DB 卡桑德拉 API 支援的 Apache 卡桑德拉功能](cassandra-support.md)
