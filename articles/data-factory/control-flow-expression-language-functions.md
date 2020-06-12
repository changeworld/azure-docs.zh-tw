---
title: Azure Data Factory 中的運算式和函式
description: 本文提供可用來建立資料處理站實體的運算式和函式相關資訊。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 9870b239ca0501e63df3d800b8e4847cb0f390ac
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860931"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Azure Data Factory 中的運算式和函式

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-functions-variables.md)
> * [目前的版本](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文提供 Azure Data Factory 支援之運算式和函式的詳細資料。 

## <a name="expressions"></a>運算式

定義中的 JSON 值可以是常值，或是在執行階段評估的運算式。 例如：  
  
```json
"name": "value"
```

 或  
  
```json
"name": "@pipeline().parameters.password"
```

運算式可以出現在 JSON 字串值中的任何一處，並一律產生另一個 JSON 值。 當 JSON 值為運算式時，可透過移除 \@ 符號來擷取運算式的主體。 如果需要的常值字串開頭為 \@，就必須使用 \@\@ 逸出。 下列範例顯示如何評估運算式。  
  
|JSON 值|結果|  
|----------------|------------|  
|"parameters"|傳回字元 'parameters'。|  
|"parameters[1]"|傳回字元 'parameters[1]'。|  
|"\@\@"|傳回包含 '\@' 的 1 個字元字串。|  
|" \@"|傳回包含 '\@' 的 2 個字元字串。|  
  
 使用稱為「字串插補」的功能，運算式也可以出現在字串內，其中運算式會包含在 `@{ ... }` 內。 例如： `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 使用字串插補時，結果一律為字串。 假設我將 `myNumber` 定義為 `42`，將 `myString` 定義為 `foo`：  
  
|JSON 值|結果|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| 傳回 `foo` 做為字串。|  
|"\@{pipeline().parameters.myString}"| 傳回 `foo` 做為字串。|  
|"\@pipeline().parameters.myNumber"| 傳回 `42` 做為「編號」。|  
|"\@{pipeline().parameters.myNumber}"| 傳回 `42` 做為「字串」。|  
|"Answer is: @{pipeline().parameters.myNumber}"| 傳回字串 `Answer is: 42`。|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| 傳回字串 `Answer is: 42`|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| 傳回字串 `Answer is: @{pipeline().parameters.myNumber}`。|  
  
## <a name="examples"></a>範例

### <a name="complex-expression-example"></a>複雜運算式範例
下列範例顯示的複雜範例參考活動輸出的深度子欄位。 若要參考評估為子欄位的管道參數，請使用 [] 語法，而不是點 (.) 運算子 (如同 subfield1 和 subfield2 的情況)

@activity('{activityName}').output.{subfield1}.{subfield2}[pipeline().parameters.subfield3].{subfield4}

### <a name="a-dataset-with-a-parameter"></a>具有參數的資料集
在以下範例中，BlobDataset 會採用一個名為 **path** 的參數。 其值會藉由使用下列運算式，設定 **folderPath** 屬性的值：`dataset().path`。 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>具有參數的管線
在以下範例中，管線會採用 **inputPath** 和 **outputPath** 參數。 這些參數的值會用來設定參數化 Blob 資料集的 **path**。 這裡使用的語法為：`pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>教學課程
本[教學課程](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf)會引導您如何在管道與活動之間，以及活動之間傳遞參數。

  
## <a name="functions"></a>函式

您可以在運算式內呼叫函式。 下列各節提供可在運算式中使用之函式的相關資訊。  

## <a name="string-functions"></a>字串函數  

若要處理字串，您可以使用這些字串函式以及一些[集合函式](#collection-functions)。
字串函式只能用於字串。

| 字串函式 | Task |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | 結合兩個或多個字串，並傳回合併的字串。 |
| [endsWith](control-flow-expression-language-functions.md#endswith) | 檢查字串是否以指定的子字串結束。 |
| [guid](control-flow-expression-language-functions.md#guid) | 以字串形式產生全域唯一識別碼 (GUID)。 |
| [indexOf](control-flow-expression-language-functions.md#indexof) | 傳回子字串的起始位置。 |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | 傳回子字串最後一次出現的起始位置。 |
| [replace](control-flow-expression-language-functions.md#replace) | 使用指定字串取代子字串，並傳回已更新的字串。 |
| [分割](control-flow-expression-language-functions.md#split) | 根據原始字串中指定的分隔符號字元，從較大型字串傳回包含以逗號分隔之子字串的陣列。 |
| [startsWith](control-flow-expression-language-functions.md#startswith) | 檢查字串是否以特定的子字串開始。 |
| [substring](control-flow-expression-language-functions.md#substring) | 傳回字串中的字元 (從指定的位置起始)。 |
| [toLower](control-flow-expression-language-functions.md#toLower) | 傳回小寫格式的字串。 |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | 傳回大寫格式的字串。 |
| [修剪](control-flow-expression-language-functions.md#trim) | 移除字串的開頭和尾端空白字元，並傳回更新後的字串。 |

## <a name="collection-functions"></a>集合函式

若要處理集合 (通常為陣列、字串，而有時候為字典)，您可以使用這些集合函式。

| 集合函式 | Task |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | 檢查集合是否具有特定項目。 |
| [empty](control-flow-expression-language-functions.md#empty) | 檢查集合是否是空的。 |
| [first](control-flow-expression-language-functions.md#first) | 傳回集合中的第一個項目。 |
| [intersection](control-flow-expression-language-functions.md#intersection) | 在指定的多個集合中，傳回「只有」共同項目的集合。 |
| [join](control-flow-expression-language-functions.md#join) | 傳回具有陣列中「所有」項目 (以指定的字元隔開) 的字串。 |
| [last](control-flow-expression-language-functions.md#last) | 傳回集合中的最後一個項目。 |
| [length](control-flow-expression-language-functions.md#length) | 傳回字串或陣列中的項目數目。 |
| [skip](control-flow-expression-language-functions.md#skip) | 移除集合前端的項目，並傳回「其他所有」項目。 |
| [take](control-flow-expression-language-functions.md#take) | 傳回集合中的前端項目。 |
| [union](control-flow-expression-language-functions.md#union) | 傳回具有指定集合中「所有」項目的集合。 | 

## <a name="logical-functions"></a>邏輯函式  

這些函式在條件內相當有用，而且可以用來評估任何類型的邏輯。  
  
| 邏輯比較函式 | Task |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | 檢查是否所有運算式都是 True。 |
| [equals](control-flow-expression-language-functions.md#equals) | 檢查兩個值是否相等。 |
| [greater](control-flow-expression-language-functions.md#greater) | 檢查第一個值是否大於第二個值。 |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | 檢查第一個值是否大於或等於第二個值。 |
| [if](control-flow-expression-language-functions.md#if) | 檢查運算式是 True 或 False。 根據結果，傳回指定的值。 |
| [less](control-flow-expression-language-functions.md#less) | 檢查第一個值是否小於第二個值。 |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | 檢查第一個值是否小於或等於第二個值。 |
| [not](control-flow-expression-language-functions.md#not) | 檢查運算式是否為 False。 |
| [or](control-flow-expression-language-functions.md#or) | 檢查是否至少有一個運算式是 True。 |
  
## <a name="conversion-functions"></a>轉換函數  

 這些函式是用來在每個原生類型語言之間轉換︰  
-   字串
-   integer
-   FLOAT
-   boolean
-   陣列
-   字典

| 轉換函式 | Task |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | 從單一指定輸入傳回的陣列。 關於多個輸入的資訊，請參閱 [createArray](control-flow-expression-language-functions.md#createArray)。 |
| [base64](control-flow-expression-language-functions.md#base64) | 傳回字串的 base64 編碼版本。 |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | 傳回 base64 編碼字串的二進位版本。 |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | 傳回 base64 編碼字串的字串版本。 |
| [binary](control-flow-expression-language-functions.md#binary) | 傳回輸入值的二進位版本。 |
| [bool](control-flow-expression-language-functions.md#bool) | 傳回輸入值的布林值版本。 |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | 從一個或多個參數中傳回第一個非 Null 值。 |
| [createArray](control-flow-expression-language-functions.md#createArray) | 從多個輸入傳回陣列。 |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | 傳回輸入值的資料 URI。 |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | 傳回資料 URI 的二進位版本。 |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | 傳回資料 URI 的字串版本。 |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | 傳回 base64 編碼字串的字串版本。 |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | 傳回資料 URI 的二進位版本。 |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | 傳回以已解碼版本取代逸出字元的字串。 |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | 傳回以逸出字元取代 URL 中 Unsafe 字元的字串。 |
| [float](control-flow-expression-language-functions.md#float) | 傳回輸入值的浮點數。 |
| [int](control-flow-expression-language-functions.md#int) | 傳回字串的整數版本。 |
| [json](control-flow-expression-language-functions.md#json) | 傳回字串或 XML 的 JavaScript 物件標記法 (JSON) 類型值或物件。 |
| [string](control-flow-expression-language-functions.md#string) | 傳回輸入值的字串版本。 |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | 藉由以逸出字元取代 URL 中的 Unsafe 字元，傳回輸入值的 URI 編碼版本。 |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | 傳回 URI 編碼字串的二進位版本。 |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | 傳回 URI 編碼字串的字串版本。 |
| [xml](control-flow-expression-language-functions.md#xml) | 傳回字串的 SML 版本。 |
| [xpath](control-flow-expression-language-functions.md#xpath) | 檢查 XML 中是否有符合 XPath (XML 路徑語言) 運算式的節點或值，並傳回符合的節點或值。 |

## <a name="math-functions"></a>數學函數  
 這些函式可用於任一類型的數字︰**整數**和**浮點數**。  

| 數學函式 | Task |
| ------------- | ---- |
| [新增](control-flow-expression-language-functions.md#add) | 傳回兩個數字相加的結果。 |
| [div](control-flow-expression-language-functions.md#div) | 傳回兩個數字相除的結果。 |
| [max](control-flow-expression-language-functions.md#max) | 從數字集合或陣列中傳回最大值。 |
| [min](control-flow-expression-language-functions.md#min) | 從數字集合或陣列中傳回最小值。 |
| [mod](control-flow-expression-language-functions.md#mod) | 傳回兩數相除的餘數。 |
| [mul](control-flow-expression-language-functions.md#mul) | 傳回將兩數相乘的乘積。 |
| [rand](control-flow-expression-language-functions.md#rand) | 從指定範圍傳回隨機整數。 |
| [range](control-flow-expression-language-functions.md#range) | 傳回從指定整數開始的整數陣列。 |
| [sub](control-flow-expression-language-functions.md#sub) | 傳回第一個數字減去第二個數字的結果。 |
  
## <a name="date-functions"></a>日期函式  

| 日期或時間函式 | Task |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | 將天數加入時間戳記。 |
| [addHours](control-flow-expression-language-functions.md#addHours) | 將時數加入時間戳記。 |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | 將分鐘數加入時間戳記。 |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | 將秒數加入時間戳記。 |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | 將時間單位數字加入時間戳記。 另請參閱 [getFutureTime](control-flow-expression-language-functions.md#getFutureTime)。 |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | 將時間戳記從國際標準時間 (UTC) 轉換為目標時區。 |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | 將時間戳記從來源時區轉換為目標時區。 |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | 將時間戳記從來源時區轉換為國際標準時間 (UTC)。 |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | 傳回時間戳記中的當月日期元件。 |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | 傳回時間戳記中的星期幾元件。 |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | 傳回時間戳記中一年的第幾天元件。 |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | 以選用格式將時間戳記當作字串傳回。 |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | 傳回目前時間戳記加上指定時間單位的結果。 另請參閱 [addToTime](control-flow-expression-language-functions.md#addToTime)。 |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | 傳回目前時間戳記減去指定時間單位的結果。 另請參閱 [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime)。 |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | 傳回時間戳記中當天的起始點。 |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | 傳回時間戳記中小時的起始點。 |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | 傳回時間戳記中月份的起始點。 |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | 從時間戳記減去時間單位數字。 另請參閱 [getPastTime](control-flow-expression-language-functions.md#getPastTime)。 |
| [ticks](control-flow-expression-language-functions.md#ticks) | 傳回指定時間戳記的 `ticks` 屬性值。 |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | 傳回目前的時間戳記作為字串。 |

## <a name="function-reference"></a>函式參考

本節依字母順序列出所有可用的函式。

<a name="add"></a>

### <a name="add"></a>add

傳回兩個數字相加的結果。

```
add(<summand_1>, <summand_2>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | 是 | 整數、浮點數或混合 | 要相加的數字 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | -----| ----------- |
| <*result-sum*> | 整數或浮點數 | 指定數字相加的結果 |
||||

*範例*

此範例會相加指定的數字：

```
add(1, 1.5)
```

並傳回此結果：`2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

將天數加入時間戳記。

```
addDays('<timestamp>', <days>, '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*days*> | 是 | 整數 | 要加入的天數 (正數或負數) |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 時間戳記加上指定的天數  |
||||

*範例 1*

此範例會在指定的時間戳記中加上 10 天：

```
addDays('2018-03-15T13:00:00Z', 10)
```

並傳回此結果：`"2018-03-25T00:00:0000000Z"`

*範例 2*

此範例會從指定的時間戳記中減去五天：

```
addDays('2018-03-15T00:00:00Z', -5)
```

並傳回此結果：`"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

將時數加入時間戳記。

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*hours*> | 是 | 整數 | 要加入的時數 (正數或負數) |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 時間戳記加上指定的時數  |
||||

*範例 1*

此範例會在指定的時間戳記中加上 10 小時：

```
addHours('2018-03-15T00:00:00Z', 10)
```

並傳回此結果：`"2018-03-15T10:00:0000000Z"`

*範例 2*

此範例會從指定的時間戳記中減去五小時：

```
addHours('2018-03-15T15:00:00Z', -5)
```

並傳回此結果：`"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

將分鐘數加入時間戳記。

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*minutes*> | 是 | 整數 | 要加入的分鐘數 (正數或負數) |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 時間戳記加上指定的分鐘數 |
||||

*範例 1*

此範例會在指定的時間戳記中加上 10 分鐘：

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

並傳回此結果：`"2018-03-15T00:20:00.0000000Z"`

*範例 2*

此範例會從指定的時間戳記中減去五分鐘：

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

並傳回此結果：`"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

將秒數加入時間戳記。

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*seconds*> | 是 | 整數 | 要加入的秒數 (正數或負數) |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 時間戳記加上指定的秒數  |
||||

*範例 1*

此範例會在指定的時間戳記中加上 10 秒鐘：

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

並傳回此結果：`"2018-03-15T00:00:10.0000000Z"`

*範例 2*

此範例會在指定的時間戳記中減去 5 秒鐘：

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

並傳回此結果：`"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

將時間單位數字加入時間戳記。
另請參閱 [getFutureTime()](#getFutureTime)。

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*interval*> | 是 | 整數 | 要加入的指定時間單位數字 |
| <*timeUnit*> | 是 | String | 與 *interval* 搭配使用的時間單位："Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 時間戳記加上指定的時間單位數字  |
||||

*範例 1*

此範例會在指定的時間戳記中加上 1 天：

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

並傳回此結果：`"2018-01-02T00:00:00.0000000Z"`

*範例 2*

此範例會在指定的時間戳記中加上 1 天：

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

並傳回選用 "D" 格式的結果：`"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>和

檢查是否所有運算式都是 True。
在所有運算式都是 True 時，傳回 True，或至少有一個運算式為 False 時傳回 False。

```
and(<expression1>, <expression2>, ...)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | 是 | Boolean | 要檢查的運算式 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | -----| ----------- |
| true 或 false | Boolean | 所有運算式都是 True 時，即傳回 True。 至少一個運算式為 False 時，則傳回 False。 |
||||

*範例 1*

這些範例會檢查指定的布林值是否都是 True：

```
and(true, true)
and(false, true)
and(false, false)
```

並傳回下列結果：

* 第一個範例：兩個運算式都是 True，所以會傳回 `true`。
* 第二個範例：一個運算式為 False，所以會傳回 `false`。
* 第三個範例：兩個運算式都是 False，所以會傳回 `false`。

*範例 2*

這些範例會檢查指定的運算式是否都是 True：

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

並傳回下列結果：

* 第一個範例：兩個運算式都是 True，所以會傳回 `true`。
* 第二個範例：一個運算式為 False，所以會傳回 `false`。
* 第三個範例：兩個運算式都是 False，所以會傳回 `false`。

<a name="array"></a>

### <a name="array"></a>array

從單一指定輸入傳回的陣列。
關於多個輸入的資訊，請參閱 [createArray()](#createArray)。

```
array('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 建立陣列的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | 包含單一指定輸入的陣列 |
||||

*範例*

此範例會從 "hello" 字串建立陣列：

```
array('hello')
```

並傳回此結果：`["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

傳回字串的 base64 編碼版本。

```
base64('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 輸入字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | 輸入字串的 base64 編碼版本 |
||||

*範例*

此範例會將 "hello" 字串轉換為 base64 編碼的字串：

```
base64('hello')
```

並傳回此結果：`"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

傳回 base64 編碼字串的二進位版本。

```
base64ToBinary('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換的 base64 編碼字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | String | base64 編碼字串的二進位版本 |
||||

*範例*

此範例會將 "aGVsbG8=" base64 編碼的字串轉換為二進位字串：

```
base64ToBinary('aGVsbG8=')
```

並傳回此結果：

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

傳回 base64 編碼字串的字串版本，也就是有效地解碼 base64 字串。
使用此函式而非 [decodeBase64()](#decodeBase64)。
雖然這兩個函數的運作方式相同，但是較常使用 `base64ToString()`。

```
base64ToString('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要解碼的 base64 編碼字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | 傳回 base64 編碼字串的字串版本 |
||||

*範例*

此範例會將 "aGVsbG8=" base64 編碼的字串轉換為單純字串：

```
base64ToString('aGVsbG8=')
```

並傳回此結果：`"hello"`

<a name="binary"></a>

### <a name="binary"></a>BINARY

傳回字串的二進位版本。

```
binary('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | 指定字串的二進位版本 |
||||

*範例*

此範例會將 "hello" 字串轉換為二進位字串：

```
binary('hello')
```

並傳回此結果：

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

傳回值的布林值版本。

```
bool(<value>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 任意 | 要轉換的值 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 指定值的布林值版本 |
||||

*範例*

這些範例會將指定的值轉換為布林值：

```
bool(1)
bool(0)
```

並傳回下列結果：

* 第一個範例：`true`
* 第二個範例：`false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

從一個或多個參數中傳回第一個非 Null 值。
空白字串、空白陣列和空白物件不是 null。

```
coalesce(<object_1>, <object_2>, ...)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | 是 | 任何類型，可以是混合類型 | 要檢查是否有 Null 的一個或多個項目 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | 任意 | 第一個不是 Null 的項目或值。 如果所有參數都是 Null，則此函式會傳回 Null。 |
||||

*範例*

這些範例會從指定值傳回第一個非 Null 的值，或是，如果所有值都是 Null，則傳回 Null：

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

並傳回下列結果：

* 第一個範例：`true`
* 第二個範例：`"hello"`
* 第三個範例：`null`

<a name="concat"></a>

### <a name="concat"></a>concat

結合兩個或多個字串，並傳回合併的字串。

```
concat('<text1>', '<text2>', ...)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | 是 | String | 要結合的至少兩個字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*text1text2...* > | String | 從合併輸入字串中建立的字串 |
||||

*範例*

此範例會合併 "Hello" 和 "World" 字串：

```
concat('Hello', 'World')
```

並傳回此結果：`"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

檢查集合是否具有特定項目。
找到項目時，傳回 True，或找不到項目時，傳回 False。
此函式會區分大小寫。

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

具體而言，此函數會用在這些集合類型上：

* 要尋找「子字串」的「字串」
* 要尋找「值」的「陣列」
* 要尋找「索引碼」的「字典」

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字串、陣列或字典 | 要檢查的集合 |
| <*value*> | 是 | 個別的字串、陣列或字典 | 要尋找的項目 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 找到項目時，傳回 True。 找不到項目時，傳回 False。 |
||||

*範例 1*

此範例會檢查字串 "hello world" 是否有子字串 "world"，並傳回 True：

```
contains('hello world', 'world')
```

*範例 2*

此範例會檢查字串 "hello world" 是否有子字串 "universe"，並傳回 False：

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

將時間戳記從國際標準時間 (UTC) 轉換為目標時區。

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*destinationTimeZone*> | 是 | String | 目標時區的名稱。 如需時區名稱，請參閱 [Microsoft 時區索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但您可能必須從時區名稱中移除任何標點符號。 |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | 轉換為目標時區的時間戳記 |
||||

*範例 1*

此範例會將時間戳記轉換為指定的時區：

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

並傳回此結果：`"2018-01-01T00:00:00Z"`

*範例 2*

此範例會將時間戳記轉換為指定的時區和格式：

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

並傳回此結果：`"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

將時間戳記從來源時區轉換為目標時區。

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*sourceTimeZone*> | 是 | String | 來源時區的名稱。 如需時區名稱，請參閱 [Microsoft 時區索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但您可能必須從時區名稱中移除任何標點符號。 |
| <*destinationTimeZone*> | 是 | String | 目標時區的名稱。 如需時區名稱，請參閱 [Microsoft 時區索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但您可能必須從時區名稱中移除任何標點符號。 |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | 轉換為目標時區的時間戳記 |
||||

*範例 1*

此範例會將來源時區轉換為目標時區：

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

並傳回此結果：`"2018-01-01T00:00:00.0000000"`

*範例 2*

此範例會將時區轉換為指定的時區和格式：

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

並傳回此結果：`"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

將時間戳記從來源時區轉換為國際標準時間 (UTC)。

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*sourceTimeZone*> | 是 | String | 來源時區的名稱。 如需時區名稱，請參閱 [Microsoft 時區索引值](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)，但您可能必須從時區名稱中移除任何標點符號。 |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | 轉換為 UTC 的時間戳記 |
||||

*範例 1*

此範例會將時間戳記轉換為 UTC：

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

並傳回此結果：`"2018-01-01T08:00:00.0000000Z"`

*範例 2*

此範例會將時間戳記轉換為 UTC：

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

並傳回此結果：`"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

從多個輸入傳回陣列。
如需單一輸入陣列的資訊，請參閱 [array()](#array)。

```
createArray('<object1>', '<object2>', ...)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | 是 | 任何類型，但不能是混合 | 用來建立陣列的至少兩個項目 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | 從所有輸入項目建立的陣列 |
||||

*範例*

此範例會從以下輸入建立陣列：

```
createArray('h', 'e', 'l', 'l', 'o')
```

並傳回此結果：`["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

傳回字串的資料統一資源識別項 (URI)。

```
dataUri('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | 輸入字串的資料 URI |
||||

*範例*

此範例會針對 "hello" 字串建立資料 URI：

```
dataUri('hello')
```

並傳回此結果：`"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

傳回資料統一資源識別項 (URI) 的二進位版本。
使用此函式而非 [decodeDataUri()](#decodeDataUri)。
雖然這兩個函數的運作方式相同，但是較常使用 `dataUriBinary()`。

```
dataUriToBinary('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換的資料 URI |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | 資料 URI 的二進位版本 |
||||

*範例*

此範例會建立此資料 URI 的二進位版本：

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

並傳回此結果：

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

傳回資料統一資源識別項 (URI) 的字串版本。

```
dataUriToString('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換的資料 URI |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | 資料 URI 的字串版本 |
||||

*範例*

此範例會建立此資料 URI 的字串：

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

並傳回此結果：`"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

傳回時間戳記中的當月日期。

```
dayOfMonth('<timestamp>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*day-of-month*> | 整數 | 在指定時間戳記中的當月日期 |
||||

*範例*

此範例會傳回此時間戳記中的當月日期數字：

```
dayOfMonth('2018-03-15T13:27:36Z')
```

並傳回此結果：`15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

從時間戳記傳回當週的第幾天。

```
dayOfWeek('<timestamp>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*day-of-week*> | 整數 | 在指定時間戳記上那一週的第幾天，其中星期日是 0、星期一是 1，依此類推 |
||||

*範例*

此範例會從此時間戳記傳回當週的第幾天：

```
dayOfWeek('2018-03-15T13:27:36Z')
```

並傳回此結果：`3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

從時間戳記傳回一年的第幾天。

```
dayOfYear('<timestamp>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*day-of-year*> | 整數 | 在指定時間戳記上那一年的第幾天 |
||||

*範例*

此範例會從此時間戳記傳回一年的第幾天：

```
dayOfYear('2018-03-15T13:27:36Z')
```

並傳回此結果：`74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

傳回 base64 編碼字串的字串版本，也就是有效地解碼 base64 字串。
請考慮使用 [base64ToString()](#base64ToString)，而非 `decodeBase64()`。
雖然這兩個函數的運作方式相同，但是較常使用 `base64ToString()`。

```
decodeBase64('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要解碼的 base64 編碼字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | 傳回 base64 編碼字串的字串版本 |
||||

*範例*

此範例會建立 base64 編碼字串的字串：

```
decodeBase64('aGVsbG8=')
```

並傳回此結果：`"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

傳回資料統一資源識別項 (URI) 的二進位版本。
請考慮使用 [dataUriToBinary()](#dataUriToBinary)，而非 `decodeDataUri()`。
雖然這兩個函數的運作方式相同，但是較常使用 `dataUriToBinary()`。

```
decodeDataUri('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要解碼的資料 URI 字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | 資料 URI 字串的二進位版本 |
||||

*範例*

此範例會傳回此資料 URI 的二進位版本：

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

並傳回此結果：

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

傳回以已解碼版本取代逸出字元的字串。

```
decodeUriComponent('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 其逸出字元需要解碼的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | 更新後的字串，其中逸出字元已解碼 |
||||

*範例*

此範例會以已解碼的版本取代此字串中的逸出字元：

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

並傳回此結果：`"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

傳回兩數相除的整數結果。
若要取得餘數，請參閱 [mod()](#mod)。

```
div(<dividend>, <divisor>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | 是 | 整數或浮點數 | 要除以「除數」的數字 |
| <*divisor*> | 是 | 整數或浮點數 | 要除「被除數」的數字，但不能為 0 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*quotient-result*> | 整數 | 第一個數字除以第二個數字的整數結果 |
||||

*範例*

這兩個範例會將第一個數字除以第二個數字：

```
div(10, 5)
div(11, 5)
```

並傳回此結果：`2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

傳回字串的統一資源識別項 (URI) 編碼版本，以逸出字元取代 URL 中的 Unsafe 字元。
請考慮使用 [uriComponent()](#uriComponent)，而非 `encodeUriComponent()`。
雖然這兩個函數的運作方式相同，但是較常使用 `uriComponent()`。

```
encodeUriComponent('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換成 URI 編碼格式的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | 具有逸出字元的 URI 編碼字串 |
||||

*範例*

此範例會建立此字串的 URI 編碼版本：

```
encodeUriComponent('https://contoso.com')
```

並傳回此結果：`"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

檢查集合是否是空的。
集合若是空的，傳回 True，或集合若不是空的，則傳回 False。

```
empty('<collection>')
empty([<collection>])
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字串、陣列或物件 | 要檢查的集合 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 若集合是空的，傳回 True。 若不是空的，傳回 False。 |
||||

*範例*

這些範例會檢查指定的集合是否是空的：

```
empty('')
empty('abc')
```

並傳回下列結果：

* 第一個範例：傳遞空字串，所以函數傳回 `true`。
* 第二個範例：傳遞 "abc" 字串，所以函數傳回 `false`。

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

檢查字串是否以特定的子字串結束。
找到子字串時，傳回 True，或找不到子字串時，傳回 False。
此函式不區分大小寫。

```
endsWith('<text>', '<searchText>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 要檢查的字串 |
| <*searchText*> | 是 | String | 要尋找的結尾子字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false  | Boolean | 找到結尾子字串時，傳回 True。 找不到項目時，傳回 False。 |
||||

*範例 1*

此範例會檢查 "hello world" 字串是否以 "world" 字串結尾：

```
endsWith('hello world', 'world')
```

並傳回此結果：`true`

*範例 2*

此範例會檢查 "hello world" 字串是否以 "universe" 字串結尾：

```
endsWith('hello world', 'universe')
```

並傳回此結果：`false`

<a name="equals"></a>

### <a name="equals"></a>equals

檢查兩個值、運算式或物件是否相等。
兩個項目相等時，傳回 True，或兩個項目不相等時，傳回 False。

```
equals('<object1>', '<object2>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | 是 | 各種類型 | 要比較的值、運算式或物件 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 當兩個項目相等時，傳回 True。 當兩個項目不相等時，傳回 False。 |
||||

*範例*

這些範例會檢查指定的輸入是否相等。

```
equals(true, 1)
equals('abc', 'abcd')
```

並傳回下列結果：

* 第一個範例：兩個值相等，所以函數傳回 `true`。
* 第二個範例：兩個值不相等，所以函式傳回 `false`。

<a name="first"></a>

### <a name="first"></a>first

傳回字串或陣列中的第一個項目。

```
first('<collection>')
first([<collection>])
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字串或陣列 | 要從中尋找第一個項目的集合 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | 任意 | 集合中的第一個項目 |
||||

*範例*

這些範例會尋找以下集合中第一個項目：

```
first('hello')
first(createArray(0, 1, 2))
```

並傳回下列結果：

* 第一個範例：`"h"`
* 第二個範例：`0`

<a name="float"></a>

### <a name="float"></a>FLOAT

將浮點數的字串版本轉換為實際浮點數。

```
float('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 具有有效浮點數要轉換的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | 所指定字串的浮點數 |
||||

*範例*

此範例會建立此浮點數的字串版本：

```
float('10.333')
```

並傳回此結果：`10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

傳回指定格式的時間戳記。

```
formatDateTime('<timestamp>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | 所指定格式的更新時間戳記 |
||||

*範例*

此範例會將時間戳記轉換為指定格式：

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

並傳回此結果：`"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

傳回目前時間戳記加上指定時間單位的結果。

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | 是 | 整數 | 要加入的指定時間單位數字 |
| <*timeUnit*> | 是 | String | 與 *interval* 搭配使用的時間單位："Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 目前時間戳記加上指定的時間單位數字 |
||||

*範例 1*

假設目前的時間戳記是 "2018-03-01T00:00:00.0000000Z"。
此範例會在時間戳記中加上 5 天：

```
getFutureTime(5, 'Day')
```

並傳回此結果：`"2018-03-06T00:00:00.0000000Z"`

*範例 2*

假設目前的時間戳記是 "2018-03-01T00:00:00.0000000Z"。
此範例會加上五天，並將結果轉換為 "D" 格式：

```
getFutureTime(5, 'Day', 'D')
```

並傳回此結果：`"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

傳回目前時間戳記減去指定時間單位的結果。

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*interval*> | 是 | 整數 | 要減去的指定時間單位數字 |
| <*timeUnit*> | 是 | String | 與 *interval* 搭配使用的時間單位："Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 目前時間戳記減去指定的時間單位數字 |
||||

*範例 1*

假設目前的時間戳記是 "2018-02-01T00:00:00.0000000Z"。
此範例會從此時間戳記中減去五天：

```
getPastTime(5, 'Day')
```

並傳回此結果：`"2018-01-27T00:00:00.0000000Z"`

*範例 2*

假設目前的時間戳記是 "2018-02-01T00:00:00.0000000Z"。
此範例會減去五天，並將結果轉換為 "D" 格式：

```
getPastTime(5, 'Day', 'D')
```

並傳回此結果：`"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

檢查第一個值是否大於第二個值。
當第一個值比較大時，傳回 True，或當第一個值比較小時，傳回 False。

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整數、浮點數或字串 | 要檢查其是否大於第二個值的第一個值 |
| <*compareTo*> | 是 | 個別的整數、浮點數或字串 | 比較值 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 當第一個值大於第二個值時，傳回 True。 當第一個值等於或小於第二個值時，傳回 False。 |
||||

*範例*

以下範例會檢查第一個值是否大於第二個值：

```
greater(10, 5)
greater('apple', 'banana')
```

並傳回下列結果：

* 第一個範例：`true`
* 第二個範例：`false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

檢查第一個值是否大於或等於第二個值。
當第一個值較大或相等時，傳回 True，或當第一個值較小時，傳回 False。

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整數、浮點數或字串 | 要檢查其是否大於或等於第二個值的第一個值 |
| <*compareTo*> | 是 | 個別的整數、浮點數或字串 | 比較值 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 當第一個值大於或等於第二個值時，傳回 True。 當第一個值小於第二個值時，傳回 False。 |
||||

*範例*

以下範例會檢查第一個值是否大於或等於第二個值：

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

並傳回下列結果：

* 第一個範例：`true`
* 第二個範例：`false`

<a name="guid"></a>

### <a name="guid"></a>guid

以字串形式產生唯一識別碼 (GUID)，例如 "c2ecc88d-88c8-4096-912c-d6f2e2b138ce"：

```
guid()
```

此外，除了預設格式 "D" (以連字號分隔的 32 個數字)，您可以指定不同格式的 GUID。

```
guid('<format>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*format*> | 否 | String | 所傳回 GUID 的單一[格式規範](https://msdn.microsoft.com/library/97af8hh4)。 預設格式為 "D"，但您可以使用 "N"、"D"、"B"、"P" 或 "X"。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | 隨機產生的 GUID |
||||

*範例*

此範例會產生相同 GUID，不過格式是 32 個以連字號分隔的數字，並以括號括住：

```
guid('P')
```

並傳回此結果：`"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

檢查運算式是 True 或 False。
根據結果，傳回指定的值。

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | 是 | Boolean | 要檢查的運算式 |
| <*valueIfTrue*> | 是 | 任意 | 運算式為 True 時要傳回的值 |
| <*valueIfFalse*> | 是 | 任意 | 運算式為 False 時要傳回的值 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | 任意 | 根據運算式為 True 或 False，傳回的指定值 |
||||

*範例*

此範例會傳回 `"yes"`，因為指定的運算式傳回 True。
否則，此範例會傳回 `"no"`：

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

傳回子字串的起始位置或索引值。
此函式不區分大小寫，而且索引以數字 0 開頭。

```
indexOf('<text>', '<searchText>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 具有子字串要尋找的字串 |
| <*searchText*> | 是 | String | 要尋找的子字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*index-value*>| 整數 | 所指定子字串的起始位置或索引值。 <p>如果找不到該字串，傳回數字 -1。 |
||||

*範例*

此範例會在 "hello world" 字串中，尋找 "world" 子字串的起始索引值：

```
indexOf('hello world', 'world')
```

並傳回此結果：`6`

<a name="int"></a>

### <a name="int"></a>int

傳回字串的整數版本。

```
int('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*integer-result*> | 整數 | 所指定字串的整數版本 |
||||

*範例*

此範例會建立字串 "10" 的整數版本：

```
int('10')
```

並傳回此結果：`10`

<a name="json"></a>

### <a name="json"></a>json

傳回字串或 XML 的 JavaScript 物件標記法 (JSON) 類型值或物件。

```
json('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 字串或 XML | 要轉換的字串或 XML |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*JSON-result*> | JSON 原生類型或物件 | 所指定字串或 XML 的 JSON 原生類型值或物件。 如果字串為 Null，函式會傳回空物件。 |
||||

*範例 1*

此範例會將此字串轉換為 JSON 值：

```
json('[1, 2, 3]')
```

並傳回此結果：`[1, 2, 3]`

*範例 2*

此範例會將此字串轉換為 JSON：

```
json('{"fullName": "Sophia Owen"}')
```

並傳回此結果：

```
{
  "fullName": "Sophia Owen"
}
```

*範例 3*

此範例會將此 XML 轉換為 JSON：

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

並傳回此結果：

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>交集

在指定的多個集合中，傳回「只有」共同項目的集合。
項目若要出現在結果中，必須出現在所有傳遞至此函式的集合中。
如果一個或多個項目有相同的名稱，則具有該名稱的最後一個項目會出現在結果中。

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | 是 | 陣列或物件，但不可以兩者並存 | 您想要其中「只有」共同項目的集合 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*common-items*> | 個別的陣列或物件 | 在指定的多個集合中，「只有」共同項目的集合 |
||||

*範例*

此範例會從以下陣列中找出共同項目：

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

並傳回的「只有」這些項目的陣列：`[1, 2]`

<a name="join"></a>

### <a name="join"></a>Join

傳回具有陣列中所有項目的字串，並以「分隔符號」將每個字元隔開。

```
join([<collection>], '<delimiter>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | Array | 要將其項目聯結的陣列 |
| <*delimiter*> | 是 | String | 在結果字串中，要出現在每個字元之間的分隔符號 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | 從指定陣列中所有項目建立的結果字串 |
||||

*範例*

此範例會從此陣列中所有項目建立字串，並以指定字元作為分隔符號：

```
join(createArray('a', 'b', 'c'), '.')
```

並傳回此結果：`"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

傳回集合中的最後一個項目。

```
last('<collection>')
last([<collection>])
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字串或陣列 | 要從中尋找最後一個項目的集合 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | 個別的字串或陣列 | 集合中的最後一個項目 |
||||

*範例*

這些範例會尋找以下集合中最後一個項目：

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

並傳回下列結果：

* 第一個範例：`"d"`
* 第二個範例：`3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

傳回子字串最後一次出現的起始位置或索引值。
此函式不區分大小寫，而且索引以數字 0 開頭。

```
lastIndexOf('<text>', '<searchText>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 具有子字串要尋找的字串 |
| <*searchText*> | 是 | String | 要尋找的子字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | 整數 | 指定的子字串最後一次出現的起始位置或索引值。 <p>如果找不到該字串，傳回數字 -1。 |
||||

*範例*

此範例會在 "hello world" 字串中，尋找 "world" 子字串最後一次出現的起始索引值：

```
lastIndexOf('hello world', 'world')
```

並傳回此結果：`6`

<a name="length"></a>

### <a name="length"></a>長度

傳回集合中的項目數目。

```
length('<collection>')
length([<collection>])
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字串或陣列 | 要計算其項目數的集合 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*length-or-count*> | 整數 | 集合中的項目數目 |
||||

*範例*

這些範例會計算以下集合中的項目數：

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

並傳回此結果：`4`

<a name="less"></a>

### <a name="less"></a>less

檢查第一個值是否小於第二個值。
當第一個值較小時，傳回 True，或當第一個值較大時，傳回 False。

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整數、浮點數或字串 | 要檢查其是否小於第二個值的第一個值 |
| <*compareTo*> | 是 | 個別的整數、浮點數或字串 | 比較項目 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 當第一個值小於第二個值時，傳回 True。 當第一個值等於或大於第二個值時，傳回 False。 |
||||

*範例*

以下範例會檢查第一個值是否小於第二個值。

```
less(5, 10)
less('banana', 'apple')
```

並傳回下列結果：

* 第一個範例：`true`
* 第二個範例：`false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

檢查第一個值是否小於或等於第二個值。
當第一個值較小或相等時，傳回 True，或當第一個值較大時，傳回 False。

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 整數、浮點數或字串 | 要檢查其是否小於或等於第二個值的第一個值 |
| <*compareTo*> | 是 | 個別的整數、浮點數或字串 | 比較項目 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false  | Boolean | 當第一個值小於或等於第二個值時，傳回 True。 當第一個值大於第二個值時，傳回 False。 |
||||

*範例*

以下範例會檢查第一個值是否小於或等於第二個值。

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

並傳回下列結果：

* 第一個範例：`true`
* 第二個範例：`false`

<a name="max"></a>

### <a name="max"></a>max

從具有數字的清單或陣列中傳回最大值 (包含首尾兩端的值)。

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 是 | 整數、浮點數或兩者並存 | 您需要其中最大值的數字集合 |
| [<*number1*>, <*number2*>, ...] | 是 | 陣列 - 整數、浮點數或兩者並存 | 您需要其中最大值的數字陣列 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*max-value*> | 整數或浮點數 | 所指定陣列或數字集合中的最大值 |
||||

*範例*

這些範例會從數字集合和陣列中取得最大值：

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

並傳回此結果：`3`

<a name="min"></a>

### <a name="min"></a>Min

從數字集合或陣列中傳回最小值。

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 是 | 整數、浮點數或兩者並存 | 您需要其中最小值的數字集合 |
| [<*number1*>, <*number2*>, ...] | 是 | 陣列 - 整數、浮點數或兩者並存 | 您需要其中最小值的數字陣列 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*min-value*> | 整數或浮點數 | 所指定數字集合或陣列中的最小值 |
||||

*範例*

這些範例會取得數字集合和陣列中的最小值：

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

並傳回此結果：`1`

<a name="mod"></a>

### <a name="mod"></a>mod

傳回兩數相除的餘數。
若要取得整數結果，請參閱 [div()](#div)。

```
mod(<dividend>, <divisor>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | 是 | 整數或浮點數 | 要除以「除數」的數字 |
| <*divisor*> | 是 | 整數或浮點數 | 要除「被除數」的數字，但不能為 0。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*modulo-result*> | 整數或浮點數 | 第一個數字除以第二個數字的餘數 |
||||

*範例*

此範例會將第一個數字除以第二個數字：

```
mod(3, 2)
```

並傳回此結果：`1`

<a name="mul"></a>

### <a name="mul"></a>mul

傳回將兩數相乘的乘積。

```
mul(<multiplicand1>, <multiplicand2>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | 是 | 整數或浮點數 | 要與「被乘數 2」 相乘的數字 |
| <*multiplicand2*> | 是 | 整數或浮點數 | 要與「被乘數 1」 相乘的數字 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*product-result*> | 整數或浮點數 | 第一個數字與第二個數字相乘的乘積 |
||||

*範例*

這些範例會將第一個數字與第二個數字相乘：

```
mul(1, 2)
mul(1.5, 2)
```

並傳回下列結果：

* 第一個範例：`2`
* 第二個範例：`3`

<a name="not"></a>

### <a name="not"></a>否

檢查運算式是否為 False。
運算式為 False 時，傳回 True，或運算式為 True 時，傳回 False。

```json
not(<expression>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*expression*> | 是 | Boolean | 要檢查的運算式 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 運算式為 False 時，傳回 True。 運算式為 True 時，傳回 False。 |
||||

*範例 1*

這些範例會檢查指定的運算式是否為 False：

```json
not(false)
not(true)
```

並傳回下列結果：

* 第一個範例：運算式為 False，所以函數傳回 `true`。
* 第二個範例：運算式為 True，所以函數傳回 `false`。

*範例 2*

這些範例會檢查指定的運算式是否為 False：

```json
not(equals(1, 2))
not(equals(1, 1))
```

並傳回下列結果：

* 第一個範例：運算式為 False，所以函數傳回 `true`。
* 第二個範例：運算式為 True，所以函數傳回 `false`。

<a name="or"></a>

### <a name="or"></a>或

檢查是否至少有一個運算式是 True。
至少有一個運算式是 True 時，傳回 True，或所有運算式都是 False 時，傳回 False。

```
or(<expression1>, <expression2>, ...)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | 是 | Boolean | 要檢查的運算式 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false | Boolean | 至少有一個運算式是 True 時，傳回 True。 所有運算式都是 False 時，即傳回 False。 |
||||

*範例 1*

這些範例會檢查是否至少有一個運算式是 True：

```json
or(true, false)
or(false, false)
```

並傳回下列結果：

* 第一個範例：至少有一個運算式為 True，所以函數傳回 `true`。
* 第二個範例：兩個運算式都是 False，所以函式傳回 `false`。

*範例 2*

這些範例會檢查是否至少有一個運算式是 True：

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

並傳回下列結果：

* 第一個範例：至少有一個運算式為 True，所以函數傳回 `true`。
* 第二個範例：兩個運算式都是 False，所以函式傳回 `false`。

<a name="rand"></a>

### <a name="rand"></a>rand

從指定範圍傳回隨機整數 (不包含範圍中的末端數字)。

```
rand(<minValue>, <maxValue>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | 是 | 整數 | 範圍中的最小整數 |
| <*maxValue*> | 是 | 整數 | 在範圍中，在函式所能傳回最大整數後面的整數 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*random-result*> | 整數 | 從指定範圍傳回的隨機整數 |
||||

*範例*

此範例會從指定範圍取得隨機整數，但不包含最大值：

```
rand(1, 5)
```

並傳回這些數字的其中一個作為結果：`1`、`2`、`3` 或 `4`

<a name="range"></a>

### <a name="range"></a>range

傳回從指定整數開始的整數陣列。

```
range(<startIndex>, <count>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | 是 | 整數 | 作為第一個項目起始陣列的整數值 |
| <*count*> | 是 | 整數 | 陣列中的整數數量 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | 從指定索引開始的整數陣列 |
||||

*範例*

此範例會建立從指定索引開始的整數陣列，且其中有指定數量的整數：

```
range(1, 4)
```

並傳回此結果：`[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>取代

使用指定字串取代子字串，並傳回結果字串。 此函式會區分大小寫。

```
replace('<text>', '<oldText>', '<newText>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 有子字串要取代的字串 |
| <*oldText*> | 是 | String | 要取代的子字串 |
| <*newText*> | 是 | String | 取代字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | 取代子字串後的更新字串 <p>如果找不到子字串，則傳回原始字串。 |
||||

*範例*

此範例會尋找 "the old string" 中的 "old" 字串，然後以"new" 取代 "old"：

```
replace('the old string', 'old', 'new')
```

並傳回此結果：`"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

移除集合前端的項目，並傳回「其他所有」項目。

```
skip([<collection>], <count>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | Array | 您想要從中移除項目的集合 |
| <*count*> | 是 | 整數 | 正整數，表示要移除的前端項目數量 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | 移除指定項目之後的更新集合 |
||||

*範例*

此範例會從指定陣列的前端移除一個項目，也就是數字 0：

```
skip(createArray(0, 1, 2, 3), 1)
```

並傳回具有剩餘項目的此陣列：`[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

根據原始字串中指定的分隔符號字元，傳回包含以逗號分隔之子字串的陣列。

```
split('<text>', '<delimiter>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 要根據原始字串中指定的分隔符號分隔成子字串的字串 |
| <*delimiter*> | 是 | String | 原始字串中用來作為分隔符號的字元 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | 包含來自原始字串並以逗號分隔之子字串的陣列 |
||||

*範例*

這個範例會根據指定來作為分隔符號的字元，建立包含來自指定字串之子字串的陣列：

```
split('a_b_c', '_')
```

而且會傳回此陣列作為結果：`["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

傳回時間戳記中當天的起始點。

```
startOfDay('<timestamp>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 指定的時間戳記，但以當天的零小時標記開始 |
||||

*範例*

此範例會尋找此時間戳記中當天的起始點：

```
startOfDay('2018-03-15T13:30:30Z')
```

並傳回此結果：`"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

傳回時間戳記中小時的起始點。

```
startOfHour('<timestamp>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 指定的時間戳記，但以小時的零分鐘標記開始 |
||||

*範例*

此範例會尋找此時間戳記中小時的起始點：

```
startOfHour('2018-03-15T13:30:30Z')
```

並傳回此結果：`"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

傳回時間戳記中月份的起始點。

```
startOfMonth('<timestamp>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 指定的時間戳記，但以當月第一天的零小時標記開始 |
||||

*範例*

此範例會傳回此時間戳記中月份的起始點：

```
startOfMonth('2018-03-15T13:30:30Z')
```

並傳回此結果：`"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

檢查字串是否以特定的子字串開始。
找到子字串時，傳回 True，或找不到子字串時，傳回 False。
此函式不區分大小寫。

```
startsWith('<text>', '<searchText>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 要檢查的字串 |
| <*searchText*> | 是 | String | 要尋找的起始字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| true 或 false  | Boolean | 找到起始子字串時，傳回 True。 找不到項目時，傳回 False。 |
||||

*範例 1*

此範例會檢查 "hello world" 字串是否以 "hello" 子字串開始：

```
startsWith('hello world', 'hello')
```

並傳回此結果：`true`

*範例 2*

此範例會檢查 "hello world" 字串是否以 "greetings" 子字串開始：

```
startsWith('hello world', 'greetings')
```

並傳回此結果：`false`

<a name="string"></a>

### <a name="string"></a>字串

傳回值的字串版本。

```
string(<value>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | 任意 | 要轉換的值 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*string-value*> | String | 指定值的字串版本 |
||||

*範例 1*

此範例會建立此數字的字串版本：

```
string(10)
```

並傳回此結果：`"10"`

*範例 2*

此範例會為指定的 JSON 物件建立字串，並使用反斜線字元 (\\) 作為雙引號 (") 的逸出字元。

```
string( { "name": "Sophie Owen" } )
```

並傳回此結果：`"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

傳回第一個數字減去第二個數字的結果。

```
sub(<minuend>, <subtrahend>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | 是 | 整數或浮點數 | 要從中減去「減數」的數字 |
| <*subtrahend*> | 是 | 整數或浮點數 | 從「被減數」中減去的數字 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*result*> | 整數或浮點數 | 從第一個數字減去第二個數字的結果 |
||||

*範例*

此範例會從第一個數字減去第二個數字：

```
sub(10.3, .3)
```

並傳回此結果：`10`

<a name="substring"></a>

### <a name="substring"></a>substring

從字串中傳回從指定位置或索引起始的字元。
索引值會以數字 0 開頭。

```
substring('<text>', <startIndex>, <length>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 您需要其中字元的字串 |
| <*startIndex*> | 是 | 整數 | 等於或大於 0 的正數，您想要將其用作起始位置或索引值 |
| <*length*> | 是 | 整數 | 子字串中您需要的字元數正數 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | 具有指定字元數目的子字串，並以來源字串中的指定索引位置起始 |
||||

*範例*

此範例會從指定字串建立 5 個字元的子字串，並且從索引值 6 起始：

```
substring('hello world', 6, 5)
```

並傳回此結果：`"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

從時間戳記減去時間單位數字。
另請參閱 [getPastTime](#getPastTime)。

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 包含時間戳記的字串 |
| <*interval*> | 是 | 整數 | 要減去的指定時間單位數字 |
| <*timeUnit*> | 是 | String | 與 *interval* 搭配使用的時間單位："Second"、"Minute"、"Hour"、"Day"、"Week"、"Month"、"Year" |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 時間戳記減去指定的時間單位數字 |
||||

*範例 1*

此範例會從此時間戳記中減去一天：

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

並傳回此結果：`"2018-01-01T00:00:00:0000000Z"`

*範例 2*

此範例會從此時間戳記中減去一天：

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

並傳回選用 "D" 格式的此結果：`"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

傳回集合中的前端項目。

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 是 | 字串或陣列 | 您需要其中項目的集合 |
| <*count*> | 是 | 整數 | 正整數，表示您需要的前端項目數量 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*subset*> 或 [<*subset*>] | 個別的字串或陣列 | 從原始集合前端取得指定項目數量的字串或陣列 |
||||

*範例*

此範例會從這些集合的前端取得指定數目的項目：

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

並傳回下列結果：

* 第一個範例：`"abc"`
* 第二個範例：`[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>刻度

傳回指定時間戳記的 `ticks` 屬性值。
一「刻度」是 100 奈秒的間隔。

```
ticks('<timestamp>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 是 | String | 時間戳記的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*ticks-number*> | 整數 | 自指定時間戳記以來的刻度數目 |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

傳回小寫格式的字串。 如果字串中的字元沒有小寫版本，則該字元在傳回的字串中會保持不變。

```
toLower('<text>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 要以小寫格式傳回的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | 小寫格式的原始字串 |
||||

*範例*

此範例會將此字串轉換為小寫：

```
toLower('Hello World')
```

並傳回此結果：`"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

傳回大寫格式的字串。 如果字串中的字元沒有大寫版本，則該字元在傳回的字串中會保持不變。

```
toUpper('<text>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 要以大寫格式傳回的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | 大寫格式的原始字串 |
||||

*範例*

此範例會將此字串轉換為大寫：

```
toUpper('Hello World')
```

並傳回此結果：`"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>修剪

移除字串的開頭和尾端空白字元，並傳回更新後的字串。

```
trim('<text>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*text*> | 是 | String | 要為其移除開頭和尾端空白字元的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | 不含開頭或尾端空白字元的原始字串更新版本 |
||||

*範例*

此範例會從 " Hello World  " 字串中移除開頭和尾端空白字元：

```
trim(' Hello World  ')
```

並傳回此結果：`"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

傳回具有指定集合中「所有」項目的集合。
出現在結果中的項目，可以出現在任何傳遞至此函式的集合中。 如果一個或多個項目有相同的名稱，則具有該名稱的最後一個項目會出現在結果中。

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | 是 | 陣列或物件，但不可以兩者並存 | 您想要其中「所有」項目的集合 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | 個別的陣列或物件 | 具有指定集合中所有項目的集合 - 不含重複值 |
||||

*範例*

此範例會從以下集合取得「所有」項目：

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

並傳回此結果：`[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

傳回字串的統一資源識別項 (URI) 編碼版本，以逸出字元取代 URL 中的 Unsafe 字元。
使用此函式而非 [encodeUriComponent()](#encodeUriComponent)。
雖然這兩個函數的運作方式相同，但是較常使用 `uriComponent()`。

```
uriComponent('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換成 URI 編碼格式的字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | 具有逸出字元的 URI 編碼字串 |
||||

*範例*

此範例會建立此字串的 URI 編碼版本：

```
uriComponent('https://contoso.com')
```

並傳回此結果：`"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

傳回統一資源識別項 (URI) 元件的二進位版本。

```
uriComponentToBinary('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要轉換的 URI 編碼字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | URI 編碼字串的二進位版本。 二進位內容是以 base64 編碼，而且由 `$content` 表示。 |
||||

*範例*

此範例會建立此 URI 編碼字串的二進位版本：

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

並傳回此結果：

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

傳回統一資源識別項 (URI) 編碼字串的字串版本，也就是有效地解碼 URI 編碼字串。

```
uriComponentToString('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 要解碼的 URI 編碼字串 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | URI 編碼字串的已解碼版本 |
||||

*範例*

此範例會建立此 URI 編碼字串的已解碼字串版本：

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

並傳回此結果：`"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

傳回目前的時間戳記。

```
utcNow('<format>')
```

您可以選擇性地以 <*format*> 參數指定不同格式。

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*format*> | 否 | String | [單一格式規範](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings)或[自訂格式模式](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 時間戳記的預設格式為 ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (yyyy-MM-ddTHH:mm:ss:fffffffK)，其符合 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) \(英文\) 並保留時區資訊。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | 目前的日期和時間 |
||||

*範例 1*

假設今天是 2018 年 4 月 15 日下午 1:00:00。
此範例會取得目前的時間戳記：

```
utcNow()
```

並傳回此結果：`"2018-04-15T13:00:00.0000000Z"`

*範例 2*

假設今天是 2018 年 4 月 15 日下午 1:00:00。
此範例會使用選擇性的 "D" 格式取得目前時間戳記：

```
utcNow('D')
```

並傳回此結果：`"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>Xml

傳回包含 JSON 物件的 XML 版字串。

```
xml('<value>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*value*> | 是 | String | 其中有 JSON 物件要轉換的字串 <p>JSON 物件必須只能有一個根屬性，且不可以是陣列。 <br>使用反斜線字元 (\\) 作為雙引號 (") 的逸出字元。 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | 所指定字串或 JSON 物件的編碼 XML |
||||

*範例 1*

此範例會為包含 JSON 物件的此字串建立 XML 版本：

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

並傳回此結果 XML：

```xml
<name>Sophia Owen</name>
```

*範例 2*

假設您有此 JSON 物件：

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

此範例會為包含此 JSON 物件的字串建立 XML：

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

並傳回此結果 XML：

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

檢查 XML 中是否有符合 XPath (XML 路徑語言) 運算式的節點或值，並傳回符合的節點或值。 XPath 運算式 (或 "XPath") 可協助您瀏覽 XML 文件結構，讓您可以在 XML 內容中選取節點或計算值。

```
xpath('<xml>', '<xpath>')
```

| 參數 | 必要 | 類型 | 描述 |
| --------- | -------- | ---- | ----------- |
| <*xml*> | 是 | 任意 | XML 字串，將對其搜尋是否有符合 XPath 運算式的值或節點 |
| <*xpath*> | 是 | 任意 | 用來尋找相符 XML 節點或值的 XPath 運算式 |
|||||

| 傳回值 | 類型 | 描述 |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | 只有單一節點符合指定的 XPath 運算式時會傳回 XML 節點 |
| <*value*> | 任意 | 只有單一值符合指定的 XPath 運算式時，會傳回 XML 節點的值 |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-或- </br>[<*value1*>, <*value2*>, ...] | Array | 陣列，其中有符合指定 XPath 運算式的 XML 節點或值 |
||||

*範例 1*

以範例 1 為基礎，此範例會尋找符合 `<count></count>` 節點的節點，並使用 `sum()` 函式將這些節點值相加：

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

並傳回此結果：`30`

*範例 2*

在此範例中，有兩個運算式會在指定的引數中 (其中包含具有命名空間的 XML) 尋找符合 `<location></location>` 節點的節點。 此運算式使用反斜線字元 (\\) 作為雙引號 (") 的逸出字元。

* 運算式 1

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* 運算式2 

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

以下是引數：

* 包含 XML 文件命名空間的此 XML `xmlns="http://contoso.com"`：

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* 此處其中一個 XPath 運算式：

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

以下是符合 `<location></location>` 節點的結果節點：

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*範例 3*

以範例 3 為基礎，此範例會尋找 `<location></location>` 節點中的值：

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

並傳回此結果：`"Paris"`

## <a name="next-steps"></a>後續步驟
如需可在運算式中使用之系統變數的清單，請參閱[系統變數](control-flow-system-variables.md)。
