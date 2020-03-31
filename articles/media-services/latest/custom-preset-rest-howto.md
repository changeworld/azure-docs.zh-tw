---
title: 使用媒體服務 v3 REST 對自訂轉換進行編碼 - Azure |微軟文檔
description: 本主題演示如何使用 Azure 媒體服務 v3 使用 REST 對自訂轉換進行編碼。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761802"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>如何使用自訂轉換進行編碼 - REST

使用 Azure 媒體服務進行編碼時，您可以根據行業最佳實踐快速啟動推薦的內置預設之一，如[流式處理檔](stream-files-tutorial-with-rest.md#create-a-transform)教程所示。 您還可以構建自訂預設，以定位特定方案或設備要求。

## <a name="considerations"></a>考量

創建自訂預設時，需要考慮以下因素：

* AVC 內容上的高度和寬度的所有值必須為 4 的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率均以每秒位表示。 這與 v2 API 的預設不同，後者使用千位/秒作為單元。 例如，如果 v2 中的位元速率指定為 128 （千位/秒），則 v3 中它將設置為 128000（位/秒）。

## <a name="prerequisites"></a>Prerequisites 

- [創建媒體服務帳戶](create-account-cli-how-to.md)。 <br/>請務必記住資源群組名稱和「媒體服務」帳戶名稱。 
- [設定 Postman 以進行 Azure 媒體服務 REST API 呼叫](media-rest-apis-with-postman.md)。<br/>請務必遵循本[取得 Azure AD 權杖](media-rest-apis-with-postman.md#get-azure-ad-token)主題的最後一個步驟。 

## <a name="define-a-custom-preset"></a>定義自訂預設

下面的示例定義新轉換的請求正文。 我們定義一組要在使用此轉換時生成的輸出。 

在此示例中，我們首先為音訊編碼添加 AacAudio 圖層，為視頻編碼添加兩個 H264 視頻圖層。 在視頻圖層中，我們分配標籤，以便在輸出檔案名中使用。 接下來，我們希望輸出還包括縮略圖。 在下面的示例中，我們指定以 PNG 格式生成的圖像，以輸入視頻的 50% 解析度生成，並在輸入視頻長度的三個時間戳記中生成圖像 - [25%、50%、75]。 最後，我們指定輸出檔案的格式 - 一個用於視頻 + 音訊，另一個用於縮略圖。 由於我們擁有多個 H264Layers，因此我們必須使用每個圖層生成唯一名稱的宏。 我們可以使用 或`{Label}``{Bitrate}`宏，示例顯示前者。

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

## <a name="create-a-new-transform"></a>創建新的轉換  

在此示例中，我們創建一個基於我們前面定義的自訂預設的**轉換**。 創建 Transform 時，應首先使用[Get](https://docs.microsoft.com/rest/api/media/transforms/get)檢查是否存在轉換。 如果存在轉換，請重複使用它。 

在下載的 Postman 集合中，選擇 **"轉換"和"作業**->**創建或更新轉換**"。

**PUT** HTTP 要求方法類似：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

選擇"**正文"** 選項卡，並將正文替換為[您之前定義的](#define-a-custom-preset)json 代碼。 對於媒體服務要將轉換應用於指定的視頻或音訊，您需要根據該轉換提交作業。

選擇 **"發送**"。 

對於媒體服務要將轉換應用於指定的視頻或音訊，您需要根據該轉換提交作業。 有關演示如何在轉換下提交作業的完整示例，請參閱[教程：資料流視頻檔 - REST](stream-files-tutorial-with-rest.md)。

## <a name="next-steps"></a>後續步驟

查看[其他 REST 操作](https://docs.microsoft.com/rest/api/media/)
