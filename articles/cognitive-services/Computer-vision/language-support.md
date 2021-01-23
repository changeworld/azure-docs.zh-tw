---
title: 語言支援-電腦視覺
titleSuffix: Azure Cognitive Services
description: 本文提供電腦視覺功能所支援的自然語言清單;OCR，影像分析。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: 1abb857c9f03be502db02099383c6fe0b5110461
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736813"
---
# <a name="language-support-for-computer-vision"></a>電腦視覺的語言支援

電腦視覺的某些功能支援多種語言;此處未提及的任何功能只支援英文。

## <a name="optical-character-recognition-ocr"></a>光學字元辨識 (OCR)

電腦視覺的 OCR Api 支援數種語言。 您不需要指定語言代碼。 如需詳細資訊，請參閱 [光學字元辨識 (OCR) ](concept-recognizing-text.md) 。

|Language| 語言代碼 | OCR API | 閱讀3.0 和3。1 | 閱讀 3.2-preview. 1 |
|:-----|:----:|:-----:|:---:|:---:|
|阿拉伯文 | `ar`|✔ | | |
|簡體中文 | `zh-Hans`|✔ | |✔ |
|繁體中文 | `zh-Hant`|✔ | | |
|捷克文 | `cs` |✔ | | |
|丹麥文 | `da` |✔ | | |
|荷蘭文 | `nl` |✔ |✔ |✔ |
|英文 | `en` |✔ |✔ |✔ |
|芬蘭文 | `fi` |✔ | | |
|法文 | `fr` |✔ |✔ |✔ |
|德文 | `de` |✔ |✔ |✔ |
|希臘文 | `el` |✔ | | |
|匈牙利文 | `hu` |✔ | | |
|義大利文 | `it` |✔ |✔ |✔ |
|日文 | `ja` |✔ | |✔ |
|韓文 | `ko` |✔ | | |
|挪威文 | `nb` |✔ | | |
|波蘭文 | `pl` |✔ | | |
|葡萄牙文 | `pt` |✔ |✔ |✔ |
|羅馬尼亞文 | `ro` |✔ | | |
|俄文 | `ru` |✔ | | |
|塞爾維亞文 (斯拉夫) | `sr-Cyrl` |✔ | | |
|塞爾維亞文 (拉丁) | `sr-Latn` |✔ | | |
|斯洛伐克文 | `sk` |✔ | | |
|西班牙文 | `es` |✔ |✔ |✔ |
|瑞典文 | `sw` |✔ | | |
|土耳其文 | `tr` |✔ | | |

## <a name="image-analysis"></a>影像分析

[分析影像](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)API 的某些動作可能會以查詢參數指定的其他語言傳回結果 `language` 。 無論指定何種語言，其他動作都會以英文傳回結果，而其他動作則會針對不支援的語言擲回例外狀況。 動作是使用 `visualFeatures` 和 `details` 查詢參數指定的; 請參閱 [總覽](overview.md) ，以取得您可以使用影像分析執行的所有動作清單。

|Language | 語言代碼 | 類別 | 標籤 | 描述 | 成人 | 品牌 | Color | 笑臉 | ImageType | 物件 | 名人 | 特徵點 |
|:---|:---:|:----:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|中文 | `zh`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|英文 | `en`   | ✔ | ✔| ✔|✔|✔|✔|✔|✔|✔|✔|✔|
|日文 | `ja`   | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|葡萄牙文 | `pt` | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|
|西班牙文 | `es`    | ✔ | ✔| ✔|-|-|-|-|-|❌|✔|✔|

## <a name="next-steps"></a>後續步驟

開始使用本指南中所述的電腦視覺功能。

* [ (REST) 分析本機影像 ](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-analyze.md)
* [將列印的文字解壓縮 (REST) ](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-print-text.md)