---
title: 調整大小和裁剪影像縮圖 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: Bing 搜尋 Api 的一些答案包含 Bing 提供的縮圖影像 Url，您可以調整大小和裁剪，而且可能包含查詢參數。
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "74110632"
---
# <a name="resize-and-crop-thumbnail-images"></a>調整大小和裁剪縮圖映像

Bing 搜尋 Api 的一些答案包含 Bing 提供的縮圖影像 Url，您可以調整大小和裁剪，而且可能包含查詢參數。 例如：

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

如果您顯示這些縮圖的子集，請提供選項來查看其餘影像。

> [!NOTE]
> 請務必根據 Bing 搜尋 API[使用和顯示需求](use-display-requirements.md)的要求，裁剪並調整縮圖影像，以提供符合協力廠商權利的搜尋案例。

## <a name="resize-a-thumbnail"></a>調整縮圖大小 

若要調整縮圖的大小，Bing 建議您在縮`w`圖的 URL 中`h`只指定一個（寬度）或（高度）查詢參數。 僅指定高度或寬度，讓 Bing 維持影像的原始外觀。 指定寬度和高度 (以像素為單位)。 

例如，如果原始的縮圖為480x620：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

而且您想要減少其大小，請將`w`參數設定為新的值（例如`336`），並移除`h`參數：

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

如果您只指定縮圖的高度或寬度，則會維護影像的原始外觀比例。 如果您同時指定這兩個參數，而且不會維護外觀比例，Bing 會在影像的框線加上白色填補。

例如，如果您將480x359 影像的大小調整為200x200 而不進行裁剪，則完整寬度會包含影像，但高度會包含影像頂端和底部的25圖元白色填補。 如果影像是359x480，左邊和右邊的框線會包含白色填補。 如果您裁剪影像，則不會新增填白。  

下圖顯示縮圖影像的原始大小 (480x300)。  
  
![原始橫向影像](./media/resize-crop/bing-resize-crop-landscape.png)  
  
下圖顯示大小調整為 200x200 的影像。 長寬比會維持不變，而上邊界和下框線會以白色填補（此處包含黑色框線以顯示填補）。  
  
![已調整大小的橫向映像](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

如果您指定的維度大於影像的原始寬度和高度，Bing 會在左邊和上框線加上白色填補。  

## <a name="request-different-thumbnail-sizes"></a>要求不同的縮圖大小

若要要求不同的縮圖影像大小，請移除縮圖 URL 中的`id`所有查詢參數， `pid`但和參數除外。 然後，使用所`&w`需的影像大小`&h` （以圖元為單位）新增（width）或（height）查詢參數，但不能同時加入兩者。 Bing 會維持影像的原始外觀比例。 

若要將上述 URL 所指定影像的寬度增加為165圖元，您可以使用下列 URL：

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

如果您要求的影像大於影像的原始大小，Bing 會視需要在影像周圍新增白色填補。 例如，如果影像的原始大小是474x316，而您將設定`&w`為500，則 Bing 會傳回500x333 影像。 此影像在上和下邊緣會有8.5 圖元的白色填補，而左邊和右邊緣的填補則為13圖元。

若要防止 Bing 在所要求的大小大於影像的原始大小時加入白色填補，請將`&p`查詢參數設定為0。 例如，如果您在上述 URL `&p=0`中包含參數，Bing 會傳回474x316 的影像，而不是500x333 的影像：

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

如果您同時`&w`指定和`&h`查詢參數，Bing 會維持影像的外觀比例，並視需要新增白色填補。 例如，如果影像的原始大小是474x316，而且您將 width 和 height 參數設定為200x200 （`&w=200&h=200`），Bing 會傳回一個影像，其中包含頂端和底部的33圖元的白色填補。 如果您包含`&p`查詢參數，Bing 會傳回200x134 影像。

## <a name="crop-a-thumbnail"></a>裁剪縮圖 

若要裁剪影像，請包含`c` （裁剪）查詢參數。 您可以使用下列值：
  
- `4`&mdash;盲比例  
- `7`&mdash;智慧比率  

### <a name="smart-ratio-cropping"></a>智慧比率裁剪

如果您要求智慧比率裁剪（藉由將`c`參數設定`7`為），Bing 會從其感向外區域的中央裁剪影像，同時維持影像的外觀比例。 感興趣區域是 Bing 判斷的影像區域，其中包含大部分的匯入組件。 下圖顯示感興趣區域範例。  
  
![感興趣區域](./media/resize-crop/bing-resize-crop-regionofinterest.png)

如果您調整影像大小並要求智慧比例裁剪，Bing 會將影像縮小為所要求的大小，同時維持外觀比例。 然後，Bing 會根據調整大小的維度來裁剪影像。 例如，如果調整大小的寬度小於或等於高度，Bing 會將影像裁剪到感的區域中心的左邊和右邊。 否則，Bing 會將它裁剪到感的區域中心的頂端和底部。  
  
 
下圖顯示使用智慧比例裁剪縮減為 200x200 的影像。 因為 Bing 會從左上角測量影像，所以會裁剪影像的下半部。 
  
![裁剪為 200x200 的橫向影像](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
下圖顯示使用智慧比例裁剪縮減為 200x100 的影像。 因為 Bing 會從左上角測量影像，所以會裁剪影像的下半部。 
   
![裁剪為 200x100 的橫向影像](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
下圖顯示使用智慧比例裁剪縮減為 100x200 的影像。 因為 Bing 會從中央測量影像，所以會裁剪影像的左右部分。
  
![裁剪為 100x200 的橫向影像](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

如果 Bing 無法判斷影像的相關區域，服務就會使用盲比例裁剪。  

### <a name="blind-ratio-cropping"></a>盲人比例裁剪

如果您要求盲比例裁剪（藉由將`c`參數設定`4`為），Bing 會使用下列規則來裁剪影像。  
  
- 如果`(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)`為，則會從左上角測量影像，並在底部裁剪。  
- 如果`(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`為，則會從中央測量影像，並裁剪至左邊和右邊。  

下圖顯示 225x300 的直向映像。  
  
![原始向日葵影像](./media/resize-crop/bing-resize-crop-sunflower.png)
  
下圖顯示使用盲目比例裁剪縮減為 200x200 的影像。 影像會從左上角測量，導致影像的下半部被裁剪。  
  
![裁剪為 200x200 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
下圖顯示使用盲目比例裁剪縮減為 200x100 的影像。 影像會從左上角測量，導致影像的下半部被裁剪。  
  
![裁剪為 200x100 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
下圖顯示使用盲目比例裁剪縮減為 100x200 的影像。 從中央度量影像會導致影像的左右兩邊被裁切。  
  
![裁剪為 100x200 的向日葵影像](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>後續步驟

* [Bing 搜尋 API 是什麼？](bing-api-comparison.md)
* [Bing 搜尋 API 使用和顯示需求](use-display-requirements.md)
