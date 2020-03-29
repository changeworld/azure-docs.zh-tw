---
title: Azure 媒體服務中的 LiveEvent 狀態和計費 | Microsoft Docs
description: 本主題提供 Azure 媒體服務 LiveEvent 狀態和計費的概觀。
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
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543070"
---
# <a name="live-event-states-and-billing"></a>實況活動狀態和計費

在「Azure 媒體服務」中，「實況活動」只要狀態轉換為 [正在執行]****，就會立即開始計費。 即使沒有視頻流經服務，您也會被計費。 若要停止「實況活動」計費，您必須停止「實況活動」。 即時轉錄的計費方式與即時事件相同。

當[即時事件的](https://docs.microsoft.com/rest/api/media/liveevents) **LiveEventEncodeType**設置為標準或高級 1080p 時，媒體服務會自動關閉輸入源丟失 12 小時後仍處於**運行**狀態且沒有**即時輸出**運行的任何即時事件。 不過，您仍將需要支付「實況活動」處於 [正在執行]**** 狀態時間的費用。

> [!NOTE]
> 傳遞即時事件不會自動關閉，必須通過 API 顯式停止，以避免過度計費。 

## <a name="states"></a>狀態

「實況活動」的狀態可以是下列其中之一。

|State|描述|
|---|---|
|**停止**| 這是創建後即時事件的初始狀態（除非自動啟動設置為 true。此狀態下不發生計費。 在此狀態下，可以更新「實況活動」屬性，但是不允許進行串流。|
|**開始**| 正在啟動「實況活動」並配置資源。 此狀態中不會計費。 在此狀態期間，不允許任何更新或串流。 如果發生錯誤，「實況活動」會回到 [已停止] 狀態。|
|**正在執行**| 已配置、內嵌「實況活動」資源，並已產生預覽 URL，而能夠接收即時資料流。 此時已開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。|
|**停止中**| 正在停止「實況活動」並將資源取消佈建。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|
|**刪除**| 正在刪除「實況活動」。 此暫時性狀態中不會計費。 在此狀態期間，不允許任何更新或串流。|

您可以選擇在創建即時事件時啟用即時轉錄。 如果這樣做，每當即時事件處於 **"正在運行"** 狀態時，您都會為即時轉錄收費。 請注意，即使沒有音訊流經即時事件，您也會被計費。

## <a name="next-steps"></a>後續步驟

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)
