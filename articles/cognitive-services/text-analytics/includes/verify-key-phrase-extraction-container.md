---
title: 驗證關鍵短文提取容器實體
titleSuffix: Azure Cognitive Services
description: 瞭解如何驗證關鍵短語提取容器實例。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c177517ec18d7526b1cc09da74e35cb5434766d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876394"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>驗證關鍵短文提取容器實體

1. 選擇 **「概述」** 選項卡,然後複製 IP 位址。
1. 開啟新的瀏覽器選項卡,然後輸入 IP 位址。 例如,輸入`http://<IP-address>:5000 (http://55.55.55.55:5000`。 將顯示容器的主頁,讓您知道容器正在運行。

    ![檢視容器主頁以驗證是否正在執行](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. 選擇 **「服務 API 描述」** 連結以轉到容器的 Swagger 頁面。

1. 選擇任選**點**API,然後選擇 **「試用」。** 將顯示參數,其中包括此範例輸入:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
        }
      ]
    }
    ```

1. 將輸入取代為以下 JSON 內容:

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

1. 將 **「顯示」**`true`設定為 。

1. 選擇 **「執行」** 以確定文本的情緒。

    打包在容器中的模型生成介於 0 到 1 的分數,其中 0 為負,1 為正。

    傳回的 JSON 回應包括更新的文字輸入的情緒:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

我們現在可以將回應有效負載的`id`JSON 數據的文檔與原始請求有效`id`負載文文件 相關聯。 生成的文件具有一個`keyPhrases`陣列,其中包含從相應的輸入文檔中提取的關鍵短語的清單。 此外,還有各種統計資訊,例如`characterCount`和`transactionCount`每個生成的文檔。