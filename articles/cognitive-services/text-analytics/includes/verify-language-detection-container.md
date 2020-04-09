---
title: 驗證語言偵測容器實體
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證語言檢測容器實例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc52586550f89ddae147d79458584331ed984eea
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876396"
---
### <a name="verify-the-language-detection-container-instance"></a>驗證語言偵測容器實體

1. 選擇 **「概述」** 選項卡,然後複製 IP 位址。
1. 開啟新的瀏覽器選項卡,然後輸入 IP 位址。 例如,輸入`http://<IP-address>:5000 (http://55.55.55.55:5000`。 將顯示容器的主頁,讓您知道容器正在運行。

    ![檢視容器主頁以驗證是否正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選擇 **「服務 API 描述」** 連結以轉到容器的 Swagger 頁面。

1. 選擇任選**點**API,然後選擇 **「試用」。** 將顯示參數,其中包括此範例輸入:

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

1. 將 **「顯示」**`true`設定為 。

1. 選擇 **「執行」** 以確定文本的情緒。

    包裝在容器中的模型生成介於 0 到 1 的分數,其中 0 為負情緒,1 為正情緒。

    傳回的 JSON 回應包括更新的文字輸入的情緒:

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

我們可以透過對對對媒體的 JSON 資料的文件與其原始要求有效負載文件相關聯`id`。 每個文件都單獨處理,其中包含各種統計資訊,`characterCount``transactionCount`如與 。 此外,每個`detectedLanguages`產生的文件都有包含的`name`陣列`iso6391Name`,每個`score`語言都偵測到 。 當檢測到多種語言時,`score`用於確定最可能的語言。