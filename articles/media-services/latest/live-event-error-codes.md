---
title: Azure 媒體服務即時事件錯誤代碼 |Microsoft Docs
description: 本文列出即時事件錯誤碼。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/4/2020
ms.author: inhenkel
ms.openlocfilehash: 8f86c157652f6219afdf1df1da9b8ad385f059aa
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87000946"
---
# <a name="media-services-live-event-error-codes"></a>媒體服務即時事件錯誤代碼

本節中的表格會列出[即時事件](live-events-outputs-concept.md)錯誤碼。

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

當您訂閱即時事件的[事件方格](../../event-grid/index.yml)事件時，您可能會在[LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected)事件中看到下列其中一個錯誤。

| 結果碼 | 描述 |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | 不正確的內嵌 URL |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | 編碼器 IP 不存在於所設定的 IP 允許清單中 |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | RTMP 編碼器未傳送 setDataFrame 命令。 |
| MPE_INGEST_CODEC_NOT_SUPPORTED | 不支援指定的轉碼器。 |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |未在實際媒體資料傳遞之前收到媒體描述資訊。|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |音訊或影片類型的品質計數已超過允許的上限。|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |即時事件或通道服務中的傳入位元速率總計超過允許的上限。|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | 來自 RTMP 編碼器的視訊或音訊 FLVTag 時間戳記無效。 |
| MPE_INGEST_FRAMERATE_EXCEEDED | 包含 framerates 的傳入編碼器內嵌串流超過允許的30fps 編碼即時事件/通道的上限。|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | 內送編碼器內嵌串流超過下列允許的解決方式：用於編碼即時事件/通道的1920x1088，以及用於傳遞即時事件/通道的 4096 x 2160。|
| MPE_INGEST_RTMP_TOO_LARGE_UNPROCESSED_FLV | 實況活動已收到大量的音訊資料，或大量的影片資料，而沒有任何主要畫面格。 我們已中斷編碼器的連線，讓它有機會使用正確的資料重試。 |

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

您可能會在[LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected)事件中看到下列其中一個錯誤。

|結果碼|描述|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|RTMP 工作階段在閒置達允許時間限制後逾時。|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|來自 RTMP 編碼器的視訊或音訊 FLVTag 時間戳記無效。|
|MPE_CAPACITY_LIMIT_REACHED|編碼器的資料傳送速度太快。|
|未知的錯誤代碼|這些錯誤碼的範圍從記憶體錯誤到雜湊對應中有重複項目。|


## <a name="see-also"></a>另請參閱

[串流端點（原點）錯誤碼](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>接下來的步驟

[教學課程：使用媒體服務即時串流](stream-live-tutorial-with-api.md)
