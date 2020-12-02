---
title: Azure Data Lake 的 U-SQL 可程式性指南
description: 瞭解 Azure Data Lake Analytics 中的 SQL 總覽和 UDF 可程式性，讓您建立絕佳的 .USQL 腳本。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510857"
---
# <a name="u-sql-programmability-guide-overview"></a>U-SQL 程式設計指南總覽

U-SQL 是為巨量資料類型的工作負載所設計的查詢語言。 U-SQL 的其中一項獨特功能是，可將類 SQL 的宣告式語言與 C# 所提供的擴充性和可程式性結合在一起。 在本指南中，我們將著重於介紹由 C# 所實現的 U-SQL 語言之擴充性和可程式性。

## <a name="requirements"></a>需求

下載及安裝 [Azure Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)。

## <a name="get-started-with-u-sql"></a>開始使用 U-SQL  

請看下面的 U-SQL 指令碼：

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

此指令碼會定義兩個資料列集：`@a` 和 `@results`。 資料列集 `@results` 是透過 `@a` 所定義的。

## <a name="c-types-and-expressions-in-u-sql-script"></a>U-SQL 指令碼中的 C# 類型和運算式

U-SQL 運算式是 C# 運算式，與例如 `AND`、`OR` 和 `NOT` 的 U-SQL 邏輯作業合併。 U-SQL 運算式可以與 SELECT、EXTRACT、WHERE、HAVING、GROUP BY 和 DECLARE 搭配使用。 例如，下列指令碼會將字串剖析為 DateTime 值。

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

下列程式碼片段會將字串剖析為 DECLARE 陳述式中的 DateTime 值。

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>使用 C# 運算式轉換資料類型

下列範例示範如何使用 C# 運算式進行日期時間資料轉換。 在這個特別案例中，字串日期時間資料會轉換成標準日期時間，也就是以午夜 00:00:00 為準的時間標記法。

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>使用 C# 運算式來表示今天的日期

若要提取今天的日期，我們可以使用下列 C# 運算式：`DateTime.Now.ToString("M/d/yyyy")`

如何在指令碼中使用此運算式的範例如下︰

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>使用 .NET 組件

U-SQL 的擴充性模型極為依賴從 .NET 組件新增自訂程式碼的能力。 

### <a name="register-a-net-assembly"></a>註冊 .NET 組件

使用 `CREATE ASSEMBLY` 陳述式將 .NET 組件放到 U-SQL 資料庫。 之後，U-SQL 指令碼即可透過 `REFERENCE ASSEMBLY` 陳述式使用這些組件。 

下列程式碼將說明如何註冊組件：

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

下列程式碼將說明如何參考組件：

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

請參閱更詳細涵蓋此主題的[組件註冊指示](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog)。


### <a name="use-assembly-versioning"></a>使用組件版本控制
目前，SQL-DMO 使用 .NET Framework 版本4.7.2。 因此，請確定您自己的組件與該執行階段版本相容。

如前面所述，U-SQL 會執行 64 位元 (x64) 格式的程式碼。 因此，請確定您的程式碼是編譯為可在 x64 上執行。 否則，您會得到如上所示的格式不正確錯誤。

每個上傳的組件 DLL 和資源檔，例如不同的執行階段、原生組件或組態檔中，最多可達 400 MB。 已部署資源的大小總計 (透過 DEPLOY RESOURCE 或透過參考組件及其他檔案) 不能超過 3 GB。

最後請注意，每一個 U-SQL 資料庫所包含的任何指定組件只能有一個版本。 例如，如果您同時需要第7版和第8版的 NewtonSoft Json.NET 程式庫，您必須在兩個不同的資料庫中註冊它們。 此外，每個指令碼所參考的指定組件 DLL 只能有一個版本。 在這方面，U-SQL 會遵循 C# 組件的管理和版本設定語意。

## <a name="use-user-defined-functions-udf"></a>使用使用者定義函式：UDF
U-SQL 使用者定義函數 (簡稱 UDF) 會編寫常式，以接受參數、執行動作 (例如複雜計算)，以及傳回該動作結果的值。 UDF 的傳回值只能是單一純量。 U-SQL UDF 可以和任何其他 C# 純量函式一樣，在 U-SQL 基底指令碼中進行呼叫。

我們建議您將 U-SQL 使用者定義函式初始化為 **公用** 且 **靜態** 的函式。

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

我們先看看簡單的建立 UDF 範例。

在此使用案例中，我們必須決定特定使用者首次登入的會計週期 (包括會計季度和會計月份)。 在我們的案例中，一年的第一個會計月份為六月。

為了計算會計週期，我們引進了下列 C# 函式：

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
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

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

它只會計算會計月份和季度，並傳回字串值。 六月 (第一個會計季度的第一個月份)，我們使用「Q1:P1」。 七月，我們使用「Q1:P2」，以此類推。

這是一般 C# 函式，我們將使用我們的 U-SQL 專案。

此案例中的程式碼後置區段外觀如下：

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
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

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

現在，我們要從基底 U-SQL 指令碼呼叫此函式。 若要這樣做，我們必須提供完整函式名稱，包括命名空間，在此案例中為 NameSpace.Class.Function(parameter)。
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

以下是實際的 U-SQL 基底指令碼：
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

以下是指令碼執行的輸出檔案：

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

這個範例示範 U-SQL 中內嵌 UDF 的簡易用法。

### <a name="keep-state-between-udf-invocations"></a>在 UDF 的引動過程之間保持狀態
U-SQL C# 可程式性物件可以透過程式碼後置全域變數，更加複雜地利用互動性。 讓我們看看下列商務使用案例。

在大型組織中，使用者可以切換使用各種內部應用程式。 這些可能包括 Microsoft Dynamics CRM、Power BI 等等。 客戶可能想要以遙測資料分析使用者切換使用不同應用程式的方式、使用趨勢為何等等的資訊。 企業的目的是獲得最佳的應用程式使用情況。 他們或許也想要會合併不同應用程式或特定登入常式。

為了達成此目標，我們必須確定工作階段識別碼，以及最後發生之工作階段間的延遲時間。

我們必須找到上一次登入，然後對所有將為相同應用程式產生的工作階段指派此登入。 第一項挑戰是 U-SQL 基底指令碼無法讓我們使用 LAG 函數，對所有已經過計算的資料行套用計算。 第二項挑戰在於，我們必須讓相同時間週期內的所有工作階段保持在特定工作階段。

為了解決這個問題，我們將在程式碼後置區段內使用全域變數：`static public string globalSession;`。

在我們執行指令碼期間，此全域變數會套用到整個資料列集。

以下是 U-SQL 程式的程式碼後置區段：

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

這個範例會示範全域變數 `static public string globalSession;` 用於 `getStampUserSession` 函式內，每次工作階段參數變更時都會重新初始化。

U-SQL 基礎指令碼如下所示︰

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

第二次計算記憶體資料列集期間會在此呼叫函式 `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)`。 它會傳遞 `UserSessionTimestamp` 資料行，並傳回值直到 `UserSessionTimestamp` 變更。

輸出檔案如下所示：

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

此範例示範當我們在程式碼後置區段內使用套用至整個記憶體資料列集之全域變數時的更複雜使用案例。

## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [U-SQL 程式設計指南-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)