---
title: 搜尋純文字 blob
titleSuffix: Azure Cognitive Search
description: 設定搜尋索引子，以在 Azure 認知搜尋中從 Azure blob 解壓縮純文字以進行全文檢索搜尋。
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2b01b9a3782d25660462d0cc2bf3aec87baf0023
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403773"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中為純文字 blob 編制索引

使用 [blob 索引子](search-howto-indexing-azure-blob-storage.md) 來解壓縮可搜尋的文字以進行全文檢索搜尋時，您可以叫用各種剖析模式，以取得更佳的索引結果。 根據預設，索引子會將分隔的文字 blob 剖析為單一的文字區塊。 但是，如果您的所有 blob 都是以相同的編碼方式包含純文字，則可以使用 **文字剖析模式**來大幅改善索引效能。

## <a name="set-up-plain-text-indexing"></a>設定純文字索引

若要編制純文字 blob 的索引，請 `parsingMode` `text` 在 [建立索引子](/rest/api/searchservice/create-indexer) 要求上使用設定屬性來建立或更新索引子定義：

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

根據預設，會假定使用 `UTF-8` 編碼。 若要指定其他編碼，請使用 `encoding` 設定屬性： 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>要求範例

剖析模式是在索引子定義中指定的。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>協助我們改善 Azure 認知搜尋
如果您有功能要求或改進的想法，請在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 提供。 如果您需要使用現有功能的協助，請將您的問題張貼在 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)上。

## <a name="see-also"></a>另請參閱

* [Azure 認知搜尋中的索引子](search-indexer-overview.md)
* [如何設定 blob 索引子](search-howto-indexing-azure-blob-storage.md)
* [Blob 索引編制總覽](search-blob-storage-integration.md)