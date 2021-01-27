---
title: 不雅內容篩選-Translator
titleSuffix: Azure Cognitive Services
description: 使用「不雅內容篩選」來判斷在您的 Azure 認知服務翻譯工具中，文字轉譯的不雅內容層級。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 9f389d669e69dbfa6ec6d4d0b4716d2367443f17
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896794"
---
# <a name="add-profanity-filtering-with-the-translator"></a>使用翻譯工具新增不雅內容篩選

Translator 服務通常會在翻譯中保留存在於來源中的粗話。 粗話程度和導致單字不雅的內容在文化特性之間各異。 因此，粗話程度在目標語言中可能增強，也可能減弱。

如果您想要避免在翻譯中看到粗話 (即使來源文字就有粗話)，可使用 Translate() 方法中可用的粗話篩選選項。 此選項可讓您選擇是要看到粗話被刪除、以適當的標籤標記起來，還是不採取任何動作。

轉譯 ( # A1 方法會接受 "options" 參數，其中包含新的元素 "ProfanityAction"。 接受的 ProfanityAction 值為 "NoAction"、"標記" 和 "Deleted"。

## <a name="accepted-values-of-profanityaction-and-examples"></a>可接受的 ProfanityAction 值和範例
|ProfanityAction 值 | 動作 | 範例：來源 - 日文 | 範例：目標 - 英文|
| :---|:---|:---|:---|
| NoAction | 預設值。 與未設定此選項時相同。 粗話會從來源傳遞到目標。 | 彼は変態です。 | He is a jerk. |
| Marked | 不雅單字會以 \<profanity> XML 標記括住 ... \</profanity> | 彼は変態です。 | 他是 \<profanity> 直覺式 \</profanity> 。 |
| 已刪除 | 將會從輸出中移除不雅單字，而不予以取代。 | 彼は。 | He is a. |

## <a name="next-steps"></a>後續步驟
> [!div class="nextstepaction"]
> [使用翻譯工具呼叫套用不雅內容篩選](reference/v3-0-translate.md)
