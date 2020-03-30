---
title: 在 Azure 哨兵中使用狩獵書簽進行資料調查
description: 本文介紹如何使用 Azure Sentinel 搜索書簽來跟蹤資料。
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588683"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>使用 Azure 哨兵在搜索過程中跟蹤資料

威脅尋獵通常需要查看大量日誌資料，以查找惡意行為的證據。 在此過程中，調查人員會發現他們想要記住、重新審視和分析的事件，以驗證潛在的假設並瞭解妥協的全部故事。

Azure Sentinel 中的搜索書簽通過保留在**Azure Sentinel - Logs**中運行的查詢以及您認為相關的查詢結果來説明您做到這一點。 您還可以通過添加注釋和標記來記錄上下文觀測並引用您的發現。 您和您的團隊成員可以看到書簽資料，便於協作。

您可以隨時在 **"搜索**"窗格的 **"書簽**"選項卡上重新訪問書簽資料。 您可以使用篩選和搜索選項快速查找當前調查的特定資料。 或者，您也可以直接在日誌分析工作區中的**HuntingBook 標記**表中查看書簽資料。 例如：

> [!div class="mx-imgBorder"]
> ![查看亨廷頓Book標誌表](./media/bookmarks/bookmark-table.png)

通過查看表中的書簽，您可以篩選、匯總書簽資料並將其與其他資料來源聯接，從而輕鬆查找確鑿的證據。

當前在預覽版中，如果您在日誌中搜索時發現急需解決的問題，只需按一下幾下，即可創建書簽並將其升級為事件，或將書簽添加到現有事件。 有關事件的詳細資訊，請參閱[教程：使用 Azure Sentinel 調查事件](tutorial-investigate-cases.md)。 

此外，在預覽中，您可以通過按一下書簽詳細資訊中的 **"調查"** 來視覺化書簽資料。 這將啟動調查體驗，您可以在其中使用互動式實體圖和時間表查看、調查和直觀地傳達您的發現。

## <a name="add-a-bookmark"></a>新增書籤

1. 在 Azure 門戶中，導航到**哨兵** > **威脅管理** > **搜索**以運行可疑和異常行為的查詢。

2. 選擇其中一個狩獵查詢，在右側，在狩獵查詢詳細資訊中，選擇 **"執行查詢**"。 

3. 選擇 **"查看查詢結果**"。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![查看 Azure 哨兵搜索的查詢結果](./media/bookmarks/new-processes-observed-example.png)
    
    此操作將在 **"日誌"** 窗格中打開查詢結果。

4. 在日誌查詢結果清單中，使用核取方塊選擇一個或多個包含您感興趣的資訊的行。

5. 選擇 **"添加書簽**"
    
    > [!div class="mx-imgBorder"]
    > ![添加狩獵書簽進行查詢](./media/bookmarks/add-hunting-bookmark.png)

6. 在右側，在 **"添加書簽"** 窗格中，可以選擇更新書簽名稱、委任標記和注釋，以説明您識別有關該專案的有趣內容。

7. 在 **"查詢資訊**"部分中，使用下拉清單從**帳戶**、**主機**和**IP 位址**實體類型的查詢結果中提取資訊。 此操作將所選實體類型映射到查詢結果中的特定列。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![用於狩獵書簽的映射實體類型](./media/bookmarks/map-entity-types-bookmark.png)
    
    要查看調查圖中的書簽（當前處於預覽中），必須映射至少一個實體類型，即**帳戶**、**主機**或**IP 位址**。 

5. 按一下 **"保存**"以提交更改並添加書簽。 所有書簽資料都與其他調查人員共用，是邁向協作調查體驗的第一步。

 
> [!NOTE]
> 每當從 Azure Sentinel 打開此窗格時，日誌查詢結果都支援書簽。 例如，從巡覽列中選擇 **"常規** > **日誌**"，在調查圖中選擇事件連結，或從事件的完整詳細資訊中選擇警報 ID（當前處於預覽狀態）。 當從其他位置（如直接從 Azure 監視器）打開 **"日誌"** 窗格時，無法創建書簽。

## <a name="view-and-update-bookmarks"></a>查看和更新書簽 

1. 在 Azure 門戶中，導航到**哨兵** > **威脅管理** > **搜索**。 

2. 選擇 **"書簽**"選項卡以查看書簽清單。

3. 要説明您查找特定書簽，請使用搜索框或篩選器選項。

4. 選擇單個書簽，並在右側詳細資訊窗格中查看書簽詳細資訊。

5. 根據需要進行更改，這些更改將自動儲存。

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>探索調查圖中的書簽

> [!IMPORTANT]
> 探索調查圖和調查圖本身中的書簽，目前正在公開預覽。
> 這些功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 門戶中，導航到**哨兵** > **威脅管理** > **狩獵** > **書簽**選項卡，然後選擇要調查的書簽或書簽。

2. 在書簽詳細資訊中，確保至少映射一個實體。 例如，對於**實體**，您將看到**IP、****電腦**或**帳戶**的條目。

3. 按一下 **"調查**"以查看調查圖中的書簽。

有關使用調查圖的說明，請參閱[使用調查圖進行深度潛水](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)。

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>將書簽添加到新事件或現有事件

> [!IMPORTANT]
> 向新或現有事件添加書簽當前處於公共預覽版中。
> 此功能在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 門戶中，導航到**哨兵** > **威脅管理** > **狩獵** > **書簽**選項卡，然後選擇要添加到事件的書簽或書簽。

2. 從命令列中選擇**事件操作（預覽）：**
    
    > [!div class="mx-imgBorder"]
    > ![向事件添加書簽](./media/bookmarks/incident-actions.png)

3. 根據需要選擇 **"創建新事件**"或 **"添加到現有事件**"。 然後：
    
    - 對於新事件：可選更新事件的詳細資訊，然後選擇 **"創建**"。
    - 要向現有事件添加書簽：選擇一個事件，然後選擇"**添加**"。 

要查看事件中的書簽：導航到**哨兵** > **威脅管理** > **事件**，然後使用書簽選擇事件。 選擇 **"查看完整詳細資訊**"，然後選擇 **"書簽**"選項卡。

> [!TIP]
> 作為命令列上 **"事件操作（預覽）"** 選項的替代方法，您可以使用內容功能表 **（...）** 對一個或多個書簽選擇選項以**創建新事件**、**添加到現有事件**以及**從事件中刪除**。 

## <a name="view-bookmarked-data-in-logs"></a>在日誌中查看書簽資料

要查看書簽查詢、結果或其歷史記錄，請從 **"狩獵** > **書簽"** 選項卡中選擇書簽，並使用詳細資訊窗格中提供的連結： 

- **查看源查詢**以查看 **"日誌"** 窗格中的源查詢。

- **查看書簽日誌**以查看所有書簽中繼資料，其中包括誰進行了更新、更新的值以及更新發生的時間。

您還可以通過從 **"狩獵** > **書簽**"選項卡上的命令列中選擇**書簽日誌**來查看所有書簽的原始書簽資料：

> [!div class="mx-imgBorder"]
> ![書簽日誌](./media/bookmarks/bookmark-logs.png)

此視圖顯示具有關聯中繼資料的所有書簽。 您可以使用[關鍵字查詢語言](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference)（KQL） 查詢篩選到要查找的特定書簽的最新版本。

> [!NOTE]
> 在創建書簽和在 **"書簽"** 選項卡中顯示書簽之間，可能會有顯著的延遲（以分鐘為單位）。

## <a name="delete-a-bookmark"></a>刪除書簽
 
1.  在 Azure 門戶中，導航到**哨兵** > **威脅管理** > **狩獵** > **書簽**選項卡，然後選擇要刪除的書簽或書簽。 

2. 按右鍵您的選擇，然後選擇刪除書簽或書簽的選項。 例如，如果只選擇了一個書簽，**則刪除書簽**;如果選擇了兩個書簽，則**刪除 2 個書簽**。
    
刪除書簽會從 **"書簽"** 選項卡中清單中刪除書簽。日誌分析工作區的**HuntingBook 標記**表將繼續包含以前的書簽條目，但最新的條目將**SoftDelete**值更改為 true，從而輕鬆篩選出舊書簽。 刪除書簽不會從與其他書簽或警報關聯的調查體驗中刪除任何實體。 


## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何使用 Azure Sentinel 中的書簽運行狩獵調查。 若要深入了解 Azure Sentinel，請參閱下列文章：


- [主動尋找威脅](hunting.md)
- [使用筆記本運行自動狩獵活動](notebooks.md)
