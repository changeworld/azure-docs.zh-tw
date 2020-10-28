---
title: Azure 媒體服務中的實況活動狀態和計費 |Microsoft Docs
description: 本主題提供 Azure 媒體服務實況活動狀態和計費的總覽。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 2d3d3f5c56bd42aeb148c19fefebc0e7d364cd1c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782360"
---
# <a name="live-event-states-and-billing"></a>實況活動狀態和計費

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒體服務中，實況活動會在其狀態轉換為「 **執行中」或「** **待命** 」時立即開始計費。 即使沒有任何影片流經服務，也會向您收費。 若要停止計費的實況活動，您必須停止實況活動。 即時轉譯的計費方式與實況活動相同。

當 [實況活動](/rest/api/media/liveevents)上的 **LiveEventEncodingType** 設定為 [標準] 或 [Premium1080p] 時，媒體服務會在輸入摘要遺失且沒有執行中的即時 **輸出** 時，自動關閉仍處於執行中 **狀態的任何** 實況活動。 不過，當實況活動處於執行 **中狀態時** ，仍會向您收取費用。

> [!NOTE]
> 傳遞即時事件不會自動關閉，而且必須透過 API 明確地停止，以避免過度計費。

## <a name="states"></a>狀態

實況活動可以是下列其中一種狀態。

|State|描述|
|---|---|
|**已停止**| 這是在建立 (的實況活動初始狀態，除非自動啟動已設為 true。 ) 不會在此狀態下進行計費。 即時事件無法接收任何輸入。 |
|**啟動中**| 正在啟動實況活動，並配置資源。 此狀態中不會計費。  如果發生錯誤，實況活動會回到 [已停止] 狀態。|
| **分配** | 系統會對實況活動呼叫配置動作，並為此實況活動布建資源。 順利完成這項作業之後，實況活動就會轉換成待命狀態。
|**備用**| 已布建實況活動資源，並已準備好開始使用。 計費會在此狀態下發生。  大部分的屬性仍可更新，但在此狀態期間不允許內嵌或串流處理。
|**執行中**| 已配置實況活動資源、內嵌和預覽 Url，而且能夠接收即時串流。 此時已開始計費。 您必須在實況活動資源上明確呼叫 Stop，以停止進一步的計費。|
|**停止中**| 正在停止實況活動，並取消布建資源。 此暫時性狀態中不會計費。 |
|**刪除**| 正在刪除實況活動。 此暫時性狀態中不會計費。 |

當您建立實況活動時，可以選擇啟用即時轉譯。 如果您這樣做，只要實況活動處於執行中狀態，您就必須支付即時轉譯 **的費用** 。 請注意，即使沒有任何音訊流經實況活動，也會向您收費。

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)
