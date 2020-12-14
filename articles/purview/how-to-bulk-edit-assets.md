---
title: 如何將多個詞彙標記標記為選取的資產清單
description: 瞭解 Azure 範疇中的大量編輯資產。
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: c42a6894c33993dc9aee5a9fdd10b1c3a3627320
ms.sourcegitcommit: 287c20509c4cf21d20eea4619bbef0746a5cd46e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/14/2020
ms.locfileid: "97372109"
---
# <a name="how-to-bulk-edit-assets-to-tag-glossary-terms"></a>如何大量編輯資產以標記詞彙

本文說明如何在單一動作中，將多個詞彙條款標記為選取的資產清單。

### <a name="add-assets-to-view-selected-list-using-search"></a>使用搜尋新增資產以查看選取的清單

1. 搜尋您想要新增到清單中的資料資產，以進行大量編輯。

2. 在 [搜尋結果] 頁面中，將滑鼠停留在您想要新增至 [大量編輯 **視圖] 選取** 清單中的資產上，以查看核取方塊。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="核取方塊的螢幕擷取畫面。":::

3. 選取核取方塊，將它加入至 [大量編輯 **視圖] 選取** 的清單。 新增之後，您會看到頁面底部的 [選取的專案] 圖示。

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="清單的螢幕擷取畫面。":::

4. 重複上述步驟，將所有資料資產新增至清單。

### <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>新增資產以從資產詳細資料頁面查看選取的清單

1. 在 [資產詳細資料] 頁面中，選取右上角的核取方塊，將資產新增至 [大量編輯 **視圖] 選取** 的清單。

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="資產的螢幕擷取畫面。":::

### <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>大量編輯 [View selected] 清單中的資產，以新增、取代或移除詞彙。

1. 您已完成所有需要進行大量編輯之資料資產的識別，請從 [搜尋結果] 頁面或 [資產詳細資料] 頁面選取 [ **查看選取** 的清單]。

:::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="視圖的螢幕擷取畫面。":::

2. 檢查清單，如果您想要移除任何條款，請選取 [ **移除** ]。

:::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="移除的螢幕擷取畫面。":::

3. 選取 [大量編輯]，以新增、移除或取代所有選定資產的詞彙條款。

4. 若要加入詞彙，請選取 [作業為 **新增**]。 選取您要在新值中新增的所有詞彙字詞。 選取 [完成時套用]。
    - 新增作業會將新值附加至已標記為數據資產的詞彙清單。  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="[新增] 的螢幕擷取畫面。":::

5. 若要取代詞彙詞彙，請選取 [ **取代** 為]。 選取您要在新值中取代的所有詞彙字詞。 選取 [完成時套用]。
    - 取代作業將會以 [新值] 中選取的詞彙取代所選資料資產的所有詞彙條款。
   
6. 若要移除詞彙條款，請選取 [ **移除**]。 選取 [完成時套用]。
    - 移除操作將會移除所選資料資產的所有詞彙條款。
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="移除條款的螢幕擷取畫面。":::

7. 完成之後，請選取 [ **關閉** ] 或 [ **全部移除]，然後** 關閉 [大量編輯] 分頁。 [關閉] 將不會移除選取的資產，而 [全部移除] 和 [關閉] 將會移除所有選取的資產。
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="關閉的螢幕擷取畫面。":::

   > [!Important]
   > 大量編輯的建議資產數量為15。 選取超過15個可能會導致效能問題。
   > 只有在至少選取一個資產時，才會顯示 [ **選取的視圖** ] 方塊。


遵循 [教學課程：建立和匯入詞彙](how-to-create-import-export-glossary.md) ，以深入瞭解。
