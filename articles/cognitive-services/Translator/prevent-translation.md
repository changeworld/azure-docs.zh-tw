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
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 33939976a0824ce8afeb2e6f6fb19e7033098683
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592690"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>如何使用翻譯工具防止內容轉譯

翻譯工具可讓您標記內容，使其不會轉譯。 例如，您可以標記程式碼、品牌名稱，或當地語系化後沒有意義的字組/片語。

## <a name="methods-for-preventing-translation"></a>防止翻譯的方法

1. 使用 `notranslate` 標記您的內容。 這是設計的，只有在輸入 textType 設定為 HTML 時才會運作。

   範例：

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. 使用[動態字典](dynamic-dictionary.md)來規定特定翻譯。

3. 請勿將字串傳遞至翻譯工具進行轉譯。

4. 自訂翻譯：使用[自訂翻譯中的字典](custom-translator/what-is-dictionary.md)來規定以100% 機率轉譯的片語。


## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [避免翻譯工具呼叫中的轉譯](reference/v3-0-translate.md)
