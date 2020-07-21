---
title: 剖析 Azure 監視器記錄中的文字資料 | Microsoft Docs
description: 說明在擷取資料時，以及在查詢中取出資料時，剖析 Azure 監視器記錄中記錄資料的不同選項，並比較各個選項的相對優點。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: c76cf939959d198d2c28181295a695a65ae46af9
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505698"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>剖析 Azure 監視器記錄中的文字資料
Azure 監視器所收集的部分記錄資料將在單一屬性中包含多個資訊片段。 將此資料剖析成多個屬性可讓您更輕鬆地在查詢中使用。 常見的範例為[自訂記錄檔](../platform/data-sources-custom-logs.md)，可將具有多個值的整個記錄檔項目收集成單一屬性。 您可以針對不同的值建立不同的屬性，以便在每個屬性中搜尋並彙總。

本文說明在擷取資料時，以及在查詢中取出資料時，剖析 Azure 監視器中記錄資料的不同選項，並比較各個選項的相對優點。


## <a name="parsing-methods"></a>剖析方法
您可以在收集資料時剖析擷取期間的資料，或是在使用查詢分析資料時剖析查詢期間的資料。 每個策略都有獨特的優點，如下所述。

### <a name="parse-data-at-collection-time"></a>在收集期間剖析資料
當您在收集時間剖析資料時，您要設定可在資料表中建立新屬性的 [[自訂欄位]](../platform/custom-fields.md)。 查詢不一定會包含任何剖析邏輯，因此只要使用這些屬性作為資料表中的其他任何欄位即可。

這個方法的優點包括：

- 更輕鬆地查詢所收集的資料，因為您不需要在查詢中包含剖析命令。
- 更好的查詢效能，因為查詢不需要執行剖析。
 
這個方法的缺點包括：

- 必須事先加以定義。 無法包含已經收集的資料。
- 如果您變更剖析邏輯，它只會套用至新的資料。
- 比查詢中可用的剖析選項還少。
- 增加收集資料的延遲時間。
- 錯誤可能難以處理。


### <a name="parse-data-at-query-time"></a>在查詢期間剖析資料
當您在查詢期間剖析資料時，您會在查詢中包含邏輯，以便將資料剖析成多個欄位。 實際資料表本身未修改。

這個方法的優點包括：

- 適用於任何資料，包括已收集的資料。
- 邏輯變更可以立即套用至所有資料。
- 剖析選項彈性，包括針對特定資料結構預先定義的邏輯。
 
這個方法的缺點包括：

- 需要更複雜的查詢。 這可以藉由使用[模擬資料表的函式](#use-function-to-simulate-a-table)解決。
- 必須在多個查詢中複寫剖析邏輯。 可以透過函式共用特定邏輯。
- 針對非常大型的記錄集 (數百萬筆記錄) 執行複雜的邏輯時，可能會造成額外負荷。

## <a name="parse-data-as-its-collected"></a>剖析所收集的資料
如需剖析所收集資料的詳細資訊，請參閱[在 Azure 監視器中建立自訂欄位](../platform/custom-fields.md)。 這會在可供查詢使用的資料表中建立自訂屬性，就像其他任何屬性一樣。

## <a name="parse-data-in-query-using-patterns"></a>使用模式剖析查詢中的資料
當您想要剖析的資料可以透過記錄之間重複的模式識別時，您可以在 [Kusto 查詢語言](/azure/kusto/query/)中使用不同的運算子，將特定資料片段擷取到一或多個新的屬性。

### <a name="simple-text-patterns"></a>簡易文字模式

在查詢中使用 [parse](/azure/kusto/query/parseoperator) 運算子建立可以從字串運算式擷取的一或多個自訂屬性。 您可以指定要識別的模式，以及要建立之屬性的名稱。 這對於具有格式類似於 _key=value_ 之索引鍵-值字串的資料而言，特別實用。

請考慮具有下列格式資料的自訂記錄檔。

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

下列查詢會將此資料剖析成個別的屬性。 加入具有 _project_ 的那一行僅會傳回計算的屬性，而非 _RawData_，後者是保留自訂記錄檔中整個項目的單一屬性。

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

以下是在 _AzureActivity_ 資料表中，細分 UPN 使用者名稱的另一個範例。

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>規則運算式
如果可以使用規則運算式識別您的資料，則您可以利用[使用規則運算式的函式](/azure/kusto/query/re2)來擷取個別的值。 下列範例會使用 [extract](/azure/kusto/query/extractfunction)，從 _AzureActivity_ 記錄中細分 _UPN_ 欄位，然後再傳回不同的使用者。

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

若要大規模啟用有效率的剖析功能，Azure 監視器會使用 re2 版的規則運算式，這類似部分其他規則運算式版本，但不完全相同。 如需詳細資訊，請參閱 [re2 運算式語法](https://aka.ms/kql_re2syntax)。


## <a name="parse-delimited-data-in-a-query"></a>在查詢中剖析分隔的資料
分隔的資料會以一般字元分隔欄位，例如 CSV 檔案中的逗號。 利用 [split](/azure/kusto/query/splitfunction) 函式剖析使用您指定之分隔符號分隔的資料。 您可以使用這個搭配 [extend](/azure/kusto/query/extendoperator) 運算子，傳回資料中的所有欄位，或指定要包含在輸出中的個別欄位。

> [!NOTE]
> split 會傳回動態物件，因此結果可能需要明確地轉換成字串之類的資料類型，才能用於運算子和篩選中。

請考慮具有下列 CSV 格式資料的自訂記錄檔。

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

下列查詢會剖析此資料，並以其中兩個計算的屬性摘要說明。 第一行會將 _RawData_ 屬性分割成字串陣列。 接下來幾行的每一行都會為個別的屬性提供名稱，並使用將這些屬性轉換成適當資料類型的函式，將其新增至輸出。

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>剖析查詢中預先定義的結構
如果您的資料採用已知的結構設定格式，您可以使用 [Kusto 查詢語言](/azure/kusto/query/)中的其中一種函式，剖析預先定義的結構：

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL 查詢](/azure/kusto/query/parseurlqueryfunction)
- [檔案路徑](/azure/kusto/query/parsepathfunction)
- [使用者代理程式](/azure/kusto/query/parse-useragentfunction)
- [版本字串](/azure/kusto/query/parse-versionfunction)

下列範例查詢會剖析 _AzureActivity_ 資料表 (JSON 結構) 的 [屬性]__ 欄位。 它會將結果儲存到稱為 _parsedProp_ 的動態屬性，其中包括 JSON 中的個別命名值。 這些值用來篩選和彙總查詢結果。

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

這些剖析函式可能是密集型處理器，因此只有在您的查詢使用格式化資料的多個屬性時，才會使用這些函式。 否則，簡單的模式比對處理將會更快。

下列範例顯示網域控制站 TGT 事先驗證類型的分解。 此類型只存在於 EventData 欄位中，這是 XML 字串，但不需要此欄位中的其他任何資料。 在此情況下，[parse](/azure/kusto/query/parseoperator) 用來挑出所需的資料片段。

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>使用函式模擬資料表
您可能有多個查詢會針對特定資料表執行相同的剖析。 在此情況下，[建立函式](functions.md)，且該函式會傳回剖析的資料，而不會複寫每個查詢中的剖析邏輯。 之後您可以使用函式別名來取代其他查詢中的原始資料表。

請考慮上述以逗號分隔的自訂記錄檔範例。 若要在多個查詢中使用已剖析的資料，請使用下列查詢建立函式，並以別名 _MyCustomCSVLog_ 加以儲存。

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

您現在可以使用別名 _MyCustomCSVLog_ 來取代查詢中的實際資料表名稱，如下所示。

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>後續步驟
* 了解[記錄查詢](log-query-overview.md)，以分析從資料來源和解決方案收集到的資料。
