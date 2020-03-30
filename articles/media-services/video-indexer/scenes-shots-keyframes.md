---
title: 視頻索引子場景、鏡頭和關鍵幀
titleSuffix: Azure Media Services
description: 本主題概述了視頻索引子場景、鏡頭和關鍵幀。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: a833fd808049cfce95b182910e50e38d3c39f4e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245937"
---
# <a name="scenes-shots-and-keyframes"></a>場景、擷取畫面和主要畫面格

視頻索引子支援根據結構和語義屬性將視頻分割到時態單位。 此功能使客戶能夠根據不同的細微性輕鬆流覽、管理和編輯其視頻內容。 例如，基於本主題仲介紹的場景、鏡頭和關鍵幀。   

![場景、擷取畫面和主要畫面格](./media/scenes-shots-keyframes/scenes-shots-keyframes.png)
 
## <a name="scene-detection"></a>場景檢測  
 
視頻索引子根據視覺提示確定視頻中場景何時更改。場景描述單個事件，由一系列連續鏡頭組成，這些鏡頭在語義上是相關的。 場景縮略圖是其基礎拍攝的第一個關鍵幀。 視頻索引子根據連續鏡頭的顏色一致性將視頻分割成場景，並檢索每個場景的開始和結束時間。 場景檢測被認為是一項具有挑戰性的任務，因為它涉及量化視頻的語義方面。

> [!NOTE]
> 適用于包含至少 3 個場景的視頻。

## <a name="shot-detection"></a>分鏡偵測

視頻索引子通過跟蹤相鄰幀的色彩配置中的突然和漸進過渡，根據視覺提示確定視頻拍攝何時發生更改。 拍攝的中繼資料包括開始和結束時間，以及該拍攝中包含的關鍵幀清單。 拍攝是同時從同一攝像機拍攝的連續幀。

## <a name="keyframe-detection"></a>關鍵幀檢測

視頻索引子選擇最能代表每個鏡頭的幀。 關鍵幀是根據美觀特性（例如，對比度和穩定性）從整個視頻中選擇的代表幀。 視頻索引子檢索關鍵幀 I 的清單，作為拍攝中繼資料的一部分，客戶可以基於該清單提取關鍵幀作為高解析度圖像。  

### <a name="extracting-keyframes"></a>提取關鍵幀

要提取視頻的高解析度關鍵幀，必須首先上載和索引視頻。

![關鍵 幀](./media/scenes-shots-keyframes/extracting-keyframes.png)

#### <a name="with-the-video-indexer-website"></a>使用視頻索引子網站

要使用視頻索引子網站提取關鍵幀，請上傳和索引視頻。 索引作業完成後，按一下 **"下載**"按鈕並選擇 **"專案 "（ZIP）。** 這將將工件資料夾下載到您的電腦。 

![關鍵 幀](./media/scenes-shots-keyframes/extracting-keyframes2.png)
 
解壓縮並打開資料夾。 在 *_KeyframeThumbnail*資料夾中，您將找到從視頻中提取的所有關鍵幀。 

#### <a name="with-the-video-indexer-api"></a>使用視頻索引子 API

要使用視頻索引子 API 獲取關鍵幀，請使用[上傳視頻](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?)呼叫上傳和索引視頻。 索引作業完成後，調用[獲取視頻索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)。 這將為您提供視頻索引子從 JSON 檔中的內容中提取的所有見解。  

您將獲得關鍵幀 I 的清單，作為每個鏡頭中繼資料的一部分。 

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

現在，您需要在[獲取縮略圖](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Thumbnail?)調用上運行每個關鍵幀的 ID。 這將將每個關鍵幀圖像下載到您的電腦。 

## <a name="editorial-shot-type-detection"></a>編輯拍攝類型檢測

關鍵幀與輸出 JSON 中的鏡頭相關聯。 

與見解 JSON 中的單個拍攝關聯的拍攝類型表示其編輯類型。 在將視頻編輯成剪輯、預告片或為藝術目的搜索特定類型的關鍵幀時，您可能會發現這些拍攝類型特徵很有用。 根據對每個拍攝的第一個關鍵幀的分析確定不同類型的類型。 鏡頭由第一個關鍵幀中顯示的人臉的比例、大小和位置標識。 

拍攝大小和比例根據攝像機與幀中顯示的人臉之間的距離來確定。 使用這些屬性，視頻索引子檢測以下拍攝類型：

* 寬：顯示整個人的身體。
* 中等：顯示一個人的上半身和麵部。
* 特寫：主要顯示一個人的臉。
* 極端特寫：顯示一個人的臉填充螢幕。 

拍攝類型還可以根據主題字元相對於框架中心的位置來確定。 此屬性在視頻索引子中定義以下拍攝類型：

* 左面：一個人出現在框架的左側。
* 中心面：一個人出現在框架的中心區域。
* 右面：一個人出現在框架的右側。
* 戶外：一個人出現在戶外環境中。
* 室內：一個人出現在室內環境中。

其他特徵：

* 兩張照片：顯示兩張中等大小的人臉。
* 多張面孔：兩個以上。


## <a name="next-steps"></a>後續步驟

[檢查 API 生成的視頻索引子輸出](video-indexer-output-json-v2.md#scenes)
