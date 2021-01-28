---
title: 使用媒體編碼器標準 REST 產生縮圖
description: 本文說明如何使用 REST 來編碼資產，並同時使用媒體編碼器標準產生縮圖。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f4456a65f422c23da73f36dd74680fbb598db186
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955829"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>如何使用編碼器標準搭配 REST 來產生縮圖

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

您可以使用媒體編碼器標準，以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 影像檔案格式，從您的輸入視訊中產生一或多個縮圖。

## <a name="recommended-reading-and-practice"></a>建議閱讀和練習

建議您先閱讀 [如何使用自訂轉換進行編碼-REST](custom-preset-rest-howto.md)，以熟悉自訂轉換。

## <a name="thumbnail-parameters"></a>縮圖參數

您應該設定下列參數：

- **開始** -輸入影片中開始產生縮圖的位置。 此值可為 ISO 8601 格式 (例如，PT05S 從5秒開始) 或框架計數 (例如，10開始于第10個框架) ，或的相對值，以串流期間的10% 開始 (。 也支援宏 {最佳}，它會指示編碼器從影片的前幾秒內選取最適合的縮圖，而且只會產生一個縮圖，不論步驟和範圍有哪些其他設定。 預設值為宏 {最佳}。
- **步驟** -產生縮圖的間隔。 此值可為 ISO 8601 格式 (例如，每隔5秒為一個影像 PT05S) ，或一個框架計數 (例如，每30個畫面) 有一個影像的30個，或是一個影像的相對值 (例如，每隔10% 的串流持續時間) 為一個影像10%。 步驟值會影響第一個產生的縮圖，而這可能不是轉換預設開始時間所指定的縮圖。 這是因為編碼器，它會嘗試選取開始時間與開始時間之間的最大縮圖做為第一個輸出。 因為預設值為10%，這表示如果資料流程的持續時間很長，則第一個產生的縮圖可能遠遠離開始時指定的縮圖。 如果預期第一個縮圖接近開始時間，請嘗試為步驟選取合理的值，如果在開始時只需要一個縮圖，請將範圍值設定為1。
- **範圍** -相對於轉換輸入影片中要停止產生縮圖的相對位置。 此值可為 ISO 8601 格式 (例如，PT5M30S 從開始時間的5分鐘和30秒停止) 或框架計數 (例如300在開始時間從畫面格停止300的畫面格。 如果這個值是1，則表示只會在開始時間) 產生一個縮圖，或在資料流程持續期間產生相對值 (例如，50% 從開始時間) 的一半串流期間停止。 預設值為100%，表示要在資料流程的結尾停止。
- **圖層** -編碼器所產生的輸出影像圖層集合。

## <a name="example-of-a-single-png-file-preset"></a>「單一 PNG 檔」預設值範例

您可以使用下列 JSON 預設值，從輸入影片的前幾秒產生單一輸出 PNG 檔，在這種情況下，編碼器會在尋找「有趣」的畫面格時，盡力進行嘗試。 請注意，輸出影像大小已設定為 100%，表示這些會符合輸入視訊的大小。 另外也請注意，"Outputs" 中的 “Format” 設定必須如何符合 “Codecs” 區段中的 "PngLayers" 使用。  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>「一系列的 JPEG 影像」預設值範例

下列 JSON 預設值可用來產生一組10個影像，其時間戳記為5%、15%、...、95% 的輸入時間軸，其中的影像大小指定為輸入影片的一季。

### <a name="json-preset"></a>JSON 預設值

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>「特定時間戳記的一個影像」預設值範例

下列 JSON 預設可以用來在輸入影片的30秒標記處產生單一 JPEG 影像。 此預設值會預期輸入視訊的持續時間為 30 秒以上 (否則作業會失敗)。

### <a name="json-preset"></a>JSON 預設值

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>「不同解析度縮圖」的預設值範例

您可以使用下列預設值，在一個工作中產生不同解析度的縮圖。 在範例中，編碼器會在輸入時間軸的 5%、15%、…、95% 等位置產生兩個影像，一個在輸入視訊解析度的 100% 位置，另一個在 50%。

請注意 FileName 中的 {Resolution} 巨集用法；該巨集會指示編碼器使用的寬度與高度，是產生輸出影像的檔案名稱時，您在預設值編碼區段所指定的。 這也可協助您輕鬆地區分不同的影像。

### <a name="json-preset"></a>JSON 預設值

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>編碼時產生縮圖範例

上述範例已討論如何提交產生影像的編碼工作，但您也可以在縮圖產生時結合視訊/音訊編碼。 下列 JSON 預設值會告知編碼器標準在編碼期間產生縮圖。

### <a name="json-preset"></a>JSON 預設值

如需結構描述的資訊，請參閱[這個](../previous/media-services-mes-schema.md)文章。

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>下一步
[使用 .NET 來產生縮圖](media-services-generate-thumbnails-dotnet.md)