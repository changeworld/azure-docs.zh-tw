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
ms.openlocfilehash: bf8923c1090669caa46ef51a26418933b1cda023
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563427"
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
   ```
   
3. 使用[動態字典](dynamic-dictionary.md)來規定特定翻譯。

4. 請勿將字串傳遞至翻譯工具進行轉譯。

5. 自訂翻譯：使用 [自訂翻譯中的字典](custom-translator/what-is-dictionary.md) 來規定轉譯具有100% 機率的片語。


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用轉譯作業來翻譯文字](reference/v3-0-translate.md)
