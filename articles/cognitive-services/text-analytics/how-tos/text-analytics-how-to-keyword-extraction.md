---
title: 使用文字分析 REST API 的主要片語解壓縮
titleSuffix: Azure Cognitive Services
description: 如何使用 Azure 認知服務中的文字分析 REST API 來將關鍵字組解壓縮。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/15/2020
ms.author: aahi
ms.openlocfilehash: e5d25e71e4700f3f327319e4f444d2060c7ab5f6
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561880"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>範例：如何使用文字分析來將關鍵字組解壓縮

[關鍵片語擷取 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) \(英文\) 會評估非結構化的文字，並針對每份 JSON 文件，傳回關鍵片語的清單。

此功能在您需要快速識別文件集合中的要點時相當有用。 例如，假設輸入文字為 "The food was delicious and there were wonderful staff"，服務即會傳回主要討論要點："food" 和 "wonderful staff"。

如需詳細資訊，請參閱[支援的語言](../language-support.md)。

> [!TIP]
> * 文字分析也會提供可用來擷取關鍵片語的 Linux 型 Docker 容器映像，好讓您可以在接近資料的位置[安裝和執行文字分析容器](text-analytics-how-to-install-containers.md)。
> * 您也可以使用端點， [以非同步方式](text-analytics-how-to-call-api.md) 使用這項功能 `/analyze` 。

## <a name="preparation"></a>準備

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

關鍵片語擷取在處理較大量文字時的效果最佳。 這與情感分析相反，後者較適合用來處理較少量的文字。 若要從這兩個作業中取得最佳結果，請考慮據以重建輸入。

您必須具有下列格式的 JSON 檔：識別碼、文字、語言

檔案大小必須是每份檔5120或更少的字元，而且每個集合可以有多達1000個專案 (識別碼) 。 集合會在要求本文中提交。 下列範例是您可能會針對關鍵片語擷取所提交內容的說明。 

如需要求和回應物件的詳細資訊，請參閱 [如何呼叫文字分析 API](text-analytics-how-to-call-api.md) 。  

### <a name="example-synchronous-request-object"></a>同步要求物件範例


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>範例非同步要求物件

從開始 `v3.1-preview.3` ，您可以使用端點以非同步方式傳送 NER 要求 `/analyze` 。


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>步驟 1:建立要求結構

如需要求定義的詳細資訊，請參閱 [如何呼叫文字分析 API](text-analytics-how-to-call-api.md)。 為了方便起見，我們將重申下列各點：

+ 建立 **POST** 要求。 請參閱此要求的 API 檔： [主要片語 api](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases)。

+ 使用 Azure 上的文字分析資源或具現化的 [文字分析容器](text-analytics-how-to-install-containers.md)來設定金鑰片語解壓縮的 HTTP 端點。 如果您是以同步方式使用 API，則必須包含 `/text/analytics/v3.0/keyPhrases` 在 URL 中。 例如： `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases` 。

+ 設定要求標頭以包含文字分析作業的[存取金鑰](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)。

+ 在要求主體中，提供您準備用於此分析的 JSON 文件集合。

> [!Tip]
> 使用 [Postman](text-analytics-how-to-call-api.md) 或開啟 [文件](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) \(英文\) 中的 **API 測試主控台** 來建立要求結構，並將它 POST 到服務。

## <a name="step-2-post-the-request"></a>步驟 2：張貼要求

分析會在接收要求時執行。 如需您可以每分鐘或每秒傳送的要求大小和數量的相關資訊，請參閱總覽中的 [資料限制](../overview.md#data-limits) 一節。

請記得，服務是無狀態的。 您的帳戶中並不會儲存任何資料。 結果會在回應中立即傳回。

## <a name="step-3-view-results"></a>步驟 3：檢視結果

所有的 POST 要求都會傳回 JSON 格式的回應，具有識別碼和偵測到的屬性。 傳回的主要片語順序是由模型在內部決定。

輸出會立即傳回。 您可以將結果串流處理到可接受 JSON 的應用程式，或將輸出儲存到本機系統上的檔案，然後將它匯入能讓您排序、搜尋和操作資料的應用程式。

從 3.1-preview 開始，主要片語的輸出範例如下所示。2端點如下所示：

### <a name="synchronous-result"></a>同步結果

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
如同前面所述，分析器會尋找並捨棄非必要的字組，並保留看似句子主體或物件的單一字詞或片語。

### <a name="asynchronous-result"></a>非同步結果

如果您使用 `/analyze` 端點進行非同步作業，您將會收到包含您傳送給 API 之工作的回應。

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>摘要

在本文中，您已瞭解使用認知服務中的文字分析來進行關鍵字組解壓縮的概念和工作流程。 摘要說明：

+ [關鍵片語擷取 API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) \(英文\) 僅針對特定語言提供。
+ 要求本文中的 JSON 文件包含識別碼、文字和語言代碼。
+ POST 要求會 `/keyphrases` `/analyze` 使用個人化 [存取金鑰和](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 對您訂用帳戶有效的端點，來存取或端點。
+ 回應輸出（包含每個檔識別碼的關鍵字組和片語）可以串流處理到任何可接受 JSON 的應用程式，包括 Microsoft Office Excel 和 Power BI 等等。

## <a name="see-also"></a>另請參閱

 [文字分析概觀](../overview.md) [常見問題集 (FAQ)](../text-analytics-resource-faq.md)</br>
 [文字分析產品頁面](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>後續步驟

* [文字分析概觀](../overview.md)
* [使用文字分析用戶端程式庫](../quickstarts/client-libraries-rest-api.md)
* [新功能](../whats-new.md)
