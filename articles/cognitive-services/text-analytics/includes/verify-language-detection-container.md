---
title: 驗證語言檢測容器實例
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證語言檢測容器實例。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: f4e0770bc052044a408f2c4088f2bd5ead225aa3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "70968553"
---
### <a name="verify-the-language-detection-container-instance"></a>驗證語言檢測容器實例

1. 選擇 **"概述"** 選項卡，然後複製 IP 位址。
1. 打開新的瀏覽器選項卡，然後輸入 IP 位址。 例如，輸入`http://<IP-address>:5000 (http://55.55.55.55:5000`。 將顯示容器的主頁，讓您知道容器正在運行。

    ![查看容器主頁以驗證其是否正在運行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選擇 **"服務 API 描述"** 連結以轉到容器的 Swagger 頁面。

1. 選擇任選**點**API，然後選擇 **"試用"。** 將顯示參數，其中包括此示例輸入：

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

1. 將 **"顯示"設置**為`true`。

1. 選擇 **"執行"** 以確定文本的情緒。

    包裝在容器中的模型生成介於 0 到 1 的分數，其中 0 為負情緒，1 為正情緒。

    返回的 JSON 回應包括更新的文本輸入的情緒：

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

我們現在可以通過相應的將回應有效負載的 JSON 資料的文檔與其原始請求有效負載文檔相關聯`id`。 每個文檔都單獨處理，其中包含各種統計資訊，`characterCount`如`transactionCount`和 。 此外，每個`detectedLanguages`生成的文檔都有包含 的`name`陣列`iso6391Name`，每個`score`語言都檢測到 。 當檢測到多種語言時，`score`用於確定最可能的語言。