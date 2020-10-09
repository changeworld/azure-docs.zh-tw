---
title: " (預覽版的檔解壓縮認知技能) "
titleSuffix: Azure Cognitive Search
description: 從擴充管線內的檔案解壓縮內容。 此技能目前為公開預覽狀態。
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: chalton
ms.openlocfilehash: f209be383e445e3b0c011e0bfb4266a191a8d931
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85080864"
---
# <a name="document-extraction-cognitive-skill"></a>檔提取認知技能

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有入口網站或 .NET SDK 支援。

檔 **提取** 技能會從擴充管線內的檔案中解壓縮內容。 這可讓您利用其他技能可能產生的檔案，在技能集執行之前，使用通常會發生的檔解壓縮步驟。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 Api 時，會產生費用，並在編制索引時將影像解壓縮作為檔破解階段的一部分。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 [定價頁面](https://azure.microsoft.com/pricing/details/search/)上會說明映射解壓縮定價。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入            | 允許的值 | 描述 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | `default`針對從非純文字或 json 的檔案進行檔解壓縮的設定為。 設定為 `text` 以改善純文字檔的效能。 設定為 `json` 可從 json 檔案解壓縮結構化內容。 如果未 `parsingMode` 明確定義，它會設定為 `default` 。 |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 將設定為， `contentAndMetadata` 以從每個檔案中解壓縮所有中繼資料和文字內容。 若設定為， `allMetadata` 則只會將 [內容類型特定的中繼資料](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata) 解壓縮 (例如，僅限 .png 檔案的唯一中繼資料) 。 如果未 `dataToExtract` 明確定義，它會設定為 `contentAndMetadata` 。 |
| `configuration` | 請參閱下文。 | 可調整檔解壓縮執行方式的選擇性參數字典。 請參閱下表以取得支援的設定屬性的說明。 |

| 設定參數   | 允許的值 | 描述 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 將設定為， `none` 以忽略資料集中的內嵌影像或影像檔。 此為預設值。 <br/>若要 [使用認知技能進行影像分析](cognitive-search-concept-image-scenarios.md)，請將設定為， `generateNormalizedImages` 讓技能建立正規化影像的陣列，做為檔破解的一部分。 這個動作需要將 `parsingMode` 設定為 `default` ，且 `dataToExtract` 設定為 `contentAndMetadata` 。 標準化影像指的是藉由額外的處理過程以產生統一的影像輸出。影像經過調整大小與旋轉之後，會使得視覺化搜索結果中的影像有一致的呈現 (例如，如 [JFK 示範](https://github.com/Microsoft/AzureSearch_JFK_Files) \(英文\) 中所見圖形控制項中相同尺寸的照片)。 當您使用此選項時，會為每個影像產生此資訊。  <br/>如果您將設定為 `generateNormalizedImagePerPage` ，PDF 檔案會以不同的方式處理，而不是解壓縮內嵌影像，每個頁面都會轉譯為影像並據以正規化。  非 PDF 檔案類型的處理方式會與設定時相同 `generateNormalizedImages` 。
| `normalizedImageMaxWidth` | 介於50-10000 之間的任何整數 | 所產生的標準化影像的最大寬度 (以像素為單位)。 預設值為 2000。 | 
| `normalizedImageMaxHeight` | 介於50-10000 之間的任何整數 | 所產生標準化影像的最大高度 (以像素為單位)。 預設值為 2000。 |

> [!NOTE]
> 標準化影像的最大寬度與最大高度的預設值為 2000 像素，這是根據 [OCR 技術](cognitive-search-skill-ocr.md)和[影像分析技術](cognitive-search-skill-image-analysis.md)所支援的大小上限。 [OCR 技能](cognitive-search-skill-ocr.md)針對非英文語言支援最大的寬度和高度4200，以及適用于英文的10000。  如果您增加最大限制，則根據您的技能集定義和檔的語言，可能會在較大的影像上進行處理。 
## <a name="skill-inputs"></a>技能輸入

| 輸入名稱     | 描述 |
|--------------------|-------------|
| `file_data` | 應從中解壓縮內容的檔案。 |

"File_data" 輸入必須是定義如下的物件：

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

您可以透過下列三種方式產生此檔案參考物件：

 - 將 `allowSkillsetToReadFileData` 索引子定義上的參數設定為 "true"。  這會建立一個 `/document/file_data` 代表從 blob 資料來源下載之原始檔案資料的物件路徑。 此參數只適用于 Blob 儲存體中的資料。

 - 將 `imageAction` 索引子定義上的參數設定為以外的值 `none` 。  如果個別傳遞 (（亦即) ），這會建立一組影像，以遵循此技能的輸入所需的慣例。 `/document/normalized_images/*`

 - 擁有自訂技能會傳回完全依照上述方式定義的 json 物件。  `$type`參數必須設定為精確 `file` ，而且 `data` 參數必須是檔案內容的基底64編碼位元組陣列資料。

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 描述 |
|--------------|-------------|
| `content` | 檔的文字內容。 |
| `normalized_images`   | 如果 `imageAction` 設定為其他值 `none` ，則新的 *normalized_images* 欄位將包含影像的陣列。 如需每個影像輸出格式的詳細資訊，請參閱 [影像解壓縮檔](cognitive-search-concept-image-scenarios.md) 。 |

##  <a name="sample-definition"></a>範例定義

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>範例輸入

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>範例輸出

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>另請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [如何在認知搜尋案例中處理影像並從影像擷取資訊](cognitive-search-concept-image-scenarios.md)