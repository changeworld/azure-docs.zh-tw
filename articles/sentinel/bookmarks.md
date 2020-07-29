---
title: 在 Azure Sentinel 中使用搜尋書簽進行資料調查
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588683"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>使用 Azure Sentinel 在搜尋期間追蹤資料

威脅搜尋通常需要檢查山脈的記錄資料，尋找是否有惡意行為的證明。 在此過程中，調查者會尋找他們想要記住、重新流覽和分析的事件，以驗證可能的假設，並瞭解危害的完整案例。

在 Azure Sentinel 中搜尋書簽可協助您完成這項工作，方法是保留您在**Azure Sentinel 記錄**檔中執行的查詢，以及您認為相關的查詢結果。 您也可以藉由新增附注和標籤來記錄內容相關的觀察，並參考您的結果。 您和您的小組可以看到已加入書簽的資料，以方便共同作業。

您可以隨時在 [**搜尋**] 窗格的 [**書簽**] 索引標籤上，重新流覽已加入書簽的資料。 您可以使用篩選和搜尋選項，快速找出您目前調查的特定資料。 或者，您也可以直接在 Log Analytics 工作區的**HuntingBookmark**資料表中，查看已加入書簽的資料。 例如：

> [!div class="mx-imgBorder"]
> ![view HuntingBookmark 資料表](./media/bookmarks/bookmark-table.png)

從資料表中查看書簽可讓您篩選、匯總和聯結具有其他資料來源的書簽資料，讓您輕鬆尋找 corroborating 辨識項。

目前為預覽狀態，如果您發現在搜尋記錄時，迫切需要解決的事情，只要按幾下滑鼠，您就可以建立書簽並將其升級為事件，或將書簽新增至現有的事件。 如需有關事件的詳細資訊，請參閱[教學課程：使用 Azure Sentinel 調查事件](tutorial-investigate-cases.md)。 

此外，在預覽中，您可以從書簽詳細資料按一下 [**調查**]，將已加入書簽的資料視覺化。 這會啟動調查體驗，讓您可以使用互動式實體圖表和時程表來查看、調查和視覺化您的結果。

## <a name="add-a-bookmark"></a>新增書籤

1. 在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**] 來執行可疑和異常行為的查詢。

2. 選取其中一個搜尋查詢，然後在右側的 [搜尋查詢詳細資料] 中，選取 [**執行查詢**]。 

3. 選取 [**查看查詢結果**]。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![從 Azure Sentinel 搜尋中查看查詢結果](./media/bookmarks/new-processes-observed-example.png)
    
    此動作會在 [**記錄**] 窗格中開啟查詢結果。

4. 從 [記錄查詢結果] 清單中，使用核取方塊來選取包含您感興趣之資訊的一或多個資料列。

5. 選取 [**新增書簽**]：
    
    > [!div class="mx-imgBorder"]
    > ![將搜尋書簽新增至查詢](./media/bookmarks/add-hunting-bookmark.png)

6. 在右側的 [**加入書簽**] 窗格中，選擇性地更新書簽名稱、加入標記和附注，以協助您識別專案的相關內容。

7. 在 [**查詢資訊**] 區段中，使用下拉式方塊，從 [**帳戶**]、[**主機**] 和 [ **IP 位址**] 實體類型的查詢結果中，提取資訊。 此動作會將選取的實體類型對應至查詢結果中的特定資料行。 例如：
    
    > [!div class="mx-imgBorder"]
    > ![搜尋書簽的對應實體類型](./media/bookmarks/map-entity-types-bookmark.png)
    
    若要在調查圖表中查看書簽（目前處於預覽狀態），您必須至少對應一個實體類型，也就是**帳戶**、**主機**或**IP 位址**。 

5. 按一下 [**儲存**] 以認可您的變更並新增書簽。 所有書簽的資料會與其他調查人員共用，而且是共同作業調查體驗的第一個步驟。

 
> [!NOTE]
> 每當從 Azure Sentinel 開啟此窗格時，記錄查詢結果就會支援書簽。 例如，您可以從導覽列選取 **[一般**  >  **記錄**]、選取 [調查] 圖表中的 [事件連結]，或從事件的完整詳細資料（目前處於預覽狀態）選取警示識別碼。 當 [**記錄**] 窗格從其他位置（例如直接從 Azure 監視器）開啟時，您無法建立書簽。

## <a name="view-and-update-bookmarks"></a>查看和更新書簽 

1. 在 [Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  **搜尋**]。 

2. 選取 [**書簽**] 索引標籤，以查看書簽清單。

3. 若要協助您尋找特定的書簽，請使用 [搜尋] 方塊或 [篩選] 選項。

4. 選取 [個別書簽]，然後在右側的詳細資料窗格中，查看書簽詳細資料。

5. 視需要進行變更，這會自動儲存。

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>探索調查圖形中的書簽

> [!IMPORTANT]
> 探索調查圖表中的書簽和調查圖表本身，目前處於公開預覽狀態。
> 這些功能會在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 [Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  ] [**搜尋**  >  **書簽**] 索引標籤，然後選取您要調查的書簽或書簽。

2. 在書簽詳細資料中，確定至少有一個實體對應。 例如，針對**實體**，您會看到 [ **IP**]、[**電腦**] 或 [**帳戶**] 的專案。

3. 按一下 [**調查**] 以在調查圖表中查看書簽。

如需使用調查圖表的指示，請參閱[使用調查圖表深入探討](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)。

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>將書簽新增至新的或現有的事件

> [!IMPORTANT]
> 將書簽新增至新的或現有的事件目前處於公開預覽狀態。
> 這項功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

1. 在 [Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  ] [**搜尋**  >  **書簽**] 索引標籤，然後選取您要新增至事件的書簽或書簽。

2. 從命令列選取 [**事件動作（預覽）** ]：
    
    > [!div class="mx-imgBorder"]
    > ![將書簽新增至事件](./media/bookmarks/incident-actions.png)

3. 視需要選取 [**建立新事件**] 或 [**新增至現有事件**]。 然後：
    
    - 針對新的事件：選擇性地更新事件的詳細資料，然後選取 [**建立**]。
    - 若要將書簽新增至現有的事件：選取一個事件，然後選取 [**新增**]。 

若要在事件內查看書簽：流覽至 [ **Sentinel**  >  **威脅管理**  >  **事件**]，然後選取含有書簽的事件。 選取 [ **View full details**]，然後選取 [**書簽**] 索引標籤。

> [!TIP]
> 除了命令列上的 [**事件動作（預覽）** ] 選項之外，您還可以使用一或多個書簽的操作功能表（**...**）來選取**建立新事件**、新增**至現有事件**，以及**從事件中移除**的選項。 

## <a name="view-bookmarked-data-in-logs"></a>在記錄中查看已標記的資料

若要查看書簽查詢、結果或其歷程記錄，請從 [**搜尋**書簽] 索引標籤中選取書簽  >  **Bookmarks** ，然後使用 [詳細資料] 窗格中提供的連結： 

- **View source query**以在 [**記錄**] 窗格中查看來源查詢。

- **查看書簽記錄**以查看所有書簽中繼資料，包括進行更新的人員、更新的值，以及更新發生的時間。

您也可以在 [**搜尋**書簽] 索引標籤上，從命令列選取 [**書簽記錄**]，以查看所有書簽的原始書簽資料  >  **Bookmarks** ：

> [!div class="mx-imgBorder"]
> ![書簽記錄](./media/bookmarks/bookmark-logs.png)

此視圖會顯示所有具有相關聯中繼資料的書簽。 您可以使用[關鍵字查詢語言](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference)（KQL）查詢，篩選出您要尋找的特定書簽的最新版本。

> [!NOTE]
> 當您建立書簽時，以及在 [**書簽**] 索引標籤中顯示時，可能會有相當大的延遲（以分鐘為單位）。

## <a name="delete-a-bookmark"></a>刪除書簽
 
1.  在 Azure 入口網站中，流覽至 [ **Sentinel**  >  **威脅管理**  >  ] [**搜尋**  >  **書簽**] 索引標籤，然後選取您想要刪除的書簽或書簽。 

2. 以滑鼠右鍵按一下您的選取專案，然後選取刪除書簽或書簽的選項。 例如，如果您只選取一個書簽，則**刪除書簽**，如果您選取兩個書簽，則**刪除2個書簽**。
    
刪除書簽會從 [**書簽**] 索引標籤中的清單移除書簽。Log Analytics 工作區的**HuntingBookmark**資料表會繼續包含先前的書簽專案，但最新的專案會將**SoftDelete**值變更為 true，讓您輕鬆地篩選掉舊的書簽。 刪除書簽並不會移除與其他書簽或警示相關聯之調查體驗中的任何實體。 


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何在 Azure Sentinel 中使用書簽來執行搜尋調查。 若要深入了解 Azure Sentinel，請參閱下列文章：


- [主動搜捕威脅](hunting.md)
- [使用筆記本來執行自動化的搜尋活動](notebooks.md)
