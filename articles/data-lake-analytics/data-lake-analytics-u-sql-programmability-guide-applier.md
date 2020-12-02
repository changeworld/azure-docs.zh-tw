---
title: Azure Data Lake 的 U SQL 使用者定義套用器可程式性指南
description: 深入瞭解 U SQL UDO 可程式性指南-使用者定義套用器。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512536"
---
# <a name="use-user-defined-applier"></a>使用使用者定義套用器 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO：使用者定義套用器
U-SQL 使用者定義套用器可讓您針對查詢的外部資料表運算式所傳回的每個資料列，叫用自訂 C# 函式。 系統會針對左邊輸入中的每個資料列評估右邊輸入，然後結合所產生的資料列來產生最終輸出。 APPLY 運算子所產生的資料行清單是左邊輸入和右邊輸入之資料行集的組合。


## <a name="how-to-define-and-use-user-defined-applier"></a>如何定義和使用使用者定義套用器
U-SQL SELECT 運算式中會叫用使用者定義套用器。

使用者定義套用器的典型呼叫看起來如下所示︰

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

如需在 SELECT 運算式中使用套用器的詳細資訊，請參閱[從 CROSS APPLY 和 OUTER APPLY 選取的 U-SQL SELECT](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)。

使用者定義套用器的基底類別定義如下所示：

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

若要定義使用者定義套用器，我們必須以 [`SqlUserDefinedApplier`] 屬性 (這是使用者定義套用器之定義的選擇性屬性) 建立 `IApplier` 介面。

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* 會針對外部資料表的每個資料列呼叫 Apply。 它會傳回 `IUpdatableRow` 輸出資料列集。
* 建構函式類別可用來將參數傳遞至使用者定義套用器。

**SqlUserDefinedApplier** 表示類型應該註冊為使用者定義套用器。 此類別無法獲得繼承。

**SqlUserDefinedApplier** 是使用者定義套用器定義的 **選擇性**。


主要的可程式性物件如下所示︰

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

輸入資料列集會傳遞做為 `IRow` 輸入。 輸出資料列會產生為 `IUpdatableRow` 輸出介面。

呼叫 `IRow` 結構描述方法即可判斷個別資料行名稱。

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

若要從傳入的 `IRow` 取得實際資料值，我們可以使用 `IRow` 介面的 Get() 方法。

```csharp
mycolumn = row.Get<int>("mycolumn")
```

或者，我們使用結構描述資料行名稱︰

```csharp
row.Get<int>(row.Schema[0].Name)
```

輸出值必須使用 `IUpdatableRow` 輸出進行設定：

```csharp
output.Set<int>("mycolumn", mycolumn)
```

請務必了解，自訂套用器只會輸出以 `output.Set` 方法呼叫定義的資料行和值。

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的輸出。

使用者定義套用器參數可以傳遞給建構函式。 在基底 U-SQL 指令碼的套用器呼叫期間，套用器可傳回數目不定且需要加以定義的資料行。

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

以下是使用者定義套用器的範例︰

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

以下是此使用者定義套用器的基底 U-SQL 指令碼：

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此使用案例中，使用者定義套用器可做為車隊屬性的逗號分隔值剖析器。 輸入檔資料列看起來如下所示︰

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

這是典型的定位點分隔符號 TSV 檔案，其屬性資料行內含製造商、型號等汽車屬性。 這些屬性必須剖析到資料表資料行。 所提供的套用者也可讓您根據傳遞的參數，在結果資料列集中產生屬性的動態數字。 您可以產生所有屬性或僅一組特定的內容。

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

使用者定義套用器可以呼叫做為套用器物件的新執行個體：

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

或透過叫用包裝函式 Factory 方法來呼叫：

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)