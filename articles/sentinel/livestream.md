---
title: 在 Azure 哨兵中使用搜索即時流來檢測威脅*微軟文檔
description: 本文介紹如何在 Azure Sentinel 中使用尋線即時流來跟蹤資料。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2019
ms.author: yelevin
ms.openlocfilehash: b392644e504fa8187e637278bef8718c9c2caa3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77582121"
---
# <a name="use-hunting-livestream-in-azure-sentinel-to-detect-threats"></a>在 Azure 哨兵中使用搜索即時流來檢測威脅

> [!IMPORTANT]
> Azure Sentinel 中的尋線直播當前處於公共預覽階段，並逐步向租戶推出。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


使用搜索直播創建互動式會話，這些會話允許您在事件發生時測試新創建的查詢，在找到匹配時從會話中獲取通知，並在必要時啟動調查。 您可以使用任何日誌分析查詢快速建立即時流會話。

- **在事件發生時測試新創建的查詢**
    
    您可以測試和調整查詢，而不會與正在主動應用於事件的當前規則發生衝突。 確認這些新查詢按預期工作後，通過選擇將會話提升為警報的選項，可以輕鬆地將它們提升為自訂警報規則。

- **在威脅發生時收到通知**
    
    您可以將威脅資料摘要與聚合日誌資料進行比較，並在發生匹配時收到通知。 威脅資料摘要是持續的資料流程，與潛在或當前威脅相關，因此通知可能表示對您的組織的潛在威脅。 當您希望收到潛在問題通知而不保留自訂警報規則的開銷時，請創建即時流會話，而不是自訂警報規則。

- **啟動調查**
    
    如果存在涉及資產（如主機或使用者）的活動調查，則可以在日誌資料中查看該資產上的特定（或任何）活動。 活動發生時，可以通知您。


## <a name="create-a-livestream-session"></a>創建即時流會話

可以從現有尋線查詢創建即時流會話，也可以從頭開始創建會話。

1. 在 Azure 門戶中，導航到**哨兵** > **威脅管理** > **搜索**。

2. 要從尋線查詢創建即時流會話，請：
    
    1. 從 **"查詢"** 選項卡中，找到要使用的狩獵查詢。
    2. 按右鍵查詢並選擇"**添加到即時流**"。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure 哨兵搜索查詢創建即時流會話](./media/livestream/livestream-from-query.png)

3. 要從頭開始創建即時流會話，請： 
    
    1. 選擇 **"即時流**"選項卡
    2. 選擇**轉到直播**。
    
4. 在 **"即時流"** 窗格中：
    
    - 如果從查詢開始即時流，請查看查詢並進行任何更改。
    - 如果從頭開始即時流，請創建查詢。 

5. 從命令列**中選擇"播放**"。
    
    命令列下的狀態列指示直播會話是正在運行的還是暫停的。 在下面的示例中，會話正在運行：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure 哨兵搜索創建即時流會話](./media/livestream/livestream-session.png)

6. 從命令列中選擇 **"保存**"。
    
    除非選擇 **"暫停**"，否則會話將繼續運行，直到從 Azure 門戶登出。

## <a name="view-your-livestream-sessions"></a>查看直播會話

1. 在 Azure 門戶中，導航到**哨兵** > **威脅管理** > **搜索** > **即時流**選項卡。

2. 選擇要查看或編輯的即時流會話。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure 哨兵搜索查詢創建即時流會話](./media/livestream/livestream-tab.png)
    
    您選定的直播會話將打開，以便您播放、暫停、編輯等。

## <a name="receive-notifications-when-new-events-occur"></a>發生新事件時接收通知

由於新事件的即時流通知使用 Azure 門戶通知，因此每當使用 Azure 門戶時，都會看到這些通知。 例如：

![即時流的 Azure 門戶通知](./media/livestream/notification.png)

選擇通知以打開 **"即時流"** 窗格。
 
## <a name="elevate-a-livestream-session-to-an-alert"></a>將即時流會話提升為警報

您可以通過選擇 **"提升"以**從相關直播會話上的命令列發出警報來將直播會話提升到新警報：

> [!div class="mx-imgBorder"]
> ![將即時流會話提升為警報](./media/livestream/elevate-to-alert.png)

此操作將打開規則創建嚮導，該嚮導預填充了與即時流會話關聯的查詢。

## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何在 Azure Sentinel 中使用狩獵直播。 若要深入了解 Azure Sentinel，請參閱下列文章：

- [主動尋找威脅](hunting.md)
- [使用筆記本運行自動狩獵活動](notebooks.md)
