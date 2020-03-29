---
title: 調整大小和裁剪影像縮圖 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: 必應搜索 API 中的一些答案包括必應提供的縮略圖圖像的 URL，您可以調整大小並裁剪這些縮略圖，並可能包含查詢參數。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: 630b86f55a537d109c851cb585cfccc34d229f83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110632"
---
# <a name="resize-and-crop-thumbnail-images"></a>調整大小和裁剪縮圖映像

必應搜索 API 中的一些答案包括必應提供的縮略圖圖像的 URL，您可以調整大小並裁剪這些縮略圖，並可能包含查詢參數。 例如：

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

如果顯示這些縮略圖的子集，請提供查看其餘圖像的選項。

> [!NOTE]
> 確保裁剪和調整縮略圖圖像大小將提供一個搜索方案，尊重協力廠商許可權，這是必應搜索 API[使用和顯示要求的要求](use-display-requirements.md)。

## <a name="resize-a-thumbnail"></a>調整縮略圖的大小 

要調整縮略圖的大小，必應建議您只指定縮`w`略圖 URL`h`中的一個（寬度）或（高度）查詢參數。 僅指定高度或寬度，Bing 可以維護圖像的原始方面。 指定寬度和高度 (以像素為單位)。 

例如，如果原始縮略圖為 480x620：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

您希望減小其大小，將`w`參數設置為新值（例如`336`），並刪除參數： `h`

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

如果僅指定縮略圖的高度或寬度，則將保留圖像的原始縱橫比。 如果同時指定兩個參數，並且縱橫比未保持，必應將添加白色填充到圖像的邊框。

例如，如果將 480x359 圖像的大小調整到 200x200 而不進行裁剪，則全寬將包含圖像，但高度將在圖像的頂部和底部包含 25 圖元的白色填充。 如果圖像為 359x480，則左右邊框將包含白色填充。 如果您裁剪影像，則不會新增填白。  

下圖顯示縮圖影像的原始大小 (480x300)。  
  
![原始橫向影像](./media/resize-crop/bing-resize-crop-landscape.png)  
  
下圖顯示大小調整為 200x200 的影像。 保持縱橫比，頂部和底部邊框用白色填充（此處的黑色邊框包括顯示填充）。  
  
![已調整大小的橫向映像](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

如果指定的尺寸大於圖像的原始寬度和高度，必應將添加白色填充到左側和頂部邊框。  

## <a name="request-different-thumbnail-sizes"></a>請求不同的縮略圖大小

要請求不同的縮略圖圖像大小，請從縮略圖的 URL 中刪除所有查詢參數`id`，`pid`除了 和 參數。 然後添加`&w`（寬度）或`&h`（高度）查詢參數，所需圖像大小以圖元為單位，但不能同時添加兩者。 必應將保持圖像的原始縱橫比。 

要將上述 URL 指定的圖像寬度增加到 165 圖元，請使用以下 URL：

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

如果請求的圖像大於圖像的原始大小，必應根據需要在圖像周圍添加白色填充。 例如，如果圖像的原始大小為 474x316，而您設置為`&w`500，必應將返回 500x33 圖像。 此圖像沿上邊緣和下邊緣將具有 8.5 圖元的白色填充，在左右邊緣有 13 圖元的填充。

為防止必應添加白色填充（如果請求的大小大於圖像的原始大小），請將`&p`查詢參數設置為 0。 例如，如果在上述 URL`&p=0`中包含參數，必應將返回 474x316 圖像，而不是 500x33 圖像：

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

如果同時指定和`&w``&h`查詢參數，必應將維護圖像的縱橫比並根據需要添加白色填充。 例如，如果圖像的原始大小為 474x316，並且將寬度和高度參數設置為 200x200 （），`&w=200&h=200`必應返回頂部和底部包含 33 圖元白色填充的圖像。 如果包含`&p`查詢參數，必應將返回 200x134 圖像。

## <a name="crop-a-thumbnail"></a>裁剪縮略圖 

要裁剪圖像，`c`請包括（裁剪）查詢參數。 您可以使用下列值：
  
- `4`&mdash;盲比  
- `7`&mdash;智慧比  

### <a name="smart-ratio-cropping"></a>智慧比率裁剪

如果請求智慧比率裁剪（通過將`c`參數設置為`7`），必應將從其感興趣區域的中心向外裁剪圖像，同時保持圖像的縱橫比。 感興趣區域是 Bing 判斷的影像區域，其中包含大部分的匯入組件。 下圖顯示感興趣區域範例。  
  
![感興趣區域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

如果調整圖像大小並請求智慧比率裁剪，必應將圖像減小到請求的大小，同時保持縱橫比。 然後，必應根據調整大小的尺寸裁剪圖像。 例如，如果調整大小的寬度小於或等於高度，必應將圖像裁剪到感興趣區域中心的左側和右側。 否則，必應將裁剪到感興趣區域中心的頂部和底部。  
  
 
下圖顯示使用智慧比例裁剪縮減為 200x200 的影像。 由於必應從左上角測量圖像，因此將裁剪圖像的底部。 
  
![裁剪為 200x200 的橫向影像](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
下圖顯示使用智慧比例裁剪縮減為 200x100 的影像。 由於必應從左上角測量圖像，因此將裁剪圖像的底部。 
   
![裁剪為 200x100 的橫向影像](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
下圖顯示使用智慧比例裁剪縮減為 100x200 的影像。 由於必應從中心測量圖像，因此將裁剪圖像的左側和右側部分。
  
![裁剪為 100x200 的橫向影像](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

如果必應無法確定圖像的感興趣區域，則服務將使用盲比率裁剪。  

### <a name="blind-ratio-cropping"></a>盲比裁剪

如果請求盲比率裁剪（通過將`c`參數設置為`4`），必應使用以下規則裁剪圖像。  
  
- 如果`(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`從左上角測量圖像，並在底部裁剪。  
- 如果`(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`從中心測量圖像，然後裁剪到左側和右側。  

下圖顯示 225x300 的直向映像。  
  
![原始向日葵影像](./media/resize-crop/bing-resize-crop-sunflower.png)
  
下圖顯示使用盲目比例裁剪縮減為 200x200 的影像。 從左上角測量圖像，導致裁剪圖像的底部。  
  
![裁剪為 200x200 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
下圖顯示使用盲目比例裁剪縮減為 200x100 的影像。 從左上角測量圖像，導致裁剪圖像的底部。  
  
![裁剪為 200x100 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
下圖顯示使用盲目比例裁剪縮減為 100x200 的影像。 從中央度量影像會導致影像的左右兩邊被裁切。  
  
![裁剪為 100x200 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>後續步驟

* [Bing 搜尋 API 是什麼？](bing-api-comparison.md)
* [Bing 搜尋 API 使用和顯示需求](use-display-requirements.md)
