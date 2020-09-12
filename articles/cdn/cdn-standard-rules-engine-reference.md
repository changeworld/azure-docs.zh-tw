---
title: Azure CDN 的標準規則引擎參考 |Microsoft Docs
description: 適用于 Azure 內容傳遞網路之標準規則引擎中的比對條件和動作的參考檔 (Azure CDN) 。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: cae0ec1daed8277d1e7e4f3c5cbc701965ac00b8
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661761"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的標準規則引擎參考

在 Azure 內容傳遞網路的 [標準規則引擎](cdn-standard-rules-engine.md) (azure CDN) 中，規則是由一或多個符合條件和動作所組成。 本文提供適用于 Azure CDN 標準規則引擎中的相符條件和功能的詳細說明。

規則引擎是設計來作為標準 Azure CDN 處理特定類型要求的最終授權。

**規則的常見用法**：

- 覆寫或定義自訂快取原則。
- 將要求重新導向。
- 修改 HTTP 要求和回應標頭。

## <a name="terminology"></a>詞彙

若要在規則引擎中定義規則，請設定 [符合條件](cdn-standard-rules-engine-match-conditions.md) 和 [動作](cdn-standard-rules-engine-actions.md)：

 ![Azure CDN 規則結構](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每個規則最多可以有十個相符條件和五個動作。 每個 Azure CDN 端點最多可有25個規則。 

這項限制中包含了預設的 *全域規則*。 全域規則沒有符合條件;全域規則中所定義的動作一律會觸發。

## <a name="limits-and-pricing"></a>限制和定價 

每個 Azure CDN 端點最多可有25個規則。 每個規則最多可以有十個相符條件和五個動作。 規則引擎的定價遵循下列維度： 
- 規則：每月每個規則 $1 
- 處理的要求：每百萬 multi-factor $0.60
- 前5個規則將保持免費

## <a name="syntax"></a>語法

在規則中處理特殊字元的方式會根據不同的比對條件和動作處理文字值的方式而有所不同。 比對條件或動作可以用下列其中一種方式來解讀文字：

- [常值](#literal-values)
- [萬用字元值](#wildcard-values)


### <a name="literal-values"></a>常值

解釋為常值的文字會將所有特殊字元 *（除了% 符號之外* ）視為在規則中必須符合的值一部分。 例如， `'*'` 只有在找到精確的值時，才會滿足設定為的常值比對條件 `'*'` 。

百分比符號是用來表示 URL 編碼 (例如 `%20`) 。

### <a name="wildcard-values"></a>萬用字元值

目前我們支援標準規則引擎中 UrlPath 比對 **條件** 中的萬用字元。 \*字元是代表一或多個字元的萬用字元。 

## <a name="next-steps"></a>接下來的步驟

- [標準規則引擎中的比對條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)
