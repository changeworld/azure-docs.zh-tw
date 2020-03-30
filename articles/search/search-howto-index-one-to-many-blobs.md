---
title: 索引包含多個文檔的 Blob
titleSuffix: Azure Cognitive Search
description: 使用 Azure 查詢 Blob 索引子對文本內容進行爬網 Azure Blob，其中每個 Blob 都可能產生一個或多個搜索索引文檔。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1840bda0ecc9462a5d8f796b616d728d0bb412f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112275"
---
# <a name="indexing-blobs-to-produce-multiple-search-documents"></a>索引 Blob 以生成多個搜索文檔
預設情況下，Blob 索引子會將 Blob 的內容視為單個搜索文檔。 某些**分析模式**值支援單個 Blob 可能導致多個搜索文檔的方案。 允許索引子從 Blob 中提取多個搜索文檔的不同類型的**分析模式**是：
+ `delimitedText`
+ `jsonArray`
+ `jsonLines`

## <a name="one-to-many-document-key"></a>一對多文檔金鑰
Azure 認知搜索索引中顯示的每個文檔都由文檔鍵唯一標識。 

如果未指定解析模式，並且索引 Azure 認知搜索中的關鍵字段沒有顯式映射，則會自動[將](search-indexer-field-mappings.md)`metadata_storage_path`屬性對應為鍵。 此映射可確保每個 Blob 顯示為不同的搜索文檔。

使用上面列出的任何分析模式時，一個 Blob 映射到"多"搜索文檔，使僅基於 blob 中繼資料的文檔金鑰不適合。 為了克服此約束，Azure 認知搜索能夠為從 blob 中提取的每個實體生成"一對多"文檔金鑰。 此屬性被命名`AzureSearch_DocumentKey`，並添加到從 blob 中提取的每個實體。 此屬性的值保證_在 Blob 中的每個_實體是唯一的，並且實體將顯示為單獨的搜索文檔。

預設情況下，當未指定關鍵索引欄位的顯式欄位映射時，`AzureSearch_DocumentKey`將使用`base64Encode`欄位映射函數映射到該欄位映射。

## <a name="example"></a>範例
假設您具有具有以下欄位的索引定義：
+ `id`
+ `temperature`
+ `pressure`
+ `timestamp`

Blob 容器具有具有以下結構的 blob：

_Blob1.json_

    { "temperature": 100, "pressure": 100, "timestamp": "2019-02-13T00:00:00Z" }
    { "temperature" : 33, "pressure" : 30, "timestamp": "2019-02-14T00:00:00Z" }

_Blob2.json_

    { "temperature": 1, "pressure": 1, "timestamp": "2018-01-12T00:00:00Z" }
    { "temperature" : 120, "pressure" : 3, "timestamp": "2013-05-11T00:00:00Z" }

創建索引子並將**分析模式**設置為`jsonLines`- 而不為鍵欄位指定任何顯式欄位映射，則將隱式應用以下映射
    
    {
        "sourceFieldName" : "AzureSearch_DocumentKey",
        "targetFieldName": "id",
        "mappingFunction": { "name" : "base64Encode" }
    }

此設置將導致 Azure 認知搜索索引包含以下資訊（base64 編碼 ID 縮短，以便簡潔）

| id | 溫度 | pressure | timestamp |
|----|-------------|----------|-----------|
| aHR0 ...伊尤恩·恩夫比克斯 | 100 | 100 | 2019-02-13T00：00：00Z |
| aHR0 ...伊尤恩·恩布西 | 33 | 30 | 2019-02-14T00：00：00Z |
| aHR0 ...伊尤安·恩夫比克斯 | 1 | 1 | 2018-01-12T00：00：00Z |
| aHR0 ...伊揚·恩夫比西 | 120 | 3 | 2013-05-11T00：00：00Z |

## <a name="custom-field-mapping-for-index-key-field"></a>索引鍵欄位的自訂欄位映射

假設索引定義與前面的示例相同，假設 blob 容器具有具有以下結構的 Blob：

_Blob1.json_

    recordid, temperature, pressure, timestamp
    1, 100, 100,"2019-02-13T00:00:00Z" 
    2, 33, 30,"2019-02-14T00:00:00Z" 

_Blob2.json_

    recordid, temperature, pressure, timestamp
    1, 1, 1,"2018-01-12T00:00:00Z" 
    2, 120, 3,"2013-05-11T00:00:00Z" 

當您創建具有`delimitedText`**分析Mode**的索引子時，將欄位映射函數設置為鍵欄位可能感覺很自然，如下所示：

    {
        "sourceFieldName" : "recordid",
        "targetFieldName": "id"
    }

但是，此_映射不會導致_索引中出現 4 個文檔，因為`recordid`該欄位在 blob_中_並不唯一。 因此，我們建議您使用從`AzureSearch_DocumentKey`屬性應用於關鍵索引欄位的隱式欄位映射，以便進行"一對多"分析模式。

如果確實要設置顯式欄位映射，請確保_源欄位_**對於所有 Blob 中的每個**實體都不同。

> [!NOTE]
> 確保每個提取的`AzureSearch_DocumentKey`實體的唯一性所使用的方法可能會發生變化，因此您不應依賴它的價值來滿足應用程式的需求。

## <a name="next-steps"></a>後續步驟

如果您尚未熟悉 Blob 索引的基本結構和工作流，則應首先查看[使用 Azure 認知搜索索引 Azure Blob 存儲](search-howto-index-json-blobs.md)。 有關分析不同 Blob 內容類型的模式的詳細資訊，請查看以下文章。

> [!div class="nextstepaction"]
> [索引 CSV Blob](search-howto-index-csv-blobs.md)
> [索引 JSON blob](search-howto-index-json-blobs.md)
