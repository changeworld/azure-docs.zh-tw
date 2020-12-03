---
title: 如何：搜尋資料目錄
description: 本文概述如何搜尋資料目錄。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552025"
---
# <a name="search-the-azure-purview-data-catalog"></a>搜尋 Azure 範疇資料目錄

本文說明如何使用 Azure 範疇資料目錄中的各種搜尋功能。

## <a name="search-the-catalog-for-assets"></a>搜尋資產的目錄

進行資產搜尋的步驟如下：

1. 選取 [**搜尋目錄** [] 以開啟 [資產搜尋] 對話方塊](#open-the-asset-search-dialog)。
1. [輸入搜尋詞彙](#enter-search-terms) ，以尋找具有符合條款之特性的資產。
1. [設定快速篩選](#set-quick-filters) 以縮小搜尋範圍。
1. [開始搜尋](#start-the-search) 並移至搜尋結果。

無論您是在輸入搜尋字詞之前或之後設定快速篩選，都不重要。

如果沒有搜尋字詞且沒有任何篩選準則，則搜尋結果會包含所有資產。

### <a name="open-the-asset-search-dialog"></a>開啟資產搜尋對話方塊

選取 [ **搜尋目錄**] 以開啟 [資產搜尋] 對話方塊。

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="在 [搜尋目錄] 下方，有一個包含搜尋篩選的左窗格，以及一個使用最新搜尋的右窗格。" border="true":::

[搜尋] 對話方塊會顯示快速篩選、搜尋歷程記錄和最近存取的資產清單。

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="搜尋清單位於 [搜尋目錄] 下方的右窗格中。" border="true":::

### <a name="enter-search-terms"></a>輸入搜尋詞彙

在 **搜尋目錄** 中輸入一或多個搜尋字詞。 當您輸入時，最新搜尋中的比對搜尋詞彙會列在 **最近的搜尋** 中，建議的相符搜尋詞彙會列在 **搜尋建議** 中，而相符的資料資產會列在 **資產建議** 中。

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="顯示在 [搜尋目錄] 方塊中輸入搜尋結果的螢幕擷取畫面":::

搜尋結果只包含具有一或多個符合搜尋字詞之特性的資產。 這些特性包括資產名稱、資產類型、分類與連絡人。

#### <a name="types-of-search-criteria"></a>搜尋準則的類型

Azure 範疇支援下列類型的搜尋準則。

> [!Note]
> 一律指定布林運算子 (**AND**、 **OR**、 **NOT**) 全部大寫。 否則，大小寫並不重要，也不會有額外的空間。

- **hive**：尋找包含 **hive** 的檔。
- **hive 資料庫**：尋找只包含 **hive 資料庫** 的檔。
- **HIVE 或資料庫**：尋找包含 **hive** 或 **資料庫** 的檔，或兩者。
- **HIVE 和資料庫**， **hive && 資料庫**：尋找同時包含 **hive** 和 **資料庫** 的檔。
- **HIVE 和 (資料庫或倉儲)**：尋找包含 **hive** 和 **資料庫** 或 **倉儲** 的檔，或兩者。
- **HIVE 非資料庫**：尋找包含 **hive** 但不是 **資料庫** 的檔。
- **hiv**：尋找包含以 **hiv** 開頭之單字的檔。 例如， **hiv**、 **hive**、 **hivbar** ( * 是符合任何) 字元數目的萬用字元。

### <a name="set-quick-filters"></a>設定快速篩選

搜尋結果清單是根據您在 [ **搜尋目錄**] 中輸入的搜尋字詞，以及您針對快速篩選所選取的值。

快速篩選器會將搜尋結果清單限制為具有所選取特性值的資產。 此篩選器具有下拉式清單和一個文字方塊。 下拉式清單會顯示 *目前* 搜尋結果中特性的值。 清單中每個值的旁邊都是目前搜尋結果中具有該值的資產數目計數。 如果您從清單中選取一個值，搜尋結果將會限制為具有該值的資產。 您只能選取一個值。

用來形成下拉式清單的目前搜尋結果取決於：

- **搜尋目錄** 中輸入的搜尋字詞。 
- 在快速篩選中選取的值。

以下是「資產類型」快速篩選的範例。

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="資產類型快速篩選範例。" border="true":::

您可以在文字方塊中輸入文字，將下拉式清單中的值限制為符合或部分符合文字的值。 如需使用文字方塊的範例，請參閱 [搜尋快速篩選：依資產類型篩選](#search-quick-filter-filter-by-asset-type)和 [搜尋快速篩選：依分類篩選：依分類](#search-quick-filter-filter-by-classification)篩選。

#### <a name="search-quick-filter-filter-by-asset-type"></a>搜尋快速篩選：依資產類型篩選

若要依資產類型篩選，請使用 **資產類型** 的快速篩選。 下拉式清單會顯示在目前搜尋結果中找到的資產類型，由搜尋字詞和快速篩選所決定。 針對每個類型，會顯示該類型的資產數目。

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="[資產類型] 快速篩選會反白顯示。它會顯示資產類型，以及每個資產類型的計數。" border="true":::

選取資產類型，將搜尋結果限制為該類型的資產。 您只能選取一種類型。

若只要顯示其名稱符合字串的資產類型，請在文字方塊中輸入字串。 例如，若只要在名稱中顯示具有 **sql** 的資產類型，請輸入 **sql**。

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="[快速篩選] 窗格具有「資產類型」的 sql。包含 sql 的資產清單會顯示三個專案。" border="true":::

選取資產類型，將搜尋結果限制為該類型的資產。 您只能選取一種類型。

#### <a name="search-quick-filter-filter-by-classification"></a>搜尋快速篩選：依分類篩選

若要依資產分類篩選，請使用 [ **分類** 快速篩選]。 下拉式清單會顯示已在目前搜尋結果中指派給一或多個資產的分類（由搜尋字詞和快速篩選所決定）。 針對每個分類，會顯示指派給分類的資產數目。

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="分類快速篩選器會反白顯示。" border="true":::

選取分類，將搜尋結果限制為指派給該分類的資產。 您只能選取一個分類。

若只要顯示其名稱符合字串的分類，請在文字方塊中輸入字串。 例如，若只要顯示名稱中包含 **數位** 的分類，請輸入 **number**。

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="在 [快速篩選] 窗格中，分類是「銀行」，而列出的分類全都包含該值。" border="true":::

選取分類，將搜尋結果限制為已獲指派該分類的資產。 您無法選取一個以上的分類。

#### <a name="search-quick-filter-filter-by-contacts"></a>搜尋快速篩選：依連絡人篩選

*連絡人* 是指派給資產作為擁有者或專家的人員。 當您查看資產詳細資料時，連絡人會顯示在 [ **連絡人** ] 索引標籤上。

有兩種方式可以搜尋指派給他們特定連絡人的資產。

- 在 [ **搜尋目錄** ] 中輸入全部或部分的連絡人名稱，並進行搜尋。 搜尋結果會包含具有名稱符合您的搜尋字詞之連絡人的資產。
- 在 [ **連絡人** 快速篩選] 中選取感興趣的連絡人，並進行搜尋。

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="[快速篩選] 窗格中的 [Person] 值為 [darren]。建議窗格中有三個建議。" border="true":::

## <a name="search-example"></a>搜尋範例

讓我們來看看一個假設的範例，看看搜尋詞彙和快速篩選器如何互動以判斷搜尋結果。 尤其是，我們會監視資產類型 **Azure Blob 儲存體** 的計數。

- 我們會進行未輸入搜尋字詞的第一次搜尋，且不會在快速篩選中選取任何值。 搜尋會尋找目錄中的所有資產。 搜尋結果清單和 **資產類型** 快速篩選器會顯示：

    - 搜尋結果清單有164230資產，也就是目錄中的所有資產。
    - [ **資產類型** ] 下拉式清單有43個專案。 這些都是目錄中的所有資產類型。 由於每個資產都只有一個類型，因此43資產類型的計數總和為164230。
    - **Azure Blob 儲存體** 資產類型是 **資產類型** 快速篩選的下拉式清單中的第一個專案。 這些值是依計數排序，最大優先，因此 **Azure Blob 儲存體** 是最常見的資產類型。 其計數為118174。

- 我們現在會在 **搜尋目錄** 中輸入 **parquet** ，並執行其他搜尋。 搜尋結果只會包含具有與 **parquet** 相符之特性的資產。 這會減少所有計數，如下所示：

    - 搜尋結果清單有493的資產。 在目錄中，只有493的164230資產具有符合 "parquet" 的特性。
    - [ **資產類型** ] 下拉式清單有15個專案。 493資產的每一個都是這15種類型的其中一種，而15種類型的計數加總為493。
    - 有456種類型的資產 **Azure Blob 儲存體**。 其他 37 (493 減去 456) 資產屬於其他14種類型的其中一種。

- 現在，我們來看看不同快速篩選器的下拉式清單： **分類**：

    - [搜尋結果] 清單中有12個493資產的分類。 493資產的計數不會加總至493，因為可將任何數量的分類指派給資產。
    - **人員的名稱** 分類會指派給36資產，而不是其他任何分類。

- 我們會選取 **人員的名稱** 分類。 搜尋結果清單會如預期般下降到36資產，因為 **人員名稱** 的計數是36。 這些結果都沒有 **Azure Blob 儲存體** 的類型。

我們可以得出的結論是，沒有任何資產的類型 **Azure Blob 儲存體** 是符合 **parquet** 的類型，而且具有 **人員名稱** 的分類。

## <a name="start-the-search"></a>開始搜尋

當您搜尋時，您在 [ **搜尋目錄** ] 中輸入的搜尋字詞會與資產特性進行比對。 這些特性包括名稱、類型、分類與連絡人。 具有相符特性的資產會顯示在搜尋結果清單中，除非是透過快速篩選來排除。

輸入搜尋詞彙並設定快速篩選之後，請以下列其中一種方式開始搜尋：

- 若要根據您輸入的字詞進行搜尋，請選取搜尋圖示 (:::image type="icon" source="./media/how-to-search-catalog/search-icon.png":::) ，按 **enter** 鍵，或選取 [ **視圖搜尋結果**]。
- 若要使用先前搜尋的字詞進行搜尋，請從 **最近的搜尋** 中選取它們。
- 若要使用建議的字詞進行搜尋，請從 **搜尋建議** 中選取。

從 **資產建議** 中選取資產，以直接前往資產的詳細資料頁面。 未完成任何搜尋。

建議和使用者搜尋的結果清單可能會稍有不同。 建議清單中的結果是以模糊相符專案為基礎，而使用者起始的搜尋結果是以完全相符的專案為基礎。

當您搜尋時，[ **搜尋結果** ] 頁面隨即出現，並列出搜尋所找到的資產。

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="顯示 contoso 搜尋值搜尋結果的螢幕擷取畫面。":::

若要查看資產詳細資料，請選取資產名稱。

使用搜尋結果頁面底部的控制項來流覽至其他搜尋結果頁面。

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="顯示如何流覽搜尋結果頁面的螢幕擷取畫面。":::

### <a name="sort-search-results"></a>排序搜尋結果

使用 [ **排序依據** ]，依 **相關性** 或 **名稱** 排序搜尋結果。

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="顯示如何排序搜尋結果的螢幕擷取畫面。在此範例中，[排序依據] 下拉式清單會設定為 [相關性]。":::

### <a name="search-results-dynamic-filters"></a>搜尋結果動態篩選

[**搜尋結果**] 頁面上的 [**篩選**] 窗格有篩選，可在搜尋結果清單上提供資產的動態篩選。 篩選是動態的，可根據篩選選取專案來顯示其他篩選器。

動態篩選準則會針對下拉式清單中的每個值提供核取方塊。 使用這些核取方塊可依您的需要篩選任意數量的值。

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>搜尋結果動態篩選：依資產類型篩選

如果您在 [ **資產類型** ] 下拉式清單中選取資產類型，就會顯示動態篩選器，讓您有更多方式來縮小搜尋結果的範圍。 篩選準則會根據選取的資產類型而有所不同。 例如，如果您選取 **Azure SQL Database**，則會顯示伺服器、資料庫和架構的動態篩選。 這些篩選器中的值來自于所選類型的搜尋結果中的資產。

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="Azure SQL Database 篩選項目是唯一選取的「資產類型」專案。此外，也會反白顯示該資產類型的搜尋結果。" border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>搜尋結果動態篩選：依分類篩選

[ **分類** ] 清單中的每個分類都會套用至 [搜尋結果] 清單中至少一個專案。 選取一或多個分類，將搜尋結果縮小為所選分類的資產。

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="[搜尋結果] 的分類篩選已反白顯示。" border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>編輯和刪除搜尋結果篩選

若要移除篩選，請清除篩選名稱旁邊的核取方塊。

### <a name="recently-accessed-assets"></a>最近存取的資產

展開之搜尋方塊的 [ **最近存取** ] 區段會顯示您最近存取的資產（如果有的話）。

- 選取 [**最近存取**] 區段中的 [**全部查看**]，以查看最近存取之資產的完整清單。

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="顯示展開搜尋方塊的 [最近存取] 區段的螢幕擷取畫面。":::

   最近存取的資產清單隨即出現。

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="顯示最近存取的資產清單的螢幕擷取畫面。":::

- 若要依名稱篩選，請在 [ **依名稱篩選**] 中輸入搜尋字串。

- 若要從清單中移除專案，請選取其核取方塊，然後選取 [ **移除**]。

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="螢幕擷取畫面，顯示如何從最近存取的資產清單中移除專案。":::

- 若要清除整個清單，請選取 [ **清除**]。

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="顯示如何清除最近存取的資產清單的螢幕擷取畫面":::

### <a name="search-assets"></a>搜尋資產

**首頁** 以外的許多頁面都在頂端有 [**搜尋資產**] 方塊。 例如，以下是已醒目提示 **搜尋資產** 的資產詳細資料頁面。

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="顯示資產詳細資料頁面的螢幕擷取畫面，其中已醒目提示搜尋資產":::

選取 [**搜尋資產**] 以啟動搜尋方塊，例如您 **在家** 的 [**搜尋目錄**] 中取得的搜尋方塊，具有相同的功能。

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="顯示展開搜尋資產方塊的螢幕擷取畫面。":::

## <a name="next-steps"></a>後續步驟

- [如何建立、匯入和匯出詞彙](how-to-create-import-export-glossary.md)
- [如何管理商務詞彙的詞彙範本](how-to-manage-term-templates.md)
