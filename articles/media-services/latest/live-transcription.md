---
title: 即時轉譯
titleSuffix: Azure Media Services
description: 深入瞭解 Azure 媒體服務即時轉譯。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/12/2019
ms.author: inhenkel
ms.openlocfilehash: da80dacadbef560bb597a235fee59924d3887e19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84765007"
---
# <a name="live-transcription-preview"></a>即時轉譯（預覽）

Azure 媒體服務會以不同的通訊協定提供影片、音訊和文字。 當您使用 MPEG 或 HLS/CMAF 發佈您的即時串流，並搭配影片和音訊時，我們的服務會在 IMSC 1.1 相容 TTML 中傳遞轉譯文字。 此傳遞封裝為 MPEG-2 第30部分（ISO/IEC 14496-30）片段。 如果透過 HLS/TS 使用傳遞，則會以區塊 VTT 的形式傳遞文字。

開啟即時轉譯時，會收取額外費用。 請參閱[媒體服務定價頁面](https://azure.microsoft.com/pricing/details/media-services/)的即時影片一節中的定價資訊。

本文說明如何在使用 Azure 媒體服務串流處理即時事件時，啟用即時轉譯。 在繼續之前，請確定您已熟悉媒體服務 v3 REST Api 的使用方式（如需詳細資訊，請參閱[本教學](stream-files-tutorial-with-rest.md)課程）。 您也應該熟悉[即時串流](live-streaming-overview.md)的概念。 建議使用媒體服務教學課程來[即時完成串流](stream-live-tutorial-with-api.md)。

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

## <a name="create-the-live-event-with-live-transcription"></a>使用即時轉譯來建立實況活動

若要建立已開啟轉譯的實況活動，請使用 2019-05-01-preview API 版本傳送 PUT 作業，例如：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

作業具有下列主體（其中以 RTMP 做為內嵌通訊協定來建立傳遞即時事件）。 請注意，轉譯屬性的新增。

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

## <a name="start-or-stop-transcription-after-the-live-event-has-started"></a>在實況活動開始後開始或停止轉譯

當即時事件處於執行中狀態時，您可以啟動和停止即時轉譯。 如需有關啟動和停止即時事件的詳細資訊，請參閱[使用媒體服務 V3 api 進行開發](media-services-apis-overview.md#long-running-operations)中的長時間執行作業一節。

若要開啟即時轉譯或更新轉譯語言，請修補即時事件以包含 "轉譯" 屬性。 若要關閉即時轉譯，請從 live 事件物件中移除 "轉譯" 屬性。  

> [!NOTE]
> 在實況活動期間開啟或關閉**轉譯多次，** 並不是支援的案例。

這是開啟即時轉譯的範例呼叫。

跳```https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview```

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

請參閱[動態封裝總覽](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)一文，瞭解我們的服務如何使用動態封裝，以不同的通訊協定傳遞影片、音訊和文字。 當您使用 MPEG 或 HLS/CMAF 發佈您的即時串流，並搭配影片和音訊時，我們的服務會在 IMSC 1.1 相容 TTML 中傳遞轉譯文字。 此傳遞封裝為 MPEG-2 第30部分（ISO/IEC 14496-30）片段。 如果透過 HLS/TS 使用傳遞，則會以區塊 VTT 的形式傳遞文字。 您可以使用 web 播放機（例如[Azure 媒體播放機](use-azure-media-player.md)）來播放串流。  

> [!NOTE]
> 如果使用 Azure 媒體播放機，請使用2.3.3 或更新版本。

## <a name="known-issues"></a>已知問題

針對預覽，下列是即時轉譯的已知問題：

- 應用程式需要使用預覽 Api，如[媒體服務 V3 OpenAPI 規格](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)中所述。
- 數位版權管理（DRM）保護不會套用到文字播放軌，只有 AES 信封加密是可行的。

## <a name="next-steps"></a>後續步驟

* [媒體服務概觀](media-services-overview.md)
