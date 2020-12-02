---
title: Azure Data Lake 的 U-SQL 使用者定義結合器可程式性指南
description: 深入瞭解 < SQL UDO 可程式性指南-使用者定義的結合器。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512551"
---
# <a name="use-user-defined-combiner"></a>使用使用者定義的結合器

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO：使用者定義的結合器
使用者定義結合器 (簡稱 UDC) 可讓您根據自訂邏輯結合左邊和右邊資料列集中的資料列。 使用者定義結合器會與 COMBINE 運算式搭配使用。

## <a name="how-to-define-and-use-user-defined-combiner"></a>如何定義及使用使用者定義的結合器

結合器會使用 COMBINE 運算式來叫用，以提供關於輸入資料列集、群組資料行、預期的結果結構描述和其他資訊的必要資訊。

若要在基底 U-SQL 指令碼中呼叫結合器，我們可以使用下列語法：

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

如需詳細資訊，請參閱 [COMBINE 運算式 (U-SQL)](/u-sql/statements-and-expressions/combine-expression)。

若要定義使用者定義結合器，我們必須以 [`SqlUserDefinedCombiner`] 屬性 (這是使用者定義結合器之定義的選擇性屬性) 建立 `ICombiner` 介面。

基底 `ICombiner` 類別定義：

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

`ICombiner` 介面的自訂實作應該包含 `IEnumerable<IRow>` 結合覆寫的定義。

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

**SqlUserDefinedCombiner** 表示類型應該註冊為使用者定義結合器。 此類別無法獲得繼承。

**SqlUserDefinedCombiner** 用來定義合併程式模式屬性。 它是使用者定義結合器定義的選擇性屬性。

CombinerMode     Mode

CombinerMode 列舉可以採用下列值︰

* 完整 (0) 每個輸出資料列可能相依於所有具有相同金鑰值的左邊和右邊輸入資料列。

* 左邊 (1) 每個輸出資料列相依於左邊的單一輸入資料列 (並可能相依於所有具有相同金鑰值的右邊資料列)。

* 右邊 (2) 每個輸出資料列相依於右邊的單一輸入資料列 (並可能相依於所有具有相同金鑰值的左邊資料列)。

* 內部 (3) 每個輸出資料列相依於具有相同值的左邊和右邊單一輸入資料列。

範例：[`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`]


主要的可程式性物件為：

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

輸入資料列集會傳遞做為介面的「左邊」和「右邊」 `IRowset` 類型。 這兩個資料列集必須列舉以進行處理。 您僅可以允許每個介面列舉一次，因此我們必須在必要時加以列舉和快取。

基於快取目的，我們可以建立 \<T\> 記憶體結構的清單類型，作為 LINQ 查詢執行的結果，特別是列出<`IRow`>。 列舉期間也可以使用匿名資料類型。

如需 LINQ 查詢的詳細資訊，請參閱 [Linq 查詢簡介 (c # ) ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) ， [以及 ienumerable 介面的詳細 \<T\> ](/dotnet/api/system.collections.generic.ienumerable-1) 資訊，以取得 ienumerable 介面的詳細資訊 \<T\> 。

若要從傳入的 `IRowset` 取得實際資料值，我們可以使用 `IRow` 介面的 Get() 方法。

```csharp
mycolumn = row.Get<int>("mycolumn")
```

呼叫 `IRow` 結構描述方法即可判斷個別資料行名稱。

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

或者，使用結構描述資料行名稱︰

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

LINQ 的一般列舉看起來如下所示︰

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

列舉這兩個資料列集之後，我們將所有資料列執行迴圈。 針對資料列集左邊的每個資料列，我們要尋找符合我們合併程式條件的所有資料列。

輸出值必須使用 `IUpdatableRow` 輸出進行設定。

```csharp
output.Set<int>("mycolumn", mycolumn)
```

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的輸出。

以下是結合器範例：

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

在此使用案例中，我們會建置零售商的分析報告。 目標是要尋找成本超過 20,000 美元，且特定時間範圍內透過網站的銷售速度快過一般零售商的所有產品。

以下是基底 U-SQL 指令碼。 您可以比較一般 JOIN 與結合器之間的邏輯︰

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

使用者定義結合器可以呼叫做為套用器物件的新執行個體︰

```csharp
USING new MyNameSpace.MyCombiner();
```


或透過叫用包裝函式 Factory 方法來呼叫：

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)