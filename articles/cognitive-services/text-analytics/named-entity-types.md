---
title: 命名實體辨識的支援類別
titleSuffix: Azure Cognitive Services
description: 瞭解文字分析 API 中支援的實體類別。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/28/2020
ms.author: aahi
ms.openlocfilehash: 77b75b1134bbc8366478b1f9f4d14e86e9684f70
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709195"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>文字分析 API v3 中支援的實體類別

您可以使用本文來尋找可由 [命名實體](how-tos/text-analytics-how-to-entity-linking.md) 辨識 (NER) 傳回的實體類別。 也提供 NER 3.1 版的預覽，其中包含偵測個人 (`PII`) 和健康 (`PHI`) 資訊的能力。 此外，按一下 [ **健康** 情況] 索引標籤，以查看文字分析的健全狀況清單。

## <a name="entity-categories"></a>實體類別

#### <a name="general"></a>[一般](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="pii"></a>[PII](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[醫療](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>後續步驟

* [如何在文字分析中使用命名實體辨識](how-tos/text-analytics-how-to-entity-linking.md)
