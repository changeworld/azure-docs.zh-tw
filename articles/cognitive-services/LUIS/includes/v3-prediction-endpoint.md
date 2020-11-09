---
title: 如何取得 V3 預測端點
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128124"
---
1. 在 LUIS 入口網站的 [管理] 區段 (右上方功能表)、[Azure 資源] 頁面 (左側功能表)、[預測資源] 索引標籤上，複製頁面底部的 **範例查詢** 。 URL 有您的應用程式識別碼、金鑰和位置名稱。 V3 預測端點 URL 的格式如下：`https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="預測資源區段中的範例查詢" lightbox="../media/prediction-resources-example-query.png":::
    
    將 URL 貼到新的瀏覽器索引標籤中。若未看到 URL，表示您沒有預測資源，而必須建立一個。 

    

    

