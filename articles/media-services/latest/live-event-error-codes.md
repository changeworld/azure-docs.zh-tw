---
title: Azure 媒體服務即時事件錯誤代碼 |微軟文檔
description: 本文列出了即時事件錯誤代碼。
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654574"
---
# <a name="media-services-live-event-error-codes"></a>媒體服務即時事件錯誤代碼

本節中的表列出了[即時事件](live-events-outputs-concept.md)錯誤代碼。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

當您訂閱即時[事件的事件網格](https://docs.microsoft.com/azure/event-grid/)事件時，您可能會看到[LiveEventConnection拒絕](media-services-event-schemas.md#liveeventconnectionrejected)事件中的以下錯誤之一。

| 結果碼 | 描述 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 不正確的內嵌 URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 編碼器 IP 不存在於所設定的 IP 允許清單中 |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP 編碼器未發送 setDataFrame 命令。 |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 不支援指定的轉碼器。 |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |在提供實際媒體資料之前，未收到媒體描述資訊。|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |音訊或視頻類型的品質計數超過允許的最大限制。|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |即時事件或通道服務中的總傳入位元速率超過了允許的最大限制。|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | 來自 RTMP 編碼器的視訊或音訊 FLVTag 時間戳記無效。 |
| MPE_INGEST_FRAMERATE_EXCEEDED | 具有畫面播放速率的傳入編碼器引入的流超過對即時事件/通道進行編碼的最大允許的 30fps。|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | 傳入編碼器引入的流超過了以下允許的解析度：1920x1088 用於編碼即時事件/通道，4096 x 2160 用於傳遞即時事件/通道。|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

您可能會從[LiveEventEncoder 斷開連接](media-services-event-schemas.md#liveeventencoderdisconnected)事件中看到以下錯誤之一。

|結果碼|描述|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|RTMP 工作階段在閒置達允許時間限制後逾時。|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|來自 RTMP 編碼器的視訊或音訊 FLVTag 時間戳記無效。|
|MPE_CAPACITY_LIMIT_REACHED|編碼器的資料傳送速度太快。|
|未知錯誤代碼|這些錯誤碼的範圍從記憶體錯誤到雜湊對應中有重複項目。|


## <a name="see-also"></a>另請參閱

[流式處理終結點（原點）錯誤代碼](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>後續步驟

[教程：使用媒體服務進行流直播](stream-live-tutorial-with-api.md)
