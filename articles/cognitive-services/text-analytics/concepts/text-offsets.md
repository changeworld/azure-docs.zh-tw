---
title: 文本分析 API 中的文本偏移量
titleSuffix: Azure Cognitive Services
description: 瞭解多語言編碼和表情符號編碼造成的偏移量。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221191"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>文本分析 API 輸出中的文本偏移量

多語言和表情符號支援已導致 Unicode 編碼使用多個[代碼點](https://wikipedia.org/wiki/Code_point)來表示單個顯示的字元，稱為圖形學。 例如，🌷等表情符號，👍可以使用多個字元來組成具有視覺屬性的其他字元（如膚色）的形狀。 同樣，印度文單詞`अनुच्छेद`被編碼為五個字母和三個組合標記。

由於可能的多語言編碼和表情符號編碼長度不同，文本分析 API 可能會在回應中返回偏移量。

## <a name="offsets-in-the-api-response"></a>API 回應中的偏移量。 

每當返回偏移時，API 回應（如[命名實體識別](../how-tos/text-analytics-how-to-entity-linking.md)或[情緒分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)）請記住以下內容：

* 回應中的元素可能特定于調用的終結點。 
* HTTP POST/GET 有效負載在[UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)中編碼，這可能是用戶端編譯器或作業系統上的預設字元編碼。
* 偏移量是指基於[Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)標準而不是字元計數的圖目數。

## <a name="extracting-substrings-from-text-with-offsets"></a>從具有偏移量的文本中提取子字串

使用基於字元的子字串方法（例如 .NET[子字串）](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8)方法時，偏移可能會導致問題。 一個問題是，偏移量可能會導致子字串方法以多字元圖目數編碼而不是結束結束結束。

在 .NET 中，請考慮使用[StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)類，該類使您能夠將字串用作一系列文本元素，而不是單個字元物件。 您還可以在首選軟體環境中查找石墨分分器庫。 

為了方便起見，文本分析 API 也返回這些文本元素。

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [實體識別](../how-tos/text-analytics-how-to-entity-linking.md)
* [偵測語言種類](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [辨識語言](../how-tos/text-analytics-how-to-language-detection.md)
