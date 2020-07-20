---
title: 如何使用裝飾標記醒目提示文字 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Bing Web 搜尋 API 在您的搜尋結果中使用文字裝飾與叫用醒目提示。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "68854036"
---
# <a name="using-decoration-markers-to-highlight-text"></a>使用裝飾標記醒目提示文字

Bing 支援搜尋結果醒目提示，這會在某些答案的顯示字串中標示查詢詞彙（或 Bing 找到的其他詞彙）。 例如，網頁結果的`name`、 `displayUrl`和`snippet`欄位可能包含已標記的查詢字詞。 

根據預設，Bing 不會在顯示字串中包含醒目提示標記。 若要啟用標記，請在`textDecorations`您的要求中包含查詢參數，並`true`將它設定為。

## <a name="hit-highlighting-example"></a>搜尋反白顯示範例

下列範例顯示的 web 結果`Sailing Dinghy`。 Bing 會使用 E000 和 E001 Unicode 字元來標示查詢詞彙的開頭和結尾。
  
![搜尋結果醒目提示](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

在使用者介面中顯示結果之前，請使用適合您的顯示格式的 Unicode 字元來取代它們。

## <a name="marker-formatting"></a>標記格式化

Bing 提供了使用 Unicode 字元或 HTML 標籤做為標記的選項。 若要指定要使用的標記，請包含[textFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat)查詢參數： 

| 值             | 標記                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode 字元（預設值） |
| `textFormat=HTML` | HTML 字元              |

## <a name="additional-text-decorations"></a>其他文字裝飾

Bing 可以傳回數種不同的文字裝飾。 例如， `Computation`答案可以包含`log(2)` `expression`欄位中查詢字詞的注標標記。

![computation 標記](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

如果要求未指定裝飾，此`expression`欄位會包含。 `log10(2)` 

如果`textDecorations`為`true`，Bing 可能會在答案的顯示字串中包含下列標記。 如果沒有對等的 HTML 標籤，則資料表資料格會是空的。

|Unicode|HTML|描述
|-|-|-
|U+E000|\<b>|標示查詢詞彙的開頭 (搜尋結果醒目提示)
|U+E001|\</b>|標示查詢詞彙的結尾
|U+E002|\<i>|標示斜體內容的開頭 
|U+E003|\</i>|標示斜體內容的結尾
|U+E004|\<br/>|標示分行符號
|U+E005||標示電話號碼的開頭
|U+E006||標示電話號碼的結尾
|U+E007||標示地址的開頭
|U+E008||標示地址的結尾
|U+E009|\&nbsp;|標示不分行空格
|U+E00C|\<strong>|標示粗體內容的開頭
|U+E00D|\</strong>|標示粗體內容的結尾
|U+E00E||標示背景應該比其周圍背景淡之內容的開頭
|U+E00F||標示背景應該比其周圍背景淡之內容的結尾
|U+E010||標示背景應該比其周圍背景深之內容的開頭
|U+E011||標示背景應該比其周圍背景深之內容的結尾
|U+E012|\<del>|標示應刪除之內容的開頭
|U+E013|\</del>|標示應刪除之內容的結尾
|U+E016|\<sub>|標示下標內容的開頭
|U+E017|\</sub>|標示下標內容的結尾
|U+E018|\<sup>|標示上標內容的結尾
|U+E019|\</sup>|標示上標內容的開頭

## <a name="next-steps"></a>後續步驟

* [什麼是 Bing Web 搜尋 API？](overview.md) 
* [調整大小和裁剪縮圖](resize-and-crop-thumbnails.md)