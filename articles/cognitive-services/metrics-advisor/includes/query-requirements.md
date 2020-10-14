---
title: 查詢需求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: mbullwin
ms.openlocfilehash: 777e3261bd6f842861ed6622b78589eedaaf65d7
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043186"
---
在查詢內，使用 `@StartTime` 參數取得單一時間戳記的計量資料。 Metrics Advisor 在執行查詢時會將該參數取代為 `yyyy-MM-ddTHH:mm:ss` 格式字串。

> [!IMPORTANT]
> 在每個時間戳記上，查詢對於每個維度組合最多僅應傳回一筆記錄。 而且，查詢所傳回的所有記錄必須具有相同的時間戳記。 Metrics Advisor 會對每個時間戳記執行此查詢，以擷取您的資料。 如需詳細資訊和範例，請參閱[查詢的常見問題集一節](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)。 