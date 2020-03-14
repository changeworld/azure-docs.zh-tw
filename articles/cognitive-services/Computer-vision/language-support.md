---
title: 語言支援-電腦視覺
titleSuffix: Azure Cognitive Services
description: 本文提供電腦視覺功能所支援的自然語言清單;OCR，辨識文字並讀取。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: a834c68119340d796f87971912a07fc0524a6d21
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221355"
---
# <a name="language-support-for-computer-vision"></a>電腦視覺的語言支援

電腦視覺的部分功能支援多種語言;此處未提及的任何功能僅支援英文。

## <a name="text-recognition"></a>文字辨識

電腦視覺可以辨識許多語言的文字。 具體而言， [OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) API 支援多種語言，而[讀取](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)api 和[辨識文字](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)api 則僅支援英文。 如需這項功能的詳細資訊和每個 API 的優點，請參閱[辨識印刷和手寫文字](concept-recognizing-text.md)。

OCR 會自動偵測輸入材質的語言，因此不需要在 API 呼叫中指定語言代碼。 不過，語言代碼一律會傳回為 JSON 回應中 `"language"` 節點的值。

|Language| 語言代碼 | OCR API |
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

## <a name="image-analysis"></a>影像分析

[分析影像](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)API 的某些動作可能會以 `language` 查詢參數所指定的其他語言傳回結果。 無論指定何種語言，其他動作都會傳回英文的結果，而其他動作則會擲回不支援語言的例外狀況。 動作是使用 `visualFeatures` 和 `details` 查詢參數所指定。請參閱[總覽](home.md)，以取得您可以使用影像分析執行的所有動作清單。

|Language | 語言代碼 | 類別 | Tags | 描述 | 帶有 | 品牌 | Color | 笑臉 | ImageType | 物件 | 名人 | 特徵點 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中文 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英文 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日文 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|葡萄牙文 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|西班牙文 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>後續步驟

開始使用本指南中所述的電腦視覺功能。

* [分析本機影像（REST）](./quickstarts/csharp-analyze.md)
* [解壓縮列印的文字（REST）](./quickstarts/csharp-print-text.md)