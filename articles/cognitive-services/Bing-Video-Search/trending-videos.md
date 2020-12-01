---
title: 使用 Bing 影片搜尋 API 搜尋網路上的發燒影片
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Bing 影片搜尋 API 來搜尋網路上的發燒影片。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: cfeebcda201df592f6c396dcc780208a36d1e989
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353794"
---
# <a name="get-trending-videos-with-the-bing-video-search-api"></a>使用 Bing 影片搜尋 API 來取得發燒影片 

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照 [這裡](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

Bing 影片搜尋 API 可讓您跨網路尋找今天的發燒影片，也可根據不同類別來尋找。 

## <a name="get-request"></a>GET 要求

若要從 Bing 影片搜尋 API 取得今天的發燒影片，請傳送下列 GET 要求：  
  
```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="market-support"></a>市場支援

下列市場支援發燒影片。  
 
-   en-AU (英文，澳洲)  
-   en-CA (英文，加拿大)  
-   en-GB (英文，英國)  
-   en-ID (英文，印尼)  
-   en-IE (英文，愛爾蘭)  
-   en-IN (英文，印度)  
-   en-NZ (英文，紐西蘭)  
-   en-PH (英文，菲律賓)  
-   en-SG (英文，新加坡)  
-   en-US (英文，美國)  
-   en-WW (英文，全球彙總代碼)  
-   en-ZA (英文，南非)  
-   zh-CN (中文，中國)

## <a name="example-json-response"></a>範例 JSON 回應  

下列範例會顯示包含發燒影片的 API 回應，並依類別和子類別來列出。 回應也會包含橫幅影片，也就是最熱門的發燒影片，並可來自一或多個類別。  

```json
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [取得影片深入解析](video-insights.md)