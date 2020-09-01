---
title: 使用時間變化和即時輸出來建立隨選影片播放
titleSuffix: Azure Media Services
description: 本文說明如何使用時間變動和即時輸出來記錄即時資料流，以及建立隨選播放。
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f188d959bf702a2907fe34805b41fa62cae7ee40
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265383"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>使用時間變化和即時輸出來建立隨選影片播放

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

在 Azure 媒體服務中， [即時輸出](/rest/api/media/liveoutputs) 物件就像是數位視訊錄製器，它會將您的即時串流攔截並記錄至媒體服務帳戶中的資產。 記錄的內容會保存到 [資產](/rest/api/media/assets) 資源所定義的容器中， (容器位於附加至您帳戶) 的 Azure 儲存體帳戶中。 即時輸出也可讓您控制傳出即時資料流的一些屬性，例如在封存記錄中保留的串流數量 (例如，雲端 DVR) 的容量，或當檢視器可以開始觀看即時資料流時。 磁片上的封存是迴圈封存的「視窗」，只會保留即時輸出的 **archiveWindowLength** 屬性中所指定的內容數量。 落在此視窗外的內容會自動從儲存體容器中捨棄，且無法復原。 ArchiveWindowLength 值代表 ISO-8601 timespan 持續時間 (例如 PTHH： MM： SS) ，指定 DVR 的容量。 值最少可設定為一分鐘到最多25小時。

實況活動和其即時輸出之間的關聯性與傳統電視廣播類似，因為通道 (實況活動) 代表影片的持續串流和錄製 (即時輸出) 範圍設定為特定的時間範圍 (例如，從6：30PM 到7： 00) 的夜晚新聞。 當資料流程流入實況活動之後，您就可以建立資產、即時輸出和串流定位器來開始串流事件。 「即時事件」會封存資料流，並透過[串流端點](/rest/api/media/streamingendpoints)將它提供給檢視者。 您可以使用不同的封存長度和設定，在實況活動建立多個實況輸出 (最多三個)。 如需即時串流工作流程的詳細資訊，請參閱 [一般步驟](live-streaming-overview.md#general-steps) 一節。

## <a name="using-a-dvr-during-an-event"></a>在事件期間使用 DVR

本節討論如何在事件期間使用 DVR，以控制資料流程的哪些部分可用於「倒轉」。

此 `archiveWindowLength` 值決定檢視器可以從目前的即時位置返回多久的時間。 此 `archiveWindowLength` 值也會決定用戶端資訊清單可以成長的時間長度。

假設您要串流足球遊戲，而且 `ArchiveWindowLength` 只有30分鐘的時間。 在比賽開始後 45 分鐘開始觀看事件的檢視者，只能往過去搜尋最多至 15 分鐘標記。 遊戲的即時輸出將繼續進行，直到實況活動停止為止。 超出 archiveWindowLength 範圍的內容會持續從儲存體中捨棄，且無法復原。 在此範例中，事件開始和15分鐘標記之間的影片，會從您的 DVR 和 blob 儲存體中的容器清除。 封存無法復原，而且會從 Azure blob 儲存體中的容器移除。

實況活動支援最多三個同時執行的即時輸出 (您可以同時從一個即時資料流建立最多3個錄製/封存) 。 這項支援可讓您視需要發行和封存事件的不同部分。 假設您需要廣播 24 小時全年無休的即時線性饋送，並建立一天中不同節目的「錄製」，以提供給客戶作為可追趕觀看的隨選內容。 在此案例中，您會先建立一個簡短的封存時間範圍為1小時或更少的主要即時輸出，這是您的檢視器會微調的主要即時資料流。 您會建立此即時輸出的串流定位器，並將其發佈至您的應用程式或網站作為「即時」摘要。 在實況活動執行的同時，您可以透過程式設計方式，在節目開始時 (或提早 5 分鐘以提供一些可供稍後修剪的控點)，建立第二個並行的實況輸出。 這第二個實況輸出可在節目結束 5 分鐘後刪除。 使用第二個資產時，您可以建立新的串流定位器，以將此程式發佈為應用程式目錄中的隨選資產。 只要來自第一個實況輸出的「即時」摘要繼續廣播線性摘要，您就可以針對您想要當作隨選視訊來共用的其他節目界限或精選，多次重複此程序。

## <a name="creating-an-archive-for-on-demand-playback"></a>建立隨選播放的封存

當即時輸出被刪除時，即時輸出所封存的資產會自動成為隨選資產。 您必須先刪除所有的即時輸出，才能停止即時事件。 您可以使用選擇性旗標 [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) ，在停止時自動移除實況輸出。

即使在停止並刪除事件之後，只要您未刪除資產，使用者就可以視需要將封存的內容串流為隨選影片。 如果事件使用資產，則不應將其刪除。必須先刪除事件。

如果您已使用串流定位器來發佈即時輸出的資產，則即時事件 (最多 DVR 視窗長度) 將可繼續查看，直到串流定位器的到期或刪除為止（以先達到者為准）。

如需詳細資訊，請參閱：

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)

> [!NOTE]
> 當您刪除即時輸出時，不會刪除資產中的基礎資產和內容。

## <a name="next-steps"></a>後續步驟

* [子剪輯您的](subclip-video-rest-howto.md)影片。
* [為您的資產定義篩選準則](filters-dynamic-manifest-rest-howto.md)。
