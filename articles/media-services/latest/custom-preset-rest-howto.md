---
title: 使用媒體服務 v3 REST 編碼自訂轉換-Azure |Microsoft Docs
description: 本主題說明如何使用 Azure 媒體服務 v3 來編碼使用 REST 的自訂轉換。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "65761802"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>如何使用自訂轉換進行編碼-REST

使用 Azure 媒體服務進行編碼時，您可以根據業界最佳作法，快速開始使用其中一個建議的內建預設值，如[串流](stream-files-tutorial-with-rest.md#create-a-transform)檔案教學課程中所示。 您也可以建立自訂預設值，以特定案例或裝置需求為目標。

## <a name="considerations"></a>考量

建立自訂預設時，適用下列考慮事項：

* AVC 內容的所有高度和寬度值都必須是4的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率都是以每秒位數為單位。 這不同于預設值和我們的 v2 Api，其使用千位/秒作為單位。 例如，如果 v2 中的位元速率指定為128（千位/秒），則在 v3 中，它會設定為128000（位/秒）。

## <a name="prerequisites"></a>Prerequisites 

- [建立媒體服務帳戶](create-account-cli-how-to.md)。 <br/>請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。<br/>請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 

## <a name="define-a-custom-preset"></a>定義自訂預設值

下列範例會定義新轉換的要求主體。 我們會定義一組我們想要在使用此轉換時產生的輸出。 

在此範例中，我們會先新增音訊編碼的 Aacaudio 屬性圖層，以及用於影片編碼的兩個 H264Video 圖層。 在影片圖層中，我們會指派標籤，以便在輸出檔案名中使用。 接下來，我們希望輸出也包含縮圖。 在下列範例中，我們會以 PNG 格式來指定影像，在輸入影片的解析度50% 產生，並以三個時間戳記（{25%，50%，75}）輸入影片的長度。 最後，我們會指定輸出檔案的格式：一個用於 video + 音訊，另一個用於縮圖。 因為我們有多個 H264Layers，所以我們必須使用每個圖層產生唯一名稱的宏。 我們可以使用 `{Label}` 或 `{Bitrate}` 宏，此範例會顯示前者。

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.AacAudio",
                            "channels": 2,
                            "samplingRate": 48000,
                            "bitrate": 128000,
                            "profile": "AacLc"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.H264Video",
                            "keyFrameInterval": "PT2S",
                            "stretchMode": "AutoSize",
                            "sceneChangeDetection": false,
                            "complexity": "Balanced",
                            "layers": [
                                {
                                    "width": "1280",
                                    "height": "720",
                                    "label": "HD",
                                    "bitrate": 3400000,
                                    "maxBitrate": 3400000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                },
                                {
                                    "width": "640",
                                    "height": "360",
                                    "label": "SD",
                                    "bitrate": 1000000,
                                    "maxBitrate": 1000000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                }
                            ]
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "25%",
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

## <a name="create-a-new-transform"></a>建立新的轉換  

在此範例中，我們建立的**轉換**是以先前定義的自訂預設值為基礎。 建立轉換時，您應該先使用[Get](https://docs.microsoft.com/rest/api/media/transforms/get)來檢查其中一個是否已存在。 如果轉換存在，請重複使用它。 

在您下載的 Postman 集合中，選取 [**轉換和作業**] [ -> **建立或更新轉換**]。

**PUT** HTTP 要求方法類似：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

選取 [**主體**] 索引標籤，並以您稍[早定義](#define-a-custom-preset)的 json 程式碼取代本文。 若要媒體服務將轉換套用到指定的影片或音訊，您必須在該轉換下提交作業。

選取 [傳送]。 

若要媒體服務將轉換套用到指定的影片或音訊，您必須在該轉換下提交作業。 如需示範如何在轉換下提交作業的完整範例，請參閱[教學課程：串流影片檔案-REST](stream-files-tutorial-with-rest.md)。

## <a name="next-steps"></a>後續步驟

查看[其他 REST 作業](https://docs.microsoft.com/rest/api/media/)
