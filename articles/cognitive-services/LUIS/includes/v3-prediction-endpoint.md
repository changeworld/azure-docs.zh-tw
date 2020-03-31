---
title: 如何取得 V3 預測端點
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80287742"
---
1. 在 LUIS 入口網站的 [管理]  區段 (右上方功能表)、[Azure 資源]  頁面 (左側功能表)、[預測資源]  索引標籤上，複製頁面底部的**範例查詢**。

    將 URL 貼到新的瀏覽器索引標籤中。

    URL 有您的應用程式識別碼、金鑰和位置名稱。 V3 預測端點 URL 如下所示：

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

