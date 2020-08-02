---
title: 使用媒體服務 v3 Azure CLI 編碼自訂轉換 |Microsoft Docs
description: 本主題說明如何使用 Azure 媒體服務 v3 來編碼使用 Azure CLI 的自訂轉換。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: devx-track-azurecli
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 4ea328cc21d849367d36342ed1b0953a7eb26c77
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87503883"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>如何使用自訂轉換進行編碼-Azure CLI

使用 Azure 媒體服務進行編碼時，您可以根據業界最佳作法，快速開始使用其中一個建議的內建預設值，如[串流](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding)檔案快速入門中所示。 您也可以建立自訂預設值，以特定案例或裝置需求為目標。

## <a name="considerations"></a>考量

建立自訂預設時，適用下列考慮事項：

* AVC 內容的所有高度和寬度值都必須是4的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率都是以每秒位數為單位。 這不同于預設值和我們的 v2 Api，其使用千位/秒作為單位。 例如，如果 v2 中的位元速率指定為128（千位/秒），則在 v3 中，它會設定為128000（位/秒）。

## <a name="prerequisites"></a>Prerequisites

[建立媒體服務帳戶](./create-account-howto.md)。

請務必記住資源群組名稱和「媒體服務」帳戶名稱。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>定義自訂預設值

下列範例會定義新轉換的要求主體。 我們會定義一組我們想要在使用此轉換時產生的輸出。

在此範例中，我們會先新增音訊編碼的 Aacaudio 屬性圖層，以及用於影片編碼的兩個 H264Video 圖層。 在影片圖層中，我們會指派標籤，以便在輸出檔案名中使用。 接下來，我們希望輸出也包含縮圖。 在下列範例中，我們會以 PNG 格式來指定影像，在輸入影片的解析度50% 產生，並以三個時間戳記（{25%，50%，75}）輸入影片的長度。 最後，我們會指定輸出檔案的格式：一個用於 video + 音訊，另一個用於縮圖。 因為我們有多個 H264Layers，所以我們必須使用每個圖層產生唯一名稱的宏。 我們可以使用 `{Label}` 或 `{Bitrate}` 宏，此範例會顯示前者。

我們要將此轉換儲存在檔案中。 在此範例中，我們將檔案命名為 `customPreset.json` 。

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

在此範例中，我們建立的**轉換**是以先前定義的自訂預設值為基礎。 建立轉換時，您應該先檢查其中一個是否已存在。 如果轉換存在，請重複使用它。 下列 `show` 命令 `customTransformName` 會傳回轉換（如果存在的話）：

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

下列 Azure CLI 命令會根據自訂預設值（稍早定義）建立轉換。

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

若要媒體服務將轉換套用到指定的影片或音訊，您必須在該轉換下提交作業。 如需示範如何在轉換下提交作業的完整範例，請參閱[快速入門：串流影片檔-Azure CLI](stream-files-cli-quickstart.md)。

## <a name="see-also"></a>請參閱

[Azure CLI](/cli/azure/ams)
