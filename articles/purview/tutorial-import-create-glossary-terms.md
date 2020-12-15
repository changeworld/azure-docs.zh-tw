---
title: 教學課程：在 Azure Purview (預覽) 中建立和匯入字彙
description: 本教學課程說明如何建立字彙、將字彙新增至資產，以及匯入字彙。
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 3a81d9480dfb07703d5f1ccfb495d069f140dd71
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549586"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>教學課程：在 Azure Purview (預覽) 中建立和匯入字彙

> [!IMPORTANT]
> Azure Purview 目前為預覽狀態。 [Microsoft Azure 預覽版增補使用規定](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含適用於 Azure 功能 (搶鮮版 (Beta)、預覽版，或尚未正式發行的版本) 的其他法律條款。

詞彙是維護和組織目錄的重要工具。 您可以定義新的字詞或匯入字詞清單，然後將這些字詞套用至您的資產，以建立您的詞彙。

本教學課程是「五部分教學課程系列的第 5 部分」，您將大致了解如何使用 Azure Purview 來管理資料資產中的資料控管。 本教學課程建基於您在[第 4 部分：探索 Azure Purview (預覽) 中的資源集、詳細資料、結構描述和分類](tutorial-schemas-and-classifications.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 建立字彙。
> * 將字彙新增至資產。
> * 匯入字彙。

## <a name="prerequisites"></a>必要條件

* 完成[教學課程：探索 Azure Purview (預覽) 中的資源集、詳細資料、結構描述和分類](tutorial-schemas-and-classifications.md)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-glossary-terms"></a>建立字彙

您可以在詞彙中建立商務或技術字詞。 您也可以將字詞套用至資產，而加以標註。

以下摘錄了 [字彙] 頁面上最常見的一些欄位：

* **狀態**：將狀態指派給字詞 (**草稿**、**已核准**、**已過期** 或 **警示**)。

* **定義**：輸入字詞的定義。

* **縮寫字**：使用每個單字的初始字母，輸入字詞的縮寫版本。

* **同義字**：選取具有相同或類似定義的其他字詞。

* **相關字詞**：在詞彙中選取與此字詞相關、但有不同定義的其他字詞。 範例包括與商務字詞、程式碼名稱相關的技術字詞，或應與字詞相關聯的其他字詞。

請依照下列步驟來建立字詞：

1. 在 Azure 入口網站中瀏覽至您的 Azure Purview 資源，然後選取 [開啟 Purview Studio]。 您會自動進入 Purview Studio 的首頁。

1. 從左窗格中選取 [詞彙]，以開啟 [字彙] 頁面。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="顯示 [字彙] 頁面的螢幕擷取畫面。":::

1. 選取 [新增字詞] > [系統預設] > [繼續]。

1. 在 [新增字詞] 頁面的 [概觀] 索引標籤上，輸入新字詞的 [名稱]：*財務*。

1. 輸入 [定義]：*此字詞代表 Contoso Inc 的財務資訊。*

1. 在 [狀態] 下拉式清單中，選取 [已核准]。

1. 完成之後，選取 [建立]。

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="顯示如何建立新字彙的螢幕擷取畫面。":::

## <a name="add-glossary-terms-to-an-asset"></a>將字彙新增至資產

1. 使用您在[本教學課程系列的第 1 部分](tutorial-scan-data.md)中學習到的步驟，來尋找資產。 例如 **Contoso_CashFlow_{N}.csv**。

1. 在資產詳細資料頁面上，選取 [編輯]。

1. 在 [概觀] 索引標籤的 [字彙] 下拉式清單中選取 [財務]，然後選取 [儲存]。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="顯示如何將字彙新增至資產的螢幕擷取畫面。":::

1. 移至 [概觀] 索引標籤上的 [字彙] 區段，您會發現「財務」一詞已套用至資產。

## <a name="import-glossary-terms"></a>匯入字彙

若要匯入字詞或大量更新現有的字詞，請將預先填入的範本上傳至您的詞彙。

在此程序中，您會透過 .csv 檔案匯入字彙：

1. 記下您儲存檔案 *StarterKitTerms.csv* 的位置，此檔案包含在您於[本教學課程系列的第 1 部分](tutorial-scan-data.md)下載的入門套件中。

   此檔案包含已預先填入、且與您的資料資產相關的字詞清單。

1. 若要開始匯入，請選取 [詞彙]，然後選取 [匯入字詞]。

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="顯示如何匯入字彙的螢幕擷取畫面。":::

1. 在 [匯入字詞] 頁面上選取 [系統預設]，然後選取 [繼續]。

1. 選取 [瀏覽]，移至您下載 *StarterKitTerms.csv* 的位置，然後選取 [開啟]。

1. 選取 [確定] 以開始匯入字詞。

   匯入完成後，新的字彙會顯示在 [字彙] 頁面上。

1. 檢視每個新匯入的字詞，以查看其定義的情形。

## <a name="create-custom-term-templates"></a>建立自訂字詞範本

在本節中，您將了解如何使用自訂屬性建立自訂字詞範本，以及如何使用範本 csv 檔案匯入資料。

目前有數個現有的系統字詞範本，選取 [詞彙] > [管理字詞範本] > [系統] 即可加以檢視。

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="系統字詞範本。":::

若要建立新的自訂字詞範本，請執行下列步驟：

1. 從左側功能表中選取 [詞彙]。
1. 選取 [管理字詞範本] > [自訂] > [新增字詞範本]

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="建立新的自訂字詞範本。":::

在 [新增字詞範本] 畫面上，執行下列步驟：

1. 輸入 [範本名稱]：`Sensitivity level`。
1. 輸入您要的說明，例如 `Indicates the level of sensitivity for this data.`
1. 選取 [+ 新增屬性]，以開啟新增屬性的對話方塊。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="新增字詞範本畫面隨即啟動。":::

1. 在 [新增屬性] 畫面上，輸入下列參數：

   |設定|建議的值|
   |---------|-----------|
   |屬性名稱|是敏感資訊|
   |欄位類型|下拉式清單|單選|
   |標示為必要|核取此方塊。|
   |+ 新增選項| 新增兩個選項。 [是] 和 [否]。|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="新增屬性。":::

1. 重複上述步驟，新增名稱為 `can be shared externally` 的另一個屬性。 在新增這兩個屬性後，選取 [建立] 完成操作。

## <a name="import-terms-from-a-template"></a>從範本匯入字詞

接下來，使用您所建立的 [敏感度層級] 範本將新字詞匯入。 

1. 從 [字彙] 畫面中，選取 [匯入字詞]。

1. 在 [匯入字詞] 畫面中，選取 [敏感度層級]。 選取 [繼續]。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="選取敏感度層級。":::

1. **敏感度層級** 的字詞範本包含預設系統屬性，以及您已新增的新屬性：`can be shared externally` 和 `is sensitive information`。 選取 [下載範例 .CSV] 檔案。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="下載範例 csv 檔案。":::

1. 系統會下載範本檔案，供您編輯和上傳具有客戶屬性的新字彙。 開啟您已下載的 CSV 檔案。 在 CSV 檔案中，將新的字詞及其適當的值新增為新的資料列。

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="在 csv 檔案中建立字詞。":::

   [相關字詞] 或 [同義字] 資料行中若有任何列出的字詞尚不存在，將會在檔案上傳時建立。 這些字詞會使用 **系統預設** 範本來建立。

1. 若要上傳檔案，請回到 [匯入字詞] 畫面，然後選取 [選取要上傳的完整 .CSV 檔案] 方塊旁的 [瀏覽]。 在開啟的對話方塊中選取您的檔案，然後選取 [確定]。

1. 新的字詞此時會列在 [字彙] 畫面上。 您可以按一下清單中的字詞名稱，以檢視關於新字詞的詳細資料。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
>
> * 建立字彙。
> * 將字彙新增至資產。
> * 匯入字彙。

請前往下一篇文章，以了解不同的目錄深入解析。

> [!div class="nextstepaction"]
> [了解 Azure Purview 中的深入解析](concept-insights.md)
