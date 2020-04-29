---
title: 檔解壓縮認知技能（預覽）
titleSuffix: Azure Cognitive Search
description: 從擴充管線內的檔案解壓縮內容。 此技能目前為公開預覽狀態。
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76837726"
---
# <a name="document-extraction-cognitive-skill"></a>檔解壓縮認知技能

> [!IMPORTANT] 
> 此技能目前為公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 目前沒有入口網站或 .NET SDK 支援。

檔**提取**技能會從擴充管線內的檔案中提取內容。 這可讓您利用通常在技能集執行之前發生的檔解壓縮步驟，以及可能由其他技能產生的檔案。

> [!NOTE]
> 當您藉由增加處理頻率、新增更多檔或新增更多 AI 演算法來擴展範圍時，您將需要[附加可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 Api 時，會產生費用，並在編制索引的檔破解階段進行映射解壓縮。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 [[價格] 頁面](https://go.microsoft.com/fwlink/?linkid=2042400)上會描述影像提取定價。
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入            | 允許的值 | 描述 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 針對 [ `default`從非純文字或 json 的檔案解壓縮檔]，將設為。 設定為`text`可改善純文字檔案的效能。 設定為`json`可從 json 檔案解壓縮結構化內容。 如果`parsingMode`未明確定義，則會將它設定為`default`。 |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 設定為`contentAndMetadata`可從每個檔案解壓縮所有中繼資料和文字內容。 設定為`allMetadata`可只解壓縮[內容類型特定的中繼資料](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata)（例如，僅限 .png 檔案的唯一中繼資料）。 如果`dataToExtract`未明確定義，則會將它設定為`contentAndMetadata`。 |
| `configuration` | 請參閱下列內容。 | 選擇性參數的字典，可調整執行檔解壓縮的方式。 請參閱下表，以取得支援的設定屬性的描述。 |

| 設定參數   | 允許的值 | 描述 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 設定為`none`以忽略資料集中的內嵌影像或影像檔案。 這是預設值。 <br/>若要[使用認知技能進行影像分析](cognitive-search-concept-image-scenarios.md)， `generateNormalizedImages`請將設定為，讓技能在檔破解過程中建立正規化影像的陣列。 `parsingMode`此動作需要設定`default`為，且`dataToExtract`設定為。 `contentAndMetadata` 標準化影像指的是藉由額外的處理過程以產生統一的影像輸出。影像經過調整大小與旋轉之後，會使得視覺化搜索結果中的影像有一致的呈現 (例如，如 [JFK 示範](https://github.com/Microsoft/AzureSearch_JFK_Files) \(英文\) 中所見圖形控制項中相同尺寸的照片)。 當您使用此選項時，會為每個影像產生這項資訊。  <br/>如果您將設定`generateNormalizedImagePerPage`為，PDF 檔案會以不同的方式處理，而不是解壓縮內嵌影像，而是會將每個頁面轉譯為影像，並據以進行正規化。  非 PDF 檔案類型的處理方式會與`generateNormalizedImages`已設定的相同。
| `normalizedImageMaxWidth` | 介於50-10000 之間的任何整數 | 所產生的標準化影像的最大寬度 (以像素為單位)。 預設值為 2000。 | 
| `normalizedImageMaxHeight` | 介於50-10000 之間的任何整數 | 所產生標準化影像的最大高度 (以像素為單位)。 預設值為 2000。 |

> [!NOTE]
> 標準化影像的最大寬度與最大高度的預設值為 2000 像素，這是根據 [OCR 技術](cognitive-search-skill-ocr.md)和[影像分析技術](cognitive-search-skill-image-analysis.md)所支援的大小上限。 [OCR 技能](cognitive-search-skill-ocr.md)支援非英文語言的最大寬度和高度4200，以及適用于英文的10000。  如果您增加上限，視您的技能集定義和檔的語言而定，較大的影像處理可能會失敗。 
## <a name="skill-inputs"></a>技能輸入

| 輸入名稱     | 描述 |
|--------------------|-------------|
| file_data | 應從中解壓縮內容的檔案。 |

"File_data" 輸入必須是定義如下的物件：

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

此檔案參考物件可透過下列三種方式產生：

 - 將索引`allowSkillsetToReadFileData`器定義上的參數設為 "true"。  這會建立一個代表`/document/file_data`從 blob 資料來源下載的原始檔案資料的路徑。 此參數只適用于 Blob 儲存體中的資料。

 - 將索引`imageAction`器定義上的參數設定為以外的值`none`。  這會建立影像陣列，遵循此技能的輸入慣例（如果個別傳遞的話`/document/normalized_images/*`）（例如）。

 - 擁有自訂技能會傳回完全依照上述方式定義的 json 物件。  `$type`參數必須設定為 [精確`file` ]，而`data`參數必須是檔案內容的基底64編碼位元組陣列資料。

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 描述 |
|--------------|-------------|
| 內容 | 檔的文字內容。 |
| normalized_images | `imageAction`當設定為其他值時`none`，新的*normalized_images*欄位會包含影像陣列。 如需每個影像輸出格式的詳細資訊，請參閱[影像解壓縮的檔](cognitive-search-concept-image-scenarios.md)。 |

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

## <a name="see-also"></a>請參閱

+ [內建技能](cognitive-search-predefined-skills.md)
+ [如何定義技能集](cognitive-search-defining-skillset.md) (英文)
+ [如何在認知搜尋案例中處理影像並從影像擷取資訊](cognitive-search-concept-image-scenarios.md)