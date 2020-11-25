---
title: 確認情感分析的容器實例
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證情感分析的容器實例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d0a5e09f1d697c2f5f842edfb06be4b13229d095
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009849"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>確認情感分析的容器實例

1. 選取 [ **總覽** ] 索引標籤，然後複製 IP 位址。
1. 開啟新的瀏覽器索引標籤，然後輸入 IP 位址。 例如，輸入 `http://<IP-address>:5000 (http://55.55.55.55:5000`) 。 容器的首頁隨即顯示，讓您知道容器正在執行。

    ![查看容器首頁以確認其正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選取 [ **服務 API 描述** ] 連結，以移至容器的 Swagger 頁面。

1. 選擇任何 **貼** 文 api，然後選取 [立即 **試用**]。系統會顯示參數，其中包括下列範例輸入：

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

1. 以下列 JSON 內容取代輸入：

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. 將 **showStats** 設定為 `true` 。

1. 選取 [ **執行** ] 來判斷文字的情感。

    封裝在容器中的模型會產生範圍從0到1的分數，其中0是負面情感，1是正面的情感。

    傳回的 JSON 回應包含更新文字輸入的情感：

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

我們現在可以將回應裝載 `id` 之 JSON 資料的檔與原始要求承載檔產生關聯 `id` 。 超過的分數 `0.98` 表示非常正面的情感。