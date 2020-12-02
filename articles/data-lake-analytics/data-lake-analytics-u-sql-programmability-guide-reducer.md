---
title: Azure Data Lake 的 U-SQL 使用者定義歸納器可程式性指南
description: 深入瞭解 < SQL UDO 可程式性指南-使用者定義的歸納器。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512532"
---
# <a name="use-user-defined-reducer"></a>使用使用者定義的歸納器

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL UDO：使用者定義的歸納器

U-SQL 可讓您藉由實作 IReducer 介面，在 C# 中使用使用者定義運算子擴充性架構撰寫自訂的資料列集歸納器。

使用者定義歸納器 (簡稱 UDR) 可用來在資料擷取 (匯入) 期間消除不必要的資料列。 也可以用來操作和評估資料列和資料行。 根據程式設計邏輯，它也可以定義需要擷取哪些資料列。

## <a name="how-to-define-and-use-user-defined-reducer"></a>如何定義及使用使用者定義的歸納器
若要定義 UDR 類別，我們需要使用選擇性的 `SqlUserDefinedReducer` 屬性建立 `IReducer` 介面。

此類別介面應該包含 `IEnumerable` 介面資料列集覆寫的定義。

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

**SqlUserDefinedReducer** 表示類型應該註冊為使用者定義歸納器。 此類別無法獲得繼承。
**SqlUserDefinedReducer** 是使用者定義歸納器之定義的選擇性屬性。 它可用來定義 IsRecursive 屬性。

* bool     IsRecursive    
* **true** = 表示此歸納器是否為關聯式和交換式

主要的可程式性物件為「輸入」和「輸出」。 輸入物件用來列舉輸入資料列。 輸出用來設定減少活動結果的輸出資料列。

若要列舉輸入資料列，我們可以使用 `Row.Get` 方法。

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

`Row.Get` 方法的參數是傳遞做為 U-SQL 基底指令碼之 `REDUCE` 陳述式內 `PRODUCE` 類別一部分的資料行。 在此，我們也必須使用正確的資料類型。

針對輸出，使用 `output.Set` 方法。

請務必了解，自訂歸納器只會輸出以 `output.Set` 方法呼叫定義的值。

```csharp
output.Set<string>("mycolumn", guid);
```

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的歸納器輸出。

以下是歸納器範例：

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

在此使用案例中，歸納器會略過使用者名稱空白的資料列。 針對資料列集中的每個資料列，它會讀取每個必要的資料行，然後評估使用者名稱的長度。 只有當使用者名稱值的長度大於 0 時，它才會輸出實際的資料列。

以下為使用自訂歸納器的基底 U-SQL 指令碼：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)