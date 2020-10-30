---
title: 影片索引子場景、快照和主要畫面格
titleSuffix: Azure Media Services
description: 本主題概述影片索引子的幕後、快照和主要畫面。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: 248799d70e0741efcaea1714c12f4d92a42cef25
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041903"
---
# <a name="scenes-shots-and-keyframes"></a>場景、擷取畫面和主要畫面格

影片索引子支援根據結構和語義屬性，將影片分割成時態單位。 這項功能可讓客戶根據不同的資料細微性，輕鬆地流覽、管理及編輯其影片內容。 例如，根據本主題中所述的場景、照片和主要畫面格。   

![場景、擷取畫面和主要畫面格](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>場景偵測  
 
影片索引子會根據視覺提示，判斷影片中的場景變更時機。場景描述單一事件，它是由一系列與語義相關的連續快照所組成。 場景縮圖是其基礎快照的第一個主要畫面格。 影片索引子會根據連續圖的色彩連貫性來將影片分割成場景，並抓取每個場景的開始和結束時間。 場景偵測視為一項挑戰的工作，因為它牽涉到量化影片的語義部分。

> [!NOTE]
> 適用于至少包含3個場景的影片。

## <a name="shot-detection"></a>分鏡偵測

影片索引子會根據視覺提示來決定影片中的快照集變更，方法是在相鄰框架的色彩配置中追蹤突然和逐漸轉換。 此照片的中繼資料包含開始和結束時間，以及該課程中包含的主要畫面格清單。 這張照片是同時取自相同相機的連續框架。

## <a name="keyframe-detection"></a>關鍵畫面偵測

影片索引子會選取最能代表每一次的框架 (s) 。 主要畫面格是根據美觀屬性從整個影片中選取的代表性框架 (例如對比和 stableness) 。 影片索引子會根據哪些客戶可以將主要畫面格作為高解析度影像，來將主要畫面格識別碼的清單當作拍攝中繼資料的一部分來取出。  

### <a name="extracting-keyframes"></a>解壓縮主要畫面格

若要為您的影片解壓縮高解析度的主要畫面格，您必須先上傳影片並為其編制索引。

![關鍵 幀](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>使用影片索引子網站

若要使用影片索引子網站來解壓縮主要畫面格，請上傳影片並為其編制索引。 索引工作完成後，請按一下 [ **下載** ] 按鈕，然後選取 [成品] **(ZIP)** 。 這會將構件資料夾下載至您的電腦。 

![螢幕擷取畫面，顯示已選取 [成品] 的 [下載] 下拉式清單。](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
解壓縮並開啟資料夾。 在 [ *_KeyframeThumbnail* ] 資料夾中，您會發現所有從影片解壓縮的主要畫面格。 

#### <a name="with-the-video-indexer-api"></a>使用影片索引子 API

若要使用影片索引子 API 來取得主要畫面格，請使用 [上傳影片](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?) 通話上傳影片並為其編制索引。 完成編制索引作業之後，請呼叫 [取得影片索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)。 這會為您提供影片索引子從 JSON 檔案中的內容解壓縮的所有見解。  

您將會在每個照片的中繼資料中取得主要畫面格識別碼的清單。 

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

您現在將需要在「 [取得縮圖](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?) 」呼叫上執行每個主要畫面格識別碼。 這會將每個主要畫面格影像下載到您的電腦。 

## <a name="editorial-shot-type-detection"></a>編輯照片類型偵測

主要畫面格與輸出 JSON 中的快照相關聯。 

與見解 JSON 中的個別快照相關聯的照片類型代表其編輯類型。 當您將影片編輯成剪輯、結尾或搜尋特定的主要畫面格樣式以進行藝術時，可能會發現這些類型特性很有用。 不同的類型取決於每個快照的第一個主要畫面格的分析。 影像會以顯示在其第一個主要畫面格的臉部、大小和位置來識別。 

快照大小和小數位數是根據相機之間的距離，以及顯示在畫面中的臉部之間的距離來決定。 影片索引子會使用這些屬性來偵測下列的快照類型：

* 寬：顯示整個人員的主體。
* 中：顯示個人的上半體和臉部。
* 關閉：主要會顯示人員的臉部。
* 極端特寫：顯示人員填滿螢幕的臉部。 

您也可以根據框架中心的主旨字元位置來決定照片類型。 這個屬性會定義影片索引子中的下列快照類型：

* 左方臉部： person 出現在框架的左邊。
* 中心臉：人物出現在框架的中央區域。
* 右臉部： person 出現在框架的右側。
* 戶外：某位人出現在戶外設定中。
* 室內： person 出現在室內設定中。

其他特性：

* 兩張照片：顯示兩位人的中等尺寸臉部。
* 多個臉部：兩個以上的人員。


## <a name="next-steps"></a>後續步驟

[檢查 API 所產生的影片索引子輸出](video-indexer-output-json-v2.md#scenes)
