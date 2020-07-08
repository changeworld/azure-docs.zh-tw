---
title: 在 Azure Sentinel 中使用 [搜尋 Livestream] 來偵測威脅 |Microsoft Docs
description: 本文說明如何使用 Azure Sentinel 中的 [搜尋 Livestream] 來追蹤資料。
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783158"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>在 Azure Sentinel 中使用搜尋 livestream 來偵測威脅

使用 [搜尋 livestream] 來建立互動式會話，讓您在發生事件時測試新建立的查詢、在找到相符的情況時從會話取得通知，並視需要啟動調查。 您可以使用任何 Log Analytics 查詢來快速建立 livestream 會話。

- **在發生事件時測試新建立的查詢**
    
    您可以測試並調整查詢，而不會發生與目前正套用到事件的規則之間的任何衝突。 在您確認這些新查詢如預期般運作之後，您可以藉由選取一個選項來提升警示的會話，輕鬆地將其升級為自訂警示規則。

- **在發生威脅時收到通知**
    
    您可以比較威脅資料摘要與匯總記錄資料，並在發生相符的情況時收到通知。 威脅資料摘要是持續的資料流程，與潛在或目前的威脅相關，因此通知可能表示您的組織可能有潛在威脅。 當您想要收到潛在問題的通知，而不需要維護自訂警示規則的額外負荷時，請建立 livestream 會話，而不是自訂警示規則。

- **啟動調查**
    
    如果有作用中的調查涉及資產（例如主機或使用者），您可以在記錄資料中查看該資產所發生的特定（或任何）活動。 您可以在該活動發生時收到通知。


## <a name="create-a-livestream-session"></a>建立 livestream 會話

您可以從現有的搜尋查詢建立 livestream 會話，或從頭開始建立您的會話。

1. 在 [Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**]。

1. 若要從搜尋查詢建立 livestream 會話：
    
    1. 從 [**查詢**] 索引標籤中，找出要使用的搜尋查詢。
    1. 以滑鼠右鍵按一下查詢，然後選取 [**新增至 livestream**]。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋查詢建立 Livestream 會話](./media/livestream/livestream-from-query.png)

1. 若要從頭開始建立 livestream 會話： 
    
    1. 選取 [ **Livestream** ] 索引標籤
    1. 按一下 [ **+ 新增 livestream**]。
    
1. 在 [ **Livestream** ] 窗格上：
    
    - 如果您從查詢開始 livestream，請檢查查詢，並進行您想要進行的任何變更。
    - 如果您從頭開始 livestream，請建立您的查詢。 

1. 從命令列選取 [**播放**]。
    
    命令列底下的狀態列會指出您的 livestream 會話是否正在執行或已暫停。 在下列範例中，會話正在執行：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋建立 livestream 會話](./media/livestream/livestream-session.png)

1. 從命令列中選取 [**儲存**]。
    
    除非您選取 [**暫停**]，否則會話會繼續執行，直到您從 Azure 入口網站登出為止。

## <a name="view-your-livestream-sessions"></a>查看您的 livestream 會話

1. 在 [Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**  >  **Livestream** ] 索引標籤。

1. 選取您想要查看或編輯的 livestream 會話。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋查詢建立 livestream 會話](./media/livestream/livestream-tab.png)
    
    您選取的 livestream 會話隨即開啟，以供您播放、暫停、編輯等等。

## <a name="receive-notifications-when-new-events-occur"></a>在發生新事件時收到通知

因為新事件的 livestream 通知使用 Azure 入口網站通知，所以您會在每次使用 Azure 入口網站時看到這些通知。 例如：

![Livestream 的 Azure 入口網站通知](./media/livestream/notification.png)

選取通知以開啟 [ **Livestream** ] 窗格。
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>將 livestream 會話提升為警示

您可以從相關 livestream 會話上的命令列中選取 [**提升警示**]，將 livestream 會話升級為新的警示：

> [!div class="mx-imgBorder"]
> ![將 livestream 會話提升為警示](./media/livestream/elevate-to-alert.png)

此動作會開啟 [規則建立嚮導]，它會預先填入與 livestream 會話相關聯的查詢。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Sentinel 中使用搜尋 livestream。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動搜捕威脅](hunting.md)
- [使用筆記本來執行自動化的搜尋活動](notebooks.md)
