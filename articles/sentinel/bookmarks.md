---
title: 使用搜尋書簽進行 Azure Sentinel 中的資料調查
description: 本文說明如何使用 Azure Sentinel 搜尋書簽來追蹤資料。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77588683"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>使用 Azure Sentinel 在搜尋期間追蹤資料

威脅搜尋通常需要檢查記錄資料的山脈，以尋找惡意行為的辨識項。 在這個過程中，調查人員會在驗證潛在的假設以及瞭解洩漏的完整案例時，尋找他們想要記住、重新流覽和分析的事件。

搜尋 Azure Sentinel 中的書簽可協助您完成這項作業，方法是保留您在 **Azure Sentinel 記錄**檔中執行的查詢，以及您認為相關的查詢結果。 您也可以記錄內容觀察，並藉由新增附注和標記來參考您的調查結果。 您和您的小組成員可以看到已加入書簽的資料，以方便協同作業。

您可以隨時在 [**搜尋**] 窗格的 [**書簽**] 索引標籤上，重新流覽您的已加入書簽的資料。 您可以使用篩選和搜尋選項，快速尋找您目前調查的特定資料。 或者，您可以直接在 Log Analytics 工作區的 **HuntingBookmark** 資料表中，查看已加上書簽的資料。 例如：

> [!div class="mx-imgBorder"]
> ![view HuntingBookmark table](./media/bookmarks/bookmark-table.png)

從資料表中查看書簽可讓您使用其他資料來源篩選、摘要和加入書簽的資料，讓您輕鬆尋找 corroborating 的辨識項。

目前處於預覽狀態，如果您發現當您在記錄中搜尋時需要處理的東西，只要按幾下滑鼠，您就可以建立書簽並將其升階至事件，或是將書簽新增至現有的事件。 如需事件的詳細資訊，請參閱 [教學課程：使用 Azure Sentinel 調查事件](tutorial-investigate-cases.md)。 

此外，在預覽中，您可以按一下書簽詳細資料中的 [ **調查** ]，將已加入書簽的資料視覺化。 這會啟動調查體驗，您可以在其中使用互動式實體圖表和時程表來查看、調查和視覺化傳達您的結果。

## <a name="add-a-bookmark"></a>新增書籤

1. 在 Azure 入口網站中，流覽至**Sentinel**  >  **威脅管理**  >  **搜尋**，以執行可疑和異常行為的查詢。

2. 選取其中一個搜尋查詢，然後在 [搜尋查詢詳細資料] 中選取 [ **執行查詢**]。 

3. 選取 [ **視圖查詢結果**]。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋查看查詢結果](./media/bookmarks/new-processes-observed-example.png)
    
    此動作會在 [ **記錄** ] 窗格中開啟查詢結果。

4. 從記錄查詢結果清單中，使用核取方塊來選取一或多個資料列，其中包含您感興趣的資訊。

5. 選取 [ **加入書簽**]：
    
    > [!div class="mx-imgBorder"]
    > ![將搜尋書簽新增至查詢](./media/bookmarks/add-hunting-bookmark.png)

6. 在右側的 [ **加入書簽** ] 窗格中，選擇性地更新書簽名稱、新增標籤和附注，以協助您識別專案的相關內容。

7. 在 [ **查詢資訊** ] 區段中，使用下拉式方塊從 [ **帳戶**]、[ **主機**] 和 [ **IP 位址** ] 實體類型的查詢結果中解壓縮資訊。 此動作會將選取的實體類型對應至查詢結果中的特定資料行。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![對應搜尋書簽的實體類型](./media/bookmarks/map-entity-types-bookmark.png)
    
    若要在調查圖表中查看書簽 (目前為預覽) ，您必須至少對應一個屬於 **帳戶**、 **主機**或 **IP 位址**的實體類型。 

5. 按一下 [ **儲存** ] 以認可您的變更並新增書簽。 所有加入書簽的資料都會與其他調查人員共用，而且是共同作業調查體驗的第一步。

 
> [!NOTE]
> 每當此窗格從 Azure Sentinel 開啟時，記錄查詢結果都支援書簽。 例如，您可以從導覽列選取 **[一般**  >  **記錄**]，選取 [調查] 圖形中的 [事件連結]，或從 [事件 (的完整詳細資料中選取警示識別碼) 目前為預覽狀態。 當您從其他位置開啟 [ **記錄** ] 窗格（例如直接從 Azure 監視器）時，無法建立書簽。

## <a name="view-and-update-bookmarks"></a>查看和更新書簽 

1. 在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**]。 

2. 選取 [ **書簽** ] 索引標籤，以查看書簽清單。

3. 若要協助您尋找特定的書簽，請使用搜尋方塊或篩選選項。

4. 選取個別的書簽，並在右邊的詳細資料窗格中查看書簽詳細資料。

5. 視需要進行變更，這些會自動儲存。

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>探索調查圖表中的書簽

> [!IMPORTANT]
> 探索調查圖表和調查圖表本身的書簽目前處於公開預覽狀態。
> 這些功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**  >  **書簽**] 索引標籤，然後選取您要調查的書簽或書簽。

2. 在 [書簽詳細資料] 中，確定至少有一個實體對應。 例如，針對 **實體**，您會看到 **IP**、 **電腦**或 **帳戶**的專案。

3. 按一下 [ **調查** ] 以查看調查圖形中的書簽。

如需使用調查圖表的指示，請參閱 [使用調查圖表深入探討](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)。

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>將書簽新增至新的或現有的事件

> [!IMPORTANT]
> 將書簽新增至新的或現有的事件目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**  >  **書簽**] 索引標籤，然後選取您要新增至事件的書簽或書簽。

2. 從命令列選取 [ **事件動作 (預覽) ** ：
    
    > [!div class="mx-imgBorder"]
    > ![將書簽新增至事件](./media/bookmarks/incident-actions.png)

3. 視需要選取 [ **建立新事件** ] 或 [ **新增至現有事件**]。 然後：
    
    - 針對新的事件：選擇性地更新事件的詳細資料，然後選取 [ **建立**]。
    - 若要將書簽新增至現有的事件：選取一個事件，然後選取 [ **新增**]。 

若要查看事件內的書簽：流覽至**Sentinel**  >  **威脅管理**  >  **事件**，並使用您的書簽選取事件。 選取 [ **View full details**]，然後選取 [ **書簽** ] 索引標籤。

> [!TIP]
> 除了命令列上 ** (預覽]) ** 選項之外，您還可以使用操作功能表 (**...**) 以選取選項來 **建立新的事件**、 **新增至現有的事件**，以及 **從事件中移除**。 

## <a name="view-bookmarked-data-in-logs"></a>在記錄中查看已加上書簽的資料

若要查看已加上書簽的查詢、結果或其歷程記錄，請從 [**搜尋**書簽] 索引標籤中選取書簽  >  **Bookmarks** ，然後使用詳細資料窗格中提供的連結： 

- **查看** [ **記錄** ] 窗格中的 [來源查詢]，以查看來源查詢。

- **查看書簽記錄** 以查看所有書簽中繼資料，包括進行更新的人員、更新的值，以及更新的發生時間。

您也可以在 [**搜尋**書簽] 索引標籤上，從命令列選取 [**書簽記錄**]，以查看所有書簽的原始書簽資料  >  **Bookmarks** ：

> [!div class="mx-imgBorder"]
> ![書簽記錄](./media/bookmarks/bookmark-logs.png)

這個視圖會顯示您所有具有相關聯中繼資料的書簽。 您可以使用 [關鍵字查詢語言](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) 查詢，以篩選出您要尋找的特定書簽的最新版本。

> [!NOTE]
> 當您建立書簽時，以及顯示在 [ **書簽** ] 索引標籤中的時間，可能會有明顯的延遲 (以分鐘為單位) 。

## <a name="delete-a-bookmark"></a>刪除書簽
 
1.  在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**  >  **書簽**] 索引標籤，然後選取您想要刪除的書簽或書簽。 

2. 以滑鼠右鍵按一下您的選取專案，然後選取要刪除書簽或書簽的選項。 例如，如果您只選取一個書簽，請 **刪除書簽** ，如果您選取兩個書簽，請刪除 **2 個書簽** 。
    
刪除書簽會從 [ **書簽** ] 索引標籤的清單中移除書簽。Log Analytics 工作區的 **HuntingBookmark** 資料表將會繼續包含先前的書簽專案，但是最新的專案會將 **SoftDelete** 值變更為 true，讓您輕鬆地篩選掉舊的書簽。 刪除書簽不會移除與其他書簽或警示相關之調查體驗中的任何實體。 


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用 Azure Sentinel 中的書簽來執行搜尋調查。 若要深入了解 Azure Sentinel，請參閱下列文章：


- [主動搜捕威脅](hunting.md)
- [使用筆記本來執行自動化搜尋活動](notebooks.md)
