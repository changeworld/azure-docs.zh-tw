---
title: 如何管理商務詞彙的詞彙範本
description: 瞭解如何在 Azure 範疇資料目錄中管理商務詞彙的詞彙範本。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552340"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>如何管理商務詞彙的詞彙範本

Azure 範疇可讓您建立重要的詞彙，以豐富資料。 每個新增至範疇資料目錄詞彙的新詞彙都是以判斷詞彙欄位的詞彙範本為基礎。 本文說明如何建立可與詞彙相關聯的詞彙範本和自訂屬性。

## <a name="manage-term-templates-and-custom-attributes"></a>管理字詞範本和自訂屬性

使用詞彙範本，您可以建立自訂屬性、將它們群組在一起，並在建立詞彙時套用範本。 一旦建立詞彙之後，就無法變更與該詞彙相關聯的範本。

1. 在 [ **詞彙條款** ] 頁面上，選取 [ **管理詞彙範本**]。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="詞彙範本 > 管理詞彙範本按鈕的螢幕擷取畫面。":::

2. 此頁面會顯示系統和自訂屬性。 選取 [ **自訂** ] 索引標籤，以建立或編輯字詞範本。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="詞彙範本 > 管理詞彙範本] 頁面的螢幕擷取畫面。":::

3. 選取 [ **+ 新增字詞範本** ]，然後輸入範本名稱和描述。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="詞彙範本 > 管理詞彙範本 > 新詞彙範本的螢幕擷取畫面":::

4. 選取 [ **+ 新增屬性** ]，為字詞範本建立新的自訂屬性。 輸入屬性名稱、描述。 自訂屬性名稱在詞彙範本內必須是唯一的，但是可以在範本之間重複使用相同的名稱。

   從選項 **文字**、 **單一選項**、 **多重選取** 或  **日期** 的選項清單中選擇欄位類型。 您也可以提供文字欄位類型的預設值字串。  也可以將屬性標示為 **必要**。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="詞彙 > 新屬性頁面的螢幕擷取畫面。":::

5. 建立所有自訂屬性之後，請選取 [ **預覽** ] 來排列自訂屬性的順序。 您可以將自訂屬性拖放到所需的順序。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="詞彙範本 > 預覽詞彙範本的螢幕擷取畫面。":::

6. 定義所有自訂屬性之後，請選取 [ **建立** ] 以建立具有自訂屬性的詞彙範本。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="詞彙範本 > 新的詞彙範本-建立按鈕的螢幕擷取畫面。":::

7. 核取 [已 **過期**]，即可將現有的自訂屬性標示為已過期。 一旦過期，就無法重新開機屬性。 過期的屬性將會呈現為現有詞彙的灰色。 未來以此詞彙範本建立的新詞彙將不會再顯示已標示為過期的屬性。

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="詞彙的螢幕擷取畫面 > 編輯屬性以將其標示為已過期。":::

## <a name="next-steps"></a>後續步驟

遵循 [教學課程：建立和匯入詞彙](tutorial-import-create-glossary-terms.md) ，以深入瞭解。
