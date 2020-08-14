---
title: 索引器中的欄位對應
titleSuffix: Azure Cognitive Search
description: 在索引子中設定欄位對應，以考慮功能變數名稱和資料表示的差異。
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: 47a8d58d6ca0a8a04823fe09fb52490f13cfead7
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208756"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>使用 Azure 認知搜尋索引子的欄位對應和轉換

![索引子階段](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "索引子階段")

使用 Azure 認知搜尋索引子時，您有時會發現輸入資料不會完全符合目標索引的架構。 在這些情況下，您可以在編制索引程式期間使用 **欄位** 對應來重新塑造您的資料。

欄位對應在某些情況下很有用︰

* 您的資料來源具有名為的欄位 `_id` ，但 Azure 認知搜尋不允許以底線開頭的功能變數名稱。 欄位對應可讓您有效地重新命名欄位。
* 您想要從相同的資料來源資料填入索引中的幾個欄位。 例如，您可能會想要將不同的分析器套用至這些欄位。
* 您想要將來自多個資料來源的資料填入索引欄位，而每個資料來源都使用不同的功能變數名稱。
* 您必須以 Base64 格式編碼或解碼資料。 欄位對應支援數個 **對應函式**，包括 Base64 編碼和解碼的函式。

> [!NOTE]
> 索引子中的欄位對應是一個簡單的方式，可將資料欄位對應至索引欄位，並提供一些輕量資料轉換的功能。 較複雜的資料可能需要預先處理，才能將其重新調整為採用遭利用編制索引的表單。 您可以考慮的其中一個選項是 [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)。

## <a name="set-up-field-mappings"></a>設定欄位對應

欄位對應包含三個部分︰

1. `sourceFieldName`，表示資料來源中的欄位。 這是必要屬性。
2. 選擇性的 `targetFieldName`，表示搜尋索引中的欄位。 如果省略，則會使用資料來源中的相同名稱。
3. 選擇性的 `mappingFunction`，可以使用數個預先定義的函式之一轉換您的資料。 這可以同時套用在輸入和輸出欄位對應上。 函式的完整清單 [如下](#mappingFunctions)。

欄位對應會加入 `fieldMappings` 索引子定義的陣列中。

> [!NOTE]
> 如果沒有加入欄位對應，則索引子會假設資料來源欄位應對應至具有相同名稱的索引欄位。 加入欄位對應會移除 [來源] 和 [目標] 欄位的這些預設欄位對應。 某些索引子，例如 [blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)，會加入 [索引鍵] 欄位的預設欄位對應。

## <a name="map-fields-using-the-rest-api"></a>使用 REST API 對應欄位

當您使用 [建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API 要求建立新的索引子時，可以加入欄位對應。 您可以使用 [更新索引子](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API 要求來管理現有索引子的欄位對應。

例如，以下說明如何將來源欄位對應至具有不同名稱的目標欄位：

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

可以在多個欄位對應中參考來源欄位。 下列範例顯示如何「分叉」欄位，並將相同的來源欄位複製到兩個不同的索引欄位：

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure 認知搜尋會使用不區分大小寫比較來解析欄位對應中的欄位和函數名稱。 這很方便 (大小寫不需要完全正確)，但這表示資料來源或索引不能有只以大小寫區分的欄位。  
>
>

## <a name="map-fields-using-the-net-sdk"></a>使用 .NET SDK 對應欄位

您可以使用 [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) 類別定義 .net SDK 中的欄位對應，其具有屬性 `SourceFieldName` 和 `TargetFieldName` ，以及選擇性的 `MappingFunction` 參考。

您可以在建立索引子時指定欄位對應，或在稍後直接設定 `Indexer.FieldMappings` 屬性。

下列 c # 範例會在建立索引子時設定欄位對應。

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

欄位對應函式會先轉換欄位的內容，然後再將它儲存在索引中。 目前支援下列對應函數：

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode 函式

執行輸入字串的安全 URL ** Base64 編碼。 假設輸入以 UTF-8 編碼。

#### <a name="example---document-key-lookup"></a>範例-檔索引鍵查閱

Azure 認知搜尋檔金鑰中僅能顯示 URL 安全的字元 (因為客戶必須能夠使用 [查閱 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ) 來處理檔。 如果您金鑰的來源欄位包含不安全的 URL 字元，您可以使用函式 `base64Encode` 在索引時間進行轉換。 不過，檔索引鍵 (轉換前後) 不能超過1024個字元。

當您在搜尋時抓取編碼的金鑰時，您可以使用函式 `base64Decode` 來取得原始索引鍵值，並使用該值來抓取來源文件。

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

#### <a name="example---preserve-original-values"></a>範例-保留原始值

如果未指定欄位對應， [blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md) 會自動將欄位對應從 `metadata_storage_path` blob 的 URI 新增至索引鍵欄位。 這個值是以 Base64 編碼，因此可以安全地當做 Azure 認知搜尋檔金鑰使用。 下列範例顯示如何同時將 *URL 安全* 的 Base64 編碼版本對應 `metadata_storage_path` 至 `index_key` 欄位，並將原始值保留在 `metadata_storage_path` 欄位中：

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

如果您未包含對應函數的 parameters 屬性，它會預設為值 `{"useHttpServerUtilityUrlTokenEncode" : true}` 。

Azure 認知搜尋支援兩種不同的 Base64 編碼。 編碼和解碼相同的欄位時，您應該使用相同的參數。 如需詳細資訊，請參閱 [base64 編碼選項](#base64details) ，以決定要使用哪些參數。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 函式

執行輸入字串的 Base64 解碼。 輸入會假設為 *URL 安全* 的 Base64 編碼字串。

#### <a name="example---decode-blob-metadata-or-urls"></a>範例-將 blob 中繼資料或 Url 解碼

您的來源資料可能包含您想要以純文字形式搜尋的 Base64 編碼字串，例如 blob 中繼資料字串或 web Url。 填入您的搜尋索引時，您可以使用函式 `base64Decode` 將編碼的資料轉換回一般字串。

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

如果您未包含 parameters 屬性，它會預設為值 `{"useHttpServerUtilityUrlTokenEncode" : true}` 。

Azure 認知搜尋支援兩種不同的 Base64 編碼。 編碼和解碼相同的欄位時，您應該使用相同的參數。 如需詳細資訊，請參閱 [base64 編碼選項](#base64details) ，以決定要使用哪些參數。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 編碼選項

Azure 認知搜尋支援 URL 安全的 base64 編碼和一般 base64 編碼。 在編制索引期間以 base64 編碼的字串應該在之後使用相同的編碼選項進行解碼，否則結果不會符合原始的。

如果 `useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` 用於編碼和解碼的或參數分別設為 `true` ，則的 `base64Encode` 行為會像 [HttpServerUtility. HTTPserverutility.urltokenencode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) ，而 `base64Decode` 行為就像 [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)。

> [!WARNING]
> 如果 `base64Encode` 用來產生索引鍵值， `useHttpServerUtilityUrlTokenEncode` 必須設定為 true。 只有 URL 安全的 base64 編碼可以用於索引鍵值。 如需索引鍵值中字元的完整限制，請參閱 [&#40;Azure 認知搜尋&#41;的命名規則 ](https://docs.microsoft.com/rest/api/searchservice/naming-rules) 。

Azure 認知搜尋中的 .NET 程式庫會假設完整的 .NET Framework，它會提供內建的編碼方式。 `useHttpServerUtilityUrlTokenEncode`和 `useHttpServerUtilityUrlTokenDecode` 選項會利用這項內建功能。 如果您使用 .NET Core 或其他架構，建議您將這些選項設定為 `false` ，並直接呼叫您架構的編碼和解碼函數。

下表比較 `00>00?00` 字串的不同 base64 編碼。 若要判斷您的 base64 函式需要的額外處理 (如果有的話)，將您的程式庫編碼函式套用在 `00>00?00` 字串，然後比較輸出與預期的輸出 `MDA-MDA_MDA`。

| 編碼 | Base64 編碼的輸出 | 程式庫編碼之後的額外處理 | 程式庫解碼之前的額外處理 |
| --- | --- | --- | --- |
| Base64 加填補 | `MDA+MDA/MDA=` | 使用 URL 安全的字元，並移除填補 | 使用標準 base64 字元，並加上填補 |
| Base64 無填補 | `MDA+MDA/MDA` | 使用 URL 安全的字元 | 使用標準 base64 字元 |
| URL 安全的 base64 填補加填補 | `MDA-MDA_MDA=` | 移除填補 | 加上填補 |
| URL 安全的 base64 無填補 | `MDA-MDA_MDA` | 無 | 無 |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 函式

使用指定的分隔符號分割字串欄位，並在分割結果的指定位置挑選權杖。

此函式會使用下列參數：

* `delimiter`︰分割輸入字串時，用為分隔符號的字串。
* `position`：分割輸入字串後，要挑選的權杖以零為基底位置的整數。

例如，如果輸入是 `Jane Doe`，而 `delimiter` 是 `" "` (空格) 且 `position` 為 0，則結果是 `Jane`；如果 `position` 為 1，則結果是 `Doe`。 如果位置參考不存在的權杖，會傳回錯誤。

#### <a name="example---extract-a-name"></a>範例-將名稱解壓縮

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

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection 函式

將格式化為字串 JSON 陣列的字串，轉換為可用來填入索引中 `Collection(Edm.String)` 欄位的字串陣列。

例如，輸入字串是 `["red", "white", "blue"]`，則 `Collection(Edm.String)` 類型的目標欄位會填入 `red`、`white`、`blue` 三個值。 若為無法剖析為 JSON 字串陣列的輸入值，會傳回錯誤。

#### <a name="example---populate-collection-from-relational-data"></a>範例-從關聯式資料填入集合

Azure SQL Database 沒有內建的資料類型，自然對應至 `Collection(Edm.String)` Azure 認知搜尋中的欄位。 若要填入字串集合欄位，您可以將來源資料預先處理為 JSON 字串陣列，然後使用 `jsonArrayToStringCollection` 對應函數。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode 函式

此函式可以用來將字串編碼，使其成為「URL 安全」。 搭配包含 URL 中不允許之字元的字串使用時，此函式會將這些「不安全」字元轉換成字元實體對等專案。 此函式會使用 UTF-8 編碼格式。

#### <a name="example---document-key-lookup"></a>範例-檔索引鍵查閱

`urlEncode` 函式可用來做為函式的替代方案 `base64Encode` ，如果只轉換 URL unsafe 字元，則保留其他字元。

假設輸入字串為-， `<hello>` 則類型的目標欄位 `(Edm.String)` 會填入值 `%3chello%3e`

當您在搜尋時抓取編碼的金鑰時，您可以使用函式 `urlDecode` 來取得原始索引鍵值，並使用該值來抓取來源文件。

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

 ### <a name="urldecode-function"></a>urlDecode 函式

 此函式會使用 UTF-8 編碼格式，將 URL 編碼的字串轉換成已解碼的字串。

 ### <a name="example---decode-blob-metadata"></a>範例-解碼 blob 中繼資料

 某些 Azure 儲存體用戶端會自動對 blob 中繼資料進行 url 編碼（如果它包含非 ASCII 字元）。 不過，如果您想要讓這類中繼資料可供搜尋 (以純文字) ，則在填入搜尋索引時，您可以使用函式 `urlDecode` 將編碼的資料轉換回一般字串。

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
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode 函式
 
 此函式會將任何長度的字串轉換成固定長度的字串。
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>範例-對應太長的檔索引鍵
 
當面對的錯誤抱怨檔索引鍵的長度超過1024個字元時，可以套用此函式來減少檔索引鍵的長度。

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
