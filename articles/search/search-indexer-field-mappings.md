---
title: 索引器中的欄位對應
titleSuffix: Azure Cognitive Search
description: 在索引子中配置欄位映射，以考慮欄位名稱和資料表示的差異。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e09741e841897032b8146dee67b79e0c26ea5cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275147"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>使用 Azure 認知搜索索引子進列欄位映射和轉換

使用 Azure 認知搜索索引子時，有時會發現輸入資料與目標索引的架構不完全符合。 在這些情況下，可以使用**欄位映射**在索引過程中重塑資料。

欄位對應在某些情況下很有用︰

* 資料來源具有名為 的`_id`欄位，但 Azure 認知搜索不允許以底線開頭的欄位名稱。 通過欄位映射，您可以有效地重命名欄位。
* 您希望從同一資料來源資料填充索引中的多個欄位。 例如，您可能希望將不同的分析器應用於這些欄位。
* 您希望使用來自多個資料來源的資料填充索引欄位，並且資料來源各自使用不同的欄位名稱。
* 您必須以 Base64 格式編碼或解碼資料。 欄位對應支援數個 **對應函式**，包括 Base64 編碼和解碼的函式。

> [!NOTE]
> Azure 認知搜索索引子的欄位映射功能提供了將資料欄位映射到索引欄位的簡單方法，並提供了一些資料轉換選項。 更複雜的資料可能需要預處理，才能將其重塑為易於索引的表單。
>
> Microsoft Azure 資料工廠是一個強大的基於雲的解決方案，用於導入和轉換資料。 您還可以在編制索引之前編寫代碼來轉換來源資料。 有關代碼示例，請參閱[模型關係資料和](search-example-adventureworks-modeling.md)[模型多級面。](search-example-adventureworks-multilevel-faceting.md)
>

## <a name="set-up-field-mappings"></a>設置欄位映射

欄位對應包含三個部分︰

1. `sourceFieldName`，表示資料來源中的欄位。 這是必要屬性。
2. 選擇性的 `targetFieldName`，表示搜尋索引中的欄位。 如果省略，則會使用資料來源中的相同名稱。
3. 選擇性的 `mappingFunction`，可以使用數個預先定義的函式之一轉換您的資料。 函式的完整清單 [如下](#mappingFunctions)。

欄位映射將添加到索引子定義的`fieldMappings`陣列中。

## <a name="map-fields-using-the-rest-api"></a>使用 REST API 映射欄位

使用[創建索引子](https://docs.microsoft.com/rest/api/searchservice/create-Indexer)API 請求創建新索引子時，可以添加欄位映射。 您可以使用[更新索引](https://docs.microsoft.com/rest/api/searchservice/update-indexer)器 API 要求管理現有索引子的欄位映射。

例如，下面介紹如何將源欄位映射到具有不同名稱的目標欄位：

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

可以在多個欄位映射中引用源欄位。 下面的示例演示如何"分叉"欄位，將同一源欄位複製到兩個不同的索引欄位：

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure 認知搜索使用不區分大小寫的比較來解決欄位映射中的欄位和函數名稱。 這很方便 (大小寫不需要完全正確)，但這表示資料來源或索引不能有只以大小寫區分的欄位。  
>
>

## <a name="map-fields-using-the-net-sdk"></a>使用 .NET SDK 映射欄位

使用[欄位映射](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping)類在 .NET SDK 中定義欄位映射，該類具有屬性`SourceFieldName`和`TargetFieldName`，以及可選`MappingFunction`的引用。

在構造索引子時，或者通過直接設置`Indexer.FieldMappings`屬性來指定欄位映射。

以下 C# 示例設置構造索引子時的欄位映射。

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>欄位對應函式

欄位映射函數在欄位存儲在索引之前轉換欄位的內容。 當前支援以下映射功能：

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>基64編碼功能

執行輸入字串的安全 URL ** Base64 編碼。 假設輸入以 UTF-8 編碼。

#### <a name="example---document-key-lookup"></a>示例 - 文檔金鑰查找

Azure 認知搜索文檔金鑰中只能顯示 URL 安全字元（因為客戶必須能夠使用[查找 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document)解決文檔問題）。 如果金鑰的源欄位包含 URL 不安全字元，則可以使用 函數`base64Encode`在索引時轉換它。 但是，文檔金鑰（轉換之前和之後）不能超過 1，024 個字元。

在搜索時檢索編碼的金鑰時，可以使用 函數`base64Decode`獲取原始鍵值，並使用該函數檢索來源文件。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

如果不包括映射函數的參數屬性，則它預設為 值`{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 認知搜索支援兩種不同的 Base64 編碼。 編碼和解碼同一欄位時，應使用相同的參數。 有關詳細資訊，請參閱[base64 編碼選項](#base64details)以決定要使用的參數。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>基64解碼功能

執行輸入字串的 Base64 解碼。 假定該輸入是 URL*安全*Base64 編碼的字串。

#### <a name="example---decode-blob-metadata-or-urls"></a>示例 - 解碼 Blob 中繼資料或 URL

來源資料可能包含 Base64 編碼的字串，如 Blob 中繼資料字串或 Web URL，您希望這些字串可以搜索為純文字。 在填充搜索索引`base64Decode`時，可以使用 函數將編碼的資料重新轉換為常規字串。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

如果不包括參數屬性，則它預設為 值`{"useHttpServerUtilityUrlTokenEncode" : true}`。

Azure 認知搜索支援兩種不同的 Base64 編碼。 編碼和解碼同一欄位時，應使用相同的參數。 有關詳細資訊，請參閱[base64 編碼選項](#base64details)以決定要使用的參數。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>基64編碼選項

Azure 認知搜索支援 URL 安全基 64 編碼和常規基 64 編碼。 在索引期間編碼的 base64 字串應稍後使用相同的編碼選項進行解碼，否則結果將不匹配原始。

`useHttpServerUtilityUrlTokenEncode`如果編碼和`useHttpServerUtilityUrlTokenDecode`解碼的 或 參數分別`true`設置為`base64Encode`，則的行為類似于[HttpServer 實用程式.UrlTokenEncode，](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)`base64Decode`並且的行為類似于[HttpServer 實用程式.UrlTokendecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)。

> [!WARNING]
> 如果`base64Encode`用於生成鍵值，`useHttpServerUtilityUrlTokenEncode`則必須設置為 true。 只有 URL 安全基 64 編碼可用於鍵值。 有關對鍵值中的字元的完整限制集，請參閱[azure 認知搜索&#41;&#40;命名規則](https://docs.microsoft.com/rest/api/searchservice/naming-rules)。

Azure 認知搜索中的 .NET 庫假定完整的 .NET 框架，該框架提供內置編碼。 `useHttpServerUtilityUrlTokenEncode`和`useHttpServerUtilityUrlTokenDecode`選項利用了這種內置功能。 如果您使用的是 .NET Core 或其他框架，我們建議將這些選項設置為並`false`直接調用框架的編碼和解碼函數。

下表比較 `00>00?00` 字串的不同 base64 編碼。 若要判斷您的 base64 函式需要的額外處理 (如果有的話)，將您的程式庫編碼函式套用在 `00>00?00` 字串，然後比較輸出與預期的輸出 `MDA-MDA_MDA`。

| 編碼 | Base64 編碼的輸出 | 程式庫編碼之後的額外處理 | 程式庫解碼之前的額外處理 |
| --- | --- | --- | --- |
| Base64 加填補 | `MDA+MDA/MDA=` | 使用 URL 安全的字元，並移除填補 | 使用標準 base64 字元，並加上填補 |
| Base64 無填補 | `MDA+MDA/MDA` | 使用 URL 安全的字元 | 使用標準 base64 字元 |
| URL 安全的 base64 填補加填補 | `MDA-MDA_MDA=` | 移除填補 | 加上填補 |
| URL 安全的 base64 無填補 | `MDA-MDA_MDA` | None | None |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>提取標記位置函數

使用指定的分隔符號分割字串欄位，並在分割結果的指定位置挑選權杖。

此函數使用以下參數：

* `delimiter`︰分割輸入字串時，用為分隔符號的字串。
* `position`：分割輸入字串後，要挑選的權杖以零為基底位置的整數。

例如，如果輸入是 `Jane Doe`，而 `delimiter` 是 `" "` (空格) 且 `position` 為 0，則結果是 `Jane`；如果 `position` 為 1，則結果是 `Doe`。 如果位置參考不存在的權杖，會傳回錯誤。

#### <a name="example---extract-a-name"></a>示例 - 提取名稱

資料來源包含 `PersonName` 欄位，而您想要將它編製為 `FirstName` 和 `LastName` 兩個不同欄位的索引。 您可以使用這個函式來分割以空格字元作為分隔符號的輸入。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToString集合函數

將格式化為字串 JSON 陣列的字串，轉換為可用來填入索引中 `Collection(Edm.String)` 欄位的字串陣列。

例如，輸入字串是 `["red", "white", "blue"]`，則 `Collection(Edm.String)` 類型的目標欄位會填入 `red`、`white`、`blue` 三個值。 若為無法剖析為 JSON 字串陣列的輸入值，會傳回錯誤。

#### <a name="example---populate-collection-from-relational-data"></a>示例 - 填充關係資料的集合

Azure SQL 資料庫沒有自然映射到`Collection(Edm.String)`Azure 認知搜索中的欄位的內置資料類型。 要填補字元串集合欄位，可以將來源資料作為 JSON 字串陣列進行預處理，`jsonArrayToStringCollection`然後使用映射函數。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

有關將關係資料轉換為索引集合欄位的詳細示例，請參閱[模型關係資料](search-example-adventureworks-modeling.md)。

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>url編碼功能

此功能可用於對字串進行編碼，以便它是"URL 安全"。 當與包含 URL 中不允許的字元的字串一起使用時，此函數將這些"不安全"字元轉換為字元實體等效項。 此功能使用 UTF-8 編碼格式。

#### <a name="example---document-key-lookup"></a>示例 - 文檔金鑰查找

`urlEncode`如果只轉換 URL 不安全字元，`base64Encode`同時保留其他字元，則函數可用作函數的替代方法。

假設輸入字串是`<hello>`- 然後類型`(Edm.String)`的目標欄位將填充值`%3chello%3e`

在搜索時檢索編碼的金鑰時，可以使用 函數`urlDecode`獲取原始鍵值，並使用該函數檢索來源文件。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>url 解碼功能

 此函數使用 UTF-8 編碼格式將 URL 編碼的字串轉換為解碼的字串。

 ### <a name="example---decode-blob-metadata"></a>示例 - 解碼 Blob 中繼資料

 如果某些 Azure 存儲用戶端包含非 ASCII 字元，則會自動對 Blob 中繼資料進行 url 編碼。 但是，如果要使此類中繼資料可搜索（如純文字），則可以使用`urlDecode`函數在填充搜索索引時將編碼資料重新轉換為常規字串。

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>固定長度編碼功能
 
 此函數將任何長度的字串轉換為固定長度字串。
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>示例 - 映射太長的文檔鍵
 
當遇到抱怨文檔金鑰長度超過 1024 個字元的錯誤時，可以應用此功能來縮短文檔金鑰的長度。

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```
