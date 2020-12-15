---
title: 教學課程：探索 Azure Purview (預覽) 中的資源集、詳細資料、結構描述和分類
description: 本教學課程說明如何使用資源集、資產詳細資料、結構描述和分類。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c39ed746630c646b0ce305a9535ae6d610f5161c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549568"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>教學課程：探索 Azure Purview (預覽) 中的資源集、詳細資料、結構描述和分類

> [!IMPORTANT]
> Azure Purview 目前為預覽狀態。 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含適用於 Azure 功能 (搶鮮版 (Beta)、預覽版，或尚未正式發行的版本) 的其他法律條款。

在本教學課程中，您將探索目錄的主要元件：資源集、資產詳細資料、結構描述和分類。

本教學課程是「五部分教學課程系列的第 4 部分」，您將大致了解如何使用 Azure Purview 來管理資料資產中的資料控管。 本教學課程建基於您在[第 3 部分：瀏覽 Azure Purview (預覽) 中的資產並檢視其譜系](tutorial-browse-and-view-lineage.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 檢視資源集。
> * 檢視資產詳細資料。
> * 編輯結構描述及新增分類。

## <a name="prerequisites"></a>必要條件

* 完成[教學課程：瀏覽 Azure Purview (預覽) 中的資產並檢視其譜系](tutorial-browse-and-view-lineage.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="view-resource-sets"></a>檢視資源集

資源集是目錄中的單一物件，代表儲存體中的許多實體物件。 這些物件通常會共用一個通用的結構描述，大部分情況下是命名慣例或資料夾結構。 例如，日期格式為 *yyyy/mm/dd*。 如需資源集的詳細資訊，請參閱[了解資源集](concept-resource-sets.md)。

1. 在 Azure 入口網站中瀏覽至您的 Azure Purview 資源，然後選取 [開啟 Purview Studio]。 您會自動進入 Purview Studio 的首頁。

2. 在 [搜尋資產] 方塊中輸入 **contoso_staging_positivecashflow_ n**，然後從搜尋結果中選取 **Contoso_Staging_PositiveCashFlow_{N}.csv**。

## <a name="view-asset-details"></a>檢視資產詳細資料

1. [概觀] 索引標籤會顯示 [說明]、[字彙] 和 [屬性]，例如 **qualifiedName**。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="此螢幕擷取畫面顯示資源集資產頁面的 [概觀] 索引標籤。":::

1. 在 [屬性] 底下，記下以下兩個欄位的內容：

   * **partitionCount**：指出與此資源集相關聯的實體檔案數目。
   * **schemaCount**：指出在此資源集內找到的結構描述變化數目。

   當您完成[本教學課程系列的第 1 部分](tutorial-scan-data.md)中的掃描之後，Azure Purview 會在 24 小時內填入這些屬性。

1. 選取 [連絡人] 索引標籤，以檢閱 [專家] 和 [擁有者] 值。

## <a name="edit-the-schema-and-add-classifications"></a>編輯結構描述及新增分類

1. 選取 [結構描述] 索引標籤。觀察資料行名稱及其相關聯的分類。 請注意，掃描會自動填入屬性。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="顯示 [結構描述] 索引標籤的螢幕擷取畫面。":::

1. 若要編輯資產，請選取左上方的 [編輯] 按鈕。 然後，選取 [結構描述] 索引標籤。

1. 新增資料行的 [說明]，或將資料行重新命名為更好記的名稱。

1. 在資產層級新增分類，針對沒有設定分類的資料行名稱選取 [資料行層級分類] 下拉式清單。

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="結構描述編輯頁面":::

1. 在完成變更後，選取 [儲存]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 檢視資源集。
> * 檢視資產詳細資料。
> * 編輯結構描述及新增分類。

繼續進行下一個教學課程，以了解詞彙以及如何定義和匯入資產的新字詞。

> [!div class="nextstepaction"]
> [建立和匯入字彙](tutorial-import-create-glossary-terms.md)