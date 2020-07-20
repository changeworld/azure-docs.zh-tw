---
title: 不雅內容篩選-Translator
titleSuffix: Azure Cognitive Services
description: 使用 [不雅內容篩選] 來判斷您的文字在 Azure 認知服務 Translator 中翻譯的不雅內容等級。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7ebfe766e6362a3f62e70db8bf2dcae370aceee3
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996154"
---
# <a name="add-profanity-filtering-with-the-translator"></a>使用翻譯工具新增不雅內容篩選

Translator 服務通常會在翻譯中保留存在於來源中的粗話。 粗話程度和導致單字不雅的內容在文化特性之間各異。 因此，粗話程度在目標語言中可能增強，也可能減弱。

如果您想要避免在翻譯中看到粗話 (即使來源文字就有粗話)，可使用 Translate() 方法中可用的粗話篩選選項。 此選項可讓您選擇是要看到粗話被刪除、以適當的標籤標記起來，還是不採取任何動作。

轉譯（）方法會採用 "options" 參數，其中包含新的元素 "ProfanityAction"。 ProfanityAction 接受的值為 "NoAction"、"已標記" 和 "Deleted"。

## <a name="accepted-values-of-profanityaction-and-examples"></a>可接受的 ProfanityAction 值和範例
|ProfanityAction 值 | 動作 | 範例：來源 - 日文 | 範例：目標 - 英文|
| :---|:---|:---|:---|
| NoAction | 預設值。 與未設定此選項時相同。 粗話會從來源傳遞到目標。 | 彼は変態です。 | He is a jerk. |
| Marked | 不雅字眼字會以 XML 標記括住 ... \<profanity> \</profanity> 。 | 彼は変態です。 | 他是 \<profanity> 直覺式 \</profanity> 。 |
| 已刪除 | 將會從輸出中移除不雅單字，而不予以取代。 | 彼は。 | He is a. |

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用您的 Translator 呼叫來套用不雅內容篩選](reference/v3-0-translate.md)
