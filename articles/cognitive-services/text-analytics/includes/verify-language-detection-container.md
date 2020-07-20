---
title: 確認語言偵測的容器實例
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證語言偵測的容器實例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876396"
---
### <a name="verify-the-language-detection-container-instance"></a>確認語言偵測的容器實例

1. 選取 [**總覽**] 索引標籤，然後複製 [IP 位址]。
1. 開啟新的瀏覽器索引標籤，並輸入 IP 位址。 例如，輸入 `http://<IP-address>:5000 (http://55.55.55.55:5000` ）。 容器的首頁隨即顯示，讓您知道容器正在執行。

    ![查看容器首頁以確認它正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選取 [**服務 API 描述**] 連結，以移至容器的 Swagger 頁面。

1. 選擇任何**張貼**api，然後選取 [立即**試用**]。會顯示參數，其中包括下列範例輸入：

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. 將**showStats**設定為 `true` 。

1. 選取 [**執行**] 以決定文字的情感。

    封裝在容器中的模型會產生範圍從0到1的分數，其中0是負情感，而1是正向情感。

    傳回的 JSON 回應包含更新文字輸入的情感：

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

我們現在可以依據對應的，將回應承載的 JSON 資料檔案與原始要求承載檔相互關聯 `id` 。 每份檔都會獨立處理，其中包含各種統計資料 `characterCount` ，例如和 `transactionCount` 。 此外，每個產生的檔都有 `detectedLanguages` 陣列 `name` ，其中每個偵測到的語言都有、 `iso6391Name` 和 `score` 。 當偵測到多個語言時， `score` 會使用來判斷最可能的語言。