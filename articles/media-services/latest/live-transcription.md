---
title: 即時轉譯
titleSuffix: Azure Media Services
description: 瞭解 Azure 媒體服務即時轉譯。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c3465e294af104c4d9c3b34960f5e95cf41e7cb8
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291410"
---
# <a name="live-transcription-preview"></a>即時轉譯 (預覽) 

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure 媒體服務會以不同的通訊協定提供影片、音訊和文字。 當您使用 MPEG-2 或 HLS/CMAF 發佈即時串流時，以及影片和音訊，我們的服務會提供 IMSC 1.1 相容 TTML 中的轉譯文字。 傳遞會封裝至 MPEG-2 部分30， (ISO/IEC 14496-30) 片段。 如果透過 HLS/TS 使用傳遞，則文字會以區塊 VTT 的形式傳遞。

開啟即時轉譯時，會收取額外費用。 請參閱 [Media Services 定價頁面](https://azure.microsoft.com/pricing/details/media-services/)的「即時影片」一節中的定價資訊。

本文描述如何在使用 Azure 媒體服務串流實況活動時啟用即時轉譯。 繼續之前，請確定您已熟悉媒體服務 v3 REST Api 的使用 (如需詳細資料，請參閱 [本教學](stream-files-tutorial-with-rest.md) 課程) 。 您也應該熟悉 [即時串流](live-streaming-overview.md) 概念。 建議您 [使用媒體服務](stream-live-tutorial-with-api.md) 教學課程來即時完成串流。

## <a name="live-transcription-preview-regions-and-languages"></a>即時轉譯預覽區域和語言

即時轉譯適用于下欄區域：

- 東南亞
- 西歐
- 北歐
- 美國東部
- 美國中部
- 美國中南部
- 美國西部 2
- 巴西南部

這是可轉譯的可用語言清單，請使用 API 中的語言代碼。

| Language | 語言代碼 |
| -------- | ------------- |
| 卡達隆尼亞文  | ca-ES |
| 丹麥文 (丹麥) | da-DK |
| 德文 (德國) | de-DE |
| 英文 (澳大利亞) | en-AU |
| 英文 (加拿大) | en-CA |
| 英文 (英國) | en-GB |
| 英文 (印度) | en-IN |
| 英文 (紐西蘭) | en-NZ |
| 英文 (美國) | zh-TW |
| 西班牙文 (西班牙) | es-ES |
| 西班牙文 (墨西哥) | es-MX |
| 芬蘭文 (芬蘭) | fi-FI |
| 法文 (加拿大) | fr-CA |
| 法文 (法國) | fr-FR |
| 義大利文 (義大利) | it-IT |
| 荷蘭文 (荷蘭) | nl-NL |
| 葡萄牙文 (巴西) | pt-BR |
| 葡萄牙文 (葡萄牙) | pt-PT |
| 瑞典文 (瑞典) | sv-SE |

## <a name="create-the-live-event-with-live-transcription"></a>使用即時轉譯建立實況活動

若要建立已開啟轉譯的實況活動，請使用 2019-05-01-preview API 版本傳送 PUT 作業，例如：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

作業具有下列主體 (使用 RTMP 來建立傳遞即時事件作為內嵌通訊協定) 。 請注意新增轉譯屬性。

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions",
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>在實況活動開始之後啟動或停止轉譯

當實況活動處於執行中狀態時，您可以啟動和停止即時轉譯。 如需啟動和停止實況活動的詳細資訊，請參閱 [使用媒體服務 V3 api 進行開發](media-services-apis-overview.md#long-running-operations)時的長時間執行作業一節。

若要開啟即時轉譯或更新轉譯語言，請修補實況活動以包含 "轉譯" 屬性。 若要關閉即時轉譯，請從即時事件物件中移除 "轉譯" 屬性。  

> [!NOTE]
> 在實況活動期間開啟或關閉 **多次** 轉譯並不是支援的案例。

這是開啟即時轉譯的範例呼叫。

補丁： ```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

```
{
  "properties": {
    "description": "Demonstrate how to enable live transcriptions", 
    "input": {
      "streamingProtocol": "RTMP",
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "preview": {
      "accessControl": {
        "ip": {
          "allow": [
            {
              "name": "Allow All",
              "address": "0.0.0.0",
              "subnetPrefixLength": 0
            }
          ]
        }
      }
    },
    "encoding": {
      "encodingType": "None"
    },
    "transcriptions": [
      {
        "language": "en-US"
      }
    ],
    "vanityUrl": false,
    "streamOptions": [
      "Default"
    ]
  },
  "location": "West US 2"
}
```

## <a name="transcription-delivery-and-playback"></a>轉譯傳遞和播放

請參閱 [動態封裝的總覽](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) 文章，以瞭解我們的服務如何使用動態封裝，以不同的通訊協定傳遞影片、音訊和文字。 當您使用 MPEG-2 或 HLS/CMAF 發佈即時串流時，以及影片和音訊，我們的服務會提供 IMSC 1.1 相容 TTML 中的轉譯文字。 這項傳遞會封裝至 MPEG-2 部分 30 (ISO/IEC 14496-30) 片段。 如果透過 HLS/TS 使用傳遞，則文字會以區塊 VTT 的形式傳遞。 您可以使用 web 播放機（例如 [Azure 媒體播放機](use-azure-media-player.md) ）來播放串流。  

> [!NOTE]
> 如果使用 Azure 媒體播放機，請使用2.3.3 版或更新版本。

## <a name="known-issues"></a>已知問題

在預覽中，下列是即時轉譯的已知問題：

- 應用程式需要使用預覽 Api，如 [媒體服務 V3 OpenAPI 規格](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)中所述。
- 數位版權管理 (DRM) 保護不會套用到文字播放軌，只有 AES 信封加密才有可能。

## <a name="next-steps"></a>後續步驟

* [媒體服務概觀](media-services-overview.md)
