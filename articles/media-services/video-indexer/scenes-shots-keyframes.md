---
title: 影片索引子場景、螢幕擷取畫面和主要畫面格
titleSuffix: Azure Media Services
description: 本主題概述影片索引子的場景、快照和主要畫面格。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78363922"
---
# <a name="scenes-shots-and-keyframes"></a>場景、擷取畫面和主要畫面格

影片索引子支援根據結構化和語義屬性，將影片分割成時態單位。 這項功能可讓客戶根據不同的資料細微性，輕鬆地流覽、管理及編輯其影片內容。 例如，根據本主題中所述的場景、快照和主要畫面格。   

![場景、擷取畫面和主要畫面格](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>場景偵測  
 
影片索引子會根據視覺提示判斷影片中的場景變更時機。場景會描述單一事件，而且它是由一系列與語義相關的連續快照所組成。 場景縮圖是其基礎快照的第一個主要畫面格。 影片索引子會根據連續拍照的色彩連貫性，將影片分割成場景，並抓取每個場景的開始和結束時間。 場景偵測被視為一項挑戰的工作，因為它牽涉到量化影片的語義層面。

> [!NOTE]
> 適用于至少包含3個場景的影片。

## <a name="shot-detection"></a>分鏡偵測

影片索引子會根據視覺提示來決定影片中的快照集，方法是追蹤相鄰框架的色彩配置中的突然和漸進轉換。 這個快照的中繼資料包括開始和結束時間，以及該課程中所包含的主要畫面格清單。 此相片是一次從相同相機拍攝的連續畫面格。

## <a name="keyframe-detection"></a>主要畫面格偵測

影片索引子會選取最能代表每個快照的畫面。 主要畫面格是根據美觀屬性（例如，對比和 stableness）從整段影片中選取的代表性框架。 影片索引子會在拍照的中繼資料中抓取主要畫面格識別碼清單，這是根據哪些客戶可以將主要畫面格視為高解析度影像。  

### <a name="extracting-keyframes"></a>解壓縮主要畫面格

若要為您的影片解壓縮高解析度的主要畫面格，您必須先上傳影片並為其編制索引。

![幀](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>使用影片索引子網站

若要使用影片索引子網站來解壓縮主要畫面格，請上傳影片並為其編制索引。 索引作業完成後，請按一下 [**下載**] 按鈕，然後選取 [成品 **（ZIP）** ]。 這會將 [構件] 資料夾下載到您的電腦。 

![幀](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
解壓縮並開啟資料夾。 在 [ *_KeyframeThumbnail* ] 資料夾中，您會找到所有從影片解壓縮的主要畫面格。 

#### <a name="with-the-video-indexer-api"></a>使用影片索引子 API

若要使用影片索引子 API 來取得主要畫面格，請使用[上傳影片](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?)呼叫來上傳並編制您的影片索引。 索引作業完成後，請呼叫[取得影片索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)。 這會提供影片索引子從 JSON 檔案中的內容解壓縮的所有深入解析。  

您會在每個拍照的中繼資料中取得主要畫面格識別碼的清單。 

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

您現在必須在「[取得縮圖](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?)」呼叫上執行每個主要畫面格識別碼。 這會將每個主要畫面格影像下載至您的電腦。 

## <a name="editorial-shot-type-detection"></a>編輯快照類型偵測

主要畫面格會與輸出 JSON 中的拍照相關聯。 

與深入解析 JSON 中的個別快照相關聯的快照類型代表其編輯類型。 當您將影片編輯成剪輯、結尾，或搜尋特定樣式的主要畫面格以進行藝術時，您可能會發現這些「影像類型」特性很有用。 不同的類型是根據每個快照的第一個主要畫面格的分析來決定。 照片是以其第一個主要畫面格中顯示之臉部的尺規、大小和位置來識別。 

相片大小和尺規的決定，是根據相機和出現在畫面格中的臉部之間的距離而定。 影片索引子會使用這些屬性來偵測下列快照類型：

* 寬：顯示整個人員的主體。
* 中：顯示個人的上方主體和臉部。
* 關閉：主要會顯示個人的臉部。
* 極端的特寫：顯示個人用來填滿螢幕的臉部。 

您也可以根據與框架中心相關的主旨字元位置來決定 [拍攝類型]。 此屬性會定義影片索引子中的下列快照類型：

* 左方臉部：人員會出現在框架的左邊。
* 中心臉部：人物出現在框架的中央區域。
* 右側臉部：人員會出現在畫面的右側。
* 室外：某位人員出現在戶外設定中。
* 室內：某位人員出現在室內設定中。

其他特性：

* 兩個拍照：顯示兩個人的中型大小臉部。
* 多個臉部：兩個以上的人員。


## <a name="next-steps"></a>後續步驟

[檢查 API 所產生的影片索引子輸出](video-indexer-output-json-v2.md#scenes)
