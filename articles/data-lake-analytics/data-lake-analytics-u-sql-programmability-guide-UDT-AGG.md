---
title: Azure Data Lake 的 U-SQL UDT 和 UDAGG 可程式性指南
description: 瞭解 Azure Data Lake Analytics 中的 SQL-DMO UDT 和 UDAGG 可程式性，讓您建立絕佳的 .USQL 腳本。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: bf2e1e1bc30aeb3306d0a643eca4725d8765edac
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512556"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL 程式設計指南-UDT 和 UDAGG



## <a name="use-user-defined-types-udt"></a>使用使用者定義的類型：UDT
使用者定義類型 (UDT) 是 U-SQL 的另一個可程式性功能。 U-SQL UDT 的作用就像一般的 C# 使用者定義類型。 C# 是強類型的語言，允許使用內建和自訂的使用者定義類型。

在資料列集的頂點之間傳遞 UDT 時，U-SQL 無法以隱含方式序列化或還原序列化任意 UDT。 這表示使用者必須使用 IFormatter 介面提供明確的格式器。 這將提供 U-SQL 將 UDT 序列化和還原序列化的方法。

> [!NOTE]
> U-SQL 的內建擷取器和 outputter 目前無法將 UDT 資料從檔案往返序列化或還原序列化，即使是已設定 IFormatter 亦然。 因此當您使用 OUTPUT 陳述式撰寫 UDT 資料至檔案，或是使用解壓縮讀取它時，您必須將它做為字串或位元組陣列傳遞。 然後呼叫序列化，並明確地還原序列化程式碼 (也就是 UDT 的 ToString() 方法)。 另一方面，使用者定義的擷取器和 outputter 可以讀取並寫入 UDT。

如果我們嘗試在 EXTRACTOR 或 OUTPUTTER 中使用 UDT (出自上一個 SELECT)，如下所示︰

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

我們會收到下列錯誤：

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

若要在 Outptutter 中使用 UDT，我們必須使用 ToString() 方法將它序列化為字串，或建立自訂 Outputter。

UDT 目前不能用於 GROUP BY。 如果將 UDT 用於 GROUP BY，系統會擲回下列錯誤︰

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

若要定義 UDT，我們必須︰

1. 新增下列命名空間：

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. 新增 `Microsoft.Analytics.Interfaces`，其為 UDT 介面所需。 此外，`System.IO` 可能需要用來定義 IFormatter 介面。

3. 使用 SqlUserDefinedType 屬性來定義使用者定義類型。

**SqlUserDefinedType** 可用來將組件中的類型定義標示為 U-SQL 中的使用者定義類型 (UDT)。 屬性 (attribute) 上的屬性 (property) 會反映 UDT 的實際特性。 此類別無法獲得繼承。

SqlUserDefinedType 是 UDT 定義的必要屬性 (attribute)。

類別的建構函式：  

* SqlUserDefinedTypeAttribute (類型格式器)

* 輸入格式器︰需要參數來定義 UDT 格式器--明確地說，必須在這裡傳遞 `IFormatter` 介面的類型。

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* 典型的 UDT 也需要 IFormatter 介面定義，如下列範例所示︰

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`介面會以的根類型序列化和還原序列化物件圖形 \<typeparamref name="T"> 。

\<typeparam name="T">要序列化和還原序列化的物件圖形根型別。

* **還原序列化**：對所提供串流上的資料還原序列化，並重組物件的圖形。

* **序列化**：使用指定根將物件或物件圖形序列化到所提供的串流。

`MyType` 執行個體：類型的執行個體。  
`IColumnWriter` writer / `IColumnReader` 讀取器：基礎資料行串流。  
`ISerializationContext` 內容：定義一組旗標的列舉，可在序列化期間指定串流的來源或目的地內容。

* **中繼**：指定來源或目的地內容不是持續性存放區。

* **持續性**：指定來源或目的地內容是持續性存放區。

做為一般的 C# 類型，U-SQL UDT 定義可包括 +/==/!= 等運算子的覆寫。 它也可以包含靜態方法。 例如，如果我們要使用此 UDT 做為 U-SQL MIN 彙總函式的參數，我們必須定義 < 運算子覆寫。

本指南稍早曾示範過從格式為 `Qn:Pn (Q1:P10)` 之特定日期識別會計週期的範例。 下列範例將示範如何為會計週期值定義自訂類型。

以下是具有自訂 UDT 和 IFormatter 介面的程式碼後置區段範例︰

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

已定義的類型包含兩個數字：季度和月份。 運算子 `==/!=/>/<` 和靜態方法 `ToString()` 定義於此。

如先前所述，UDT 可用於 SELECT 運算式中，但不能用在沒有自訂序列化的 OUTPUTTER/EXTRACTOR。 它必須使用 `ToString()` 序列化為字串或與自訂 OUTPUTTER/EXTRACTOR 搭配使用。

現在我們來討論一下 UDT 的使用方式。 在程式碼後置區段中，我們已將 GetFiscalPeriod 函式變更為：

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

如您所見，它會傳回 FiscalPeriod 類型的值。

這裡我們提供如何在 U-SQL 基底指令碼中進一步使用它的範例。 這個範例會示範從 U-SQL 指令碼叫用 UDT 的不同方式。

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

以下是完整程式碼後置區段的範例︰

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>使用使用者定義彙總：UDAGG
使用者定義彙總是指並非 U-SQL 現成提供的彙總相關函式。 其範例包括用來執行自訂數學計算、字串串連、使用字串之操作等彙總。

使用者定義彙總的基底類別定義如下所示：

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** 表示類型應該註冊為使用者定義彙總。 此類別無法獲得繼承。

SqlUserDefinedType 屬性是 UDAGG 定義的 **選擇性** 屬性。


基底類別可讓您傳遞三個抽象參數：其中兩個做為輸入參數，一個做為結果參數。 資料類型會變動，但應該會在繼承類別時進行定義。

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** 會在計算期間，為每個群組叫用一次。 Init 可為每個彙總群組提供初始化常式。  
* **Accumulate** 會為每個值執行一次。 它提供彙總演算法的主要功能。 在繼承類別時，它可用來彙總各種資料類型的值。 它可接受兩個變數資料類型的參數。
* **Terminate** 在處理結束時對每個彙總群組執行一次，以輸出每個群組的結果。

若要宣告正確的輸入和輸出資料類型，使用類別定義如下所示︰

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1：要累積的第一個參數
* T2：要累積的第二個參數
* TResult：傳回終止的類型

例如：

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

或

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>在 U-SQL 中使用 UDAGG
若要使用 UDAGG，請先在程式碼後置中加以定義，或如稍早所述從現存的可程式性 DLL 參考它。

然後使用下列語法：

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

UDAGG 範例如下：

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

以及基底 U-SQL 指令碼：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

在此使用案例中，我們會串連特定使用者的類別 GUID。

## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
