---
title: 使用 Azure 串流分析中的 .NET 自訂還原序列化程式來讀取任何格式的輸入
description: 本文說明序列化格式，以及定義適用于 Azure 串流分析雲端和邊緣作業之自訂 .NET 還原序列化程式的介面。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845270"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>使用 .NET 自訂還原序列化程式來讀取任何格式的輸入

.NET 自訂還原序列化程式可讓您的 Azure 串流分析作業從三種內[建資料格式](stream-analytics-parsing-json.md)以外的格式讀取資料。 本文說明序列化格式，以及定義適用于 Azure 串流分析雲端和邊緣作業之 .NET 自訂還原序列化程式的介面。 另外還有通訊協定緩衝區和 CSV 格式的範例還原序列化程式。

## <a name="net-custom-deserializer"></a>.NET 自訂還原序列化

下列程式碼範例是定義自訂還原序列化和執行 `StreamDeserializer<T>`的介面。

`UserDefinedOperator` 是所有自訂串流運算子的基類。 它會初始化 `StreamingContext`，它會提供內容，其中包含發佈診斷的機制，而您需要對其進行還原序列化程式的任何問題。

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

下列程式碼片段是串流資料的還原序列化。 

Skippable 錯誤應使用透過 `UserDefinedOperator`的 Initialize 方法傳遞 `IStreamingDiagnostics` 來發出。 所有例外狀況都會被視為錯誤，而還原序列化會重新建立。 在特定數目的錯誤之後，作業將會進入失敗狀態。

`StreamDeserializer<T>` 將資料流程還原序列化成 `T`類型的物件。 必須符合下列條件：

1. T 是類別或結構。
1. T 中的所有公用欄位都是
    1. 其中一個 [sbyte、byte、short、ushort、int、uint、long、DateTime、string、float、double] 或其可為 null 的對應專案。
    1. 遵循相同規則的另一個結構或類別。
    1. 遵循相同規則之 `T2` 類型的陣列。
    1. IList`T2`，其中 T2 遵循相同的規則。
    1. 沒有任何遞迴類型。

參數 `stream` 是包含序列化物件的資料流程。 `Deserialize` 會傳回 `T` 實例的集合。

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` 提供內容，其中包括為使用者操作員發佈診斷的機制。

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` 是使用者定義的運算子（包括序列化程式、還原序列化程式和使用者定義函數）的診斷。

`WriteError` 會將錯誤訊息寫入診斷記錄，並將錯誤傳送到診斷。

`briefMessage` 是簡短的錯誤訊息。 此訊息會顯示在診斷中，並由產品小組用來進行偵錯工具。 請勿包含敏感性資訊，並將訊息保留少於200個字元

`detailedMessage` 是詳細的錯誤訊息，只會新增至儲存體中的診斷記錄。 此訊息應小於2000個字元。

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>還原序列化範例

本節說明如何撰寫 Protobuf 和 CSV 的自訂還原序列化程式。 如需其他範例（例如事件中樞 Capture 的 AVRO 格式），請造訪[GitHub 上的 Azure 串流分析](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

### <a name="protocol-buffer-protobuf-format"></a>通訊協定緩衝區（Protobuf）格式

這是使用通訊協定緩衝區格式的範例。

假設下列通訊協定緩衝區定義。

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

從**Protobuf**執行 `protoc.exe` NuGet 會產生具有定義的 .cs 檔案。 產生的檔案不會顯示在這裡。

下列程式碼片段是還原序列化程式的執行，假設產生的檔案包含在專案中。 此實作為產生之檔案上的精簡型包裝函式。

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

下列程式碼片段是簡單的 CSV 還原序列化程式，也會示範如何傳播錯誤。

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>REST Api 的序列化格式

每個串流分析輸入都具有**序列化格式**。 如需輸入選項的詳細資訊，請參閱[輸入 REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)檔。

下列 JAVAscript 程式碼是使用 REST API 時的 .NET 還原序列化程式序列化格式的範例：

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` 應該是可執行 `StreamDeserializer<T>`的類別。 這會在下一節中說明。

## <a name="region-support"></a>區域支援

這項功能會在下欄區域內上市：

* 美國中西部
* 北歐
* 美國東部
* 美國西部
* 美國東部 2
* 歐洲西部

您可以[要求](https://aka.ms/ccodereqregion)其他區域的支援。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>這項功能何時會在所有 Azure 區域中提供？

這項功能可在[6 個區域](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)中使用。 如果您有興趣在另一個區域中使用這項功能，您可以[提交要求](https://aka.ms/ccodereqregion)。 所有 Azure 區域的支援都在藍圖中。

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>我可以從類似 GetMetadataPropertyValue 函數的輸入存取 MetadataPropertyValue 嗎？

但是，目前已不支援這項功能。 如果您需要這項功能，您可以在[UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)上為此要求投票。

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>我可以與該社區分享我的還原序列化程式，讓其他人可以受益嗎？

當您完成還原序列化程式之後，您可以透過與該社區共用來協助其他人。 將您的程式碼提交至[Azure 串流分析 GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)存放庫。

## <a name="next-steps"></a>後續步驟

* [適用于 Azure 串流分析雲端作業的 .NET 自訂還原序列化程式](custom-deserializer.md)
