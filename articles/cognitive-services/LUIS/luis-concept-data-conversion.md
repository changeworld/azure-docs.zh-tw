---
title: 資料轉換-LUIS
titleSuffix: Azure Cognitive Services
description: 了解在 Language Understanding (LUIS) 中的預測之前如何改變語句
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b2455df87c8eae1a48cb6c8b1381dad85d304bf4
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82099235"
---
# <a name="convert-data-format-of-utterances"></a>轉換語句的資料格式
LUIS 在預測之前，會提供使用者語句的下列轉換

* 使用[認知服務語音](../Speech-Service/overview.md)服務的語音轉換文字。

## <a name="speech-to-text"></a>語音轉換文字

語音轉換文字是以與 LUIS 的整合方式來提供。

### <a name="intent-conversion-concepts"></a>意圖轉換概念
LUIS 的語音轉換文字功能可讓您將口頭語句傳送到端點並接收 LUIS 預測回應。 此流程整合了[語音](https://docs.microsoft.com/azure/cognitive-services/Speech)服務與 LUIS。 透過[教學課程](../speech-service/how-to-recognize-intents-from-speech-csharp.md)，深入了解語音轉換到意圖的更多資訊。

### <a name="key-requirements"></a>重要需求
您不必為此整合建立 **Bing 語音 API** 金鑰。 在 Azure 入口網站中建立的 **Language Understanding** 金鑰適用於此整合。 請勿使用 LUIS 入門金鑰。

### <a name="pricing-tier"></a>定價層
此整合會使用與一般 Language Understanding 定價層不同的[定價](luis-limits.md#key-limits)模型。

### <a name="quota-usage"></a>配額使用方式
相關資訊請參閱[重要限制](luis-limits.md#key-limits)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [擷取資料](luis-concept-data-extraction.md)

