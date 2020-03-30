---
title: 使用媒體服務 v3 Azure CLI 對自訂轉換進行編碼 |微軟文檔
description: 本主題演示如何使用 Azure 媒體服務 v3 使用 Azure CLI 對自訂轉換進行編碼。
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
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382948"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>如何使用自訂轉換進行編碼 - Azure CLI

使用 Azure 媒體服務進行編碼時，您可以根據行業最佳實踐快速啟動推薦的內置預設之一快速入門，如[流式處理檔](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding)快速入門所示。 您還可以構建自訂預設，以定位特定方案或設備要求。

## <a name="considerations"></a>考量

創建自訂預設時，需要考慮以下因素：

* AVC 內容上的高度和寬度的所有值必須為 4 的倍數。
* 在 Azure 媒體服務 v3 中，所有編碼位元速率均以每秒位表示。 這與 v2 API 的預設不同，後者使用千位/秒作為單元。 例如，如果 v2 中的位元速率指定為 128 （千位/秒），則 v3 中它將設置為 128000（位/秒）。

## <a name="prerequisites"></a>Prerequisites

[創建媒體服務帳戶](create-account-cli-how-to.md)。

請務必記住資源群組名稱和「媒體服務」帳戶名稱。

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>定義自訂預設

下面的示例定義新轉換的請求正文。 我們定義一組要在使用此轉換時生成的輸出。

在此示例中，我們首先為音訊編碼添加 AacAudio 圖層，為視頻編碼添加兩個 H264 視頻圖層。 在視頻圖層中，我們分配標籤，以便在輸出檔案名中使用。 接下來，我們希望輸出還包括縮略圖。 在下面的示例中，我們指定以 PNG 格式生成的圖像，以輸入視頻的 50% 解析度生成，並在輸入視頻長度的三個時間戳記中生成圖像 - [25%、50%、75]。 最後，我們指定輸出檔案的格式 - 一個用於視頻 + 音訊，另一個用於縮略圖。 由於我們擁有多個 H264Layers，因此我們必須使用每個圖層生成唯一名稱的宏。 我們可以使用 或`{Label}``{Bitrate}`宏，示例顯示前者。

我們將在此轉換中保存。 在此示例中，我們命名檔`customPreset.json`。

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

## <a name="create-a-new-transform"></a>創建新的轉換  

在此示例中，我們創建一個基於我們前面定義的自訂預設的**轉換**。 創建 Transform 時，應首先檢查是否存在轉換。 如果存在轉換，請重複使用它。 如果轉換`show`存在，`customTransformName`以下命令將返回該轉換：

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

以下 Azure CLI 命令基於自訂預設（定義于前面）創建轉換。

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

對於媒體服務要將轉換應用於指定的視頻或音訊，您需要根據該轉換提交作業。 有關演示如何在轉換下提交作業的完整示例，請參閱[快速入門：流視頻檔 - Azure CLI](stream-files-cli-quickstart.md)。

## <a name="see-also"></a>另請參閱

[Azure CLI](/cli/azure/ams)
