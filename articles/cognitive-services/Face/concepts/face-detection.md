---
title: 臉部偵測和屬性概念
titleSuffix: Azure Cognitive Services
description: 臉部偵測是在影像中尋找人臉，並選擇性地傳回不同種類的臉部相關資料的動作。
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 15e39eb9f5b8dd3556ea9ff8240bc2c9d252cd31
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73743063"
---
# <a name="face-detection-and-attributes"></a>臉部偵測和屬性

本文說明臉部偵測和臉部屬性資料的概念。 臉部偵測是在影像中尋找人臉，並選擇性地傳回不同種類的臉部相關資料的動作。

您可以使用[臉部-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)偵測作業來偵測影像中的臉部。 每個偵測到的臉部至少會對應到回應中的 faceRectangle 欄位。 左側、頂端、寬度和高度的這組圖元座標會標示所在的臉部。 您可以使用這些座標來取得臉部的位置及其大小。 在 API 回應中，臉部會以最大到最小的大小順序列出。

## <a name="face-id"></a>臉部識別碼

臉部識別碼是影像中每個偵測到之臉部的唯一識別碼字串。 您可以在[臉部-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)偵測 API 呼叫中要求臉部識別碼。

## <a name="face-landmarks"></a>臉部特徵點

臉部地標是一組表面上容易尋找的點，例如鼻子的瞳孔或刀尖。 預設會有 27 個預先定義的臉部特徵點。 下圖顯示所有27點：

![已標記所有27個地標的臉部圖表](../Images/landmarks.1.jpg)

點的座標會以圖元為單位傳回。

## <a name="attributes"></a>屬性

屬性是一組功能，可選擇性地由[臉部-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)偵測 API 偵測出來。 可以偵測到下列屬性：

* **年齡**。 特定臉部的預估年齡（以年為單位）。
* **模糊**。 影像中臉部的 blurriness。 這個屬性會傳回介於0和1之間的值，以及低、中或高的非正式評等。
* **表情**。 表情的清單及其對指定臉部的偵測信心。 信賴分數會正規化，而所有表情上的分數會加上最多一個。 傳回的表情包括快樂、悲傷、中性、生氣、藐視、厭惡、驚訝和恐懼。
* **公開**。 影像中臉部的曝光。 這個屬性會傳回介於0和1之間的值，以及 underExposure、goodExposure 或 overExposure 的非正式評等。
* 臉部的**表情**。 預估的臉部線目前狀態，以及指定臉部的長度。
* **性別**。 指定臉部的預估性別。 可能的值為男性、女性和 genderless。
* **眼鏡**。 指定的臉部是否有眼鏡。 可能的值為 NoGlasses、ReadingGlasses、太陽眼鏡和 Swimming Goggles。
* **頭髮**。 臉部的頭髮類型。 這個屬性會顯示是否可以看見頭髮、是否偵測到 baldness，以及要偵測哪些顏色線。
* **頭部姿勢**。 3D 空間中的臉部方向。 此屬性是以度為單位的音調、翻轉和偏擺角度來描述。 值範圍分別為-90 度到90度、-180 度到180度，以及-90 度到90度。 如需角度對應，請參閱下圖：

    ![具有音調、翻轉和偏擺軸的標頭](../Images/headpose.1.jpg)
* **構成**。 臉部是否有構成。 這個屬性會傳回 eyeMakeup 和 lipMakeup 的布林值。
* **雜訊**。 臉部影像中偵測到的視覺雜訊。 這個屬性會傳回介於0和1之間的值，以及低、中或高的非正式評等。
* **遮蔽**。 是否有物件封鎖臉部的部分。 這個屬性會傳回 eyeOccluded、foreheadOccluded 和 mouthOccluded 的布林值。
* **笑臉**。 指定臉部的笑臉運算式。 這個值介於零之間，沒有笑臉，另一個用於明確笑臉。

> [!IMPORTANT]
> 臉部屬性會透過使用統計演算法來預測。 它們可能不一定是正確的。 當您根據屬性資料進行決策時，請務必小心。

## <a name="input-data"></a>輸入資料

請使用下列秘訣來確保您的輸入影像能提供最精確的偵測結果：

* 支援的輸入影像格式包括 JPEG、PNG、第一個畫面格的 GIF 和 BMP。
* 影像檔案大小不應超過 4 MB。
* 可偵測的臉部大小範圍為 36 x 36 至 4096 x 4096 像素。 不會偵測到此範圍之外的臉部。
* 某些臉部可能因技術挑戰而無法偵測出來。 極端臉部角度（頭部姿勢）或臉部遮蔽（像是太陽眼鏡或手中封鎖部分表面的物件）可能會影響偵測。 正面和近正面的臉部提供最佳的結果。

如果您要偵測影片摘要中的人臉，可以藉由調整攝影機上的特定設定來改善效能：

* **平滑**：許多視頻相機會套用凹凸貼圖。 您應該關閉此功能，因為它會在畫面格之間建立模糊，並減少清楚的效果。
* **快門速度**：更快的快門速度可減少框架之間的動作量，並使每個畫面格更清楚。 我們建議以1/60 秒或更快的快門速度來進行。
* **快門角度**：某些相機指定快門角度，而不是快門速度。 如果可能，您應該使用較低的快門角度。 這會產生更清楚的影片畫面。

    >[!NOTE]
    > 具有較低快門角度的相機將會在每個畫面格中收到較少的光線，因此影像會變暗。 您必須決定要使用的正確層級。

## <a name="next-steps"></a>後續步驟

既然您已經熟悉臉部偵測概念，請瞭解如何撰寫腳本來偵測指定影像中的臉部。

* [偵測影像中的臉部](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)