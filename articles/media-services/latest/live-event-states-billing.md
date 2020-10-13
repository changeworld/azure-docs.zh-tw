---
title: Azure 媒體服務中的 LiveEvent 狀態和計費 | Microsoft Docs
description: 本主題提供 Azure 媒體服務 LiveEvent 狀態和計費的概觀。
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1b058eefe22238b60c3482c55b5ae340f4e597f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296731"
---
# <a name="live-event-states-and-billing"></a>實況活動狀態和計費

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在「Azure 媒體服務」中，「實況活動」只要狀態轉換為 [正在執行]****，就會立即開始計費。 即使沒有任何影片流經服務，也會向您收費。 若要停止「實況活動」計費，您必須停止「實況活動」。 即時轉譯的計費方式與實況活動相同。

當[實況活動](/rest/api/media/liveevents)上的**LiveEventEncodingType**設定為 [標準] 或 [Premium1080p] 時，媒體服務會在輸入摘要遺失且沒有執行中的即時**輸出**時，自動關閉仍處於執行中**狀態的任何**實況活動。 不過，您仍將需要支付「實況活動」處於 [正在執行]**** 狀態時間的費用。

> [!NOTE]
> 傳遞即時事件不會自動關閉，而且必須透過 API 明確地停止，以避免過度計費。 

## <a name="states"></a>狀態

「實況活動」的狀態可以是下列其中之一。

|State|描述|
|---|---|
|**已停止**| 這是在建立 (的實況活動初始狀態，除非自動啟動已設為 true。 ) 不會在此狀態下進行計費。 在此狀態下，可以更新「實況活動」屬性，但是不允許進行串流。|
|**啟動中**| 正在啟動「實況活動」並配置資源。 此狀態中不會計費。 在此狀態期間，不允許任何更新或串流。 如果發生錯誤，「實況活動」會回到 [已停止] 狀態。|
|**執行中**| 已配置、內嵌「實況活動」資源，並已產生預覽 URL，而能夠接收即時資料流。 此時已開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。|
|**停止中**| 正在停止「實況活動」並將資源取消佈建。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|
|**刪除**| 正在刪除「實況活動」。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|

當您建立實況活動時，可以選擇啟用即時轉譯。 如果您這樣做，只要實況活動處於執行中狀態，您就必須支付即時轉譯 **的費用** 。 請注意，即使沒有任何音訊流經實況活動，也會向您收費。

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)
