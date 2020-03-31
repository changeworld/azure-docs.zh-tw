---
title: 即時轉譯
titleSuffix: Azure Media Services
description: 瞭解 Azure 媒體服務的即時轉錄。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499868"
---
# <a name="live-transcription-preview"></a>即時轉錄（預覽）

Azure 媒體服務以不同的協定提供視頻、音訊和文本。 當您使用 MPEG-DASH 或 HLS/CMAF 發佈即時流時，以及視頻和音訊，我們的服務提供 IMSC1.1 相容 TTML 中的轉錄文本。 交付被打包成 MPEG-4 第 30 部分（ISO/IEC 14496-30）片段。 如果使用通過 HLS/TS 傳遞，則文本將作為分塊 VTT 傳遞。

本文介紹如何使用 Azure 媒體服務 v3 資料流即時事件時啟用即時轉錄。 在繼續之前，請確保您熟悉媒體服務 v3 REST API 的使用（有關詳細資訊，請參閱[本教程](stream-files-tutorial-with-rest.md)）。 您還應熟悉[即時流式處理](live-streaming-overview.md)的概念。 建議[使用媒體服務教程完成"流即時](stream-live-tutorial-with-api.md)"。

> [!NOTE]
> 目前，即時轉錄僅在美國西部 2 區域作為預覽功能提供。 它支援將英語口語轉錄為文本。 此功能的 API 引用位於下方 — 請看它是預覽版，我們的 REST 文檔不提供詳細資訊。

## <a name="creating-the-live-event"></a>創建即時事件

要創建即時事件，請將 PUT 操作發送到 2019-05-01 預覽版，例如：

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

該操作具有以下正文（其中使用 RTMP 作為引入協定創建傳遞即時事件）。 請注意添加轉錄屬性。 唯一允許的語言值是 en-US。

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

輪詢即時事件的狀態，直到它進入"正在運行"狀態，這表明您現在可以發送貢獻 RTMP 源。 現在，您可以按照本教程中相同的步驟，例如檢查預覽源和創建即時輸出。

## <a name="transcription-delivery-and-playback"></a>轉錄傳遞和播放

查看[動態打包概述](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)文章，瞭解我們的服務如何使用動態打包以不同協定提供視頻、音訊和文本。 當您使用 MPEG-DASH 或 HLS/CMAF 發佈即時流時，以及視頻和音訊，我們的服務提供 IMSC1.1 相容 TTML 中的轉錄文本。 此交付被打包到 MPEG-4 第 30 部分（ISO/IEC 14496-30） 片段中。 如果使用通過 HLS/TS 傳遞，則文本將作為分塊 VTT 傳遞。 可以使用 Web 播放機（如[Azure 媒體播放機）](use-azure-media-player.md)來播放流。  

> [!NOTE]
> 如果使用 Azure 媒體播放機，請使用版本 2.3.3 或更高版本。

## <a name="known-issues"></a>已知問題

對於預覽，以下是即時轉錄的已知問題：

* 此功能僅在美國西部 2 中可用。
* 應用需要使用[媒體服務 v3 OpenAPI 規範](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json)中描述的預覽 API。
* 唯一支援的語言是英語。
* 使用內容保護，僅支援 AES 信封加密。

## <a name="next-steps"></a>後續步驟

* [媒體服務概觀](media-services-overview.md)
