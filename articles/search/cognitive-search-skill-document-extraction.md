---
title: 文檔提取認知技能（預覽）
titleSuffix: Azure Cognitive Search
description: 從濃縮管道中的檔中提取內容。 此技能當前處於公共預覽版中。
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76837726"
---
# <a name="document-extraction-cognitive-skill"></a>文檔提取認知技能

> [!IMPORTANT] 
> 此技能當前處於公共預覽版中。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 當前沒有門戶或 .NET SDK 支援。

**文檔提取**技能從濃縮管道中的檔中提取內容。 這允許您利用文檔提取步驟，該步驟通常發生在技能集執行之前，這些檔可能由其他技能生成。

> [!NOTE]
> 隨著通過增加處理頻率、添加更多文檔或添加更多 AI 演算法來擴大範圍，您需要[附加計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中調用 API 時會產生費用，以及作為索引中文檔破解階段的一部分的圖像提取。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 圖像提取定價在[定價頁面上](https://go.microsoft.com/fwlink/?linkid=2042400)進行了描述。
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>技能參數

這些參數會區分大小寫。

| 輸入            | 允許的值 | 描述 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | 設置為`default`從不是純文字或 json 的檔提取文檔。 設置為`text`提高純文字檔的性能。 設置為`json`從 json 檔中提取結構化內容。 如果未`parsingMode`顯式定義，則將其設置為`default`。 |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 設置為`contentAndMetadata`從每個檔中提取所有中繼資料和文本內容。 設置為`allMetadata`僅提取[特定于內容類型的中繼資料](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata)（例如，僅 .png 檔所特有的中繼資料）。 如果未`dataToExtract`顯式定義，則將其設置為`contentAndMetadata`。 |
| `configuration` | 請參閱下列內容。 | 調整文檔提取執行方式的可選參數字典。 有關支援的配置屬性的說明，請參閱下表。 |

| 設定參數   | 允許的值 | 描述 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 設置為`none`忽略資料集中的嵌入圖像或影像檔。 這是預設值。 <br/>對於[使用認知技能的圖像分析](cognitive-search-concept-image-scenarios.md)，設置為`generateNormalizedImages`讓技能創建一系列正常化的圖像，作為文檔破解的一部分。 此操作要求`parsingMode`設置為`default`和`dataToExtract`設置為 。 `contentAndMetadata` 標準化影像指的是藉由額外的處理過程以產生統一的影像輸出。影像經過調整大小與旋轉之後，會使得視覺化搜索結果中的影像有一致的呈現 (例如，如 [JFK 示範](https://github.com/Microsoft/AzureSearch_JFK_Files) \(英文\) 中所見圖形控制項中相同尺寸的照片)。 使用此選項時，將為每個圖像生成此資訊。  <br/>如果設置為`generateNormalizedImagePerPage`，PDF 檔將以不同的方式處理，因為而不是提取嵌入的圖像，每個頁面將呈現為圖像並相應地正常化。  非 PDF 檔案類型的處理方式`generateNormalizedImages`與設置相同。
| `normalizedImageMaxWidth` | 50-10000 之間的任何整數 | 所產生的標準化影像的最大寬度 (以像素為單位)。 預設值為 2000。 | 
| `normalizedImageMaxHeight` | 50-10000 之間的任何整數 | 所產生標準化影像的最大高度 (以像素為單位)。 預設值為 2000。 |

> [!NOTE]
> 標準化影像的最大寬度與最大高度的預設值為 2000 像素，這是根據 [OCR 技術](cognitive-search-skill-ocr.md)和[影像分析技術](cognitive-search-skill-image-analysis.md)所支援的大小上限。 [OCR 技能](cognitive-search-skill-ocr.md)支援非英語語言的最大寬度和高度為 4200，英語支援 10000。  如果增加最大限制，則根據技能集定義和文檔的語言，處理較大的圖像可能會失敗。 
## <a name="skill-inputs"></a>技能輸入

| 輸入名稱     | 描述 |
|--------------------|-------------|
| file_data | 應從中提取內容的檔。 |

"file_data"輸入必須是定義如下的物件：

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

可以生成此檔引用物件，這三種方法之一：

 - 將`allowSkillsetToReadFileData`索引子定義的參數設置為"true"。  這將創建一個路徑`/document/file_data`，該路徑是表示從 Blob 資料來源下載的原始檔案資料的物件。 此參數僅適用于 Blob 存儲中的資料。

 - 將`imageAction`索引子定義的參數設置為 以外的`none`值。  這將創建一個圖像陣列，該陣列遵循輸入此技能所需的約定（如果單獨傳遞（即`/document/normalized_images/*`）。

 - 具有自訂技能返回與上述完全一樣定義的 json 物件。  參數`$type`必須設置為精確，`file``data`並且參數必須是檔內容的基礎 64 編碼位元組陣列資料。

## <a name="skill-outputs"></a>技能輸出

| 輸出名稱    | 描述 |
|--------------|-------------|
| content | 文檔的文本內容。 |
| normalized_images | 當`imageAction`設置為 其他值時`none`，新*normalized_images*欄位將包含圖像陣列。 有關每個圖像的輸出格式的更多詳細資訊，請參閱[圖像提取文檔](cognitive-search-concept-image-scenarios.md)。 |

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