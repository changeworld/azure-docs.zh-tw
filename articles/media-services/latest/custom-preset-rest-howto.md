---
title: 使用媒體服務 v3 REST 編碼自訂轉換-Azure |Microsoft Docs
description: 本主題說明如何使用 Azure 媒體服務 v3，使用 REST 來編碼自訂轉換。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 2656bf93cb9c29ded4b9dde49f0caba91c1654b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89295626"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>如何使用自訂轉換進行編碼-REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒體服務進行編碼時，您可以根據業界最佳作法，使用其中一個建議的內建預設來快速開始，如「 [串流](stream-files-tutorial-with-rest.md#create-a-transform) 檔案」教學課程中所示範。 您也可以建立自訂預設值，以特定案例或裝置需求為目標。

## <a name="considerations"></a>考量

建立自訂預設時，適用下列考慮事項：

* AVC 內容的所有高度和寬度值都必須是4的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率都是每秒位數。 這與使用 v2 Api 的預設值不同，後者使用千位/秒作為單位。 例如，如果 v2 中的位元速率指定為 128 (千位/秒) ，則在 v3 中，它會設定為 128000 (位/秒) 。

## <a name="prerequisites"></a>Prerequisites 

- [建立媒體服務帳戶](./create-account-howto.md)。 <br/>請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。<br/>請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 

## <a name="define-a-custom-preset"></a>定義自訂預設值

下列範例會定義新轉換的要求主體。 我們會定義一組要在使用此轉換時產生的輸出。 

在此範例中，我們會先新增音訊編碼的 >aacaudio 圖層和兩個 >h264video 圖層來進行影片編碼。 在影片圖層中，我們會指派標籤，讓它們可用於輸出檔名稱。 接下來，我們希望輸出也包含縮圖。 在下列範例中，我們會指定 PNG 格式的影像，以50% 的輸入影片解析度產生，以及在輸入影片長度的三個時間戳記-{25%、50%、75} 產生。 最後，我們會指定輸出檔的格式-一個適用于影片 + 音訊，另一個用於縮圖。 由於有多個 H264Layers，因此我們必須使用每一層產生唯一名稱的宏。 我們可以使用 `{Label}` 或 `{Bitrate}` 宏，此範例會顯示前者。

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

在此範例中，我們會根據先前定義的自訂預設值建立 **轉換** 。 建立轉換時，您應該先使用 [Get](/rest/api/media/transforms/get) 來檢查是否已有一個轉換。 如果轉換存在，請重複使用它。 

在您下載的 Postman 集合中，選取 [**轉換] 和 [作業** -> **建立或更新轉換**]。

**PUT** HTTP 要求方法類似：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

選取 [內 **文] 索引標籤，並** 使用您稍 [早所定義](#define-a-custom-preset)的 json 程式碼來取代主體。 針對媒體服務將轉換套用至指定的影片或音訊，您需要在該轉換下提交作業。

選取 [傳送]。 

針對媒體服務將轉換套用至指定的影片或音訊，您需要在該轉換下提交作業。 如需示範如何在轉換下提交作業的完整範例，請參閱 [教學課程：串流影片檔案-REST](stream-files-tutorial-with-rest.md)。

## <a name="next-steps"></a>後續步驟

查看 [其他 REST 作業](/rest/api/media/)
