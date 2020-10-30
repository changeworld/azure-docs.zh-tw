---
title: 使用 Azure Data Factory 將 Azure SQL Database 資料表遷移至 Azure CosmosDB
description: 從 Azure SQL Database 取得現有的正規化資料庫架構，並使用 Azure Data Factory 遷移至 Azure CosmosDB 反正規化容器。
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: makromer
ms.openlocfilehash: 5d93cb49c77a4c8164a8b4e9bca349a805f39678
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041630"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>將正規化資料庫結構從 Azure SQL Database 遷移至 Azure CosmosDB 的反正規化容器

本指南將說明如何在 Azure SQL Database 中採用現有的正規化資料庫架構，並將其轉換成 Azure CosmosDB 反正規化架構以載入 Azure CosmosDB。

SQL 架構通常會使用第三種一般形式來建立模型，產生標準化的架構，以提供高層級的資料完整性，並減少重複的資料值。 查詢可以在資料表之間聯結實體以進行讀取。 CosmosDB 已針對在集合或容器內的高度快速交易和查詢進行優化，並透過檔中自主資料的反正規化架構來進行查詢。

使用 Azure Data Factory，我們將建立使用單一對應資料流程的管線，以從包含主鍵和外鍵作為實體關聯性的兩個 Azure SQL Database 正規化資料表中讀取。 ADF 會使用資料流程 Spark 引擎將這些資料表聯結至單一資料流程、將聯結的資料列收集至陣列，並產生個別的清理檔以插入新的 Azure CosmosDB 容器。

本指南將會立即建立稱為「訂單」的新容器，此容器將使用 ```SalesOrderHeader``` ```SalesOrderDetail``` 標準 SQL Server AdventureWorks 範例資料庫中的和資料表。 這些資料表代表所聯結的銷售交易 ```SalesOrderID``` 。 每個唯一的詳細資料記錄都有自己的主要索引鍵 ```SalesOrderDetailID``` 。 標頭和詳細資料之間的關聯性為 ```1:M``` 。 我們將在 ```SalesOrderID``` ADF 中加入，然後將每個相關的詳細資料記錄轉換為稱為「詳細資料」的陣列。

本指南的代表性 SQL 查詢如下：

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

產生的 CosmosDB 容器會將內部查詢內嵌至單一檔，如下所示：

![集合](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>建立管線

1. 選取 [ **+ 新增管線** ] 以建立新的管線。

2. 新增資料流程活動

3. 在 [資料流程] 活動中，選取 [ **新增對應資料流程** ]。

4. 我們將在下方建立此資料流程圖

![資料流程圖](media/data-flow/cosmosb1.png)

5. 定義 "SourceOrderDetails" 的來源。 針對資料集，建立指向資料表的新 Azure SQL Database 資料集 ```SalesOrderDetail``` 。

6. 定義 "SourceOrderHeader" 的來源。 針對資料集，建立指向資料表的新 Azure SQL Database 資料集 ```SalesOrderHeader``` 。

7. 在最上層來源上，將「衍生的資料行」轉換新增至 "SourceOrderDetails" 之後。 呼叫新的轉換 "轉換"。 我們必須將資料 ```UnitPrice``` 行四捨五入，並將其轉換成 double 資料類型，以進行 CosmosDB。 將公式設定為： ```toDouble(round(UnitPrice,2))``` 。

8. 新增另一個衍生的資料行，並將其命名為 "MakeStruct"。 我們將在這裡建立階層式結構，以保存詳細資料資料表中的值。 請記住，詳細資料是 ```M:1``` 與標頭相關的詳細資料。 ```orderdetailsstruct```以這種方式命名新的結構並建立階層，將每個 subcolumn 設定為傳入的資料行名稱：

![建立結構](media/data-flow/cosmosb9.png)

9. 現在，讓我們移至 sales 標頭來源。 新增聯結轉換。 針對右邊選取 [MakeStruct]。 將它設定為 [內部聯結]，並選擇 ```SalesOrderID``` [聯結] 條件的兩側。

10. 在您加入的新聯結中，按一下 [資料預覽] 索引標籤，讓您可以看到目前為止的結果。 您應該會看到所有與詳細資料列聯結的標頭資料列。 這是從形成聯結的結果 ```SalesOrderID``` 。 接下來，我們會將一般資料列中的詳細資料結合到詳細資料結構，並匯總通用資料列。

![Join](media/data-flow/cosmosb4.png)

11. 我們必須先移除不必要的資料行，並確定資料值符合 CosmosDB 資料類型，才可以建立陣列來反正規化這些資料列。

12. 接下來，加入「選取」轉換，並將欄位對應設定如下：

![資料行清除程式](media/data-flow/cosmosb5.png)

13. 現在讓我們再次轉換貨幣資料行，這次 ```TotalDue``` 。 就像我們在步驟7中所做的一樣，將公式設定為： ```toDouble(round(TotalDue,2))``` 。

14. 以下是我們將依據一般索引鍵分組來反正規化資料列的位置 ```SalesOrderID``` 。 加入「匯總」轉換，並將 group by 設定為 ```SalesOrderID``` 。

15. 在匯總公式中，加入名為「詳細資料」的新資料行，並使用此公式來收集先前建立的結構中所呼叫的值 ```orderdetailsstruct``` ： ```collect(orderdetailsstruct)``` 。

16. 「匯總」轉換只會輸出屬於匯總或依公式分組的資料行。 因此，我們也必須包含 sales 標頭中的資料行。 若要這樣做，請在相同的匯總轉換中加入資料行模式。 此模式會在輸出中包含所有其他資料行：

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. 在其他屬性中使用 "this" 語法，以維護相同的資料行名稱，並使用函 ```first()``` 式做為匯總：

![Aggregate](media/data-flow/cosmosb6.png)

18. 我們已準備好透過新增接收轉換來完成遷移流程。 按一下資料集旁邊的 [新增]，然後新增指向 CosmosDB 資料庫的 CosmosDB 資料集。 針對此集合，我們會將它稱為「訂單」，而且它將不會有任何架構和檔，因為它會在即時建立。

19. 在 [接收設定] 中，將 [資料分割索引鍵 ```\SalesOrderID``` ] 和 [集合] 動作重新建立。 請確定您的 [對應] 索引標籤看起來像這樣：

![螢幕擷取畫面：顯示 [對應] 索引標籤。](media/data-flow/cosmosb7.png)

20. 按一下 [資料預覽] 以確定您看到這些32資料列已設定為在新的容器中插入新的檔：

![螢幕擷取畫面：顯示 [資料預覽] 索引標籤。](media/data-flow/cosmosb8.png)

如果一切看起來都沒問題，您現在可以開始建立新的管線，將此資料流程活動新增至該管線，然後執行它。 您可以從 debug 或觸發的執行執行。 幾分鐘後，您的 CosmosDB 資料庫中應該會有一個名為 "orders" 的新反正規化容器。

## <a name="next-steps"></a>後續步驟

* 使用對應資料流程 [轉換](concepts-data-flow-overview.md)來建立其餘的資料流程邏輯。
* 下載此教學課程[的已完成管線範本](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip)，然後將範本匯入您的 factory。
