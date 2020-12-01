---
title: 調整大小和裁剪影像縮圖 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: Bing 搜尋 Api 的一些答案包括 Bing 所提供之縮圖影像的 Url，您可以調整大小和裁剪，而且可能包含查詢參數。
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350478"
---
# <a name="resize-and-crop-thumbnail-images"></a>調整大小和裁剪縮圖映像

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

Bing 搜尋 Api 的一些答案包括 Bing 所提供之縮圖影像的 Url，您可以調整大小和裁剪，而且可能包含查詢參數。 例如：

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

如果您顯示這些縮圖的子集，請提供選項來查看剩餘的影像。

> [!NOTE]
> 請確定裁剪和調整大小的縮圖影像將提供遵循協力廠商權利的搜尋案例，如 Bing 搜尋 API [使用和顯示需求](use-display-requirements.md)的要求。

## <a name="resize-a-thumbnail"></a>調整縮圖的大小 

若要調整縮圖大小，Bing 建議您只在 `w` `h` 縮圖的 URL 中，) 或 (高度) 查詢參數中指定一個 (寬度。 僅指定高度或寬度，可讓 Bing 維持影像的原始外觀。 指定寬度和高度 (以像素為單位)。 

例如，如果原始縮圖為480x620：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

而您想要減少其大小，請將 `w` 參數設定為新值 (例如 `336`) ），然後移除 `h`  參數：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

如果您只指定縮圖的高度或寬度，則會維持影像的原始外觀比例。 如果您同時指定這兩個參數，且不會維持外觀比例，Bing 會將白色填補新增至影像的框線。

例如，如果您將480x359 影像的大小調整為200x200 而不進行裁剪，則完整寬度會包含影像，但高度會在影像的頂端和底部包含25圖元的白色填補。 如果影像是359x480，則左邊和右邊框線會包含白色填補。 如果您裁剪影像，則不會新增填白。  

下圖顯示縮圖影像的原始大小 (480x300)。  
  
![原始橫向影像](./media/resize-crop/bing-resize-crop-landscape.png)  
  
下圖顯示大小調整為 200x200 的影像。 會維持外觀比例，並以白色填補頂端和下框線 (此處包含的黑色框線會顯示填補) 。  
  
![已調整大小的橫向映像](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

如果您指定的維度大於影像的原始寬度和高度，Bing 會在左邊和上框線新增白色填補。  

## <a name="request-different-thumbnail-sizes"></a>要求不同的縮圖大小

若要要求不同的縮圖影像大小，請從縮圖的 URL 中移除所有查詢參數，但 `id` 和 `pid` 參數除外。 然後以 `&w` 圖元為單位，新增 (寬度) 或 `&h` (高度) 查詢參數，但不是兩者都有。 Bing 將維持影像的原始外觀比例。 

若要將上述 URL 所指定之影像的寬度增加到165圖元，請使用下列 URL：

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

如果您要求的影像大於影像的原始大小，Bing 會視需要在影像周圍新增白色填補。 例如，如果影像的原始大小是474x316，而您設 `&w` 為500，Bing 將會傳回500x333 影像。 此影像在上邊緣和下邊緣會有8.5 圖元的白色填補，以及左邊緣和右邊的13圖元填補。

若要防止 Bing 在要求的大小大於影像的原始大小時新增白色填補，請將 `&p` 查詢參數設定為0。 例如，如果您 `&p=0` 在上述 URL 中包含參數，Bing 將會傳回474x316 影像，而不是500x333 影像：

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

如果您同時指定 `&w` 和 `&h` 查詢參數，Bing 將會維持影像的外觀比例，並視需要新增白色填補。 例如，如果影像的原始大小為474x316，而您將寬度和高度參數設定為 200x200 (`&w=200&h=200`) ，Bing 會傳回影像，其頂端和底部包含33圖元的白色填補。 如果您包含 `&p` 查詢參數，Bing 會傳回200x134 影像。

## <a name="crop-a-thumbnail"></a>裁剪縮圖 

若要裁剪影像，請包含 `c` (裁剪) 查詢參數。 您可以使用下列值：
  
- `4`&mdash;盲孔比例  
- `7`&mdash;智慧型比例  

### <a name="smart-ratio-cropping"></a>智慧型比例裁剪

如果您要求智慧比例裁剪 (藉由將 `c` 參數設定為 [ `7`) ]，Bing 將會從感興趣區域的中央裁剪影像，同時維持影像的外觀比例。 感興趣區域是 Bing 判斷的影像區域，其中包含大部分的匯入組件。 下圖顯示感興趣區域範例。  
  
![感興趣區域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

如果您調整影像大小並要求智慧比例裁剪，Bing 會將影像縮減為要求的大小，同時維持外觀比例。 Bing 接著會根據調整大小的維度來裁剪影像。 例如，如果調整大小的寬度小於或等於高度，Bing 會將影像裁剪到感興趣區域中央的左邊和右邊。 否則，Bing 會將其裁剪到感興趣區域中央的頂端和底部。  
  
 
下圖顯示使用智慧比例裁剪縮減為 200x200 的影像。 因為 Bing 從左上角測量影像，所以會裁剪影像的下半部。 
  
![裁剪為 200x200 的橫向影像](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
下圖顯示使用智慧比例裁剪縮減為 200x100 的影像。 因為 Bing 從左上角測量影像，所以會裁剪影像的下半部。 
   
![裁剪為 200x100 的橫向影像](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
下圖顯示使用智慧比例裁剪縮減為 100x200 的影像。 因為 Bing 從中央測量影像，所以會裁剪影像的左邊和右邊部分。
  
![裁剪為 100x200 的橫向影像](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

如果 Bing 無法判斷出影像的感興趣區域，服務將會使用盲比的裁剪。  

### <a name="blind-ratio-cropping"></a>盲人比例裁剪

如果您藉由將參數設定為) ，來要求 (的密件比例裁剪 `c` `4` ，Bing 會使用下列規則來裁剪影像。  
  
- 如果為 `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` ，則會從左上角測量影像，並在底部裁剪影像。  
- 如果為 `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)` ，則會從中央測量影像，並裁剪至左方和右邊。  

下圖顯示 225x300 的直向映像。  
  
![原始向日葵影像](./media/resize-crop/bing-resize-crop-sunflower.png)
  
下圖顯示使用盲目比例裁剪縮減為 200x200 的影像。 影像會從左上角測量，導致影像的下半部遭到裁剪。  
  
![裁剪為 200x200 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
下圖顯示使用盲目比例裁剪縮減為 200x100 的影像。 影像會從左上角測量，導致影像的下半部遭到裁剪。  
  
![裁剪為 200x100 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
下圖顯示使用盲目比例裁剪縮減為 100x200 的影像。 從中央度量影像會導致影像的左右兩邊被裁切。  
  
![裁剪為 100x200 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>後續步驟

* [Bing 搜尋 API 是什麼？](bing-api-comparison.md)
* [Bing 搜尋 API 使用和顯示需求](use-display-requirements.md)