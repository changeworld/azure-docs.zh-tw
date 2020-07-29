---
title: 支援的命名實體識別類別
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
ms.openlocfilehash: f66ed9a7d26a0c2d7def7d17820379b8e3460460
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373059"
---
# <a name="supported-entity-categories-in-the-text-analytics-api-v3"></a>文字分析 API v3 中支援的實體類別

使用本文來尋找可由[命名實體識別](how-tos/text-analytics-how-to-entity-linking.md)（NER）傳回的實體類別。 NER 3.1 的預覽版本也可供使用，其中包括偵測個人（ `PII` ）和健全狀況（ `PHI` ）資訊的功能。 此外，按一下 [**健康**情況] 索引標籤，即可查看文字分析健康狀態的支援類別清單。

## <a name="entity-categories"></a>實體類別

#### <a name="general"></a>[一般](#tab/general)

[!INCLUDE [supported entity types - general](./includes/entity-types/general-entities.md)]

#### <a name="personal"></a>[個人](#tab/personal)

[!INCLUDE [supported entity types - personally identifying information](./includes/entity-types/personal-information-entities.md)]

#### <a name="health"></a>[醫療](#tab/health)

[!INCLUDE [biomedical entity types](./includes/entity-types/health-entities.md)]

***

## <a name="next-steps"></a>後續步驟

* [如何在文字分析中使用已命名的實體識別](how-tos/text-analytics-how-to-entity-linking.md)
