---
title: 使用 Azure Sentinel 中的搜尋即時資料流來偵測威脅 |Microsoft Docs
description: 本文說明如何使用 Azure Sentinel 中的 [搜尋即時資料流] 來追蹤資料。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2020
ms.author: yelevin
ms.openlocfilehash: 14928d3c94ced8d1cd0c12e76428be73b68b91d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "84783158"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>使用 Azure Sentinel 中的搜尋即時資料流來偵測威脅

您可以使用 [搜尋即時資料流] 來建立互動式會話，讓您在事件發生時測試新建立的查詢、從會話取得相符的通知，以及在必要時啟動調查。 您可以使用任何 Log Analytics 查詢，快速建立即時資料流會話。

- **在事件發生時測試新建立的查詢**
    
    您可以測試和調整查詢，而不會與目前正在主動套用至事件的規則產生任何衝突。 確認這些新的查詢如預期般運作之後，您可以藉由選取將會話提升至警示的選項，輕鬆地將這些查詢升階為自訂警示規則。

- **在發生威脅時取得通知**
    
    您可以比較威脅資料摘要與匯總記錄資料，並在發生相符的情況時收到通知。 威脅資料摘要是與潛在或目前威脅相關的持續資料流程，因此通知可能表示您的組織有潛在的威脅。 當您想要收到潛在問題的通知，而不需要維護自訂警示規則的負荷時，請建立即時資料流會話，而不是自訂警示規則。

- **開始調查**
    
    如果有與資產（例如主機或使用者）有關的主動式調查，您可以在記錄資料中，查看該資產上發生的特定 (或任何) 活動。 當該活動發生時，您可以收到通知。


## <a name="create-a-livestream-session"></a>建立即時資料流會話

您可以從現有的搜尋查詢建立即時資料流會話，或是從頭開始建立您的會話。

1. 在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**]。

1. 若要從搜尋查詢建立即時資料流會話：
    
    1. 在 [ **查詢** ] 索引標籤中，找出要使用的搜尋查詢。
    1. 以滑鼠右鍵按一下查詢，然後選取 [ **加入至即時資料流**。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋查詢建立即時資料流會話](./media/livestream/livestream-from-query.png)

1. 從頭開始建立即時資料流會話： 
    
    1. 選取 [ **即時資料流** ] 索引標籤
    1. 按一下 [ **+ 新增即時資料流**]。
    
1. 在 [ **即時資料流** ] 窗格上：
    
    - 如果您已從查詢開始即時資料流，請檢查查詢並進行您想要進行的任何變更。
    - 如果您從頭開始即時資料流，請建立您的查詢。 

1. 從命令列選取 [ **播放** ]。
    
    命令列底下的狀態列會指出您的即時資料流會話是否正在執行或已暫停。 在下列範例中，會話正在執行：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋建立即時資料流會話](./media/livestream/livestream-session.png)

1. 從命令列選取 [ **儲存** ]。
    
    除非您選取 [ **暫停**]，否則會話將會繼續執行，直到您從 Azure 入口網站登出為止。

## <a name="view-your-livestream-sessions"></a>查看您的即時資料流研討會

1. 在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**  >  **即時資料流**] 索引標籤。

1. 選取您要查看或編輯的即時資料流會話。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋查詢建立即時資料流會話](./media/livestream/livestream-tab.png)
    
    您選取的即時資料流會話隨即開啟，讓您播放、暫停、編輯等等。

## <a name="receive-notifications-when-new-events-occur"></a>在發生新事件時接收通知

因為新事件的即時資料流通知會使用 Azure 入口網站通知，您會在每次使用 Azure 入口網站時看到這些通知。 例如：

![即時資料流的 Azure 入口網站通知](./media/livestream/notification.png)

選取通知以開啟 [ **即時資料流** ] 窗格。
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>將即時資料流會話提升為警示

您可以從相關即時資料流會話的命令列中選取 [提高 **警示** ]，將即時資料流會話升階至新的警示：

> [!div class="mx-imgBorder"]
> ![將即時資料流會話提升為警示](./media/livestream/elevate-to-alert.png)

此動作會開啟規則建立嚮導，並預先填入與即時資料流會話相關聯的查詢。

## <a name="next-steps"></a>接下來的步驟

在本文中，您已瞭解如何在 Azure Sentinel 中使用 [搜尋即時資料流]。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動搜捕威脅](hunting.md)
- [使用筆記本來執行自動化搜尋活動](notebooks.md)
