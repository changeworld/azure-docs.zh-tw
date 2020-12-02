---
title: Azure Data Lake 的 U-SQL 使用者定義輸出器可程式性指南
description: 深入瞭解如何使用使用者定義輸出器的 [U-SQL UDO 可程式性指南]。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512535"
---
# <a name="use-user-defined-outputter"></a>使用使用者定義的輸出器

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL UDO：使用者定義的輸出器
使用者定義輸出器是另一個 U-SQL UDO，其可讓您擴充內建的 U-SQL 功能。 和擷取器類似，系統也有數個內建輸出器。

* Outputters.Text()︰將資料寫入不同編碼的分隔文字檔。
* Outputters.Csv()︰將資料寫入不同編碼的逗號分隔值 (CSV) 檔案。
* Outputters.Tsv()︰將資料寫入不同編碼的定位鍵分隔值 (TSV) 檔案。

自訂輸出器可讓您以自訂的定義格式寫入資料。 這可適用於下列工作︰

* 將資料寫入半結構化檔案或非結構化檔案。
* 寫入編碼不受支援的資料。
* 修改輸出資料或新增自訂屬性。

## <a name="how-to-define-and-use-user-defined-outputter"></a>如何定義及使用使用者定義的輸出器
若要定義使用者定義輸出器，我們需要建立 `IOutputter` 介面。

以下是基底 `IOutputter` 類別實作︰

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

輸出器的所有輸入參數 (例如資料行/資料列分隔符號、編碼等) 都必須在類別的建構函式中加以定義。 `IOutputter` 介面也應包含 `void Output` 覆寫的定義。 可選擇性地將屬性 `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` 設定為不可部分完成檔案處理。 如需詳細資訊，請參閱下列詳細資料。

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* 會針對每個輸入資料列呼叫 `Output`。 它會傳回 `IUnstructuredWriter output` 資料列集。
* 建構函式類別可用來將參數傳遞至使用者定義輸出器。
* `Close` 可選擇性地覆寫，以釋出耗費資源的狀態或判斷最後一個資料列的寫入時間。

**SqlUserDefinedOutputter** 表示類型應該註冊為使用者定義輸出器。 此類別無法獲得繼承。

SqlUserDefinedOutputter 是使用者定義輸出器之定義的選擇性屬性。 它可用來定義 AtomicFileProcessing 屬性。

* bool     AtomicFileProcessing   

* **true** = 表示此輸出器需要不可部分完成的輸出檔 (JSON、XML ...)
* **false** = 表示此輸出器可以處理分割/分散式檔案 (CSV、SEQ ...)

主要的可程式性物件為「資料列」和「輸出」。 **列** 物件可用來列舉輸出資料做為 `IRow` 介面。 **輸出** 用來設定輸出資料至目標檔案。

輸出資料是透過 `IRow` 介面來存取。 一次會對輸出資料傳遞一個資料列。

藉由呼叫 IRow 介面的 Get 方法可列舉個別的值：

```csharp
row.Get<string>("column_name")
```

呼叫 `row.Schema` 即可判斷個別資料行名稱：

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

這種方法可讓您為任何中繼資料結構描述建置彈性的輸出器。

會使用 `System.IO.StreamWriter` 將輸出資料寫入檔案。 資料流參數會設為 `output.BaseStream` 做為 `IUnstructuredWriter output` 的一部分。

請注意，必須在每個資料列反覆項目之後排清檔案的資料緩衝區。 此外，`StreamWriter` 物件必須可用於啟用可處置的屬性 (預設值) 與 **使用** 關鍵字︰

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

否則，在每個反覆項目之後明確地呼叫 Flush() 方法。 我們將此顯示在下列範例中。

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>設定使用者定義輸出器的頁首和頁尾
若要設定頁首，請使用單一的反覆運算執行流程。

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

第一個 `if (isHeaderRow)` 區塊中的程式碼只會執行一次。

針對頁尾，使用參考 `System.IO.Stream` 物件的執行個體 (`output.BaseStream`)。 在 `IOutputter` 介面的 Close() 方法中撰寫頁尾。  (如需詳細資訊，請參閱下列範例。)

以下是使用者定義 outputter 的範例︰

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

以及 U-SQL 基底指令碼：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

這是 HTML 輸出器，會以資料表資料建立 HTML 檔案。

### <a name="call-outputter-from-u-sql-base-script"></a>從 U-SQL 基底指令碼呼叫輸出器
若要從基底 U-SQL 指令碼呼叫自訂輸出器，您必須為輸出器物件建立新的執行個體。

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

若不要在基底指令碼中建立物件執行個體，我們可以建立函式的包裝函式，如先前的範例所示︰

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

在此案例中，原始呼叫看起來像：

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)