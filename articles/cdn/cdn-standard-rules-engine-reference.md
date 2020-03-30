---
title: Azure CDN 的標準規則引擎引用 |微軟文檔
description: Azure 內容交付網路 （Azure CDN） 的標準規則引擎中匹配條件和操作的參考文檔。
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171568"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN 的標準規則引擎引用

在 Azure 內容交付網路 （Azure CDN）[的標準規則引擎](cdn-standard-rules-engine.md)中，規則由一個或多個匹配條件和操作組成。 本文詳細介紹了 Azure CDN 的標準規則引擎中可用的匹配條件和功能。

規則引擎旨在成為標準 Azure CDN 處理特定類型請求的最終許可權。

**規則的常見用途**：

- 覆寫或定義自訂快取原則。
- 將要求重新導向。
- 修改 HTTP 要求和回應標頭。

## <a name="terminology"></a>詞彙

要在規則引擎中定義規則，請設置[匹配條件和](cdn-standard-rules-engine-match-conditions.md)[操作](cdn-standard-rules-engine-actions.md)：

 ![Azure CDN 規則結構](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

每個規則最多可以有四個匹配條件和三個操作。 每個 Azure CDN 終結點最多隻能有五個規則。 

Azure CDN 終結點的當前五個規則限制中包括預設*全域規則*。 全域規則沒有匹配條件，並且在全域規則中定義的操作始終觸發。

## <a name="syntax"></a>語法

規則中特殊字元的處理方式因匹配條件和操作處理文本值的不同而不同。 匹配條件或操作可以通過以下方式之一解釋文本：

- [文字值](#literal-values)
- [萬用字元值](#wildcard-values)


### <a name="literal-values"></a>常值

解釋為文本值的文本將*除 % 符號之外*的所有特殊字元視為規則中必須匹配的值的一部分。 例如，僅當找到確切值`'*'``'*'`時，才滿足設置為 的文本匹配條件。

百分比符號用於指示 URL 編碼（例如， `%20`。

### <a name="wildcard-values"></a>萬用字元值

被解釋為萬用字元值的文本為特殊字元分配了其他含義。 下表描述了在標準規則引擎中如何解釋特定特殊字元：

字元 | 描述
----------|------------
\ | 反斜線是用來逸出此資料表中指定的任何字元。 必須在逸出特殊字元之前指定反斜線。 例如，下列語法逸出星號︰`\*`
% | 百分比符號用於指示 URL 編碼（例如， `%20`。
\* | 星號為萬用字元，表示一或多個字元。
space | 空白字元指示匹配條件可以通過指定的值或模式之一滿足。
單引號 | 單個引號沒有特殊含義。 但是，一組單引號指示值應視為文本值。 單引號可通過以下方式使用：<ul><li>允許在指定值匹配比較值的任何部分時滿足匹配條件。  例如，`'ma'` 會比對下列任何字串︰ <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>允許將特殊字元指定為文本字元。 例如，可以通過將空白字元括在一組單引號 （`' '`或`'<sample value>'`） 中來指定文本空白字元。</li><li>允許指定空白值。 通過指定一組單引號 **（''**） 指定空值。</li></ul>**重要**：<br /><ul><li>如果指定的值不包含萬用字元，則該值將自動視為文本值。 不需要為文本值指定一組單引號。</li><li>如果反斜線不用於轉義此表中的另一個字元，則在一組單引號中指定反斜線時將忽略它。</li><li>將特殊字元指定為文本字元的另一種方法是使用反斜線 （）`\`來轉義它。</li></ul>

## <a name="next-steps"></a>後續步驟

- [標準規則引擎中的匹配條件](cdn-standard-rules-engine-match-conditions.md)
- [標準規則引擎中的操作](cdn-standard-rules-engine-actions.md)
- [使用標準規則引擎強制執行 HTTPS](cdn-standard-rules-engine.md)
- [Azure CDN 概觀](cdn-overview.md)
