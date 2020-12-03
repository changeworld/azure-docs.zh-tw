---
title: '對資產套用分類 (預覽) '
description: 本檔說明如何在資產上套用分類。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: d12a7d52562fe32126e12a844c2d36c14cf01431
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552184"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>對 Azure 範疇中的資產套用分類

本文將討論如何在資產上套用分類。

## <a name="introduction"></a>簡介

分類可以是系統或自訂類型。 依預設，範疇中會有系統分類。 您可以根據正則運算式模式來建立自訂分類。 分類可以自動或手動方式套用至資產。

本檔說明如何將分類套用至您的資料。

## <a name="prerequisites"></a>先決條件

- 根據您的需求建立自訂分類。
- 設定資料來源的掃描。

## <a name="apply-classifications"></a>套用分類
您可以在 Azure 範疇中，對檔案、資料表或資料行資產套用系統或自訂分類。 本文說明在資產上手動套用分類的步驟。

### <a name="apply-classification-to-a-file-asset"></a>將分類套用至檔案資產
Azure 範疇可以掃描並自動分類檔檔。 例如，如果您有一個名為 *multiple.docx* 的檔案，且其內容中有全國識別碼號碼，則 Azure 範疇會將 **歐盟國家/地區識別碼** 分類新增至檔案資產的詳細資料頁面。

在某些情況下，您可能會想要以手動方式將分類新增至檔案資產。 如果您有多個群組至資源集的檔案，請在資源集層級新增分類。

請遵循下列步驟，將自訂或系統分類新增至分割區資源集：

1. 搜尋或流覽分割區，然後流覽至 [資產詳細資料] 頁面。

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="顯示資產詳細資料頁面的螢幕擷取畫面。":::

1. 在 [ **總覽** ] 索引標籤上，查看 [ **分類** ] 區段以查看是否有任何現有的分類。 選取 [編輯]。

1. 從 [ **分類** ] 下拉式清單中，選取您感興趣的特定分類。 例如， **信用卡號碼** 是系統分類和 **CustomerAccountID**，也就是自訂分類。

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="螢幕擷取畫面，顯示如何選取要新增至資產的分類。":::

1. 選取 [儲存]

1. 在 [ **總覽** ] 索引標籤上，確認您選取的分類出現在 [ **分類** ] 區段下。

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="顯示如何確認分類已新增至資產的螢幕擷取畫面。":::

### <a name="apply-classification-to-a-table-asset"></a>將分類套用至資料表資產

當 Azure 範疇掃描您的資料來源時，它不會自動將分類指派給資料表資產。 如果您希望資料表資產具有分類，您必須手動新增。

將分類新增至資料表資產：

1. 尋找您感興趣的資料表資產。 例如， **Customer** 資料表。

1. 確認沒有任何分類指派給資料表。 選取 [編輯]

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="螢幕擷取畫面，顯示如何查看和編輯資料表資產的分類。":::

1. 從 [ **分類** ] 下拉式清單中，選取一或多個分類。 此範例會使用名為 **CustomerInfo** 的自訂分類，但您可以選取此步驟的任何分類。

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="螢幕擷取畫面，顯示如何選取要加入至資料表資產的分類。":::

1. 選取 [ **儲存** ] 以儲存分類。

1. 在 [ **總覽** ] 頁面上，確認 Azure 範疇已新增您的新分類。

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="顯示如何確認是否已將分類新增至資料表資產的螢幕擷取畫面。":::

### <a name="add-classification-to-a-column-asset"></a>將分類新增至資料行資產

Azure 範疇會自動掃描並將分類新增至所有資料行資產。 但是，如果您想要變更分類，可以在資料行層級進行。

將分類新增至資料行：

1. 尋找並選取資料行資產，然後從 [**總覽**] 索引標籤選取 [**編輯**]。

1. 選取 [ **架構** ] 索引標籤

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="顯示如何編輯資料行架構的螢幕擷取畫面。":::

1. 找出您感興趣的資料行，然後選取 [ **新增分類**]。 此範例會將 **一般密碼** 分類新增至 **PasswordHash** 資料行。

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="顯示如何將分類加入至資料行的螢幕擷取畫面。":::

1. 選取 [儲存]

1. 選取 [ **架構** ] 索引標籤，並確認已將分類新增至資料行。

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="顯示如何確認已將分類新增至資料行架構的螢幕擷取畫面。":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>重新掃描現有分類的影響

第一次套用分類時，是根據您資料的取樣集檢查，並根據設定的 RegEx 模式進行比對。 在重新掃描時，如果套用新的分類，資料行會取得其他分類。 現有的分類會留在資料行上，而且必須以手動方式移除。

## <a name="next-steps"></a>後續步驟
若要瞭解如何建立自訂分類，請參閱 [建立自訂分類](create-a-custom-classification-and-classification-rule.md)。