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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854036"
---
# <a name="using-decoration-markers-to-highlight-text"></a>使用裝飾標記醒目提示文字

必應支援命中突出顯示，該突出顯示在某些答案的顯示字串中標記查詢詞（或必應認為相關的其他術語）。 例如，網頁結果的`name`、`displayUrl`和`snippet`欄位可能包含標記的查詢術語。 

根據預設，Bing 不會在顯示字串中包含醒目提示標記。 要啟用標記，請在請求中包括`textDecorations`查詢參數並將其設置為`true`。

## <a name="hit-highlighting-example"></a>點擊突出顯示示例

下面的示例顯示了 的`Sailing Dinghy`Web 結果。 必應使用 E000 和 E001 Unicode 字元標記了查詢術語的開始和結束。
  
![搜尋結果醒目提示](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

在使用者介面中顯示結果之前，請將 Unicode 字元替換為適合您的顯示格式的字元。

## <a name="marker-formatting"></a>標記格式

必應提供了使用 Unicode 字元或 HTML 標籤作為標記的選項。 要指定要使用的標記，請包括[文本格式](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat)查詢參數： 

| 值             | 標記                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | 單碼字元（預設） |
| `textFormat=HTML` | HTML 字元              |

## <a name="additional-text-decorations"></a>其他文本裝飾

必應可以返回幾個不同的文本裝飾。 例如，`Computation`答案可以包含`log(2)``expression`欄位中查詢術語的子標標記。

![computation 標記](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

如果請求未指定修飾，則`expression`該欄位將包含`log10(2)`。 

如果是`textDecorations``true`，必應可在顯示答案字串中包含以下標記。 如果沒有等效的 HTML 標籤，則表儲存格為空。

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