---
title: 如何取得 V3 預測端點
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589111"
---
1. 在 LUIS 入口網站的 [管理] 區段 (右上方功能表)、[Azure 資源] 頁面 (左側功能表)、[預測資源] 索引標籤上，複製頁面底部的**範例查詢**。

    將 URL 貼到新的瀏覽器索引標籤中。

    URL 有您的應用程式識別碼、金鑰和位置名稱。 V3 預測端點 URL 如下所示：

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

