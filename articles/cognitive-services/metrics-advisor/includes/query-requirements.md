---
title: 查詢需求
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/10/2020
ms.author: aahi
ms.openlocfilehash: 23da214f4ffc9e589523f7ada00a77494183eb15
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934691"
---
在查詢內，請使用 `@StartTime` 參數來取得特定時間戳記的計量資料。 這會取代為 `yyyy-MM-ddTHH:mm:ss` 格式字串。 

> [!IMPORTANT]
> 請確定查詢只會傳回**單一時間戳記**的計量資料。 Metrics Advisor 會針對每個時間戳記執行查詢，以取得對應的計量資料。 例如，具有「每日」資料細微性的計量查詢應該只包含單個時間戳記，例如在執行查詢一次時的 `2020-06-21T00:00:00Z`。 
