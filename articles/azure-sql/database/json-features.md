---
title: 使用 JSON 資料
description: Azure SQL Database 和 Azure SQL 受控執行個體可讓您以 JavaScript 物件標記法（JSON）標記法來剖析、查詢和格式化資料。
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: b138263ff48d5be24d9453b82eef4a3e9fb0d31b
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85986305"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Azure SQL Database 和 Azure SQL 受控執行個體中的 JSON 功能使用者入門
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 和 Azure SQL 受控執行個體可讓您剖析並查詢以 JavaScript 物件標記法[（JSON）](https://www.json.org/)格式表示的資料，並將您的關聯式資料匯出為 JSON 文字。 可用的 JSON 案例如下：

- 使用 `FOR JSON` 子句[以 JSON 格式將關聯式資料格式化](#formatting-relational-data-in-json-format)。
- [使用 JSON 資料](#working-with-json-data)
- 使用 JSON 純量函式[查詢 JSON 資料](#querying-json-data)。
- 使用 `OPENJSON` 函式[將 JSON 轉換成表格式格式](#transforming-json-into-tabular-format)。

## <a name="formatting-relational-data-in-json-format"></a>以 JSON 格式將關聯式資料格式化

如果您有會從資料庫層擷取資料並以 JSON 格式提供回應的 Web 服務，或是有會接受以 JSON 格式化之資料的用戶端 JavaScript 架構或程式庫，您就可以直接在 SQL 查詢中將資料庫內容格式化為 JSON。 您不再需要撰寫應用程式程式碼，將來自 Azure SQL Database 或 Azure SQL 受控執行個體的結果格式化為 JSON，或包含一些 JSON 序列化程式庫來轉換表格式查詢結果，然後將物件序列化為 JSON 格式。 相反地，您可以使用 FOR JSON 子句來將 SQL 查詢結果格式化為 JSON，並直接在您的應用程式中使用它。

在下列範例中，會透過使用 FOR JSON 子句，將來自 Sales.Customer 資料表的資料列格式化為 JSON：

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

FOR JSON PATH 子句會將查詢的結果格式化為 JSON 文字。 資料行名稱會作為索引鍵，而儲存格值則會以 JSON 值的形式產生︰

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

結果集會格式化為 JSON 陣列，其中每個資料列皆格式化為個別的 JSON 物件。

PATH 表示您可以在資料行別名中使用點標記法來自訂 JSON 結果的輸出格式。 下列查詢會變更輸出 JSON 格式中 "CustomerName" 索引鍵的名稱 ，並將電話及傳真號碼放入 "Contact" 子物件中︰

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

此查詢的輸出看起來會像這樣：

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

在此範例中，我們透過指定 [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) 選項，傳回了單一 JSON 物件而不是陣列。 如果您知道您要傳回單一物件來作為查詢結果，就可以使用此選項。

FOR JSON 子句的主要價值在於，它可讓您從資料庫傳回格式化為巢狀 JSON 物件或陣列的複雜階層式資料。 下列範例說明如何將屬於 `Customer` 之 `Orders` 資料表中的資料列包含為 `Orders` 的巢狀陣列：

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

您可以不傳送個別查詢來取得 Customer 資料，然後再擷取相關 Orders 清單，而是透過單一查詢來取得所有必要的資料，如下列範例輸出所示：

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>使用 JSON 資料

如果您沒有嚴格結構化的資料，如果您有複雜的子物件、陣列或階層式資料，或如果您的資料結構會隨時間演變，則 JSON 格式可協助您表現任何複雜的資料結構。

JSON 是一種文字格式，可以像 Azure SQL Database 和 Azure SQL 受控執行個體中的任何其他字串類型一樣使用。 您可以用標準 NVARCHAR 形式來傳送或儲存 JSON 資料：

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

在此範例中，是透過使用 NVARCHAR(MAX) 類型來表現所使用的 JSON 資料。 您可以使用標準 Transact-SQL 語法，將 JSON 插入此資料表中或提供來作為預存程序的引數，如下列範例所示：

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

在 Azure SQL Database 和 Azure SQL 受控執行個體中使用字串資料的任何用戶端語言或程式庫也會使用 JSON 資料。 JSON 可以儲存在任何支援 NVARCHAR 類型的資料表中，例如記憶體最佳化資料表或由系統控制版本的資料表。 JSON 不會導入任何條件約束，不論是用戶端程式碼中還是在資料庫層中的條件約束。

## <a name="querying-json-data"></a>查詢 JSON 資料

如果您有格式化為 JSON 的資料儲存在 Azure SQL 資料表中，JSON 函數可讓您在任何 SQL 查詢中使用此資料。

Azure SQL Database 和 Azure SQL 受控執行個體中提供的 JSON 函式可讓您將格式化為 JSON 的資料視為任何其他 SQL 資料類型。 您可以輕鬆地從 JSON 文字中擷取值，然後在任何查詢中使用 JSON 資料︰

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

JSON_VALUE 函數會從儲存在 Data 資料行的 JSON 文字中擷取值。 此函數會使用類似 JavaScript 的路徑來參考所要擷取的 JSON 文字中的值。 所擷取的值可以在 SQL 查詢的任何部分中使用。

JSON_QUERY 函數類似於 JSON_VALUE。 與 JSON_VALUE 不同，此函數會擷取複雜的子物件，例如置於 JSON 文字中的陣列或物件。

JSON_MODIFY 函數可讓您指定 JSON 文字中應更新的值路徑，以及將覆寫舊值的新值。 如此一來，您便可以輕鬆更新 JSON 文字，而不需重新剖析整個結構。

由於 JSON 是以標準文字儲存，因此無法保證儲存在文字資料行中的值格式會正確。 您可以使用標準的 Azure SQL Database 檢查條件約束和 ISJSON 函數，來確認儲存在 JSON 資料行中的文字是否格式正確︰

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

如果輸入的文字是格式正確的 JSON，ISJSON 函數就會傳回值 1。 在每次插入或更新 JSON 資料行時，這個條件約束都會確認新的文字值不是格式錯誤的 JSON。

## <a name="transforming-json-into-tabular-format"></a>將 JSON 轉換成表格式格式

Azure SQL Database 和 Azure SQL 受控執行個體也可讓您將 JSON 集合轉換成表格式格式，並載入或查詢 JSON 資料。

OPENJSON 是一個資料表值函數，可剖析 JSON 文字、找出 JSON 物件陣列、逐一查看陣列的元素，然後在輸出結果中為每個陣列元素傳回一個資料列。

![JSON 表格式](./media/json-features/image_2.png)

在上述範例中，我們可以指定要在哪裡尋找應該開啟的 JSON 陣列 (在 $.Orders 路徑中)、應該傳回哪些資料行作為結果，以及要在哪裡尋找將以資料格形式傳回的 JSON 值。

我們可以將 @orders 變數中的 JSON 陣列轉換成一組資料列、分析此結果集，或將資料列插入標準資料表中︰

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

我們可以剖析採用 JSON 陣列格式並作為參數提供給預存程序的訂單集合，然後將它插入 Orders 資料表中。

## <a name="next-steps"></a>後續步驟

若要了解如何將 JSON 整合到您的應用程式中，請參閱下列資源︰

若要了解將 JSON 整合到您應用程式中的各種案例，請參閱這段 [Channel 9 影片](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds)中的示範，或從 [JSON 部落格文章](https://docs.microsoft.com/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases)中尋找符合您使用案例的情況。


