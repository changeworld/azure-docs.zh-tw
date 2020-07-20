---
title: 文字分析 API 中的文字位移
titleSuffix: Azure Cognitive Services
description: 深入瞭解多語系和表情編碼所造成的位移。
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79221191"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>文字分析 API 輸出中的文字位移

多語系和表情支援導致 Unicode 編碼，使用一個以上的程式[代碼點](https://wikipedia.org/wiki/Code_point)來表示單一顯示的字元，稱為語素簇。 例如，emoji 就像🌷， 👍而且可能會使用數個字元來撰寫圖形，其中包含視覺屬性的其他字元，例如外觀色調。 同樣地，印度文`अनुच्छेद`單字會編碼為五個字母和三個組合標記。

因為多語系和表情編碼的長度不同，文字分析 API 可能會在回應中傳回位移。

## <a name="offsets-in-the-api-response"></a>API 回應中的位移。 

每當傳回位移時，API 回應（例如，[命名實體](../how-tos/text-analytics-how-to-entity-linking.md)辨識或[情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)），請記住下列事項：

* 回應中的元素可能是所呼叫之端點的特定專案。 
* HTTP POST/GET 承載以[utf-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)編碼，這在您的用戶端編譯器或作業系統上不一定是預設的字元編碼。
* 位移會參考以[Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)標準為基礎的語素簇計數，而不是字元計數。

## <a name="extracting-substrings-from-text-with-offsets"></a>從包含位移的文字解壓縮子字串

使用以字元為基礎的子字串方法（例如 .NET [substring （）方法）](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8)時，位移可能會造成問題。 其中一個問題是，位移可能會導致 substring 方法在多字元語素簇編碼的中間結束，而不是結束。

在 .NET 中，請考慮使用[system.globalization.stringinfo>](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)類別，它可讓您使用字串做為一連串的文字元素，而不是個別的字元物件。 您也可以在慣用的軟體環境中尋找語素簇分隔器程式庫。 

為了方便起見，文字分析 API 也會傳回這些文字元素。

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [實體辨識](../how-tos/text-analytics-how-to-entity-linking.md)
* [偵測語言種類](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [辨識語言](../how-tos/text-analytics-how-to-language-detection.md)
