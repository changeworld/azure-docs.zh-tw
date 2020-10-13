---
title: 使用 Azure Sentinel watchlists
description: 本文說明如何使用 Azure Sentinel watchlists 調查威脅、匯入商務資料、建立允許清單和擴充事件資料。
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 25252b73f25a96f85d5e2cf1d68b76f9eaa3ca75
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979469"
---
# <a name="use-azure-sentinel-watchlists"></a>使用 Azure Sentinel watchlists

Azure Sentinel watchlists 可讓您從外部資料源收集資料，以便與您 Azure Sentinel 環境中的事件相互關聯。 一旦建立之後，您就可以在搜尋、偵測規則、威脅搜尋和回應手冊中使用 watchlists。 Watchlists 會以名稱/值組的形式儲存在 Azure Sentinel 工作區中，並快取以獲得最佳查詢效能和低延遲。

使用 watchlists 的常見案例包括：

- 透過快速匯入 IP 位址、檔案雜湊和 CSV 檔案中的其他資料，快速**調查威脅**並回應事件。 匯入之後，您就可以在警示規則、威脅搜尋、活頁簿、筆記本和一般查詢中使用關注清單的名稱/值組來進行聯結和篩選。

- 將**商務資料匯入**為 watchlists。 例如，匯入具有特殊許可權的系統存取權的使用者清單或終止的員工，然後使用關注清單來建立允許和拒絕清單，用來偵測或防止這些使用者登入網路。

- **減少警示疲勞**。 建立允許清單來隱藏使用者群組中的警示，例如來自已授權 IP 位址的使用者，這些使用者會執行通常會觸發警示的工作，以及防止良性事件變成警示。

- 擴充**事件資料**。 使用 watchlists，透過衍生自外部資料源的名稱/值組合來擴充您的事件資料。

## <a name="create-a-new-watchlist"></a>建立新的關注清單

1. 在 Azure 入口網站中，流覽至 [ **Azure Sentinel**設定關注清單]，  >  **Configuration**  >  **Watchlist**然後選取 [**加入新**的]。

    > [!div class="mx-imgBorder"]
    > ![新關注清單](./media/watchlists/sentinel-watchlist-new.png)

1. 在 [ **一般** ] 頁面上，提供關注清單的 [名稱]、[描述] 和 [別名]，然後選取 **[下一步]**。

    > [!div class="mx-imgBorder"]
    > ![關注清單一般頁面](./media/watchlists/sentinel-watchlist-general.png)

1. 在 [ **來源** ] 頁面上，選取資料集類型、上傳檔案，然後選取 **[下一步]**。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="關注清單來源頁面" lightbox="./media/watchlists/sentinel-watchlist-source.png":::


1. 檢查資訊、確認資訊正確無誤，然後選取 [ **建立**]。

    > [!div class="mx-imgBorder"]
    > ![關注清單審核頁面](./media/watchlists/sentinel-watchlist-review.png)

    建立關注清單之後，就會出現通知。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="關注清單來源頁面" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="use-watchlists-in-queries"></a>在查詢中使用 watchlists

1. 在 Azure 入口網站中，流覽至 [ **Azure Sentinel**設定  >  **Configuration**  >  **關注清單**]，選取您要使用的關注清單，然後選取 [**在 Log Analytics 中查看**]。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="關注清單來源頁面" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::


1. 系統會自動為您的查詢解壓縮關注清單中的專案，而且會出現在 [ **結果** ] 索引標籤上。下列範例顯示 [ **ServerName** ] 和 [ **IpAddress** ] 欄位的解壓縮結果。

    > [!NOTE]
    > 查詢 UI 和排程警示中的時間戳記將會忽略。

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="關注清單來源頁面" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
## <a name="use-watchlists-in-analytics-rules"></a>在分析規則中使用 watchlists

若要在分析規則中使用 watchlists，請在 Azure 入口網站中，流覽至**Azure Sentinel**設定  >  **Configuration**  >  **分析**，並 `_GetWatchlist('<watchlist>')` 在查詢中使用函數建立規則。

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="關注清單來源頁面" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::


## <a name="view-list-of-watchlists-aliases"></a>查看 watchlists 別名的清單

若要取得關注清單別名的清單，請從 Azure 入口網站中，流覽至**Azure Sentinel**  >  **一般**  >  **記錄**檔，然後執行下列查詢： `_GetWatchlistAlias` 。 您可以在 [ **結果** ] 索引標籤中看到別名清單。

> [!div class="mx-imgBorder"]
> ![列出 watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何在 Azure Sentinel 中使用 watchlists 來擴充資料及改進調查。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats.md)。
- [使用活頁簿](tutorial-monitor-your-data.md)監視資料。

