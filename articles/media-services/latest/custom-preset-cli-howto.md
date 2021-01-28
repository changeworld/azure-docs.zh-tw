---
title: 編碼自訂轉換 CLI
description: 本主題說明如何使用 Azure 媒體服務 v3，以使用 Azure CLI 來編碼自訂轉換。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 91ee605035dbc81c2302aa0350763e68dc73ecb3
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956101"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>如何使用自訂轉換進行編碼-Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

使用 Azure 媒體服務進行編碼時，您可以根據業界最佳作法，使用其中一個建議的內建預設來快速開始，如 [串流](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) 檔案快速入門中所示。 您也可以建立自訂預設值，以特定案例或裝置需求為目標。

## <a name="considerations"></a>考量

建立自訂預設時，適用下列考慮事項：

* AVC 內容的所有高度和寬度值都必須是4的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率都是每秒位數。 這與使用 v2 Api 的預設值不同，後者使用千位/秒作為單位。 例如，如果 v2 中的位元速率指定為 128 (千位/秒) ，則在 v3 中，它會設定為 128000 (位/秒) 。

## <a name="prerequisites"></a>Prerequisites

[建立媒體服務帳戶](./create-account-howto.md)。

請務必記住資源群組名稱和「媒體服務」帳戶名稱。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>定義自訂預設值

下列範例會定義新轉換的要求主體。 我們會定義一組要在使用此轉換時產生的輸出。

在此範例中，我們會先新增音訊編碼的 >aacaudio 圖層和兩個 >h264video 圖層來進行影片編碼。 在影片圖層中，我們會指派標籤，讓它們可用於輸出檔名稱。 接下來，我們希望輸出也包含縮圖。 在下列範例中，我們會指定 PNG 格式的影像，以50% 的輸入影片解析度產生，以及在輸入影片長度的三個時間戳記-{25%、50%、75} 產生。 最後，我們會指定輸出檔的格式-一個適用于影片 + 音訊，另一個用於縮圖。 由於有多個 H264Layers，因此我們必須使用每一層產生唯一名稱的宏。 我們可以使用 `{Label}` 或 `{Bitrate}` 宏，此範例會顯示前者。

我們會將這項轉換儲存在檔案中。 在此範例中，我們會為檔案命名 `customPreset.json` 。

```json
{
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
```

## <a name="create-a-new-transform"></a>建立新的轉換  

在此範例中，我們會根據先前定義的自訂預設值建立 **轉換** 。 建立轉換時，您應該先檢查是否已有一個轉換。 如果轉換存在，請重複使用它。 下列 `show` 命令 `customTransformName` 會傳回轉換（如果有的話）：

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

下列 Azure CLI 命令會根據稍早) 定義的自訂預設 (來建立轉換。

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

針對媒體服務將轉換套用至指定的影片或音訊，您需要在該轉換下提交作業。 如需示範如何在轉換下提交作業的完整範例，請參閱 [快速入門：串流影片檔案-Azure CLI](stream-files-cli-quickstart.md)。

## <a name="see-also"></a>另請參閱

[Azure CLI](/cli/azure/ams)
