---
title: 文字分析 API 中的文字位移
titleSuffix: Azure Cognitive Services
description: 瞭解多語系和表情編碼所造成的位移。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 14fd7c2b034077d818d1a1224d3c4c12a7fc07bc
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855635"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>文字分析 API 輸出中的文字位移

多語系和表情支援導致 Unicode 編碼，使用一個以上的程式 [代碼點](https://wikipedia.org/wiki/Code_point) 來代表一個顯示的字元，稱為語素簇。 例如，類似🌷的 emoji， 👍 可能會使用數個字元來撰寫視覺屬性的其他字元（例如外觀色調）的圖形。 同樣地，印度文文字 `अनुच्छेद` 會編碼為五個字母和三個組合標記。

因為可能多語系和表情編碼的長度不同，文字分析 API 可能會傳迴響應中的位移。

## <a name="offsets-in-the-api-response"></a>API 回應中的位移。 

每當傳回位移時，API 回應（例如 [命名實體](../how-tos/text-analytics-how-to-entity-linking.md) 辨識或 [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)）都會記住下列各項：

* 回應中的元素可能是所呼叫端點的特定專案。 
* HTTP POST/GET 承載以 [utf-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)編碼，這可能是用戶端編譯器或作業系統上的預設字元編碼。
* 位移會根據 [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) 標準（而非字元計數）參考語素簇計數。

## <a name="extracting-substrings-from-text-with-offsets"></a>從具有位移的文字中解壓縮子字串

使用以字元為基礎的子字串方法時，位移可能會造成問題，例如 [ ( # B1 ](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) 方法的 .net 子字串。 其中一個問題是，位移可能會導致子字串方法在多字元語素簇編碼（而非結尾）的中間結束。

在 .NET 中，請考慮使用 [system.globalization.stringinfo>](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) 類別，此類別可讓您使用字串作為一連串的文字專案，而不是個別的字元物件。 您也可以在慣用的軟體環境中尋找語素簇分隔器程式庫。 

為了方便起見，文字分析 API 也會傳回這些文字元素。

## <a name="offsets-in-api-version-31-preview"></a>API 版本 3.1-preview 中的位移

從 API 版本 3.1-preview. 1 開始，所有傳回 offset 文字分析 API 端點都將支援 `stringIndexType` 參數。 此參數會調整 `offset` `length` API 輸出中的和屬性，以符合要求的字串反復專案配置。 目前，我們支援三種類型：

1. `textElement_v8` (預設) ：逐一查看 [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) 標準所定義的 graphemes
2. `unicodeCodePoint`：反復查看 [Unicode 程式碼點](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564)（Python 3 的預設配置）
3. `utf16CodeUnit`：逐一查看 [utf-16 程式碼單位](https://unicode.org/faq/utf_bom.html#UTF16)，這是 JAVAscript、JAVA 和 .net 的預設配置

如果 `stringIndexType` 要求的符合選擇的程式設計環境，則可以使用標準的子字串或配量方法來進行子字串解壓縮。 

## <a name="see-also"></a>另請參閱

* [文字分析概觀](../overview.md)
* [情感分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [實體辨識](../how-tos/text-analytics-how-to-entity-linking.md)
* [偵測語言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [辨識語言](../how-tos/text-analytics-how-to-language-detection.md)
