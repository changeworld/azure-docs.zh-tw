---
title: 列印的手寫文字識別-電腦視覺
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365463"
---
# <a name="recognize-printed-and-handwritten-text"></a>辨識印刷和手寫文字

電腦視覺提供一些服務，可偵測並解壓縮影像中所顯示的列印或手寫文字。 這適用于各種案例，例如記事、醫療記錄、安全性和銀行。 下列三節詳細說明三種不同的文字辨識 Api，分別針對不同的使用案例進行優化。

## <a name="read-api"></a>讀取 API

讀取 API 會使用我們最新的辨識模型來偵測影像中的文字內容，並將識別的文字轉換成電腦可讀取的字元資料流。 它最適合用於文字繁重的影像（例如已進行數位掃描的檔），以及適用于具有許多視覺雜訊的影像。 它會決定要針對每一行文字使用哪一個辨識模型，同時支援具有印刷和手寫文字的影像。 讀取 API 會以非同步方式執行，因為較大的檔可能需要幾分鐘的時間才會傳回結果。

讀取作業會維護其輸出中已辨識單字的原始行分組。 每一行都有周框方塊座標，而且線條內的每個單字也有自己的座標。 如果已辨識出具有低信心的單字，則也會傳達該資訊。 若要深入瞭解，請參閱[讀取 api 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb)檔和[閱讀 api 預覽參考](https://go.microsoft.com/fwlink/?linkid=2118322)檔。

> [!NOTE]
> 這項功能僅適用于英文和西班牙文（預覽）文字。

### <a name="image-requirements"></a>影像需求

讀取 API 適用于符合下列需求的影像：

- 影像必須以 JPEG、PNG、BMP、PDF 或 TIFF 格式呈現。
- 影像的維度必須介於 50 x 50 與 10000 x 10000 圖元之間。 PDF 頁面必須小於或等於 17 x 17 英寸。
- 映射的檔案大小必須小於 20 mb。

### <a name="limitations"></a>限制

如果您使用免費層的訂用帳戶，讀取 API 將只會處理 PDF 或 TIFF 檔的前兩個頁面。 使用付費訂用帳戶時，最多可處理200個頁面。 另請注意，API 會偵測到每個頁面最多300行。

## <a name="ocr-optical-character-recognition-api"></a>OCR （光學字元辨識） API

電腦視覺的光學字元辨識（OCR） API 類似于讀取 API，但它會以同步方式執行，而且不會針對大型檔進行優化。 它會使用較舊的辨識模型，但會使用更多語言;如需所支援語言的完整清單，請參閱[語言支援](language-support.md#text-recognition)。

若有必要，OCR 會以角度為單位傳回以水平影像座標軸為依據的旋轉位移，來修正已辨識文字的旋轉。 OCR 也會提供每個字的框架座標，如下圖所示。

![要旋轉的影像及其要讀取和描繪的文字](./Images/vision-overview-ocr.png)

若要深入瞭解，請參閱[OCR 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)檔。

### <a name="image-requirements"></a>影像需求

OCR API 適用于符合下列需求的影像：

* 影像必須以 JPEG、PNG、GIF 或 BMP 格式呈現。
* 輸入影像的大小必須介於 50 x 50 與 4200 x 4200 圖元之間。
* 影像中的文字能以 90 度的任何倍數進行旋轉，並可輔以不超過 40 度的小角度旋轉。

### <a name="limitations"></a>限制

在主要由文字構成的相片上，部分辨識的文字可能會造成誤判。 在某些相片（特別是沒有任何文字的相片）上，精確度可能會隨著影像類型而有所不同。

## <a name="recognize-text-api"></a>辨識文字 API

> [!NOTE]
> 辨識文字 API 會被取代為讀取 API。 讀取 API 具有類似的功能，並已更新以處理 PDF、TIFF 和多頁檔案。

辨識文字 API 與 OCR 類似，但它會以非同步方式執行，並使用更新的辨識模型。 若要深入瞭解，請參閱[辨識文字 API 參考](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200)檔。

### <a name="image-requirements"></a>影像需求

辨識文字 API 適用于符合下列需求的影像：

- 影像必須以 JPEG、PNG 或 BMP 格式呈現。
- 影像的維度必須介於 50 x 50 與 4200 x 4200 圖元之間。
- 映射的檔案大小必須小於 4 mb。

## <a name="limitations"></a>限制

文字辨識作業的精確度取決於影像的品質。 下列因素可能會導致不正確的讀取：

* 影像模糊。
* 手寫或草寫的文字。
* 藝術字型樣式。
* 文字太小。
* 複雜的背景、陰影、文字反光或透視失真。
* 文字開頭的大寫字母過大或遺失。
* 文字加上了下標、上標或刪除線。

## <a name="next-steps"></a>後續步驟

遵循 [[解壓縮文字（讀取）](./QuickStarts/CSharp-hand-text.md) ] 快速入門，在簡單C#的應用程式中執行文字辨識。
