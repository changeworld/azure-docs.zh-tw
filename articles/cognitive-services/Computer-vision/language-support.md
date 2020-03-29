---
title: 語言支援 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 本文提供了電腦視覺功能支援的自然語言清單;OCR，識別文本和讀取。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221355"
---
# <a name="language-support-for-computer-vision"></a>電腦視覺的語言支援

電腦視覺的某些功能支援多種語言;此處未提及的任何功能僅支援英語。

## <a name="text-recognition"></a>文字辨識

電腦視覺可以識別多種語言的文本。 具體來說[，OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API 支援多種語言，而[讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)API 和[識別文本](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)API 僅支援英語。 有關此功能和每個 API 的優勢的詳細資訊，請參閱[識別列印和手寫文本](concept-recognizing-text.md)。

OCR 自動檢測輸入材料的語言，因此無需在 API 呼叫中指定語言代碼。 但是，語言代碼始終作為`"language"`JSON 回應中的節點值返回。

|語言| 語言代碼 | OCR API |
|:-----|:----:|:-----:|
|阿拉伯文 | `ar`|✔ |
|中文 (簡體) | `zh-Hans`|✔ |
|中文 (繁體) | `zh-Hant`|✔ |
|捷克文 | `cs` |✔ |
|丹麥文 | `da` |✔ |
|荷蘭文 | `nl` |✔ |
|英文 | `en` |✔ |
|芬蘭文 | `fi` |✔ |
|法文 | `fr` |✔ |
|德文 | `de` |✔ |
|希臘文 | `el` |✔ |
|匈牙利文 | `hu` |✔ |
|義大利文 | `it` |✔ |
|日文 | `ja` |✔ |
|韓文 | `ko` |✔ |
|挪威文 | `nb` |✔ |
|波蘭文 | `pl` |✔ |
|葡萄牙文 | `pt` |✔ |
|羅馬尼亞文 | `ro` |✔ |
|俄文 | `ru` |✔ |
|塞爾維亞文 (斯拉夫) | `sr-Cyrl` |✔ |
|塞爾維亞文 (拉丁) | `sr-Latn` |✔ |
|斯洛伐克文 | `sk` |✔ |
|西班牙文 | `es` |✔ |
|瑞典文 | `sw` |✔ |
|土耳其文 | `tr` |✔ |

## <a name="image-analysis"></a>圖像分析

分析 -[圖像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 的某些操作可以返回其他語言的結果，使用`language`查詢參數指定。 其他操作返回英語結果，無論指定何種語言，而其他操作都會為不支援的語言引發異常。 操作使用`visualFeatures`和`details`查詢參數指定;有關使用圖像分析可以執行的所有操作的清單，請參閱[概述](home.md)。

|語言 | 語言代碼 | 類別 | Tags | 描述 | 成人 | 品牌 | Color | 笑臉 | ImageType | 物件 | 名人 | 特徵點 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中文 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英文 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日文 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|葡萄牙文 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|西班牙文 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>後續步驟

開始使用本指南中提到的電腦視覺功能。

* [分析本地圖像 （REST）](./quickstarts/csharp-analyze.md)
* [提取列印的文本 （REST）](./quickstarts/csharp-print-text.md)