---
title: 適用于 Azure CDN 的標準規則引擎參考 |Microsoft Docs
description: 適用于 Azure 內容傳遞網路 (Azure CDN) 之標準規則引擎中的比對條件和動作的參考檔。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: f729176d3f79c2a1f6fabb5631d49747219db48f
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87760085"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的標準規則引擎參考

在適用于 Azure 內容傳遞網路 (Azure CDN) 的[標準規則引擎](cdn-standard-rules-engine.md)中，規則包含一或多個比對條件和動作。 本文提供適用于 Azure CDN 之標準規則引擎中的比對條件和功能的詳細說明。

規則引擎是設計來作為標準 Azure CDN 處理特定類型要求的最終授權。

**規則的常見用法**：

- 覆寫或定義自訂快取原則。
- 將要求重新導向。
- 修改 HTTP 要求和回應標頭。

## <a name="terminology"></a>術語

若要在規則引擎中定義規則，請設定 [比對[條件](cdn-standard-rules-engine-match-conditions.md)] 和 [[動作](cdn-standard-rules-engine-actions.md)]：

 ![Azure CDN 規則結構](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每個規則最多可以有10個相符條件和五個動作。 每個 Azure CDN 端點最多可以有25個規則。 

此限制中包含的是預設的*全域規則*。 全域規則沒有符合條件;全域規則中定義的動作一律會觸發。

## <a name="limits-and-pricing"></a>限制和定價 

每個 Azure CDN 端點最多可以有25個規則。 每個規則最多可以有10個相符條件和五個動作。 規則引擎的定價遵循下列維度： 
- 規則：每月每個規則 $1 
- 已處理的要求：每百萬 multi-factor $0.60
- 前5個規則將維持免費

## <a name="syntax"></a>語法

在規則中處理特殊字元的方式會根據不同的比對條件和動作如何處理文字值而有所不同。 比對條件或動作可以使用下列其中一種方式來解讀文字：

- [常值](#literal-values)
- [萬用字元值](#wildcard-values)


### <a name="literal-values"></a>常值

轉譯為常值的文字會將所有特殊字元 *（除了% 符號之外*）視為在規則中必須符合的值的一部分。 例如， `'*'` 只有在找到精確的值時，才會滿足設定為的常值比對條件 `'*'` 。

百分比符號是用來表示 URL 編碼 (例如， `%20`) 。

### <a name="wildcard-values"></a>萬用字元值

目前，我們在標準規則引擎的**UrlPath**比對條件中支援萬用字元。 \*字元是表示一或多個字元的星號。 

## <a name="next-steps"></a>後續步驟

- [標準規則引擎中的比對條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)
