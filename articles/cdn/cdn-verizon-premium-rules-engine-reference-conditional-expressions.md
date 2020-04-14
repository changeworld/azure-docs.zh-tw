---
title: Azure CDN 的條件表示式 - Verizon 進階規則引擎
description: 來自 Verizon 高級版中 Azure CDN 的參考文件會管理引擎匹配條件和功能。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 25b3c1a011e39adea651a6daa1d4ab9aa3a9d186
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253504"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>從 Verizon 進階規則的 Azure CDN 引擎條件運算式

本文列出了 Azure 內容傳遞網路 (CDN)[規則引擎](cdn-verizon-premium-rules-engine.md)的條件表達式的詳細說明。

規則的第一個部分是條件運算式。

條件運算式 | 描述
-----------------------|-------------
IF | IF 運算式永遠是規則中第一個陳述式的一部分。 像所有其他條件運算式一樣，此 IF 陳述式必須與符合項目相關聯。 如果未定義其他條件表達式,則此匹配確定在將一組要素應用於請求之前必須滿足的條件。
AND IF | AND IF 運算式只能在下列類型的條件運算式之後新增︰IF、AND IF。 它表示針對初始 IF 陳述式有必須符合的另一個條件。
ELSE IF| ELSE IF 運算式會指定其他條件，必須在此 ELSE IF 陳述式特定的一組功能發生之前符合。 有 ELSE IF 陳述式表示前一個陳述式的結尾。 可以放在 ELSE IF 陳述式之後的條件運算式是另一個 ELSE IF 陳述式。 這表示 ELSE IF 陳述式只能用來指定必須符合的單一其他條件。

**範例**![:CDN 符合條件](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > 後一項規則可能會覆寫前一項規則所指定的動作。
   > 範例︰全面涵蓋規則會保護所有透過以權杖為基礎的驗證要求。 可以直接在它底下建立另一個規則，對特定類型的要求產生例外狀況。

## <a name="next-steps"></a>後續步驟

- [Azure CDN 概觀](cdn-overview.md)
- [規則引擎參考](cdn-verizon-premium-rules-engine-reference.md)
- [規則引擎符合條件](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [規則引擎功能](cdn-verizon-premium-rules-engine-reference-features.md)
- [使用規則引擎覆寫預設的 HTTP 行為](cdn-verizon-premium-rules-engine.md)