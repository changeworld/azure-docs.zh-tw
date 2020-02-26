---
title: Azure 媒體服務即時事件錯誤代碼 |Microsoft Docs
description: 本文列出即時事件錯誤碼。
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
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599466"
---
# <a name="media-services-live-event-error-codes"></a>媒體服務即時事件錯誤代碼

下表列出[即時事件](live-events-outputs-concept.md)錯誤碼：

|錯誤|描述|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|當內送編碼器傳送的資料流程超過30fps 以編碼即時事件/通道時，就會發生此錯誤。|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|當連入編碼器傳送的資料流程超過下列解決方式時，就會發生此錯誤：用於編碼即時事件/通道的1920x1088，以及用於傳遞即時事件/通道的 4096 x 2160。|

## <a name="see-also"></a>另請參閱

[串流端點（原點）錯誤碼](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>後續步驟

[教學課程：使用媒體服務即時串流](stream-live-tutorial-with-api.md)
