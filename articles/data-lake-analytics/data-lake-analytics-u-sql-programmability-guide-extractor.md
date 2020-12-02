---
title: Azure Data Lake 的 U-SQL 使用者定義的解壓縮程式設計指南
description: 深入瞭解如何使用使用者定義的解壓縮程式。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a3613c2b95f13e6bbaaf570f522ad1f7b7edd756
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512548"
---
# <a name="use-user-defined-extractor"></a>使用使用者定義的解壓縮

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL UDO：使用者定義的解壓縮
U-SQL 可讓您使用 EXTRACT 陳述式來匯入外部資料。 EXTRACT 陳述式可以使用內建的 UDO 擷取器：  

* Extractors.Text()︰可從不同編碼的分隔文字檔進行擷取。

* Extractors.Csv()︰可從不同編碼的逗號分隔值 (CSV) 檔案進行擷取。

* Extractors.Tsv()︰可從不同編碼的定位鍵分隔值 (TSV) 檔案進行擷取。

它很適合用來開發自訂擷取器。 在匯入資料期間，如果我們想要執行下列任何作業，這會很有幫助：

* 分割資料行並修改個別值，以修改輸入資料。 PROCESSOR 功能更適合用來結合資料行。
* 剖析非結構化資料 (例如網頁/電子郵件) 或半非結構化資料 (例如 XML/JSON)。
* 剖析編碼不受支援的資料。

## <a name="how-to-define-and-use-user-defined-extractor"></a>如何定義及使用使用者定義的解壓縮
若要定義使用者定義擷取器 (UDE)，我們需要建立 `IExtractor` 介面。 擷取器的所有輸入參數 (例如資料行/資料列分隔符號和編碼等) 都必須在類別的建構函式中加以定義。 `IExtractor` 介面也應包含 `IEnumerable<IRow>` 覆寫的定義，如下所示︰

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

**SqlUserDefinedExtractor** 表示類型應該註冊為使用者定義擷取器。 此類別無法獲得繼承。

SqlUserDefinedExtractor 是 UDE 定義的選擇性屬性。 它可用來定義 UDE 物件的 AtomicFileProcessing 屬性。

* bool     AtomicFileProcessing   

* **true** = 表示此擷取器需要不可部分完成的輸入檔 (JSON、XML ...)
* **false** = 表示此擷取器可以處理分割/分散式檔案 (CSV、SEQ ...)

主要的 UDE 可程式性物件為「輸入」和「輸出」。 輸入物件用來列舉輸入資料做為 `IUnstructuredReader`。 輸入物件可用來將輸出資料設定為擷取器活動的結果。

輸入資料是透過 `System.IO.Stream` 和 `System.IO.StreamReader` 來存取。

若要列舉輸入資料行，我們必須先使用資料列分隔符號分割輸入串流。

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

然後，將輸入資料列進一步分割為資料行組件。

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

若要設定輸出資料，我們可以使用 `output.Set` 方法。

請務必了解，自訂擷取程式只會輸出資料行和使用輸出定義的值。 設定方法呼叫。

```csharp
output.Set<string>(count, part);
```

呼叫 `yield return output.AsReadOnly();` 就會觸發實際的擷取器輸出。

以下是擷取器範例：

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

在此使用案例中，擷取器會為「guid」資料行重新產生 GUID，並將「使用者」資料行的值轉換為大寫。 自訂擷取器可藉由剖析輸入資料並用它進行操作，來產生更複雜的結果。

以下為使用自訂擷取器的基底 U-SQL 指令碼：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)