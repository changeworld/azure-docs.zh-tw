---
title: 防止內容轉譯-Translator
titleSuffix: Azure Cognitive Services
description: 使用翻譯工具防止內容轉譯。 翻譯工具可讓您標記內容，使其不會轉譯。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: ec39b3692a90f22409e85b5502d3ea874e3282d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91742055"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>如何使用翻譯工具防止內容轉譯

翻譯工具可讓您標記內容，使其不會轉譯。 例如，您可能想要標記在當地語系化時，沒有必要翻譯的程式碼、品牌名稱或字詞/語句。

## <a name="methods-for-preventing-translation"></a>防止翻譯的方法

1. 使用 `notranslate` 標記您的內容。 這是設計，只有在輸入 textType 設定為 HTML 時，才會運作

   範例：

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. 使用 `translate="no"` 標記您的內容。 這僅適用于將輸入 textType 設定為 HTML 時

   範例：

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>

3. Use the [dynamic dictionary](dynamic-dictionary.md) to prescribe a specific translation.

4. Don't pass the string to the Translator for translation.

5. Custom Translator: Use a [dictionary in Custom Translator](custom-translator/what-is-dictionary.md) to prescribe the translation of a phrase with 100% probability.


## Next steps
> [!div class="nextstepaction"]
> [Use the Translate operation to translate text](reference/v3-0-translate.md)
