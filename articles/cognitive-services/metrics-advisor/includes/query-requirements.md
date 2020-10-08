---
title: 查詢需求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 09/30/2020
ms.author: aahi
ms.openlocfilehash: 518865f78c170f1fbe4e65b96dc149c1b449a88b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91631355"
---
在查詢內，使用 `@StartTime` 參數取得單一時間戳記的計量資料。 Metrics Advisor 在執行查詢時會將該參數取代為 `yyyy-MM-ddTHH:mm:ss` 格式字串。

> [!IMPORTANT]
> 在每個時間戳記上，查詢對於每個維度組合最多僅應傳回一筆記錄。 而且，查詢所傳回的所有記錄必須具有相同的時間戳記。 Metrics Advisor 會對每個時間戳記執行此查詢，以擷取您的資料。 如需詳細資訊和範例，請參閱[查詢的常見問題集一節](../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data)。 