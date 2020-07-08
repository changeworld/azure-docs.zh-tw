---
title: 適用于 Azure CDN 的標準規則引擎參考 |Microsoft Docs
description: 適用于 Azure 內容傳遞網路（Azure CDN）之標準規則引擎中的比對條件和動作的參考檔。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/22/2020
ms.author: allensu
ms.openlocfilehash: 6260a4b78197329e020bebaa3bc08db5ad792086
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559299"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的標準規則引擎參考

在適用于 Azure 內容傳遞網路（Azure CDN）的[標準規則引擎](cdn-standard-rules-engine.md)中，規則包含一或多個比對條件和動作。 本文提供適用于 Azure CDN 之標準規則引擎中的比對條件和功能的詳細說明。

規則引擎是設計來作為標準 Azure CDN 處理特定類型要求的最終授權。

**規則的常見用法**：

- 覆寫或定義自訂快取原則。
- 將要求重新導向。
- 修改 HTTP 要求和回應標頭。

## <a name="terminology"></a>詞彙

若要在規則引擎中定義規則，請設定 [比對[條件](cdn-standard-rules-engine-match-conditions.md)] 和 [[動作](cdn-standard-rules-engine-actions.md)]：

 ![Azure CDN 規則結構](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每個規則最多可以有10個相符條件和五個動作。 每個 Azure CDN 端點最多可以有25個規則。 

此限制中包含的是預設的*全域規則*。 全域規則沒有符合條件;全域規則中定義的動作一律會觸發。

## <a name="limits-and-pricing"></a>限制和定價 

每個 Azure CDN 端點最多可以有25個規則。 每個規則最多可以有10個相符條件和五個動作。 規則引擎的定價遵循下列維度： 
- 規則：每月每個規則 $1 
- 已處理的要求：每百萬 multi-factor $0.60
- 前5個規則將維持免費

## <a name="syntax"></a>Syntax

在規則中處理特殊字元的方式會根據不同的比對條件和動作如何處理文字值而有所不同。 比對條件或動作可以使用下列其中一種方式來解讀文字：

- [常值](#literal-values)
- [萬用字元值](#wildcard-values)


### <a name="literal-values"></a>常值

轉譯為常值的文字會將所有特殊字元 *（除了% 符號之外*）視為在規則中必須符合的值的一部分。 例如， `'*'` 只有在找到精確的值時，才會滿足設定為的常值比對條件 `'*'` 。

百分比符號是用來表示 URL 編碼（例如 `%20` ）。

### <a name="wildcard-values"></a>萬用字元值

轉譯為萬用字元值的文字會將額外意義指派給特殊字元。 下表描述如何在標準規則引擎中解讀特定特殊字元：

字元 | 描述
----------|------------
\ | 反斜線是用來逸出此資料表中指定的任何字元。 必須在逸出特殊字元之前指定反斜線。 例如，下列語法逸出星號︰`\*`
% | 百分比符號是用來表示 URL 編碼（例如 `%20` ）。
\* | 星號為萬用字元，表示一或多個字元。
space | 空白字元表示符合條件可由指定的任何值或模式滿足。
單引號 | 單引號沒有特殊意義。 不過，一組單引號表示應該將值視為常值。 您可以透過下列方式使用單引號：<ul><li>當指定的值符合比較值的任何部分時，允許滿足符合條件。  例如，`'ma'` 會比對下列任何字串︰ <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>允許將特殊字元指定為常值字元。 例如，您可以指定常值空白字元，方法是在一組單引號（或）中括住空白字元 `' '` `'<sample value>'` 。</li><li>表示允許指定空白值。 藉由指定一組單引號（**' '**）來指定空白值。</li></ul>**重要**：<br /><ul><li>如果指定的值不包含萬用字元，則此值會自動視為常值。 您不需要為常值指定一組單引號。</li><li>如果未使用反斜線來轉義此資料表中的另一個字元，則會在一組單引號中指定反斜線時予以忽略。</li><li>另一種將特殊字元指定為常值字元的方法，就是使用反斜線（）來將它換成 `\` 。</li></ul>

## <a name="next-steps"></a>後續步驟

- [標準規則引擎中的比對條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎中的動作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)
