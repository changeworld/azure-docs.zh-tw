---
title: 使用時間轉換和即時輸出創建點播視頻播放
titleSuffix: Azure Media Services
description: 本文介紹如何使用時間轉換和即時輸出來錄製即時流並創建點播播放。
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
ms.date: 08/27/2019
ms.author: juliako
ms.openlocfilehash: 4c7618b60e5fd86a9b8b3f22fb3333c00cfdfa61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899786"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>使用時間轉換和即時輸出創建點播視頻播放

在 Azure 媒體服務中，[即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)物件類似于數位錄影機，它將捕獲即時流並將其記錄到媒體服務帳戶中的資產中。 記錄的內容將保留到[資產](https://docs.microsoft.com/rest/api/media/assets)資源定義的容器中（容器位於附加到帳戶的 Azure 存儲帳戶中）。 Live 輸出還允許您控制傳出即時流的某些屬性，例如存檔錄製中保留的流量（例如，雲 DVR 的容量）或觀看者何時可以開始觀看直播。 磁片上的存檔是一個迴圈存檔"視窗"，它僅保存即時輸出的**存檔 WindowLength**屬性中指定的內容量。 位於此視窗之外的內容將自動從存儲容器中丟棄，並且不可恢復。 存檔視窗長度值表示 ISO-8601 時間跨度持續時間（例如，PTHH：MM：SS），它指定 DVR 的容量。 該值可以從至少三分鐘設置為最多 25 小時。

即時事件與其即時輸出之間的關係與傳統電視廣播類似，因為頻道（即時事件）表示源源不斷的視頻流，錄製（即時輸出）的範圍限定為特定時間段（例如，來自下午6：30至晚上7：00）。 將流流流入即時事件後，可以通過創建資產、即時輸出和流式處理器來開始流式處理事件。 「即時事件」會封存資料流，並透過[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)將它提供給檢視者。 您可以使用不同的封存長度和設定，在實況活動建立多個實況輸出 (最多三個)。 有關即時流式處理工作流的資訊，請參閱[常規步驟](live-streaming-overview.md#general-steps)部分。

## <a name="using-a-dvr-during-an-event"></a>在事件期間使用 DVR

本節討論如何在事件期間使用 DVR 來控制流的哪些部分可用於"倒帶"。

該`archiveWindowLength`值確定查看者可以從當前即時位置返回的時間有多遠。 該`archiveWindowLength`值還確定用戶端清單可以增長多長時間。

假設您正在資料流一場足球比賽，並且`ArchiveWindowLength`只有 30 分鐘。 在比賽開始後 45 分鐘開始觀看事件的檢視者，只能往過去搜尋最多至 15 分鐘標記。 遊戲的即時輸出將繼續，直到即時事件停止。 屬於存檔範圍範圍的內容會不斷從存儲中丟棄，並且不可恢復。 在此示例中，事件開始到 15 分鐘標記之間的視頻將從 DVR 和資產 Blob 存儲中的容器中清除。 存檔不可恢復，並且將從 Azure Blob 存儲中的容器中刪除。

即時事件最多支援三個併發運行的即時輸出（您最多可以從一個即時流同時創建 3 個錄製/存檔）。 此支援允許您根據需要發佈和存檔事件的不同部分。 假設您需要廣播 24 小時全年無休的即時線性饋送，並建立一天中不同節目的「錄製」，以提供給客戶作為可追趕觀看的隨選內容。 對於此方案，您首先創建一個短存檔視窗為 1 小時或更少的主即時輸出 -這是查看者將調諧到的主即時流。 您將為此即時輸出創建流式處理器定位器，並將其作為"即時"源發佈到應用或網站。 在實況活動執行的同時，您可以透過程式設計方式，在節目開始時 (或提早 5 分鐘以提供一些可供稍後修剪的控點)，建立第二個並行的實況輸出。 這第二個實況輸出可在節目結束 5 分鐘後刪除。 使用第二個資產，您可以創建一個新的流式處理器，將此程式發佈為應用目錄中的按需資產。 只要來自第一個實況輸出的「即時」摘要繼續廣播線性摘要，您就可以針對您想要當作隨選視訊來共用的其他節目界限或精選，多次重複此程序。

## <a name="creating-an-archive-for-on-demand-playback"></a>創建存檔以進行按需播放

刪除即時輸出時，即時輸出存檔為自動成為按需資產的資產。 必須先刪除所有即時輸出，然後才能停止即時事件。 您可以使用可選標誌[刪除輸出OnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body)以自動刪除停止時的即時輸出。

即使在您停止並刪除事件後，只要您不刪除資產，使用者也可以將存檔的內容作為視頻點播進行資料流。 如果某個資產被事件使用，則不應刪除該資產;必須首先刪除該事件。

如果您已使用流式處理器發佈即時輸出資產，則即時事件（最多 DVR 視窗長度）將繼續可查看，直到流式處理器過期或刪除（以先到者為准）。

如需詳細資訊，請參閱

- [即時串流概觀](live-streaming-overview.md)
- [即時串流教學課程](stream-live-tutorial-with-api.md)

> [!NOTE]
> 刪除即時輸出時，不會刪除資產中的基礎資產和內容。

## <a name="next-steps"></a>後續步驟

* [子剪輯您的視頻](subclip-video-rest-howto.md)。
* [定義資產的篩選器](filters-dynamic-manifest-rest-howto.md)。
