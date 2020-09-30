---
title: 包含多份檔的索引 blob
titleSuffix: Azure Cognitive Search
description: 使用 Azure 認知搜尋 Blob 索引子來編目適用于文字內容的 Azure blob，其中每個 blob 可能會產生一或多個搜尋索引檔。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: e5a69525c4bd0717c0561bc61ee3c52aa68e1c9d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533956"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>編制 blob 的索引以產生多個搜尋檔
根據預設，blob 索引子會將 blob 的內容視為單一搜尋檔。 某些 **parsingMode** 值支援個別 blob 可能會導致多個搜尋檔的案例。 不同類型的 **parsingMode** 可讓索引子從 blob 中解壓縮一個以上的搜尋檔，如下所示：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一對多檔索引鍵
顯示在 Azure 認知搜尋索引中的每份檔都是以檔索引鍵來唯一識別。 

如果未指定剖析模式，而且索引中的索引鍵欄位沒有明確的對應 Azure 認知搜尋會自動將屬性 [對應](search-indexer-field-mappings.md) 為索引 `metadata_storage_path` 鍵。 此對應可確保每個 blob 會顯示為不同的搜尋檔。

使用上述任何一種剖析模式時，一個 blob 會對應至「多個」搜尋檔，只根據不適合的 blob 中繼資料來建立檔索引鍵。 為了克服這項限制，Azure 認知搜尋能夠針對從 blob 解壓縮的每個個別實體，產生「一對多」檔索引鍵。 這個屬性會命名為 `AzureSearch_DocumentKey` ，並新增至從 blob 解壓縮的每個個別實體。 這個屬性的值在每個 _blob_ 中保證是唯一的，而且實體會顯示為個別的搜尋檔。

依預設，當未指定索引鍵索引欄位的明確欄位對應時， `AzureSearch_DocumentKey` 就會使用欄位對應函式來對應到它 `base64Encode` 。

## <a name="example"></a>範例
假設您有一個具有下欄欄位的索引定義：
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

而您的 blob 容器具有具有下列結構的 blob：

_Blob1.js開啟_

```json
    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }
```

_Blob2.js開啟_

```json
    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }
```

當您建立索引子，並將 **parsingMode** 設定為 `jsonLines` -但不指定索引鍵欄位的任何明確欄位對應時，將會隱含地套用下列對應

```http
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }
```

此安裝程式會產生包含下列資訊的 Azure 認知搜尋索引， (以 base64 編碼的識別碼縮寫以求簡潔) 

| 識別碼 | 溫度 | 壓力 | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...YjEuanNvbjsx | 100 | 100 | 2019-02-13T00：00：00Z |
| aHR0 ...YjEuanNvbjsy | 33 | 30 | 2019-02-14T00：00：00Z |
| aHR0 ...YjIuanNvbjsx | 1 | 1 | 2018-01-12T00：00：00Z |
| aHR0 ...YjIuanNvbjsy | 120 | 3 | 2013-05-11T00：00：00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引鍵欄位的自訂欄位對應

假設您的 blob 容器具有與上一個範例相同的索引定義，假設您的 blob 容器具有下列結構的 blob：

_Blob1.js開啟_

```json
    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 
```

_Blob2.js開啟_

```json
    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 
```

當您使用 parsingMode 建立索引子時 `delimitedText` ** **，您可能會覺得自然地將欄位對應函式設定為索引鍵欄位，如下所示：

```http
    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }
```

不過，此對應 _不_ 會導致在索引中顯示4份檔，因為欄位在 `recordid` _blob_中不是唯一的。 因此，我們建議您在「一對多」剖析模式中，使用從屬性套用的隱含欄位對應 `AzureSearch_DocumentKey` 至索引鍵索引欄位。

如果您想要設定明確的欄位對應，請確定**跨所有 blob**的每個個別實體都有不同的_sourceField_ 。

> [!NOTE]
> 用來 `AzureSearch_DocumentKey` 確保每個已解壓縮實體之唯一性的方法都有可能變更，因此您不應該依賴它的價值來滿足應用程式的需求。

## <a name="help-us-make-azure-cognitive-search-better"></a>協助我們改善 Azure 認知搜尋
如果您有功能要求或改進的想法，請在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 提供。 如果您需要使用現有功能的協助，請將您的問題張貼在 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)上。

## <a name="next-steps"></a>後續步驟

如果您還不熟悉 blob 索引編制的基本結構和工作流程，您應該先參閱 [Azure 認知搜尋的索引 Azure Blob 儲存體](search-howto-index-json-blobs.md) 。 如需不同 blob 內容類型剖析模式的詳細資訊，請參閱下列文章。

> [!div class="nextstepaction"]
> [編制 CSV blob](search-howto-index-csv-blobs.md) 
>  的索引[編制 JSON blob 的索引](search-howto-index-json-blobs.md)
