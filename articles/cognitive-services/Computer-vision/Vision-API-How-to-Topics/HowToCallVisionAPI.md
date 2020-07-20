---
title: 呼叫電腦視覺 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用「Azure 認知服務」中的 REST API 呼叫電腦視覺 API。
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 0d2ef4af8af8ad9545277202f0aa7842ac05ea67
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957897"
---
# <a name="call-the-computer-vision-api"></a>呼叫電腦視覺 API

本文將示範如何使用 REST API 呼叫電腦視覺 API。 這些範例會藉由使用電腦視覺 API 用戶端程式庫以 C# 撰寫，並作為 HTTP POST 或 GET 呼叫。 此文章著重於：

- 取得標籤、描述與類別
- 取得特定領域資訊或「名人」

本文的範例會示範下列功能：

* 分析影像以傳回標籤陣列與描述
* 以特定領域模型 (具體而言是「名人」模型) 分析影像，然後以 JSON 傳回對應結果

這些功能提供下列選項：

- **選項 1**：特定範圍分析 - 只分析指定的模型
- **選項 2**：強化分析 - 使用 [86 個類別的分類法](../Category-Taxonomy.md)分析，以提供其他詳細資料

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="建立電腦視覺資源"  target="_blank">建立電腦視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 電腦視覺服務。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 本機儲存影像的影像 URL 或路徑
* 支援的輸入方法：應用程式/八位元資料流或影像 URL 形式的原始影像二進位檔
* 支援的影像檔案格式：JPEG、PNG、GIF 和 BMP
* 影像檔大小：4MB 或更少
* 影像尺寸：50 &times; 50 像素或更高
  
## <a name="authorize-the-api-call"></a>授權 API 呼叫

每次呼叫電腦視覺 API 時，都需要訂用帳戶金鑰。 此金鑰必須透過查詢字串參數來傳遞，或在要求標頭中指定。

您可以執行下列任一動作來傳遞訂用帳戶金鑰：

* 透過查詢字串傳遞，如下列電腦視覺 API 範例所示：

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* 在 HTTP 要求標頭中指定：

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* 當您使用用戶端程式庫時，透過 ComputerVisionClient 的建構函式傳遞金鑰，並在用戶端的屬性中指定區域：

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>將影像上傳至電腦視覺 API 服務

執行電腦視覺 API 呼叫的基本方式，就是直接上傳影像以傳回標籤、描述及名人。 若要這麼做，您可以使用 HTTP 主體中的二進位影像，連同從影像讀取的資料來傳送 "POST" 要求。 所有電腦視覺 API 呼叫的上傳方法都相同。 唯一的差異是您指定的查詢參數。 

針對指定影像，使用下列其中一個選項來取得標籤和描述：

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>選項 1：取得標籤清單與描述

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>選項 2：僅取得標籤清單或僅取得描述

若是僅取得標籤，請執行：

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

若是僅取得描述，請執行：

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>取得特定領域的分析 (名人)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>選項 1：特定範圍分析 - 只分析指定的模型
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

針對此選項，所有其他查詢參數 {visualFeatures, details} 皆無效。 如果您想要查看所有支援的模型，請使用：

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>選項 2：強化分析 - 使用 86 個類別的分類法分析，以提供其他詳細資料

除了一或多個特定領域模型中的詳細資料之外，針對您還想要取得一般影像分析的應用程式，可使用模型查詢參數來擴充 v1 API。

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

當您叫用這個方法時，您會先呼叫 [86 個類別](../Category-Taxonomy.md)分類器。 如果有任何類別符合已知或比對模型的類別，則會發生第二輪的分類器叫用。 例如，如果 "details = all" 或 "details" 包含 "celebrities"，則在呼叫 86 個類別分類器之後，您會呼叫名人模型。 結果會包含該類別的人員。 相較於選項 1，這個方法會使對名人感興趣的使用者有更多延遲。

在此案例中，所有 v1 查詢參數的運作方式都會相同。 如果您未指定 visualFeatures=categories，其依然會隱含地啟用。

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>擷取和了解 JSON 輸出以供分析

以下是範例：

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

欄位 | 類型 | 內容
------|------|------|
Tags  | `object` | 標籤陣列的最上層物件。
tags[].Name | `string`    | 標籤分類器的關鍵字。
tags[].Score    | `number`    | 信賴分數，介於 0 到 1 之間。
description     | `object`    | 描述的最上層物件。
description.tags[] |    `string`    | 標籤清單。  如果能否產生標題的信賴度不足，呼叫者可能僅有標籤的資訊可用。
description.captions[].text    | `string`    | 描述影像的片語。
description.captions[].confidence    | `number`    | 片語的信賴分數。

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>擷取和了解特定領域模型的 JSON 輸出

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>選項 1：特定範圍分析 - 只分析指定的模型

輸出是一組標籤，如下列範例所示：

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>選項 2：強化分析 - 使用「86 個類別」的分類法分析，以提供其他詳細資料

針對使用選項 2 (強化分析) 的特定領域模型，會擴充類別傳回類型，如下列範例所示：

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

類別欄位是原始分類法中 [86 個類別](../Category-Taxonomy.md)的一或多個類別。 以底線結尾的類別會符合該類別與其子項 (例如，名人模型的 "people_" 或 "people_group")。

欄位    | 類型    | 內容
------|------|------|
categories | `object`    | 最上層物件。
categories[].name     | `string`    | 86 個類別分類法清單中的名稱。
categories[].score    | `number`    | 信賴分數，介於 0 到 1 之間。
categories[].detail     | `object?`      | (選擇性) 詳細資料物件。

如果多個類別皆符合 (例如，當 model=celebrities 時，86 個類型分類器傳回 "people_" 與 "people_young" 兩者的分數)，以附加在最一般層級的詳細資料為符合者 (在該範例中為 "people_")。

## <a name="error-responses"></a>錯誤回應

這些錯誤和 vision.analyze 中的完全相同，還有可能會在「選項 1」與「選項 2」情節中傳回 NotSupportedModel 錯誤 (HTTP 400) 的其他錯誤。 針對選項 2 (強化分析)，如果無法辨識詳細資料中指定的任何模型，即使其中一或多個模型有效，API 也會傳回 NotSupportedModel。 若要了解支援的模型有哪些，您可以呼叫 listModels。

## <a name="next-steps"></a>後續步驟

若要使用 REST API，請前往[電腦視覺 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b) \(英文\)。
