---
title: 如何使用 Azure 媒體服務編碼器標準搭配 REST 產生縮圖
description: 本文說明如何使用 REST 來編碼資產，並使用媒體編碼器標準同時產生縮圖。
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
ms.date: 08/10/2020
ms.author: inhenkel
ms.openlocfilehash: 635c1bb500f563da3c0eef8698cad8ab9fa5c810
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068098"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>如何使用編碼器標準搭配 REST 產生縮圖

您可以使用媒體編碼器標準，以 [JPEG](https://en.wikipedia.org/wiki/JPEG)、[PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 或 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 影像檔案格式，從您的輸入視訊中產生一或多個縮圖。

## <a name="recommended-reading-and-practice"></a>建議的閱讀與實務

建議您透過閱讀[如何使用自訂轉換-REST 進行編碼](custom-preset-rest-howto.md)，來熟悉自訂轉換。

## <a name="thumbnail-parameters"></a>縮圖參數

您應該設定下列參數：

- **開始**-輸入影片中開始產生縮圖的位置。 此值可以是 ISO 8601 格式 (例如，從5秒開始的 PT05S) 或框架計數 (例如，10從10個畫面格開始) ，或使用相對值來串流持續時間 (例如，10% 從串流持續時間的10% 開始) 。 也支援宏 {最佳}，這會指示編碼器從影片的前幾秒內選取最佳縮圖，而且只會產生一個縮圖，無論步驟和範圍的其他設定為何。 預設值為宏 {最佳}。
- **step** -產生縮圖的間隔。 此值可以是 ISO 8601 格式 (例如，每5秒) 一個影像的 PT05S，或一個框架計數 (例如，每隔30個畫面) 一個影像30，或一個相對值來串流持續時間 (例如，每隔10% 的串流持續時間) 一個影像10%。 步驟值會影響第一個產生的縮圖，而這可能不是轉換預設開始時間所指定。 這是因為編碼器所造成，它會嘗試選取開始時間和步驟位置之間的最佳縮圖做為第一個輸出。 由於預設值是10%，因此，如果資料流程的持續時間很長，第一個產生的縮圖可能會遠離在開始時間指定的縮圖。 如果第一個縮圖應接近開始時間，請嘗試為 [步驟] 選取合理的值，或在開始時間只需要一個縮圖時，將 [範圍] 值設定為 [1]。
- **範圍**-在輸入影片中相對於轉換預設開始時間的位置，以停止產生縮圖。 此值可以是 ISO 8601 格式 (例如，PT5M30S 會從開始時間) 的5分鐘和30秒停止，或框架計數 (例如，300在開始時間從框架的300框架停止。 如果這個值是1，則表示只會在開始時產生一個縮圖) ，或是在資料流程持續時間的相對值 (例如，50% 在開始時間) 的一半資料流程期間停止。 預設值為100%，表示在資料流程結尾處停止。
- **圖層**-由編碼器產生的輸出影像層集合。

## <a name="example-of-a-single-png-file-preset"></a>「單一 PNG 檔」預設值範例

下列 JSON 預設值可用來從輸入影片的前幾秒產生單一輸出 PNG 檔案，其中編碼器會盡力嘗試尋找「有趣」的畫面。 請注意，輸出影像大小已設定為 100%，表示這些會符合輸入視訊的大小。 另外也請注意，"Outputs" 中的 “Format” 設定必須如何符合 “Codecs” 區段中的 "PngLayers" 使用。  

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

下列 JSON 預設值可以用來產生一組10個影像，其時間戳記為5%、15%、...、95% 的輸入時程表，其中影像大小指定為輸入影片的一季。

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

下列 JSON 預設值可以用來在輸入影片的30秒標記產生單一 JPEG 影像。 此預設值會預期輸入視訊的持續時間為 30 秒以上 (否則作業會失敗)。

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

請注意 FileName 中的 {Resolution} 巨集用法；該巨集會指示編碼器使用的寬度與高度，是產生輸出影像的檔案名稱時，您在預設值編碼區段所指定的。 這也有助於您輕鬆區分不同的影像。

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

上述範例已討論如何提交產生影像的編碼工作，但您也可以在縮圖產生時結合視訊/音訊編碼。 下列 JSON 預設值會指示編碼器標準在編碼期間產生縮圖。

### <a name="json-preset"></a>JSON 預設值

如需結構描述的資訊，請參閱[這個](/azure/media-services/previous/media-services-mes-schema)文章。

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

## <a name="next-steps"></a>後續步驟
[使用 .NET 產生縮圖](media-services-generate-thumbnails-dotnet.md)
