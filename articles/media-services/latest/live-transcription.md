---
title: 即時轉譯
titleSuffix: Azure Media Services
description: 深入瞭解 Azure 媒體服務即時轉譯。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79499868"
---
# <a name="live-transcription-preview"></a>即時轉譯（預覽）

Azure 媒體服務會以不同的通訊協定提供影片、音訊和文字。 當您使用 MPEG 或 HLS/CMAF 發佈您的即時串流，並搭配影片和音訊時，我們的服務會在 IMSC 1.1 相容 TTML 中傳遞轉譯文字。 此傳遞封裝為 MPEG-2 第30部分（ISO/IEC 14496-30）片段。 如果透過 HLS/TS 使用傳遞，則會以區塊 VTT 的形式傳遞文字。

本文說明如何在使用 Azure 媒體服務 v3 串流處理實況活動時，啟用即時轉譯。 在繼續之前，請確定您已熟悉媒體服務 v3 REST Api 的使用方式（如需詳細資訊，請參閱[本教學](stream-files-tutorial-with-rest.md)課程）。 您也應該熟悉[即時串流](live-streaming-overview.md)的概念。 建議使用媒體服務教學課程來[即時完成串流](stream-live-tutorial-with-api.md)。

> [!NOTE]
> 目前，即時轉譯僅以美國西部2區域中的預覽功能形式提供。 它支援以英文轉譯成文字的語音文字。 這項功能的 API 參考位於下列位置： volumename 它處於預覽狀態，但我們的 REST 檔無法取得詳細資料。

## <a name="creating-the-live-event"></a>建立實況活動

若要建立實況活動，請將 PUT 作業傳送至 2019-05-01-preview 版本，例如：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

作業具有下列主體（其中以 RTMP 做為內嵌通訊協定來建立傳遞即時事件）。 請注意，轉譯屬性的新增。 [Language] 唯一允許的值是 en-us。

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

輪詢即時事件的狀態，直到進入「執行中」狀態為止，這表示您現在可以傳送投稿 RTMP 摘要。 您現在可以遵循本教學課程中的相同步驟，如檢查預覽摘要和建立即時輸出。

## <a name="transcription-delivery-and-playback"></a>轉譯傳遞和播放

請參閱[動態封裝總覽](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)一文，瞭解我們的服務如何使用動態封裝，以不同的通訊協定傳遞影片、音訊和文字。 當您使用 MPEG 或 HLS/CMAF 發佈您的即時串流，並搭配影片和音訊時，我們的服務會在 IMSC 1.1 相容 TTML 中傳遞轉譯文字。 此傳遞封裝為 MPEG-2 第30部分（ISO/IEC 14496-30）片段。 如果透過 HLS/TS 使用傳遞，則會以區塊 VTT 的形式傳遞文字。 您可以使用 web 播放機（例如[Azure 媒體播放機](use-azure-media-player.md)）來播放串流。  

> [!NOTE]
> 如果使用 Azure 媒體播放機，請使用2.3.3 或更新版本。

## <a name="known-issues"></a>已知問題

針對預覽，下列是即時轉譯的已知問題：

* 此功能僅適用于美國西部2。
* 應用程式需要使用預覽 Api，如[媒體服務 V3 OpenAPI 規格](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)中所述。
* 唯一支援的語言是英文（en-us）。
* 使用內容保護時，只支援 AES 信封加密。

## <a name="next-steps"></a>後續步驟

* [媒體服務概觀](media-services-overview.md)
