---
title: 使用 Azure Data Factory 將 Azure SQL Database 資料表遷移至 Azure CosmosDB
description: 從 Azure SQL Database 中取出現有的正規化資料庫架構，並使用 Azure Data Factory 遷移至 Azure CosmosDB 反正規化容器。
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: makromer
ms.openlocfilehash: 3d2ef6fb0cd7af444b9bff755eee4eee70d03d15
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691890"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>將正規化的資料庫架構從 Azure SQL Database 遷移至 Azure CosmosDB 的未正常化容器

本指南將說明如何在 Azure SQL Database 中取得現有的正規化資料庫架構，並將它轉換成 Azure CosmosDB 反正規化架構，以載入至 Azure CosmosDB。

SQL 架構通常會使用第三個一般格式來進行模型化，因而產生標準化的架構，以提供高度的資料完整性和較少的重復資料值。 查詢可以在資料表之間聯結實體以進行讀取。 CosmosDB 已針對高度快速的交易進行優化，並透過反正規化的架構來查詢集合或容器內的資料，並在檔內獨立包含資料。

使用 Azure Data Factory，我們將建立一個管線，使用單一對應資料流程從包含主要和外鍵做為實體關聯性的兩個 Azure SQL Database 正規化資料表中讀取。 ADF 會使用資料流程 Spark 引擎將這些資料表聯結至單一資料流程、將聯結的資料列收集到陣列中，並產生個別的清理檔來插入新的 Azure CosmosDB 容器。

本指南將會在即時建立新的容器，稱為「訂單」，將使用```SalesOrderHeader```標準```SalesOrderDetail``` SQL Server AdventureWorks 範例資料庫中的和資料表。 這些資料表代表所聯結的```SalesOrderID```銷售交易。 每個唯一的詳細資料記錄都有自己```SalesOrderDetailID```的主要索引鍵。 標頭和詳細資料之間的```1:M```關聯性為。 我們會在 ADF ```SalesOrderID```中聯結，然後將每個相關的詳細資料記錄變換為名為「詳細資料」的陣列。

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

1. 選取 [ **+ 新增管線**] 以建立新的管線。

2. 加入資料流程活動

3. 在 [資料流程] 活動中，選取 [**新增對應資料流程**]。

4. 我們將在下方建立此資料流程圖形

![資料流程圖](media/data-flow/cosmosb1.png)

5. 定義 "SourceOrderDetails" 的來源。 針對 [ ```SalesOrderDetail```資料集]，建立指向資料表的新 Azure SQL Database 資料集。

6. 定義 "SourceOrderHeader" 的來源。 針對 [ ```SalesOrderHeader```資料集]，建立指向資料表的新 Azure SQL Database 資料集。

7. 在頂端的來源上，將「衍生的資料行」轉換新增至 "SourceOrderDetails" 之後。 呼叫新的轉換 "轉換"。 我們需要將資料```UnitPrice```行舍入，並將它轉換成 double 資料類型，以進行 CosmosDB。 將公式設定為： ```toDouble(round(UnitPrice,2))```。

8. 新增另一個衍生的資料行，並將它稱為「MakeStruct」。 在這裡，我們將建立階層式結構來保存 details 資料表中的值。 請記住，details 是```M:1```標頭的關聯。 將新結構```orderdetailsstruct```命名為，並以這種方式建立階層，並將每個 subcolumn 設定為傳入資料行名稱：

![建立結構](media/data-flow/cosmosb9.png)

9. 現在，讓我們前往 sales 標頭來源。 新增聯結轉換。 針對右側，選取 [MakeStruct]。 將它設定為 [內部聯結] ```SalesOrderID``` ，並選擇 [針對聯結條件的兩端]。

10. 在您加入的新聯結中，按一下 [資料預覽] 索引標籤，您就可以在此時間點看到您的結果。 您應該會看到所有與詳細資料列聯結的標題資料列。 這是從形成的聯結結果```SalesOrderID```。 接下來，我們會將一般資料列的詳細資訊結合到 details 結構中，並匯總一般資料列。

![Join](media/data-flow/cosmosb4.png)

11. 我們必須先移除不想要的資料行，並確定資料值會符合 CosmosDB 資料類型，才能建立陣列以反正規化這些資料列。

12. 新增 [選取轉換]，並將欄位對應設定如下：

![資料行清除程式」](media/data-flow/cosmosb5.png)

13. 現在讓我們再次轉換貨幣資料行，這次```TotalDue```是。 就像我們在步驟7中所做的一樣，將```toDouble(round(TotalDue,2))```公式設定為：。

14. 在這裡，我們將依據一般索引鍵```SalesOrderID```進行分組，以反正規化資料列。 加入「匯總」轉換，並將 [群組```SalesOrderID```依據] 設定為。

15. 在匯總公式中，加入名為 "details" 的新資料行，並使用此公式來收集先前所建立的結構中的```orderdetailsstruct```值```collect(orderdetailsstruct)```：。

16. 「匯總」轉換只會輸出屬於匯總或 group by 公式一部分的資料行。 因此，我們也必須包含 sales 標頭中的資料行。 若要這麼做，請在相同的匯總轉換中加入資料行模式。 此模式會包含輸出中的所有其他資料行：

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. 請在其他屬性中使用 "this" 語法，讓我們維持相同的資料行名稱，並```first()```使用函數做為匯總：

![Aggregate](media/data-flow/cosmosb6.png)

18. 我們已準備好藉由新增接收轉換來完成遷移流程。 按一下 [資料集] 旁的 [新增]，然後新增指向 CosmosDB 資料庫的 CosmosDB 資料集。 針對集合，我們會將其稱為「訂單」，而且不會有任何架構，而且不會有任何檔，因為它會即時建立。

19. 在 [接收設定] 中， ```\SalesOrderID```將 [資料分割索引鍵] 和 [集合] 動作重新建立。 請確定您的 [對應] 索引標籤看起來像這樣：

![接收設定](media/data-flow/cosmosb7.png)

20. 按一下 [資料預覽] 以確定您看到這些32資料列設定為 [在新的容器中插入為新檔]：

![接收設定](media/data-flow/cosmosb8.png)

如果一切看起來都沒問題，您現在可以建立新的管線，將此資料流程活動新增至該管線並加以執行。 您可以從 [debug] 或 [觸發的執行] 執行。 幾分鐘後，您的 CosmosDB 資料庫中應該會有一個新的反正規化的訂單容器，稱為「訂單」。

## <a name="next-steps"></a>後續步驟

* 使用對應資料流程[轉換](concepts-data-flow-overview.md)來建立其餘的資料流程邏輯。
* 下載本教學課程[的已完成管線範本](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip)，並將範本匯入您的 factory。
