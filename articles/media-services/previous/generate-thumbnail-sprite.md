---
title: 使用 Azure 媒體服務產生縮圖原件 | Microsoft Docs
description: 本主題說明如何使用 Azure 媒體服務產生縮圖原件。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "61229045"
---
# <a name="generate-a-thumbnail-sprite"></a>產生縮圖原件  

您可以使用媒體編碼器標準產生縮圖原件，其中包含以多個低解析度縮圖結合成單一 (大型) 影像的 JPEG 檔案，和一個 VTT 檔案。 這個 VTT 檔案會在每個縮圖所代表的輸入影像中指定時間範圍，以及該縮圖在大型 JPEG 檔案中的大小和座標。 影片播放程式會使用 VTT 檔案和原件影像顯示「視覺化」搜尋列，讓檢視者在使用影片時間軸向前或向後刪除時獲得視覺化回饋。

若要使用媒體編碼器標準產生縮圖原件，預設值：

1. 必須使用 JPG 縮圖影像格式
2. 必須將起始/間距/範圍值指定為時間戳記或 % 值 (而非畫面格計數) 
    
    1. 時間戳記和 % 值可以混用

3. 必須有 SpriteColumn 值，且此值必須是大於或等於 1 的非負數

    1. 如果 SpriteColumn 設定為 M >= 1，輸出影像將是含有 M 個資料行的矩形。 如果透過 #2 產生的縮圖數目不是 M 的倍數，最後一個資料列就會不完整，並顯示為黑色像素。  

範例如下：

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
    }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
   ]
}
```

## <a name="known-issues"></a>已知問題

1.  無法以單一資料列的影像產生原件影像 (SpriteColumn = 1 會產生含有單一資料行的影像)。
2.  尚不支援將原件影像區塊化為一般大小的 JPEG 影像。 因此，務必要謹慎限制縮圖的數目和大小，使結合後的縮圖原件保持在 8M 個像素左右或以內。
3.  Azure 媒體播放器支援 Microsoft Edge、Chrome 和 Firefox 瀏覽器的原件。 在 IE11 中不支援 VTT 剖析。

## <a name="next-steps"></a>後續步驟

[編碼內容](media-services-encode-asset.md)
