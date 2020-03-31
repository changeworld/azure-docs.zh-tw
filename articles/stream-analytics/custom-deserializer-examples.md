---
title: 使用 Azure 流分析中的 .NET 自訂反序列化器以任何格式讀取輸入
description: 本文介紹了為 Azure 流分析雲和邊緣作業定義自訂 .NET 反序列化的序列化格式和介面。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845270"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>使用 .NET 自訂反序列化器以任何格式讀取輸入

.NET 自訂反序列化器允許 Azure 流分析作業從三種[內置資料格式以外的格式讀取資料](stream-analytics-parsing-json.md)。 本文介紹了序列化格式和定義 .NET 自訂序列化的 Azure 流分析雲和邊緣作業的介面。 還有協定緩衝區和 CSV 格式的示例反序列化器。

## <a name="net-custom-deserializer"></a>.NET 自訂序列化器

以下代碼示例是定義自訂反序列化並實現`StreamDeserializer<T>`的介面。

`UserDefinedOperator`是所有自訂流式處理運算子的基類。 它初始化`StreamingContext`，它提供上下文，其中包括用於發佈診斷的機制，您需要為此調試反序列化程式的任何問題。

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

以下程式碼片段是流資料反序列化。 

應使用`IStreamingDiagnostics`通過`UserDefinedOperator`'s 初始化方法傳遞可跳過的錯誤。 所有異常將被視為錯誤，並重新創建反序列化程式。 出現一定數量的錯誤後，作業將轉到失敗狀態。

`StreamDeserializer<T>`將流序列化為類型`T`的物件。 必須符合下列條件：

1. T 是類或結構。
1. T 中的所有公共欄位都是
    1. 其中一個 [s位元組， 位元組， 短， ushort， int， uint， 長， DateTime， 字串， 浮點， 雙] 或它們可無等效項.
    1. 遵循相同規則的另一個結構或類。
    1. 遵循相同規則`T2`的類型陣列。
    1. IList`T2` T2 遵循相同的規則。
    1. 沒有任何遞迴類型。

參數`stream`是包含序列化物件的流。 `Deserialize`返回實例的集合`T`。

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`提供上下文，其中包括用於發佈使用者操作員診斷的機制。

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`是使用者定義的運算子的診斷，包括序列化器、反序列化器和使用者定義的函數。

`WriteError`將錯誤訊息寫入診斷日誌並將錯誤發送到診斷。

`briefMessage`是一條簡短的錯誤訊息。 此消息顯示在診斷中，由產品團隊用於調試目的。 不要包含敏感資訊，並且郵件少於 200 個字元

`detailedMessage`是僅添加到存儲中的診斷日誌的詳細錯誤訊息。 此消息應小於 2000 個字元。

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>反序列化器示例

本節介紹如何為 Protobuf 和 CSV 編寫自訂反序列化器。 有關其他示例（如事件中心捕獲的 AVRO 格式），請訪問[GitHub 上的 Azure 流分析](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

### <a name="protocol-buffer-protobuf-format"></a>協定緩衝區（原型）格式

這是使用協定緩衝區格式的示例。

假設以下協定緩衝區定義。

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

從`protoc.exe` **Google.Protobuf.Tools** NuGet 運行生成帶有定義的 .cs 檔。 生成的檔不在此處顯示。

以下程式碼片段是反序列化器實現，假定生成的檔包含在專案中。 此實現只是生成的檔的精簡包裝器。

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

以下程式碼片段是一個簡單的 CSV 反序列化器，它還演示傳播錯誤。

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

每個流分析輸入都有**一個序列化格式**。 有關輸入選項的詳細資訊，請參閱[輸入 REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)文檔。

使用 REST API 時，以下 JAVAscript 代碼是 .NET 反序列化序列化格式的示例：

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

`serializationClassName`應該是實現`StreamDeserializer<T>`的類。 下面一節將對此進行說明。

## <a name="region-support"></a>區域支援

此功能在以下區域可用：

* 美國中西部
* 北歐
* 美國東部
* 美國西部
* 美國東部 2
* 西歐

您可以[請求支援](https://aka.ms/ccodereqregion)其他區域。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>此功能何時在所有 Azure 區域中可用？

此功能在 6[個區域](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support)可用。 如果您有興趣在另一個區域使用此功能，則可以[提交請求](https://aka.ms/ccodereqregion)。 所有 Azure 區域的支援都在路線圖中。

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>我能否從類似于 GetMetadata屬性值函數的輸入訪問中繼資料屬性值？

但是，目前已不支援這項功能。 如果您需要此功能，您可以在[UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese)上投票支援此請求。

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>我能否與社區共用我的反序列化實現，以便其他人能夠受益？

實現反序列化後，您可以通過與社區共用來説明他人。 將代碼提交到[Azure 流分析 GitHub 存儲庫](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)。

## <a name="next-steps"></a>後續步驟

* [.NET Azure 流分析雲作業的自訂序列化程式](custom-deserializer.md)
