---
title: Azure Data Lake 的 U-SQL 使用者定義處理器可程式性指南
description: 深入瞭解 U SQL UDO 可程式性指南-使用者定義的處理器。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 6ff45c577e94a8c63bd7cb1e6603e4d5519af5c6
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512550"
---
# <a name="use-user-defined-processor"></a>使用使用者定義的處理器

## <a name="u-sql-udo-user-defined-processor"></a>U-SQL UDO：使用者定義的處理器
使用者定義處理器 (簡稱 UDP) 是一種 U-SQL UDO，可藉由套用可程式性功能處理傳入的資料列。 UDP 可讓您視需要結合資料行、修改值，以及新增資料行。 基本上，它能夠協助您處理資料列集，以產生所需的資料元素。

## <a name="how-to-define-and-use-user-defined-processor"></a>如何定義及使用使用者定義的處理器
若要定義 UDP，我們必須以 `SqlUserDefinedProcessor` 屬性 (這是 UDP 的選擇性屬性) 建立 `IProcessor`。

此介面應該包含 `IRow` 介面資料列集覆寫的定義，如下列範例所示︰

```csharp
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** 表示類型應該註冊為使用者定義處理器。 此類別無法獲得繼承。

SqlUserDefinedProcessor 是 UDP 定義的 **選擇性** 屬性。

主要的可程式性物件為「輸入」和「輸出」。 輸入物件可用來列舉輸入資料欄和輸出，以將輸出資料設定為處理器活動的結果。

若要列舉輸入資料行，我們可以使用 `input.Get` 方法。

```csharp
string column_name = input.Get<string>("column_name");
```

`input.Get` 方法的參數是傳遞做為 U-SQL 基底指令碼之 `PROCESS` 陳述式內 `PRODUCE` 子句一部分的資料行。 在此，我們必須使用正確的資料類型。

針對輸出，使用 `output.Set` 方法。

請務必注意，自訂產生程式只會輸出資料行和使用 `output.Set` 方法呼叫定義的值。

```csharp
output.Set<string>("mycolumn", mycolumn);
```

呼叫 `return output.AsReadOnly();` 就會觸發實際的處理器輸出。

以下是處理器範例：

```csharp
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

在此使用案例中，處理器會結合現存資料行 (在此使用案例中為大寫的「user」和「des」) 來產生稱為「full_description」的新資料行。 它也會重新產生 GUID，並傳回原始及新的 GUID 值。

如您在上一個範例中所看到，您可以在 `output.Set` 方法呼叫期間呼叫 C# 方法。

以下是使用自訂處理器的基底 U-SQL 指令碼範例：

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>後續步驟
* [U-SQL 程式設計指南-總覽](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL 程式設計指南-UDT 和 UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)