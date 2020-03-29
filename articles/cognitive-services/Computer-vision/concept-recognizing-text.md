---
title: 列印，手寫文本識別 - 電腦視覺
titleSuffix: Azure Cognitive Services
description: 使用電腦視覺 API 辨識影像中印刷和手寫文字的相關概念。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a4c90ed12c8023e0b9ebc509b20d8d9224b49f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221359"
---
# <a name="recognize-printed-and-handwritten-text"></a>辨識印刷和手寫文字

電腦視覺提供了許多服務來檢測和提取圖像中顯示的列印或手寫文本。 這在各種方案中非常有用，例如記筆記、醫療記錄、安全性和銀行。 以下三個部分詳細介紹了三個不同的文本識別 API，每個 API 針對不同的用例進行了優化。

## <a name="read-api"></a>讀取 API

讀取 API 使用我們最新的識別模型檢測圖像中的文本內容，並將標識的文本轉換為機器可讀字元流。 它針對文本重圖像（如經過數位掃描的文檔）和具有大量視覺噪音的圖像進行了優化。 它將確定每行文本使用哪種識別模型，支援包含列印和手寫文本的圖像。 讀取 API 以非同步方式執行，因為較大的文檔可能需要幾分鐘才能返回結果。

讀取操作在其輸出中維護已識別單詞的原始行分組。 每行都帶有邊界框座標，行中的每個單詞也有其自己的座標。 如果一個詞被低信地識別，那資訊也傳達。 有關詳細資訊，請參閱[讀取 API 參考文檔](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)和[讀取 API 預覽參考文檔](https://go.microsoft.com/fwlink/?linkid=2118322)。

> [!NOTE]
> 此功能僅適用于英語和西班牙文（預覽）文本。

### <a name="image-requirements"></a>影像需求

讀取 API 適用于滿足以下要求的圖像：

- 圖像必須以 JPEG、PNG、BMP、PDF 或 TIFF 格式顯示。
- 圖像的尺寸必須在 50 x 50 和 10000 x 10000 圖元之間。 PDF 頁面必須為 17 x 17 英寸或更小。
- 圖像的檔案大小必須小於 20 MB。

### <a name="limitations"></a>限制

如果使用免費套餐訂閱，則讀取 API 將僅處理 PDF 或 TIFF 文檔的前兩頁。 使用付費訂閱，它將處理多達 200 頁。 另請注意，API 每頁最多檢測 300 行。

## <a name="ocr-optical-character-recognition-api"></a>OCR（光學字元辨識）API

電腦視覺的光學字元辨識 （OCR） API 與讀取 API 類似，但它同步執行，並且未針對大型文檔進行優化。 它使用較早的識別模型，但適用于更多語言;有關支援的語言的完整清單，請參閱[語言支援](language-support.md#text-recognition)。

若有必要，OCR 會以角度為單位傳回以水平影像座標軸為依據的旋轉位移，來修正已辨識文字的旋轉。 OCR 還提供每個單詞的幀座標，如下圖所示。

![正在旋轉的圖像及其文本正在讀取和描繪](./Images/vision-overview-ocr.png)

有關詳細資訊，請參閱[OCR 參考文檔](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)。

### <a name="image-requirements"></a>影像需求

OCR API 適用于滿足以下要求的圖像：

* 圖像必須以 JPEG、PNG、GIF 或 BMP 格式顯示。
* 輸入圖像的大小必須介於 50 x 50 和 4200 x 4200 圖元之間。
* 影像中的文字能以 90 度的任何倍數進行旋轉，並可輔以不超過 40 度的小角度旋轉。

### <a name="limitations"></a>限制

在主要由文字構成的相片上，部分辨識的文字可能會造成誤判。 在某些照片上，尤其是沒有任何文字的照片，精度可能因圖像類型而異。

## <a name="recognize-text-api"></a>識別文本 API

> [!NOTE]
> 識別文本 API 正在棄用，轉而使用讀取 API。 讀取 API 具有類似的功能，並更新以處理 PDF、TIFF 和多頁檔。

識別文本 API 與 OCR 類似，但它非同步執行並使用更新的識別模型。 有關詳細資訊，請參閱[識別文本 API 參考文檔](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)。

### <a name="image-requirements"></a>影像需求

識別文本 API 適用于滿足以下要求的圖像：

- 圖像必須以 JPEG、PNG 或 BMP 格式顯示。
- 圖像的尺寸必須在 50 x 50 和 4200 x 4200 圖元之間。
- 圖像的檔案大小必須小於 4 MB。

## <a name="limitations"></a>限制

文本識別操作的準確性取決於圖像的品質。 以下因素可能導致讀數不准確：

* 影像模糊。
* 手寫或草寫的文字。
* 藝術字型樣式。
* 文字太小。
* 複雜的背景、陰影、文字反光或透視失真。
* 單詞開頭的大寫字母過大或缺失。
* 文字加上了下標、上標或刪除線。

## <a name="next-steps"></a>後續步驟

按照["提取文本（閱讀）](./QuickStarts/CSharp-hand-text.md)快速入門"在簡單的 C# 應用中實現文本識別。
