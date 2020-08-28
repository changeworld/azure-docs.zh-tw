---
title: 在 Azure 串流分析中使用 .NET 自訂還原序列化程式來讀取任何格式的輸入
description: 本文說明序列化格式，以及可為 Azure 串流分析雲端和邊緣作業定義自訂 .NET 還原序列化程式的介面。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4616f6c567b0bba13fe04aed56fd5e4ddc293f90
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008381"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>使用 .NET 自訂還原序列化程式讀取任何格式的輸入

.NET 自訂還原序列化程式可讓 Azure 串流分析作業從三種[內建資料格式](stream-analytics-parsing-json.md)以外的格式讀取資料。 本文說明序列化格式，以及可為 Azure 串流分析雲端和邊緣作業定義 .NET 自訂還原序列化程式的介面。 另外還有通訊協定緩衝區和 CSV 格式的範例還原序列化程式。

## <a name="net-custom-deserializer"></a>.NET 自訂還原序列化程式

下列程式碼範例是可定義自訂還原序列化程式及實作 `StreamDeserializer<T>` 的介面。

`UserDefinedOperator` 是所有自訂串流運算子的基底類別。 其會初始化 `StreamingContext`來提供內容，其中包含用於發佈診斷的機制，而您需要使用還原序列化程式進行任何問題的偵錯。

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

下列程式碼片段是串流資料的還原序列化。 

可略過錯誤應使用透過 `UserDefinedOperator` 的 Initialize 方法傳遞的 `IStreamingDiagnostics` 來發出。 所有例外狀況都會被視為錯誤，而且會重新建立還原序列化程序。 在特定數量的錯誤之後，作業將會進入失敗狀態。

`StreamDeserializer<T>` 可將資料流還原序列化為 `T` 類型的物件。 必須符合下列條件：

1. T 是類別或結構。
1. T 中的所有公用欄位不是
    1. [sbyte、byte、short、ushort、int、uint、long、DateTime、string、float、double] 其中一項，就是其可為 Null 的對等項目。
    1. 另一個遵循相同規則的結構或類別。
    1. 遵循相同規則的 `T2` 類型陣列。
    1. IList`T2`，其中 T2 會遵循相同的規則。
    1. 沒有任何遞迴類型。

參數 `stream` 是包含序列化物件的資料流。 `Deserialize` 可傳回 `T` 執行個體的集合。

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` 可提供內容，其中包含用於發佈使用者運算子診斷的機制。

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` 是使用者定義運算子 (包括序列化程式、還原序列化程式和使用者定義的函式) 的診斷。

`WriteError` 可將錯誤訊息寫入資源記錄，並將錯誤傳送至診斷。

`briefMessage` 是簡短的錯誤訊息。 此訊息會顯示在診斷中，並由產品小組用來進行偵錯。 請勿包含敏感性資訊，並使訊息保持少於 200 個字元

`detailedMessage` 是詳細的錯誤訊息，其只會新增至儲存體中的資源記錄。 此訊息不得超過 2000 個字元。

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>還原序列化程式範例

本節說明如何為 Protobuf 和 CSV 撰寫自訂還原序列化程式。 如需其他範例 (例如事件中樞擷取的 AVRO 格式)，請造訪 [GitHub 上的 Azure 串流分析](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

### <a name="protocol-buffer-protobuf-format"></a>通訊協定緩衝區 (Protobuf) 格式

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

從 **Google.Protobuf.Tools** NuGet 執行 `protoc.exe` 可產生具有定義的 .cs 檔案。 所產生的檔案不會在此顯示。 您必須確保您在串流分析專案中使用的 Protobuf Nuget 版本，符合用來產生輸入的 Protobuf 版本。 

下列程式碼片段是假設所產生檔案包含在專案中的還原序列化程式實作。 此實作就是所產生檔案的精簡包裝函式。

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

下列程式碼片段是簡單的 CSV 還原序列化程式，其也會示範如何傳播錯誤。

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

## <a name="serialization-format-for-rest-apis"></a>REST API 的序列化格式

每個串流分析輸入都有**序列化格式**。 如需輸入選項的詳細資訊，請參閱[輸入 REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) 文件。

下列 Javascript 程式碼是使用 REST API 時的 .NET 還原序列化程式序列化格式的範例：

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

`serializationClassName` 應該是可實作 `StreamDeserializer<T>` 的類別。 以下章節會加以說明。

## <a name="region-support"></a>區域支援

此功能適用於下列區域：

* 美國中西部
* 北歐
* 美國東部
* 美國西部
* 美國東部 2
* 西歐

您可針對其他區域[要求支援](https://aka.ms/ccodereqregion)。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>此功能何時會在所有 Azure 區域中提供？

這項功能會在 [6 個區域](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)中提供。 如果您有興趣在另一個區域中使用這項功能，您可以[提交要求](https://aka.ms/ccodereqregion)。 所有 Azure 區域的支援都在藍圖規劃中。

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>我可以從類似 GetMetadataPropertyValue 函式的輸入存取 MetadataPropertyValue 嗎？

但是，目前已不支援這項功能。 如果您需要這項功能，可以在 [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese) 上投票同意此要求。

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>我可與社群分享我的還原序列化程式實作，讓其他人因此受惠嗎？

實作還原序列化程式後，即可藉由與社群分享來協助其他人。 將您的程式碼提交至 [Azure 串流分析 GitHub 存放庫](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

### <a name="what-are-the-other-limitation-of-using-custom-deserializers-in-stream-analytics"></a>在串流分析中使用自訂還原序列化程式的其他限制為何？

如果您的輸入為 Protobuf 格式，其具有包含 MapField 類型的結構描述，您將無法實作自訂還原序列化程式。 我們正努力持續支援這個類型。

## <a name="next-steps"></a>後續步驟

* [Azure 串流分析雲端作業的 .NET 自訂還原序列化程式](custom-deserializer.md)
